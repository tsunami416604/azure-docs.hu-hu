---
title: Az Azure Cosmos DB index típusa
description: Index típusú Azure Cosmos DB-ben – áttekintés
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: f45663fd0f63537f87ee4466ad5f17cce0bed6a3
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961720"
---
# <a name="index-types-in-azure-cosmos-db"></a>Az Azure Cosmos DB index típusa

Több lehetőség van, konfigurálhatja az indexelési házirendet az elérési utat. Egy vagy több indexelő definíciót minden útvonalhoz adhatja meg:

- **Adattípus:** Karakterlánc, szám, pont, Polygon vagy LineString (tartalmazhat elérési adattípus eszközönként csak egy bejegyzés).

- **Index típusa:** Ujjlenyomat (egyenlőség lekérdezések), a tartomány (egyenlőség, tartomány vagy ORDER BY lekérdezések) vagy Spatial (térbeli lekérdezések).

- **Pontosság:** Kivonatoló index Ez az érték 1-8 karakterláncok és a számok és az alapértelmezett érték 3. A tartomány index maximális pontosság értéke -1. 1 és 100 (maximális pontosság) karakterlánc vagy szám érték között változhat.

## <a name="index-kind"></a>Index típusa

Az Azure Cosmos DB támogatja a kivonat index és tartományindexszel minden elérési utat, karakterlánc vagy szám adattípusok konfigurálható, illetve mindkettőt.

- **Kivonatoló index** hatékony egyenlőség és JOIN lekérdezéseket támogat. A legtöbb használati esetek kivonatindexek pontossága nagyobb, mint az alapértelmezett érték 3 bájt nem szükséges. Az adattípus karakterlánc vagy szám lehet.

  > [!NOTE]
  > Azure Cosmos-tárolók támogatják a egy új index elrendezést, amely már nem használ a kivonat index típusa. Ha megad egy kivonatot index szolgálhat a az indexelési házirendet, a CRUD-kérések a tárolón csendes figyelmen kívül hagyja az index típusa és a tárolóban csak válaszban tartomány index típusa. Az összes új Cosmos-tároló alapértelmezés szerint az új index elrendezés használják. 
  
- **Tartományindexszel** támogatja hatékony egyenlőség lekérdezéseket, a lekérdezések (használatával >, <>, =, < =,! =), és ORDER BY lekérdezések. Alapértelmezés szerint az ORDER By lekérdezések maximális pontosság (-1) is szükséges. Az adattípus karakterlánc vagy szám lehet.

- **Térbeli index** támogatja a hatékony térbeli (belül és a távolság) lekérdezéseket. Az adattípus lehet pont, Polygon vagy LineString. Az Azure Cosmos DB is támogatja a térbeli index típusa minden elérési utat, amely a terjesztési pontok, Polygon, valamint LineString adattípusok adható meg. A megadott elérési úton értéke nem lehet például egy érvényes GeoJSON töredék {"type": "Pont", "koordináták": [0.0, 10.0]}. Az Azure Cosmos DB támogatja az automatikus indexelését, LineString, pontot és sokszög adattípusokat.

Példa a lekérdezéseket, amelyek kivonata, tartomány, és a térbeli indexek is használható:

| **Index típusa** | **Leírás és használati eset** |
| ---------- | ---------------- |
| Kivonat  | Ujjlenyomat-keresztül/prop /? (vagy vagy) segítségével hatékonyan szolgálja ki a következő lekérdezéseket:<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Kivonatoló keresztül/Kellékek / [] /? (és / vagy/Kellékek /) segítségével hatékonyan szolgálja ki a következő lekérdezéseket:<br><br>Válassza ki címkét a gyűjtemény c ILLESZTÉSI címke IN c.props WHERE címke = 5  |
| Tartomány  | Tartomány/prop/keresztül? (vagy vagy) segítségével hatékonyan szolgálja ki a következő lekérdezéseket:<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop > 5<br><br>Válassza ki a gyűjtemény c ORDER BY c.prop   |
| Térbeli     | Tartomány/prop/keresztül? (vagy vagy) segítségével hatékonyan szolgálja ki a következő lekérdezéseket:<br><br>Válassza ki a gyűjtemény c<br><br>HOL ST_DISTANCE (c.prop, {"type": "Pont", "koordináták": [0.0, 10.0]}) < 40<br><br>Válassza ki a gyűjtemény c ahol ST_WITHIN(c.prop, {"type": "Pontra:",...}) – az indexelést a pont van engedélyezve<br><br>Válassza ki a gyűjtemény c ahol ST_WITHIN({"type": "Sokszög",...}, c.prop) – az indexelést a sokszög engedélyezve van.     |

