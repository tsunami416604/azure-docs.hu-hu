---
title: Gyakori kérdések a kiosztott átviteli sebességről Azure Cosmos DB
description: Gyakori kérdések az Azure Cosmos DB-adatbázisok és-tárolók kiosztott átviteli sebességéről
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: fa4c2708f34a377a17914c7e6e5abdd709cbb5b1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791782"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Gyakori kérdések a kiosztott átviteli sebességről Azure Cosmos DB

Az automatikus méretezés kiépített átviteli sebességével a Azure Cosmos DB automatikusan kezeli és méretezi a tároló vagy az adatbázis használatát a használat alapján. Ez a cikk az autoskálázással kapcsolatos gyakori kérdésekre ad választ.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="is-autoscale-supported-for-all-apis"></a>Támogatott-e az összes API-t használó autoskálázás?
Igen, az autoscale minden API esetében támogatott: Core (SQL), Gremlin, Table, Cassandra és API a MongoDB-hez.

### <a name="is-autoscale-supported-for-multi-master-accounts"></a>Támogatott-e a több főkiszolgálós fiókok esetében az autoscale?
Igen, a több főkiszolgálós fiókok esetében az autoskálázás is támogatott. A Max RU/s a Cosmos-fiókhoz hozzáadott minden régióban elérhető. 

