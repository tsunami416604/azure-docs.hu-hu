---
title: Gyakori kérdések az Azure Cosmos DB-hez készült Azure Synapse Linkkel kapcsolatban
description: Válaszok a szinapszis-hivatkozással kapcsolatos gyakori kérdésekre a Azure Cosmos DB olyan területeken, mint például a számlázás, az analitikus tárolás, a biztonság, az elemzési tár élettartama.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: b5bf29969f8b6d54215e2b2f1a2d0b1587faad6f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332185"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Gyakori kérdések az Azure Cosmos DB-hez készült Azure Synapse Linkkel kapcsolatban

A Azure Cosmos DB Azure szinapszis-hivatkozása szoros integrációt hoz létre a Azure Cosmos DB és az Azure szinapszis Analytics között. Lehetővé teszi az ügyfelek számára a közel valós idejű elemzések futtatását a működési adataik teljes teljesítményének elkülönítésével a tranzakciós munkaterhelések és az ETL-folyamat nélkül. Ez a cikk az Azure Cosmos DB-hez készült Azure Synapse Linkkel kapcsolatos gyakori kérdésekre ad választ.

## <a name="general-faq"></a>Gyakori kérdések – általános

### <a name="is-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Támogatott-e a szinapszis-hivatkozás az összes Azure Cosmos DB API-hoz?

