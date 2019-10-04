---
title: A Azure Cosmos DB díjszabási modellje
description: Ez a cikk ismerteti a Azure Cosmos DB díjszabási modelljét, valamint azt, hogy miként egyszerűsíthető a Cost Management és a Cost-tervezés.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: rimman
ms.openlocfilehash: 43a4b46199277da11d2820a11ec0801c0345e62b
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717604"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Díjszabási modell a Azure Cosmos DB 

A Azure Cosmos DB díjszabási modellje leegyszerűsíti a költséghatékonyságot és a tervezést. A Azure Cosmos DB a kiosztott átviteli sebességért és a felhasznált tárterületért kell fizetnie.

* **Kiépített átviteli sebesség**: A kiépített átviteli sebesség (más néven fenntartott átviteli sebesség) garantálja a nagy teljesítményt bármilyen méretben. Meg kell adnia a szükséges átviteli sebességet (RU/mp), és Azure Cosmos DB a konfigurált átviteli sebesség biztosításához szükséges erőforrásokat. A maximális kiosztott átviteli sebességért óradíjat számolunk fel egy adott órában.

   > [!NOTE]
   > Mivel a kiépített átviteli sebességi modell erőforrásokat rendel a tárolóhoz vagy az adatbázishoz, akkor is a kiosztott átviteli sebességért kell fizetnie, akkor is, ha nem futtat semmilyen munkaterhelést.

* **Felhasznált tárterület**: Az adathoz felhasznált teljes tárterület (GB) és az indexek egy adott órában való kiszámlázása után számítunk fel díjat.

A kiépített átviteli sebesség ( [kérési egység](request-units.md) /másodperc) szerint (ru/s) lehetővé teszi az adatok tárolók vagy adatbázisokba történő olvasását és írását. [Az átviteli sebességet egy adatbázison vagy tárolón](set-throughput.md)is kiépítheti. A számítási feladatok igénye alapján bármikor felgyorsíthatja az átviteli sebességet. A Azure Cosmos DB díjszabása rugalmas, és az adatbázison vagy tárolón konfigurált átviteli sebesség arányos. A minimális átviteli sebesség és a tárolási értékek, valamint a skálázási növekmények a díjszabás és a rugalmasság spektrumának teljes skáláját biztosítják az ügyfelek összes szegmense számára, a kis mérettől a nagy méretű tárolóig. Minden adatbázishoz vagy tárolóhoz óradíjat számolunk fel a 100 RU/s egységben kiosztott átviteli sebességért, amely legalább 400 RU/s, és a GB-ban felhasznált tárterület. A kiépített átviteli sebességtől eltérően a tárterületet a szolgáltatás fogyasztási alapon számlázza. Ez azt eredményezi, hogy nem kell előzetesen lefoglalnia a tárterületet. Csak a felhasznált tárterületért kell fizetnie.

További információkért tekintse meg a [Azure Cosmos db díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/) , és [Ismerkedjen meg a Azure Cosmos db számlával](understand-your-bill.md).

A Azure Cosmos DB díjszabási modellje konzisztens az összes API-ban. További információ: [How Azure Cosmos db díjszabási modell költséghatékony az ügyfelek számára](total-cost-ownership.md). Egy adatbázison vagy tárolón legalább egy adatátviteli sebesség szükséges az SLA-k biztosításához, és a kiépített átviteli sebesség $6-ig növelhető és csökkenthető minden 100 RU/s esetében.

