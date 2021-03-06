<properties 
	pageTitle="Erstellen, Überwachen und Verwalten von Azure Data Factorys mithilfe des Data Factory SDK | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Azure Data Factorys mithilfe des Data Factory .NET SDK programmgesteuert erstellen, überwachen und verwalten." 
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
	ms.topic="article" 
	ms.date="07/28/2016" 
	ms.author="spelluru"/>

# Erstellen, Überwachen und Verwalten von Azure Data Factorys mithilfe des Data Factory .NET SDK
## Übersicht
Sie können Azure Data Factorys mithilfe des Data Factory .NET SDK programmgesteuert erstellen, überwachen und verwalten. Dieser Artikel enthält eine exemplarische Vorgehensweise, die Sie befolgen können, um eine .NET-Beispielkonsolenanwendung zu erstellen, die eine Data Factory erstellt und überwacht. Weitere Informationen zum Data Factory .NET SDK finden Sie unter [Data Factory-Klassenbibliotheksreferenz][adf-class-library-reference].



## Voraussetzungen

- Visual Studio 2012, 2013 oder 2015
- Herunterladen und Installieren des [Azure .NET SDK][azure-developer-center]
- Herunterladen und Installieren von NuGet-Paketen für Azure Data Factory Entsprechende Anweisungen sind in der exemplarischen Vorgehensweise enthalten.

## Exemplarische Vorgehensweise
1. Erstellen Sie mithilfe von Visual Studio 2012 oder 2013 eine C# .NET-Konsolenanwendung.
	<ol type="a">
		<li>Starten Sie <b>Visual Studio 2012</b> oder <b>Visual Studio 2013</b>.</li>
		<li>Klicken Sie auf <b>Datei</b>, zeigen Sie auf <b>Neu</b>, und klicken Sie auf <b>Projekt</b>.</li> 
		<li>Erweitern Sie <b>Vorlagen</b>, und wählen Sie <b>Visual C#</b> aus. In dieser exemplarischen Vorgehensweise verwenden Sie C#, aber Sie können jede .NET-Sprache verwenden.</li> 
		<li>Wählen Sie in der Liste der Projekttypen auf der rechten Seite <b>Konsolenanwendung</b> aus.</li>
		<li>Geben Sie <b>DataFactoryAPITestApp</b> in <b>Name</b> ein.</li> 
		<li>Wählen Sie <b>C:\ADFGetStarted</b> als <b>Speicherort</b>.</li>
		<li>Klicken Sie auf <b>OK</b>, um das Projekt zu erstellen.</li>
	</ol>
2. Klicken Sie auf <b>Extras</b>, zeigen Sie auf <b>NuGet-Paket-Manager</b>, und klicken Sie auf <b>Paket-Manager-Konsole</b>.
3.	Führen Sie in der <b>Paket-Manager-Konsole</b> die folgenden Befehle nacheinander aus.</b>

		Install-Package Microsoft.Azure.Management.DataFactories
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
6. Fügen Sie den folgenden **appSettings**-Abschnitt zur Datei **App.config** hinzu. Diese werden von der Hilfsmethode **GetAuthorizationHeader** verwendet.

	Ersetzen Sie die Werte für **AdfClientId**, **RedirectUri**, **SubscriptionId** und **ActiveDirectoryTenantId** durch Ihre eigenen Werte.

	Nachdem Sie sich mithilfe von „Login-AzureRmAccount“ angemeldet haben, können Sie die Abonnement-ID und die Mandanten-ID durch Ausführen von **Get-AzureAccount -Format-List** in Azure PowerShell abrufen. (Möglicherweise müssen Sie sich zuerst mithilfe von „Add-AzureAccount“ anmelden.)

	Sie können CLIENT-ID und Umleitungs-URI für Ihre AD-Anwendung im Azure-Portal abrufen.
 
		<appSettings>
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

		    <!-- Replace the following values with your own -->
		    <add key="AdfClientId" value="Your AD application ID" />
		    <add key="RedirectUri" value="Your AD application's redirect URI" />
		    <add key="SubscriptionId" value="your subscription ID" />
    		<add key="ActiveDirectoryTenantId" value="your tenant ID" />
		</appSettings>