A nyilvános előzetes kiadásban a szinapszis hivatkozás a Azure Cosmos DB SQL (Core) API és a MongoDB Azure Cosmos DB API esetében támogatott. A Cassandra API támogatása jelenleg a GateD Preview-ban érhető el. Ha hozzáférést szeretne kérni a megtekintett előzetes verzióhoz, nyissa meg a [Azure Cosmos db csapatot](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="is-synapse-link-supported-for-multi-region-azure-cosmos-accounts"></a>Támogatott-e a szinapszis-hivatkozás a többrégiós Azure Cosmos-fiókok esetében?

Igen, a többrégiós Azure Cosmos-fiókok esetében az analitikus tárolóban tárolt adatforgalom is globálisan terjeszthető. Az Azure szinapszis Analytics szolgáltatásból származó, az egyetlen írási régióból (egyetlen főkiszolgálóból) vagy több írási régióból (más néven több főkiszolgálóból) származó analitikai lekérdezések a legközelebbi helyi régióból is kiszolgálható.

Ha többrégiós Azure Cosmos-fiókot szeretne konfigurálni az analitikai tár támogatásával, ajánlott az összes szükséges régiót hozzáadni a fiók létrehozásakor.

### <a name="can-i-choose-to-enable-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Engedélyezhető a szinapszis hivatkozás engedélyezése csak bizonyos régiókban, és nem minden régióban a többrégiós fiók beállítása?

Az előzetes kiadásban, ha a szinapszis-kapcsolat engedélyezve van egy többrégiós fiók esetében, az analitikai tároló minden régióban létrejön. Az alapul szolgáló adatok a tranzakciós tárolóban a teljesítményre és a tranzakciós konzisztenciare vannak optimalizálva.

### <a name="is-backup-and-restore-supported-for-synapse-link-enabled-accounts"></a>Támogatott-e a biztonsági mentés és a visszaállítás a szinapszis-hivatkozások számára engedélyezett fiókok esetében?

Az előzetes verzióban a szinapszis-kapcsolattal rendelkező adatbázis-fiókok, a tárolók biztonsági mentése és helyreállítása nem támogatott. Ha olyan éles számítási feladatokkal rendelkezik, amelyek biztonsági mentési és visszaállítási funkciókat igényelnek, javasoljuk, hogy ne engedélyezze a szinapszis-hivatkozást ezen adatbázis-fiókokon. 

### <a name="can-i-disable-the-synapse-link-feature-for-my-azure-cosmos-account"></a>Le tudom tiltani az Azure Cosmos-fiókhoz tartozó szinapszis-hivatkozás funkciót?

Jelenleg nem tilthatja le a Synapse Link képességet, miután fiókszinten engedélyezte azt.  Ha ki szeretné kapcsolni a képességet, törölnie kell, majd újra létre kell hoznia egy új Azure Cosmos-fiókot.

Tudomásul veszi, hogy **nem** lesz számlázási vonzata, ha a szinapszis kapcsolati funkciója engedélyezve van a fiók szintjén, de nincs analitikus tárolóval rendelkező tároló.

## <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analitikus tároló

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Engedélyezhető az analitikai tár a meglévő tárolókban?

Az analitikai tároló jelenleg csak új tárolók esetében engedélyezhető (az új és a meglévő fiókokban is).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-containers-after-enabling-it-during-container-creation"></a>Letilthatom az analitikus tárolót az Azure Cosmos-tárolókban, miután engedélyezte azt a tároló létrehozásakor?

Az analitikai tároló jelenleg nem tiltható le egy Azure Cosmos-tárolón, miután az engedélyezve lett a tároló létrehozásakor.

### <a name="is-analytical-store-supported-for-azure-cosmos-containers-with-autoscale-provisioned-throughput"></a>Támogatott-e az analitikus tároló az Azure Cosmos-tárolók számára az autoscale kiosztott átviteli sebességgel?

Igen, az analitikai tárolót engedélyezheti olyan tárolókban, amelyek az autoscale kiosztott átviteli sebességgel rendelkeznek.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Van-e hatása Azure Cosmos DB tranzakciós áruház kiépített RUs-re?

Azure Cosmos DB garantálja a tranzakciós és az analitikai számítási feladatok elkülönítését. Az analitikai tároló tárolón való engedélyezése nem befolyásolja az Azure Cosmos DB tranzakciós tárolóban kiosztott RU/s-t. Az analitikus tárolóra vonatkozó tranzakciók (olvasási & írás) és a tárolási költségek külön lesznek felszámítva. További részletekért tekintse [meg Azure Cosmos db analitikus áruház díjszabását](analytical-store-introduction.md#analytical-store-pricing) .

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>A tranzakciós tárolóban a DELETE és a Update művelet szerepel az analitikus tárolóban?

Igen, a tranzakciós tárolóban lévő adattörlési és-frissítési műveletek megjelennek az analitikus tárolóban. Beállíthatja, hogy az élettartam (TTL) a tárolón, hogy tartalmazza a korábbi adatokat, hogy az analitikai tár megőrizze az összes olyan verziót, amely megfelel az analitikai élettartam feltételeinek. További részletekért tekintse meg az [analitikai TTL áttekintését](analytical-store-introduction.md#analytical-ttl) .

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Csatlakozhatok az analitikus tárolóhoz az Azure szinapszis Analytics szolgáltatástól eltérő elemzési motoroktól?

Csak az Azure Synapse Analytics által biztosított különböző futtatókörnyezetekkel érhet el és futtathat lekérdezéseket az elemzési táron. Az elemzési tár a következők használatával kérdezhető le és elemezhető:

* A szinapszis Spark teljes körű támogatást nyújt a Scala, a Python, a SparkSQL és a C# nyelvhez. A Synapse Spark az adatfeldolgozási és -elemzési forgatókönyvek központi eleme
* Kiszolgáló nélküli SQL T-SQL nyelvvel és az ismert BI-eszközök támogatásával (például Power BI Premium stb.)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Csatlakozhatok az analitikus tárolóhoz a szinapszis SQL kiépített használatával?

Jelenleg az analitikai tároló nem érhető el a szinapszis SQL kiépített állapotáról.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Lehet-e visszaírni a lekérdezési összesítés eredményeit a Szinapszisból az analitikai tárolóba?

Az analitikai tár egy írásvédett tároló egy Azure Cosmos-tárolóban. Így nem tudja közvetlenül visszaírni az összesítési eredményeket az analitikai tárolóba, de megírhatja őket egy másik tároló Azure Cosmos DB tranzakciós tárolójába, amely később kihasználhatja a kiszolgálóként szolgáló réteget.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>A tranzakciós tárolóból az elemzési tárolóba aszinkron vagy szinkron módon történik az AutoSync replikáció, és mi a késés?

Az automatikus szinkronizálás késése általában 2 percen belül megtörténik. Ha a megosztott átviteli sebességű adatbázis nagy mennyiségű tárolóval rendelkezik, az egyes tárolók automatikus szinkronizálási késése magasabb lehet, és akár 5 percet is igénybe vehet. Szeretnénk többet megtudni, hogy ez a késés hogyan illeszkedik a forgatókönyvekhez. Ehhez kérjük, lépjen kapcsolatba a [Azure Cosmos db csapatával](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Vannak olyan helyzetek, amikor a tranzakciós tárolóból származó elemek nem lesznek automatikusan propagálva az analitikai tárolóba?

Ha a tárolóban meghatározott elemek megsértik a [jól meghatározott sémát az elemzéshez](analytical-store-introduction.md#analytical-schema), azok nem kerülnek bele az analitikai tárolóba. Ha az elemzéshez jól definiált séma által blokkolt forgatókönyvek vannak, küldje el a [Azure Cosmos db csapatának](mailto:cosmosdbsynapselink@microsoft.com) segítségét.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Az analitikus tárolóban lévő adatok a tranzakciós tárolótól eltérően is particionálva vannak?

Az elemzési tárban lévő adatok particionálása a tranzakciós tárolóban lévő szegmensek horizontális particionálásán alapul. Jelenleg nem választhat más particionálási stratégiát az elemzési tárhoz.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Testreszabható vagy felülbírálható a tranzakciós adatértékek oszlopos formátumba való átalakítása az analitikai tárolóban?

Az adatelemek jelenleg nem alakíthatók át, ha automatikusan propagálják őket a tranzakciós tárolóból az analitikai tárolóba. Ha ezt a korlátozást a forgatókönyvek blokkolják, küldje el a [Azure Cosmos db csapatnak](mailto:cosmosdbsynapselink@microsoft.com).

## <a name="analytical-time-to-live-ttl"></a>Analitikai élettartam (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>A a tárolók és az elemek szintjén támogatott analitikai adatértékek élettartama?

Jelenleg az analitikai adatértékek élettartama csak a tároló szintjén konfigurálható, és nem támogatott az analitikai élettartam beállítása az elem szintjén.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Miután beállította a tároló szintjének analitikai ÉLETTARTAMát egy Azure Cosmos DB tárolón, másik értékre válthatok később?

Igen, az analitikai élettartam bármely érvényes értékre frissíthető. Az analitikai ÉLETTARTAMmal kapcsolatos további információkért tekintse meg az [analitikai TTL](analytical-store-introduction.md#analytical-ttl) című cikket.

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Frissíthetek vagy törölhetek egy elemet az analitikus tárolóból, miután a tranzakciós tárolóból már ÉLETTARTAMa van?

Az összes tranzakciós frissítést és törlést a rendszer átmásolja az analitikai tárolóba, de ha az elemet törölte a tranzakciós tárolóból, akkor az nem frissíthető az analitikai tárolóban. További információért lásd az [analitikai élettartamot](analytical-store-introduction.md#analytical-ttl) ismertető cikket.

## <a name="billing"></a>Számlázás

### <a name="what-is-the-billing-model-of-synapse-link-for-azure-cosmos-db"></a>Mi a Azure Cosmos DB-ra vonatkozó szinapszis-hivatkozás számlázási modellje?

A [Azure Cosmos db Analytical Store](analytical-store-introduction.md) nyilvános előzetes kiadásban érhető el, az analitikai tár díja nélkül, 2020. augusztus 30-ig. A szinapszis Spark és a szinapszis SQL számlázása a [szinapszis szolgáltatás felhasználásán](https://azure.microsoft.com/pricing/details/synapse-analytics/)keresztül történik.

## <a name="security"></a>Biztonság

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Milyen módon lehet hitelesíteni az analitikai tárolót?

Az analitikai tárolóval való hitelesítés megegyezik a tranzakciós tárolóval. Egy adott adatbázis esetében a Master vagy a csak olvasható kulccsal végezheti el a hitelesítést. A szinapszis Studióban használhatja a társított szolgáltatást, hogy megakadályozza a Azure Cosmos DB kulcsok beillesztését a Spark-jegyzetfüzetekben. A társított szolgáltatáshoz való hozzáférés mindenki számára elérhető, aki hozzáfér a munkaterülethez.

## <a name="synapse-run-times"></a>Szinapszis-futtatási idő

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Mi a jelenleg támogatott szinapszis-futtatási idő a Azure Cosmos DB Analytical Store-hoz való hozzáféréshez?

|Szinapszis-futtatókörnyezet |Jelenlegi támogatás |
|---------|---------|
|Szinapszis Spark-készletek | Olvasás, írás (tranzakciós tárolón keresztül), tábla, ideiglenes nézet |
|Szinapszis SQL Server nélküli    | Olvasás, megtekintés |
|A szinapszis SQL kiépítve   |  Nem elérhető |

### <a name="do-my-synapse-spark-tables-sync-with-my-synapse-sql-serverless-tables-the-same-way-they-do-with-azure-data-lake"></a>Hogyan szinkronizálhatók a szinapszis Spark-táblái a szinapszis SQL Server nélküli tábláival, ugyanúgy, mint a Azure Data Lake?

Ez a funkció jelenleg nem érhető el.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Használhatom a Spark strukturált streamet az analitikus áruházból?

Az Azure Cosmos DB jelenleg Spark strukturált adatfolyam-támogatást nyújt a tranzakciós tároló változási funkciójának módosításával, és ez a módszer még nem támogatott az analitikai tárolóban.

## <a name="synapse-studio"></a>Synapse Studio

### <a name="in-the-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>A szinapszis Studióban hogyan ismerhető fel, ha az Analytics-tárolót engedélyező Azure Cosmos DB-tárolóhoz csatlakozik?

Az analitikai tárolóval engedélyezett Azure Cosmos DB tárolóban a következő ikon látható:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Azure Cosmos DB tároló engedélyezve az analitikai tárolóval – ikon":::

A rendszer a következő ikonnal fogja megjeleníteni a tranzakciós tároló tárolóját:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Azure Cosmos DB tároló engedélyezve a tranzakciós tárolóval – ikon":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-synapse-studio"></a>Hogyan továbbíthatja Azure Cosmos DB hitelesítő adatait a szinapszis studióból?

Jelenleg Azure Cosmos DB hitelesítő adatokat a rendszer a társított szolgáltatásnak a Azure Cosmos DB adatbázisokhoz hozzáféréssel rendelkező felhasználó általi létrehozásakor adja át. A tárolóhoz való hozzáférés a munkaterülethez hozzáférő más felhasználók számára is elérhető.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a [szinapszis-hivatkozás előnyeiről](synapse-link.md#synapse-link-benefits)

* Ismerkedjen meg a [szinapszis-kapcsolat és a Azure Cosmos db közötti integrációval](synapse-link.md#synapse-link-integration).
