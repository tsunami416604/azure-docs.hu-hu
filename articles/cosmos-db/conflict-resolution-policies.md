---
title: Az Azure Cosmos DB ütközésének feloldása
description: Ez a cikk ismerteti az ütközés kategóriákat és ütközés feloldása házirendek az Azure Cosmos DB-ben.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 2f219ed6c3155e8b7d3d978116e1748f6c115fea
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244114"
---
# <a name="conflict-types-and-resolution-policies"></a>Ütközés típusa és a névfeloldási házirend

Ütközések és a szabályzatok alkalmazandók, ha Cosmos-fiókja van konfigurálva több ütközésfeloldás írási régió.

Cosmos DB-konfigurálva több írási régióval rendelkező fiókok esetében a frissítések ütközésének előfordulhatnak, ha több írók egyszerre több régióban található azonos elem frissítése. A következő három típusú frissítések ütközésének sorolhatók:

1. **Ütközések beszúrása** fordulhat elő, ha egy alkalmazás egyidejűleg szúr be két vagy több elemet a azonos (például az ID tulajdonság) egyedi indexszel rendelkező két vagy több régió közül. Ebben az esetben az összes írási művelet sikerülhet, kezdetben a megfelelő helyi régióban, de úgy dönt, az ütközésfeloldási házirend alapján, csak egy elemet az eredeti azonosítójú elkötelezett végül.
2. **Cserélje le az ütközések** fordulhat elő, amikor egy alkalmazás frissíti egy elem egyidejűleg két vagy több régió közül.
3. **Törlés ütközések** fordulhat elő, amikor egy alkalmazás egyidejűleg egy elem törlése egy adott régióban található, és frissíti azt más régiókba.

## <a name="conflict-resolution-policies"></a>Ütközés feloldása házirendek

A cosmos DB frissítések ütközésének feloldása egy rugalmas házirendekkel vezérelt mechanizmust kínál. A következő két ütközés feloldása házirendek egy Cosmos-tárolón közül választhat:

- **Wins-utolsó-írási (LWW)** , amely alapértelmezés szerint a rendszer által meghatározott időbélyeg-tulajdonság (időszinkronizálás – óra protokollon alapuló) használja. Azt is megteheti, az SQL API használatával, miközben Cosmos DB lehetővé teszi, hogy minden más egyéni numerikus tulajdonság (más néven az "ütközés feloldása elérési útja") adja meg a használandó ütközésének feloldása.  

Két vagy több elem ütközés a Beszúrás, vagy cserélje le az operations, az elem, amely tartalmazza a legmagasabb érték a "ütközés feloldása elérési útja" a "győztes" lesz. Ha több elem is ugyanazt az ütközés feloldása elérési numerikus értéket, a rendszer a kiválasztott "győztes" verziója határozza meg. Minden régió garantáltan szerveződjön egy egyetlen győztes és a záró fel a véglegesített elem azonos verzióját. Ha részt vevő ütközések törlése, a törölt verziót mindig a WINS-beszúrások és a csere ütközések, az ütközés feloldása elérési út értékétől függetlenül.

> [!NOTE]
> LWW az alapértelmezett ütközésfeloldási házirend és az SQL, a tábla, a MongoDB, a Cassandra és a Gremlin API-k számára érhető el.

További tudnivalókért lásd: [LWW használatával példák feloldási házirendek ütköznek](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Egyéni** a házirend célja az alkalmazás által meghatározott szemantika ütközések egyeztetéséhez. Ez a házirend beállításakor a Cosmos-tárolót is regisztrálnia kell egy merge tárolt eljárás, amely automatikusan megnyílik, ha a frissítés ütközést észlelt a kiszolgálói oldalon az adatbázis-tranzakciók alapján. A rendszer biztosít pontosan egyszer garantálja a kötelezettségvállalás protokoll részeként egy merge eljárás végrehajtására.  

Azonban ha az egyéni feloldási lehetőséggel konfigurálja a tároló, de akár egy merge eljárás a tároló regisztrációja elmarad, vagy az egyesítési eljárás futásidőben kivételt jelez, az ütközések kerüljenek a hírcsatorna ütközéseket. Az alkalmazás ezután kell manuálisan oldja fel az ütközést az ütközések-hírcsatornában. További tudnivalókért lásd: [példa az egyéni házirend és hogyan használhatja a hírcsatorna ütközések](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

> [!NOTE]
> Egyéni ütközésfeloldási házirend csak az SQL API-fiókok számára érhető el.

## <a name="next-steps"></a>További lépések

Ezután megismerheti ütközés feloldása házirendek konfigurálása a következő cikkek segítségével:

* [A LWW ütközésfeloldási házirend használata](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Az egyéni ütközésfeloldási házirend használata](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Az ütközések hírcsatorna használata](how-to-manage-conflicts.md#read-from-conflict-feed)
