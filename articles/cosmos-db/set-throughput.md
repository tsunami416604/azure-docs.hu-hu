---
title: Az Azure Cosmos DB üzembe helyezése átviteli
description: Ismerje meg, hogyan állíthatja be a kiosztott átviteli sebesség az Azure Cosmos DB-tárolók és adatbázisok.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: andrl
ms.openlocfilehash: 3e992dd8ab24e4e60b81c6565ea4ec3971a9336b
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036422"
---
# <a name="provision-throughput-on-azure-cosmos-containers-and-databases"></a>Az Azure Cosmos-tárolók és adatbázisok kiépítése átviteli

Egy Azure Cosmos database tárolók több felügyeleti egység. Egy adatbázis sémafüggetlen tárolók készlete áll. Az Azure Cosmos-tárolókat méretezhetőséget biztosít az átviteli sebesség és a tárolási egység. Egy tároló között egy Azure-régión belüli gépek vízszintesen particionált, és az Azure Cosmos-fiókhoz társított összes Azure-régió között oszlanak meg.

Az Azure Cosmos DB teszi lehetővé a következő két granularitással – a átviteli sebesség konfigurálásához **tárolók az Azure Cosmos** és **Azure Cosmos-adatbázis**.

## <a name="setting-throughput-on-a-container"></a>A beállítás átviteli sebességet egy tárolón  

A az Azure Cosmos-tárolókat a létesített átviteli sebesség kizárólag a tároló számára van fenntartva. A tároló kap a kiosztott átviteli sebesség folyamatosan. Egy tároló kiosztott átviteli felelősséggel szavatolják. Átviteli sebesség beállítása egy tárolón: [hogyan helyezhet üzembe egy Azure Cosmos-tároló átviteli sebességet](how-to-provision-container-throughput.md).

Egy tároló kiosztott átviteli kapacitás beállítását a széles körben használt beállítás. Rugalmasan méretezheti egy tároló átviteli sebesség (RU) átviteli bármekkora mennyiségű kiépítésével, amíg az átviteli logikai partíció(k) esetében külön-külön adható meg. Az egyes logikai partíciók futó számítási feladat több, mint az, hogy az adott logikai partíció lett lefoglalva átviteli sebességet használ fel, ha a műveletek sebessége korlátozott fog kapni. A sebességhatárolt történik, ha növelni az átviteli sebesség a teljes tárolót, vagy próbálja megismételni a műveletet. A particionálás további információkért lásd: [logikai partíció](partition-data.md).

Ajánlott úgy beállítani, hogy átviteli tároló részletességgel Ha azt szeretné, hogy a tároló garantált teljesítményt.

Egy Azure Cosmos-tároló kiosztott átviteli sebesség egyenletesen legyen elosztva a tároló összes logikai partíciót. Egy vagy több tároló logikai partíciót egy fizikai partíciók által üzemeltetett, mivel a fizikai partíciók kizárólag a tároló tartozik, és a tárolóban kiosztott átviteli támogatja. A következő kép bemutatja, hogyan a fizikai partíciók futtat egy vagy több tároló logikai partíció:

![Fizikai partíciónként](./media/set-throughput/resource-partition.png)

## <a name="setting-throughput-on-a-database"></a>A beállítás átviteli sebességet egy adatbázist

Amikor üzembe helyez egy Azure Cosmos database átviteli sebességet, átviteli közösen használja az összes tárolót az adatbázisban, kivéve, ha az adott tárolók a kiosztott átviteli sebesség megadása. Az adatbázis átviteli sebességet a tárolók között megosztása hasonlatos számítógépfürtökön adatbázis üzemeltetéséhez. Adatbázison belüli összes tárolók a gépen elérhető erőforrásokon osztozik, mivel természetesen nem kap kiszámítható teljesítményt bármely adott tárolón. Átviteli sebesség, adatbázis beállítása: [kiosztott átviteli sebesség konfigurálása egy Azure Cosmos database](how-to-provision-database-throughput.md).

Beállítás átviteli sebességet egy Azure Cosmos database garantálja, hogy megkapja a kiosztott átviteli sebesség folyamatosan. Minden tárolók belül az adatbázis-megosztás a kiosztott átviteli sebesség, mivel az Azure Cosmos DB nem biztosít bármely kiszámítható teljesítményt garantálja az adatbázis egy adott tárolóhoz. Az átviteli sebességet egy adott tároló fogadhatják része az adott nyelvtől függ:

* A tárolók száma
* A kiválasztott partíciókulcsok különböző tárolókhoz és
* A tárolók különböző logikai partíciók között a számítási feladatok eloszlása. 

Ajánlott konfigurálása átviteli sebesség, adatbázis, ha meg szeretné osztani az átviteli sebességet a több tárolón, de nem szeretné, hogy az átviteli sebesség dedikált bármely adott tárolóhoz. Alábbiakban bemutatunk néhány példát, ahol a szolgáltatás kiépítése átviteli sebesség az adatbázis szintjén az előnyben részesített:

