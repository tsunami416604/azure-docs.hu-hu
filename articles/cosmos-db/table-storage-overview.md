---
title: Az Azure Table Storage áttekintése
description: Ismerje meg, hogyan tárolhatja az Azure Table Storage-t a rugalmas adatkészletek, például a webalkalmazások felhasználói adatai, a címjegyzékek, az eszközök adatai vagy más típusú metaadatok tárolására.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: overview
ms.date: 05/20/2019
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 42d0b2c5d39ea7b5c44b7004f65f13e07067cdc7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079512"
---
# <a name="azure-table-storage-overview"></a>Az Azure Table Storage áttekintése
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Az Azure Table Storage szolgáltatás strukturált NoSQL-adatokat tárol a felhőben, így séma nélküli kulcs-/attribútumtárat biztosítva. Mivel a Table Storage séma nélküli, az adatokat könnyen az alkalmazás változó igényeihez igazíthatja. A Table Storage adataihoz számos alkalmazástípus gyorsan és költséghatékonyan férhet hozzá, a költségei pedig jellemzően alacsonyabbak, mint a hagyományos SQL hasonló mennyiségű adathoz való használata esetében.

A Table Storage segítségével olyan rugalmas adatkészleteket tárolhat, mint például webalkalmazások felhasználói adatai, címtárak, eszközadatok és bármilyen egyéb metaadat, amelyre a szolgáltatásnak szüksége van. Egy táblán korlátlan számú entitást tárolhat, és egy tárfiók a kapacitásán belül korlátlan számú táblát tartalmazhat.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Következő lépések

* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

* [Bevezetés az Azure Cosmos DB Table API és az Azure Table Storage a .NET SDK-val való használatába](./tutorial-develop-table-dotnet.md)

* A Table Service elérhető API-kat részletesen ismertető referenciadokumentációjának megtekintése:

    * [A Storage ügyféloldali kódtára a .NET-hez – referencia](/dotnet/api/overview/azure/storage)

    * [REST API-referencia](/rest/api/storageservices/)