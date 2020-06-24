---
title: Adatfeldolgozási eszközök
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg a Data Science Virtual Machine előtelepített adatfeldolgozási eszközöket és segédprogramokat.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d86858f8d7f09628457b718ca3c481934d720081
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84708008"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Adatfeldolgozási eszközök Data Science Virtual Machine

Az adatelemzési vagy AI-projekt első technikai lépéseként meg kell határoznia a használni kívánt adatkészleteket, és azokat be kell állítani az elemzési környezetbe. A Data Science Virtual Machine (DSVM) eszközöket és könyvtárakat biztosít a különböző forrásokból származó adatoknak az analitikus adattárolóba helyileg történő, vagy a felhőben vagy a helyszínen lévő adatplatformba való DSVM.

Íme néhány, a DSVM elérhető adatáthelyezési eszköz.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Mi ez?   | Eszköz az adatok Azure Blob Storage-ból Azure Data Lake Storeba való másolásához. Az Adatmásolás két Azure Data Lake Store fiók között is elvégezhető.      |
| Támogatott DSVM-verziók      | Windows      |
| Jellemző felhasználások      | Több blob importálása az Azure Blob Storage-ból Azure Data Lake Storeba.      |
|  Hogyan használhatja/futtatja?    |   Nyisson meg egy parancssort, és írja be `adlcopy` , hogy segítséget kapjon.    |
| Mintákra mutató hivatkozások      | [Az AdlCopy használata](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| A DSVM kapcsolódó eszközök      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy felügyeleti eszköz az Azure-hoz. Emellett parancssori műveleteket is tartalmaz az Azure-beli adatplatformok, például az Azure Blob Storage és a Azure Data Lake Store adatainak áthelyezéséhez.     |
| Támogatott DSVM-verziók      | Windows, Linux     |
| Jellemző felhasználások      | Adatok importálása és exportálása az Azure Storage-ba és a Azure Data Lake Storeba.      |
|  Hogyan használhatja/futtatja?    |   Nyisson meg egy parancssort, és írja be `az` , hogy segítséget kapjon.    |
| Mintákra mutató hivatkozások      | [Az Azure parancssori felület használata](https://docs.microsoft.com/cli/azure)     |
| A DSVM kapcsolódó eszközök      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Mi ez?   | Eszköz helyi fájlokba, Azure Blob Storage-ba, fájlokba és táblákba másolt adatok másolására.      |
| Támogatott DSVM-verziók      | Windows      |
| Jellemző felhasználások      | Fájlok másolása az Azure Blob Storage-ba és a Blobok másolása a fiókok között.      |
|  Hogyan használhatja/futtatja?    |   Nyisson meg egy parancssort, és írja be `azcopy` , hogy segítséget kapjon.    |
| Mintákra mutató hivatkozások      | [AzCopy Windowson](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| A DSVM kapcsolódó eszközök      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Adatáttelepítési eszköz Azure Cosmos DB

|    |           |
| ------------- | ------------- |
| Mi ez?   | Eszköz különböző forrásokból származó adatok importálásához a felhőben lévő NoSQL-adatbázisba Azure Cosmos DBba. Ezek a források közé tartoznak a JSON-fájlok, a CSV-fájlok, az SQL, a MongoDB, az Azure Table Storage, az Amazon DynamoDB és a Azure Cosmos DB SQL API-gyűjtemények.      |
| Támogatott DSVM-verziók      | Windows      |
| Jellemző felhasználások      | Fájlok importálása egy virtuális gépről a CosmosDB, adatok importálása az Azure Table Storage-ból a CosmosDB, és adatok importálása egy Microsoft SQL Server adatbázisból a CosmosDB-be.     |
|  Hogyan használhatja/futtatja?    |   A parancssori verzió használatához nyisson meg egy parancssort, és írja be a következőt: `dt` . A grafikus felhasználói felület eszköz használatához nyisson meg egy parancssort, és írja be a következőt: `dtui` .    |
| Mintákra mutató hivatkozások      | [CosmosDB importálása](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| A DSVM kapcsolódó eszközök      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

|    |           |
| ------------- | ------------- |
| Mi ez?   | Grafikus felhasználói felület az Azure-felhőben tárolt fájlokkal való interakcióhoz. |
| Támogatott DSVM-verziók      | Windows      |
| Jellemző felhasználások      | Adatok importálása és exportálása a DSVM.    |
|  Hogyan használhatja/futtatja?    | A Start menüben keressen rá a "Azure Storage Explorer" kifejezésre. |
| Mintákra mutató hivatkozások      | [Azure Storage Explorer](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Mi ez?   | SQL Server eszköz az Adatmásolás SQL Server és az adatfájl között.      |
| Támogatott DSVM-verziók      | Windows      |
| Jellemző felhasználások      | CSV-fájl importálása SQL Server táblába, és SQL Server táblázat exportálása fájlba.      |
|  Hogyan használhatja/futtatja?    |   Nyisson meg egy parancssort, és írja be `bcp` , hogy segítséget kapjon.    |
| Mintákra mutató hivatkozások      | [BCP segédprogram](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| A DSVM kapcsolódó eszközök      | SQL Server, Sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy eszköz, amely egy Azure Blob Storage-tárolót csatlakoztat a Linux-fájlrendszerben.      |
| Támogatott DSVM-verziók      | Linux      |
| Jellemző felhasználások      | Egy tárolóban lévő Blobok olvasása és írása.      |
|  Hogyan kell használni és futtatni?    |   Futtassa a _blobfuse_ -t egy terminálon.    |
| Mintákra mutató hivatkozások      | [blobfuse a GitHubon](https://github.com/Azure/azure-storage-fuse)      |
| A DSVM kapcsolódó eszközök      | Azure CLI      |
