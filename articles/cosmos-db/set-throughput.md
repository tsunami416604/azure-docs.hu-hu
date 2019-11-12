---
title: Átviteli sebesség az Azure Cosmos-tárolók és-adatbázisok számára
description: Megtudhatja, hogyan állíthatja be az Azure Cosmos-tárolók és-adatbázisok kiépített átviteli sebességét.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: 9ac22461e04b447fe34d5647eb5ec7847d25a09d
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931271"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Átviteli sebesség kiosztása tárolókra és adatbázisokra

Az Azure Cosmos Database egy tároló-készlet felügyeleti egysége. Az adatbázisok séma-független tárolók készletét alkotják. Az Azure Cosmos-tároló az átviteli sebesség és a tárterület méretezhetőségének egysége. A tárolók horizontálisan particionálva vannak egy Azure-régióban található gépek között, és az Azure Cosmos-fiókhoz társított összes Azure-régióban el vannak osztva.

A Azure Cosmos DB segítségével két részletességgel kiépítheti az átviteli sebességet:
 
- Azure Cosmos-tárolók
- Azure Cosmos-adatbázisok

## <a name="set-throughput-on-a-container"></a>Átviteli sebesség beállítása egy tárolón  

Az Azure Cosmos-tárolón kiépített átviteli sebesség kizárólag az adott tároló számára van fenntartva. A tároló minden alkalommal megkapja a kiosztott átviteli sebességet. A tárolók kiépített átviteli sebessége pénzügyi támogatást biztosít a SLA-kat illetően. Az átviteli sebesség tárolón való konfigurálásával kapcsolatos további információkért lásd: [átviteli sebesség kiépítése egy Azure Cosmos-tárolón](how-to-provision-container-throughput.md).

A kiépített átviteli sebesség egy tárolón való beállítása a leggyakrabban használt lehetőség. A tárolók kapacitása rugalmasan méretezhető, ha a [kérelmek egységei (RUs)](request-units.md)használatával bármilyen mennyiségű átviteli sebességet kiépít. 

A tárolók számára kiépített átviteli sebesség egyenletesen oszlik meg a fizikai partíciók között, és feltételezi, hogy a logikai partíciók egyenletes elosztása a fizikai partíciók között, az átviteli sebesség egyenletesen oszlik el az összes között a tároló logikai partíciói. A logikai partíciók átviteli sebességét nem lehet szelektív módon megadni. Mivel a tárolók egy vagy több logikai partíciója egy fizikai partíción fut, a fizikai partíciók kizárólag a tárolóhoz tartoznak, és támogatják a tárolón kiosztott átviteli sebességet. 

Ha a logikai partíción futó munkaterhelés több, mint az adott logikai partícióhoz lefoglalt átviteli sebesség, a műveletek lekérési díja korlátozott. Az arány korlátozása esetén növelheti a kiosztott átviteli sebességet a teljes tárolón, vagy megismételheti a műveleteket. További információ a particionálásról: [logikai partíciók](partition-data.md).

Azt javasoljuk, hogy az átviteli sebességet a tároló részletessége szerint konfigurálja, ha a tárolóhoz garantált teljesítményt szeretne használni.

Az alábbi képen látható, hogy egy fizikai partíció egy vagy több tároló logikai partícióját tárolja:

