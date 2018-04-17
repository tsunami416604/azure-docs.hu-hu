---
title: Azure Table Storage-támogatás az Azure Cosmos Adatbázisba |} Microsoft Docs
description: Ismerje meg, hogy Azure Cosmos DB tábla API és az Azure Storage-táblákat együttműködni.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 2560c2ee34a83ce86db043e17fb41192c31de398
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Fejlesztés az Azure Cosmos DB tábla API és az Azure Table storage

Azure Cosmos DB tábla API és az Azure Table storage megosztani a tábla ugyanazon adatmodell, és teszi közzé a azonos létrehozás, törlés, frissítés és lekérdezési műveletek az SDK-k használatával. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Fejlesztés az Azure Cosmos DB tábla API az

Ilyenkor a [Azure Cosmos DB tábla API](table-introduction.md) elérhető fejlesztésére négy SDK-k rendelkezik: 
- [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget) .NET SDK-val. Ezt a szalagtárat a azonos osztályok és metódus-aláírása megegyezik a nyilvános [Windows Azure Storage szolgáltatás SDK](https://www.nuget.org/packages/WindowsAzure.Storage), de arra is van lehetősége a tábla API-jával Azure Cosmos DB fiókokhoz való kapcsolódáshoz. 
- [Python SDK](table-sdk-python.md). Az új Azure Cosmos DB Python SDK nem az egyetlen SDK-t, amely támogatja az Azure Table storage a Python. Ez az SDK csatlakoznak az Azure Table-tároló, mind az Azure Cosmos DB tábla API.
- [Java SDK](table-sdk-java.md). Az Azure Storage szolgáltatás SDK képes a Azure Cosmos DB fiókok tábla API használatával való kapcsolódáshoz.
- [NODE.js SDK](table-sdk-nodejs.md). Az Azure Storage szolgáltatás SDK képes a Azure Cosmos DB fiókok tábla API használatával való kapcsolódáshoz.

A tábla API használatával kapcsolatos további információkat a [– gyakori kérdések: a tábla API fejlesztést](faq.md#develop-with-the-table-api) cikk.

## <a name="developing-with-the-azure-table-storage"></a>Az az Azure Table storage fejlesztése

[Az Azure Table storage](table-storage-overview.md) sok SDK-k elérhető és oktatóanyagok érhető el, amelyek is elérhetők a [Azure Table storage](table-storage-overview.md) szakasz. Ezek a cikkek frissítés alatt álló együttműködés az Azure Table storage SDK-k között, és Azure Cosmos DB tábla API-k elérhetővé válik.  





