---
title: Elágazás az Azure Data Factory folyamatában
description: Megismerheti, hogyan vezérelheti az adatok folyamát az Azure Data Factoryben elágaztatási és láncolási tevékenységekkel.
services: data-factory
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 9/27/2019
ms.openlocfilehash: 77fa8f72d4d4d929d15859fde71f112de1ddd14e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418728"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Elágaztatási és láncolási tevékenységek a Data Factory-folyamatokban

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben az oktatóanyagban egy Data Factory-folyamatot hoz létre, amely bemutatja a vezérlési folyamat néhány funkcióját. Ez a folyamat az Azure Blob Storage egy tárolóból egy másik tárolóba másolja az azonos tárfiókban. Ha a másolási tevékenység sikeres, a folyamat e-mailben elküldi a sikeres másolási művelet részleteit. Ez az információ magában foglalhatja az írott adatok mennyiségét is. Ha a másolási tevékenység sikertelen, a másolási hiba részleteit, például a hibaüzenetet e-mailben küldi el. Az oktatóanyag során megismerheti, hogyan adhatók át a paraméterek.

Ez az ábra áttekintést nyújt a forgatókönyvről:

![Áttekintés](media/tutorial-control-flow/overview.png)

Ez az oktatóanyag bemutatja, hogyan kell elvégezni a következő feladatokat:

