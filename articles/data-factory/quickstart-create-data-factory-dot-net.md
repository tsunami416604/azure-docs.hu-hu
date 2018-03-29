---
title: Azure-beli adat-előállító létrehozása a .NET használatával | Microsoft Docs
description: Létrehozhat egy Azure-beli adat-előállítót az adatok egy Azure Blob Storage-beli helyről egy másik helyre történő másolásához.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 7e3fd12b2157d28c5d61bfa8d5fb69a00557a1f9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-data-factory-and-pipeline-using-net-sdk"></a>Adat-előállító és folyamat létrehozása a .NET SDK használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [2. verzió – Előzetes verzió](quickstart-create-data-factory-dot-net.md)

Ez a rövid útmutató bemutatja, hogyan használható a .NET SDK egy Azure-beli adat-előállító létrehozásához. Az adat-előállítóban létrehozott folyamat adatokat **másol** egy Azure-blobtároló egyik mappájából egy másikba. Az adatok Azure Data Factoryval történő **átalakításának** útmutatásáért olvassa el az [az adatok Spark segítségével történő átalakítását ismertető oktatóanyagot](transform-data-using-spark.md). 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd a [Data Factory 1. verzió használatának első lépéseit](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Ez a cikk nem mutatja be részletesen a Data Factory szolgáltatást. Ha szeretné megismerni az Azure Data Factoryt, tekintse meg [Az Azure Data Factory bemutatását](introduction.md).

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-subscription"></a>Azure-előfizetés
Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

### <a name="azure-roles"></a>Azure-szerepkörök
Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie. Az Azure Portalon kattintson a **felhasználónévre** a jobb felső sarokban, majd válassza az **Engedélyek** elemet az előfizetésben található engedélyek megtekintéséhez. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. Ha szeretne példautasításokat látni egy felhasználó szerepkörhöz adására, olvassa el a [Szerepkörök hozzáadása](../billing/billing-add-change-azure-subscription-administrator.md) című cikket.

### <a name="azure-storage-account"></a>Azure Storage-tárfiók neve
Ebben a rövid útmutatóban egy általános célú Azure Storage-fiókot (ebben az esetben blobtárolót) használunk **forrás-** és **céladattárként**. Ha még nem rendelkezik általános célú Azure Storage-fiókkal, tekintse meg a [Tárfiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket. 

#### <a name="get-storage-account-name-and-account-key"></a>Tárfióknév és fiókkulcs beszerzése
Ebben a rövid útmutatóban az Azure Storage-fiók nevét és kulcsát használjuk. Az alábbi eljárás bemutatja a tárfióknév és -kulcs beszerzéséhez szükséges lépéseket. 

1. Nyisson meg egy webböngészőt, és keresse fel az [Azure Portalt](https://portal.azure.com). Jelentkezzen be az Azure-beli felhasználónevével és jelszavával. 
2. Kattintson a **További szolgáltatások >** elemre a bal oldali menüben, állítson be egy szűrőt a **Tárfiók** kulcsszóval, majd válassza a **Tárfiókok** lehetőséget.

    ![Tárfiók keresése](media/quickstart-create-data-factory-dot-net/search-storage-account.png)
3. A tárfiókok listájában állítson be szűrőt a tárfiók nevéhez (ha szükséges), majd válassza ki a **tárfiókját**. 
4. A **Tárfiók** oldalon a menüben válassza a **Hozzáférési kulcsok** elemet.

    ![Tárfióknév és -kulcs beszerzése](media/quickstart-create-data-factory-dot-net/storage-account-name-key.png)
5. Másolja a **Tárfiók neve** és az **1. kulcs** mezők értékét a vágólapra. Illessze be őket a Jegyzettömbbe, vagy bármely más szerkesztőbe, majd mentse a fájlt.  

#### <a name="create-input-folder-and-files"></a>Bemeneti mappa és fájlok létrehozása
Ebben a szakaszban egy **adftutorial** nevű blobtárolót hoz létre az Azure Blob Storage-ban. Ezután létrehoz egy **input** nevű mappát a tárolóban, majd feltölt egy mintafájlt az input mappába. 

1. A **Storage-fiók** lapon váltson át az **Áttekintés** panelre, majd kattintson a **Blobok** elemre. 

    ![A Blobok elem választása](media/quickstart-create-data-factory-dot-net/select-blobs.png)
2. A **Blob service** lapon kattintson az eszköztár **+ Tároló** elemére. 

    ![Tároló hozzáadása gomb](media/quickstart-create-data-factory-dot-net/add-container-button.png)    
3. Az **Új tároló** párbeszédablakban adja meg az **adftutorial** nevet, és kattintson az **OK** gombra. 

    ![Tárolónév megadása](media/quickstart-create-data-factory-dot-net/new-container-dialog.png)
4. A tárolók listájában kattintson az **adftutorial** elemre. 

    ![A tároló kiválasztása](media/quickstart-create-data-factory-dot-net/select-adftutorial-container.png)
1. A **Tároló** lapon kattintson az eszköztár **Feltöltés** elemére.  

    ![Feltöltés gomb](media/quickstart-create-data-factory-dot-net/upload-toolbar-button.png)
6. A **Blob feltöltése** lapon kattintson a **Speciális** elemre.

    ![Kattintás a Speciális hivatkozásra](media/quickstart-create-data-factory-dot-net/upload-blob-advanced.png)
7. Indítsa el a **Jegyzettömböt**, és hozzon létre egy **emp.txt** nevű fájlt a következő tartalommal. Mentse a fájlt a **c:\ADFv2QuickStartPSH** mappába. Ha még nem létezik, hozza létre az **ADFv2QuickStartPSH** mappát.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. Az Azure Portal **Blob feltöltése** lapjának **Fájlok** mezőben keresse meg, és válassza ki az **emp.txt** fájlt. 
9. Adja meg az **input** értéket a **Feltöltés mappába** mezőben. 

    ![Blobbeállítások feltöltése](media/quickstart-create-data-factory-dot-net/upload-blob-settings.png)    
10. Ellenőrizze, hogy a mappa az **input** mappa-e, a fájl pedig az **emp.txt** fájl-e, majd kattintson a **Feltöltés** elemre.
11. A listában meg kell jelennie az **emp.txt** fájlnak és a feltöltés állapotának. 
12. A sarokban található **X** gombra kattintva zárja be a **Blob feltöltése** lapot. 

    ![A Blob feltöltése lap bezárása](media/quickstart-create-data-factory-dot-net/close-upload-blob.png)
1. Ne zárja be a **Tároló** lapot. A segítségével ellenőrizheti ennek a rövid útmutatónak az eredményét.

### <a name="visual-studio"></a>Visual Studio
A jelen cikkben található útmutató a Visual Studio 2017-et használja. A Visual Studio 2013-at vagy 2015-öt is használhatja.

### <a name="azure-net-sdk"></a>Azure .NET SDK
Töltse le és telepítse az [Azure .NET SDK](http://azure.microsoft.com/downloads/)-t a gépen.

### <a name="create-an-application-in-azure-active-directory"></a>Alkalmazás létrehozása az Azure Active Directoryban
[A cikkben](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) lévő utasításokkal a következő feladatokat végezheti el: 

1. **Egy Azure Active Directory-alkalmazás létrehozása**. Olyan alkalmazást hozhat létre az Azure Active Directoryban, amely az oktatóanyagban létrehozott .NET-alkalmazást képviseli. A bejelentkezési URL-hez megadhat egy hamis URL-t, a cikkben láthatóak szerint (`https://contoso.org/exampleapp`).
2. Szerezze be az **alkalmazásazonosítót** és a **hitelesítési kulcsot**** a cikk **Alkalmazásazonosító és hitelesítési kulcs beszerzése** szakaszának utasításai alapján. Jegyezze fel ezeket az értékeket, mert később használni fogja őket az oktatóanyagban. 
3. Szerezze be a **bérlőazonosítót** a cikk **Bérlőazonosító beszerzése** szakaszának utasításait követve. Jegyezze fel az értékét. 
4. Rendelje az alkalmazást a **Közreműködő** szerepkörhöz az előfizetés szintjén, hogy az alkalmazás adat-előállítókat hozhasson létre az előfizetésben. Ehhez kövesse a cikk **Alkalmazás hozzárendelése szerepkörhöz** szakaszát. 

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

2. Adja hozzá az alábbi kódot a **Main** metódushoz, amely beállítja a változókat. A helyőrzőket cserélje le a saját értékeire. A Data Factory 2-es verziója jelenleg csak az USA keleti régiójában, az USA 2. keleti régiójában és a nyugat-európai régióban teszi lehetővé adat-előállítók létrehozását. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID where the data factory resides>";
    string resourceGroup = "<your resource group where the data factory resides>";
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

1. Adja hozzá a következő kódot a **Main** metódushoz az állapot folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez.

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

## <a name="verify-the-output"></a>Kimenet ellenőrzése
A folyamat automatikusan létrehozza a kimeneti mappát az adftutorial blobtárolóban. Ezután átmásolja az emp.txt fájlt a bemeneti mappából a kimeneti mappába. 

1. Az Azure Portal **adftutorial** tároló lapján kattintson a **Frissítés** elemre a kimeneti mappa megtekintéséhez. 
    
    ![Frissítés](media/quickstart-create-data-factory-dot-net/output-refresh.png)
2. Kattintson a mappalista **kimenet** elemére. 
2. Ellenőrizze, hogy az **emp.txt** fájl bekerült-e a kimeneti mappába. 

    ![Frissítés](media/quickstart-create-data-factory-dot-net/output-file.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az adat-előállító programkódból történő törléséhez adja hozzá az alábbi kódsorokat a programhoz: 

```csharp
            Console.WriteLine("Deleting the data factory");
            client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információkért tekintse meg az [oktatóanyagokat](tutorial-copy-data-dot-net.md). 
