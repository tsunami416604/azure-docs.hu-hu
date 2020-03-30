---
title: Az Azure Cosmos DB díjszabási modellje
description: Ez a cikk ismerteti az Azure Cosmos DB díjszabási modelljét, és azt, hogy hogyan egyszerűsíti a költségkezelést és a költségtervezést.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7efae8fb3c00868e2740eac2d4d5bcb3c82f663a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75977538"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Díjszabási modell az Azure Cosmos DB-ben 

Az Azure Cosmos DB díjszabási modellje leegyszerűsíti a költségkezelést és -tervezést. Az Azure Cosmos DB-vel a kiosztott átviteli sebességért és a felhasznált tárterületért fizet.

* **Kiépített átviteli:** A kiosztott átviteli teljesítmény (más néven fenntartott átviteli teljesítmény) garantálja a nagy teljesítményt bármely skálán. Adja meg az átviteli -átviteli (RU/s), hogy szükség van, és az Azure Cosmos DB szenteli a konfigurált átviteli érték biztosításához szükséges erőforrásokat. Egy adott órára vonatkozó maximális kiosztott átviteli teljesítményért óránként számlázunk.

   > [!NOTE]
   > Mivel a kiosztott átviteli modell erőforrásokat szentel a tárolónak vagy az adatbázisnak, akkor is fizetnie kell a kiosztott átviteli sebességgel, ha nem futtat semmilyen számítási feladatot.

* **Felhasznált tárterület:** Az adatokhoz felhasznált tárterület (GB-k) teljes mennyiségéért és az indexekért egy adott órára vonatkozó átalánydíjat számlázunk.

A kiépített átviteli, [kérési egység/másodperc](request-units.md) (RU/s) beállításlehetővé teszi az adatok olvasását vagy írását tárolókba vagy adatbázisokba. Az [átviteli átbocsátás adatbázison vagy tárolón is kiépíthető.](set-throughput.md) A számítási feladatok igényei nek megfelelően bármikor felfelé és lefelé skálázhatja az átviteli terhelést. Az Azure Cosmos DB díjszabása rugalmas, és arányos az adatbázisban vagy tárolóban konfigurált átviteli. A minimális átviteli és tárolási értékek, valamint a méretezési lépések az ár és a rugalmassági spektrum teljes skáláját biztosítják az ügyfelek minden szegmense számára, a kis méretűtől a nagyméretű tárolókig. Minden adatbázis vagy tároló óránként számláza a 100 RU/s egységben kiosztott átviteli forgalomért, legalább 400 RU/s-ért, valamint a GB-kben felhasznált tárolásért. A kiosztott átviteli kapacitással ellentétben a tárolás számlázása a felhasználás alapján történik. Ez azt, hogy nem kell előre lefoglalnia a tárhelyet. A számlázás csak a felhasznált tárhelyért lesz számlázva.