![Fizikai partíció](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Átviteli sebesség beállítása egy adatbázison

Amikor egy Azure Cosmos-adatbázison kiépíti az átviteli sebességet, az átviteli sebesség az adatbázisban található összes tárolóban (megosztott adatbázis-tárolókban) van megosztva. Kivételt képez, ha meghatározott adatátviteli sebességet adott meg az adatbázis egy adott tárolóján. Az adatbázis-szinten kiosztott átviteli sebesség megosztása a tárolók között hasonló ahhoz, hogy egy adatbázist a gépek egy fürtjén lehessen üzemeltetni. Mivel az adatbázisban lévő összes tároló megosztja a gépen elérhető erőforrásokat, természetesen nem kap kiszámítható teljesítményt egyetlen adott tárolón sem. A kiépített átviteli sebesség adatbázison való konfigurálásával kapcsolatos további információkért lásd: [kiépített átviteli sebesség konfigurálása egy Azure Cosmos-adatbázisban](how-to-provision-database-throughput.md).

Az átviteli sebesség Azure Cosmos-adatbázison való beállítása garantálja, hogy az adott adatbázishoz tartozó kiosztott átviteli sebességet minden alkalommal megkapja. Mivel az adatbázisban lévő összes tároló osztozik a kiépített átviteli sebességen, Azure Cosmos DB nem biztosít kiszámítható átviteli garanciát az adatbázis egy adott tárolójához. Egy adott tároló által fogadott átviteli sebesség része a következőktől függ:

* A tárolók száma.
* A különböző tárolók partíciós kulcsai közül választhat.
* A munkaterhelés elosztása a tárolók különböző logikai partíciói között. 

Azt javasoljuk, hogy az átviteli sebességet egy adatbázison konfigurálja, ha az átviteli sebességet több tárolón szeretné megosztani, de nem kívánja az átviteli sebességet egyetlen adott tárolóhoz hozzárendelni. 

Az alábbi példák azt mutatják be, hogy hol érdemes az átviteli sebességet az adatbázis szintjén kiépíteni:

* Több-bérlős alkalmazás esetében hasznos a kiosztott átviteli sebesség megosztása egy tárolón keresztül. Minden felhasználó egy különálló Azure Cosmos-tárolóban szerepelhet.

* Egy adatbázis kiosztott átviteli sebessége több tárolóban való megosztása akkor hasznos, ha olyan NoSQL-adatbázist telepít át, mint például a MongoDB vagy a Cassandra, amely virtuális gépek vagy helyszíni fizikai kiszolgálók fürtön található, és Azure Cosmos DB. Gondoljon arra, hogy az Azure Cosmos-adatbázison konfigurált kiépített átviteli sebességet logikai egyenértékűként, de költséghatékonyan és rugalmasan, a MongoDB vagy a Cassandra-fürt számítási kapacitása szerint konfigurálja.  

A kiosztott átviteli sebességgel rendelkező adatbázisban létrehozott összes tárolót [partíciós kulccsal](partition-data.md)kell létrehozni. Egy adott időpontban az adatbázisban lévő tároló számára lefoglalt átviteli sebesség a tároló összes logikai partícióján át lesz osztva. Ha olyan tárolókkal rendelkezik, amelyek osztoznak egy adatbázison konfigurált kiépített átviteli sebességen, nem alkalmazhat szelektíven egy adott tárolóra vagy logikai partícióra az átviteli sebességet. 

Ha egy logikai partíció munkaterhelése több, mint az adott logikai partícióhoz lefoglalt átviteli sebesség, a műveletek díja korlátozott. A ráta korlátozása esetén növelheti a teljes adatbázis átviteli sebességét, vagy próbálja megismételni a műveletet. További információ a particionálásról: [logikai partíciók](partition-data.md).

Az adatbázison keresztül kiépített átviteli sebesség megosztható az adott adatbázisban lévő tárolók között. Az adatbázis-szintű megosztott átviteli sebesség minden új tárolója 100 RU/s-t igényel. Ha megosztott adatbázis-ajánlattal rendelkező tárolókat épít ki:

* Minden 25 tároló egy partíció-készletbe van csoportosítva, és az adatbázis átviteli sebessége (D) meg van osztva a partíciós készletben lévő tárolók között. Ha az adatbázisban legfeljebb 25 tároló található, és bármikor, ha csak egy tárolót használ, akkor a tároló maximális átviteli sebességet használhat.

* A 25 tárolót követően létrehozott összes új tároló létrehozásakor létrejön egy új lemezpartíció, és az adatbázis átviteli sebessége a létrehozott új partíciók között oszlik el (ez a 2 partícióhoz tartozó D/2, a D/3 partíciós készlet...). Ha az adatbázisból csak egy tárolót használ, a maximális érték (D/2, D/3, D/4) lehet... átviteli sebesség). A csökkentett átviteli sebesség miatt ajánlott, hogy legfeljebb 25 tárolót hozzon létre egy adatbázisban.

**Példa**

* Ha létrehoz egy "MyDB" nevű adatbázist, amelynek kiosztott átviteli sebessége 10K RU/s.

* Ha 25 tárolót hoz létre a "MyDB" alatt, akkor az összes tároló egy partíció-készletbe van csoportosítva. Ha az adatbázisból csak egy tárolót használ, akkor az adott időpontban legfeljebb 10K RU/s (D) lehet.

* A 26th-tároló kiépítésekor létrejön egy új lemezpartíció, és az átviteli sebesség egyenlően oszlik meg a partíciók között. Így bármikor, ha csak egy tárolót használ az adatbázisból, legfeljebb 5K RU/s (D/2) értéket használhat. Mivel két partíciótípus van, az átviteli sebesség megosztási tényezője a D/2 értékre van bontva.

   Az alábbi kép az előző példát mutatja be grafikusan:

   ![Megosztási tényező az adatbázis szintű átviteli sebességben](./media/set-throughput/database-level-throughput-shareability-factor.png)


Ha a számítási feladatok egy adatbázisban lévő összes gyűjtemény törlését és újbóli létrehozását vonják maguk után, akkor azt javasoljuk, hogy a gyűjtemény létrehozása előtt dobja el az üres adatbázist, és hozzon létre egy új adatbázist. Az alábbi képen látható, hogyan tárolhat egy fizikai partíció egy vagy több olyan logikai partíciót, amely különböző tárolókban található egy adatbázison belül:

