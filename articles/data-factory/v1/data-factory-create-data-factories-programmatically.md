---
title: Adatok folyamatok létrehozása az Azure .NET SDK használatával |} Microsoft Docs
description: Megtudhatja, hogyan programozott módon létrehozása, felügyelete és kezelése az Azure adat-előállítók Data Factory SDK használatával.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: b0a357be-3040-4789-831e-0d0a32a0bda5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 25bb16ebda29722fc8200ece525e870f6c85ef7d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621955"
---
# <a name="create-monitor-and-manage-azure-data-factories-using-azure-data-factory-net-sdk"></a>Hozzon létre, felügyelete és kezelése az Azure Data Factory .NET SDK használatával Azure adat-előállítók
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Ha a Data Factory szolgáltatás előzetes verzióban elérhető 2. verzióját használja, tekintse meg [a másolási tevékenység oktatóanyagát a 2. verzió dokumentációjában](../quickstart-create-data-factory-dot-net.md). 

## <a name="overview"></a>Áttekintés
Hozzon létre, figyelése és kezelése az Azure adat-előállítók programozott módon a Data Factory .NET SDK használatával. A cikkben egy forgatókönyv, amelyeket követve hozzon létre egy minta .NET konzolalkalmazást, amely létrehoz és egy adat-előállító figyeli. 

> [!NOTE]
> Ez a cikk nem tárgyalja a Data Factory teljes .NET API-ját. Lásd: [Data Factory .NET API-referencia](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1) .NET API-t a Data Factory átfogó dokumentációját. 

