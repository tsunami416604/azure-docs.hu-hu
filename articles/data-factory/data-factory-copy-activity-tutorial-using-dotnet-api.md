---
title: 'Oktatóanyag: Másolási tevékenységgel ellátott adatcsatorna létrehozása a .NET API használatával | Microsoft Docs'
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre másolási tevékenységgel rendelkező Azure Data Factory-adatcsatornákat a .NET API használatával.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/16/2016
ms.author: spelluru

---
# Oktatóanyag: Másolási tevékenységgel ellátott adatcsatorna létrehozása a .NET API használatával
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és figyelhet meg Azure-adatelőállítót a .NET API segítségével. Az adat-előállítóban szereplő adatcsatorna másolási tevékenységgel másolja az adatokat az Azure Blob Storage-ből az Azure SQL Database-be.

A másolási tevékenység végzi az adatok továbbítását az Azure Data Factoryban. A tevékenységet egy globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és skálázható módon másolja az adatokat a különböző adattárak között. A Másolás tevékenységgel kapcsolatos részletekért tekintse meg a [Data Movement Activities](data-factory-data-movement-activities.md) (Adattovábbítási tevékenységek) című cikket.   

> [!NOTE]
> Ez a cikk nem tárgyalja a Data Factory teljes .NET API-ját. A Data Factory .NET SDK-val kapcsolatos részletes információkért olvassa el a [Data Factory .NET API Reference](https://msdn.microsoft.com/library/mt415893.aspx) (Data Factory .NET API referenciája) című cikket. 
> 
> 

## Előfeltételek
* Olvassa el figyelmesen [Az oktatóanyag áttekintése és az Előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című részt, hogy képet kapjon az oktatóanyag céljáról, és tisztában legyen az **előfeltételként** elvégzendő lépésekkel. 
* Visual Studio 2012, 2013 vagy 2015
* Az [Azure .NET SDK](http://azure.microsoft.com/downloads/) letöltése és telepítése.
* Azure PowerShell. Kövesse a [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása) cikkben foglalt utasításokat az Azure PowerShell telepítéséhez a számítógépre. Az Azure PowerShellt egy Azure Active Directory-alkalmazás létrehozására fogjuk használni.

### Alkalmazás létrehozása az Azure Active Directoryban
Hozzon létre egy Azure Active Directory-alkalmazást, hozza létre az alkalmazás szolgáltatásnevét, és rendelje hozzá a **Data Factory közreműködője** szerepkört.  

1. Indítsa el a **PowerShellt**. 
2. Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.
   
        Login-AzureRmAccount   
3. Futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez.
   
        Get-AzureRmSubscription 
4. Futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. A **&lt;NameOfAzureSubscription**&gt; helyére írja be Azure-előfizetése nevét. 
   
        Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
   
   > [!IMPORTANT]
   > Írja le a parancs kimenetében szereplő **SubscriptionId** és **TenantId** paraméter értékét. 
   > 
   > 
5. Hozzon létre egy **ADFTutorialResourceGroup** nevű Azure-erőforráscsoportot. Ehhez futtassa a következő parancsot a PowerShellben.  
   
        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
   
    Ha az erőforráscsoport már létezik, adja meg, hogy frissítse azt a rendszer (Y), vagy hagyja változatlanul (N). 
   
    Ha másik erőforráscsoportot használ, használja ehelyett saját erőforráscsoportja nevét, amikor az oktatóanyag az ADFTutorialResourceGroup csoportra utal.
6. Hozzon létre egy Azure Active Directory-alkalmazást. 
   
        $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
   
    Ha az alábbi hiba jelenik meg, adjon meg egy másik URL-címet, és futtassa ismét a parancsot. 
   
        Another object with the same value for property identifierUris already exists.
7. Hozza létre az AD-szolgáltatásnevet. 
   
        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
8. Adja hozzá a szolgáltatásnevet a **Data Factory közreműködője** szerepkörhöz. 
   
        New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
9. Szerezze be az alkalmazásazonosítót.
   
        $azureAdApplication
   
    Írja le az alkalmazásazonosítót (a parancs kimenetében szereplő **applicationID** paraméter értéke).

A fenti lépések elvégzésével beszereztük az alábbi négy értéket: 

* Bérlőazonosító
* Előfizetés azonosítója
* Alkalmazásazonosító 
* Jelszó (az első parancsnál adtuk meg)   

## Útmutatás
1. Hozzon létre egy a C# nyelvet használó .NET-konzolalkalmazást a Visual Studio 2012/2013/2015 segítségével.
   1. Indítsa el a **Visual Studio** 2012/2013/2015-at.
   2. Kattintson a **File** (Fájl) menüre, mutasson a **New** (Új) elemre, és kattintson a **Project** (Projekt) lehetőségre.
   3. Bontsa ki a **Sablonok** lehetőséget, és válassza a **Visual C#** lehetőséget. Ebben az útmutatóban a C#-t fogjuk használni, de bármelyik más .NET-nyelv is alkalmazható.
   4. A projekttípusok jobb oldalon megjelenő listájában válassza a **Konzolalkalmazás** elemet.
   5. A Név mezőbe írja be: **DataFactoryAPITestApp**.
   6. A Hely mezőbe írja be: **C:\ADFGetStarted**.
   7. A projekt létrehozásához kattintson az **OK** gombra.
2. Kattintson az **Eszközök** elemre, mutasson a **Nuget Package Manager** (Nuget-csomagkezelő) lehetőségre, majd kattintson a **Package Manager Console** (Csomagkezelő konzol) elemre.
3. A **Package Manager Console** (Csomagkezelő konzol) ablakban egyesével adja ki az alábbi parancsokat. 
   
       Install-Package Microsoft.Azure.Management.DataFactories
       Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
4. Adja hozzá az alábbi **appSetttings** részt az **App.config** fájlhoz. Ezeket a beállításokat a **GetAuthorizationHeader** segédmetódus használja. 
   
    Az **&lt;Application ID&gt;**, **&lt;Password&gt;**, **&lt;Subscription ID&gt;** és **&lt;tenant ID&gt;** értékek helyére írja be saját értékeit. 
   
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
   
            <!-- Replace the following values with your own -->
            <add key="ApplicationId" value="<Application ID>" />
            <add key="Password" value="<Password>" />    
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="tenant ID" />
        </appSettings>
5. Adja hozzá az alábbi **using** utasításokat a projekt forrásfájljához (Program.cs).
   
        using System.Threading;
        using System.Configuration;
        using System.Collections.ObjectModel;
   
        using Microsoft.Azure.Management.DataFactories;
        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Common.Models;
   
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure;
6. Adja hozzá az alábbi kódot, amellyel létrehoz egy példányt a **DataPipelineManagementClient** osztályból a **Main** metódusban. Ezzel az objektummal adat-előállítót, társított szolgáltatást, bemeneti és kimeneti adatkészleteket és egy adatcsatornát is létrehozhat. Ezenfelül ez az objektum használható az adatkészlet szeleteinek figyelése a futtatókörnyezetben.    
   
            // create data factory management client
            string resourceGroupName = "ADFTutorialResourceGroup";
            string dataFactoryName = "APITutorialFactory";
   
            TokenCloudCredentials aadTokenCredentials =
                new TokenCloudCredentials(
                    ConfigurationManager.AppSettings["SubscriptionId"],
                    GetAuthorizationHeader());
   
            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);
   
            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
   
   > [!IMPORTANT]
   > A **resourceGroupName** érték helyére írja be saját Azure-erőforráscsoportja nevét. 
   > 
   > Módosítsa úgy az adat-előállító nevét (**dataFactoryName**), hogy az egyedi legyen. Az adat-előállító nevének globálisan egyedinek kell lennie. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja. 
   > 
   > 
7. Adja hozzá az alábbi kódot, amely létrehozza az **adat-előállítót** a **Main** metódusban.
   
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
8. Adja hozzá az alábbi kódot, amely létrehozza az **Azure Storage-társított szolgáltatást** a **Main** metódusban. 
   
   > [!IMPORTANT]
   > A **storageaccountname** és az **accountkey** értékek helyére írja be Azure Storage-tárfiókja nevét, illetve kulcsát. 
   > 
   > 
   
            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );
9. Adja hozzá az alábbi kódot, amely létrehozza az **Azure SQL-társított szolgáltatást** a **Main** metódusban.
   
   > [!IMPORTANT]
   > A **servername**, **databasename**, **username** és **password** paraméterek értékét cserélje le az Azure SQL-kiszolgáló, az adatbázis és a felhasználói fiók nevére, valamint a felhasználói fiók jelszavára.  
   > 
   > 
   
            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure SQL Database linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureSqlLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                        )
                    }
                }
            );