6. Fügen Sie die folgenden **using**-Anweisungen zur Quelldatei (Program.cs) im Projekt hinzu.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
		
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Common.Models;
		
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure;
6. Fügen Sie folgenden Code, der eine Instanz der **DataPipelineManagementClient**-Klasse erstellt, zur Methode **Main** hinzu. Sie verwenden dieses Objekt, um eine Data Factory, einen verknüpften Dienst, Eingabe- und Ausgabedatasets sowie eine Pipeline zu erstellen. Zudem verwenden Sie dieses Objekt, um Datenslices eines Datasets zur Laufzeit zu überwachen.

        // create data factory management client
        string resourceGroupName = "resourcegroupname";
        string dataFactoryName = "APITutorialFactorySP";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

	> [AZURE.NOTE] Ersetzen Sie die**resourcegroupname** durch den Namen Ihrer Azure-Ressourcengruppe. Mit dem Cmdlet [New-AzureResourceGroup](https://msdn.microsoft.com/library/Dn654594.aspx) können Sie eine Ressourcengruppe erstellen.

7. Fügen Sie den folgenden Code, der eine **Data Factory** erstellt, zur Methode **Main** hinzu.

        // create a data factory
        Console.WriteLine("Creating a data factory");
        client.DataFactories.CreateOrUpdate(resourceGroupName,
            new DataFactoryCreateOrUpdateParameters()
            {
                DataFactory = new DataFactory()
                {
                    Name = dataFactoryName,
                    Location = "westus",
                    Properties = new DataFactoryProperties() { }
                }
            }
        );

8. Fügen Sie den folgenden Code, der einen **verknüpften Dienst** erstellt, zur Methode **Main** hinzu.

	> [AZURE.NOTE] Verwenden Sie **Kontoname** und **Kontoschlüssel** Ihres Azure-Speicherkontos für **ConnectionString**.

        // create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new LinkedServiceProperties
                    (
                        new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>")
                    )
                }
            }
        );
