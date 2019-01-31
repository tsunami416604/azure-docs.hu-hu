---
title: Data Science virtuális gép adatfeldolgozási eszközök – Azure |} A Microsoft Docs
description: További tudnivalók az adatfeldolgozási eszközök és segédprogramok előre telepítve van a az adatelemző virtuális gép.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 824793c9897d7d728f621182a02a6092edc69e89
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452379"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Data Science virtuális gép adatfeldolgozási eszközök

Az adatelemzés vagy AI-projektben műszaki első lépéseként egyik azonosításához használható, és az elemzési környezet alá az adatkészletek. Az adatelemzési virtuális gép (DSVM) biztosít az eszközök és kódtárak ahhoz, hogy az adatokat egy analitikus adatok tárba helyileg, a dsvm-hez vagy a data platform különböző forrásokból származó a felhőben vagy helyszíni. 

Az alábbiakban néhány adat adatátviteli eszközök adtunk meg a dsvm-hez a. 

## <a name="adlcopy"></a>Az AdlCopy

|    |           |
| ------------- | ------------- |
| Mi ez?   | Olyan eszköz, az adatok másolása az Azure storage-blobokból az Azure Data Lake Store-bA. Azt is másolja az adatokat két Azure Data Lake Store-fiókok között.      |
| A DSVM támogatott verziói      | Windows      |
| Gyakori használati      | Több blobok importálása az Azure storage-ból az Azure Data Lake Store-bA.      |
|  Hogyan futtathatja / használata?    |   Nyisson meg egy parancssort, majd írja be a `adlcopy` kaphat segítséget.    |
| A minták mutató hivatkozások      | [Az AdlCopy használata](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| A dsvm-hez kapcsolódó eszközök      | Az AzCopy, az Azure parancssori felületével     |

## <a name="azure-command-line"></a>Azure parancssori felületével

|    |           |
| ------------- | ------------- |
| Mi ez?   | Az Azure felügyeleti eszköz. Adatok áthelyezése az Azure-beli adat-platformokon, például az Azure storage-blobokkal, az Azure Data Lake Storage parancs HTTP-parancsokat is tartalmaz     |
| A DSVM támogatott verziói      | Windows, Linux     |
| Gyakori használati      | Az importálás az Azure storage, Azure Data Lake Store-ból, és az adatok exportálása      |
|  Hogyan futtathatja / használata?    |   Nyisson meg egy parancssort, majd írja be a `az` kaphat segítséget.    |
| A minták mutató hivatkozások      | [Az Azure parancssori felület használata](https://docs.microsoft.com/cli/azure)     |
| A dsvm-hez kapcsolódó eszközök      | Az AzCopy, az AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Mi ez?   | Olyan eszköz, az adatok másolásához, és a helyi fájlok, az Azure storage-blobokat, fájlokat és táblákat.      |
| A DSVM támogatott verziói      | Windows      |
| Gyakori használati      | Fájlok másolása a blob storage, BLOB-fiókok közötti másolását.      |
|  Hogyan futtathatja / használata?    |   Nyisson meg egy parancssort, majd írja be a `azcopy` kaphat segítséget.    |
| A minták mutató hivatkozások      | [AzCopy Windowson](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| A dsvm-hez kapcsolódó eszközök      | Az AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Az Azure Cosmos DB adatáttelepítési eszköz

|    |           |
| ------------- | ------------- |
| Mi ez?   | Adatok importálása különböző forrásokból, beleértve a JSON-fájlokat, a CSV-fájlok, SQL, MongoDB, az Azure Table storage, az Amazon DynamoDB és Azure Cosmos DB SQL API gyűjtemények az Azure Cosmos DB-be az eszközt.      |
| A DSVM támogatott verziói      | Windows      |
| Gyakori használati      | Fájlok importálása egy virtuális Gépet, a cosmosdb-be, az adatok importálása az Azure table storage-ból a cosmos DB vagy adatok importálása az SQL Server-adatbázisból a cosmosdb-be.     |
|  Hogyan futtathatja / használata?    |   A parancssor verzió, nyissa meg egy parancssort, majd írja be a következőt `dt`. A grafikus felhasználói Felülettel eszközzel, nyisson meg egy parancssort, majd írja be a `dtui`.    |
| A minták mutató hivatkozások      | [Adatok importálása a cosmos DB](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| A dsvm-hez kapcsolódó eszközök      | Az AzCopy, az AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Mi ez?   | Az SQL Server eszköz adatokat másolhat az SQL Server és a egy adatfájlt között.      |
| A DSVM támogatott verziói      | Windows      |
| Gyakori használati      | CSV-fájl importálása SQL Server-táblát, egy SQL Server-táblát egy fájlba exportálja.      |
|  Hogyan futtathatja / használata?    |   Nyisson meg egy parancssort, majd írja be a `bcp` kaphat segítséget.    |
| A minták mutató hivatkozások      | [A tömeges másolási segédprogram](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| A dsvm-hez kapcsolódó eszközök      | SQL Server, az Sqlcmd-t      |

## <a name="blobfuse"></a>Blobfuse

|    |           |
| ------------- | ------------- |
| Mi ez?   | Eszköz csatlakoztatása az Azure blob-tároló a Linux-fájlrendszer.      |
| A DSVM támogatott verziói      | Linux      |
| Gyakori használati      | A tárolóban lévő blobokat írásakor vagy olvasásakor      |
|  Hogyan futtathatja / használata?    |   Futtatás _blobfuse_ a terminálon.    |
| A minták mutató hivatkozások      | [a Githubon blobfuse](https://github.com/Azure/azure-storage-fuse)      |
| A dsvm-hez kapcsolódó eszközök      | Azure parancssori felületével      |


## <a name="microsoft-data-management-gateway"></a>A Microsoft adatkezelési átjáró

|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy eszköz csatlakozni a helyszíni adatforrások, a felhőalapú szolgáltatások felhasználásra.      |
| A DSVM támogatott verziói      | Windows      |
| Gyakori használati      | Virtuális gép egy helyszíni adatforráshoz való kapcsolódáshoz.      |
|  Hogyan futtathatja / használata?    |   Indítsa el a "Microsoft Data Management Gateway" a Start menüből.    |
| A minták mutató hivatkozások      | [Adatkezelési átjáró](https://msdn.microsoft.com/library/dn879362.aspx)      |
| A dsvm-hez kapcsolódó eszközök      | Az AzCopy, az AdlCopy, BCP használatával    |
