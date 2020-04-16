---
title: Adatok másolása az Azure Blob Storage szolgáltatásból az Azure SQL Database-be
description: Ez az oktatóanyag részletes útmutatást biztosít adatok másolásához az Azure Blob Storage-ból az Azure SQL Database-be.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: jingwang
ms.openlocfilehash: a835e67b1091a55c832955d8dac8615289a6d99e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418694"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Adatok másolása az Azure Blobból az Azure SQL Database-be az Azure Data Factory segítségével

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben az oktatóanyagban olyan adat-előállító folyamatot hoz létre, amely az Azure Blob Storage-ból az Azure SQL Database-be másol adatokat. Az oktatóanyagban szereplő konfigurációs minta fájlalapú adattárból relációs adattárba való másolásra vonatkozik. A forrásként és fogadóként támogatott adattárak listáját a [támogatott adattárak és formátumok című témakörben tetszésszerint.](copy-activity-overview.md#supported-data-stores-and-formats)

Az oktatóanyagban a következő lépéseket kell tennie:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure Storage-beli és Azure SQL Database-beli társított szolgáltatások létrehozása
> * Azure Blob- és Azure SQL Database-adatkészletek létrehozása
> * Másolási tevékenységet tartalmazó folyamat létrehozása
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Ez az oktatóanyag a .NET SDK-t használja. Más mechanizmusokat is használhat az Azure Data Factory használatához; tekintse meg a rövid útmutatók alatti **mintákat.**

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* *Az Azure Storage-fiók.* A blobtárolót használjuk *forrás* adattárként. Ha nem rendelkezik Azure-tárfiókkal, olvassa el az Általános célú tárfiók létrehozása című [témakört.](../storage/common/storage-account-create.md)
* *Az Azure SQL Database .* Ezt az adatbázist használjuk *fogadó* adattárként. Ha nem rendelkezik Azure SQL-adatbázissal, olvassa [el az Azure SQL-adatbázis létrehozása](../sql-database/sql-database-single-database-get-started.md)című témakört.
* *Visual Studio*. A cikkforgatókönyv a Visual Studio 2019-et használja.
* *[Azure SDK a .NET](/dotnet/azure/dotnet-tools)*.
* *Az Azure Active Directory-alkalmazás*. Ha nem rendelkezik Azure Active Directory-alkalmazással, tekintse meg az [Azure Active Directory-alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) című témakört: [Hogyan: A portál használata Azure AD-alkalmazás létrehozásához.](../active-directory/develop/howto-create-service-principal-portal.md) Másolja a következő értékeket a későbbi lépésekben való használatra: **Alkalmazás (ügyfél) azonosító**, **hitelesítési kulcs**és **címtár (bérlői) azonosító**. Rendelje hozzá az alkalmazást a **közreműködői** szerepkörhöz az ugyanabban a cikkben található utasításokat követve.

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

Most készítse elő az Azure Blob és az Azure SQL Database az oktatóanyag hoz létre egy forrásblog és egy elfogadó SQL-tábla.

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

Először hozzon létre egy forrásblobot egy tároló létrehozásával és egy bemeneti szövegfájl feltöltésével:

1. Nyissa meg a Jegyzettömböt. Másolja a következő szöveget, és mentse helyileg egy *inputEmp.txt*nevű fájlba.

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. Egy eszköz, például az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) segítségével hozza létre az *adfv2tutorial* tárolót, és töltse fel a *inputEmp.txt* fájlt a tárolóba.

#### <a name="create-a-sink-sql-table"></a>Fogadó SQL-tábla létrehozása

Ezután hozzon létre egy fogadó SQL-táblát:

