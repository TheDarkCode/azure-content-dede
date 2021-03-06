<properties
   pageTitle="Verwenden der Hive-Shell in HDInsight (Hadoop) | Microsoft Azure"
   description="Erfahren Sie, wie Sie die Hive-Shell mit einem Linux-basierten HDInsight-Cluster verwenden. Sie erfahren, wie Sie eine Verbindung zum HDInsight-Cluster über SSH herstellen und anschließend die Hive-Shell zum interaktiven Ausführen von Abfragen verwenden."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/19/2016"
   ms.author="larryfr"/>

#Verwenden von Hive mit Hadoop in HDInsight über SSH

[AZURE.INCLUDE [Hive-Selektor](../../includes/hdinsight-selector-use-hive.md)]

In diesem Artikel erfahren Sie, wie Sie mit SSH (Secure Shell) eine Verbindung zu einem Hadoop-Cluster, der auf Azure HDInsight aufsetzt, herstellen und dann Hive-Abfragen mithilfe der Hive-Befehlszeilenschnittstelle (CLI) interaktiv übermitteln.

> [AZURE.IMPORTANT] In Linux-basierten HDInsight-Clustern ist der Hive-Befehl verfügbar, jedoch empfiehlt sich die Verwendung von Beeline. Beeline ist ein neuerer Client für die Arbeit mit Hive und im HDInsight-Cluster enthalten. Weitere Informationen zur Verwendung von Beeline finden Sie unter [Verwenden von Hive mit Hadoop in HDInsight über Beeline](hdinsight-hadoop-use-hive-beeline.md).

##<a id="prereq"></a>Voraussetzungen

Um die in diesem Artikel aufgeführten Schritte auszuführen, benötigen Sie Folgendes:

* Einen Linux-basierten Hadoop auf einem HDInsight-Cluster.

