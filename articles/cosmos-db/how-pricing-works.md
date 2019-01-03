---
title: Az Azure Cosmos DB díjszabási modell
description: Ez a cikk ismerteti az Azure Cosmos DB-hez, és hogyan egyszerűsíti a cost management és a költségek tervezési díjszabási modelljét.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: rimman
ms.openlocfilehash: 5651e7ee8196eec9d1b1bf20e84ed73e484afb4b
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542396"
---
# <a name="pricing-model-of-azure-cosmos-db"></a>Az Azure Cosmos DB díjszabási modell 

A díjszabási modell az Azure Cosmos DB leegyszerűsíti a cost management és a tervezés. Az Azure Cosmos DB kell fizetnie a kiosztott átviteli sebesség és a felhasznált tárolási.

* **Kiosztott átviteli sebesség**: Kiosztott átviteli sebesség (más néven a fenntartott átviteli sebesség) garantálja a nagy teljesítményű, bármilyen méretben. Megadhatja az átviteli sebesség (RU/s) van szüksége, és az Azure Cosmos DB megjelenítésének szenteli a konfigurált átviteli sebesség biztosításához szükséges erőforrásokat. Ön elszámolása óraszám a maximális kiosztott átviteli sebesség egy adott óra.

   > [!NOTE]
   > A kiosztott átviteli sebesség modell mely dedikált erőforrásokat nyújt a tároló vagy-adatbázishoz, mert akkor kell fizetni a kiosztott átviteli sebesség még akkor is, ha bármilyen számítási feladatot nem lehet futtatni.

* **Felhasznált tároló**: A számlázás és átalánydíjas, az adat- és az indexek adott órában felhasznált tárterület (GB) teljes mennyisége.

Kiosztott átviteli sebesség, mint a megadott [kérelemegység](request-units.md) / másodperc (RU/s), lehetővé teszi, hogy az adatok olvasására vagy írására tárolók vagy adatbázisok. Is [egy adatbázist vagy a tároló üzembe helyezése átviteli sebességet](set-throughput.md). Számítási feladat igényei szerint, bármikor átviteli felfelé és lefelé is méretezheti. Azure Cosmos DB-díjszabás rugalmas, és az átviteli sebességet, amelyet állít be egy adatbázist vagy egy tároló arányos. A minimális átviteli sebesség és tárterület-értékeket és a méretezési csoport lépésekkel biztosíthat az ár és a rugalmasság spektrum széles, akik az összes szegmensek nagy méretű tárolókkal kis léptékű. Minden adatbázis vagy egy tárolót a az egység: 100 Kérelemegység/s, minimum 400 Kérelemegység/s kiosztott átviteli sebesség és a felhasznált GB-ban mért tárterület óránkénti alapon történik. Ellentétben a kiosztott átviteli sebesség a storage használata alapján történik. Azt jelenti nem kell tárolási lefoglalhatja. Ön csak a felhasznált tárterület díjköteles.

További információkért lásd: a [díjszabását ismertető oldalt az Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) és [az Azure Cosmos DB-elszámolások ismertetése](understand-your-bill.md).

Az Azure Cosmos DB a díjszabási modell esetén minden API-k egységes. További tudnivalókért lásd: [hogyan Azure Cosmos DB díjszabási modell az ügyfelek költséghatékony](total-cost-ownership.md). Van egy adatbázis vagy egy tárolót az SLA-k biztosítása szükséges minimális teljesítmény, és növelheti vagy csökkentheti a kiosztott átviteli sebesség $6 minden 100 Kérelemegység/s a.

