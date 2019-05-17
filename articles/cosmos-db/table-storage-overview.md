---
title: Az Azure Table storage áttekintése
description: Az Azure Table Storage, amely egy NoSQL-adattár, a strukturált adatok felhőben való tárolásához használható.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: overview
ms.date: 11/03/2017
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 0c15a7bc3295a26b004a32a8a464b41ac8c0a4b4
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787235"
---
# <a name="azure-table-storage-overview"></a>Az Azure Table Storage áttekintése

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Az Azure Table Storage szolgáltatás strukturált NoSQL-adatokat tárol a felhőben, így séma nélküli kulcs-/attribútumtárat biztosítva. Mivel a Table Storage séma nélküli, az adatokat könnyen az alkalmazás változó igényeihez igazíthatja. A Table Storage adataihoz számos alkalmazástípus gyorsan és költséghatékonyan férhet hozzá, a költségei pedig jellemzően alacsonyabbak, mint a hagyományos SQL hasonló mennyiségű adathoz való használata esetében.

A Table Storage segítségével olyan rugalmas adatkészleteket tárolhat, mint például webalkalmazások felhasználói adatai, címtárak, eszközadatok és bármilyen egyéb metaadat, amelyre a szolgáltatásnak szüksége van. Egy táblán korlátlan számú entitást tárolhat, és egy tárfiók a kapacitásán belül korlátlan számú táblát tartalmazhat.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>További lépések

* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

* [A .NET SDK használatával az Azure Cosmos DB Table API és az Azure Table storage használatának első lépései](table-storage-how-to-use-dotnet.md)

* A Table Service elérhető API-kat részletesen ismertető referenciadokumentációjának megtekintése:

    * [Az Azure Storage .NET-hez készült ügyféloldali kódtára – referencia](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [REST API – referencia](https://msdn.microsoft.com/library/azure/dd179355)