* Az adatbázis kiosztott átviteli sebesség megosztása tárolók több hasznos egy több-bérlős alkalmazást. Minden felhasználó egy különálló Azure Cosmos-tárolót is képviseli.

* Az adatbázis kiosztott átviteli sebesség megosztása több tárolók akkor hasznos, ha telepít át egy NoSQL-adatbázis (például a MongoDB, a Cassandra) üzemeltetett virtuális gépek, illetve a helyszíni fizikai kiszolgálóknak az Azure Cosmos DB. A kiosztott átviteli logikai megfelelője (de több költséghatékony és rugalmas), valamint a számítási kapacitás, a mongodb-hez vagy a Cassandra-fürt az Azure Cosmos Database konfigurált is felfoghatók.  

A partíciókulccsal rendelkező összes tárolót hozott létre egy adatbázist, a kiosztott átviteli sebesség belül kell létrehozni. Megadott idő bármikor az adatbázison belüli egy tároló kiosztott átviteli legyen elosztva a tároló összes logikai partíciót. Tárolók, megosztás, adatbázis kiosztott átviteli sebesség, ha külön-külön nem alkalmazhat az átviteli sebességet egy adott tároló és a egy logikai partíciót. A logikai partíció terhelése nagyobb átviteli sebesség az adott logikai partíció számára lefoglalt használ fel, a műveletek sebessége korlátozott lesz. A sebességhatárolt történik, ha növelni az átviteli sebesség a teljes tárolót, vagy próbálja megismételni a műveletet. A particionálás további információkért lásd: [logikai partíció](partition-data.md).

A adatbázishoz létesített átviteli sebesség megosztása több logikai partíciót egy egyetlen fizikai partícióján lehet üzemeltetni. Egy adott tároló egy logikai partíció mindig hatókörét fizikai partíción belül, miközben "L" a "C" tárolók a kiosztott átviteli sebesség adatbázis megosztását több logikai partíció hozzárendelve, és "R" fizikai partíciók üzemeltethetők. A következő kép bemutatja, hogyan fizikai partíciók egy vagy több logikai partíciót, egy adatbázison belül különböző tárolók tartozó üzemeltetésére is képes:

![Fizikai partíciónként](./media/set-throughput/resource-partition2.png)

## <a name="setting-throughput-on-a-database-and-a-container"></a>Az adatbázis és a egy tároló átviteli sebesség beállítása

A két modell kombinálhatja, adatbázis és a tároló átviteli kiépítés engedélyezett. Az alábbi példa bemutatja, hogyan helyezhet üzembe egy Azure Cosmos database és a egy tárolót az átviteli sebesség:

* Létrehozhat egy Azure Cosmos database "Z" nevű "K" fenntartott átviteli sebesség. 
* Ezután hozzon létre öt tárolók neve A, B, C, D és az adatbázison belül E.
* Explicit módon konfigurálhatja a "B" tároló "P" RUs a kiosztott átviteli sebesség.
* A "K" fenntartott átviteli sebesség közösen használja a négy tárolók – A, C, D és E. Átviteli sebesség pontos mennyisége érhető el a, C, D, E eltérőek, és nincsenek nincs SLA-k minden egyes tároló átviteli sebességet.
* A tároló "B" a "P" fenntartott átviteli sebesség lekérdezése mindig garantáltan, és azt szavatolják.

## <a name="comparison-of-models"></a>Modellek összehasonlítása

|**Kvóta**  |**Átviteli sebesség kiosztott részéért, adatbázis**  |**Egy tároló kiosztott átviteli sebesség**|
|---------|---------|---------|
|Minimális kérelemegység |400-as (után az első négy tárolók, a tárolók további használatához az minimum 100 Kérelemegység/s.) |400|
|Minimális RUs tárolónként|100|400|
|1 GB-os tárhelyet felhasználásához szükséges minimális kérelemegység|40|40|
|Maximális kérelemegység|Korlátlan, az adatbázis|Korlátlan, a tárolóban|
|Fenntartott egységek hozzárendelt/érhető el egy adott tárolóba|Nincs garancia. Egy adott tárolóhoz rendelt RUs a tulajdonságait, például - tárolók, amelyek az átviteli sebesség, a számítási feladatok eloszlása, a tárolók száma partíciókulcsok többféle függenek. |A tároló konfigurált fenntartott egységek kizárólag a tároló számára vannak fenntartva.|
|Egy tároló maximális tárterülete|Korlátlan|Korlátlan|
|Egy adott tároló logikai partíció egységenkénti maximális adatátviteli sebesség|10 ezer kérelemegység|10 ezer kérelemegység|
|Maximális tárterület (adatok + index) egy adott tároló logikai partíciónként|10 GB|10 GB|

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [logikai partíció](partition-data.md)
* Ismerje meg, [hogyan helyezhet üzembe egy Azure Cosmos-tároló átviteli sebesség](how-to-provision-container-throughput.md)
* Ismerje meg, [átviteli sebességet egy Azure Cosmos-adatbázis kiépítése](how-to-provision-database-throughput.md)

