---
title: Particionálás az Azure Cosmos DB-ben
description: Ismerje meg az Azure Cosmos DB particionálását, a partíciókulcs kiválasztásakor ajánlott eljárásokat, valamint a logikai partíciók kezelésének módját
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251868"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Particionálás az Azure Cosmos DB-ben

Az Azure Cosmos DB particionálást használ az egyes tárolók méretezéséhez az adatbázisban az alkalmazás teljesítményigényeinek kielégítésére. A particionáláskor a tárolóelemei különböző részhalmazokra, úgynevezett *logikai partíciókra vannak osztva.* A logikai partíciók a tároló ban lévő egyes elemekhez társított *partíciókulcs* értéke alapján jönnek létre. A logikai partíció minden elemének ugyanaz a partíciókulcs-értéke.

Egy tároló például elemeket tárol. Minden elem egyedi értékkel `UserID` rendelkezik a tulajdonsághoz. Ha `UserID` a tárolóban lévő elemek partíciókulcsaként szolgál, és `UserID` 1000 egyedi érték van, 1000 logikai partíció jön létre a tárolóhoz.

Az elem logikai partícióját meghatározó partíciókulcson kívül a tároló minden eleme rendelkezik *egy elemazonosítóval* (amely egy logikai partíción belül egyedi). A partíciókulcs és az elemazonosító egyesítése létrehozza az elem *indexét,* amely egyedileg azonosítja az elemet.

[A partíciókulcs kiválasztása](partitioning-overview.md#choose-partitionkey) fontos döntés, amely hatással lesz az alkalmazás teljesítményére.

## <a name="managing-logical-partitions"></a>Logikai partíciók kezelése

Az Azure Cosmos DB transzparens módon, és automatikusan kezeli a logikai partíciók fizikai partíciókon a méretezhetőség és a teljesítmény igényeinek hatékony kielégítése érdekében. Az alkalmazás átviteli és tárolási igényeinek növekedésével az Azure Cosmos DB logikai partíciókat helyez át, hogy a terhelés tágítható a kiszolgálók nagyobb száma között. 

Az Azure Cosmos DB kivonatalapú particionálást használ a logikai partíciók fizikai partíciók közötti terjesztéséhez. Az Azure Cosmos DB kiírja egy elem partíciókulcs-értékét. A kivonatolt eredmény határozza meg a fizikai partíciót. Ezután az Azure Cosmos DB a partíciókulcs-kiã-k kulcsterületét egyenletesen osztja le a fizikai partíciók között.

Az egyetlen logikai partíción belüli adatokhoz hozzáférő lekérdezések költséghatékonyabbak, mint a több partícióhoz hozzáférő lekérdezések. A tranzakciók (a tárolt eljárásokban vagy eseményindítókban) csak egyetlen logikai partíción lévő elemek ellen engedélyezettek.

Ha többet szeretne tudni arról, hogy az Azure Cosmos DB hogyan kezeli a partíciókat, olvassa [el a Logikai partíciók](partition-data.md). (Nem szükséges megérteni a belső részleteket építeni vagy futtatni az alkalmazásokat, de hozzá itt egy kíváncsi olvasó.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Partíciókulcs kiválasztása

A következő egy jó útmutatást választott partíciókulcs:

* Egyetlen logikai partíció felső határa 20 GB tárterület.  

* Az Azure Cosmos-tárolók legalább 400 kérelemegység/másodperc (RU/s) átviteli értékével rendelkeznek. Ha az átviteli rendszer egy adatbázisban van kiépítve, a tárolónkénti minimális rus-ok 100 kérelemegység/másodperc (RU/s). Ugyanarra a partíciókulcsra irányuló kérelmek nem haladhatják meg a partícióhoz lefoglalt átviteli. Ha a kérelmek meghaladják a lefoglalt átviteli sebességet, a kérelmek díja korlátozott. Ezért fontos, hogy válasszon egy partíciókulcsot, amely nem eredményez "hot spots" az alkalmazáson belül.

* Válasszon olyan partíciókulcsot, amely értékek és hozzáférési minták széles skáláját kínálja, amelyek egyenletesen oszlanak el a logikai partíciók között. Ez segít az adatok és a tevékenység a tárolóban a logikai partíciók között, így az erőforrások az adattárolás és az átviteli kapacitás lehet osztani a logikai partíciók között.

* Válasszon egy partíciókulcsot, amely egyenletesen osztja el a számítási feladatot az összes partícióközött, és egyenletesen az idő múlásával. A választott partíciókulcs nak egyensúlyt kell teremtenie a hatékony partíciólekérdezések és tranzakciók iránti igény és az elemek több partíció közötti elosztása érdekében a méretezhetőség elérése érdekében.

* A partíciókulcsokra jelöltek olyan tulajdonságokat tartalmazhatnak, amelyek gyakran jelennek meg szűrőként a lekérdezésekben. A lekérdezések hatékonyan irányíthatók a partíciókulcs nak a szűrőpredikátumba való belefoglalásával.

## <a name="next-steps"></a>További lépések

* Ismerje meg a [particionálásés horizontális skálázás az Azure Cosmos DB.](partition-data.md)
* Ismerje meg [az Azure Cosmos DB kiépített átviteli.](request-units.md)
* Ismerje meg [az Azure Cosmos DB globális disztribúcióját.](distribute-data-globally.md)
