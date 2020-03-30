---
title: Adatfeldolgozási eszközök
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg az adatelemzési virtuális gépen előtelepített adatbetöltési eszközöket és segédprogramokat.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d86858f8d7f09628457b718ca3c481934d720081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270055"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Adatelemzési virtuálisgép-adatbetöltési eszközök

Az adatelemzési vagy AI-projekt első technikai lépései közé tartozik, hogy azonosítania kell a használandó adatkészleteket, és be kell vonnia őket az elemzési környezetbe. Az adatelemzési virtuális gép (DSVM) eszközöket és kódtárakat biztosít a különböző forrásokból származó adatok nak a DSVM-en helyileg, vagy egy adatplatformba történő helyi elemzési adattárolásba való behozására a felhőben vagy a helyszínen.

Íme néhány adatmozgatási eszköz, amely elérhető a DSVM-ben.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Mi ez?   | Az Azure Blob storage-ból az Azure Data Lake Store-ba történő adatok másolására szolgáló eszköz. Két Azure Data Lake Store-fiók között is másolhat adatokat.      |
| Támogatott DSVM-verziók      | Windows      |
| Tipikus felhasználások      | Több blob importálása az Azure Blob storage-ból az Azure Data Lake Store-ba.      |
|  Hogyan kell használni / futtatni?    |   Nyisson meg egy `adlcopy` parancssort, és írja be a súgót.    |
| A mintákra mutató hivatkozások      | [Az AdlCopy használata](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Kapcsolódó eszközök a DSVM      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

|    |           |
| ------------- | ------------- |
| Mi ez?   | Az Azure felügyeleti eszköze. Parancsműveleteket is tartalmaz az Azure-adatplatformokról, például az Azure Blob storage-ból és az Azure Data Lake Store-ból történő átmozgatáshoz.     |
| Támogatott DSVM-verziók      | Windows, Linux     |
| Tipikus felhasználások      | Adatok importálása és exportálása az Azure Storage és az Azure Data Lake Store áruházba és onnan.      |
|  Hogyan kell használni / futtatni?    |   Nyisson meg egy `az` parancssort, és írja be a súgót.    |
| A mintákra mutató hivatkozások      | [Az Azure parancssori felület használata](https://docs.microsoft.com/cli/azure)     |
| Kapcsolódó eszközök a DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Mi ez?   | Helyi fájlokba, Azure Blob-tárolóba, fájlokba és táblákba másolhat adatokat és fájlokat.      |
| Támogatott DSVM-verziók      | Windows      |
| Tipikus felhasználások      | Fájlok másolása az Azure Blob storage és blobok másolása fiókok között.      |
|  Hogyan kell használni / futtatni?    |   Nyisson meg egy `azcopy` parancssort, és írja be a súgót.    |
| A mintákra mutató hivatkozások      | [AzCopy Windowson](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Kapcsolódó eszközök a DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Az Azure Cosmos DB adatáttelepítési eszköze

|    |           |
| ------------- | ------------- |
| Mi ez?   | Különböző forrásokból származó adatok importálására szolgál az Azure Cosmos DB-be, amely egy NoSQL-adatbázis a felhőben. Ezek a források közé tartoznak a JSON-fájlok, a CSV-fájlok, az SQL, a MongoDB, az Azure Table storage, az Amazon DynamoDB és az Azure Cosmos DB SQL API-gyűjtemények.      |
| Támogatott DSVM-verziók      | Windows      |
| Tipikus felhasználások      | Fájlok importálása a virtuális gépről a CosmosDB-be, adatok importálása az Azure table storage-ból a CosmosDB-be, és adatok importálása egy Microsoft SQL Server adatbázisból a CosmosDB-be.     |
|  Hogyan kell használni / futtatni?    |   A parancssori verzió használatához nyisson meg `dt`egy parancssort, és írja be a parancsot. A GRAFIKUS ESZKÖZ használatához nyisson meg `dtui`egy parancssort, és írja be a parancsot.    |
| A mintákra mutató hivatkozások      | [CosmosDB Adatok importálása](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Kapcsolódó eszközök a DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

|    |           |
| ------------- | ------------- |
| Mi ez?   | Grafikus felhasználói felület az Azure-felhőben tárolt fájlokkal való interakcióhoz. |
| Támogatott DSVM-verziók      | Windows      |
| Tipikus felhasználások      | Adatok importálása és exportálása a DSVM-ből.    |
|  Hogyan kell használni / futtatni?    | Keressen rá az "Azure Storage Explorer" kifejezésre a Start menüben. |
| A mintákra mutató hivatkozások      | [Azure Storage Explorer](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Mi ez?   | SQL Server eszköz az SQL Server és egy adatfájl közötti másoláshoz.      |
| Támogatott DSVM-verziók      | Windows      |
| Tipikus felhasználások      | CSV-fájl importálása SQL Server táblába, és SQL Server tábla exportálása fájlba.      |
|  Hogyan kell használni / futtatni?    |   Nyisson meg egy `bcp` parancssort, és írja be a súgót.    |
| A mintákra mutató hivatkozások      | [bcp segédprogram](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Kapcsolódó eszközök a DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy Eszköz egy Azure Blob storage-tároló csatlakoztatására a Linux fájlrendszerben.      |
| Támogatott DSVM-verziók      | Linux      |
| Tipikus felhasználások      | Olvasás és írás blobok egy tárolóban.      |
|  Hogyan kell használni és futtatni?    |   _Blobfuse futtatása_ egy terminálon.    |
| A mintákra mutató hivatkozások      | [blobfuse a GitHubon](https://github.com/Azure/azure-storage-fuse)      |
| Kapcsolódó eszközök a DSVM      | Azure CLI      |
