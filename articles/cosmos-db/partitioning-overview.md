---
title: Az Azure Cosmos DB particionálási
description: Az Azure Cosmos DB particionálási áttekintése.
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: a8db510bea57fa3d6ee873571e586bcef7508b26
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961635"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Az Azure Cosmos DB particionálási

Az Azure Cosmos DB használja a particionálás az egyes tárolók méretezését egy adatbázis teljesítmény az alkalmazás igényeinek. A particionálás, a tárolóban lévő elemek vannak osztva, különböző alkészleteiben nevű *logikai partíció*. Logikai partíciókkal értékei alapján kialakított egy *partíciókulcs* , amely egy tárolóban lévő egyes elemek van társítva. Egy logikai partíció összes elemet az egyazon partíciókulcs-értékkel rendelkezik.

Például egy tárolóban tárolja a dokumentumokat. Egyes dokumentumok egyedi értékkel rendelkezik a `UserID` tulajdonság. Ha `UserID` adattárat biztosít, a partíciós kulcs a tárolóban lévő elemek, és nincsenek egyedi 1000 `UserID` értékek, 1000 logikai partíció jön létre a tárolót.

Meghatározza, hogy az elem logikai partíciót egy partíciókulcsot, egy tárolóban lévő egyes elemek most egy *konfigurációelem-azonosító* (egyedi logikai partíción belül). A cikk a partíciókulcs és a munkaelem azonosítója hoz létre *index*, amely egyedileg azonosítja az elemet.

[Olyan partíciókulcsot](partitioning-overview.md#choose-partitionkey) egy fontos döntés, melyek hatással lesznek az alkalmazás teljesítményét.

## <a name="managing-logical-partitions"></a>Logikai partíciókkal kezelése

Az Azure Cosmos DB átláthatóan és automatikusan felügyeli a logikai partíció hatékonyan a tároló méretezhetőséget és teljesítményt igényeinek kielégítéséhez (a kiszolgálói infrastruktúra) a fizikai partíciók elhelyezését. Egy alkalmazás adatátviteli és tárolási követelményeinek növelése érdekében, Azure Cosmos DB automatikusan a kiszolgálók nagyobb számú között elosztani a terhelést logikai partíciót helyezi át. 

Az Azure Cosmos DB használ a kivonat-alapú particionálás érdekében logikai partíció fizikai partíciók között. Az Azure Cosmos DB kivonatolja egy elem a partíciós kulcs értékét. A kivonatolt eredmény meghatározza, hogy a fizikai partíciók. Ezt követően Azure Cosmos DB foglal le a kulcsfontosságú terület partíció kulcs kivonatok egyenlően a fizikai partíciók között.

Ugyanazon a partíción belül adatokat elérő lekérdezésekben, költséghatékonyabb, mint a több partíciót lekérdezések. Tranzakciók (a tárolt eljárások és eseményindítók) csak egyetlen logikai partíciót elemeinek szemben engedélyezettek.

Hogyan kezeli az Azure Cosmos DB a partíciók kapcsolatos további információkért lásd: [logikai partíció](partition-data.md). (Ez még nem szükséges tudni, hogy ezeket az adatokat, illetve az alkalmazások futtatásához.)

## <a id="choose-partitionkey"></a>Olyan partíciókulcsot

Ha úgy dönt, hogy egy partíciókulcsot, vegye figyelembe a következő adatokat:

* Egy olyan logikai partíciót tartalmaz felső korlátja 10 GB tárhelyet.  

* Particionált tárolók rendelkezik egy minimum 400 kérelemegység / másodperc (RU/s) teljesítményét. Ugyanazzal a partíciókulccsal kérelmeket nem haladhatja meg az átviteli sebességet, amely hozzá van rendelve egy partíciót. Kérelmek száma meghaladja a kiosztott átviteli sebesség, ha a kérések sebessége korlátozott. Ezért fontos, hogy az alkalmazáson belül "hotspotok" nem eredményez olyan partíciókulcsot válasszon.

* Válassza ki a partíciós kulcs, amely a számítási feladat összes partíciójára és egyenletesen idővel egyenlően osztja el. A választott partíciókulcsot kell terheléselosztást végeznie hatékony partíció lekérdezéseket és tranzakciókat a kitűzött célérték elemek elosztása több partíción a skálázhatóság érdekében szükség.

* Válassza ki a partíciós kulcs, számos különféle értékeket és a hozzáférési mintákat, amelyek egyenlően vannak elosztva a logikai partíciókkal rendelkező. Ez segít az adatok és a tárolót a tevékenység elosztva a logikai partíció készletét, hogy az adatok tárolási és átviteli sebesség erőforrások a logikai partíció szét lehetnek osztva.

* Jelöltek a partíciókulcsok tartalmazhatnak, amelyek gyakran jelennek meg a lekérdezéseket szűrőként tulajdonságai. Lekérdezések a partíciókulcs a szűrőpredikátumban fel hatékonyan továbbíthatók.

## <a name="next-steps"></a>További lépések

* Ismerje meg [partíciók](partition-data.md).
* Ismerje meg [kiosztott átviteli sebesség az Azure Cosmos DB](request-units.md).
* Ismerje meg [az Azure Cosmos DB globális terjesztésének](distribute-data-globally.md).
