<properties 
	pageTitle="Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe von Visual Studio | Microsoft Azure" 
	description="In diesem Tutorial erstellen Sie mithilfe von Visual Studio eine Azure Data Factory-Pipeline mit Kopieraktivität." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="08/01/2016" 
	ms.author="spelluru"/>

# Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe von Visual Studio
> [AZURE.SELECTOR]
- [Übersicht über das Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Verwenden des Data Factory-Editors](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Verwenden von PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Verwenden von Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Verwenden der REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [Verwenden der .NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Verwenden des Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md)

In diesem Tutorial führen Sie mit Visual Studio 2013 die folgenden Schritte aus:

1. Erstellen von zwei verknüpften Diensten: **AzureStorageLinkedService1** und **AzureSqlinkedService1**. Der AzureStorageLinkedService1 verknüpft einen Azure-Speicher, und AzureSqlLinkedService1 verknüpft eine Azure SQL-Datenbank mit der Data Factory: **ADFTutorialDataFactoryVS**. Die Eingabedaten für die Pipeline befinden sich in einem Blobcontainer im Azure-Blobspeicher. Ausgabedaten werden in einer Tabelle in der Azure SQL-Datenbank gespeichert. Daher fügen Sie diese beiden Datenspeicher als verknüpfte Dienste der Data Factory hinzu.
2. Erstellen von zwei Data Factory-Tabellen: **EmpTableFromBlob** und **EmpSQLTable**, die Ein- und Ausgabedaten darstellen, die in den Datenspeichern gespeichert sind. Geben Sie für das EmpTableFromBlob-Element den Blobcontainer an, der ein Blob mit den Quelldaten enthält. Geben Sie für das EmpSQLTable-Element die SQL-Tabelle an, in der die Ausgabedaten gespeichert werden. Sie können auch andere Eigenschaften wie etwa Struktur und Verfügbarkeit angeben.
3. Erstellen einer Pipeline mit dem Namen **ADFTutorialPipeline** in ADFTutorialDataFactoryVS. Die Pipeline enthält eine **Kopieraktivität**, die Eingabedaten aus dem Azure-Blob in die Azure SQL-Ausgabetabelle kopiert. Die Kopieraktivität dient zum Verschieben von Daten in Azure Data Factory. Sie basiert auf einem global verfügbaren Dienst, mit dem Daten zwischen verschiedenen Datenspeichern sicher, zuverlässig und skalierbar kopiert werden können. Ausführliche Informationen zur Kopieraktivität finden Sie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md).
4. Erstellen einer Data Factory und Bereitstellen von verknüpften Diensten, Tabellen und der Pipeline.

## Voraussetzungen