## <a name="prerequisites"></a>Előfeltételek
* Visual Studio 2012, 2013 vagy 2015
* Töltse le és telepítse [Azure .NET SDK](http://azure.microsoft.com/downloads/).
* Azure PowerShell. Kövesse a [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) cikkben foglalt utasításokat az Azure PowerShell telepítéséhez a számítógépre. Az Azure PowerShellt egy Azure Active Directory-alkalmazás létrehozására fogjuk használni.

### <a name="create-an-application-in-azure-active-directory"></a>Alkalmazás létrehozása az Azure Active Directoryban
Hozzon létre egy Azure Active Directory-alkalmazást, hozza létre az alkalmazás szolgáltatásnevét, és rendelje hozzá a **Data Factory közreműködője** szerepkört.

1. Indítsa el a **PowerShellt**.
2. Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.

    ```PowerShell
    Connect-AzureRmAccount
    ```
3. Futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez.

    ```PowerShell
    Get-AzureRmSubscription
    ```
4. Futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. A **&lt;NameOfAzureSubscription**&gt; helyére írja be Azure-előfizetése nevét.

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```

   > [!IMPORTANT]
   > Írja le a parancs kimenetében szereplő **SubscriptionId** és **TenantId** paraméter értékét.

5. Hozzon létre egy **ADFTutorialResourceGroup** nevű Azure-erőforráscsoportot. Ehhez futtassa a következő parancsot a PowerShellben.

    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Ha az erőforráscsoport már létezik, adja meg, hogy frissítse azt a rendszer (Y), vagy hagyja változatlanul (N).

    Ha másik erőforráscsoportot használ, használja ehelyett saját erőforráscsoportja nevét, amikor az oktatóanyag az ADFTutorialResourceGroup csoportra utal.
6. Hozzon létre egy Azure Active Directory-alkalmazást.

    ```PowerShell
    $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFDotNetWalkthroughApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfdotnetwalkthroughapp.org/example" -Password "Pass@word1"
    ```

    Ha az alábbi hiba jelenik meg, adjon meg egy másik URL-címet, és futtassa ismét a parancsot.
    
    ```PowerShell
    Another object with the same value for property identifierUris already exists.
    ```
7. Hozza létre az AD-szolgáltatásnevet.

    ```PowerShell
    New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. Adja hozzá a szolgáltatásnevet a **Data Factory közreműködője** szerepkörhöz.

    ```PowerShell
    New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. Szerezze be az alkalmazásazonosítót.

    ```PowerShell
    $azureAdApplication 
    ```
    Írja le az alkalmazásazonosítót (a parancs kimenetében szereplő applicationID paraméter értéke).

A fenti lépések elvégzésével beszereztük az alábbi négy értéket:

* Bérlőazonosító
* Előfizetés azonosítója
* Alkalmazásazonosító
* Jelszó (az első parancsnál adtuk meg)

## <a name="walkthrough"></a>Útmutatás
A forgatókönyv hoz létre egy adat-előállító egy folyamatot, amely tartalmazza a másolási tevékenység. A másolási tevékenység során másol adatokat az Azure blob storage a mappa ugyanazon a blob Storage tárolóban egy másik mappába. 

A másolási tevékenység végzi az adatok továbbítását az Azure Data Factoryban. A tevékenységet egy globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és skálázható módon másolja az adatokat a különböző adattárak között. A Másolás tevékenységgel kapcsolatos részletekért tekintse meg a [Data Movement Activities](data-factory-data-movement-activities.md) (Adattovábbítási tevékenységek) című cikket.

1. Hozzon létre egy a C# nyelvet használó .NET-konzolalkalmazást a Visual Studio 2012/2013/2015 segítségével.
   1. Indítsa el a **Visual Studio** 2012/2013/2015-at.
   2. Kattintson a **File** (Fájl) menüre, mutasson a **New** (Új) elemre, és kattintson a **Project** (Projekt) lehetőségre.
   3. Bontsa ki a **Sablonok** lehetőséget, és válassza a **Visual C#** lehetőséget. Ebben az útmutatóban a C#-t fogjuk használni, de bármelyik más .NET-nyelv is alkalmazható.
   4. A projekttípusok jobb oldalon megjelenő listájában válassza a **Konzolalkalmazás** elemet.
   5. A Név mezőbe írja be: **DataFactoryAPITestApp**.
   6. A Hely mezőbe írja be: **C:\ADFGetStarted**.
   7. A projekt létrehozásához kattintson az **OK** gombra.
2. Kattintson az **Eszközök** elemre, mutasson a **NuGet Package Manager** (NuGet-csomagkezelő) lehetőségre, majd kattintson a **Package Manager Console** (Csomagkezelő konzol) elemre.
3. A **Csomagkezelő konzolban** hajtsa végre a következő lépéseket:
   1. A Data Factory-csomag telepítéséhez futtassa az alábbi parancsot: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Futtassa a következő parancsot az Azure Active Directory-csomag telepítéséhez (használja az Active Directory API-t a kódban): `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Cserélje le a tartalmát **App.config** fájlt a projektben a következő tartalommal: 
    
    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```
5. Az App.Config fájlban frissítse az értékeket  **&lt;Alkalmazásazonosító&gt;**,  **&lt;jelszó&gt;**,  **&lt;előfizetés Azonosító&gt;**, és **&lt;bérlői azonosító&gt;** saját értékekkel.
6. Adja hozzá a következő **használatával** utasítást, hogy a **Program.cs** fájlt a projektben.

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```
6. Adja hozzá az alábbi kódot, amellyel létrehoz egy példányt a **DataPipelineManagementClient** osztályból a **Main** metódusban. Ezzel az objektummal adat-előállítót, társított szolgáltatást, bemeneti és kimeneti adatkészleteket és egy adatcsatornát is létrehozhat. Ezenfelül ez az objektum használható az adatkészlet szeleteinek figyelése a futtatókörnyezetben.

    ```csharp
    // create data factory management client

    //IMPORTANT: specify the name of Azure resource group here
    string resourceGroupName = "ADFTutorialResourceGroup";

    //IMPORTANT: the name of the data factory must be globally unique.
    // Therefore, update this value. For example:APITutorialFactory05122017
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > A **resourceGroupName** érték helyére írja be saját Azure-erőforráscsoportja nevét. Egy erőforrás csoport használatával hozhat létre a [New-használják](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmag.
   >
   > Módosítsa úgy az adat-előállító nevét (dataFactoryName), hogy az egyedi legyen. Az adat-előállító nevének globálisan egyedinek kell lennie. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
7. Adja hozzá az alábbi kódot, amely létrehozza az **adat-előállítót** a **Main** metódusban.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```
8. Adja hozzá az alábbi kódot, amely létrehozza az **Azure Storage-társított szolgáltatást** a **Main** metódusban.

   > [!IMPORTANT]
   > A **storageaccountname** és az **accountkey** értékek helyére írja be Azure Storage-tárfiókja nevét, illetve kulcsát.

    ```csharp
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
    ```
9. Adja hozzá az alábbi kódot, amely létrehozza a **bemeneti és kimeneti adatkészleteket** a **Main** metódusban.

    A **FolderPath** a bemeneti blob értékre van állítva a **adftutorial /** ahol **adftutorial** neve, a blob Storage tárolóban. Ha ez a tároló nem létezik az Azure blob Storage tárolóban, a tároló létrehozása ezen a néven: **adftutorial** és a szöveges fájl feltöltése a tárolóba.

    A kimeneti BLOB FolderPath értékre van állítva: **adftutorial/apifactoryoutput / {szelet}** ahol **szelet** értékének dinamikusan kiszámítását **SliceStart** () minden szelet dátum idejű Start.)

    ```csharp
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
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Destination,
            Properties = new DatasetProperties()
            {
    
                LinkedServiceName = "AzureStorageLinkedService",
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
    ```
10. Adja hozzá az alábbi kódot, amely **létrehozza és aktiválja az adatcsatornát** a **Main** metódusban. Az adatcsatorna **CopyActivity** paraméterrel meghatározott másolási tevékenysége a **BlobSource** értékét használja forrásként, és a **BlobSink** értékét fogadóként.

    A másolási tevékenység végzi az adatok továbbítását az Azure Data Factoryban. A tevékenységet egy globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és skálázható módon másolja az adatokat a különböző adattárak között. A Másolás tevékenységgel kapcsolatos részletekért tekintse meg a [Data Movement Activities](data-factory-data-movement-activities.md) (Adattovábbítási tevékenységek) című cikket.

    ```csharp
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
                            new ActivityInput()
                {
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
    ```
12. A kimeneti adatkészlet adatszeletének állapotának lekéréséhez adja hozzá az alábbi kódot a **Main** metódushoz. Ez a minta a várt egyetlen szelet van.

    ```csharp
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
    ```
13. **(választható)**  Futtatásához lekérni egy adatszelet részleteit az alábbi kódot a **fő** metódust.

    ```csharp
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
        });
    
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
    ```
14. Adja hozzá a **Main** metódushoz szükséges alábbi segédmetódust a **Program** osztályhoz. Ez a módszer egy párbeszédpanelt, amely, amely lehetővé teszi, hogy biztosítható a POP **felhasználónév** és **jelszó** , amelyekkel jelentkezzen be Azure-portálon.

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        ClientCredential credential = new ClientCredential(
            ConfigurationManager.AppSettings["ApplicationId"],
            ConfigurationManager.AppSettings["Password"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientCredential: credential);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. A Megoldáskezelőben bontsa ki a projekt: **DataFactoryAPITestApp**, kattintson a jobb gombbal **hivatkozások**, és kattintson a **hivatkozás hozzáadása**. Jelölje be a jelölőnégyzetet `System.Configuration` szerelvény, és kattintson **OK**.
15. Hozza létre a konzolalkalmazást. Kattintson a menü **Fordítás** elemére, majd a **Megoldás fordítása** lehetőségre.
16. Győződjön meg arról, hogy van legalább egy fájlt a adftutorial tárolóban az Azure blob storage. Ha nem, hozzon létre Emp.txt fájlt a Jegyzettömbben az alábbi tartalommal, és töltse fel a adftutorial tároló.

    ```
    John, Doe
    Jane, Doe
    ```
17. Futtassa le a mintát, ehhez kattintson a menü **Hibakeresés** -> **Hibakeresés indítása** elemére. Ha megjelenik a **Getting run details of a data slice** (Adatszelet futtatási adatainak lekérése) felirat, várjon néhány percet, majd nyomja le az **ENTER** billentyűt.
18. Az Azure Portalon ellenőrizze, hogy az **APITutorialFactory** nevű adat-előállító létrejött-e az alábbi összetevőkkel:
    * Társított szolgáltatás: **AzureStorageLinkedService**
    * Adatkészlet: **DatasetBlobSource** és **DatasetBlobDestination**.
    * Adatcsatorna: **PipelineBlobSample**
19. Győződjön meg arról, hogy a kimeneti fájl jön létre a **apifactoryoutput** mappájában a **adftutorial** tároló.

## <a name="get-a-list-of-failed-data-slices"></a>Hibás adatszeletek listájának lekérdezése 

```csharp
// Parse the resource path
var ResourceGroupName = "ADFTutorialResourceGroup";
var DataFactoryName = "DataFactoryAPITestApp";

var parameters = new ActivityWindowsByDataFactoryListParameters(ResourceGroupName, DataFactoryName);
parameters.WindowState = "Failed";
var response = dataFactoryManagementClient.ActivityWindows.List(parameters);
do
{
    foreach (var activityWindow in response.ActivityWindowListResponseValue.ActivityWindows)
    {
        var row = string.Join(
            "\t",
            activityWindow.WindowStart.ToString(),
            activityWindow.WindowEnd.ToString(),
            activityWindow.RunStart.ToString(),
            activityWindow.RunEnd.ToString(),
            activityWindow.DataFactoryName,
            activityWindow.PipelineName,
            activityWindow.ActivityName,
            string.Join(",", activityWindow.OutputDatasets));
        Console.WriteLine(row);
    }

    if (response.NextLink != null)
    {
        response = dataFactoryManagementClient.ActivityWindows.ListNext(response.NextLink, parameters);
    }
    else
    {
        response = null;
    }
}
while (response != null);
```

## <a name="next-steps"></a>További lépések
A következő példa egy folyamatot, amely másol adatokat az Azure blob storage egy Azure SQL Database .NET SDK használatával létrehozásához lásd: 

- [Hozzon létre egy folyamatot, a Blob Storage az SQL Database-adatok másolása](data-factory-copy-activity-tutorial-using-dotnet-api.md)
