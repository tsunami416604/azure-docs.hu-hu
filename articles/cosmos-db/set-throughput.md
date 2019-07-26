---
title: Az Azure Cosmos-tárolók és adatbázisok kiépítése átviteli
description: Megtudhatja, hogyan állíthatja be az Azure Cosmos-tárolók és-adatbázisok kiépített átviteli sebességét.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.openlocfilehash: 2bcd428e2de90251d4d64111b1c3e6b6f812ac4c
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467627"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Átviteli sebesség kiosztása tárolókra és adatbázisokra

Egy Azure Cosmos database tárolók több felügyeleti egység. Egy adatbázis sémafüggetlen tárolók készlete áll. Az Azure Cosmos-tárolókat méretezhetőséget biztosít az átviteli sebesség és a tárolási egység. Egy tároló között egy Azure-régión belüli gépek vízszintesen particionált, és az Azure Cosmos-fiókhoz társított összes Azure-régió között oszlanak meg.

A Azure Cosmos DB segítségével két részletességgel kiépítheti az átviteli sebességet:
 
- Azure Cosmos-tárolók
- Az Azure Cosmos Database.

## <a name="set-throughput-on-a-container"></a>Átviteli sebesség beállítása egy tárolón  

Az Azure Cosmos-tárolón kiépített átviteli sebesség kizárólag az adott tároló számára van fenntartva. A tároló kap a kiosztott átviteli sebesség folyamatosan. Egy tároló kiosztott átviteli felelősséggel szavatolják. Az átviteli sebesség tárolón való konfigurálásával kapcsolatos további információkért lásd: [átviteli sebesség kiépítése egy Azure Cosmos](how-to-provision-container-throughput.md)-tárolón.

A kiépített átviteli sebesség egy tárolón való beállítása a leggyakrabban használt lehetőség. A tárolók kapacitása rugalmasan méretezhető, ha a [kérelmek egységei (RUs)](request-units.md)használatával bármilyen mennyiségű átviteli sebességet kiépít. 

Az Azure Cosmos-tárolón kiépített átviteli sebesség egységesen oszlik meg a tároló logikai partíciói között. A logikai partíciók átviteli sebességét nem lehet szelektív módon megadni. Mivel a tárolók egy vagy több logikai partíciója egy fizikai partíción fut, a fizikai partíciók kizárólag a tárolóhoz tartoznak, és támogatják a tárolón kiosztott átviteli sebességet. 

Ha a logikai partíción futó munkaterhelés több, mint az adott logikai partícióhoz lefoglalt átviteli sebesség, a műveletek lekérési díja korlátozott. Az arány korlátozása esetén növelheti a kiosztott átviteli sebességet a teljes tárolón, vagy megismételheti a műveleteket. A particionálás további információkért lásd: [logikai partíció](partition-data.md).

Azt javasoljuk, hogy az átviteli sebességet a tároló részletessége szerint konfigurálja, ha a tárolóhoz garantált teljesítményt szeretne használni.

Az alábbi képen látható, hogy egy fizikai partíció egy vagy több tároló logikai partícióját tárolja:

![Fizikai partíció](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Átviteli sebesség beállítása egy adatbázison

Amikor egy Azure Cosmos-adatbázisban kiépíti az átviteli sebességet, az átviteli sebesség az adatbázis összes tárolójában meg lesz osztva. Kivételt képez, ha meghatározott adatátviteli sebességet adott meg az adatbázis egy adott tárolóján. Az adatbázis-szinten kiosztott átviteli sebesség megosztása a tárolók között hasonló ahhoz, hogy egy adatbázist a gépek egy fürtjén lehessen üzemeltetni. Mivel az adatbázisban lévő összes tároló megosztja a gépen elérhető erőforrásokat, természetesen nem kap kiszámítható teljesítményt egyetlen adott tárolón sem. A kiépített átviteli sebesség adatbázison való konfigurálásával kapcsolatos további információkért lásd: kiépített [átviteli sebesség konfigurálása egy Azure Cosmos-adatbázisban](how-to-provision-database-throughput.md).

Az átviteli sebesség Azure Cosmos-adatbázison való beállítása garantálja, hogy az adott adatbázishoz tartozó kiosztott átviteli sebességet minden alkalommal megkapja. Mivel az adatbázisban lévő összes tároló osztozik a kiépített átviteli sebességen, Azure Cosmos DB nem biztosít kiszámítható átviteli garanciát az adatbázis egy adott tárolójához. Az átviteli sebességet egy adott tároló fogadhatják része az adott nyelvtől függ:

* A tárolók száma.
* A különböző tárolók partíciós kulcsai közül választhat.
* A tárolók különböző logikai partíciók között a számítási feladatok eloszlása. 

Azt javasoljuk, hogy az átviteli sebességet egy adatbázison konfigurálja, ha az átviteli sebességet több tárolón szeretné megosztani, de nem kívánja az átviteli sebességet egyetlen adott tárolóhoz hozzárendelni. 

Az alábbi példák azt mutatják be, hogy hol érdemes az átviteli sebességet az adatbázis szintjén kiépíteni:

* Több-bérlős alkalmazás esetében hasznos a kiosztott átviteli sebesség megosztása egy tárolón keresztül. Minden felhasználó egy különálló Azure Cosmos-tárolót is képviseli.

* Egy adatbázis kiosztott átviteli sebessége több tárolóban való megosztása akkor hasznos, ha olyan NoSQL-adatbázist telepít át, mint például a MongoDB vagy a Cassandra, amely virtuális gépek vagy helyszíni fizikai kiszolgálók fürtön található, és Azure Cosmos DB. Gondoljon arra, hogy az Azure Cosmos-adatbázison konfigurált kiépített átviteli sebességet logikai egyenértékűként, de költséghatékonyan és rugalmasan, a MongoDB vagy a Cassandra-fürt számítási kapacitása szerint konfigurálja.  

A kiosztott átviteli sebességgel rendelkező adatbázisban létrehozott összes tárolót [partíciós kulccsal](partition-data.md)kell létrehozni. Megadott idő bármikor az adatbázison belüli egy tároló kiosztott átviteli legyen elosztva a tároló összes logikai partíciót. Ha olyan tárolókkal rendelkezik, amelyek osztoznak egy adatbázison konfigurált kiépített átviteli sebességen, nem alkalmazhat szelektíven egy adott tárolóra vagy logikai partícióra az átviteli sebességet. 

Ha egy logikai partíció munkaterhelése több, mint az adott logikai partícióhoz lefoglalt átviteli sebesség, a műveletek díja korlátozott. A ráta korlátozása esetén növelheti a teljes adatbázis átviteli sebességét, vagy próbálja megismételni a műveletet. A particionálás további információkért lásd: [logikai partíció](partition-data.md).

Az adatbázisba kiépített adatátviteli kapacitással rendelkező különböző tárolókban több logikai partíció is tárolható egyetlen fizikai partíción. A tárolók egyetlen logikai partíciója mindig egy fizikai partíción belül van, az *"* *C* " logikai partíciók között, amelyek osztoznak egy adatbázis kiosztott átviteli sebességén, az " *R"* fizikai partíciók. 

Az alábbi képen látható, hogyan tárolhat egy fizikai partíció egy vagy több olyan logikai partíciót, amely különböző tárolókban található egy adatbázison belül:

![Fizikai partíció](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Átviteli sebesség beállítása egy adatbázison és egy tárolón

A két modellt kombinálhatja. Az adatátviteli sebesség az adatbázison és a tárolón is engedélyezett. Az alábbi példa bemutatja, hogyan helyezhet üzembe egy Azure Cosmos database és a egy tárolót az átviteli sebesség:

* Létrehozhat egy *Z* nevű Azure Cosmos-adatbázist a *"K"* -mel kiépített átviteli sebességgel. 
* Ezután hozzon létre öt tárolót a-adatbázison belül a, *B*, *C*, *D*és *E* *névvel.* A B tároló létrehozásakor ügyeljen arra, hogy engedélyezze a **tároló beállítás dedikált átviteli sebességét** , és explicit módon konfigurálja a *"P"* kiépített átviteli sebességét ezen a tárolón. Vegye figyelembe, hogy a megosztott és a dedikált átviteli sebességet csak az adatbázis és a tároló létrehozásakor lehet konfigurálni. 

   ![Az átviteli sebesség beállítása a tároló szintjén](./media/set-throughput/coll-level-throughput.png)

* A *"K"* RUs-átviteli sebesség az *a*, *C*, *D*és *E*négy tárolóban van megosztva. Az *a*, a *C*, a *D*vagy az *E* által elérhető átviteli sebesség pontos mennyisége változó. Az egyes tárolók átviteli sebességéhez nem tartoznak SLA-k.
* A *B* nevű tároló garantált, hogy minden alkalommal megkapja a *"P"* RUs átviteli sebességét. Ez a SLA-k által támogatott.

## <a name="update-throughput-on-a-database-or-a-container"></a>Adatbázis vagy tároló adatforgalmának frissítése

Miután létrehozta az Azure Cosmos-tárolót vagy egy adatbázist, frissítheti a kiosztott átviteli sebességet. Az adatbázison vagy a tárolón konfigurálható maximális kiépített átviteli sebességnek nincs korlátja. A minimálisan kiosztott átviteli sebesség a következő tényezőktől függ: 

* A tárolóban korábban tárolt adatmennyiség maximális mérete
* A tárolón korábban kiépített maximális átviteli sebesség
* A megosztott átviteli sebességgel rendelkező adatbázisban korábban létrehozott Azure Cosmos-tárolók maximális száma. 

Egy tároló vagy egy adatbázis minimális átviteli sebességét programozott módon lekérheti az SDK-k használatával, vagy megtekintheti a Azure Portal értékét. A .NET SDK használatakor a [DocumentClient. ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) metódus lehetővé teszi a kiépített átviteli sebesség méretezését. A Java SDK használatakor a [RequestOptions. setOfferThroughput](sql-api-java-samples.md#offer-examples) metódus lehetővé teszi a kiépített átviteli sebesség méretezését. 

A .NET SDK használatakor a [DocumentClient. ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) metódus lehetővé teszi egy tároló vagy adatbázis minimális átviteli sebességének lekérését. 

A tárolók vagy adatbázisok kiépített átviteli sebességét bármikor méretezheti. Ha méretezési műveletet hajt végre az átviteli sebesség növelése érdekében, az hosszabb időt vehet igénybe, mivel a rendszerfeladatok kiépítik a szükséges erőforrásokat. A skálázási művelet állapotát Azure Portal vagy programozott módon is megtekintheti az SDK-k használatával. A .net SDK használatakor a skálázási művelet állapotát a `DocumentClient.ReadOfferAsync` metódus segítségével érheti el.

## <a name="comparison-of-models"></a>Modellek összehasonlítása

|**A paraméter**  |**Átviteli sebesség kiosztott részéért, adatbázis**  |**Egy tároló kiosztott átviteli sebesség**|
|---------|---------|---------|
|Minimális kérelemegység |400 (az első négy tároló után minden további tárolóhoz legalább 100 RUs/másodperc szükséges.) |400|
|Minimális RUs tárolónként|100|400|
|Maximális kérelemegység|Korlátlan, az adatbázison.|Korlátlan, a tárolón.|
|Egy adott tárolóhoz rendelt vagy elérhető RUs|Nincs garancia. Az adott tárolóhoz rendelt RUs a tulajdonságoktól függ. A tulajdonságok választhatják a tárolók partíciós kulcsait, amelyek osztoznak az átviteli sebességen, a munkaterhelés eloszlásán és a tárolók számánál. |A tároló konfigurált fenntartott egységek kizárólag a tároló számára vannak fenntartva.|
|Egy tároló maximális tárterülete|Korlátlan.|Korlátlan.|
|Egy adott tároló logikai partíció egységenkénti maximális adatátviteli sebesség|10 ezer kérelemegység|10 ezer kérelemegység|
|Maximális tárterület (adatok + index) egy adott tároló logikai partíciónként|10 GB|10 GB|

## <a name="next-steps"></a>További lépések

* További információ a [logikai partíciókhoz](partition-data.md).
* Útmutató az [átviteli sebesség Azure Cosmos](how-to-provision-container-throughput.md)-tárolón való kiépítéséhez.
* Útmutató az [átviteli sebesség Azure Cosmos](how-to-provision-database-throughput.md)-adatbázison való kiépítéséhez.

