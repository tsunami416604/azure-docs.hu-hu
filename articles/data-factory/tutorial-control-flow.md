---
title: Elágaztatás Azure Data Factory-folyamatokban | Microsoft Docs
description: Megismerheti, hogyan vezérelheti az adatok folyamát az Azure Data Factoryben elágaztatási és láncolási tevékenységekkel.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 93f8a5e806bd10824a78dd62351fd3d9be0cf32c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025827"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Elágaztatási és láncolási tevékenységek a Data Factory-folyamatokban
Ebben az oktatóanyagban egy olyan adat-előállító folyamatot hoz létre, amely bemutat néhány folyamvezérlési funkciót. A folyamat egy egyszerű másolást hajt végre egy Azure Blob Storage-beli tárolóból egy másik tárolóba, amely ugyanazon tárfiókban található. Ha a másolási tevékenység sikeres, egy, a sikeres műveletet jelző e-mailt szeretne küldeni, amelyben szerepelnek a sikeres másolási művelet részletei (például az írt adatok mennyisége). Ha a másolási tevékenység sikertelen, egy, a sikertelen műveletet jelző e-mailt szeretne küldeni, amelyben szerepelnek a sikertelen másolás részletei (például a hibaüzenet). Az oktatóanyag során megismerheti, hogyan adhatók át a paraméterek.

A forgatókönyv általános áttekintést: ![Áttekintés](media/tutorial-control-flow/overview.png)

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure Storage-beli társított szolgáltatás létrehozása
> * Azure Blob-adatkészlet létrehozása
> * Másolási tevékenységet és webes tevékenységet tartalmazó folyamat létrehozása
> * Tevékenységek kimeneteinek elküldése a soron következő tevékenységek számára
> * Paraméterátadás és rendszerváltozók használata
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Ez az oktatóanyag a .NET SDK-t használja. Az Azure Data Factoryvel való interakció más módszerekkel is lehetséges. Ezekkel kapcsolatban a tartalomjegyzék „Gyors útmutatók” részében találhat információkat.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