1. A következő SQL-szkripttel hozza létre a *dbo.emp* táblát az Azure SQL Database-ben.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Engedélyezze az SQL Server elérését az Azure-szolgáltatások számára. Győződjön meg arról, hogy engedélyezi az Azure-szolgáltatásokhoz való hozzáférést az Azure SQL-kiszolgálón, hogy a Data Factory szolgáltatás adatokat írhass az Azure SQL-kiszolgálóra. A beállítás ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

    1. Az [SQL-kiszolgáló](https://portal.azure.com) kezeléséhez nyissa meg az Azure Portalt. SQL-kiszolgálók keresése és **kijelölése.**

    2. Válassza ki a kiszolgálót.

    3. Az SQL server menü **Biztonság** fejlécében válassza a **Tűzfalak és a virtuális hálózatok**lehetőséget.

    4. A **Tűzfal és** a virtuális hálózatok **lap, az Azure-szolgáltatások és -erőforrások hozzáférése a kiszolgálóhoz**csoportban válassza **a ON**lehetőséget.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

A Visual Studio segítségével hozzon létre egy C# .NET konzolalkalmazást.

1. Nyissa meg a Visual Studiót.
2. A **Start** ablakban válassza **az Új projekt létrehozása**lehetőséget.
3. Az **Új projekt létrehozása** ablakban válassza ki a **Konzolalkalmazás C# verzióját (.NET Framework)** a projekttípusok listájából. Ezután válassza a **Tovább**gombot.
4. Az **Új projekt konfigurálása** ablakban adja meg az *ADFv2Tutorial* **projektnevét.** A **Hely területen**keresse meg és/vagy hozza létre a könyvtárat a projekt mentéséhez. Ezután válassza **a Létrehozás lehetőséget.** Az új projekt megjelenik a Visual Studio IDE-ben.

## <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

Ezután telepítse a szükséges könyvtárcsomagokat a NuGet csomagkezelővel.

1. A menüsorban válassza az **Eszközök** > **NuGet Csomagkezelő csomagkezelő** > **konzolt**.
2. A **Csomagkezelő konzol** ablaktábláján futtassa a következő parancsokat a csomagok telepítéséhez. Az Azure Data Factory NuGet csomagról a [Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/)című témakörben talál további információt.

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Adat-előállító ügyfél létrehozása

Az alábbi lépésekkel hozzon létre egy adatfeldolgozó ügyfelet.

1. Nyissa *meg a Program.cs,* majd írja felül a meglévő `using` állításokat a következő kóddal a névterekre való hivatkozások hozzáadásához.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Adja hozzá a `Main` következő kódot a változókat beállító metódushoz. Cserélje le a 14 helyőrzőt a saját értékeire.

    Az Azure-régiók listájának megtekintéséhez, ahol a Data Factory jelenleg elérhető, olvassa el a [Termékek régiónként elérhető](https://azure.microsoft.com/global-infrastructure/services/)című témakört. A **Termékek** legördülő listában válassza az > **Analytics-adatok** > **gyárának** **böngészése**lehetőséget. Ezután a **Régiók** legördülő listában válassza ki az Önt érdeklő régiókat. Megjelenik egy rács a Data Factory-termékek rendelkezésre állási állapotával a kiválasztott régiókban.

    > [!NOTE]
    > Az adattárak, például az Azure Storage és az Azure SQL Database, és a számítások, például a HDInsight, amelyet a Data Factory használ, más régiókban is lehetnek, mint amit a Data Factory számára választott.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "<location to create the data factory in, such as East US>";
    string dataFactoryName = "<name of data factory to create (must be globally unique)>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString =
        "Server=tcp:<your server name>.database.windows.net,1433;" +
        "Database=<your database name>;" +
        "User ID=<your username>@<your server name>;" +
        "Password=<your password>;" +
        "Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Adja hozzá a `Main` következő kódot az `DataFactoryManagementClient` osztálypéldányt létrehozó metódushoz. Ezzel az objektummal adat-előállítót, társított szolgáltatást, adatkészleteket és folyamatot hozhat létre. Ezenfelül ez az objektum a folyamat futása részleteinek monitorozására is használható.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync(
        "https://management.azure.com/", cc
    ).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Data factory létrehozása

Adja hozzá a `Main` következő kódot az *adat-előállítót*létrehozó metódushoz.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};

client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings)
);

while (
    client.Factories.Get(
        resourceGroup, dataFactoryName
    ).ProvisioningState == "PendingCreation"
)
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Ebben az oktatóanyagban két összekapcsolt szolgáltatást hoz létre a forráshoz és a fogadóhoz.

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása

Adja hozzá a `Main` következő kódot az *Azure Storage-hoz csatolt szolgáltatást létrehozó metódushoz.* A támogatott tulajdonságokról és részletekről az [Azure Blobhoz csatolt szolgáltatás tulajdonságai](connector-azure-blob-storage.md#linked-service-properties)című témakörben talál további információt.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey
        )
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings)
);
```

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása

Adja hozzá a `Main` következő kódot az *Azure SQL Database-hez csatolt szolgáltatást létrehozó metódushoz.* A támogatott tulajdonságokról és részletekről az [Azure SQL Database csatolt szolgáltatástulajdonságai](connector-azure-sql-database.md#linked-service-properties)című témakörben talál további információt.

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings)
);
```

## <a name="create-datasets"></a>Adatkészletek létrehozása

Ebben a szakaszban két adatkészletet hoz létre: az egyiket a forráshoz, a másikat a fogadóhoz.

### <a name="create-a-dataset-for-source-azure-blob"></a>Adatkészlet létrehozása a forrás Azure Blobhoz