Jelenleg az adatbázis és a tároló-alapú átviteli sebesség minimális díja $24/hó (a legfrissebb információk a [Azure Cosmos db díjszabási oldalán](https://azure.microsoft.com/pricing/details/cosmos-db/) olvashatók. Ha a számítási feladat több tárolót használ, az adatbázis-szintű átviteli sebességre is optimalizálható, mert az adatbázis-szintű átviteli sebesség lehetővé teszi, hogy a tárolók között tetszőleges számú tárolót osszon meg egy adatbázisban. A következő táblázat összefoglalja a kiosztott átviteli sebességet és a különböző entitások költségeit:

|**Entitás**  | **Minimális átviteli sebesség &i díja** |**Méretezési növekmények & Cost** |**Kiépítési hatókör** |
|---------|---------|---------|-------|
|Adatbázis    | 400 RU/s ($ 24/hó)    | 100 RU/s ($ 6/hó)   |Az átviteli sebesség az adatbázis számára van fenntartva, és a tárolók az adatbázison belül vannak megosztva |
|Tároló     | 400 RU/s ($ 24/hó)    | 100 RU/s ($ 6/hó)  |Az átviteli sebesség egy adott tároló számára van fenntartva |

Ahogy az előző táblázatban is látható, a Azure Cosmos DB minimális átviteli sebessége $24/hó. Ha a minimális átviteli sebesség és az idő múlásával növeli az éles számítási feladatok támogatását, a költségek a $6/hó-onként növekednek. A Azure Cosmos DB díjszabási modellje rugalmas, és a vertikális fel-vagy leskálázás során az ár zökkenőmentesen növekszik vagy csökken.

## <a name="try-azure-cosmos-db-for-free"></a>Az Azure Cosmos DB ingyenes kipróbálása 

Azure Cosmos DB számos lehetőséget kínál a fejlesztők számára ingyenesen. Ezek a lehetőségek a következők:

* **Ingyenes Azure-fiók**: Az Azure egy [ingyenes szintet](https://azure.microsoft.com/free/) kínál, amely az első 30 nap során $200 az Azure-kreditekben, és korlátozott mennyiségű ingyenes szolgáltatást biztosít 12 hónapig. További információkért lásd az [ingyenes Azure-fiókot](../billing/billing-avoid-charges-free-account.md) ismertető cikket. Azure Cosmos DB az ingyenes Azure-fiók része. Kifejezetten a Azure Cosmos DB esetében ez az ingyenes fiók 5 GB tárhelyet és 400 RUs-t biztosít az egész évre kiépített átviteli sebességhez. 

* **Próbálja ki Azure Cosmos db**ingyen: A Azure Cosmos DB az ingyenes fiókok kipróbálása Azure Cosmos DB használatával biztosít időt. Létrehozhat egy Azure Cosmos DB fiókot, létrehozhat adatbázisokat és gyűjteményeket, és futtathat egy minta alkalmazást a rövid útmutatók és oktatóanyagok segítségével. A minta alkalmazást futtathatja egy Azure-fiókra való feliratkozás vagy a bankkártya használata nélkül is. [Próbálja ki a Azure Cosmos db ingyenes](https://azure.microsoft.com/try/cosmosdb/) ajánlatokat egy hónapig Azure Cosmos db a fiókját tetszőleges számú alkalommal megújíthatja.

* **Azure Cosmos db emulátor**: A Azure Cosmos DB Emulator egy helyi környezetet biztosít, amely a Azure Cosmos DB szolgáltatást fejlesztési célokra emulálja. Az emulátor díjmentesen és magas szintű megbízhatósággal érhető el a felhőalapú szolgáltatás számára. Az Azure Cosmos DB Emulator használatával helyileg fejlesztheti és tesztelheti alkalmazásait anélkül, hogy Azure-előfizetést hozna létre, vagy költségekkel kellene számolni. Alkalmazásait az emulátor helyi használatával fejlesztheti az éles környezetbe való bevezetésük előtt. Miután elégedett az alkalmazás működésével az emulátoron, átválthat a felhőben lévő Azure Cosmos DB-fiók használatára, és jelentősen megtakaríthatja a költségeket. További információ az emulátorról: [Azure Cosmos db használata fejlesztési és tesztelési](local-emulator.md) célból a további részletekért.

## <a name="pricing-with-reserved-capacity"></a>Díjszabás a fenntartott kapacitással

Azure Cosmos DB [fenntartott kapacitással](cosmos-db-reserved-capacity.md) pénzt takaríthat meg, ha egy vagy három évig Azure Cosmos db erőforrásokra előre fizet. Jelentős mértékben csökkentheti a költségeket egy-vagy hároméves előzetes kötelezettségvállalásokkal, és a normál díjszabáshoz képest 20-65%-os kedvezményt takaríthat meg. Azure Cosmos DB fenntartott kapacitással csökkentheti a költségeket a kiosztott átviteli sebesség (RU/s) előre fizetve egy évig vagy három évig, és kedvezményt kap a kiosztott átviteli sebességhez. 

A fenntartott kapacitás számlázási kedvezményt biztosít, és nincs hatással a Azure Cosmos DB erőforrásainak futtatókörnyezeti állapotára. A fenntartott kapacitás minden API-hoz elérhető, amely magában foglalja az MongoDB, a Cassandra, az SQL, a Gremlin és az Azure-táblákat, valamint a világ minden régióját. A fenntartott kapacitást előre is megtudhatja, [ha Azure Cosmos db erőforrásokhoz fenntartott](cosmos-db-reserved-capacity.md) kapacitással rendelkezik, és fenntartott kapacitást vásárol a [Azure Portal](https://portal.azure.com/).

## <a name="next-steps"></a>További lépések

A Azure Cosmos DB-erőforrások költségeinek optimalizálásáról a következő cikkekben tájékozódhat:

* Tudnivalók a [fejlesztés és a tesztelés optimalizálásáról](optimize-dev-test.md)
* További információ [a Azure Cosmos db-számla megismeréséről](understand-your-bill.md)
* További információ az [átviteli sebesség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási díjak optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási díjak optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések díjszabásának optimalizálásáról](optimize-cost-queries.md)
* További információ [a több régióból álló Cosmos-fiókok díjainak optimalizálásáról](optimize-cost-regions.md)
* További információ a [Azure Cosmos db fenntartott kapacitásról](cosmos-db-reserved-capacity.md)
* Tudnivalók a [Azure Cosmos db emulatorról](local-emulator.md)