* **Azure Storage-fiók** A blobtárolót használjuk **forrás** adattárként. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../storage/common/storage-quickstart-create-account.md) ismertető cikket.
* **Azure SQL Database** Ezt az adatbázist használjuk **fogadó** adattárként. Ha még nem rendelkezik Azure SQL Database-adatbázissal, a létrehozás folyamatáért lásd az [Azure SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikket.
* **Visual Studio** 2013, 2015 vagy 2017. A jelen cikkben található útmutató a Visual Studio 2017-et használja.
* **Az [Azure .NET SDK](https://azure.microsoft.com/downloads/)** letöltése és telepítése.
* **Egy alkalmazás létrehozása az Azure Active Directoryban** [ennek az útmutatónak](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) a lépéseit követve. Jegyezze fel a következő értékeket, amelyeket a későbbi lépésekben fog használni: **alkalmazásazonosító**, **hitelesítési kulcs** és **bérlőazonosító**. Rendelje hozzá az alkalmazást a **Közreműködő** szerepkörhöz az ugyanebben a cikkben található utasításokat követve.

### <a name="create-blob-table"></a>Blobtábla létrehozása

1. Indítsa el a Jegyzettömböt. Másolja be az alábbi szöveget, és mentse egy **input.txt** nevű fájlként a lemezen.

    ```
    John|Doe
    Jane|Doe
    ```
2. Az [Azure Storage Explorer](http://storageexplorer.com/) vagy egy hasonló eszköz használatával hozza létre az **adfv2branch** tárolót, és töltse fel az **input.txt** fájlt a tárolóba.

## <a name="create-visual-studio-project"></a>Visual Studio-projekt létrehozása

Hozzon létre egy, a C# nyelvet használó .NET-konzolalkalmazást a Visual Studio 2015/2017 segítségével.

1. Indítsa el a **Visual Studiót**.
2. Kattintson a **File** (Fájl) menüre, mutasson a **New** (Új) elemre, és kattintson a **Project** (Projekt) lehetőségre. A lépések elvégzéséhez a .NET 4.5.2-es vagy újabb verziója szükséges.
3. A projekttípusok jobb oldalon megjelenő listájában válassza a **Visual C#** -> **Konzolalkalmazás (.NET-keretrendszer)** elemet.
4. Adja a projektnek az **ADFv2BranchTutorial** nevet.
5. A projekt létrehozásához kattintson az **OK** gombra.

## <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

1. Kattintson a **Tools** (Eszközök)  -> **NuGet Package Manager** (NuGet-csomagkezelő) -> **Package Manager Console** (Csomagkezelő konzol) elemre.
2. Csomagok telepítéséhez futtassa a következő parancsokat a **Csomagkezelő konzolon**:

    ```
    Install-Package Microsoft.Azure.Management.DataFactory -Prerelease
    Install-Package Microsoft.Azure.Management.ResourceManager -Prerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Adat-előállító ügyfél létrehozása

1. Nyissa meg a **Program.cs** fájlt, majd illessze be az alábbi utasításokat, hogy a névterekre mutató hivatkozásokat tudjon felvenni.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Adja hozzá ezeket a statikus változókat a **Program osztályhoz**. Cserélje le a helyőrzőket saját értékeire. Azure-régióban, amelyben a Data Factory jelenleg listája, válassza ki a régiók, amelyek a következő oldalon érdeklődésére számot tartó, és bontsa ki **Analytics** található **adat-előállító**: [Régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/). Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

    ```csharp
        // Set variables
        static string tenantID = "<tenant ID>";
        static string applicationId = "<application ID>";
        static string authenticationKey = "<Authentication key for your application>";
        static string subscriptionId = "<Azure subscription ID>";
        static string resourceGroup = "<Azure resource group name>";

        static string region = "East US";
        static string dataFactoryName = "<Data factory name>";

        // Specify the source Azure Blob information
        static string storageAccount = "<Azure Storage account name>";
        static string storageKey = "<Azure Storage account key>";
        // confirm that you have the input.txt file placed in th input folder of the adfv2branch container. 
        static string inputBlobPath = "adfv2branch/input";
        static string inputBlobName = "input.txt";
        static string outputBlobPath = "adfv2branch/output";
        static string emailReceiver = "<specify email address of the receiver>";

        static string storageLinkedServiceName = "AzureStorageLinkedService";
        static string blobSourceDatasetName = "SourceStorageDataset";
        static string blobSinkDatasetName = "SinkStorageDataset";
        static string pipelineName = "Adfv2TutorialBranchCopy";

        static string copyBlobActivity = "CopyBlobtoBlob";
        static string sendFailEmailActivity = "SendFailEmailActivity";
        static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
    
    ```

3. Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **DataFactoryManagementClient** osztályú példányt. Ezzel az objektummal adat-előállítót, társított szolgáltatást, adatkészleteket és folyamatot hozhat létre. Ezenfelül ez az objektum a folyamat futása részleteinek monitorozására is használható.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Data factory létrehozása
Hozzon létre egy „CreateOrUpdateDataFactory” funkciót a Program.cs fájlban:

```csharp
static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating data factory " + dataFactoryName + "...");
    Factory resource = new Factory
    {
        Location = region
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

    Factory response;
    {
        response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
    }

    while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
    {
        System.Threading.Thread.Sleep(1000);
    }
    return response;
}
```



Adja hozzá a következő kódot a **Main** metódushoz, amely létrehozza az **adat-előállítót**. 

```csharp
Factory df = CreateOrUpdateDataFactory(client);
```

## <a name="create-an-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása
Hozzon létre egy „StorageLinkedServiceDefinition” funkciót a Program.cs fájlban:

```csharp
static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
    AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
    LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
    return linkedService;
}
```
Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **Azure Storage-beli társított szolgáltatást**. A támogatott tulajdonságokról és adatokról az [Azure Blobbeli társított szolgáltatások tulajdonságait](connector-azure-blob-storage.md#linked-service-properties) ismertető részből tudhat meg többet.

```csharp
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
```

## <a name="create-datasets"></a>Adatkészletek létrehozása

Ebben a részben két adatkészletet hoz létre: egyet a forráshoz és egyet a fogadóhoz. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Adatkészlet létrehozása a forrás Azure Blobhoz
Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **Azure Blob-adatkészletet**. A támogatott tulajdonságokról és adatokról az [Azure Blob-adatkészlet tulajdonságait](connector-azure-blob-storage.md#dataset-properties) ismertető részből tudhat meg többet.

Meghatároz egy adatkészletet, amely a forrásadatokat jelöli az Azure Blobban. Ez a Blob-adatkészlet az előző lépésben létrehozott Azure Storage-beli társított szolgáltatásra vonatkozik, és a következőket írja le:

- A blob másolása helye: **FolderPath** és **FileName**;
- Figyelje meg a FolderPath esetében használt paramétereket. A „sourceBlobContainer” a paraméter neve, a kifejezést pedig a folyamat futásával átadott értékek helyettesítik. A paraméterek meghatározására szolgáló szintaxis: `@pipeline().parameters.<parameterName>`

Hozzon létre egy „SourceBlobDatasetDefinition” funkciót a Program.cs fájlban:

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    { 
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Adatkészlet létrehozása a fogadó Azure Blobhoz

Hozzon létre egy „SourceBlobDatasetDefinition” funkciót a Program.cs fájlban:

```csharp
static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
    return dataset;
}
```

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehozza az Azure Blob forrás és fogadó adatkészleteit. 

```csharp
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
```

## <a name="create-a-c-class-emailrequest"></a>Hozzon létre egy C# osztály: EmailRequest
A C#-projektben hozzon létre egy **EmailRequest** nevű osztályt. Ez határozza meg, hogy a folyamat e-mailek küldésekor milyen tulajdonságokat küld a törzskérelemben. Ebben az oktatóanyagban a folyamat négy tulajdonságot küld a folyamatból az e-mailbe:

- **Message**: az e-mail törzse. Sikeres másolás esetén ez a tulajdonság tartalmazza a futás részleteit (az írt adatok mennyiségét). Sikertelen másolás esetén ez a tulajdonság tartalmazza a hiba részleteit.
- **Data factory name**: az adat-előállító neve.
- **Pipeline name**: a folyamat neve.
- **Fogadó**: Átadott paraméter. Ez a tulajdonság adja meg az e-mail fogadóját.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```
## <a name="create-email-workflow-endpoints"></a>E-mail munkafolyamat végpontjainak létrehozása
E-mail küldésének aktiválásához a [Logic Apps](../logic-apps/logic-apps-overview.md) használatával határozhat meg munkafolyamatot. A Logic App-munkafolyamatok létrehozásának részleteit a [logikai alkalmazások létrehozását ismertető](../logic-apps/quickstart-create-first-logic-app-workflow.md) cikkben tekintheti meg. 

### <a name="success-email-workflow"></a>Sikeres műveletről tájékoztató e-mail munkafolyamata 
Hozzon létre egy `CopySuccessEmail` nevű Logic App-munkafolyamatot. A munkafolyamat eseményindítója legyen `When an HTTP request is received`, és adjon hozzá egy `Office 365 Outlook – Send an email` műveletet.

![Sikeres műveletről tájékoztató e-mail munkafolyamata](media/tutorial-control-flow/success-email-workflow.png)

A kérelem eseményindítójához a `Request Body JSON Schema` esetében adja meg a következő JSON-t:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```
Ez az előző szakaszban létrehozott **EmailRequest** osztályhoz igazodik. 

A kérelemnek a következő módon kell megjelennie a Logikaialkalmazás-tervezőben:

![Logikaialkalmazás-tervező – kérelem](media/tutorial-control-flow/logic-app-designer-request.png)

Az **E-mail küldése** művelethez szabja testre az e-mail formátumát a kérelemtörzs JSON-sémájában átadott tulajdonságokkal. Például:

![Logikaialkalmazás-tervező – e-mail küldése művelet](media/tutorial-control-flow/send-email-action.png)

Jegyezze fel a sikeres műveletről tájékoztató e-mail munkafolyamatához tartozó HTTP POST-kérelem URL-címét:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="fail-email-workflow"></a>Sikertelen műveletről tájékoztató e-mail munkafolyamata 
Klónozza a **CopySuccessEmail** munkafolyamatot, és hozzon létre egy másik Logic Apps-munkafolyamatot **CopyFailEmail** néven. A kérelem eseményindítójában a `Request Body JSON schema` ugyanaz. Egyszerűen módosítsa az e-mail formátumát (például a `Subject` értékét) a sikertelen műveletről tájékoztató e-mailnek megfelelően. Például:

![Logikaialkalmazás-tervező – Sikertelen műveletről tájékoztató e-mail munkafolyamata](media/tutorial-control-flow/fail-email-workflow.png)

Jegyezze fel a sikertelen műveletről tájékoztató e-mail munkafolyamatához tartozó HTTP POST-kérelem URL-címét:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Ekkor két munkafolyamathoz tartozó URL-címmel rendelkezik:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```
## <a name="create-a-pipeline"></a>Folyamat létrehozása
Adja hozzá a következő kódot a Main metódushoz, amely létrehozza a másolási tevékenységet és a dependsOn tulajdonságot tartalmazó folyamatot. Ebben az oktatóanyagban a folyamat egy tevékenységet tartalmaz: egy másolási tevékenységet, amely a Blob-adatkészletet használja forrásként és egy másik Blob-adatkészletet fogadóként. A másolási tevékenység sikeressége vagy sikertelensége esetén más-más e-mail-feladatokat hív meg.

Ebben a folyamatban a következő funkciókat használja:

- Paraméterek
- Webes tevékenység
- Tevékenységfüggőség
- Egy tevékenység kimenetének használata egy következő tevékenység bemeneteként

Tekintsük át a következő folyamatot szakaszonként:

```csharp

static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
        {
            Console.WriteLine("Creating pipeline " + pipelineName + "...");
            PipelineResource resource = new PipelineResource
            {
                Parameters = new Dictionary<string, ParameterSpecification>
                {
                    { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                },
                Activities = new List<Activity>
                {
                    new CopyActivity
                    {
                        Name = copyBlobActivity,
                        Inputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSourceDatasetName
                            }
                        },
                        Outputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSinkDatasetName
                            }
                        },
                        Source = new BlobSource { },
                        Sink = new BlobSink { }
                    },
                    new WebActivity
                    {
                        Name = sendSuccessEmailActivity,
                        Method = WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Succeeded" }
                            }
                        }
                    },
                    new WebActivity
                    {
                        Name = sendFailEmailActivity,
                        Method =WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Failed" }
                            }
                        }
                    }
                }
            };
            Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
            return resource;
        }
```
Adja hozzá a következő kódot a **Main** metódushoz, amely létrehozza a folyamatot:

```
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
```
### <a name="parameters"></a>Paraméterek
A folyamat első szakasza határozza meg a paramétereket. 

- sourceBlobContainer – a folyamat ezen paraméterét a forrás blob-adatkészlet használja fel.
- sinkBlobContainer – a folyamat ezen paraméterét a fogadó blob-adatkészlet használja fel.
- receiver – ezt a paramétert a folyamatban lévő két webes tevékenység használja, amelyek sikerrel vagy hibával kapcsolatos e-maileket küldenek a paraméter által meghatározott e-mail-címmel rendelkező címzettnek.


```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```
### <a name="web-activity"></a>Webes tevékenység
A webes tevékenység bármely REST-végpont meghívását lehetővé teszi. Erről a tevékenységről további információt a [webes tevékenységgel](control-flow-web-activity.md) kapcsolatos témakörben talál. Ez a folyamat egy webes tevékenységgel hívja meg a Logic Apps e-mail-munkafolyamatát. Két webes tevékenységet hoz létre: az egyik a **CopySuccessEmail** munkafolyamatot, a másik pedig a **CopyFailWorkFlow** munkafolyamatot hívja meg.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```
Az „Url” tulajdonságba illessze be a Logic Apps-munkafolyamatból a megfelelő kérési URL-végpontot. A „Body” tulajdonságban adja át az „EmailRequest” osztály egy példányát. Az e-mail-kérelem a következő tulajdonságokat tartalmazza:

- Message – Az átadott érték: `@{activity('CopyBlobtoBlob').output.dataWritten`. Hozzáfér az előző másolási tevékenység egy tulajdonságához, és átadja a dataWritten értéket. Sikertelen művelet esetén az átadott érték a `@{activity('CopyBlobtoBlob').error.message` helyett a hibakimenet.
- Data Factory Name – Az átadott érték: `@{pipeline().DataFactory}`. Ez egy rendszerváltozó, amely lehetővé teszi a megfelelő adat-előállító nevének elérését. A rendszerváltozók listáját lásd a [rendszerváltozókkal](control-flow-system-variables.md) kapcsolatos cikkben.
- Pipeline Name – Az átadott érték: `@{pipeline().Pipeline}`. Ez szintén egy rendszerváltozó, amely lehetővé teszi a megfelelő folyamat nevének elérését. 
- Receiver – Az átadott érték: "\@pipeline().parameters.receiver"). Hozzáfér a folyamat paramétereihez.
 
Ez a kód egy új tevékenységfüggőséget hoz létre, amely az azt megelőző másolási tevékenységtől függ.

## <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása
Adja hozzá a következő kódot a **Main** metódushoz, amely **elindítja a folyamat futását**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};
 
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Main osztály 
A végső Main metódusnak így kell kinéznie: Állítsa össze és futtassa a programot a folyamat futásának aktiválásához.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Folyamat futásának monitorozása
1. Adja hozzá a következő kódot a **Main** metódushoz a folyamat futása állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Adja hozzá a következő kódot a **Main** metódushoz, amely lekéri a másolási tevékenység futtatási részleteit, például az írt vagy olvasott adatok méretét.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>A kód futtatása
Állítsa össze és indítsa el az alkalmazást, majd ellenőrizze a folyamat-végrehajtást.
A konzol megjeleníti az adat-előállító, a társított szolgáltatás, az adatkészletek, a folyamat, valamint a folyamat futása létrehozási állapotát. Ezután ellenőrzi a folyamat futási állapotát. Várjon, amíg megjelennek a másolási tevékenység futásának részletei, beleértve az olvasott és írt adatok méretét. Ezután például az Azure Storage Explorerhez hasonló eszközök használatával ellenőrizheti, hogy a blobok a változókban megadottak szerint át lettek-e másolva az outputBlobPath helyre az inputBlobPath helyről.

**Példa a kimenetre:**

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>További lépések
Az oktatóanyagban az alábbi lépéseket hajtotta végre: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure Storage-beli társított szolgáltatás létrehozása
> * Azure Blob-adatkészlet létrehozása
> * Másolási tevékenységet és webes tevékenységet tartalmazó folyamat létrehozása
> * Tevékenységek kimeneteinek elküldése a soron következő tevékenységek számára
> * Paraméterátadás és rendszerváltozók használata
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Az Azure Data Factoryvel kapcsolatos további információkért folytassa az Alapelvek szakasz áttekintésével.
> [!div class="nextstepaction"]
>[Folyamatok és tevékenységek](concepts-pipelines-activities.md)