### <a name="what-is-the-pricing-for-autoscale-"></a>Mi az az autoscale díjszabása?
A részletekért tekintse meg a Azure Cosmos DB [díjszabási oldalát](https://azure.microsoft.com/pricing/details/cosmos-db/) . 

### <a name="how-do-i-enable-autoscale-for-my-containers-or-databases"></a>Hogyan engedélyezi a saját tárolók vagy adatbázisok autoskálázását?
Az autoskálázás engedélyezhető a Azure Portal használatával létrehozott új tárolókban és adatbázisokban.

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autoscale-provisioned-throughput"></a>Van CLI-vagy SDK-támogatás tárolók vagy adatbázisok létrehozásához az autoscale kiépített átviteli sebességgel?
Jelenleg csak olyan erőforrásokat hozhat létre, amelyek az Azure Portalból származó, az autoscale kiosztott átviteli sebességgel rendelkeznek. A CLI és az SDK támogatása még nem érhető el.

### <a name="can-i-enable-autoscale-on-an-existing-container-or-a-database"></a>Engedélyezhető az autoskálázás egy meglévő tárolón vagy adatbázison?
Jelenleg az új tárolók és adatbázisok esetében engedélyezheti az autoskálázást a létrehozásuk során. Az autoskálázás meglévő tárolók és adatbázisok számára történő engedélyezésének támogatása még nem érhető el. A meglévő tárolókat áttelepítheti egy új tárolóba [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) vagy a [módosítási hírcsatorna](change-feed.md)használatával. 

### <a name="can-i-turn-off-autoscale-on-a-container-or-database"></a>Kikapcsolhatom az autoskálázást egy tárolón vagy adatbázison?
Igen, kikapcsolhatja az automatikus skálázást, ha átvált a "standard (manuális)" lehetőségre a kiépített átviteli sebességre. Az aktuális kiadásban, miután az autoskálázásról a standard kiosztott átviteli sebességre vált, nem engedélyezheti újra az autoskálázást ugyanarra az erőforrásra. 

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>Támogatott-e a megosztott átviteli sebességű adatbázisok esetében a méretezés?
Igen, az autoskálázás támogatott a megosztott átviteli sebességű adatbázisok esetében. A szolgáltatás engedélyezéséhez válassza az autoskálázás lehetőséget, és az adatbázis létrehozásakor a **kapacitás kiépítése** beállítást. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autoscale-is-enabled"></a>Mennyibe kerül az engedélyezett gyűjtemények száma egy megosztott átviteli sebességű adatbázisban, ha engedélyezve van az autoskálázás?
A megosztott átviteli sebességű adatbázisok esetében, amelyeken engedélyezve van az autoskálázás, az engedélyezett gyűjtemények száma a következő: MIN (25, Max RU/s, adatbázis/1000). Ha például az adatbázis maximális átviteli sebessége 20 000 RU/s, az adatbázis MIN. (25, 20 000 RU/s/1000) = 20 gyűjtemény lehet. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Mekkora a maximális RU/s beállításhoz tartozó tárolási korlát?  
Az egyes maximális RU/s-k tárolási korlátja GB-ban: az adatbázis vagy a tároló/100 maximális száma. Ha például a maximális RU/s értéke 20 000 RU/s, az erőforrás képes támogatni a 200 GB tárterületet. A rendelkezésre álló maximális RU/s-és tárolási lehetőségekért lásd az [autoskálázási korlátokat](provision-throughput-autoscale.md#autoscale-limits) ismertető cikket. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Mi történik, ha túllépem a maximális átviteli sebességhez kapcsolódó tárolási korlátot?
Ha túllépte az adatbázis vagy tároló maximális átviteli sebességével kapcsolatos tárolási korlátot, Azure Cosmos DB automatikusan növeli a maximális átviteli sebességet a következő legmagasabb szintre, amely támogatja a tárterületet. Tegyük fel például, hogy egy adatbázis vagy tároló a 4000 RU/s Max átviteli sebesség beállítással van kiépítve, amelynek tárolási korlátja 50 GB. Ha az erőforrás tárterülete 100 GB-ra nő, az adatbázis vagy tároló maximális RU/s értéke automatikusan 20 000 RU/s lesz, ami akár 200 GB-ot is képes támogatni. 

### <a name="how-quickly-will-autoscale-up-and-down-based-on-spikes-in-traffic"></a>Milyen gyorsan fel-és leskálázást kell végezni a forgalomban lévő tüskék alapján?
Az autoscale használatával a bejövő forgalom alapján azonnal, a minimális és a maximális RU/s-korláton belül, vagy akár lefelé is méretezheti az RU/s-t. A számlázás 1 órás részletességgel történik, ahol a legmagasabb RU/s díjat számítjuk fel egy adott órában.

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autoscale"></a>Megadhatok egyéni maximális átviteli sebesség (RU/s) értéket az autoscale esetében?
Jelenleg [négy lehetőség](provision-throughput-autoscale.md#autoscale-limits) közül választhat a maximális átviteli sebesség (ru/s) közül.

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Növelhető a maximális RU/s (magasabb szintűre való áttérés) az adatbázison vagy a tárolón? 
Igen. A tárolóhoz tartozó **méretezési & beállítások** lehetőségnél vagy az adatbázis **méretezési** lehetőségén kiválaszthat egy magasabb maximális ru/mp-t az autoskálázáshoz. Ez egy aszinkron méretezési művelet, amely eltarthat egy ideig (általában 4-6 óra, attól függően, hogy milyen RU/s van kiválasztva), mivel a szolgáltatás több erőforrást nyújt a nagyobb méret támogatásához. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Csökkenthető a maximális RU/s (egy alacsonyabb szintűre) az adatbázison vagy a tárolón?
Igen. Ha az adatbázis vagy a tároló jelenlegi tárterülete nem éri el a maximális ru/s szintet, amelyet le szeretne méretezni, csökkentheti a maximális RU/s [értéket](#what-is-the-storage-limit-associated-with-each-max-rus-option) az adott szintjére. Ha például a 20 000 RU/s értéket választotta a maximális RU/s értékként, akkor a maximális RU/s-t 4000 RU/s-ra méretezheti, ha kevesebb, mint 50 GB tárhellyel rendelkezik (a 4000 RU/s-hoz társított tárolási korlát).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Mi a különbség a maximális RU/s és a fizikai partíciók között?
Amikor először kiválasztja a maximális RU/s-t, Azure Cosmos DB a következőt fogja kiépíteni: Max RU/s/10 000 RU/s = # fizikai partíciók. Az egyes [fizikai partíciók](partition-data.md#physical-partitions) legfeljebb 10 000 ru/s és 50 GB tárterületet tudnak támogatni. Mivel a tárterület mérete növekszik, Azure Cosmos DB automatikusan felosztja a partíciókat, hogy további fizikai partíciókat adjon hozzá a tárterület növekedésének kezeléséhez, vagy növelje a maximális RU/s szintet, ha a tárterület [meghaladja a társított korlátot](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Az adatbázis vagy tároló maximális RU/s értéke egyenletesen oszlik el az összes fizikai partíció között. Tehát a teljes átviteli sebesség egyetlen fizikai partíción is méretezhető a következőre: az adatbázis vagy tároló/# fizikai partíciók maximális száma (RU/s). 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Mi történik, ha a bejövő kérelmek túllépik az adatbázis vagy a tároló maximális RU/s értékeit?
Ha a teljes felhasznált RU/s érték meghaladja a tároló vagy az adatbázis maximum RU/mp-t, a maximális RU/mp-t meghaladó kérések szabályozva lesznek, és egy 429 állapotkódot adnak vissza. A 100%-os normalizált kihasználtságot eredményező kérések is szabályozva lesznek. A normalizált kihasználtság az összes fizikai partíción az RU/s kihasználtság maximális értékeként van definiálva. Tegyük fel például, hogy a maximális átviteli sebesség 20 000 RU/s, és két fizikai partíció, P_1 és P_2 van, amelyek mindegyike 10 000 RU/s-ra méretezhető. Egy adott másodpercben, ha P_1 6000 RUs-t használ, és P_2 8000 RUs, a normalizált kihasználtság MAX (6000 RU/10 000 RU, 8000 RU/10 000 RU) = 0,8.

> [!NOTE]
> Az Azure Cosmos DB ügyféloldali SDK-k és adatimportálási eszközök (Azure Data Factory, tömeges végrehajtó kódtár) automatikusan újrapróbálkoznak a 429s, így az alkalmi 429s is rendben vannak. A tartósan nagy mennyiségű 429s arra utalhat, hogy emelnie kell a maximális RU/mp-t, vagy a particionálási stratégiát egy [gyors partícióra](#autoscale-rate-limiting)vonatkozóan át kell tekintenie.

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a>Továbbra is látható a 429s (szabályozás/arány korlátozása), ha engedélyezve van az autoskálázás? 
Igen. A 429s két forgatókönyvben is megtekinthető. Először is, ha a teljes felhasznált RU/s érték meghaladja a tároló vagy az adatbázis maximum RU/mp-t, a szolgáltatás ennek megfelelően szabályozza a kérelmeket. 

Másodszor, ha van egy gyakori partíció, azaz egy logikai partíciós kulcs értéke, amely aránytalanul nagyobb mennyiségű kérést tartalmaz a többi partíciós kulcs értékeihez képest, lehetséges, hogy a mögöttes fizikai partíció túllépi az RU/s költségvetést. Ajánlott eljárásként a gyakori partíciók elkerülése érdekében [válasszon egy jó partíciós kulcsot](partitioning-overview.md#choose-partitionkey) , amely a tárterület és az átviteli sebesség egyenletes eloszlását eredményezi. 

Ha például az 20 000 RU/s maximális átviteli sebesség lehetőséget választja, és 200 GB tárhellyel rendelkezik, és négy fizikai partícióval rendelkezik, az egyes fizikai partíciók akár 5000 RU/s-ra is felméretezhetők. Ha egy adott logikai partíción egy gyors partíció található, akkor a 429s jelenik meg, ha a mögöttes fizikai partíció mérete meghaladja az 5000 RU/s-t, azaz meghaladja az 100%-os normalizált kihasználtságot.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [engedélyezheti az Azure Cosmos-tárolón vagy-adatbázison az autoskálázást](provision-throughput-autoscale.md#create-db-container-autoscale).
* Ismerje meg, hogy milyen [előnyökkel jár a kiépített átviteli sebesség az autoskálázással ](provision-throughput-autoscale.md#autoscale-benefits).
* További információ a [logikai és a fizikai partíciókhoz](partition-data.md).