> [!div class="checklist"]
> * Data factory létrehozása
> * Azure Storage-beli társított szolgáltatás létrehozása
> * Azure Blob-adatkészlet létrehozása
> * Másolási tevékenységet és webes tevékenységet tartalmazó folyamat létrehozása
> * Tevékenységek kimeneteinek elküldése a soron következő tevékenységek számára
> * Paraméterátadás és rendszerváltozók használata
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Ez az oktatóanyag a .NET SDK-t használja. Más mechanizmusok használatával kommunikálhat az Azure Data Factory használatával. Az Adatgyár rövid útmutatóiról [az 5 perces rövid útmutatók című témakörben található.](/azure/data-factory/quickstart-create-data-factory-portal)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Azure Storage-fiók. A blob storage-ot forrásadattárként használja. Ha nem rendelkezik Azure-tárfiókkal, olvassa el a Tárfiók létrehozása című [témakört.](../storage/common/storage-account-create.md)
* Az Azure Storage Explorer ben. Az eszköz telepítéséről az Azure Storage Explorer című [témakörben](https://storageexplorer.com/)található.
* az Azure SQL Database-szel szemben. Ezt az adatbázist használjuk fogadóadattárként. Ha nem rendelkezik Azure SQL-adatbázissal, olvassa [el az Azure SQL-adatbázis létrehozása](../sql-database/sql-database-get-started-portal.md)című témakört.
* Visual Studio. Ez a cikk a Visual Studio 2019-et használja.
* Azure .NET SDK. Töltse le és telepítse az [Azure .NET SDK](https://azure.microsoft.com/downloads/)szolgáltatást.

Az Azure-régiók listáját, ahol a Data Factory jelenleg elérhető, [lásd: Termékek elérhető régiónként.](https://azure.microsoft.com/global-infrastructure/services/) Az adattárak és a számítási lehet más régiókban. Az üzletek közé tartozik az Azure Storage és az Azure SQL Database. A számítási feladatok közé tartozik a HDInsight, amely et használ a Data Factory.

Hozzon létre egy alkalmazást az [Azure Active Directory-alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)című dokumentumban leírtak szerint. Rendelje hozzá az alkalmazást a **közreműködői** szerepkörhöz az ugyanabban a cikkben található utasítások követésével. Az oktatóanyag későbbi részeihez több értékre lesz szükség, például **az alkalmazás (ügyfél) azonosítóés** **a címtár (bérlői) azonosító.**

### <a name="create-a-blob-table"></a>Blob-tábla létrehozása

1. Nyisson meg egy szövegszerkesztőt. Másolja a következő szöveget, és mentse helyileg *input.txt*néven.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Nyissa meg az Azure Storage Explorert. Bővítse ki tárfiókját. Kattintson a jobb gombbal **a Blob Containers elemre,** és válassza **a Blob Container létrehozása parancsot.**
1. Nevezze el az új *tárolót adfv2branch* és **válassza feltöltés** hozzáadni a *input.txt* fájlt a tárolóba.

## <a name="create-visual-studio-project"></a>Visual Studio-projekt létrehozása<a name="create-visual-studio-project"></a>

C# .NET konzolalkalmazás létrehozása:

1. Indítsa el a Visual Studio alkalmazást, és válassza **az Új projekt létrehozása**lehetőséget.
1. Az **Új projekt létrehozása**csoportban válassza a Console App **(.NET Framework) (C# )** lehetőséget, majd a **Tovább**gombot.
1. Nevezze el a *projektet ADFv2BranchTutorial*.
1. Válassza a **.NET 4.5.2-es** vagy újabb verzióját, majd a **Létrehozás**lehetőséget.

### <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

1. Válassza **az Eszközök** > **NuGet Csomagkezelő csomagkezelő** > **konzolt.**
1. A **Csomagkezelő konzolon**futtassa a következő parancsokat a csomagok telepítéséhez. A részleteket a [Microsoft.Azure.Management.DataFactory nuget csomagban](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/) találja.

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Adat-előállító ügyfél létrehozása

1. Nyissa *meg Program.cs,* és adja hozzá a következő állításokat:

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

1. Adja hozzá ezeket `Program` a statikus változókat az osztályhoz. Cserélje le a helyőrzőket saját értékeire.

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

1. Adja hozzá a következő kódot a `Main` metódushoz: Ez a kód `DataFactoryManagementClient` létrehoz egy osztálypéldányt. Ezt követően ezzel az objektummal adat-előállító, csatolt szolgáltatás, adatkészletek és folyamat hozható létre. Ezt az objektumot is használhatja a folyamat futtatási részleteinek figyelésére.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Data factory létrehozása

1. Adjon `CreateOrUpdateDataFactory` hozzá egy metódust *a Program.cs* fájlhoz:

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

1. Adja hozzá a `Main` következő sort az adat-előállítót létrehozó metódushoz:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása

1. Adjon `StorageLinkedServiceDefinition` hozzá egy metódust *a Program.cs* fájlhoz:

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

1. Adja hozzá a `Main` következő sort az Azure Storage-hoz csatolt szolgáltatást létrehozó metódushoz:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

A támogatott tulajdonságokról és részletekről a [Csatolt szolgáltatás tulajdonságai](connector-azure-blob-storage.md#linked-service-properties)című témakörben talál további információt.

## <a name="create-datasets"></a>Adatkészletek létrehozása

Ebben a szakaszban két adatkészletet hoz létre, egyet a forráshoz és egyet a fogadóhoz.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Adatkészlet létrehozása forráshoz Az Azure Blob

Adjon hozzá egy módszert, amely létrehoz egy *Azure blob adatkészletet.* A támogatott tulajdonságokról és részletekről az [Azure Blob adatkészlet tulajdonságai](connector-azure-blob-storage.md#dataset-properties)című témakörben talál további információt.

Adjon `SourceBlobDatasetDefinition` hozzá egy metódust *a Program.cs* fájlhoz:

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

Meghatároz egy adatkészletet, amely a forrásadatokat jelöli az Azure Blobban. Ez a Blob-adatkészlet az előző lépésben támogatott Azure Storage-kapcsolt szolgáltatásra hivatkozik. A Blob-adatkészlet a másolandó blob helyét írja le: *FolderPath* és *FileName*.

Figyelje meg, hogy paramétereket használ a *FolderPath*. `sourceBlobContainer`a paraméter neve, és a kifejezés helyébe a folyamat futtatásakor átadott értékek lépnek. A paraméterek meghatározására szolgáló szintaxis: `@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Adatkészlet létrehozása a fogadóHoz Az Azure Blob

1. Adjon `SourceBlobDatasetDefinition` hozzá egy metódust *a Program.cs* fájlhoz:

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

1. Adja hozzá a `Main` következő kódot a metódushoz, amely létrehozza az Azure Blob-forrás és a fogadó adatkészletek.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>C# osztály létrehozása: EmailRequest

A C# projektben hozzon `EmailRequest`létre egy osztálynevű . Ez az osztály határozza meg, hogy a folyamat milyen tulajdonságokat küld a törzskérelemben e-mail küldésekor. Ebben az oktatóanyagban a folyamat négy tulajdonságot küld a folyamatból az e-mailbe:

* Üzenetet. Az e-mail törzse. A sikeres másolás hoz ez a tulajdonság a megírt adatok mennyiségét tartalmazza. Sikertelen másolat esetén ez a tulajdonság a hiba részleteit tartalmazza.
* Adatgyár neve. Az adatgyár neve.
* Csővezeték neve. A folyamat neve.
* Vevő. Átmenő paraméter. Ez a tulajdonság adja meg az e-mail fogadóját.

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

E-mail küldésének aktiválásához a [Logic Apps](../logic-apps/logic-apps-overview.md) használatával határozhat meg munkafolyamatot. A Logic Apps-munkafolyamatok létrehozásáról a Logikai alkalmazások létrehozása című témakörben talál további [részleteket.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

### <a name="success-email-workflow"></a>Sikeres műveletről tájékoztató e-mail munkafolyamata

Az [Azure Portalon](https://portal.azure.com)hozzon létre egy CopySuccessEmail nevű logic apps *munkafolyamatot.* Adja meg a `When an HTTP request is received`munkafolyamat-eseményindítót . A kérelem eseményindítójához a `Request Body JSON Schema` esetében adja meg a következő JSON-t:

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

A munkafolyamat a következő példához hasonlóan néz ki:

![Sikeres műveletről tájékoztató e-mail munkafolyamata](media/tutorial-control-flow/success-email-workflow-trigger.png)

Ez a JSON-tartalom `EmailRequest` az előző szakaszban létrehozott osztályhoz igazodik.

Adjon hozzá `Office 365 Outlook – Send an email`egy műveletet a. Az **e-mail küldése** művelet, testreszabhatja, hogyan szeretné formázni az e-mailt, a kérelemben átadott tulajdonságok **Body** JSON séma használatával. Például:

![Logikai alkalmazástervező – e-mail küldése művelet](media/tutorial-control-flow/customize-send-email-action.png)

A munkafolyamat mentése után másolja és mentse a **HTTP POST URL-értékét** az eseményindítóból.

## <a name="fail-email-workflow"></a>Sikertelen műveletről tájékoztató e-mail munkafolyamata

Klón **CopySuccessEmail** egy másik Logic Apps munkafolyamat nevű *CopyFailEmail*. A kérelem eseményindítójában a `Request Body JSON schema` ugyanaz. Módosítsa az e-mail formátumát (például a `Subject` értékét) a sikertelen műveletről tájékoztató e-mailnek megfelelően. Például:

![Logikai alkalmazástervező – sikertelen e-mail-munkafolyamat](media/tutorial-control-flow/fail-email-workflow.png)

A munkafolyamat mentése után másolja és mentse a **HTTP POST URL-értékét** az eseményindítóból.

Most már két munkafolyamat-URL-címekkel kell rendelkeznie, például az alábbi példákkal:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Folyamat létrehozása

Lépjen vissza a projekthez a Visual Studióban. Most hozzáadjuk a kódot, amely létrehoz egy `DependsOn` folyamatot másolási tevékenységgel és tulajdonsággal. Ebben az oktatóanyagban a folyamat tartalmaz egy tevékenységet, egy másolási tevékenységet, amely a Blob-adatkészletet forrásként, egy másik Blob-adatkészletet pedig fogadóként veszi fel. Ha a másolási tevékenység sikeres vagy sikertelen, különböző e-mail feladatokat hív meg.

Ebben a folyamatban a következő funkciókat használja:

* Paraméterek
* Webes tevékenység
* Tevékenységfüggőség
* Tevékenység kimenetének használata egy másik tevékenység hez való bevitelként

1. Adja hozzá ezt a módszert a projekthez. A következő szakaszok részletesebben ismertetik.

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

1. Adja hozzá a `Main` következő sort a folyamatot létrehozó metódushoz:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Paraméterek

A csővezeték-kód első szakasza paramétereket határoz meg.

* `sourceBlobContainer`. A forrásblob-adatkészlet ezt a paramétert használja fel a folyamatban.
* `sinkBlobContainer`. A fogadó blob adatkészlet használja ezt a paramétert a folyamatban.
* `receiver`. A két webes tevékenység a folyamatban, hogy küldjön sikeres vagy sikertelen e-maileket a fogadó használja ezt a paramétert.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Webes tevékenység

A webes tevékenység bármely REST-végpont hívását lehetővé teszi. A tevékenységről további információt az Azure Data Factory webes tevékenysége című [témakörben talál.](control-flow-web-activity.md) Ez a folyamat egy webes tevékenységet használ a Logic Apps e-mail munkafolyamat ának hívásához. Két webes tevékenységet hoz létre: `CopySuccessEmail` az egyik a `CopyFailWorkFlow`munkafolyamatot hívja, a másik pedig a .

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

A `Url` tulajdonságba illessze be a **HTTP-POST URL-végpontokat** a Logic Apps munkafolyamataiból. A `Body` tulajdonság, adja át `EmailRequest` egy példányát az osztály. Az e-mail-kérelem a következő tulajdonságokat tartalmazza:

* Üzenetet. Átadja az `@{activity('CopyBlobtoBlob').output.dataWritten`értékét. Hozzáfér az előző másolási tevékenység egy tulajdonságához, `dataWritten`és átadja a értékét. Sikertelen művelet esetén az átadott érték a `@{activity('CopyBlobtoBlob').error.message` helyett a hibakimenet.
* Adat-előállító neve. Átadja az `@{pipeline().DataFactory}` értéket: Ez a rendszerváltozó lehetővé teszi a megfelelő adatgyár nevének elérését. A rendszerváltozók listáját a [Rendszerváltozók ( Rendszerváltozók](control-flow-system-variables.md)) tartalmazza.
* Folyamat neve. Átadja az `@{pipeline().Pipeline}`értékét. Ez a rendszerváltozó lehetővé teszi a megfelelő folyamatnév elérését.
* Vevő. Átadja az `"@pipeline().parameters.receiver"`értékét. Hozzáfér a folyamat paramétereihez.

Ez a kód új tevékenységfüggőséget hoz létre, amely az előző másolási tevékenységtől függ.

## <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása

Adja hozzá a `Main` következő kódot a folyamatfuttatást kiváltó metódushoz.

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

A `Main` végső módszerednek így kell kinéznie.

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

Állítsa össze és futtassa a programot a folyamat futásának aktiválásához.

## <a name="monitor-a-pipeline-run"></a>Folyamat futásának monitorozása

1. Adja hozzá a következő kódot a `Main` metódushoz:

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

    Ez a kód folyamatosan ellenőrzi a futtatás állapotát, amíg be nem fejezi az adatok másolását.

1. Adja hozzá a `Main` következő kódot a másolási tevékenység futtatási részleteinek beolvasására szolgáló metódushoz, például az olvasott/írt adatok méretéhez:

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

Az alkalmazás megjeleníti az adat-előállító, a csatolt szolgáltatás, az adatkészletek, a csővezeték és a folyamatfuttatás létrehozásának folyamatait. Ezután ellenőrzi a folyamat futási állapotát. Várjon, amíg megjelennek a másolási tevékenység futásának részletei, beleértve az olvasott és írt adatok méretét. Ezután az eszközök, például az Azure Storage Explorer segítségével ellenőrizze, hogy a blob másolása *a outputBlobPath a* *inputBlobPath* a változókban megadott módon.

A kimenetnek a következő mintához kell hasonlítania:

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
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

Az oktatóanyagban a következő feladatokat végezte el:

> [!div class="checklist"]
> * Data factory létrehozása
> * Azure Storage-beli társított szolgáltatás létrehozása
> * Azure Blob-adatkészlet létrehozása
> * Másolási tevékenységet és webes tevékenységet tartalmazó folyamat létrehozása
> * Tevékenységek kimeneteinek elküldése a soron következő tevékenységek számára
> * Paraméterátadás és rendszerváltozók használata
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Az Azure Data Factory-val kapcsolatos további információkért folytathatja a Concepts (Fogalmak) szakaszt.
> [!div class="nextstepaction"]
>[Folyamatok és tevékenységek](concepts-pipelines-activities.md)
