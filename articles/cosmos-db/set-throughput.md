---
title: Az Azure Cosmos-tárolók és adatbázisok kiépítése átviteli
description: Ismerje meg, hogyan állíthatja be a kiosztott átviteli sebesség az Azure Cosmos-tárolók és adatbázisok.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: andrl
ms.openlocfilehash: 8335a235de708227136400f3af8fa7b4d0a52e29
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520904"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Átviteli sebesség kiosztása tárolókra és adatbázisokra

Egy Azure Cosmos database tárolók több felügyeleti egység. Egy adatbázis sémafüggetlen tárolók készlete áll. Az Azure Cosmos-tárolókat méretezhetőséget biztosít az átviteli sebesség és a tárolási egység. Egy tároló között egy Azure-régión belüli gépek vízszintesen particionált, és az Azure Cosmos-fiókhoz társított összes Azure-régió között oszlanak meg.

Az Azure Cosmos DB két granularitással, konfigurálhatja a átviteli sebesség:
 
- Azure Cosmos-tárolók
- Az Azure Cosmos Database.

## <a name="set-throughput-on-a-container"></a>Teljesítmény beállítása egy tárolón  

A az Azure Cosmos-tárolókat a létesített átviteli sebesség kizárólag a tároló számára van fenntartva. A tároló kap a kiosztott átviteli sebesség folyamatosan. Egy tároló kiosztott átviteli felelősséggel szavatolják. Átviteli sebesség beállítása egy tárolón: [üzembe helyezése egy Azure Cosmos-tároló átviteli sebességet](how-to-provision-container-throughput.md).

Egy tároló kiosztott átviteli kapacitás beállítását a széles körben használt beállítás. Egy tároló átviteli sebesség rugalmasan skálázhatja a kérelemegység (RU-k) használatával átviteli bármekkora mennyiségű kiépítésével. Azonban nem külön-külön adhat az átviteli logikai partíciók. 

Az egyes logikai partíciók futó számítási feladat több, mint az, hogy az adott logikai partíció lett lefoglalva átviteli sebességet használ fel, ha a műveletek beolvasása sebessége korlátozott. A sebességhatárolt történik, ha növelni az átviteli sebesség a teljes tárolót, vagy ismételje meg az operations. A particionálás további információkért lásd: [logikai partíció](partition-data.md).

Azt javasoljuk, hogy konfigurálása átviteli tároló részletességgel Ha azt szeretné, hogy a tároló garantált teljesítményt.

Egy Azure Cosmos-tároló kiosztott átviteli sebesség egyenletesen legyen elosztva a tároló összes logikai partíciót. Egy vagy több tároló logikai partíciót egy fizikai partíciók által üzemeltetett, mert a fizikai partíciók kizárólag a tároló tartozik, és a tárolóban kiosztott átviteli támogatja. 

A következő kép bemutatja, hogyan a fizikai partíciók futtat egy vagy több tároló logikai partíció:

![Fizikai partíciónként](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Egy adatbázis teljesítmény beállítása

Amikor üzembe helyez egy Azure Cosmos database átviteli sebességet, átviteli közösen használja az összes tárolót az adatbázisban. Egy kivétel az, ha meghatározott a kiosztott átviteli sebesség az adott tárolókon. Az adatbázis átviteli sebességet a tárolók között megosztása hasonlatos számítógépfürtökön adatbázis üzemeltetéséhez. Adatbázison belüli összes tárolók a gépen elérhető erőforrásokon osztozik, mivel természetesen nem kap kiszámítható teljesítményt bármely adott tárolón. Átviteli sebesség, adatbázis beállítása: [konfigurálása az Azure Cosmos-adatbázis a kiosztott átviteli sebesség](how-to-provision-database-throughput.md).

Beállítás átviteli sebességet egy Azure Cosmos database garantálja, hogy megkapja a kiosztott átviteli sebesség folyamatosan. Mivel az adatbázison belül az összes tárolót a kiosztott átviteli sebesség, Azure Cosmos DB nem biztosít bármely kiszámítható teljesítményt garantálja az adatbázis egy adott tárolóhoz. Az átviteli sebességet egy adott tároló fogadhatják része az adott nyelvtől függ:

* A tárolók száma.
* A kiválasztott partíciókulcsok különböző tárolókhoz.
* A tárolók különböző logikai partíciók között a számítási feladatok eloszlása. 

Azt javasoljuk, hogy konfigurálása átviteli sebesség, adatbázis, ha meg szeretné osztani az átviteli sebességet a több tárolón, de nem szeretné az átviteli sebesség dedikált bármely adott tárolóhoz. 

A következő példák azt mutatják be, ahol azt rendelkezik előnyben részesített oszthatnak ki átviteli kapacitásokat az adatbázis szintjén:

* Az adatbázis kiosztott átviteli sebesség megosztása tárolók több hasznos egy több-bérlős alkalmazásban. Minden felhasználó egy különálló Azure Cosmos-tárolót is képviseli.

* Az adatbázis kiosztott átviteli sebesség megosztása több tárolók akkor hasznos, ha telepít át, egy NoSQL-adatbázis, például MongoDB vagy Cassandra, a virtuális gépek vagy a helyszíni fizikai kiszolgálóknak az Azure Cosmos DB-fürt által futtatott. Úgy gondolja, hogy a kiosztott átviteli sebesség a konfigurált az Azure Cosmos Database logikai egyenértékűként, de több költséghatékony és rugalmas, a számítási kapacitás, a mongodb-hez vagy a Cassandra-fürt a.  

A partíciókulccsal rendelkező összes tárolót hozott létre egy adatbázist, a kiosztott átviteli sebesség belül kell létrehozni. Megadott idő bármikor az adatbázison belüli egy tároló kiosztott átviteli legyen elosztva a tároló összes logikai partíciót. Tárolók, amelyek egy adatbázist a kiosztott átviteli sebesség, ha külön-külön nem alkalmazhat az átviteli sebességet egy adott tároló és a egy logikai partíciót. 

A logikai partíció terhelése nagyobb átviteli sebesség az adott logikai partíció számára lefoglalt használ fel, ha a műveletekre sebessége korlátozott. A sebességhatárolt történik, ha növelni az átviteli sebesség a teljes tárolót, vagy ismételje meg az operations. A particionálás további információkért lásd: [logikai partíció](partition-data.md).

Egyetlen fizikai partíciók több logikai partíciót, amelyek megosztása a adatbázishoz létesített átviteli sebesség lehet üzemeltetni. Egy adott tároló egy logikai partíció mindig hatókörét fizikai partíción belül, miközben "L" logikai partíció között, amelyek a kiosztott átviteli sebesség az adatbázis "C" tárolók hozzárendelve, és "R" fizikai partíciók üzemeltethetők. 

A következő kép bemutatja, hogyan fizikai partíciók egy vagy több logikai partíciót, egy adatbázison belül különböző tárolók tartozó üzemeltetésére is képes:

![Fizikai partíciónként](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Az adatbázis és a egy tárolót a teljesítmény beállítása

A két modell kombinálhatók. Az adatbázis és a tároló üzembe helyezési átviteli használata engedélyezett. Az alábbi példa bemutatja, hogyan helyezhet üzembe egy Azure Cosmos database és a egy tárolót az átviteli sebesség:

* Létrehozhat egy Azure Cosmos database Z nevű "K" fenntartott átviteli sebesség. 
* Ezután hozzon létre öt tárolók neve A, B, C, D és az adatbázison belül E.
* Explicit módon lehet konfigurálni a kiosztott átviteli sebesség RUs "P" a b nevű tárolót
* A "K" fenntartott átviteli sebesség közösen használja a négy tárolók A, C, D és E. Átviteli sebesség pontos mennyisége érhető el a, C, D, E változik. Nincsenek nem SLA-k minden egyes tároló átviteli sebességet.
* A "P" fenntartott átviteli sebesség lekérdezése mindig garantáltan a B nevű tárolót. SLA-k alapját.

## <a name="update-throughput-on-a-database-or-a-container"></a>Átviteli sebességet egy adatbázist vagy egy tároló frissítése

Miután létrehozott egy Azure Cosmos-tároló vagy egy adatbázist, akkor is frissítheti a kiosztott átviteli sebesség. Nincs korlátozva az a maximális kiosztott átviteli sebesség, amely az adatbázis vagy a tároló lehet konfigurálni. A minimális kiosztott átviteli sebesség a következő tényezőktől függ: 

* Minden eddiginél a tárolóban tárolt adatok maximális mérete
* A maximális átviteli sebesség, a tároló minden eddiginél megadó
* Az Azure Cosmos-tárolók, így minden eddiginél létrehozott megosztott adattovábbítási kapacitással rendelkező adatbázis maximális számát. 

Az SDK-k használatával programozott módon lekérni a tárolók és a egy adatbázist a minimális átviteli sebesség, vagy megtekintheti az értékét az Azure Portalon. Ha a .NET SDK használatával a [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) módszer lehetővé teszi a kiosztott átviteli sebesség értékét skálázását. A Java SDK használata esetén a [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) módszer lehetővé teszi a kiosztott átviteli sebesség értékét skálázását. 

Ha a .NET SDK használatával a [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) módszer lehetővé teszi, hogy a minimális átviteli sebesség, tárolók és a egy adatbázis beolvasása. 

A kiosztott átviteli sebesség, tárolók és a egy adatbázis bármikor skálázhatja. A skálázási művelet 4 órán keresztül tétlen időszak után is futtathatja. A tétlen időszak számít, ha az idő időszak, amikor nincs ajánlat a csere művelet (amely tartalmazza a felfelé és lefelé méretezési) egy tároló vagy az adatbázis nem létezik. 

## <a name="comparison-of-models"></a>Modellek összehasonlítása

|**Kvóta**  |**Átviteli sebesség kiosztott részéért, adatbázis**  |**Egy tároló kiosztott átviteli sebesség**|
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

