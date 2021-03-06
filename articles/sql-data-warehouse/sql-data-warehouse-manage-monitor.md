<properties
   pageTitle="Überwachen Ihres Workloads mit dynamischen Verwaltungssichten | Microsoft Azure"
   description="Informationen zum Überwachen Ihres Workloads mit dynamischen Verwaltungssichten."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/28/2016"
   ms.author="sonyama;barbkess"/>

# Überwachen Ihres Workloads mit dynamischen Verwaltungssichten

Dieser Artikel beschreibt, wie Sie mit dynamischen Verwaltungssichten Ihren Workload überwachen und die Ausführung von Abfragen in Azure SQL Data Warehouse untersuchen.

## Überwachen von Verbindungen

Alle Anmeldungen bei SQL Data Warehouse sind in [sys.dm\_pdw\_exec\_sessions][] protokolliert. Diese DMV enthält die letzten 10.000 Anmeldungen. Die Sitzungs-ID ist der Primärschlüssel und wird bei jeder neuen Anmeldung sequenziell zugewiesen.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## Überwachen der Abfrageausführung

Alle in SQL Data Warehouse ausgeführten Abfragen werden in [sys.dm\_pdw\_exec\_requests][] protokolliert. Diese DMV enthält die letzten 10.000 ausgeführten Abfragen. Die Anforderungs-ID identifiziert jede Abfrage eindeutig. Sie ist der Primärschlüssel für diese DMV. Die Anforderungs-ID wird für jede neue Abfrage sequenziell zugewiesen und erhält das Präfix QID für Abfrage-ID. Bei der Abfrage dieser DMV für eine bestimmte Sitzungs-ID werden alle Abfragen für eine bestimmte Anmeldung angezeigt.

>[AZURE.NOTE] Gespeicherte Prozeduren verwenden mehrere Anforderungs-IDs. Anforderungs-IDs werden in sequenzieller Reihenfolge zugewiesen.

Führen Sie folgende Schritte aus, um Abfrageausführungspläne und -zeiten für eine bestimmte Abfrage zu untersuchen.

### Schritt 1: Ermitteln der Abfrage, die Sie untersuchen möchten

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

Notieren Sie sich aus den oben stehenden Abfrageergebnissen **die Anforderungs-ID** der Abfrage, die Sie untersuchen möchten.

Abfragen im Status **Angehalten** werden aufgrund von Parallelitätseinschränkungen in die Warteschlange gestellt. Diese Abfragen werden auch in der Abfrage „sys.dm\_pdw\_waits“ mit dem Typ UserConcurrencyResourceType angezeigt. Weitere Informationen zu Parallelitätseinschränkungen finden Sie unter [Parallelitäts- und Workloadverwaltung in SQL Data Warehouse][]. Abfragen können auch aus anderen Gründen warten, beispielsweise wegen Objektsperren. Wenn Ihre Abfrage auf eine Ressource wartet, finden Sie nähere Informationen unter [Untersuchen von Anfragen, die auf Ressourcen warten][] weiter unten in diesem Artikel.

Vereinfachen Sie die Suche nach einer Abfrage in der Tabelle „sys.dm\_pdw\_exec\_requests“ mit [LABEL][], um Ihrer Abfrage einen Kommentar zuzuweisen, der in der Ansicht „sys.dm\_pdw\_exec\_requests“ gesucht werden kann.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### Schritt 2: Untersuchen des Abfrageplans

Rufen Sie mit der Anforderungs-ID den verteilten SQL-Plan (DSQL) der Abfrage aus [sys.dm\_pdw\_request\_steps][] ab.

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Wenn ein DSQL-Plan mehr Zeit in Anspruch nimmt als erwartet, kann die Ursache ein komplexer Plan mit vielen DSQL-Schritten oder nur ein einziger Schritt sein, der einen langen Zeitraum benötigt. Wenn der Plan viele Schritte mit mehreren Verschiebungen aufweist, erwägen Sie die Optimierung Ihrer Tabellenverteilungen, um Datenverschiebungen zu reduzieren. Der Artikel [Verteilen von Tabellen in SQL Data Warehouse][] erläutert, warum Daten verschoben werden müssen, um eine Abfrage zu lösen, und erläutert einige Verteilungsstrategien zum Minimieren von Datenverschiebungen.

