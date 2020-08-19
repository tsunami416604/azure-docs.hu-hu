---
title: Átviteli sebesség az Azure Cosmos-tárolók és-adatbázisok számára
description: Megtudhatja, hogyan állíthatja be az Azure Cosmos-tárolók és-adatbázisok kiépített átviteli sebességét.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 00ed8f6ff9839c227f3d8a929a071834c5559226
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605733"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>Bevezetés a kiépített átviteli sebességbe Azure Cosmos DB

Azure Cosmos DB lehetővé teszi a kiépített átviteli sebesség beállítását az adatbázisokon és a tárolókban. A kiosztott átviteli sebességnek, a standard (manuális) vagy az automatikus méretezésnek két típusa van. Ez a cikk áttekintést nyújt a kiépített átviteli sebesség működéséről. 

Az Azure Cosmos-adatbázis a tárolók készletének kezelési egysége. Az adatbázisok sémafüggetlen tárolókból állnak. Az Azure Cosmos-tároló az átviteli sebesség és a tárterület méretezhetőségének egysége. A tárolók horizontálisan vannak particionálva az Azure-régióban található gépek csoportjában, és az Azure Cosmos-fiókhoz társított összes Azure-régióban el vannak osztva.

A Azure Cosmos DB segítségével két részletességgel kiépítheti az átviteli sebességet:
 
- Azure Cosmos-tárolók
- Azure Cosmos-adatbázisok

## <a name="set-throughput-on-a-container"></a>Átviteli sebesség beállítása egy tárolón  

Az Azure Cosmos-tárolón kiépített átviteli sebesség kizárólag az adott tároló számára van fenntartva. A tároló minden alkalommal megkapja a kiosztott átviteli sebességet. A tárolók kiépített átviteli sebessége pénzügyi támogatást biztosít a SLA-kat illetően. Ha meg szeretné tudni, hogyan konfigurálhatja a standard (manuális) átviteli sebességet egy tárolón, tekintse meg az [átviteli sebesség Azure Cosmos-tárolón](how-to-provision-container-throughput.md)való kiosztását Ha szeretné megtudni, hogyan konfigurálhatja az adatátviteli sebességet egy tárolón, tekintse meg a következő témakört: az automatikusan [méretezhető átviteli sebesség](how-to-provision-autoscale-throughput.md)

A kiépített átviteli sebesség egy tárolón való beállítása a leggyakrabban használt lehetőség. A tárolók kapacitása rugalmasan méretezhető, ha a [kérelmek egységei (RUs)](request-units.md)használatával bármilyen mennyiségű átviteli sebességet kiépít. 

A tárolók számára kiépített átviteli sebesség egyenletesen oszlik meg a fizikai partíciók között, és feltételezi, hogy a logikai partíciók egyenletes elosztása a fizikai partíciók között történik, az átviteli sebesség egyenletesen oszlik el a tároló összes logikai partícióján is. A logikai partíciók átviteli sebességét nem lehet szelektív módon megadni. Mivel a tárolók egy vagy több logikai partíciója egy fizikai partíción fut, a fizikai partíciók kizárólag a tárolóhoz tartoznak, és támogatják a tárolón kiosztott átviteli sebességet. 

Ha a logikai partíción futó munkaterhelés több, mint a mögöttes fizikai partícióhoz lefoglalt átviteli sebesség, lehetséges, hogy a műveletek mértéke korlátozott lesz. Az úgynevezett gyakori _partíció_ akkor fordul elő, ha egy logikai partíció aránytalanul több kérést tartalmaz, mint a többi partíciós kulcs értéke.

Az arány korlátozása esetén növelheti a kiosztott átviteli sebességet a teljes tárolón, vagy megismételheti a műveleteket. Azt is meg kell adnia, hogy olyan partíciós kulcsot válasszon, amely egyenletesen osztja el a tárolót és a kérelmek mennyiségét. További információ a particionálásról: [particionálás és horizontális skálázás Azure Cosmos DBban](partition-data.md).

Azt javasoljuk, hogy az átviteli sebességet a tároló részletessége szerint konfigurálja, ha a tárolóhoz garantált teljesítményt szeretne használni.

Az alábbi képen látható, hogy egy fizikai partíció egy vagy több tároló logikai partícióját tárolja:

:::image type="content" source="./media/set-throughput/resource-partition.png" alt-text="Fizikai partíció" border="false":::

## <a name="set-throughput-on-a-database"></a>Átviteli sebesség beállítása egy adatbázison

> [!NOTE]
> Az átviteli sebesség az Azure Cosmos-adatbázisban jelenleg nem lehetséges olyan fiókokban, ahol az [ügyfél által felügyelt kulcsok](how-to-setup-cmk.md) engedélyezve vannak.