Adja hozzá a `Main` következő kódot az *Azure blob-adatkészletet létrehozó metódushoz.* A támogatott tulajdonságokról és részletekről az [Azure Blob adatkészlet tulajdonságai](connector-azure-blob-storage.md#dataset-properties)című témakörben talál.

Meghatároz egy adatkészletet, amely a forrásadatokat jelöli az Azure Blobban. Ez a Blob-adatkészlet az előző lépésben létrehozott Azure Storage-beli társított szolgáltatásra vonatkozik, és a következőket írja le:

- A blob helye, amelyből `FolderPath` másolni szeretne: és`FileName`
- A tartalom elemzésének módját jelző blobformátum `TextFormat` és annak beállításai, például az oszlophatároló
- Az adatstruktúra, beleértve az oszlopneveket és adattípusokat, amelyek ebben a példában a fogadó SQL-táblához rendelik

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement { Name = "FirstName", Type = "String" },
            new DatasetDataElement { Name = "LastName", Type = "String" }
        }
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings)
);
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Adatkészlet létrehozása a fogadó Azure SQL Database-hez

Adja hozzá a `Main` következő kódot az *Azure SQL Database-adatkészletet létrehozó metódushoz.* A támogatott tulajdonságokról és részletekről az [Azure SQL Database adatkészlet tulajdonságai című](connector-azure-sql-database.md#dataset-properties)témakörben talál további információt.

Meghatároz egy adatkészletet, amely a fogadó adatait jelöli az Azure SQL Database-ben. Ez az adatkészlet az előző lépésben létrehozott Azure SQL Database csatolt szolgáltatásra hivatkozik. Emellett megadja a másolt adatokat tartalmazó SQL-táblázatot is.

```csharp
// Create an Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings)
);
```

## <a name="create-a-pipeline"></a>Folyamat létrehozása

Adja hozzá a `Main` következő kódot ahhoz a metódushoz, amely *másolási tevékenységgel rendelkező folyamatot*hoz létre. Ebben az oktatóanyagban ez `CopyActivity`a folyamat egy tevékenységet tartalmaz: , amely a Blob-adatkészletet forrásként, az SQL-adatkészletet pedig fogadóként veszi fel. A másolási tevékenység részleteiről a [Tevékenység másolása az Azure Data Factoryban című témakörben](copy-activity-overview.md)talál.

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference() { ReferenceName = blobDatasetName }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference { ReferenceName = sqlDatasetName }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings)
);
```

## <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása

Adja hozzá a `Main` következő kódot a *folyamatfuttatást kiváltó*metódushoz.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Folyamat futásának monitorozása

Most helyezze be a kódot a folyamat futtatási állapotának ellenőrzéséhez és a másolási tevékenység futtatásának részleteihez.

1. Adja hozzá a `Main` következő kódot a metódushoz, hogy folyamatosan ellenőrizze a futó folyamat állapotát, amíg be nem fejezi az adatok másolását.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(
            resourceGroup, dataFactoryName, runResponse.RunId
        );
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Adja hozzá a `Main` következő kódot a másolási tevékenység futtatási részleteinek lekérésével kapcsolatos metódushoz, például az olvasott vagy írt adatok méretéhez.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    RunFilterParameters filterParams = new RunFilterParameters(
        DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)
    );

    ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
        resourceGroup, dataFactoryName, runResponse.RunId, filterParams
    );

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(queryResponse.Value.First().Output);
    }
    else
        Console.WriteLine(queryResponse.Value.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>A kód futtatása

Az alkalmazás**buildelési megoldása** **kiválasztásával** > hozhat létre. Ezután indítsa el az alkalmazást a **Hibakeresés** > **indítása hibakeresés**lehetőségkiválasztásával, és ellenőrizze a folyamat végrehajtását.

A konzol megjeleníti az adat-előállító, a társított szolgáltatás, az adatkészletek, a folyamat, valamint a folyamat futása létrehozásának állapotát. Ezután ellenőrzi a folyamat futási állapotát. Várjon, amíg meg nem jelenik a másolási tevékenység futtatási részletei az adatok olvasási/írásbeli méretével. Ezután az SQL Server Management Studio (SSMS) vagy a Visual Studio eszközök használatával csatlakozhat a célAzure SQL Database-hez, és ellenőrizheti, hogy a megadott céltábla tartalmazza-e a másolt adatokat.

### <a name="sample-output"></a>Példa kimenet

```json
Creating a data factory AdfV2Tutorial...
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
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
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
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
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
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedDataIntegrationUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```

## <a name="next-steps"></a>További lépések

A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure Storage-beli és Azure SQL Database-beli társított szolgáltatások létrehozása
> * Azure Blob- és Azure SQL Database-adatkészletek létrehozása
> * Másolási tevékenységet tartalmazó folyamat létrehozása.
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Folytassa a következő oktatóanyaggal, amelyben azzal ismerkedhet meg, hogyan másolhat adatokat a helyszíni rendszerből a felhőbe:

> [!div class="nextstepaction"]
>[Adatok másolása a helyszínről a felhőbe](tutorial-hybrid-copy-powershell.md)
