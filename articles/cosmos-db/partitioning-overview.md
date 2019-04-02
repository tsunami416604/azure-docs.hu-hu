---
title: Az Azure Cosmos DB particionálási
description: Az Azure Cosmos DB particionálási áttekintése.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: e88be8e7b94566ff94dd94a8679f8ade9d54c0b6
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762318"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Az Azure Cosmos DB particionálási

Az Azure Cosmos DB használja a particionálás az egyes tárolók méretezését egy adatbázis teljesítmény az alkalmazás igényeinek. A particionálás, a tárolóban lévő elemek vannak osztva, különböző alkészleteiben nevű *logikai partíció*. Logikai partíciókkal értékei alapján kialakított egy *partíciókulcs* társítva a tárolóban lévő egyes elemek. Egy logikai partíció összes elemet az egyazon partíciókulcs-értékkel rendelkezik.

Például a tároló elemeket tartalmazza. Minden cikk rendelkezik egy egyedi értéket a `UserID` tulajdonság. Ha `UserID` adattárat biztosít, a partíciós kulcs a tárolóban lévő elemek, és nincsenek egyedi 1000 `UserID` értékek, 1000 logikai partíció jön létre a tárolót.

Meghatározza, hogy az elem logikai partíciót egy partíciókulcsot, egy tárolóban lévő egyes elemek most egy *konfigurációelem-azonosító* (egyedi logikai partíción belül). A cikk a partíciókulcs és a munkaelem azonosítója hoz létre *index*, amely egyedileg azonosítja az elemet.

[Olyan partíciókulcsot](partitioning-overview.md#choose-partitionkey) egy fontos döntés, melyek hatással lesznek az alkalmazás teljesítményét.

## <a name="managing-logical-partitions"></a>Logikai partíciókkal kezelése

Az Azure Cosmos DB átláthatóan és automatikusan felügyeli a logikai partíció hatékonyan a tároló méretezhetőséget és teljesítményt igényeinek kielégítéséhez fizikai partíciók elhelyezését. Egy alkalmazás adatátviteli és tárolási követelményeinek növelése érdekében, Azure Cosmos DB automatikusan a kiszolgálók nagyobb számú között elosztani a terhelést logikai partíciót helyezi át. 

Az Azure Cosmos DB használ a kivonat-alapú particionálás érdekében logikai partíció fizikai partíciók között. Az Azure Cosmos DB kivonatolja egy elem a partíciós kulcs értékét. A kivonatolt eredmény meghatározza, hogy a fizikai partíciók. Ezt követően Azure Cosmos DB foglal le a kulcsfontosságú terület partíció kulcs kivonatok egyenlően a fizikai partíciók között.

Egy olyan logikai partíciót adatok elérő lekérdezésekben, költséghatékonyabb, mint a több partíciót elérő lekérdezésekben,. Tranzakciók (a tárolt eljárások és eseményindítók) csak egyetlen logikai partíciót elemeinek szemben engedélyezettek.

Hogyan kezeli az Azure Cosmos DB a partíciók kapcsolatos további információkért lásd: [logikai partíció](partition-data.md). (Azt nem megértéséhez a belső részleteit, illetve az alkalmazások futtatásához szükséges, de az itt hozzáadott inspirációkkal olvasói.)

## <a id="choose-partitionkey"></a>Olyan partíciókulcsot

Az alábbiakban látható egy olyan partíciókulcsot érdemes útmutatást:

* Egy olyan logikai partíciót tartalmaz felső korlátja 10 GB tárhelyet.  

* Azure Cosmos-tárolók rendelkezik egy minimum 400 kérelemegység / másodperc (RU/s) átviteli sebességet. Ugyanazzal a partíciókulccsal kérelmeket nem haladhatja meg az átviteli sebességet, amely hozzá van rendelve egy partíciót. Kérelmek száma meghaladja a kiosztott átviteli sebesség, ha a kérések sebessége korlátozott. Ezért fontos, hogy az alkalmazáson belül "hotspotok" nem eredményez olyan partíciókulcsot válasszon.

* Válassza ki a partíciós kulcs, számos különféle értékeket és a hozzáférési mintákat, amelyek egyenlően vannak elosztva a logikai partíciókkal rendelkező. Ez segít az adatok és a tárolót a tevékenység elosztva a logikai partíció készletét, hogy az adatok tárolási és átviteli sebesség erőforrások a logikai partíció szét lehetnek osztva.

* Válassza ki a partíciós kulcs, amely a számítási feladat összes partíciójára és egyenletesen idővel egyenlően osztja el. A választott partíciókulcsot kell terheléselosztást végeznie hatékony partíció lekérdezéseket és tranzakciókat a kitűzött célérték elemek elosztása több partíción a skálázhatóság érdekében szükség.

* Jelöltek a partíciókulcsok tartalmazhatnak, amelyek gyakran jelennek meg a lekérdezéseket szűrőként tulajdonságai. Lekérdezések a partíciókulcs a szűrőpredikátumban fel hatékonyan továbbíthatók.

## <a name="next-steps"></a>További lépések

* Ismerje meg [particionálási és horizontális skálázás az Azure Cosmos DB](partition-data.md).
* Ismerje meg [kiosztott átviteli sebesség az Azure Cosmos DB](request-units.md).
* Ismerje meg [az Azure Cosmos DB globális terjesztésének](distribute-data-globally.md).
