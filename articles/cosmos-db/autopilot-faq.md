---
title: Gyakori kérdések az Azure Cosmos DB robotpilóta-üzemmódban az átviteli ról
description: Gyakori kérdések az Azure Cosmos DB-adatbázisok és -tárolók robotpilóta-módjáról
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483309"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>Gyakori kérdések az Azure Cosmos DB autopilot módban lévő kiosztott átviteli fokkal kapcsolatban (előzetes verzió)
Autopilot módban az Azure Cosmos DB automatikusan kezeli és skálázhatja a tároló vagy adatbázis RU/s használat alapján. Ez a cikk választ ad a robotpilóta móddal kapcsolatos gyakori kérdésekre. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="is-autopilot-mode-supported-for-all-apis"></a>Minden API-ban támogatott a robotpilóta mód?
Igen, az autopilot mód minden API-ban támogatott: Core (SQL), Gremlin, Table, Cassandra és API a MongoDB-hoz.

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>A robotpilóta mód támogatott a többfős fiókok esetében?
Igen, a robotpilóta mód többfős fiókok esetén támogatott. A maximális RU/s minden régióban elérhető, amely hozzá van adva a Cosmos-fiókhoz. 

### <a name="what-is-the-pricing-for-autopilot"></a>Mi az árképzés a robotpilóta?
A részleteket az Azure Cosmos DB [díjszabási lapján](https://azure.microsoft.com/pricing/details/cosmos-db/) találja. 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>Hogyan engedélyezhetem a robotpilótát a tárolókhoz vagy adatbázisokhoz?
Autopilot mód engedélyezhető az Azure Portal használatával létrehozott új tárolókés adatbázisok. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>Van CLI vagy SDK támogatás a tárolók vagy adatbázisok autopilot módban való létrehozásához?
Jelenleg az előzetes verzióban csak az Azure Portalon robotpilóta módban hozhat létre erőforrásokat. A CLI és az SDK támogatása még nem érhető el.

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>Engedélyezhetem a robotpilótát egy meglévő tárolón vagy adatbázison?
Jelenleg engedélyezheti a robotpilóta az új tárolók és adatbázisok létrehozásakor. A robotpilóta mód engedélyezéséhez a meglévő tárolókon és adatbázisokon még nem érhető el támogatás. A meglévő tárolókat áttelepítheti egy új tárolóba az [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) használatával, vagy [módosíthatja a hírcsatornát.](change-feed.md) 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>Kikapcsolhatom a robotpilóta módot egy tárolón vagy adatbázison?
Igen, kikapcsolhatja a robotpilóta váltása a "Kézi" opció a kiosztott átviteli. Az előzetes verzióban a robotpilóta módról a manuális módra való váltás után nem engedélyezheti újra a robotpilótát ugyanahhoz az erőforráshoz. 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>A robotpilóta mód támogatott a megosztott átviteli adatátus-adatbázisokban?
Igen, a robotpilóta mód támogatott a megosztott átviteli adató-adatbázisok. A funkció engedélyezéséhez válassza ki a robotpilóta módot és az **átviteli áteresztőátviteli** beállítást az adatbázis létrehozásakor. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>Mi az engedélyezett gyűjtemények száma megosztott átviteli adatbázisonként, ha a robotpilóta engedélyezve van?
A robotpilóta módban engedélyezett megosztott átviteli adatbázisok esetében az engedélyezett gyűjtemények száma a következő: MIN(25, Max RU/s adatbázis / 1000). Ha például az adatbázis maximális átviteli sebessége 20 000 RU/s, az adatbázis rendelkezhet MIN(25, 20 000 RU/s / 1000) = 20 gyűjtemény. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Mi a tárolási korlát az egyes maximális RU/s beállításokhoz társítva?  
A tárolási korlát GB-ban minden egyes maximális RU/s esetében a következő: Max RU/s adatbázis vagy tároló / 100. Ha például a maximális RU/s 20 000 RU/s, az erőforrás 200 GB tárhelyet támogathat. Tekintse meg a [robotpilóta korlátok](provision-throughput-autopilot.md#autopilot-limits) cikket a rendelkezésre álló maximális RU /s és tárolási lehetőségeket. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Mi történik, ha túllépem a maximális átviteli sebességhez társított tárolási korlátot?
Ha az adatbázis vagy tároló maximális átviteli sebességéhez társított tárolási korlát túllépi, az Azure Cosmos DB automatikusan növeli a maximális átviteli sebesség a következő legmagasabb szintre, amely támogatja ezt a tárolási szintet. Tegyük fel például, hogy egy adatbázis vagy tároló a 4000 RU/s maximális átviteli sebesség beállítással van kiépítve, amely 50 GB-os tárolási korláttal rendelkezik. Ha az erőforrás tárolása 100 GB-ra nő, az adatbázis vagy tároló maximális RU/s-a automatikusan 20 000 RU/s-ra nő, amely akár 200 GB-ot is támogathat. 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>Milyen gyorsan fog robotpilóta skála felés le alapján tüskék a forgalom?
A robotpilóta a bejövő forgalom alapján azonnal felfelé vagy lefelé skálázódik a RU/s-on belül a minimális és maximális RU/s tartományon belül. A számlázás 1 órás részletességgel történik, ahol egy adott órában a legmagasabb RU-ért kell fizetnie. 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>Megadhatok egyéni maximális átviteli értéket (RU/s) a robotpilóta módhoz?
Jelenleg az előzetes verzióban négy [maximális](provision-throughput-autopilot.md#autopilot-limits) átviteli sebesség (RU/s) közül választhat.

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Növelhetem a maximális RU/s-t (magasabb szintre való áthelyezés) az adatbázisban vagy a tárolóban? 
Igen. A **méretezés & beállítások beállítás** a tároló, vagy **méretezése** lehetőség az adatbázishoz, választhat egy magasabb maximális RU/s a robotpilóta. Ez egy aszinkron felskálázási művelet, amely valamikor befejeződhet (általában 4-6 óra, a kiválasztott RU/s-tól függően), mivel a szolgáltatás több erőforrást rendel a magasabb skála támogatásához. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Csökkenthető a maximális RU/s (áthelyezés alacsonyabb szintre) az adatbázisban vagy a tárolóban?
Igen. Mindaddig, amíg az adatbázis vagy tároló aktuális tárolása a maximális RU/s szinthez társított [tárolási korlát](#what-is-the-storage-limit-associated-with-each-max-rus-option) alatt van, a maximális RU/s-t csökkentheti az adott szintre. Ha például 20 000 RU/s-ot választott ki maximális RU/s-ként, akkor a maximális RU/s-ot 4000 RU/s-ra skálázhatja, ha kevesebb mint 50 GB tárhellyel rendelkezik (a 4000 RU/s-hoz társított tárolási korlát).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Mi a maximális RU/s és a fizikai partíciók közötti leképezés?
Amikor először választja ki a maximális RU/s, az Azure Cosmos DB kiépíti: Max RU/s / 10 000 RU/s = # fizikai partíciók. Minden [fizikai partíció](partition-data.md#physical-partitions) legfeljebb 10 000 RU/s és 50 GB tárhelyet támogathat. A tárolási méret növekedésével az Azure Cosmos DB automatikusan felosztja a partíciókat, hogy további fizikai partíciókat adjon hozzá a tárolónövekedés kezeléséhez, vagy növelje a maximális RU/s szintet, ha a tárterület [meghaladja a társított korlátot.](#what-is-the-storage-limit-associated-with-each-max-rus-option) 

Az adatbázis vagy tároló maximális RU/s-ai egyenletesen oszlanak meg az összes fizikai partícióközött. Így a teljes átviteli sebesség bármely egyetlen fizikai partíció skálázható a következő: Max RU/s adatbázis vagy tároló / # fizikai partíciók. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Mi történik, ha a bejövő kérelmek meghaladják az adatbázis vagy tároló maximális RU/s-át?
Ha a teljes felhasznált RU/s meghaladja a tároló vagy adatbázis maximális RU/s-át, a maximális RU/s-ot meghaladó kérelmeket a rendszer szabályozza, és 429 állapotkódot ad vissza. A 100%-ban normalizált kihasználtságot eredményező kérelmek is szabályozásra kerülnek. Normalizált kihasználtság definíció szerint a maximális a RU / s kihasználtság a fizikai partíciókon. Tegyük fel például, hogy a maximális átviteli sebesség 20 000 RU/s, és két fizikai partícióval rendelkezik, P_1 és P_2, amelyek mindegyike 10 000 RU/s-ra skálázható. Egy adott másodpercben, ha P_1 6000 VT-t használt, és P_2 8000 VT-t, a normalizált használat MAX(6000 RU / 10 000 RU, 8000 RU / 10 000 RU) = 0,8.

> [!NOTE]
> Az Azure Cosmos DB ügyfél SDK-k és adatimportálási eszközök (Azure Data Factory, tömeges végrehajtó könyvtár) automatikusan újra próbálkoznak a 429s, így alkalmi 429s rendben van. A tartósan nagy számú 429s jelezheti, hogy növelni kell a maximális RU / s, vagy felülvizsgálja a particionálási stratégia egy [forró partíciót.](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled)

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>Továbbra is lehetséges, hogy 429s (fojtás / sebesség korlátozása), ha robotpilóta engedélyezve van? 
Igen. Lehetséges, hogy 429s két forgatókönyv. Először is, ha a teljes felhasznált RU/s meghaladja a tároló vagy adatbázis maximális RU/s-át, a szolgáltatás ennek megfelelően szabályozza a kérelmeket. 

Másodszor, ha van egy gyorsválasztó, azaz egy logikai partíciókulcs-érték, amely aránytalanul nagyobb mennyiségű kérelmeket, mint más partíciókulcs-értékek, lehetséges, hogy az alapul szolgáló fizikai partíció meghaladja a RU/s költségvetését. Ajánlott eljárásként a gyakori elérése: partíciók elkerülése érdekében [válasszon egy jó partíciókulcsot,](partitioning-overview.md#choose-partitionkey) amely a tárolási és átviteli kapacitás egyenletes elosztását eredményezi. 

Ha például a 20 000 RU/s maximális átviteli sebesség lehetőséget választja, és 200 GB tárhellyel rendelkezik, négy fizikai partícióval, minden fizikai partíció automatikusan skálázható 5000 RU/s-ra. Ha egy adott logikai partíciókulcson volt egy gyorsválasztó, akkor 429s jelenik meg, ha az alapul szolgáló fizikai partíció meghaladja az 5000 RU/s-ot, azaz meghaladja a 100%-os normalizált használatot.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [engedélyezheti a robotpilóta egy Azure Cosmos-tárolóban vagy -adatbázisban.](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode)
* Ismerje meg a [robotpilóta előnyeit.](provision-throughput-autopilot.md#benefits-of-autopilot-mode)
* További információ a [logikai és fizikai partíciókról.](partition-data.md)