* Einen SSH-Client Linux, Unix und Mac OS sollten über einen SSH-Client verfügbar sein. Windows-Benutzer müssen einen Client herunterladen, z. B [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Verbinden mit SSH

Stellen Sie mithilfe des SSH-Befehls eine Verbindung zum vollqualifizierten Domänennamen (FQDN) des HDInsight-Clusters her. Der FQDN entspricht dem Namen, den Sie dem Cluster zugewiesen haben, gefolgt von **.azurehdinsight.net**. Mithilfe des Folgenden wird z. B. eine Verbindung zu einem Cluster namens **myhdinsight** hergestellt:

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Wenn Sie beim Erstellen des HDInsight-Clusters einen Zertifikatschlüssel für die SSH-Authentifizierung** angegeben haben, müssen Sie möglicherweise den Speicherort des privaten Schlüssel im Clientsystem angeben:

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Wenn Sie beim Erstellen des HDInsight-Clusters ein Kennwort für die SSH-Authentifizierung** angegeben haben, müssen Sie nach der entsprechenden Aufforderung das Kennwort angeben.

Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, OS X und Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###PUTTY (Windows-basierte Clients)

Windows bietet keinen integrierten SSH-Client. Wir empfehlen die Verwendung von **PuTTY**. Sie können das Programm unter [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) herunterladen.

Weitere Informationen zum Verwenden von PuTTY finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hive"></a>Verwenden des Hive-Befehls

2. Nachdem die Verbindung hergestellt wurde, starten Sie die Hive-Befehlszeilenschnittstelle (CLI) mit dem folgenden Befehl:

        hive

3. Geben Sie die folgenden Anweisungen zum Erstellen einer neuen Tabelle namens **log4jLogs** mithilfe der Befehlszeilenschnittstelle ein, wobei Sie die Beispieldaten verwenden:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Diese Anweisungen führen die folgenden Aktionen aus:

    * **TABELLE LÖSCHEN**: Löscht Tabelle und Datendatei, falls die Tabelle bereits existiert.
    * **EXTERNE TABELLE ERSTELLEN**: Erstellt eine neue "externe" Tabelle in Hive. Externe Tabellen speichern nur die Tabellendefinition in Hive. Die Daten verbleiben an ihrem ursprünglichen Speicherort.
    * **ZEILENFORMAT**: Teilt Hive mit, wie die Daten formatiert werden. In diesem Fall werden die Felder in den einzelnen Protokollen durch Leerzeichen getrennt.
    * **SPEICHERORT DER TEXTDATEI** – Teilt Hive den Speicherort der Daten (das Verzeichnis "example/data") und die Information mit, dass die Speicherung als Text erfolgt.
    * **AUSWÄHLEN** – Wählt die Anzahl aller Zeilen aus, bei denen die Spalte **t4** den Wert **[FEHLER]** enthält. Dadurch sollte der Wert **3** zurückgegeben werden, da dieser Wert in drei Zeilen enthalten ist.
    * **INPUT\_\_FILE\_\_NAME LIKE '%.log'**: Teilt Hive mit, dass nur Daten aus Dateien mit der Erweiterung ".log" zurückgeben werden sollen. Dies schränkt die Suche auf die Datei "sample.log" ein, die die Daten enthält, und verhindert, dass Daten aus anderen Beispieldatendateien zurückgegeben werden, die nicht dem von uns definierten Schema entsprechen.

    > [AZURE.NOTE] Wenn erwartet wird, dass die zugrunde liegenden Daten über eine externe Quelle, z. B. einen automatisierten Prozess zum Hochladen von Daten, oder über einen anderen MapReduce-Vorgang aktualisiert, aber von Hive immer die neuesten Daten verwendet werden, sollten externe Tabellen verwendet werden.
    >
    > Durch das Ablegen einer externen Tabelle werden **nicht** die Daten, sondern nur die Tabellendefinitionen gelöscht.

4. Verwenden Sie die folgenden Anweisungen zum Erstellen einer neuen "internen" Tabelle namens **errorLogs**.

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Diese Anweisungen führen die folgenden Aktionen aus:

    * **TABELLE ERSTELLEN, FALLS NICHT VORHANDEN** – Erstellt eine Tabelle, sofern diese noch nicht vorhanden ist. Da das Schlüsselwort **EXTERN** nicht verwendet wird, ist dies eine "interne" Tabelle, die im Hive-Data Warehouse gespeichert und vollständig von Hive verwaltet wird.
    * **ALS ORC GESPEICHERT** – Speichert die Daten im ORC-Format (Optimized Row Columnar). Dies ist ein stark optimiertes und effizientes Format zum Speichern von Hive-Daten.
    * **ÜBERSCHREIBEN EINFÜGEN ... SELECT**: Wählt die Zeilen aus der Tabelle **log4jLogs** aus, die **[ERROR]** enthalten. Dann werden die Daten in die Tabelle **errorLogs** eingefügt.

    Um zu überprüfen, dass nur Zeilen, die **[ERROR]** in Spalte "t4" enthalten, in der Tabelle **errorLogs** gespeichert wurden, verwenden Sie die folgende Anweisung, um alle Zeilen aus **errorLogs** zurückzugeben:

        SELECT * from errorLogs;

    Es sollten drei Datenzeilen zurückgegeben werden, die alle in Spalte "t4" den Wert **[ERROR]** enthalten.

    > [AZURE.NOTE] Im Gegensatz zu externen Tabellen werden beim Ablegen von internen Tabellen auch die zugrunde liegenden Daten gelöscht.

##<a id="summary"></a>Zusammenfassung

Wie Sie sehen können, bietet der Hive-Befehl eine einfache Möglichkeit, um Hive-Abfragen auf einem HDInsight-Cluster interaktiv auszuführen, den Auftragsstatus zu überwachen und die Ausgabe abzurufen.

##<a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu Hive in HDInsight:

* [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)

* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Wenn Sie mit Tez mit Hive verwenden, finden Sie in den folgenden Dokumenten Informationen zum Debuggen:

* [Verwenden der Tez-Benutzeroberfläche in Windows-basiertem HDInsight](hdinsight-debug-tez-ui.md)

* [Verwenden der Ambari-Tez-Ansicht in Linux-basiertem HDInsight](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md



[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

<!---HONumber=AcomDC_0727_2016-->