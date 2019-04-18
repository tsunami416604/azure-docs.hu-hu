---
title: Az Azure Cosmos DB index típusa
description: Index típusú Azure Cosmos DB-ben – áttekintés
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: 5e7ee7c0bdfd0cff6be182e6d087cc264910e440
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271560"
---
# <a name="index-types-in-azure-cosmos-db"></a>Az Azure Cosmos DB index típusa

Több lehetőség van az indexelési házirendet egy elérési út konfigurálásakor. Egy vagy több indexelő definíciót minden útvonalhoz adhatja meg:

- **Adattípus:** Karakterlánc, szám, pont, Polygon vagy LineString (tartalmazhat elérési adattípus eszközönként csak egy bejegyzés).

- **Index típusa:** Címtartomány (az egyenlőség, tartomány vagy ORDER BY lekérdezések), vagy Spatial (a térinformatikai lekérdezéseket).

- **Pontosság:** Egy tartomány index maximális pontosság értéke -1, ami egyben az alapértelmezett.

## <a name="index-kind"></a>Index típusa

Az Azure Cosmos DB támogatja a tartományindexszel minden elérési utat, karakterlánc vagy szám adattípusok konfigurálható, illetve mindkettőt.

- **Tartományindexszel** támogatja a hatékony egyenlőség lekérdezéseket, a JOIN lekérdezéseket, a lekérdezések (használatával >, <>, =, < =,! =), és ORDER BY lekérdezések. Az ORDER BY lekérdezések, alapértelmezés szerint is szükség maximális pontosság (-1). Az adattípus karakterlánc vagy szám lehet.

- **Térbeli index** támogatja a hatékony térbeli (belül és a távolság) lekérdezéseket. Az adattípus lehet pont, Polygon vagy LineString. Az Azure Cosmos DB is támogatja a térbeli index típusa minden elérési utat, amely a terjesztési pontok, Polygon, valamint LineString adattípusok adható meg. A megadott elérési úton értéke nem lehet például egy érvényes GeoJSON töredék {"type": "Pont", "koordináták": [0.0, 10.0]}. Az Azure Cosmos DB támogatja az automatikus indexelését, LineString, pontot és sokszög adattípusokat.

Példa a lekérdezésekre, amely a címtartomány, és a térbeli indexek is használható:

| **Index típusa** | **Leírás és használati eset** |
| ---------- | ---------------- |
| Tartomány      | Tartomány/prop/keresztül? (vagy vagy) segítségével hatékonyan szolgálja ki a következő lekérdezéseket:<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop > 5<br><br>Válassza ki a gyűjtemény c ORDER BY c.prop<br><br>Tartomány/Kellékek / [] keresztül /? (és / vagy/Kellékek /) segítségével hatékonyan szolgálja ki a következő lekérdezéseket:<br><br>Válassza ki címkét a gyűjtemény c ILLESZTÉSI címke IN c.props WHERE címke = 5  |
| Térbeli    | Tartomány/prop/keresztül? (vagy vagy) segítségével hatékonyan szolgálja ki a következő lekérdezéseket:<br><br>Válassza ki a gyűjtemény c ahol ST_DISTANCE(c.prop, {"type": "Pont", "koordináták": [0.0, 10.0]}) < 40<br><br>Válassza ki a gyűjtemény c ahol ST_WITHIN(c.prop, {"type": "Pontra:",...}) – az indexelést a pont van engedélyezve<br><br>Válassza ki a gyűjtemény c ahol ST_WITHIN({"type": "Sokszög",...}, c.prop) – az indexelést a sokszög engedélyezve van. |

## <a name="default-behavior-of-index-kinds"></a>Index típusú az alapértelmezett viselkedés

- Ha nincs, hogy jelezze, hogy a vizsgálat szükséges szolgálja ki a lekérdezést, ebben az esetben alapértelmezés szerint lehet bármely pontossági tartomány index hibát ad vissza a lekérdezéseket. például tartomány operátorok > =.

- Tartomány index nélkül is elvégezhető lekérdezések használatával a **x-ms-documentdb-enable-vizsgálat** fejléc a REST API-ban vagy a **EnableScanInQuery** beállítás kérése a .NET SDK használatával. Ha bármilyen más szűrők a lekérdezésben, hogy az Azure Cosmos DB az index szűrésére használható ellen, nincs hiba adja vissza.

- Alapértelmezés szerint hibát ad vissza térbeli lekérdezéseket Ha nem talál egy térbeli index, vagy más, amely az indexből kiszolgálható szűrőt. Az ilyen lekérdezések használatával is elvégezhető a vizsgálat **x-ms-documentdb-enable-vizsgálat** vagy **EnableScanInQuery**.

## <a name="index-precision"></a>Index pontosság

> [!NOTE]
> Azure Cosmos-tárolók egy új index elrendezést, amely már nem igényel az egy egyéni index pontosság, a maximális pontosság (-1) nem támogatja. Ezzel a módszerrel elérési utak mindig a legnagyobb pontosságú indexelt. Ha megad egy pontossági értéknek a az indexelési házirendet, a CRUD-kérelmekre, a a tárolók csendes figyelmen kívül hagyja a pontosság és a válasz a tároló csak a maximális pontosság (-1) tartalmazza.  Az összes új Cosmos-tároló alapértelmezés szerint az új elrendezésnek index használja.

- Index pontosság használhatja, hogy a indextárolási terheléssel jár és a lekérdezési teljesítmény között. Számok javasolt az alapértelmezett pontosság konfiguráció 1 (maximum) használatával. Mivel számok 8 bájtos JSON-ban, ez az egyenértékű 8 bájtos konfigurációra. 1 – 7, például a pontosság kisebb értéket választja azt jelenti, hogy az egyes tartományokon belül értékek leképezése azonos index bejegyzés. Ezért csökkentheti az index tárolóhely, de a lekérdezés végrehajtása lehet feldolgozni a további elemeket. Ennek következtében további átviteli sebesség/Kérelemegységet fogyaszt.

- Index pontosság karakterlánc tartományok több gyakorlati alkalmazás rendelkezik. Karakterláncok bármilyen tetszőleges hosszúságú lehet, mert az index pontosság választása hatással lehetnek karakterlánc lekérdezések teljesítményét. Index tárterület szükséges mennyisége is érintheti. Karakterlánc tartomány indexek is konfigurálhatók, egy index pontosság 1 és 100 vagy -1 (maximális) között. ORDER BY-lekérdezéseket futtassanak karakterlánc tulajdonságait szeretné, ha meg kell adnia a megfelelő elérési utakra 1 pontossága.

- A térbeli indexek mindig használja az alapértelmezett index pontosság (pont, LineString és sokszög) minden alkalmazástípus esetében. Az alapértelmezett index pontosság a térbeli indexek nem lehet felülírni.

Az Azure Cosmos DB hibát ad vissza, ha a lekérdezés ORDER BY használja, de nem rendelkezik a legnagyobb pontosságú lekérdezett elérési elleni tartomány indexszel.

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB indexelése kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Az indexelő áttekintése](index-overview.md)
- [Indexelési házirend](indexing-policies.md)
- [Index elérési utak](index-paths.md)