Jelenleg a minimális szolgáltatás díja az adatbázis és a tárolóalapú átviteli $24/ hó (lásd a [díjszabását ismertető oldalt az Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) legújabb információkért. Ha a számítási feladatok több tárolót használ, azt optimalizálhatók a költségek adatbázis szolgáltatásiszint-átviteli használatával, mert az adatbázis-szintű átviteli lehetővé teszi, hogy rendelkezik egy adatbázis-az átviteli sebességet a tárolók között megosztást tetszőleges számú tárolót. A következő táblázat összefoglalja a kiosztott átviteli sebesség és a különböző entitások költségek:

|**Entitás**  | **Minimális átviteli sebesség és a költség** |**Méretezési csoport lépésekben & költség** |**Hatókör-kiépítés** |
|---------|---------|---------|-------|
|Adatbázis    | 400 Kérelemegység/s ($24/ hó)    | 100 Kérelemegység/másodperc ($6/ hó)   |Átviteli sebesség az adatbázis számára fenntartva, és az adatbázis belüli tárolók által megosztott |
|Tároló     | 400 Kérelemegység/s ($24/ hó)    | 100 Kérelemegység/másodperc ($6/ hó)  |Egy adott tároló számára fenntartott átviteli sebesség |

Amint az előző táblázatban látható, a minimális átviteli sebesség az Azure Cosmos DB elindul, $24 havi díj ellenében. Ha a minimális átviteli sebesség és a méretezési csoport idővel az éles számítási feladatok, a költségek $6/ hó egységekben zökkenőmentesen, arányban nő. A díjszabási modell az Azure Cosmos DB rugalmas és egy zökkenőmentes növekedés vagy csökkenése az ár, kisebbre vagy nagyobbra méretezhetők.

## <a name="try-azure-cosmos-db-for-free"></a>Az Azure Cosmos DB ingyenes kipróbálása 

Az Azure Cosmos DB ingyenes, fejlesztőknek többféle lehetőséget kínál. Ezek a lehetőségek a következők:

* **Ingyenes Azure-fiók**: Az Azure- [ingyenes szint](https://azure.microsoft.com/free/) kínáló 200 USD Azure-kreditet az első 30 nap és a egy korlátozott mennyiség 12 hónapig ingyenesen használhatja a szolgáltatásokat. További információkért lásd az [ingyenes Azure-fiókot](../billing/billing-avoid-charges-free-account.md) ismertető cikket. Az Azure Cosmos DB ingyenes Azure-fiók egy részét képezi. Kifejezetten az Azure Cosmos DB az ingyenes fiók az egész évre 5 GB-os tárolási és a létesített átviteli sebesség 400 kérelemegység kínál. 

* **Próbálja ki ingyenesen az Azure Cosmos DB**: Az Azure Cosmos DB kínál egy időben korlátozott tapasztalattal az Azure Cosmos DB kipróbálása ingyenes fiókok esetében. Hozzon létre egy Azure Cosmos DB-fiók, adatbázis és gyűjtemény létrehozása, és futtassa a mintaalkalmazást a rövid útmutatóink és oktatóanyagaink segítségével. Az Azure-fiók előfizetés vagy a hitelkártya használata nélkül futtathatja a mintaalkalmazást. [Próbálja ki ingyenesen az Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) tetszőleges számú alkalommal kínál az Azure Cosmos DB egy hónapban, és újítsa meg a fiók lehetővé teszi.

* **Az Azure Cosmos DB emulator**: Az Azure Cosmos DB emulator emulálja az Azure Cosmos DB szolgáltatás fejlesztési célokra, helyi környezetet biztosít. Emulátor költségek nélkül, és kiváló minőségben a felhőszolgáltatáshoz érhető el. Azure Cosmos DB emulatort használja, fejlesztése és tesztelése helyileg, az alkalmazások Azure-előfizetés létrehozása vagy szolgáltatásért nélkül. Az emulator használatával helyben előtt telepítés éles környezetben az alkalmazásokat fejleszthet. Ha elégedett az alkalmazás az emulatorban működése, váltson át az Azure Cosmos DB-fiók használatára a felhőben, és jelentős megtakarítást a költségek. Emulátor kapcsolatos további információkért lásd: [Azure Cosmos DB használatával fejlesztési és tesztelési](local-emulator.md) további részleteivel.

## <a name="pricing-with-reserved-capacity"></a>Díjszabási a lefoglalt kapacitás

Az Azure Cosmos DB [fenntartott kapacitás](cosmos-db-reserved-capacity.md) révén pénzt takaríthat meg előre erőforrásokért kellene fizetnie Azure Cosmos DB egyéves vagy hároméves. Jelentősen csökkentheti költségeit egyéves vagy hároméves előzetes kötelezettségvállalás a, és a 20-65 %-os engedményt képest. a normál díjszabás közötti mentése. Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás segít előre fizetnie a kiosztott átviteli sebesség (RU/s) egyéves vagy hároméves időszakra csökkentheti költségeit, és engedményt kap a létesített átviteli sebesség a. 

Lefoglalt kapacitás egy számlázási-os kedvezményt biztosít, és nem befolyásolja az Azure Cosmos DB-erőforrások futási állapotát. Lefoglalt kapacitás érhető el következetesen összes API-t, amely tartalmazza a MongoDB, Cassandra, SQL, a Gremlin és Azure-táblák és az összes régiókban világszerte. További információ a fenntartott kapacitás [előre fizetés fenntartott kapacitással rendelkező Azure Cosmos DB-erőforrásokat a](cosmos-db-reserved-capacity.md) című cikket, és a lefoglalt kapacitás vásárlása a [az Azure portal](https://portal.azure.com/).

## <a name="next-steps"></a>További lépések

További információ az Azure Cosmos DB-erőforrások az alábbi cikkeket a költségek optimalizálása:

* Ismerje meg [optimalizálása fejlesztéshez és teszteléshez](optimize-dev-test.md)
* Tudjon meg többet [az Azure Cosmos DB-elszámolások ismertetése](understand-your-bill.md)
* Tudjon meg többet [átviteli költségek optimalizálása](optimize-cost-throughput.md)
* Tudjon meg többet [tárolási költségek optimalizálása](optimize-cost-storage.md)
* Tudjon meg többet [olvasási és írási a költségek optimalizálása](optimize-cost-reads-writes.md)
* Tudjon meg többet [lekérdezések költségeinek optimalizálása](optimize-cost-queries.md)
* Tudjon meg többet [többrégiós Cosmos-fiókok költségeinek optimalizálása](optimize-cost-regions.md)
* Ismerje meg [Azure Cosmos DB lefoglalt kapacitás](cosmos-db-reserved-capacity.md)
* Ismerje meg [Azure Cosmos DB Emulator](local-emulator.md)