## <a name="default-behavior-of-index-kinds"></a>Index típusú az alapértelmezett viselkedés

- Ha nincs, hogy jelezze, hogy a vizsgálat szükséges szolgálja ki a lekérdezést, ebben az esetben alapértelmezés szerint lehet bármely pontossági tartomány index hibát ad vissza a lekérdezéseket. például tartomány operátorok > =.

- Lekérdezések az "x-ms-documentdb-enable-ellenőrzés" fejléc a REST API-ban vagy a "EnableScanInQuery" kérelem lehetőség használatával a .NET SDK-val egy tartományindexszel nélkül végrehajtható. Ha bármilyen más szűrők a lekérdezésben, hogy az Azure Cosmos DB az index szűrésére használható ellen, nincs hiba adja vissza.

- Alapértelmezés szerint hibát ad vissza térbeli lekérdezéseket Ha nem talál egy térbeli index, vagy más, amely az indexből kiszolgálható szűrőt. Az ilyen lekérdezések keresés x-ms-documentdb-enable-vizsgálat vagy EnableScanInQuery használatával hajtható végre.

## <a name="index-precision"></a>Index pontosság

> [!NOTE]
> Azure Cosmos-tárolók támogatják a egy új index elrendezést, amely már nem igényli a legnagyobb pontosságú value(-1) eltérő egyéni index pontossága. Ezzel a módszerrel elérési utak mindig a legnagyobb pontosságú indexelt. Ha megad egy pontossági értéknek a az indexelési házirendet, a CRUD-kérelmekre, a a tárolók csendes figyelmen kívül hagyja a pontosság és a válasz a tároló csak a legnagyobb pontosságú value(-1) tartalmazza.  Az összes új Cosmos-tároló alapértelmezés szerint az új index elrendezés használják.

- Index pontosság használhatja, hogy az index tárolási terhelés és a lekérdezési teljesítmény között. Számok javasolt az alapértelmezett pontosság konfigurációt a-1 (maximum értéket) használja. Mivel számok 8 bájtos JSON-ban, ez az egyenértékű 8 bájtos konfigurációra. 1 – 7, például a pontosság kisebb értéket választja azt jelenti, hogy az egyes tartományokon belül értékek leképezése azonos index bejegyzés. Ezért index tárolóhely csökkentheti, de a lekérdezés végrehajtása lehet feldolgozni a további elemeket. Ennek következtében további átviteli sebesség/Kérelemegységet fogyaszt.

- Index pontosság karakterlánc tartományok több gyakorlati alkalmazás rendelkezik. Karakterláncok bármilyen tetszőleges hosszúságú lehet, mert az index pontosság választása hatással lehetnek karakterlánc lekérdezések teljesítményét. Index tárterület szükséges mennyisége is érintheti. Karakterlánc tartomány indexek is konfigurálhatók, egy index pontosság 1 és 100 vagy -1 (maximális) között. ORDER BY-lekérdezéseket futtassanak karakterlánc tulajdonságait szeretné, ha meg kell adnia a megfelelő elérési utakra 1 pontossága.

- A térbeli indexek mindig használja az alapértelmezett index pontosság (pont, LineString és sokszög) minden alkalmazástípus esetében. Az alapértelmezett index pontosság a térbeli indexek nem lehet felülírni.

Az Azure Cosmos DB hibát ad vissza, ha a lekérdezés ORDER BY használja, de nem rendelkezik a legnagyobb pontosságú lekérdezett elérési elleni tartomány indexszel.

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB indexelése kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Az indexelő áttekintése](index-overview.md)
- [Indexelési házirend](indexing-policies.md)
- [Index elérési utak](index-paths.md)

