---
title: Adatok másolása az Azure Blob Storage-ból az SQL Database-be | Microsoft Docs
description: Ez az oktatóanyag részletes útmutatást biztosít adatok másolásához az Azure Blob Storage-ból az Azure SQL Database-be.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jingwang
ms.openlocfilehash: 630b17a3467f372190004172b31b481dcb5af3ce
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57863134"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Adatok másolása az Azure Blobból az Azure SQL Database-be az Azure Data Factory segítségével
Ebben az oktatóanyagban olyan adat-előállító folyamatot hoz létre, amely az Azure Blob Storage-ból az Azure SQL Database-be másol adatokat. Az oktatóanyagban szereplő konfigurációs minta fájlalapú adattárból relációs adattárba való másolásra vonatkozik. A forrásként és fogadóként támogatott adattárak listája a [támogatott adattárakat tartalmazó](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban található.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure Storage-beli és Azure SQL Database-beli társított szolgáltatások létrehozása
> * Azure Blob- és Azure SQL Database-adatkészletek létrehozása
> * Másolási tevékenységet tartalmazó folyamat létrehozása
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Ez az oktatóanyag a .NET SDK-t használja. Az Azure Data Factoryvel való interakció más módszerekkel is lehetséges. Ezekkel kapcsolatban a „Gyors útmutatók” részben találhat példákat.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

* **Azure Storage-fiók** A blobtárolót használjuk **forrás** adattárként. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../storage/common/storage-quickstart-create-account.md) ismertető cikket.
* **Azure SQL Database** Ezt az adatbázist használjuk **fogadó** adattárként. Ha még nem rendelkezik Azure SQL Database-adatbázissal, a létrehozás folyamatáért lásd az [Azure SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikket.
* **Visual Studio** 2015 vagy 2017. A jelen cikkben található útmutató a Visual Studio 2017-et használja.
* **Az [Azure .NET SDK](https://azure.microsoft.com/downloads/)** letöltése és telepítése.
* **Egy alkalmazás létrehozása az Azure Active Directoryban** [ennek az útmutatónak](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) a lépéseit követve. Jegyezze fel a következő értékeket, amelyeket a későbbi lépésekben fog használni: **alkalmazásazonosító**, **hitelesítési kulcs** és **bérlőazonosító**. Rendelje hozzá az alkalmazást a **Közreműködő** szerepkörhöz az ugyanebben a cikkben található utasításokat követve.

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

Készítse elő az Azure Blobot és az Azure SQL Database-t az oktatóanyaghoz a következő lépésekkel:

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

1. Indítsa el a Jegyzettömböt. Másolja be a következő szöveget, és mentse **inputEmp.txt** néven a lemezen.

    ```
    John|Doe
    Jane|Doe
    ```

2. Az [Azure Storage Explorerrel](https://storageexplorer.com/) vagy egy hasonló eszközzel hozza létre az **adfv2tutorial** tárolót, és töltse fel az **inputEmp.txt** fájlt a tárolóba.

#### <a name="create-a-sink-sql-table"></a>Fogadó SQL-tábla létrehozása

1. A következő SQL-szkripttel hozza létre a **dbo.emp** táblát az Azure SQL Database-ben.

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

2. Engedélyezze az SQL Server elérését az Azure-szolgáltatások számára. Győződjön meg arról, hogy az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás **BE** van kapcsolva az Azure SQL Serverhez, hogy a Data Factory szolgáltatás tudjon adatokat írni az Azure SQL Serverre. A beállítás ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

    1. Kattintson a **További szolgáltatások** hubra a bal oldalon, majd az **SQL-kiszolgálók** elemre.
    2. Válassza ki a kiszolgálót, és kattintson a **BEÁLLÍTÁSOK** területen a **Tűzfal** elemre.
    3. A **Tűzfalbeállítások** lapon kattintson a **BE** kapcsolóra az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás mellett.


## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

Hozzon létre egy, a C# nyelvet használó .NET-konzolalkalmazást a Visual Studio 2015/2017 segítségével.

1. Indítsa el a **Visual Studiót**.
2. Kattintson a **File** (Fájl) menüre, mutasson a **New** (Új) elemre, és kattintson a **Project** (Projekt) lehetőségre.
3. A projekttípusok jobb oldalon megjelenő listájában válassza a **Visual C#** -> **Konzolalkalmazás (.NET-keretrendszer)** elemet. A lépések elvégzéséhez a .NET 4.5.2-es vagy újabb verziója szükséges.
4. Adja a projektnek az **ADFv2Tutorial** nevet.
5. A projekt létrehozásához kattintson az **OK** gombra.

## <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

1. Kattintson a **Tools** (Eszközök)  -> **NuGet Package Manager** (NuGet-csomagkezelő) -> **Package Manager Console** (Csomagkezelő konzol) elemre.
2. Az a **Package Manager Console**, csomagok telepítéséhez a következő parancsokat. Tekintse meg [Microsoft.Azure.Management.DataFactory nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/) adatokkal.

    ```
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager
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
    
2. Adja hozzá az alábbi kódot a **Main** metódushoz, amely beállítja a változókat. Cserélje le a helyőrzőket saját értékeire. Azure-régióban, amelyben a Data Factory jelenleg listája, válassza ki a régiók, amelyek a következő oldalon érdeklődésére számot tartó, és bontsa ki **Analytics** található **adat-előállító**: [Régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/). Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "East US";
    string dataFactoryName = "<specify the name of a data factory to create. It must be globally unique.>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString = "Server=tcp:<your server name>.database.windows.net,1433;Database=<your database name>;User ID=<your username>@<your server name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
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

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehozza az **adat-előállítót**.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
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

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Ebben az oktatóanyagban két társított szolgáltatást hoz létre, egy forrás és egy fogadó szolgáltatást:

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **Azure Storage-beli társított szolgáltatást**. A támogatott tulajdonságokról és adatokról az [Azure Blobbeli társított szolgáltatások tulajdonságait](connector-azure-blob-storage.md#linked-service-properties) ismertető részből tudhat meg többet.

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

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **Azure SQL Database-beli társított szolgáltatást**. A támogatott tulajdonságokról és adatokról az [Azure SQL Database-beli társított szolgáltatások tulajdonságait](connector-azure-sql-database.md#linked-service-properties) ismertető részből tudhat meg többet.

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings));
```

## <a name="create-datasets"></a>Adatkészletek létrehozása

Ebben a részben két adatkészletet hoz létre: egyet a forráshoz és egyet a fogadóhoz. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Adatkészlet létrehozása a forrás Azure Blobhoz

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **Azure Blob-adatkészletet**. A támogatott tulajdonságokról és adatokról az [Azure Blob-adatkészlet tulajdonságait](connector-azure-blob-storage.md#dataset-properties) ismertető részből tudhat meg többet.

Meghatároz egy adatkészletet, amely a forrásadatokat jelöli az Azure Blobban. Ez a Blob-adatkészlet az előző lépésben létrehozott Azure Storage-beli társított szolgáltatásra vonatkozik, és a következőket írja le:

- A blob másolása helye: **FolderPath** és **FileName**;
- A blob formátumát, a tartalom módját jelzi: **TextFormat** és annak beállításait (például a columndelimiter).
- Az adatstruktúrát, beleértve az oszlopok neveit és az adattípusokat, amelyek ebben az esetben a fogadó SQL-táblára vannak leképezve.

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
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement
            {
                Name = "FirstName",
                Type = "String"
            },
            new DatasetDataElement
            {
                Name = "LastName",
                Type = "String"
            }
        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Adatkészlet létrehozása a fogadó Azure SQL Database-hez

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **Azure SQL Database-adatkészletet**. A támogatott tulajdonságokról és adatokról az [Azure SQL Database-adatkészlet tulajdonságait](connector-azure-sql-database.md#dataset-properties) ismertető részből tudhat meg többet.

Meghatároz egy adatkészletet, amely a fogadó adatait jelöli az Azure SQL Database-ben. Ez az adatkészlet az előző lépésben létrehozott Azure SQL Database-beli társított szolgáltatásra vonatkozik. Emellett megadja a másolt adatokat tartalmazó SQL-táblázatot is. 

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
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Folyamat létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehozza a **másolási tevékenységet tartalmazó folyamatot**. Ebben az oktatóanyagban ez a folyamat egy tevékenységet tartalmaz: egy másolási tevékenységet, amely a Blob-adatkészletet használja forrásként és az SQL-adatkészletet fogadóként. A másolási tevékenységről [a másolási tevékenység áttekintéséből](copy-activity-overview.md) tudhat meg többet.

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
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sqlDatasetName
                }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely **elindítja a folyamat futását**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName).Result.Body;
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
    }
    else
        Console.WriteLine(activityRuns.First().Error);
    
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>A kód futtatása

Állítsa össze és indítsa el az alkalmazást, majd ellenőrizze a folyamat-végrehajtást.

A konzol megjeleníti az adat-előállító, a társított szolgáltatás, az adatkészletek, a folyamat, valamint a folyamat futása létrehozásának állapotát. Ezután ellenőrzi a folyamat futási állapotát. Várjon, amíg megjelennek a másolási tevékenység futásának részletei, beleértve az olvasott és írt adatok méretét. Ezután az SSMS-sel (SQL Server Management Studio), a Visual Studióval vagy hasonló eszközzel csatlakozzon a cél Azure SQL Database-hez, és ellenőrizze, hogy az adatok be lettek-e másolva a megadott táblába.

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
> * Másolási tevékenységet tartalmazó folyamat létrehozása
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása


Folytassa a következő oktatóanyaggal, amelyben azzal ismerkedhet meg, hogyan másolhat adatokat a helyszíni rendszerből a felhőbe: 

> [!div class="nextstepaction"]
>[Adatok másolása a helyszínről a felhőbe](tutorial-hybrid-copy-powershell.md)