Um weitere Informationen zu einem einzigen Schritt zu erhalten, beachten Sie die Spalte *operation\_type* des Abfrageschritts mit langer Laufzeit, und beachten Sie den **Schrittindex**:

- Fahren Sie für **SQL-Vorgänge** mit Schritt 3a fort: OnOperation, RemoteOperation, ReturnOperation.
- Fahren Sie für **Datenverschiebungsvorgänge** mit Schritt 3b fort: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### Schritt 3a: Untersuchen von SQL auf verteilten Datenbanken

Verwenden Sie die Anforderungs-ID und den Schrittindex, um Informationen aus [sys.dm\_pdw\_sql\_requests][] abzurufen. Das Ergebnis enthält Informationen zur Ausführung des Abfrageschritts in allen verteilten Datenbanken.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Wenn der Abfrageschritt ausgeführt wird, können Sie mit [DBCC PDW\_SHOWEXECUTIONPLAN][] aus dem Cache des SQL Server-Plans den berechneten SQL Server-Ausführungsplan für den innerhalb einer bestimmten Verteilung ausgeführten Schritt abrufen.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### Schritt 3 b: Untersuchen der Datenverschiebung auf den verteilten Datenbanken

Verwenden Sie die Anforderungs-ID und den Schrittindex, um Informationen zum Datenverschiebungsschritt, der für jede Verteilung ausgeführt wird, aus [sys.dm\_pdw\_dms\_workers][] abzurufen.

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- Überprüfen Sie die Spalte *total\_elapsed\_time*, um festzustellen, ob eine bestimmte Verteilung für das Verschieben von Daten erheblich länger als andere dauert.
- Überprüfen Sie für die Verteilung mit langer Laufzeit die Spalte *rows\_processed*, um festzustellen, ob die Anzahl der Zeilen, die von dieser Verteilung verschoben werden, beträchtlich größer als bei den anderen ist. Falls ja, kann dies auf eine Ungleichmäßigkeit der zugrunde liegenden Daten hinweisen.

Wird die Abfrage gerade ausgeführt, können Sie mit [DBCC PDW\_SHOWEXECUTIONPLAN][] aus dem Cache des SQL Server-Plans den berechneten SQL Server-Ausführungsplan für den derzeit ausgeführten SQL-Schritt innerhalb einer bestimmten Verteilung abrufen.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## Überwachen von wartenden Abfragen

Wenn Sie feststellen, dass Ihre Abfrage keine Fortschritte erzielt, weil sie auf eine Ressource wartet, können Sie mit folgender Abfrage alle Ressourcen anzeigen, auf die eine Abfrage wartet.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Wenn die Abfrage aktiv auf Ressourcen einer anderen Abfrage wartet, lautet der Status **AcquireResources**. Wenn die Abfrage über alle erforderlichen Ressourcen verfügt, ist der Status **Granted**.

## Nächste Schritte
Weitere Informationen zu dynamischen Verwaltungssichten finden Sie unter [Systemsichten][]. Tipps zur Verwaltung von SQL Data Warehouse finden Sie unter [Übersicht über die Verwaltung][]. Bewährte Methoden finden Sie unter [Bewährte Methoden für SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Übersicht über die Verwaltung]: ./sql-data-warehouse-overview-manage.md
[Bewährte Methoden für SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Systemsichten]: ./sql-data-warehouse-reference-tsql-system-views.md
[Verteilen von Tabellen in SQL Data Warehouse]: ./sql-data-warehouse-tables-distribute.md
[Parallelitäts- und Workloadverwaltung in SQL Data Warehouse]: ./sql-data-warehouse-develop-concurrency.md
[Untersuchen von Anfragen, die auf Ressourcen warten]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx

<!---HONumber=AcomDC_0831_2016-->