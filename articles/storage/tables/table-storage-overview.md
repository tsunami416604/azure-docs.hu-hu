---
title: A Table Storage bemutatása – Objektumtárolás az Azure-ban | Microsoft Docs
description: Az Azure Table Storage, amely egy NoSQL-adattár, a strukturált adatok felhőben való tárolásához használható.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.devlang: dotnet
ms.topic: overview
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 2670d9ce568195fd97350bf678059ac89610422f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88236165"
---
# <a name="what-is-azure-table-storage-"></a>Mi az Azure Table Storage? 

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Az Azure Table Storage szolgáltatás strukturált NoSQL-adatokat tárol a felhőben, így séma nélküli kulcs-/attribútumtárat biztosítva. Mivel a Table Storage séma nélküli, az adatokat könnyen az alkalmazás változó igényeihez igazíthatja. A Table Storage adataihoz számos alkalmazástípus gyorsan és költséghatékonyan férhet hozzá, a költségei pedig jellemzően alacsonyabbak, mint a hagyományos SQL hasonló mennyiségű adathoz való használata esetében.

A Table Storage segítségével olyan rugalmas adatkészleteket tárolhat, mint például webalkalmazások felhasználói adatai, címtárak, eszközadatok és bármilyen egyéb metaadat, amelyre a szolgáltatásnak szüksége van. Egy táblán korlátlan számú entitást tárolhat, és egy tárfiók a kapacitásán belül korlátlan számú táblát tartalmazhat.

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>További lépések

* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

* [Az Azure Table Storage használatának első lépései a .NET-ben](../../cosmos-db/table-storage-how-to-use-dotnet.md)

* A Table Service elérhető API-kat részletesen ismertető referenciadokumentációjának megtekintése:

    * [A Storage ügyféloldali kódtára a .NET-hez – referencia](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [REST API-referencia](https://msdn.microsoft.com/library/azure/dd179355)
