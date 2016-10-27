<properties
   pageTitle="Adatokat betöltése az Azure Data Factoryvel | Microsoft Azure"
   description="Sajátítsa el az adatok betöltését az Azure Data Factoryvel"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/13/2016"
   ms.author="mausher;barbkess"/>


# <a name="load-data-with-azure-data-factory"></a>Adatok betöltése az Azure Data Factoryvel 

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)  
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
- [BCP](sql-data-warehouse-load-with-bcp.md)  

Ez az oktatóanyag bemutatja, hogyan lehet folyamatokat létrehozni az Azure Data Factoryban az adatok Azure Storage-blobból Azure SQL Data Warehouse-ba való áthelyezéséhez. A következő lépésekben:

+ Mintaadatokat telepíthet egy Azure Storage-blobba.
+ Erőforrásokat csatlakoztathat az Azure Data Factoryhoz.
+ Folyamatot hozhat létre az adatok a tárolóblobokból az SQL Data Warehouse-ba való áthelyezéséhez.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## <a name="before-you-begin"></a>Előkészületek

Tekintse át az [Az Azure Data Factory bemutatása][] című cikket, és ismerje meg az Azure Data Factoryt.

### <a name="create-or-identify-resources"></a>Erőforrások létrehozása és azonosítása

Az oktatóanyag elindítása előtt rendelkeznie kell a következő erőforrásokkal:

   + **Azure Storage-blob**: Az oktatóanyagban az Azure Storage-blob lesz az adatforrás az Azure Data Factory-folyamathoz, így rendelkeznie kell egy elérhető tárolóval a mintaadatok tárolásához. Ha még nem rendelkezik ilyennel, így hozhat létre: [Tárfiók létrehozása][] .

   + **SQL Data Warehouse**: Az oktatóanyag az adatokat az Azure Storage-blob tárolóból az SQL Data Warehouse-ba helyezi át, ezért rendelkeznie kell egy online adatraktárral, amelybe be vannak töltve az AdventureWorksDW-mintaadatok. Ha nem rendelkezik adatraktárral, sajátítsa el, miként [hozhat létre egyet](SQL Data Warehouse létrehozása) . Ha rendelkezik adatraktárral, de nem töltötte fel a mintaadatokkal, [manuálisan is feltöltheti azokat](Mintaadatok betöltése az SQL Data Warehouse adatbázisba) .

   + **Azure Data Factory**: A tényleges betöltést az Azure Data Factory végzi el, ezért szüksége lesz egyre az adatáthelyezési folyamat elvégzéséhez. Ha még nem rendelkezik fiókkal, a [Azure Data Factory (Data Factory Editor) – első lépések][] 1. lépése leírja, hogyan hozhat létre egyet.

   + **AZCopy**: Az AZCopy a mintaadatok másolásához szükséges a helyi ügyfélről az Azure Storage-blobba. Telepítési útmutatásért lásd az [AZCopy dokumentációját][].

## <a name="step-1:-copy-sample-data-to-azure-storage-blob"></a>1. lépés: Mintaadatok másolása az Azure Storage-blobba

Ha a fentiek mind rendelkezésre állnak, készen áll a mintaadatok másolására az Azure Storage-blobba.

1. [Töltse le a mintaadatokat][]. Ezek az adatok további három év értékesítési adataival bővítik az AdventureWorksDW-mintaadatait.

2. Töltse be három év adatait az Azure Storage-blobba az AZCopy parancs használatával.

    ````
    AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
    ````


## <a name="step-2:-connect-resources-to-azure-data-factory"></a>2. lépés: Erőforrások csatlakoztatása az Azure Data Factoryhoz

Most, hogy az adatok a helyükön vannak, létrehozható az Azure Data Factory-folyamat az adatok az Azure Blob Storage-ból az SQL Data Warehouse-ba való áthelyezésére.

Első lépésként nyissa meg az [Azure Portal][], és válassza ki saját data factoryját a bal oldali menüből.

### <a name="step-2.1:-create-linked-service"></a>2.1. lépés: Társított szolgáltatás létrehozása

Társítsa Azure Storage-fiókját és az SQL Data Warehouse-t saját data factoryjához.  

1. A regisztrációs folyamat elindításához először kattintson a data factory „Összekapcsolt szolgáltatások” szakaszára, majd az „Új adattároló” lehetőségre. Válasszon egy nevet, amely alatt az Azure tárterületet regisztrálni szeretné, típusként válassza az Azure Storage lehetőséget, majd adja meg a fióknevet és a fiókkulcsot.

2. Az SQL Data Warehouse regisztrálásához lépjen a „Fejlesztés és üzembe helyezés” szakaszra, és válassza az „Új adattároló”, majd az „Azure SQL Data Warehouse” lehetőséget. Másolja és illessze be ezt a sablont, majd adja meg a kért adatokat.

    ```JSON
    {
        "name": "<Linked Service Name>",
        "properties": {
            "description": "",
            "type": "AzureSqlDW",
            "typeProperties": {
                 "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
             }
        }
    }
    ```

