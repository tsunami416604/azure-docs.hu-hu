---
title: A Table API Azure Cosmos DB globális terjesztési oktatóanyaga
description: Ismerje meg, hogyan működik a globális terjesztés a Azure Cosmos DB Table API-fiókokban, és hogyan konfigurálhatja a régiók előnyben részesített listáját
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: 627086bdb13acdd29821af399f90fee8deaae432
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "76900181"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Az Azure Cosmos DB globális terjesztésének beállítása a Table API-val

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés konfigurálása a [Table API](table-introduction.md) használatával

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Csatlakozás egy kívánt régióhoz a Table API használatával

A [globális terjesztés](distribute-data-globally.md)kihasználása érdekében az ügyfélalkalmazások számára meg kell határozni az aktuális helyet, ahol az alkalmazások futnak. Ezt a tulajdonság beállításával teheti meg `CosmosExecutorConfiguration.CurrentRegion` . A `CurrentRegion` tulajdonságnak egyetlen helyet kell tartalmaznia. Minden egyes ügyfél megadhatja saját régióját az alacsony késésű olvasásokhoz. A régió nevét a [megjelenítendő nevükkel](https://msdn.microsoft.com/library/azure/gg441293.aspx) , például a "West us" névvel kell elnevezni. 

A Azure Cosmos DB Table API SDK automatikusan kiválasztja a legjobb végpontot, hogy a fiók konfigurációja és a jelenlegi regionális rendelkezésre állás alapján kommunikáljon. Rangsorolja a legközelebbi régiót, hogy jobb késést biztosítson az ügyfeleknek. Az aktuális tulajdonság beállítása után az `CurrentRegion` olvasási és írási kérelmek a következőképpen lesznek átirányítva:

* **Olvasási kérelmek:** A rendszer az összes olvasási kérelmet elküldi a konfiguráltnak `CurrentRegion` . A közelség alapján az SDK automatikusan kiválasztja a tartalék földrajzilag replikált régiót a magas rendelkezésre állás érdekében.

* **Írási kérelmek:** Az SDK automatikusan elküldi az összes írási kérelmet az aktuális írási régióba. Egy több főkiszolgálós fiókban az aktuális régió is az írási kérelmeket fogja szolgálni. A közelség alapján az SDK automatikusan kiválasztja a tartalék földrajzilag replikált régiót a magas rendelkezésre állás érdekében.

Ha nem határozza meg a `CurrentRegion` tulajdonságot, az SDK az aktuális írási régiót használja az összes művelethez.

Ha például egy Azure Cosmos-fiók "nyugati USA" és "keleti USA" régiókban található. Ha a "West US" az írási régió, és az alkalmazás szerepel az "USA keleti régiójában". Ha a CurrentRegion tulajdonság nincs konfigurálva, az összes olvasási és írási kérelem mindig az "USA nyugati régiója" régiójába lesz irányítva. Ha a CurrentRegion tulajdonság konfigurálva van, az összes olvasási kérelem az "East US" régióból lesz kézbesítve.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés konfigurálása az Azure Cosmos DB Table API-jaival

