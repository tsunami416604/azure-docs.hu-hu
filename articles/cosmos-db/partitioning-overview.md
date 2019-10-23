---
title: Particionálás Azure Cosmos DB
description: A particionálás áttekintése Azure Cosmos DBban.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: e80e548ceae2149fe7061da42c71ee8b61f00a72
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717558"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Particionálás Azure Cosmos DB

A Azure Cosmos DB particionálás használatával méretezi az egyes tárolókat egy adatbázisban az alkalmazás teljesítménybeli igényeinek kielégítése érdekében. A particionálás során a tároló elemei a *logikai partíciók*nevű különálló részhalmazokra vannak osztva. A logikai partíciók a tároló egyes elemeihez társított *partíciós kulcs* értéke alapján jönnek létre. Egy logikai partíció összes elemének ugyanaz a partíciós kulcs értéke.

Egy tároló például elemeket tárol. Minden egyes tétel egyedi értékkel rendelkezik a `UserID` tulajdonsághoz. Ha `UserID` a tárolóban lévő elemek partíciós kulcsaként szolgál, és 1 000 egyedi `UserID` érték van, akkor a rendszer 1 000 logikai partíciókat hoz létre a tárolóhoz.

Egy olyan partíciós kulcs mellett, amely meghatározza az elem logikai partícióját, a tároló minden eleméhez tartozik egy *elem azonosítója* (egyedi logikai partíción belül). A partíciós kulcs és az elem AZONOSÍTÓjának kombinálásával létrejön azelem indexe, amely egyedileg azonosítja az adott tételt.

[A partíciós kulcs kiválasztása](partitioning-overview.md#choose-partitionkey) fontos döntés, amely hatással lesz az alkalmazás teljesítményére.

## <a name="managing-logical-partitions"></a>Logikai partíciók kezelése

Azure Cosmos DB átlátható módon, és automatikusan kezeli a logikai partíciók elhelyezését a fizikai partíciókon, hogy hatékonyan kielégítse a tároló méretezhetőségét és teljesítményét. Az alkalmazások átviteli sebességének és tárolási követelményeinek növekedésével Azure Cosmos DB a logikai partíciók mozgatásával automatikusan elosztja a terhelést a több kiszolgáló között. 

A Azure Cosmos DB kivonatoló particionálást használ a logikai partíciók fizikai partíciók közötti elterjesztéséhez. Azure Cosmos DB kivonatok egy adott tétel partíciós kulcsának értékét. A kivonatos eredmény határozza meg a fizikai partíciót. Ezután a Azure Cosmos DB a partíciós kulcsok kivonatait egyenletesen osztja el a fizikai partíciók között.

Azok a lekérdezések, amelyek egyetlen logikai partíción belül érik el az adatelérést, költséghatékonyabbak, mint több partícióhoz hozzáférő lekérdezések. A tranzakciókat (tárolt eljárásokban vagy eseményindítókban) csak egyetlen logikai partíció elemeire lehet engedélyezni.

Ha többet szeretne megtudni arról, hogyan kezeli a Azure Cosmos DB a partíciókat, tekintse meg a [logikai partíciókat](partition-data.md). (Nem szükséges megérteni az alkalmazások létrehozásához és futtatásához szükséges belső adatokat, de itt egy kíváncsi olvasóhoz is hozzá lehet adni őket.)

## <a id="choose-partitionkey"></a>Partíciós kulcs kiválasztása

A következő hasznos útmutatást nyújt a partíciós kulcs kiválasztásához:

* Egyetlen logikai partíció 10 GB-os felső korláttal rendelkezik.  

* Az Azure Cosmos-tárolók minimális átviteli sebessége 400 másodpercenként (RU/s). Ha az átviteli sebesség egy adatbázison van kiépítve, a tárolók minimális száma másodpercenként 100 (RU/s). Az ugyanahhoz a partíciós kulcshoz küldött kérések nem léphetik túl a partícióhoz lefoglalt átviteli sebességet. Ha a kérések túllépik a lefoglalt átviteli sebességet, a kérések száma korlátozott. Ezért fontos, hogy olyan partíciós kulcsot válasszon, amely nem eredményez "forró pontokat" az alkalmazáson belül.

* Válasszon egy olyan partíciós kulcsot, amely az értékek és a hozzáférési minták széles körét és a logikai partíciók közötti egyenletes eloszlást tartalmaz. Ez segít a tárolóban lévő adatok és tevékenységek elosztásában a logikai partíciók készletében, így az adattárolásra és az átviteli sebességre vonatkozó erőforrások eloszthatók a logikai partíciók között.

* Olyan partíciós kulcsot válasszon, amely egyenletesen osztja el a munkaterhelést az összes partíción, és egyenletesen az idő múlásával. Az Ön által választott partíciós kulcsnak egyensúlyba kell hoznia a hatékony partíciós lekérdezéseket és tranzakciókat azzal a céllal, hogy a méretezhetőség érdekében több partíció között ossza ki az elemeket.

* A partíciós kulcsok pályázói tartalmazhatnak olyan tulajdonságokat, amelyek gyakran megjelennek szűrőként a lekérdezésekben. A lekérdezések hatékonyan irányíthatók úgy, hogy a Filter predikátumban található partíciós kulcsot is megadhatják.

## <a name="next-steps"></a>További lépések

* Tudnivalók a [particionálásról és a horizontális skálázásról Azure Cosmos db](partition-data.md).
* További információ [a kiépített átviteli sebességről Azure Cosmos db](request-units.md).
* Ismerkedjen meg a [Azure Cosmos db globális eloszlásával](distribute-data-globally.md).
