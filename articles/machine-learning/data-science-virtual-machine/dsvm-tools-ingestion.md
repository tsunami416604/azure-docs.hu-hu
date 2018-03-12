---
title: "Adatok tudományos virtuális gép adatok adatfeldolgozást eszközök - Azure |} Microsoft Docs"
description: "Adatok tudományos virtuális gép adatok adatfeldolgozást eszközök"
keywords: "adatok tudományos eszközök, adatok tudományos virtuális gép, adattudomány, linux adattudomány eszközei"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 8526e949aee2935824a03a0972d9e45c71d6601b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Adatok tudományos virtuális gép adatok adatfeldolgozást eszközök

Az első műszaki lépéseket adattudomány vagy AI projektben egyik azonosításához használható, és azokat analytics környezetébe adathalmazokat. Az adatok tudományos virtuális gép (DSVM) eszközöket és a szalagtárak átveendő adatokat különböző forrásokból történő helyileg a DSVM vagy egy adatplatform analitikai adatok tárolási a felhőbeli vagy helyszíni biztosít. 

Az alábbiakban néhány adtunk meg a DSVM adatok mozgása eszközök. 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Mi ez?   | Adatok másolása az Azure storage blobs szolgáltatásban az Azure Data Lake Store eszköz. Az adatok között két Azure Data Lake Store-fiókot is másolhatja.      |
| Támogatott DSVM verziók      | Windows      |
| A gyakori felhasználási      | Több BLOB az Azure storage importálása az Azure Data Lake Store.      |
|  Hogyan használja az / futtatni?    |   Nyisson meg egy parancssort, majd írja be a `adlcopy` Ha segítséget szeretne kérni.    |
| Minták mutató hivatkozások      | [Az AdlCopy használata](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| A DSVM a kapcsolódó eszközök      | AzCopy, az Azure parancssori     |

## <a name="azure-command-line"></a>Az Azure parancssori

|    |           |
| ------------- | ------------- |
| Mi ez?   | Az Azure-felügyeleti eszköz. Parancs műveletek tárolt adatok mozgatása az Azure data platformokon, például az Azure storage blobs, Azure Data Lake tárolási is tartalmaz     |
| Támogatott DSVM verziók      | Windows, Linux     |
| A gyakori felhasználási      | Importál, és az Azure storage, Azure Data Lake Store az adatok exportálása      |
|  Hogyan használja az / futtatni?    |   Nyisson meg egy parancssort, majd írja be a `az` Ha segítséget szeretne kérni.    |
| Minták mutató hivatkozások      | [Az Azure parancssori felület használata](https://docs.microsoft.com/cli/azure)     |
| A DSVM a kapcsolódó eszközök      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Mi ez?   | Adatok másolása helyi fájlok az Azure storage blobs, fájlok és táblák érkező vagy oda irányuló eszköz.      |
| Támogatott DSVM verziók      | Windows      |
| A gyakori felhasználási      | Fájlok másolása a blob storage, fiókokba BLOB másolása.      |
|  Hogyan használja az / futtatni?    |   Nyisson meg egy parancssort, majd írja be a `azcopy` Ha segítséget szeretne kérni.    |
| Minták mutató hivatkozások      | [AzCopy Windowson](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| A DSVM a kapcsolódó eszközök      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB adatáttelepítési eszköz

|    |           |
| ------------- | ------------- |
| Mi ez?   | Adatok importálása a különböző forrásokból, beleértve a JSON-fájlokat, CSV-fájlok, SQL, MongoDB, Azure Table storage, Amazon DynamoDB és Azure Cosmos DB SQL API gyűjtemények be Azure Cosmos DB eszköz.      |
| Támogatott DSVM verziók      | Windows      |
| A gyakori felhasználási      | Fájlok importálása a virtuális gép CosmosDB, az adatok importálása az Azure table storage CosmosDB vagy adatok importálását az SQL Server-adatbázis CosmosDB.     |
|  Hogyan használja az / futtatni?    |   A parancssor verzió, nyissa meg egy parancssori ablakot, majd írja be a következőt `dt`. A grafikus felhasználói Felülettel eszközzel, nyisson meg egy parancssort, majd írja be a `dtui`.    |
| Minták mutató hivatkozások      | [Adatok CosmosDB importálása](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| A DSVM a kapcsolódó eszközök      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Mi ez?   | SQL Server eszköz adatfájlt között az SQL Server-adatok másolása.      |
| Támogatott DSVM verziók      | Windows      |
| A gyakori felhasználási      | Importálás CSV-fájl egy SQL Server táblába, SQL Server tábla exportálása fájlba.      |
|  Hogyan használja az / futtatni?    |   Nyisson meg egy parancssort, majd írja be a `bcp` Ha segítséget szeretne kérni.    |
| Minták mutató hivatkozások      | [A tömeges másolási segédprogram](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| A DSVM a kapcsolódó eszközök      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Mi ez?   | Eszköz csatlakoztatása az Azure blob-tároló Linux fájlrendszer.      |
| Támogatott DSVM verziók      | Linux      |
| A gyakori felhasználási      | Olvasását és írását a tárolóban lévő blobok      |
|  Hogyan használja az / futtatni?    |   Futtatás _blobfuse_ a terminálon.    |
| Minták mutató hivatkozások      | [a Githubon blobfuse](https://github.com/Azure/azure-storage-fuse)      |
| A DSVM a kapcsolódó eszközök      | Az Azure parancssori      |


## <a name="microsoft-data-management-gateway"></a>A Microsoft adatkezelési átjáró

|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy eszköz csatlakozni a helyszíni adatforrások a felhőalapú szolgáltatások felhasználásra.      |
| Támogatott DSVM verziók      | Windows      |
| A gyakori felhasználási      | A virtuális gép csatlakozik egy helyszíni adatforráshoz.      |
|  Hogyan használja az / futtatni?    |   Indítsa el a Start menü "Microsoft adatkezelési átjáró".    |
| Minták mutató hivatkozások      | [Adatkezelési átjáró](https://msdn.microsoft.com/library/dn879362.aspx)      |
| A DSVM a kapcsolódó eszközök      | AzCopy, AdlCopy, bcp    |