Amikor egy Azure Cosmos-adatbázison kiépíti az átviteli sebességet, az átviteli sebesség az adatbázisban található összes tárolóban (megosztott adatbázis-tárolókban) van megosztva. Ez alól kivételt képeznek az Ön által kiépített átviteli sebességgel rendelkező tárolók. Az adatbázis-szinten kiosztott átviteli sebesség megosztása a tárolók között hasonló ahhoz, hogy egy adatbázist a gépek egy fürtjén lehessen üzemeltetni. Mivel az adatbázisban lévő összes tároló megosztja a gépen elérhető erőforrásokat, természetesen nem kap kiszámítható teljesítményt egyetlen adott tárolón sem. A kiépített átviteli sebesség adatbázison való konfigurálásával kapcsolatos további információkért lásd: [kiépített átviteli sebesség konfigurálása egy Azure Cosmos-adatbázisban](how-to-provision-database-throughput.md). Az adatátviteli sebesség adatbázison történő konfigurálásával kapcsolatos további információkért lásd: az [autoscale átviteli sebességének kiépítése](how-to-provision-autoscale-throughput.md).

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

A megosztott átviteli sebességű adatbázisokban lévő tárolók között megoszlik az adott adatbázishoz lefoglalt átviteli sebesség (RU/s). Az adatbázisban legfeljebb négy, legalább 400 RU/s átviteli sebességű tároló lehet. A standard (manuális) kiépített átviteli sebességnél az első négy után minden új tárolóhoz további 100 RU/s szükséges. Ha például egy megosztott átviteli sebességű adatbázis nyolc tárolóval rendelkezik, az adatbázis esetében a minimális átviteli sebesség 800 RU/s lesz. Az autoscale kiépített átviteli sebességével akár 25 tárolót is beállíthat egy olyan adatbázisban, amelyben az autoscale Max 4000 RU/s (a 400-4000 RU/s közötti skálán).

