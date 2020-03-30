---
title: Azure-adatgyár létrehozása a .NET SDK használatával
description: Létrehozhat egy Azure-beli adat-előállítót az adatok egy Azure Blob Storage-beli helyről egy másik helyre történő másolásához.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/24/2019
ms.author: jingwang
ms.openlocfilehash: a2b775afcd9e603a11b560bb7c42d6cf76be9b34
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241555"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-net-sdk"></a>Rövid útmutató: Adat-előállító és folyamat létrehozása a .NET SDK használatával

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuális verzió](quickstart-create-data-factory-dot-net.md)

Ez a rövid útmutató bemutatja, hogyan használható a .NET SDK egy Azure-beli adat-előállító létrehozásához. Az adat-előállítóban létrehozott folyamat adatokat **másol** egy Azure-blobtároló egyik mappájából egy másikba. Az Azure Data Factory használatával az adatok **átalakításáról** az [Oktatóanyag: Adatok átalakítása a Spark használatával](tutorial-transform-data-spark-portal.md)című témakörben található.

> [!NOTE]
> Ez a cikk nem mutatja be részletesen a Data Factory szolgáltatást. Ha szeretné megismerni az Azure Data Factoryt, tekintse meg [Az Azure Data Factory bemutatását](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="visual-studio"></a>Visual Studio

A cikkforgatókönyv a Visual Studio 2019-et használja. A Visual Studio 2013, 2015 vagy 2017 eljárásai némileg eltérnek egymástól.

### <a name="azure-net-sdk"></a>Azure .NET SDK

Töltse le és telepítse az [Azure .NET SDK](https://azure.microsoft.com/downloads/)-t a gépen.

## <a name="create-an-application-in-azure-active-directory"></a>Alkalmazás létrehozása az Azure Active Directoryban

A *szakaszok hogyan: A portál használatával hozzon létre egy Azure AD-alkalmazás és egyszerű szolgáltatás, amely képes hozzáférni az erőforrásokhoz,* kövesse az utasításokat az alábbi feladatok elvégzéséhez:

1. Az [Azure Active Directory-alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)csoportban hozzon létre egy alkalmazást, amely az oktatóanyagban létrehozott .NET alkalmazást jelöli. A bejelentkezési URL-hez megadhat egy hamis URL-t, a cikkben láthatóak szerint (`https://contoso.org/exampleapp`).
2. A [Bejelentkezési értékek beszerezni,](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)az **alkalmazás azonosítóját** és a **bérlői azonosítót,** és jegyezze fel ezeket az értékeket, amelyeket később az oktatóanyagban használ. 
3. A [Tanúsítványok és titkos kulcsok](../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets), leszerez a **hitelesítési kulcsot**, és jegyezze fel ezt az értéket, amelyet később az oktatóanyagban használ.
4. Az [alkalmazás hozzárendelése egy szerepkörhöz,](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)rendelje hozzá az alkalmazást a **közreműködői** szerepkör az előfizetés szintjén, hogy az alkalmazás hozhat létre adatgyárak az előfizetésben.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

Ezután hozzon létre egy C# .NET konzolalkalmazást a Visual Studióban:

1. Indítsa el a **Visual Studiót**.
2. A Start ablakban válassza **az Új projekt konzolalkalmazás** > **(.NET Framework)** létrehozása lehetőséget. A lépések elvégzéséhez a .NET 4.5.2-es vagy újabb verziója szükséges.
3. A **Project neve**mezőbe írja be az **ADFv2QuickStart értéket.**
4. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

1. Válassza **az Eszközök** > **NuGet Csomagkezelő csomagkezelő** > **konzolt.**
2. A **Csomagkezelő konzol** ablaktábláján futtassa a következő parancsokat a csomagok telepítéséhez. További információt a [Microsoft.Azure.Management.DataFactory nuget csomagban](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/)talál.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
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

2. Adja hozzá az alábbi kódot a **Main** metódushoz, amely beállítja a változókat. Cserélje le a helyőrzőket a saját értékeire. Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adattárak (Azure Storage, Azure SQL Database, és több) és a számítási (HDInsight és mások) által használt adatgyár lehet más régiókban.

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string dataFactoryName = 
       "<specify the name of data factory to create. It must be globally unique.>";
   string storageAccount = "<your storage account name to copy data>";
   string storageKey = "<your storage account key>";
   // specify the container and input folder from which all files 
   // need to be copied to the output folder. 
   string inputBlobPath =
       "<path to existing blob(s) to copy data from, e.g. containername/inputdir>";
   //specify the contains and output folder where the files are copied
   string outputBlobPath =
       "<the blob path to copy data to, e.g. containername/outputdir>";

   // name of the Azure Storage linked service, blob dataset, and the pipeline
   string storageLinkedServiceName = "AzureStorageLinkedService";
   string blobDatasetName = "BlobDataset";
   string pipelineName = "Adfv2QuickStartPipeline";
   ```

3. Adja hozzá a következő kódot a **Fő** metódushoz, amely létrehozza a **DataFactoryManagementClient** osztály egy példányát. Ezzel az objektummal adat-előállítót, társított szolgáltatást, adatkészleteket és adatcsatornát is létrehozhat. Ezenfelül ez az objektum a folyamat futása részleteinek monitorozására is használható.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
       "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) {
       SubscriptionId = subscriptionId };
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
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState ==
       "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>Társított szolgáltatás létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **Azure Storage-beli társított szolgáltatást**.

Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben a rövid útmutatóban csak egy Azure Storage-kapcsolt szolgáltatást kell létrehoznia mind a másolási forráshoz, mind a fogadótárolóhoz; a minta "AzureStorageLinkedService" nevű.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(
    storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>Adatkészlet létrehozása

Adja hozzá a következő kódot a **fő** metódushoz, amely létrehoz egy **Azure blob adatkészletet.**

Megadhat egy adatkészletet, amely a forrásból a fogadóba másolt adatokat jelöli. Ebben a példában a blob-adatkészlet az előző lépésben létrehozott Azure Storage-beli társított szolgáltatásra vonatkozik. Az adatkészlethez egy olyan paraméter szükséges, amelynek az értéke az adatkészletet feldolgozó tevékenységben van beállítva. A paraméter a "folderPath" létrehozásához használható, amely az adatok tárolási helyére/tárolására mutat.

```csharp
// Create an Azure Blob dataset
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
client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Folyamat létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehozza a **másolási tevékenységet tartalmazó folyamatot**.

Ebben a példában ez a folyamat egy tevékenységet tartalmaz, és két paramétert vesz igénybe: a bemeneti blob elérési útját és a kimeneti blob elérési útját. A paraméterek értékei a folyamat indításakor/futtatásakor lesznek beállítva. A másolási tevékenység az előző lépésben kimenetként és bemenetként létrehozott blob-adatkészletre hivatkozik. Ha az adatkészlet bemeneti adatkészletként van használatban, a bemeneti elérési út van megadva. Ha az adatkészlet kimeneti adatkészletként van használatban, a kimeneti elérési út van megadva. 

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

Ez a kód a folyamatban megadott **inputPath** és **outputPath** paraméterek értékeit is beállítja a forrás- és fogadóblob-útvonalak tényleges értékeivel.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName, parameters: parameters
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Folyamat futásának monitorozása

1. Adja hozzá a következő kódot a **Main** metódushoz az állapot folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez.

   ```csharp
   // Monitor the pipeline run
   Console.WriteLine("Checking pipeline run status...");
   PipelineRun pipelineRun;
   while (true)
   {
       pipelineRun = client.PipelineRuns.Get(
           resourceGroup, dataFactoryName, runResponse.RunId);
       Console.WriteLine("Status: " + pipelineRun.Status);
       if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
           System.Threading.Thread.Sleep(15000);
       else
           break;
   }
   ```

2. Adja hozzá a következő kódot a **Fő** metódushoz, amely lekéri a másolási tevékenység futtatásának részleteit, például az olvasott vagy írt adatok méretét.

   ```csharp
   // Check the copy activity run details
   Console.WriteLine("Checking copy activity run details...");

   RunFilterParameters filterParams = new RunFilterParameters(
       DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10));
   ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
       resourceGroup, dataFactoryName, runResponse.RunId, filterParams);
   if (pipelineRun.Status == "Succeeded")
       Console.WriteLine(queryResponse.Value.First().Output);
   else
       Console.WriteLine(queryResponse.Value.First().Error);
   Console.WriteLine("\nPress any key to exit...");
   Console.ReadKey();
   ```

## <a name="run-the-code"></a>A kód futtatása

Állítsa össze és indítsa el az alkalmazást, majd ellenőrizze a folyamat-végrehajtást.

A konzol megjeleníti az adat-előállító, a társított szolgáltatás, az adatkészletek, a folyamat, valamint a folyamat futása létrehozási állapotát. Ezután ellenőrzi a folyamat futási állapotát. Várjon, amíg meg nem jelenik a másolási tevékenység futtatási részletei az olvasási/írási adatok méretével. Ezután az eszközök, például az [Azure Storage explorer](https://azure.microsoft.com/features/storage-explorer/) segítségével ellenőrizze a blob(ok) másolja a "outputBlobPath" a "inputBlobPath", aváltozókban megadott módon.

### <a name="sample-output"></a>Példa kimenet

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
    "usedDataIntegrationUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Kimenet ellenőrzése

A folyamat automatikusan létrehozza a kimeneti mappát az **adftutorial** blob tárolóban. Ezután másolja az **emp.txt** fájlt a bemeneti mappából a kimeneti mappába. 

1. Az Azure Portalon az **adftutorial** tároló lapon, amely megállt a [bemeneti mappa hozzáadása és fájl a blob tároló](#add-an-input-folder-and-file-for-the-blob-container) szakasz felett, válassza a **Frissítés** a kimeneti mappa megtekintéséhez. 
2. A mappalistában válassza a **kimenet**lehetőséget.
3. Ellenőrizze, hogy az **emp.txt** fájl bekerült-e a kimeneti mappába. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adatsalóprogram programozott törléséhez adja hozzá a következő kódsorokat a programhoz: 

```csharp
Console.WriteLine("Deleting the data factory");
client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>További lépések

A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információkért tekintse meg az [oktatóanyagokat](tutorial-copy-data-dot-net.md). 