### <a name="step-2.2:-define-the-dataset"></a>2.2. lépés: Az adatkészlet meghatározása

Az összekapcsolt szolgáltatások létrehozását követően meg kell határoznunk az adatkészleteket.  Itt ez a tárolóból az adatbázisba mozgatott adatok struktúrájának meghatározását jelenti.  További információk a létrehozással kapcsolatban

1. A folyamat indításához lépjen a data factory „Fejlesztés és üzembe helyezés” szakaszára.

2. Kattintson az „Új adathalmaz”, majd az „Azure Blob Storage” lehetőségre a tároló a data factoryhoz kapcsolásához.  Az alábbi parancsfájl használatával határozhatja meg az adatokat az Azure Blob Storage-ban:

    ```JSON
    {
        "name": "<Dataset Name>",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "<linked storage name>",
            "typeProperties": {
                "folderPath": "<containter name>",
                "fileName": "FactInternetSales.csv",
                "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
                }
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }
    ```

3. Most meghatározzuk az adatkészletet az SQL Data Warehouse-hoz. A folyamat elindításához ismét kattintson az „Új adathalmaz” majd az „Azure SQL Data Warehouse” lehetőségre.

    ```JSON
    {
        "name": "DWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": "AzureSqlDWLinkedService",
            "typeProperties": {
                "tableName": "FactInternetSales"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

## <a name="step-3:-create-and-run-your-pipeline"></a>3. lépés: A folyamat létrehozása és futtatása

Végül létrehozzuk és futtatjuk a folyamatot az Azure Data Factoryban.  Ez a művelet végzi el az adatok tényleges áthelyezését.  Az SQL Data Warehouse-ban és az Azure Data Factoryben végrehajtható műveletek teljes listáját [itt] találja[Move data to and from Azure SQL Data Warehouse using Azure Data Factory] (Adatok áthelyezése az Azure SQL Data Warehouse-ba és onnan máshová az Azure Data Factory használatával).

A „Fejlesztés és üzembe helyezés” szakaszban kattintson a „Több parancs”, majd az „Új adatcsatorna” elemre.  Miután létrehozta a folyamatot, az alábbi kód használatával helyezheti át az adatokat az adatraktárba:

```JSON
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "skipHeaderLineCount": 1
                },
                "sink": {
                    "type": "SqlDWSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:10"
                }
            },
            "inputs": [
              {
                "name": "<Storage Dataset>"
              }
            ],
            "outputs": [
              {
                "name": "<Data Warehouse Dataset>"
              }
            ],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Sample Copy",
            "description": "Copy Activity"
          }
        ],
        "start": "<Date YYYY-MM-DD>",
        "end": "<Date YYYY-MM-DD>",
        "isPaused": false
    }
}
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne tudni, kezdje a következők áttekintésével:

- [Azure Data Factory képzési terv][].
- [Azure SQL Data Warehouse-összekötő][]. Ez a fő referencia-témakör az Azure Data Factory és az Azure SQL Data Warehouse együttes használatáról.


Ezek a témakörök további információkat adnak az Azure Data Factory szolgáltatással kapcsolatban. Ugyan az Azure SQL Database és a HDInsight a témájuk, a bennük foglalt információk azonban az Azure SQL Data Warehouse-ra is vonatkoznak.

- [Oktatóanyag: Azure Data Factory – első lépések][] Ez a fő oktatóanyag az adatok Azure Data Factoryval történő feldolgozásáról. Ebben az oktatóanyagban felépítheti első folyamatát, amely a HDInsight használatával webes naplókat alakít át és elemez havi rendszerességgel. Megjegyzés: Ebben az oktatóanyagban nincs másolási tevékenység.
- [Oktatóanyag: Adatok másolása az Azure Storage-blobból az Azure SQL Database-be][]. Ebben az oktatóanyagban létrehozhat egy folyamatot az Azure Data Factoryben az adatok Azure Storage-blobból az Azure SQL Database-be való áthelyezéséhez.

<!--Image references-->

<!--Article references-->
[Az AZCopy dokumentációja]: ../storage/storage-use-azcopy.md
[Azure SQL Data Warehouse-összekötő]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[SQL Data Warehouse létrehozása]: sql-data-warehouse-get-started-provision.md
[Tárfiók létrehozása]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Azure Data Factory (Data Factory Editor) – első lépések]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Az Azure Data Factory bemutatása]: ../data-factory/data-factory-introduction.md
[Mintaadatok betöltése az SQL Data Warehouse-ba]: sql-data-warehouse-load-sample-databases.md
[Adatok áthelyezése az Azure SQL Data Warehouse-ba és onnan máshová az Azure Data Factory használatával]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[Oktatóanyag: Adatok másolása az Azure Storage-blobból az Azure SQL Database-be]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[Oktatóanyag: Azure Data Factory – első lépések]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory képzési terv]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Azure Portal]: https://portal.azure.com
[Mintaadatok letöltése]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv



<!--HONumber=Oct16_HO3-->


