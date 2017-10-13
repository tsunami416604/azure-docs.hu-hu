---
title: "Azure-beli adat-előállító létrehozása a .NET használatával | Microsoft Docs"
description: "Létrehozhat egy Azure-beli adat-előállítót az adatok egy Azure Blob Storage egyik helyéről egy másik helyére történő másolásához."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/06/2017
ms.author: jingwang
ms.openlocfilehash: d78176eca6bdbf32d6b4400ad2812dea98703d67
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-data-factory-and-pipeline-using-net-sdk"></a>Adat-előállító és folyamat létrehozása a .NET SDK használatával
Az Azure Data Factory egy felhőalapú adatintegrációs szolgáltatás. Lehetővé teszi olyan, a felhőben futó, adatvezérelt munkafolyamatok létrehozását, amelyek alkalmasak az adatok átvitelének és átalakításának irányítására és automatizálására. Az Azure Data Factory segítségével létrehozhatók és ütemezhetők a különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok, feldolgozhatók és átalakíthatók az adatok különböző számítási szolgáltatások használatával (pl. Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics és Azure Machine Learning), és a kimeneti adatok közzétehetők olyan adattárakban, mint például az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket. 

Ez a rövid útmutató bemutatja, hogyan használható a .NET SDK egy Azure-beli adat-előállító létrehozásához. Az adat-előállító folyamata adatokat másol az Azure Blob Storage egyik mappájából egy másik mappába.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek
* **Azure Storage-fiók** A blobtároló **forrás-** és **fogadó**adattárként lesz használatban. Ha még nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket. 
* Hozzon létre egy **blobtárolót** a Blob Storage alatt, majd hozzon létre egy bemeneti **mappát** a tárolóban, és töltsön fel néhány fájlt a mappába. Az [Azure Storage Explorerrel](https://azure.microsoft.com/features/storage-explorer/) és hozzá hasonló eszközökkel csatlakozhat az Azure Blob Storage-hoz, blobtárolókat hozhat létre, bemeneti fájlokat tölthet fel, és ellenőrizheti a kimeneti fájlokat.
* **Visual Studio** 2013, 2015 vagy 2017. A jelen cikkben található útmutató a Visual Studio 2017-et használja.
* **Az [Azure .NET SDK](http://azure.microsoft.com/downloads/)** letöltése és telepítése.
* **Egy alkalmazás létrehozása az Azure Active Directoryban** [ennek az útmutatónak](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) a lépéseit követve. Jegyezze fel a következő értékeket, amelyeket a későbbi lépésekben fog használni: **alkalmazásazonosító**, **hitelesítési kulcs** és **bérlőazonosító**. Rendelje hozzá az alkalmazást a **Közreműködő** szerepkörhöz az ugyanebben a cikkben található utasításokat követve. 
*  

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

Hozzon létre egy, a C# nyelvet használó .NET-konzolalkalmazást a Visual Studio 2013/2015/2017 segítségével.

1. Indítsa el a **Visual Studiót**.
2. Kattintson a **File** (Fájl) menüre, mutasson a **New** (Új) elemre, és kattintson a **Project** (Projekt) lehetőségre.
3. A projekttípusok jobb oldalon megjelenő listájában válassza a **Visual C#** -> **Konzolalkalmazás (.NET-keretrendszer)** elemet. A lépések elvégzéséhez a .NET 4.5.2-es vagy újabb verziója szükséges.
4. Írja be az **ADFv2QuickStart** értéket a Név mezőbe.
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

2. Adja hozzá az alábbi kódot a **Main** metódushoz, amely beállítja a változókat. A helyőrzőket cserélje le a saját értékeire.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID where the data factory resides>";
    string resourceGroup = "<your resource group where the data factory resides>";
    // Currently, Data Factory V2 allows you to create data factories only in the East US and East US2 regions. 
    // Note that the data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by data factory can be in other regions
    string region = "East US 2";
    string dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    // specify the container and input folder from which all files need to be copied to the output folder. 
    string inputBlobPath = "<the path to existing blob(s) to copy data from, e.g. containername/foldername>";
    //specify the contains and output folder where the files are copied
    string outputBlobPath = "<the blob path to copy data to, e.g. containername/foldername>";

    string storageLinkedServiceName = "AzureStorageLinkedService";  // name of the Azure Storage linked service
    string blobDatasetName = "BlobDataset";             // name of the blob dataset
    string pipelineName = "Adfv2QuickStartPipeline";    // name of the pipeline
    ```

3. Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **DataFactoryManagementClient** osztályú példányt. Ezzel az objektummal adat-előállítót, társított szolgáltatást, adatkészleteket és adatcsatornát is létrehozhat. Ezenfelül ez az objektum a folyamat futása részleteinek monitorozására is használható.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Data factory létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehozza az **adat-előállítót**. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>Társított szolgáltatás létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **Azure Storage-beli társított szolgáltatást**.

Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben a rövid útmutatóban csak egy Azure Storage-beli társított szolgáltatást kell létrehoznia a másolás forrásaként és fogadó adattárként. A példában ennek a neve: AzureStorageLinkedService.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>Adatkészlet létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **Azure Blob-adatkészletet**.

Megadhat egy adatkészletet, amely a forrásból a fogadóba másolt adatokat jelöli. Ebben a példában a blob-adatkészlet az előző lépésben létrehozott Azure Storage-beli társított szolgáltatásra vonatkozik. Az adatkészlethez egy olyan paraméter szükséges, amelynek az értéke az adatkészletet feldolgozó tevékenységben van beállítva. A paraméter az adatok tárolására szolgáló helyre mutató „folderPath” létrehozására használható.

```csharp
// Create a Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }

        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Folyamat létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehozza a **másolási tevékenységet tartalmazó folyamatot**.

Ebben a példában a folyamat egy tevékenységet tartalmaz, és két paraméter szükséges hozzá: a bemeneti blob elérési útja és a kimeneti blob elérési útja. A paraméterek értékei a folyamat indításakor/futtatásakor lesznek beállítva. A másolási tevékenység az előző lépésben kimenetként és bemenetként létrehozott blob-adatkészletre hivatkozik. Ha az adatkészlet bemeneti adatkészletként van használatban, a bemeneti elérési út van megadva. Ha az adatkészlet kimeneti adatkészletként van használatban, a kimeneti elérési út van megadva. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely **elindítja a folyamat futását**.

Ez a kód a folyamatban meghatározott **inputPath** és **outputPath** paraméter értékét is megadja, a forrás- és fogadó-blob elérési útjának tényleges értéke alapján.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters).Result.Body;
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
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>A kód futtatása

Állítsa össze és indítsa el az alkalmazást, majd ellenőrizze a folyamat-végrehajtást.

A konzol megjeleníti az adat-előállító, a társított szolgáltatás, az adatkészletek, a folyamat, valamint a folyamat futása létrehozási állapotát. Ezután ellenőrzi a folyamat futási állapotát. Várjon, amíg megjelennek a másolási tevékenység futásának részletei, beleértve az olvasott és írt adatok méretét. Ezután például az [Azure Storage Explorerhez](https://azure.microsoft.com/features/storage-explorer/) hasonló eszközök használatával ellenőrizheti, hogy a blobok a változókban megadottak szerint át lettek-e másolva az outputBlobPath helyre az inputBlobPath helyről.

### <a name="sample-output"></a>Példa a kimenetre: 
```json
Creating data factory SPv2Factory0907...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
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
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 2,
    "billedDuration": 23
}

Press any key to exit...

```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az adat-előállító programkódból történő törléséhez adja hozzá az alábbi kódsorokat a programhoz: 

```csharp
            Console.WriteLine("Deleting the data factory");
            client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Következő lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információkért tekintse meg az [oktatóanyagokat](tutorial-copy-data-dot-net.md). 