9. Fügen Sie den folgenden Code, der **Eingabe- und Ausgabedatasets** erstellt, zur Methode **Main** hinzu.

	Beachten Sie, dass für **FolderPath** für das Eingabeblob der Wert **adftutorial/** festgelegt ist, wobei **adftutorial** den Namen des Containers im Blobspeicher darstellt. Wenn dieser Container nicht in Ihrem Azure-Blobspeicher enthalten ist, erstellen Sie einen Container mit dem Namen **adftutorial** und laden eine Textdatei in den Container hoch.
	
	Beachten Sie, dass für "FolderPath" für das Ausgabe-Blob auf **adftutorial/apifactoryoutput/{Slice}** festgelegt ist, wobei **Slice** auf Basis des Werts von **SliceStart** (Startdatum und -zeit für jeden Slice) dynamisch berechnet wird.

 
        // create input and output datasets
        Console.WriteLine("Creating input and output datasets");
        string Dataset_Source = "DatasetBlobSource";
        string Dataset_Destination = "DatasetBlobDestination";

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Destination,
                    Properties = new DatasetProperties()
                    {

                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                            PartitionedBy = new Collection<Partition>()
                            {
                                new Partition()
                                {
                                    Name = "Slice",
                                    Value = new DateTimePartitionValue()
                                    {
                                        Date = "SliceStart",
                                        Format = "yyyyMMdd-HH"
                                    }
                                }
                            }
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });

11. Fügen Sie den folgenden Code, der eine **Pipeline erstellt und aktiviert**, zur Methode **Main** hinzu. Diese Pipeline verfügt über eine **CopyActivity**, die **BlobSource** als Quelle und **BlobSink** als Senke verwendet.

Die Kopieraktivität führt die Datenverschiebung in Azure Data Factory durch, und die Aktivität wird von einem global verfügbaren Dienst gestützt, mit dem Daten zwischen verschiedenen Datenspeichern auf sichere, zuverlässige und skalierbare Weise kopiert werden können. Ausführliche Informationen zur Kopieraktivität finden Sie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md).

            // create a pipeline
        Console.WriteLine("Creating a pipeline");
        DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
        DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
        string PipelineName = "PipelineBlobSample";

        client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new PipelineCreateOrUpdateParameters()
            {
                Pipeline = new Pipeline()
                {
                    Name = PipelineName,
                    Properties = new PipelineProperties()
                    {
                        Description = "Demo Pipeline for data transfer between blobs",

                        // Initial value for pipeline's active period. With this, you won't need to set slice status
                        Start = PipelineActivePeriodStartTime,
                        End = PipelineActivePeriodEndTime,

                        Activities = new List<Activity>()
                        {                                
                            new Activity()
                            {   
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput() {
                                        Name = Dataset_Source
                                    }
                                },
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Dataset_Destination
                                    }
                                },
                                TypeProperties = new CopyActivity()
                                {
                                    Source = new BlobSource(),
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    }
                                }
                            }

                        },
                    }
                }
            });

	

12. Fügen Sie die folgende Hilfsmethode, die von der Methode **Main** verwendet wird, zur **Program**-Klasse hinzu. Diese Methode öffnet ein Dialogfeld, in dem Sie den **Benutzernamen** und das **Kennwort** bereitstellen können, mit denen Sie sich beim Azure-Portal anmelden.
 
		public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AdfClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  
 
13. Fügen Sie den folgenden Code zur Methode **Main** hinzu, um den Status eines Datenslices des Ausgabedatasets abzurufen. Es gibt nur ein Slice in diesem Beispiel erwartet.
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                new DataSliceListParameters()
                {
                    DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                    DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                });

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.State);
                }
            }
        }

14. **(optional)** Fügen Sie der **Main**-Methode den folgenden Code hinzu, um Ausführungsdetails für einen Datenslice abzurufen.

        Console.WriteLine("Getting run details of a data slice");

		// give it a few minutes for the output slice to be ready
        Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
        Console.ReadKey();

        var datasliceRunListResponse = client.DataSliceRuns.List(
                resourceGroupName,
                dataFactoryName,
                Dataset_Destination,
                new DataSliceRunListParameters()
                {
                    DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                }
            );
        
        foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
        {
            Console.WriteLine("Status: \t\t{0}", run.Status);
            Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
            Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
            Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
            Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
            Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
            Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
        }

        Console.WriteLine("\nPress any key to exit.");
        Console.ReadKey();

15. Erweitern Sie im Projektmappen-Explorer das Projekt (**DataFactoryAPITestApp**), klicken Sie mit der rechten Maustaste auf **Verweise**, und klicken Sie auf **Verweis hinzufügen**. Aktivieren Sie das Kontrollkästchen für die Assembly **System.Configuration**, und klicken Sie auf **OK**.
16. Erstellen Sie die Konsolenanwendung. Klicken Sie im Menü auf **Erstellen** und dann auf **Projektmappe erstellen**.
16. Vergewissern Sie sich, dass sich mindestens eine Datei im "adftutorial"-Container im Azure Blob-Speicher befindet. Ist dies nicht der Fall, erstellen Sie die Datei "Emp.txt" mit folgendem Inhalt im Editor, und laden Sie sie anschließend in den "adftutorial"-Container hoch.

        John, Doe
		Jane, Doe
	 
17. Führen Sie das Beispiel aus, indem Sie im Menü auf **Debuggen** -> **Debuggen starten** klicken. Wenn angezeigt wird, dass die **Ausführungsdetails für einen Datenslice** abgerufen werden, warten Sie einige Minuten, und drücken Sie dann die **EINGABETASTE**.
18. Verwenden Sie das Azure-Portal, um Folgendes für die Data Factory zu überprüfen: **APITutorialFactory** wird mit folgenden Artefakten erstellt:
	- Verknüpfter Dienst: **LinkedService\_AzureStorage**
	- DataSet: **DatasetBlobSource** und **DatasetBlobDestination**.
	- Pipeline: **PipelineBlobSample**
18. Stellen Sie sicher, dass im Ordner **apifactoryoutput** im **adftutorial**-Container eine Ausgabedatei erstellt wird.


## Anmeldung ohne Popupdialogfeld 
Der obige Beispielcode startet ein Dialogfeld zur Eingabe der Azure-Anmeldeinformationen. Weitere Informationen für den Fall, dass die Anmeldung programmgesteuert ohne ein Dialogfeld erfolgen soll, finden Sie unter [Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager](resource-group-authenticate-service-principal.md#authenticate-service-principal-with-certificate---powershell).

### Beispiel

Erstellen Sie die GetAuthorizationHeaderNoPopup-Methode wie nachfolgend gezeigt:

    public static string GetAuthorizationHeaderNoPopup()
    {
        var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        var context = new AuthenticationContext(authority.AbsoluteUri);
        var credential = new ClientCredential(ConfigurationManager.AppSettings["AdfClientId"], ConfigurationManager.AppSettings["AdfClientSecret"]);
        AuthenticationResult result = context.AcquireTokenAsync(ConfigurationManager.AppSettings["WindowsManagementUri"], credential).Result;
        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }

Ersetzen Sie in der **Main**-Funktion den Aufruf **GetAuthorizationHeader** durch einen Aufruf von **GetAuthorizationHeaderNoPopup**:

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeaderNoPopup());

Hier wird gezeigt, wie Sie die Active Directory-Anwendung und den Dienstprinzipal erstellen und ihn anschließend der Rolle „Mitwirkender von Data Factory“ zuweisen:

1. Erstellen Sie die AD-Anwendung.

		$azureAdApplication = New-AzureRmADApplication -DisplayName "MyADAppForADF" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.myadappforadf.org/example" -Password "Pass@word1"

2. Erstellen Sie den AD-Dienstprinzipal.

		New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

3. Fügen Sie den Dienstprinzipal der Rolle „Mitwirkender von Data Factory“ hinzu.

		New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

4. Rufen Sie die Anwendungs-ID ab.

		$azureAdApplication


Notieren Sie sich die Anwendungs-ID und das Kennwort (geheimer Clientschlüssel), und verwenden Sie beides im oben aufgeführten Code.

[data-factory-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/downloads/
 

<!---HONumber=AcomDC_0803_2016-->