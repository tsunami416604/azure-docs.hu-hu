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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270055"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Data Science virtuális gép adatfeldolgozási eszközök

Az adatelemzési vagy AI-projekt első technikai lépéseként meg kell határoznia a használni kívánt adatkészleteket, és azokat be kell állítani az elemzési környezetbe. A Data Science Virtual Machine (DSVM) eszközöket és könyvtárakat biztosít a különböző forrásokból származó adatoknak az analitikus adattárolóba helyileg történő, vagy a felhőben vagy a helyszínen lévő adatplatformba való DSVM.

Íme néhány, a DSVM elérhető adatáthelyezési eszköz.

## <a name="adlcopy"></a>Az AdlCopy

|    |           |
| ------------- | ------------- |
| Mi ez?   | Eszköz az adatok Azure Blob Storage-ból Azure Data Lake Storeba való másolásához. Azt is másolja az adatokat két Azure Data Lake Store-fiókok között.      |
| Támogatott DSVM-verziók      | Windows      |
| Gyakori használati      | Több blob importálása az Azure Blob Storage-ból Azure Data Lake Storeba.      |
|  Hogyan futtathatja / használata?    |   A súgó megjelenítéséhez nyisson meg egy parancssort, és írja be `adlcopy`.    |
| A minták mutató hivatkozások      | [Az AdlCopy használata](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| A DSVM kapcsolódó eszközök      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

|    |           |
| ------------- | ------------- |
| Mi ez?   | Az Azure felügyeleti eszköz. Emellett parancssori műveleteket is tartalmaz az Azure-beli adatplatformok, például az Azure Blob Storage és a Azure Data Lake Store adatainak áthelyezéséhez.     |
| Támogatott DSVM-verziók      | Windows, Linux     |
| Gyakori használati      | Adatok importálása és exportálása az Azure Storage-ba és a Azure Data Lake Storeba.      |
|  Hogyan futtathatja / használata?    |   A súgó megjelenítéséhez nyisson meg egy parancssort, és írja be `az`.    |
| A minták mutató hivatkozások      | [Az Azure parancssori felület használata](https://docs.microsoft.com/cli/azure)     |
| A DSVM kapcsolódó eszközök      | Az AzCopy, az AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Mi ez?   | Eszköz helyi fájlokba, Azure Blob Storage-ba, fájlokba és táblákba másolt adatok másolására.      |
| Támogatott DSVM-verziók      | Windows      |
| Gyakori használati      | Fájlok másolása az Azure Blob Storage-ba és a Blobok másolása a fiókok között.      |
|  Hogyan futtathatja / használata?    |   A súgó megjelenítéséhez nyisson meg egy parancssort, és írja be `azcopy`.    |
| A minták mutató hivatkozások      | [AzCopy Windowson](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| A DSVM kapcsolódó eszközök      | Az AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Az Azure Cosmos DB adatáttelepítési eszköz

|    |           |
| ------------- | ------------- |
| Mi ez?   | Eszköz különböző forrásokból származó adatok importálásához a felhőben lévő NoSQL-adatbázisba Azure Cosmos DBba. Ezek a források közé tartoznak a JSON-fájlok, a CSV-fájlok, az SQL, a MongoDB, az Azure Table Storage, az Amazon DynamoDB és a Azure Cosmos DB SQL API-gyűjtemények.      |
| Támogatott DSVM-verziók      | Windows      |
| Gyakori használati      | Fájlok importálása egy virtuális gépről a CosmosDB, adatok importálása az Azure Table Storage-ból a CosmosDB, és adatok importálása egy Microsoft SQL Server adatbázisból a CosmosDB-be.     |
|  Hogyan futtathatja / használata?    |   A parancssori verzió használatához nyisson meg egy parancssort, és írja be a következőt: `dt`. A grafikus felhasználói felület eszköz használatához nyisson meg egy parancssort, és írja be a következőt: `dtui`.    |
| A minták mutató hivatkozások      | [CosmosDB importálása](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| A DSVM kapcsolódó eszközök      | Az AzCopy, az AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

|    |           |
| ------------- | ------------- |
| Mi ez?   | Grafikus felhasználói felület az Azure-felhőben tárolt fájlokkal való interakcióhoz. |
| Támogatott DSVM-verziók      | Windows      |
| Gyakori használati      | Adatok importálása és exportálása a DSVM.    |
|  Hogyan futtathatja / használata?    | A Start menüben keressen rá a "Azure Storage Explorer" kifejezésre. |
| A minták mutató hivatkozások      | [Azure Storage Explorer](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Mi ez?   | Az SQL Server eszköz adatokat másolhat az SQL Server és a egy adatfájlt között.      |
| Támogatott DSVM-verziók      | Windows      |
| Gyakori használati      | CSV-fájl importálása SQL Server táblába, és SQL Server táblázat exportálása fájlba.      |
|  Hogyan futtathatja / használata?    |   A súgó megjelenítéséhez nyisson meg egy parancssort, és írja be `bcp`.    |
| A minták mutató hivatkozások      | [BCP segédprogram](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| A DSVM kapcsolódó eszközök      | SQL Server, az Sqlcmd-t      |

## <a name="blobfuse"></a>Blobfuse

|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy eszköz, amely egy Azure Blob Storage-tárolót csatlakoztat a Linux-fájlrendszerben.      |
| Támogatott DSVM-verziók      | Linux      |
| Gyakori használati      | Egy tárolóban lévő Blobok olvasása és írása.      |
|  Hogyan kell használni és futtatni?    |   Futtassa a _blobfuse_ -t egy terminálon.    |
| A minták mutató hivatkozások      | [blobfuse a GitHubon](https://github.com/Azure/azure-storage-fuse)      |
| A DSVM kapcsolódó eszközök      | Azure CLI      |