1. Lesen Sie den Artikel [Übersicht über das Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
	
	> [AZURE.IMPORTANT] Stellen Sie sicher, dass die Voraussetzungen erfüllt sind, bevor Sie mit dem Vorgang fortfahren.
2. Data Factory-Entitäten können nur von einem **Administrator des Azure-Abonnements** für Azure Data Factory veröffentlicht werden.
3. Folgendes muss auf Ihrem Computer installiert sein:
	- Visual Studio 2013 oder Visual Studio 2015
	- Laden Sie das Azure-SDK für Visual Studio 2013 oder Visual Studio 2015 herunter. Navigieren Sie zur [Azure-Downloadseite](https://azure.microsoft.com/downloads/), und klicken Sie auf **VS 2013** oder**VS 2015** im Abschnitt **.NET**.
	- Laden Sie das neueste Azure Data Factory-Plug-In für Visual Studio herunter: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) oder [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Bei Verwendung von Visual Studio 2013 können Sie das Plug-In auch wie folgt aktualisieren: Klicken Sie im Menü auf **Tools** -> **Erweiterungen und Updates** -> **Online** -> **Visual Studio-Katalog** -> **Microsoft Azure Data Factory-Tools für Visual Studio** -> **Aktualisieren**.
 


## Erstellen eines Visual Studio-Projekts 
1. Starten Sie **Visual Studio 2013**. Klicken Sie auf **Datei**, zeigen Sie auf **Neu**, und klicken Sie auf **Projekt**. Das Dialogfeld **Neues Projekt** sollte angezeigt werden.
2. Wählen Sie im Dialogfeld **Neues Projekt** die Vorlage **DataFactory** aus, und klicken Sie auf **Leeres Data Factory-Projekt**. Wenn die Vorlage DataFactory nicht angezeigt wird, schließen Sie Visual Studio, installieren Sie Azure SDK für Visual Studio 2013, und öffnen Sie Visual Studio erneut.

	![Dialogfeld "Neues Projekt"](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)

3. Füllen Sie für das Projekt die Felder **Name**, **Speicherort** und **Lösung** aus, und klicken Sie auf**OK**.

	![Projektmappen-Explorer](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)

## Erstellen von verknüpften Diensten
Verknüpfte Dienste verknüpfen Datenspeicher oder Serverdienste mit einer Azure Data Factory. Ein Datenspeicher kann ein Azure-Speicher, eine Azure SQL-Datenbank oder eine lokale SQL Server-Datenbank sein.

In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **AzureStorageLinkedService1** und **AzureSqlLinkedService1**. Der verknüpfte Dienst „AzureStorageLinkedService1“ verknüpft ein Azure-Speicherkonto, und der Dienst „AzureSqlLinkedService“ verknüpft eine Azure SQL-Datenbank mit der Data Factory **ADFTutorialDataFactory**.

### Erstellen des mit Azure Storage verknüpften Diensts

4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verknüpfte Dienste**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.
5. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Mit Azure-Speicher verknüpfter Dienst** aus der Liste aus, und klicken Sie auf **Hinzufügen**.

	![Neuer verknüpfter Dienst](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
 
3. Ersetzen Sie **accountname** und **accountkey** durch den Namen Ihres Azure-Speicherkontos bzw. den entsprechenden Schlüssel.

	![Mit Azure-Speicher verknüpfter Dienst](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)

4. Speichern Sie die Datei **AzureStorageLinkedService1.json**.

### Erstellen des mit Azure SQL verknüpften Diensts

5. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste erneut auf den Knoten **Verknüpfte Dienste**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.
6. Wählen Sie dieses Mal **Azure SQL Linked Service** aus, und klicken Sie dann auf **Hinzufügen**.
7. Ersetzen Sie in der Datei **AzureSqlLinkedService1.json** die Werte **servername**, **databasename**, **username@servername** und **password** durch die entsprechenden Angaben für Azure SQL-Server, -Datenbank, -Benutzerkonto und -Kennwort.
8.  Speichern Sie die Datei **AzureSqlLinkedService1.json**.


## Erstellen von Datasets
Im vorherigen Schritt haben Sie die verknüpften Dienste **AzureStorageLinkedService1** und **AzureSqlLinkedService1** erstellt, um ein Azure-Speicherkonto und eine Azure SQL-Datenbank mit der Data Factory **ADFTutorialDataFactory** zu verknüpfen. In diesem Schritt definieren Sie die beiden Data Factory-Tabellen **EmpTableFromBlob** und **EmpSQLTable**, die Ein- und Ausgabedaten in den Datenspeichern darstellen, auf die von „AzureStorageLinkedService1“ und „AzureSqlLinkedService1“ verwiesen wird. Geben Sie für „EmpTableFromBlob“ den Blobcontainer an, der ein Blob mit den Quelldaten enthält. Geben Sie für „EmpSQLTable“ die SQL-Tabelle an, in der die Ausgabedaten gespeichert werden.

### Erstellen eines Eingabedatasets

9. Klicken Sie mit der rechten Maustaste auf **Tabellen** im **Projektmappen-Explorer**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.
10. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Azure-Blob** aus, und klicken Sie auf **Hinzufügen**.
10. Ersetzen Sie den JSON-Text durch den folgenden Text, und speichern Sie die **AzureBlobLocation1.json**-Datei.

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

### Erstellen des Ausgabedatasets

11. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste erneut auf **Tabellen**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.
12. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Azure SQL** aus, und klicken Sie auf **Hinzufügen**.
13. Ersetzen Sie den JSON-Text durch den folgenden JSON-Text, und speichern Sie die Datei **AzureSqlTableLocation1.json**.

		{
		  "name": "EmpSQLTable",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService1",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

## Erstellen der Pipeline 
Sie haben bisher verknüpfte Ein-/Ausgabe-Dienste und -Tabellen erstellt. Nun erstellen Sie eine Pipeline mit einer **Kopieraktivität**, um Daten aus dem Azure-Blob in Azure SQL-Datenbank zu kopieren.


1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Pipelines**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf**Neues Element**.
15. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Copy Data Pipeline**, und klicken Sie auf **Hinzufügen**.
16. Ersetzen Sie den JSON-Code durch den folgenden JSON-Code, und speichern Sie die Datei **CopyActivity1.json**.
			
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z",
		    "isPaused": false
		  }
		}

## Veröffentlichen/Bereitstellen der Data Factory-Entitäten
  
18. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie auf **Veröffentlichen**.
19. Wenn das Dialogfeld **Melden Sie sich bei Ihrem Microsoft-Konto an** angezeigt wird, geben Sie Ihre Anmeldeinformationen für das Konto mit dem Azure-Abonnement ein, und klicken Sie auf **Anmelden**.
20. Das folgende Dialogfeld sollte angezeigt werden:

	![Dialogfeld „Veröffentlichen“](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)

21. Führen Sie auf der Seite zum Konfigurieren der Data Factory folgende Schritte aus:
	1. Wählen Sie die Option **Neue Data Factory erstellen**.
	2. Geben Sie **VSTutorialFactory** als **Name** ein.
	
		> [AZURE.NOTE]  
		Der Name der Azure Data Factory muss global eindeutig sein. Falls beim Veröffentlichen ein Fehler in Verbindung mit dem Namen der Data Factory auftritt, ändern Sie den Namen der Data Factory (beispielsweise in „IhrNameVSTutorialFactory“), und wiederholen Sie den Veröffentlichungsvorgang. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md).
		
	3. Wählen Sie im Feld **Abonnement** das richtige Abonnement aus.
	4. Wählen Sie die **Ressourcengruppe** für die zu erstellende Data Factory aus.
	5. Wählen Sie die **Region** für die Data Factory aus.
	6. Klicken Sie auf **Weiter**, um zur Seite **Publish Items** zu wechseln.
23. Stellen Sie auf der Seite **Publish Items** sicher, dass alle Data Factory-Entitäten ausgewählt sind, und klicken Sie auf **Weiter**, um zur Seite **Zusammenfassung** zu wechseln.
24. Prüfen Sie die Zusammenfassung, und klicken Sie auf **Weiter**, um den Bereitstellungsprozess zu starten und den **Bereitstellungsstatus** anzuzeigen.
25. Auf der Seite **Bereitstellungsstatus** sollte der Status des Bereitstellungsprozesses angezeigt werden. Klicken Sie auf „Fertig stellen“, nachdem die Bereitstellung abgeschlossen ist.

Beachten Sie Folgendes:

- Führen Sie einen der folgenden Schritte aus, wenn Sie eine Fehlermeldung wie **Dieses Abonnement ist nicht zur Verwendung des Microsoft.DataFactory-Namespaces registriert** erhalten, und versuchen Sie, die Veröffentlichung erneut durchzuführen:

	- Führen Sie in Azure PowerShell den folgenden Befehl aus, um den Data Factory-Anbieter zu registrieren.
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		Sie können den folgenden Befehl ausführen, um sich zu vergewissern, dass der Data Factory-Anbieter registriert ist.
	
			Get-AzureRmResourceProvider
	- Melden Sie sich mit dem Azure-Abonnement beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu einem Data Factory-Blatt, oder erstellen Sie eine Data Factory im Azure-Portal. Mit dieser Aktion wird der Anbieter automatisch für Sie registriert.
- 	Der Name der Data Factory kann in Zukunft als DNS-Name registriert und so öffentlich sichtbar werden.
- 	Data Factory-Instanzen können nur von Mitwirkenden/Administratoren des Azure-Abonnements erstellt werden.

## Zusammenfassung
In diesem Lernprogramm haben Sie eine Azure Data Factory erstellt, um Daten aus einem Azure-Blob in eine Azure SQL-Datenbank zu kopieren. Sie haben Visual Studio verwendet, um die Data Factory, verknüpfte Dienste, Datasets und eine Pipeline zu erstellen. Im Anschluss sind die allgemeinen Schritte aufgeführt, die Sie in diesem Tutorial ausgeführt haben:

1.	Sie haben eine Azure **Data Factory** erstellt.
2.	Sie haben **verknüpfte Dienste** erstellt:
	1. Einen verknüpften **Azure Storage**-Dienst zum Verknüpfen Ihres Azure Storage-Kontos, in dem Eingabedaten enthalten sind.
	2. Einen verknüpften **Azure SQL**-Dienst zum Verknüpfen Ihrer Azure SQL-Datenbank, in der die Ausgabedaten enthalten sind.
3.	Sie haben **Datasets** erstellt, die Eingabedaten und Ausgabedaten für Pipelines beschreiben.
4.	Sie haben eine **Pipeline** mit einer **Kopieraktivität**, mit **BlobSource** als Quelle und mit **SqlSink** als Senke erstellt.


## Verwenden von Server-Explorer zum Anzeigen von Data Factorys

1. Klicken Sie in **Visual Studio** im Menü auf **Ansicht** und dann auf **Server-Explorer**.
2. Erweitern Sie im Server-Explorer-Fenster erst die Option **Azure** und dann **Data Factory**. Wenn **Anmelden bei Visual Studio** angezeigt wird, geben Sie das mit Ihrem Azure-Abonnement verknüpfte **Konto** ein, und klicken Sie auf **Weiter**. Geben Sie Ihr **Kennwort** ein, und klicken Sie auf **Anmelden**. Visual Studio versucht, Informationen zu allen Azure Data Factorys abzurufen, die in Ihrem Abonnement enthalten sind. Der Status dieses Vorgangs wird im Fenster **Data Factory-Aufgabenliste** angezeigt. ![Server-Explorer](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. Durch Klicken mit der rechten Maustaste auf eine Data Factory und Auswählen von „Data Factory in neues Projekt exportieren“ können Sie anhand einer vorhandenen Data Factory ein Visual Studio-Projekt erstellen. ![Exportieren einer Data Factory in ein Visual Studio-Projekt](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)

## Aktualisieren von Data Factory-Tools für Visual Studio
Um die Azure Data Factory-Tools für Visual Studio zu aktualisieren, führen Sie folgende Schritte aus:

1. Klicken Sie im Menü auf **Extras**, und wählen Sie**Erweiterungen und Updates** aus.
2. Wählen Sie im linken Bereich **Updates** und dann **Visual Studio Gallery** aus.
4. Wählen Sie **Azure Data Factory tools for Visual Studio** aus, und klicken Sie auf **Update**. Wenn dieser Eintrag nicht angezeigt wird, verfügen Sie bereits über die neueste Version der Tools.

Unter [Überwachen von Datasets und Pipelines](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) erfahren Sie, wie Sie die in diesem Tutorial erstellte Pipeline und die entsprechenden Datasets mithilfe des Azure-Portals überwachen können.

## Weitere Informationen
| Thema | Beschreibung |
| :---- | :---- |
| [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) | Dieser Artikel enthält ausführliche Informationen zur Kopieraktivität, die Sie in diesem Tutorial verwendet haben. |
| [Planung und Ausführung](data-factory-scheduling-and-execution.md) | In diesem Artikel werden die Planungs- und Ausführungsaspekte des Azure Data Factory-Anwendungsmodells erläutert. |
| [Pipelines](data-factory-create-pipelines.md) | Dieser Artikel enthält Informationen zu Pipelines und Aktivitäten in Azure Data Factory. |
| [Datasets](data-factory-create-datasets.md) | Dieser Artikel enthält Informationen zu Datasets in Azure Data Factory.
| [Überwachen und Verwalten von Pipelines mit der Überwachungs-App](data-factory-monitor-manage-app.md) | In diesem Artikel wird das Überwachen, Verwalten und Debuggen von Pipelines mit der App für die Überwachung und Verwaltung beschrieben. 

<!---HONumber=AcomDC_0831_2016-->