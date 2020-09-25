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
ms.openlocfilehash: abd88c3cdb54747c21ad8772860a3ed6929dbd15
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318721"
---
# <a name="azure-table-storage-overview"></a>Az Azure Table Storage áttekintése

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Az Azure Table Storage szolgáltatás strukturált NoSQL-adatokat tárol a felhőben, így séma nélküli kulcs-/attribútumtárat biztosítva. Mivel a Table Storage séma nélküli, az adatokat könnyen az alkalmazás változó igényeihez igazíthatja. A Table Storage adataihoz számos alkalmazástípus gyorsan és költséghatékonyan férhet hozzá, a költségei pedig jellemzően alacsonyabbak, mint a hagyományos SQL hasonló mennyiségű adathoz való használata esetében.

A Table Storage segítségével olyan rugalmas adatkészleteket tárolhat, mint például webalkalmazások felhasználói adatai, címtárak, eszközadatok és bármilyen egyéb metaadat, amelyre a szolgáltatásnak szüksége van. Egy táblán korlátlan számú entitást tárolhat, és egy tárfiók a kapacitásán belül korlátlan számú táblát tartalmazhat.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Következő lépések

* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

* [Bevezetés az Azure Cosmos DB Table API és az Azure Table Storage a .NET SDK-val való használatába](table-storage-how-to-use-dotnet.md)

* A Table Service elérhető API-kat részletesen ismertető referenciadokumentációjának megtekintése:

    * [A Storage ügyféloldali kódtára a .NET-hez – referencia](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [REST API-referencia](https://msdn.microsoft.com/library/azure/dd179355)
