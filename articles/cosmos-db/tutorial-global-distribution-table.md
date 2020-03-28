---
title: Az Azure Cosmos DB globális disztribúciós oktatóanyaga a Table API-hoz
description: Ismerje meg, hogyan működik a globális disztribúció az Azure Cosmos DB Table API-fiókokban, és hogyan konfigurálható a régiók előnyben részesített listája
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: 627086bdb13acdd29821af399f90fee8deaae432
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76900181"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Az Azure Cosmos DB globális terjesztésének beállítása a Table API-val

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés konfigurálása a [Table API](table-introduction.md) használatával

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Csatlakozás egy kívánt régióhoz a Table API használatával

A [globális terjesztés](distribute-data-globally.md)előnyeinek kihasználása érdekében az ügyfélalkalmazásoknak meg kell adniuk azt a helyet, ahol az alkalmazás fut. Ez a `CosmosExecutorConfiguration.CurrentRegion` tulajdonság beállításával történik. A `CurrentRegion` tulajdonságnak egyetlen helyet kell tartalmaznia. Minden ügyfélpéldány megadhatja a saját régió alacsony késésű olvasás. A régiót [a megjelenítendő nevükkel](https://msdn.microsoft.com/library/azure/gg441293.aspx) kell elnevezni, például "USA nyugati régiója". 

Az Azure Cosmos DB Table API SDK automatikusan kiválasztja a legjobb végpontot a fiók konfigurációja és az aktuális regionális rendelkezésre állás alapján. Prioritásként kezeli a legközelebbi régiót, hogy jobb késést biztosítson az ügyfeleknek. Az aktuális `CurrentRegion` tulajdonság beállítása után az olvasási és írási kérelmek a következőképpen módosulnak:

* **Olvasási kérelmek:** A rendszer minden olvasási `CurrentRegion`kérelmet elküld a konfigurált nak. A közelség alapján az SDK automatikusan kiválaszt egy tartalék georeplikált régiót a magas rendelkezésre állás érdekében.

* **Írási kérelmek:** Az SDK automatikusan elküldi az összes írási kérelmet az aktuális írási régióba. Többfőkiszolgálós fiókban az aktuális régió az írási kérelmeket is szolgálja. A közelség alapján az SDK automatikusan kiválaszt egy tartalék georeplikált régiót a magas rendelkezésre állás érdekében.

Ha nem adja meg `CurrentRegion` a tulajdonságot, az SDK az aktuális írási régiót használja az összes művelethez.

Ha például egy Azure Cosmos-fiók "USA nyugati régióiban" és "USA keleti régióiban" található. Ha az "USA nyugati régiója" az írási régió, és az alkalmazás az "USA keleti régiójában" található. Ha a CurrentRegion tulajdonság nincs konfigurálva, az összes olvasási és írási kérelem mindig az "USA nyugati régió" régióba lesz irányítva. Ha a CurrentRegion tulajdonság konfigurálva van, az összes olvasási kérelem az "USA keleti régióból" kerül kiszolgálásra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés konfigurálása az Azure Cosmos DB Table API-jaival