További információt az [Azure Cosmos DB díjszabási lapján](https://azure.microsoft.com/pricing/details/cosmos-db/) és [az Azure Cosmos DB-számlájának ismertetése című témakörben talál.](understand-your-bill.md)

Az Azure Cosmos DB díjszabási modellje minden API-ban konzisztens. További információ: Hogyan érhető el [az Azure Cosmos DB díjszabási modellje az ügyfelek számára.](total-cost-ownership.md) Az adatbázisban vagy a tárolóban egy minimális átviteli szükséges az SL-ek biztosításához, és növelheti vagy csökkentheti a kiosztott átviteli-átviteli 100 RU/s$6-tal.

Jelenleg az adatbázis és a tárolóalapú átviteli érték minimális ára $24/hó (a legfrissebb információkért tekintse meg az [Azure Cosmos DB díjszabási oldalát).](https://azure.microsoft.com/pricing/details/cosmos-db/) Ha a számítási feladatok több tárolót használnak, adatbázisszintű átviteli-átmenő használatával költségre optimalizálható, mivel az adatbázis szintű átviteli-átatot lehetővé teszi, hogy tetszőleges számú tároló tegyék meg az adatbázisban az átviteli szintet a tárolók között. Az alábbi táblázat összefoglalja a kiosztott átviteli átbocsátásot és a különböző entitások költségeit:

|**Entitás**  | **Minimális átviteli & költség** |**Költség & lépések méretezése** |**Kiépítési hatókör** |
|---------|---------|---------|-------|
|Adatbázis    | 400 RU/s ($24/hó)    | 100 RU/s ($6/hó)   |Az átviteli átmenő ség az adatbázis számára van fenntartva, és az adatbázison belüli tárolók osztják meg |
|Tároló     | 400 RU/s ($24/hó)    | 100 RU/s ($6/hó)  |Az átviteli cím egy adott tárolószámára van fenntartva |

Az előző táblázatban látható módon az Azure Cosmos DB minimális átviteli sebességgel kezdődik 24 usd/hó áron. Ha a minimális átviteli és az éles számítási feladatok támogatásának érdekében a minimális átviteli terheléssel kezdi, a költségek zökkenőmentesen emelkednek, 6 000 000 000 000 000 000 000 000 000 000 000 000$-os havi lépésekben. Az Azure Cosmos DB díjszabási modellje rugalmas, és az ár egyenletes növekedése vagy csökkenése van a fel- vagy leskálázás során.

## <a name="try-azure-cosmos-db-for-free"></a>Az Azure Cosmos DB ingyenes kipróbálása 

Az Azure Cosmos DB számos lehetőséget kínál a fejlesztők számára, hogy azt ingyen. Ezek a lehetőségek a következők:

* **Ingyenes Azure-fiók:** Az Azure [egy ingyenes csomagot](https://azure.microsoft.com/free/) kínál, amely $200-os Azure-kreditet biztosít az első 30 napra, és korlátozott mennyiségű ingyenes szolgáltatást 12 hónapra. További információkért lásd az [ingyenes Azure-fiókot](../cost-management-billing/manage/avoid-charges-free-account.md) ismertető cikket. Az Azure Cosmos DB az Ingyenes Azure-fiók része. Ez az ingyenes fiók 5 GB-os tárhelyet és 400 kiépített átviteli kapacitást kínál az egész évre. 

* **Próbálja ki az Azure Cosmos DB-t ingyenesen:** Az Azure Cosmos DB időkorlátos élményt nyújt az Azure Cosmos DB ingyenes fiókokhoz való kipróbálásával. Létrehozhat egy Azure Cosmos DB-fiókot, létrehozhat adatbázist és gyűjteményeket, és futtathat egy mintaalkalmazást a rövid útmutatók és oktatóanyagok használatával. A mintaalkalmazás azure-fiókra való feliratkozás vagy a hitelkártya használata nélkül is futtatható. [Próbálja ki az Azure Cosmos DB-t egy](https://azure.microsoft.com/try/cosmosdb/) hónapig ingyenesen kínálaz Azure Cosmos DB-nek, amely számos alkalommal megújíthatja fiókját.

* **Azure Cosmos DB emulátor:** Az Azure Cosmos DB emulátor olyan helyi környezetet biztosít, amely fejlesztési célokra emulálja az Azure Cosmos DB szolgáltatást. Emulátor kínálnak költség nélkül, és a nagy hűség a felhőszolgáltatás. Az Azure Cosmos DB emulátor használatával helyileg fejlesztheti és tesztelheti alkalmazásait anélkül, hogy Azure-előfizetést hozna létre, vagy bármilyen költséggel járna. Az alkalmazásokat az emulátor helyi használatával fejlesztheti, mielőtt éles környezetbe lépne. Miután elégedett az alkalmazás az emulátor, átválthat az Azure Cosmos DB-fiók a felhőben, és jelentősen költségmegtakarítást érhet el. Az emulátorról további információt az [Azure Cosmos DB használata fejlesztési és tesztelési](local-emulator.md) cikkért című témakörben talál további részletekért.

## <a name="pricing-with-reserved-capacity"></a>Tarifa foglalt kapacitással

Az Azure Cosmos DB [fenntartott kapacitása](cosmos-db-reserved-capacity.md) segítségével pénzt takaríthat meg, ha egy évig vagy három évig előre fizet az Azure Cosmos DB-erőforrásokért. A költségeket jelentősen csökkentheti egyéves vagy hároméves előzetes kötelezettségvállalásokkal, és a rendszeres árképzéshez képest 20-65% kedvezményt takaríthat meg. Az Azure Cosmos DB fenntartott kapacitása segít csökkenteni a költségeket azáltal, hogy egy évig vagy három évig előre kifizeti a kiosztott átviteli -ot (RU/s), és kedvezményt kap a kiosztott átviteli kapacitásból. 

A fenntartott kapacitás számlázási kedvezményt nyújt, és nincs hatással az Azure Cosmos DB-erőforrások futtatási állapotára. A fenntartott kapacitás következetesen elérhető az összes API-hoz, amely magában foglalja a MongoDB, Cassandra, SQL, Gremlin és az Azure Tables és a világ összes régióját. A fenntartott kapacitásról az [Azure Cosmos DB-erőforrások előre fizetési szolgáltatásában olvashat,](cosmos-db-reserved-capacity.md) és az [Azure Portalon](https://portal.azure.com/)vásárolhat fenntartott kapacitást.

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB-erőforrások költségeinek optimalizálásáról az alábbi cikkekben olvashat bővebben:

* További információ [a fejlesztésre és tesztelésre való optimalizálásról](optimize-dev-test.md)
* További információ [az Azure Cosmos DB-számlájának ismertetéséről](understand-your-bill.md)
* További információ az [átviteli költség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási költség optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási költségek optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések költségének optimalizálásáról](optimize-cost-queries.md)
* További információ a több régióra kiterjedő [Cosmos-fiókok költségeinek optimalizálásáról](optimize-cost-regions.md)
* További információ az [Azure Cosmos DB fenntartott kapacitásáról](cosmos-db-reserved-capacity.md)
* További információ az [Azure Cosmos DB emulátoráról](local-emulator.md)