10. Adja hozzá az alábbi kódot, amely létrehozza a **bemeneti és kimeneti adatkészleteket** a **Main** metódusban. 
    
            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "DatasetBlobSource";
            string Dataset_Destination = "DatasetAzureSqlDestination";
    
            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        Structure = new List<DataElement>()
                        {
                            new DataElement() { Name = "FirstName", Type = "String" },
                            new DataElement() { Name = "LastName", Type = "String" }
                        },
                        LinkedServiceName = "AzureStorageLinkedService",
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


            Console.WriteLine("Creating output dataset of type: Azure SQL");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            Structure = new List<DataElement>()
                            {
                                new DataElement() { Name = "FirstName", Type = "String" },
                                new DataElement() { Name = "LastName", Type = "String" }
                            },
                            LinkedServiceName = "AzureSqlLinkedService",
                            TypeProperties = new AzureSqlTableDataset()
                            {
                                TableName = "emp"
                            },

                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

1. Adja hozzá az alábbi kódot, amely **létrehozza és aktiválja az adatcsatornát** a **Main** metódusban. Az adatcsatorna **CopyActivity** paraméterrel meghatározott másolási tevékenysége a **BlobSource** értékét használja forrásként, és a **BlobSink** értékét fogadóként.
   
           // create a pipeline
           Console.WriteLine("Creating a pipeline");
           DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
           DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
           string PipelineName = "ADFTutorialPipeline";
   
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
                                   Name = "BlobToAzureSql",
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
2. A kimeneti adatkészlet adatszeletének állapotának lekéréséhez adja hozzá az alábbi kódot a **Main** metódushoz. Ebben a példában csupán egy szeletet fogunk kapni.   
   
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
3. Az adatszelet futtatási adatainak beszerzéséhez adja hozzá az alábbi kódot a **Main** metódushoz.
   
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
4. Adja hozzá a **Main** metódushoz szükséges alábbi segédmetódust a **Program** osztályhoz.  
   
       public static string GetAuthorizationHeader()
       {
           AuthenticationResult result = null;
           var thread = new Thread(() =>
           {
               try
               {
                   var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
   
                   ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                   result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
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
5. A Megoldáskezelőben bontsa ki a projektet (**DataFactoryAPITestApp**), kattintson jobb gombbal a **Hivatkozások** lehetőségre, majd kattintson a **Hivatkozás hozzáadása** elemre. Jelölje be a „**System.Configuration**” nevű szerelvényhez tartozó jelölőnégyzetet, majd kattintson az **OK** gombra. 
6. Hozza létre a konzolalkalmazást. Kattintson a menü **Fordítás** elemére, majd a **Megoldás fordítása** lehetőségre. 
7. Ellenőrizze, hogy az Azure Blob-fiókban található **adftutorial** nevű tárolóban van-e legalább egy fájl. Ha nincs, a Jegyzettömbben hozzon létre egy, az alábbi sorokat tartalmazó **Emp.txt** nevű fájlt, majd töltse fel azt az adftutorial nevű tárolóba.
   
       John, Doe
       Jane, Doe
8. Futtassa le a mintát, ehhez kattintson a menü **Hibakeresés** -> **Hibakeresés indítása** elemére. Ha megjelenik a **Getting run details of a data slice** (Adatszelet futtatási adatainak lekérése) felirat, várjon néhány percet, majd nyomja le az **ENTER** billentyűt. 
9. Az Azure Portalon ellenőrizze, hogy az **APITutorialFactory** nevű adat-előállító létrejött-e az alábbi összetevőkkel: 
   * Társított szolgáltatás: **LinkedService_AzureStorage** 
   * Adatkészlet: **DatasetBlobSource** és **DatasetBlobDestination**.
   * Adatcsatorna: **PipelineBlobSample** 
10. Ellenőrizze, hogy az **adftutorial** nevű tároló „**apifactoryoutput**” mappájában létrejött-e a kimeneti fájl.

## Következő lépések
* Olvassa el az [Adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely részletes információkat tartalmaz az oktatóanyagban használt másolási tevékenységről.
* A Data Factory .NET SDK-val kapcsolatos részletes információkért olvassa el a [Data Factory .NET API Reference](https://msdn.microsoft.com/library/mt415893.aspx) (Data Factory .NET API referenciája) című cikket. Ez a cikk nem tárgyalja a Data Factory teljes .NET API-ját. 

<!--HONumber=Oct16_HO3-->