> [!NOTE]
> Február 2020-án egy olyan módosítást vezettünk be, amely lehetővé teszi, hogy legfeljebb 25 tárolót helyezzen el egy megosztott átviteli sebességű adatbázisban, ami jobb lehetővé teszi az átviteli sebesség megosztását a tárolók között. Az első 25 tároló után csak akkor adhat hozzá több tárolót az adatbázishoz, ha [dedikált átviteli sebességgel lett kiépítve](#set-throughput-on-a-database-and-a-container), amely elkülönül az adatbázis megosztott átviteli sebességével.<br>
Ha a Azure Cosmos DB fiók már tartalmaz egy megosztott átviteli sebességű adatbázist >= 25 tárolóval, akkor a fiók és az azonos Azure-előfizetésben lévő összes többi fiók mentesül a változás alól. Ha visszajelzése vagy kérdése van, vegye [fel a kapcsolatot a terméktámogatási szolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) . 

Ha a számítási feladatok egy adatbázisban lévő összes gyűjtemény törlését és újbóli létrehozását vonják maguk után, akkor azt javasoljuk, hogy a gyűjtemény létrehozása előtt dobja el az üres adatbázist, és hozzon létre egy új adatbázist. Az alábbi képen látható, hogyan tárolhat egy fizikai partíció egy vagy több olyan logikai partíciót, amely különböző tárolókban található egy adatbázison belül:

:::image type="content" source="./media/set-throughput/resource-partition2.png" alt-text="Fizikai partíció" border="false":::

## <a name="set-throughput-on-a-database-and-a-container"></a>Átviteli sebesség beállítása egy adatbázison és egy tárolón

A két modellt kombinálhatja. Az adatátviteli sebesség az adatbázison és a tárolón is engedélyezett. Az alábbi példa bemutatja, hogyan lehet a standard (manuális) kiosztott átviteli sebességet kiépíteni egy Azure Cosmos-adatbázison és egy tárolón:

* Létrehozhat egy *Z* nevű Azure Cosmos-adatbázist a standard (manuális) kiépített átviteli sebességgel a *"K"* RUs használatával. 
* Ezután hozzon létre öt tárolót a-adatbázison *belül a,* *B*, *C*, *D*és *E* névvel. A B tároló létrehozásakor ügyeljen arra, hogy engedélyezze a **tároló beállítás dedikált átviteli sebességét** , és explicit módon konfigurálja a *"P"* kiépített átviteli sebességét ezen a tárolón. Vegye figyelembe, hogy a megosztott és a dedikált átviteli sebességet csak az adatbázis és a tároló létrehozásakor lehet konfigurálni. 

   :::image type="content" source="./media/set-throughput/coll-level-throughput.png" alt-text="Az átviteli sebesség beállítása a tároló szintjén":::

* A *"K"* RUs-átviteli sebesség az *a*, *C*, *D*és *E*négy tárolóban van megosztva. Az *a*, a *C*, a *D*vagy az *E* által elérhető átviteli sebesség pontos mennyisége változó. Az egyes tárolók átviteli sebességéhez nem tartoznak SLA-k.
* A *B* nevű tároló garantált, hogy minden alkalommal megkapja a *"P"* RUs átviteli sebességét. Ez a SLA-k által támogatott.

> [!NOTE]
> A kiosztott átviteli sebességgel rendelkező tárolók nem alakíthatók át megosztott adatbázis-tárolóba. A megosztott adatbázis-tárolók nem alakíthatók át dedikált átviteli sebességgel.

## <a name="update-throughput-on-a-database-or-a-container"></a>Adatbázis vagy tároló adatforgalmának frissítése

Miután létrehozta az Azure Cosmos-tárolót vagy egy adatbázist, frissítheti a kiosztott átviteli sebességet. Az adatbázison vagy a tárolón konfigurálható maximális kiépített átviteli sebességnek nincs korlátja. 

Egy adatbázis vagy tároló [minimális kiosztott átviteli sebességének](concepts-limits.md#storage-and-database-operations) megbecsléséhez keresse meg a következő maximumot:

* 400 RU/s 
* Aktuális tárterület (GB * 10 RU/s)
* Az adatbázison vagy a tárolón/100-ben kiépített legmagasabb RU/s
* Tárolók száma * 100 RU/s (csak megosztott átviteli sebességű adatbázis esetén)

A tényleges RU/mp a fiók konfigurációjától függően változhat. [Azure monitor metrikák](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) használatával megtekintheti az erőforráson kiépített átviteli sebesség (ru/s) és tárterület előzményeit.

Egy tároló vagy egy adatbázis minimális átviteli sebességét programozott módon lekérheti az SDK-k használatával, vagy megtekintheti a Azure Portal értékét. A .NET SDK használatakor a [DocumentClient. ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) metódus lehetővé teszi a kiépített átviteli sebesség méretezését. A Java SDK használatakor a [RequestOptions. setOfferThroughput](sql-api-java-sdk-samples.md) metódus lehetővé teszi a kiépített átviteli sebesség méretezését. 

A .NET SDK használatakor a [DocumentClient. ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) metódus lehetővé teszi egy tároló vagy adatbázis minimális átviteli sebességének lekérését. 

A tárolók vagy adatbázisok kiépített átviteli sebességét bármikor méretezheti. Ha méretezési műveletet hajt végre az átviteli sebesség növelése érdekében, az hosszabb időt vehet igénybe, mivel a rendszerfeladatok kiépítik a szükséges erőforrásokat. A skálázási művelet állapotát Azure Portal vagy programozott módon is megtekintheti az SDK-k használatával. A .NET SDK használatakor a skálázási művelet állapotát a metódus segítségével érheti el `DocumentClient.ReadOfferAsync` .

## <a name="comparison-of-models"></a>Modellek összehasonlítása
Ez a táblázat a kiépítési standard (manuális) átviteli sebességének összehasonlítását mutatja be egy adatbázison és egy tárolón. 

|**Paraméter**  |**Standard (manuális) átviteli sebesség egy adatbázison**  |**Standard (manuális) átviteli sebesség egy tárolón**|**Átviteli sebesség egy adatbázison** | **Átviteli sebesség egy tárolón**|
|---------|---------|---------|---------|---------|
|Belépési pont (minimum RU/s) |400 RU/s. Az első négy tároló után minden további tárolóhoz legalább 100 RU/s szükséges</li> |400| 400 – 4000 RU/s közötti autoskálázás. Legfeljebb 25 tároló lehet, amely nem rendelkezik minimum/s/s értékkel</li> | 400 – 4000 RU/s közötti autoskálázás.|
|Legalább RU/s/tároló|100|400|--|400 – 4000 RU/s közötti autoskálázás|
|Maximális RUs|Korlátlan, az adatbázison.|Korlátlan, a tárolón.|Korlátlan, az adatbázison.|Korlátlan, a tárolón.
|Egy adott tárolóhoz rendelt vagy elérhető RUs|Nincs garancia. Az adott tárolóhoz rendelt RUs a tulajdonságoktól függ. A tulajdonságok választhatják a tárolók partíciós kulcsait, amelyek osztoznak az átviteli sebességen, a munkaterhelés eloszlásán és a tárolók számánál. |A tárolón konfigurált összes RUs kizárólag a tároló számára van fenntartva.|Nincs garancia. Az adott tárolóhoz rendelt RUs a tulajdonságoktól függ. A tulajdonságok választhatják a tárolók partíciós kulcsait, amelyek osztoznak az átviteli sebességen, a munkaterhelés eloszlásán és a tárolók számánál. |A tárolón konfigurált összes RUs kizárólag a tároló számára van fenntartva.|
|Tároló maximális tárterülete|Korlátlan.|Korlátlan|Korlátlan|Korlátlan|
|Egy tároló logikai partíciójának maximális átviteli sebessége|10K RU/s|10K RU/s|10K RU/s|10K RU/s|
|Tárolók logikai partícióinak maximális tárterülete (adatsorok és indexek)|20 GB|20 GB|20 GB|20 GB|

## <a name="next-steps"></a>Következő lépések

* További információ a [logikai partíciókhoz](partition-data.md).
* Ismerje meg, hogyan hozhat [létre standard (manuális) Azure Cosmos-tárolón](how-to-provision-container-throughput.md).
* Ismerje meg, hogyan hozhat [létre standard (manuális) átviteli sebességet egy Azure Cosmos-adatbázisban](how-to-provision-database-throughput.md).
* Megtudhatja, hogyan építhet ki az [Azure Cosmos-adatbázison vagy-tárolón az adatméretezési sebességet](how-to-provision-autoscale-throughput.md).
