---
title: Az Azure Cosmos-tárolók és adatbázisok kiépítése átviteli
description: Ismerje meg, hogyan állíthatja be a kiosztott átviteli sebesség az Azure Cosmos-tárolók és adatbázisok.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 1f8bec6fbf0bce9a3ac272231058a96a5d9e84cc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762497"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Átviteli sebesség kiosztása tárolókra és adatbázisokra

Egy Azure Cosmos database tárolók több felügyeleti egység. Egy adatbázis sémafüggetlen tárolók készlete áll. Az Azure Cosmos-tárolókat méretezhetőséget biztosít az átviteli sebesség és a tárolási egység. Egy tároló között egy Azure-régión belüli gépek vízszintesen particionált, és az Azure Cosmos-fiókhoz társított összes Azure-régió között oszlanak meg.

Az Azure Cosmos DB átviteli sebességet két granularitással is kioszthatja:
 
- Azure Cosmos-tárolók
- Az Azure Cosmos Database.

## <a name="set-throughput-on-a-container"></a>Teljesítmény beállítása egy tárolón  

A az Azure Cosmos-tárolókat a létesített átviteli sebesség kizárólag a tároló van fenntartva. A tároló kap a kiosztott átviteli sebesség folyamatosan. Egy tároló kiosztott átviteli felelősséggel szavatolják. Egy tároló átviteli konfigurálása kapcsolatban lásd: [üzembe helyezése egy Azure Cosmos-tároló átviteli sebességet](how-to-provision-container-throughput.md).

A beállítás egy tároló kiosztott átviteli sebesség, a leggyakrabban használt lehetőség. Rugalmasan méretezheti egy tároló átviteli sebesség az átviteli bármekkora mennyiségű kiépítésével [kérelemegység (RU)](request-units.md). 

Egy Azure Cosmos-tároló kiosztott átviteli sebesség egyenletesen legyen elosztva a tároló összes logikai partíciót. Az átviteli logikai partíciók esetén nem külön-külön adható meg. Egy vagy több tároló logikai partíciót egy fizikai partíciók által üzemeltetett, mert a fizikai partíciók kizárólag a tároló tartozik, és a tárolóban kiosztott átviteli támogatja. 

Az egyes logikai partíciók futó számítási feladat több, mint az átviteli sebesség az adott logikai partíció lett lefoglalva használ fel, ha a műveletek beolvasása sebessége korlátozott. A sebességhatárolt történik, ha a teljes tárolóhoz a kiosztott átviteli sebesség növelése, vagy ismételje meg az operations. A particionálás további információkért lásd: [logikai partíció](partition-data.md).

Azt javasoljuk, hogy konfigurálása átviteli tároló részletességgel Ha azt szeretné, hogy a tároló garantált teljesítményt.

A következő kép bemutatja, hogyan a fizikai partíciók futtat egy vagy több tároló logikai partíció:

![Fizikai partíciónként](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Egy adatbázis teljesítmény beállítása

Amikor üzembe helyez egy Azure Cosmos database átviteli sebességet, átviteli közösen használja az összes tárolót az adatbázisban. Egy kivétel az, ha az adatbázis konkrét tárolók a kiosztott átviteli sebesség megadott. Az adatbázisszintű kiosztott átviteli sebesség a tárolók közötti megosztása hasonlatos számítógépfürtökön adatbázis üzemeltetéséhez. Adatbázison belüli összes tárolók a gépen elérhető erőforrásokon osztozik, mivel természetesen nem kap kiszámítható teljesítményt bármely adott tárolón. Ismerje meg, hogyan konfigurálhatja a kiosztott átviteli sebesség, adatbázis, lásd: [konfigurálása az Azure Cosmos-adatbázis a kiosztott átviteli sebesség](how-to-provision-database-throughput.md).

Beállítás átviteli sebességet egy Azure Cosmos database garantálja, hogy megkapja a kiosztott átviteli sebesség az adatbázishoz tartozó folyamatosan. Mivel az adatbázison belül az összes tárolót a kiosztott átviteli sebesség, Azure Cosmos DB nem biztosít bármely kiszámítható teljesítményt garantálja az adatbázis egy adott tárolóhoz. Az átviteli sebességet egy adott tároló fogadhatják része az adott nyelvtől függ:

* A tárolók száma.
* A kiválasztott partíciókulcsok különböző tárolókhoz.
* A tárolók különböző logikai partíciók között a számítási feladatok eloszlása. 

Azt javasoljuk, hogy konfigurálása átviteli sebesség, adatbázis, ha meg szeretné osztani az átviteli sebességet a több tárolón, de nem szeretné az átviteli sebesség dedikált bármely adott tárolóhoz. 

A következő példák azt mutatják be, ahol azt rendelkezik előnyben részesített oszthatnak ki átviteli kapacitásokat az adatbázis szintjén:

* Az adatbázis kiosztott átviteli sebesség megosztása tárolók több hasznos egy több-bérlős alkalmazásban. Minden felhasználó egy különálló Azure Cosmos-tárolót is képviseli.

* Az adatbázis kiosztott átviteli sebesség megosztása több tárolók akkor hasznos, ha telepít át, egy NoSQL-adatbázis, például MongoDB vagy Cassandra, a virtuális gépek vagy a helyszíni fizikai kiszolgálóknak az Azure Cosmos DB-fürt által futtatott. Úgy gondolja, hogy a kiosztott átviteli sebesség a konfigurált az Azure Cosmos Database logikai egyenértékűként, de több költséghatékony és rugalmas, a számítási kapacitás, a mongodb-hez vagy a Cassandra-fürt a.  

Az összes tárolót hozott létre egy adatbázist, a kiosztott átviteli sebesség belül kell létrehozni egy [partíciókulcs](partition-data.md). Megadott idő bármikor az adatbázison belüli egy tároló kiosztott átviteli legyen elosztva a tároló összes logikai partíciót. Tárolók, amelyek megosztása a kiosztott átviteli sebesség, az adatbázis konfigurálva van, ha külön-külön nem alkalmazhat az átviteli sebességet egy adott tároló és a egy logikai partíciót. 

A logikai partíció terhelése nagyobb átviteli sebesség az adott logikai partíció számára lefoglalt használ fel, ha a műveletekre sebessége korlátozott. A sebességhatárolt akkor fordul elő, amikor növeléséhez a teljes adatbázis, vagy ismételje meg a műveleteket. A particionálás további információkért lásd: [logikai partíció](partition-data.md).

Több logikai partíció, amely különböző tárolók, amelyek megosztása a adatbázishoz létesített átviteli sebesség tartozik egy fizikai partíció lehet üzemeltetni. Amíg a tároló egy logikai partíció mindig hatókörét a fizikai partíciókon belül *"L"* logikai partíció között *"C"* , amelyek a kiosztott átviteli sebesség az adatbázis-tárolók hozzárendelve, és lévő üzemeltetett *"R"* fizikai partíciókra. 

A következő kép bemutatja, hogyan fizikai partíciók egy vagy több logikai partíciót, egy adatbázison belül különböző tárolók tartozó üzemeltetésére is képes:

![Fizikai partíciónként](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Az adatbázis és a egy tárolót a teljesítmény beállítása

A két modell kombinálhatók. Az adatbázis és a tároló üzembe helyezési átviteli használata engedélyezett. Az alábbi példa bemutatja, hogyan helyezhet üzembe egy Azure Cosmos database és a egy tárolót az átviteli sebesség:

* Létrehozhat egy Azure Cosmos database nevű *Z* az átviteli sebesség *"K"* RUs. 
* Ezután hozzon létre öt tárolók nevű *A*, *B*, *C*, *D*, és *E* az adatbázison belül.
* Explicit módon konfigurálható *"P"* nevű tárolót a kiosztott átviteli sebesség a RUs *B*.
* A *"K"* fenntartott átviteli sebesség közösen használja a négy tárolók *A*, *C*, *D*, és *E*. Az elérhető átviteli sebesség pontos mennyisége *A*, *C*, *D*, vagy *E* változik. Nincsenek nem SLA-k minden egyes tároló átviteli sebességet.
* A tároló nevű *B* első garantáltan a *"P"* fenntartott átviteli sebesség folyamatosan. SLA-k alapját.

## <a name="update-throughput-on-a-database-or-a-container"></a>Átviteli sebességet egy adatbázist vagy egy tároló frissítése

Miután létrehozott egy Azure Cosmos-tároló vagy egy adatbázist, akkor is frissítheti a kiosztott átviteli sebesség. Nincs korlátozva az a maximális kiosztott átviteli sebesség, amely az adatbázis vagy a tároló lehet konfigurálni. A minimális kiosztott átviteli sebesség a következő tényezőktől függ: 

* Minden eddiginél a tárolóban tárolt adatok maximális mérete
* A maximális átviteli sebesség, a tároló minden eddiginél megadó
* Az Azure Cosmos-tárolók, így minden eddiginél létrehozott megosztott adattovábbítási kapacitással rendelkező adatbázis maximális számát. 

Az SDK-k használatával programozott módon lekérni a tárolók és a egy adatbázist a minimális átviteli sebesség, vagy megtekintheti az értékét az Azure Portalon. Ha a .NET SDK használatával a [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) módszer lehetővé teszi a kiosztott átviteli sebesség értékét skálázását. A Java SDK használata esetén a [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) módszer lehetővé teszi a kiosztott átviteli sebesség értékét skálázását. 

Ha a .NET SDK használatával a [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) módszer lehetővé teszi, hogy a minimális átviteli sebesség, tárolók és a egy adatbázis beolvasása. 

A kiosztott átviteli sebesség, tárolók és a egy adatbázis bármikor skálázhatja. A skálázási művelet 4 órán keresztül tétlen időszak után is futtathatja. A tétlen időszak számít, ha az idő időszak, amikor nincs ajánlat a csere művelet (amely tartalmazza a felfelé és lefelé méretezési) egy tároló vagy az adatbázis nem létezik. 

## <a name="comparison-of-models"></a>Modellek összehasonlítása

|**A paraméter**  |**Átviteli sebesség kiosztott részéért, adatbázis**  |**Egy tároló kiosztott átviteli sebesség**|
|---------|---------|---------|
|Minimális kérelemegység |400-as (után az első négy tárolók, a tárolók további használatához az minimum 100 kérelemegység / másodperc.) |400|
|Minimális RUs tárolónként|100|400|
|1 GB-os tárhelyet felhasználásához szükséges minimális kérelemegység|40|40|
|Maximális kérelemegység|Korlátlan, az adatbázison.|Korlátlan, a tárolón.|
|Kérelemegység hozzárendelt vagy elérhető egy adott tárolóba|Nincs garancia. Egy adott tárolóhoz rendelt RUs tulajdonságainak függenek. Tulajdonságok lehet a kiválasztott partíciókulcsok a tárolók, amelyek az átviteli sebességet, a számítási feladatok és tárolók száma eloszlása. |A tároló konfigurált fenntartott egységek kizárólag a tároló számára vannak fenntartva.|
|Egy tároló maximális tárterülete|Korlátlan számú.|Korlátlan számú.|
|Egy adott tároló logikai partíció egységenkénti maximális adatátviteli sebesség|10 ezer kérelemegység|10 ezer kérelemegység|
|Maximális tárterület (adatok + index) egy adott tároló logikai partíciónként|10 GB|10 GB|

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [logikai partíció](partition-data.md).
* Ismerje meg, hogyan [üzembe helyezése egy Azure Cosmos-tároló átviteli sebességet](how-to-provision-container-throughput.md).
* Ismerje meg, hogyan [üzembe helyezése egy Azure Cosmos database átviteli sebességet](how-to-provision-database-throughput.md).