![Fizikai partíció](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Átviteli sebesség beállítása egy adatbázison és egy tárolón

A két modellt kombinálhatja. Az adatátviteli sebesség az adatbázison és a tárolón is engedélyezett. Az alábbi példa azt szemlélteti, hogyan lehet az átviteli sebességet egy Azure Cosmos-adatbázisban és egy tárolóban kiépíteni:

* Létrehozhat egy *Z* nevű Azure Cosmos-adatbázist a *"K"* -mel kiépített átviteli sebességgel. 
* Ezután hozzon létre öt tárolót a-adatbázison *belül a,* *B*, *C*, *D*és *E* névvel. A B tároló létrehozásakor ügyeljen arra, hogy engedélyezze a **tároló beállítás dedikált átviteli sebességét** , és explicit módon konfigurálja a *"P"* kiépített átviteli sebességét ezen a tárolón. Vegye figyelembe, hogy a megosztott és a dedikált átviteli sebességet csak az adatbázis és a tároló létrehozásakor lehet konfigurálni. 

   ![Az átviteli sebesség beállítása a tároló szintjén](./media/set-throughput/coll-level-throughput.png)

* A *"K"* RUs-átviteli sebesség az *a*, *C*, *D*és *E*négy tárolóban van megosztva. Az *a*, a *C*, a *D*vagy az *E* által elérhető átviteli sebesség pontos mennyisége változó. Az egyes tárolók átviteli sebességéhez nem tartoznak SLA-k.
* A *B* nevű tároló garantált, hogy minden alkalommal megkapja a *"P"* RUs átviteli sebességét. Ez a SLA-k által támogatott.

> [!NOTE]
> A kiosztott átviteli sebességgel rendelkező tárolók nem alakíthatók át megosztott adatbázis-tárolóba. A megosztott adatbázis-tárolók nem alakíthatók át dedikált átviteli sebességgel.

## <a name="update-throughput-on-a-database-or-a-container"></a>Adatbázis vagy tároló adatforgalmának frissítése

Miután létrehozta az Azure Cosmos-tárolót vagy egy adatbázist, frissítheti a kiosztott átviteli sebességet. Az adatbázison vagy a tárolón konfigurálható maximális kiépített átviteli sebességnek nincs korlátja. A minimálisan kiosztott átviteli sebesség a következő tényezőktől függ: 

* A tárolóban korábban tárolt adatmennyiség maximális mérete
* A tárolón korábban kiépített maximális átviteli sebesség
* A megosztott átviteli sebességgel rendelkező adatbázisban korábban létrehozott Azure Cosmos-tárolók maximális száma. 

Egy tároló vagy egy adatbázis minimális átviteli sebességét programozott módon lekérheti az SDK-k használatával, vagy megtekintheti a Azure Portal értékét. A .NET SDK használatakor a [DocumentClient. ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) metódus lehetővé teszi a kiépített átviteli sebesség méretezését. A Java SDK használatakor a [RequestOptions. setOfferThroughput](sql-api-java-samples.md#offer-examples) metódus lehetővé teszi a kiépített átviteli sebesség méretezését. 

A .NET SDK használatakor a [DocumentClient. ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) metódus lehetővé teszi egy tároló vagy adatbázis minimális átviteli sebességének lekérését. 

A tárolók vagy adatbázisok kiépített átviteli sebességét bármikor méretezheti. Ha méretezési műveletet hajt végre az átviteli sebesség növelése érdekében, az hosszabb időt vehet igénybe, mivel a rendszerfeladatok kiépítik a szükséges erőforrásokat. A skálázási művelet állapotát Azure Portal vagy programozott módon is megtekintheti az SDK-k használatával. A .net SDK használatakor a méretezési művelet állapotát a `DocumentClient.ReadOfferAsync` metódus használatával érheti el.

## <a name="comparison-of-models"></a>Modellek összehasonlítása

|**Paraméter**  |**Adatbázison kiépített átviteli sebesség**  |**Tárolón kiépített átviteli sebesség**|
|---------|---------|---------|
|Minimális RUs |400 (az első négy tároló után minden további tárolóhoz legalább 100 RUs/másodperc szükséges.) |400|
|Minimális RUs/tároló|100|400|
|Maximális RUs|Korlátlan, az adatbázison.|Korlátlan, a tárolón.|
|Egy adott tárolóhoz rendelt vagy elérhető RUs|Nincs garancia. Az adott tárolóhoz rendelt RUs a tulajdonságoktól függ. A tulajdonságok választhatják a tárolók partíciós kulcsait, amelyek osztoznak az átviteli sebességen, a munkaterhelés eloszlásán és a tárolók számánál. |A tárolón konfigurált összes RUs kizárólag a tároló számára van fenntartva.|
|Tároló maximális tárterülete|Korlátlan.|Korlátlan.|
|Egy tároló logikai partíciójának maximális átviteli sebessége|10K RUs|10K RUs|
|Tárolók logikai partícióinak maximális tárterülete (adatsorok és indexek)|10 GB|10 GB|

## <a name="next-steps"></a>Következő lépések

* További információ a [logikai partíciókhoz](partition-data.md).
* Útmutató az [átviteli sebesség Azure Cosmos-tárolón](how-to-provision-container-throughput.md)való kiépítéséhez.
* Útmutató az [átviteli sebesség Azure Cosmos-adatbázison](how-to-provision-database-throughput.md)való kiépítéséhez.

