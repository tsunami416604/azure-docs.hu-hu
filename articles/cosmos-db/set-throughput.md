---
title: "Az Azure Cosmos DB rendelkezés átviteli |} Microsoft Docs"
description: "Tudnivalók az Azure Cosmos DB containsers, gyűjtemények, diagramokat és táblák kiosztott átviteli sebesség."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: mimig
ms.openlocfilehash: d541bb19ba7e5ecb44c9fe91b1e232d4d9c2170e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="set-throughput-for-azure-cosmos-db-containers"></a>Az Azure Cosmos DB tárolókat átviteli beállítása

Átviteli sebesség az az Azure Cosmos DB tárolókat az Azure portálon vagy az ügyfél SDK-k segítségével állíthatja be. 

A következő táblázat felsorolja a rendelkezésre álló tárolók az átviteli sebesség:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Az egypartíciós tároló</strong></p></td>
            <td valign="top"><p><strong>Particionált tároló</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Minimális átviteli sebesség</p></td>
            <td valign="top"><p>400 kérelem egység / másodperc</p></td>
            <td valign="top"><p>2500 kérést egység / másodperc</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Maximális átviteli sebesség</p></td>
            <td valign="top"><p>10 000 kérelemegység / másodperc</p></td>
            <td valign="top"><p>Korlátlan</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Az átviteli sebesség beállítása az Azure-portál használatával

1. Egy új ablakban nyissa meg a [Azure-portálon](https://portal.azure.com).
2. A bal oldali sávon kattintson **Azure Cosmos DB**, vagy kattintson a **több szolgáltatások** alsó, majd görgessen a **adatbázisok**, és kattintson a **Azure Cosmos DB**.
3. Válassza ki a Cosmos DB fiókját.
4. Kattintson az új ablakban **adatok kezelővel (előzetes verzió)** a navigációs menü.
5. Az új ablakban bontsa ki az adatbázis és a tároló majd **skála & beállítások**.
6. Az új ablakban írja be az új átviteli értéket a **átviteli** gombra, majd **mentése**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-documentdb-api-for-net"></a>Az átviteli sebesség beállítása a .NET-hez a DocumentDB API használatával

```C#
//Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

//Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

## <a name="throughput-faq"></a>Átviteli – gyakori kérdések

**I állíthatja az átviteli sebesség kisebb, mint 400 RU/mp?**

400 RU/mp a minimális átviteli sebesség érhető el a Cosmos DB az egypartíciós gyűjtemények (2500 RU/mp a particionált gyűjtemények minimális). Kérelem egységek 100 RU/mp intervallumok belül vannak beállítva, de az átviteli sebesség nem állítható be 100 RU/mp vagy bármely érték kisebb, mint a 400 RU/mp. Ha fejlesztéséhez és teszteléséhez Cosmos DB költséghatékony módszert keres, akkor használhatja az ingyenes [Azure Cosmos DB emulátor](local-emulator.md), amely központilag telepíthető helyileg ingyenesen. 

**Hogyan állíthatom be a MongoDB API-jával througput?**

Nincs átviteli sebesség beállításához MongoDB API kiterjesztés nélkül. A javaslat, hogy a DocumentDB API-t használó, ahogy az [az átviteli sebesség beállítása a .NET-hez a DocumentDB API használatával](#set-throughput-sdk).

## <a name="next-steps"></a>Következő lépések

Üzembe helyezési és folyamatos bolygónk-méretezéssel Cosmos DB, kapcsolatos további információkért lásd: [particionálás és méretezést Cosmos DB,](partition-data.md).
