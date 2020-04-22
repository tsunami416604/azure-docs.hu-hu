---
title: Gyakori kérdések az Azure Cosmos DB API-ról Cassandra számára.
description: Válaszok at kaphat a Cassandra Azure Cosmos DB API-val kapcsolatos gyakori kérdésekre.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 416f0c5f995a101298e84c81317c7d39fb5d43f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727384"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-db-api-for-cassandra"></a>Gyakori kérdések a Cassandra Azure Cosmos DB API-jával kapcsolatban

## <a name="what-are-some-key-differences-between-apache-cassandra-and-cassandra-api"></a>Milyen fontos különbségek vannak az Apache Cassandra és a Cassandra API között?

- Az Apache Cassandra 100 MB-os korlátot javasol a partíciókulcs méretére vonatkozóan. Cassandra API lehetővé teszi, hogy partíciónként legfeljebb 10 GB.Cassandra API allows up to 10GB per partition.
- Az Apache Cassandra lehetővé teszi a tartós véglegesítések letiltását - azaz hagyja ki az írást a véglegesítési naplóba, és közvetlenül a Memtable(ek)hez lépjen. Ez adatvesztéshez vezethet, ha a csomópont leáll, mielőtt a Memtables kiürítése a SStables lemezre. Cosmos DB mindig tartós véglegesíti, így soha nem lesz adatvesztés.
- Az Apache Cassandra láthatja a csökkent teljesítményt, ha a munkaterhelés sok lecserélést és/vagy törlést tartalmaz. Ennek oka a törlési kövek, hogy az olvasási számítási feladatok at kell átugrani a legújabb adatok beolvasása. Cassandra API nem fogja látni a csökkent olvasási teljesítmény, ha a számítási feladatok sok lecseréli és/vagy törli.
- A magas csereszámítási forgatókönyvek során a tömörítésnek futnia kell az SSTables lemezen való egyesítéséhez (az egyesítésre azért van szükség, mert az Apache Cassandra írásai csak hozzáfűzést tartalmaznak, így több frissítés tárolódik különálló SSTable-bejegyzésekként, amelyeket rendszeresen egyesíteni kell). Ez azt is eredményezheti, hogy a tömörítés során csökkent az olvasási teljesítmény. Ez nem fordul elő a Cassandra API-ban, mivel nem valósítmeg tömörítést.
- Az Apache Cassandra 1 replikációs tényezőjének beállítása lehetséges. Azonban ez alacsony rendelkezésre állást eredményez, ha az egyetlen csomópont az adatok leáll. Ez nem probléma az Azure Cosmos DB Cassandra API-val, mert mindig van egy 4-es replikációs tényező (3 kvóruma).
- Az Apache Cassandra csomópontjainak hozzáadása/eltávolítása sok manuális beavatkozást igényel, de magas CPU-t is az új csomóponton, miközben a meglévő csomópontok áthelyezik a tokentartományok egy részét az új csomópontba. Ez ugyanaz, ha egy meglévő csomópontot denincs leszerel. A horizontális felskálázás azonban zökkenőmentesen történik az Azure Cosmos DB Cassandra API-ban, a szolgáltatásban/alkalmazásban megfigyelt problémák nélkül.
- Nincs szükség num_tokens a fürt minden egyes csomópontjára, mint az Apache Cassandra. A csomópontokat és a tokentartományokat a Cosmos DB teljes körűen kezeli.
- Az Azure Cosmos DB Cassandra API teljes körűen felügyelt, így nincs szükség a csomóponti eszközök parancsok, például a javítás, leszerelés, stb, amelyek az Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Egyéb gyakori kérdések

### <a name="what-is-the-protocol-version-supported-by-azure-cosmos-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Mi az Azure Cosmos DB Cassandra API által támogatott protokollverzió? Van terv más protokollok támogatására?

Az Azure Cosmos DB Cassandra API támogatja a CQL 3.x-es verzióját. A CQL-kompatibilitás a nyilvános [Apache Cassandra GitHub tárházon](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile)alapul. Ha visszajelzést kap más protokollok támogatásáról, tudassa velünk a felhasználói [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) [hangvisszajelzést,](https://feedback.azure.com/forums/263030-azure-cosmos-db) vagy küldjön e-mailt a rendszernek.

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Miért követelmény egy tábla átviteli igényének kiválasztása?

Az Azure Cosmos DB alapátviteli értéket állít be a tárolóhoz annak alapján, hogy honnan hozza létre a táblát - portálról vagy CQL-ből.
Az Azure Cosmos DB garantálja a teljesítményt és a késést, a működés felső határa. Ez a garancia akkor lehetséges, ha a motor kényszerítheti a bérlő műveleteinek irányítását. Az átviteli képesség beállítása biztosítja, hogy megkapja a garantált átviteli és késési, mert a platform fenntartja ezt a kapacitást, és garantálja a működés sikerességét.
[Rugalmasan módosíthatja az átviteli szintet,](manage-scale-cassandra.md) hogy kihasználhassa az alkalmazás szezonalitását, és költségeket takaríthat meg.

Az átviteli koncepció az [Azure Cosmos DB-ben található kérelemegységek](request-units.md) ismerteti. A tábla átviteli forgalma egyenlően oszlik meg az alapul szolgáló fizikai partíciók között.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Mi a tábla alapértelmezett RU/s-a cql-on keresztül létrehozva? Mi a teendő, ha meg kell változtatnom?

Az Azure Cosmos DB kérelemegység/másodperc (RU/s) pénznemként használja az átviteli értéket. A CQL-en keresztül létrehozott táblák 400 RU-val rendelkeznek. A vt.-t a portálról módosíthatja.

CQL

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Mi történik, ha az átviteli áteresztőáltal használt?

Az Azure Cosmos DB garantálja a teljesítményt és a késést, a működés felső határa. Ez a garancia akkor lehetséges, ha a motor kényszerítheti a bérlő műveleteinek irányítását. Ez az átviteli képesség beállítása alapján lehetséges, amely biztosítja a garantált átviteli és késési kapacitást, mivel a platform fenntartja ezt a kapacitást, és garantálja a működés sikerét.
Ha túllépi ezt a kapacitást, túlterhelt hibaüzenet jelenik meg, amely jelzi, hogy a kapacitás a használt volt.
0x1001 Túlterhelt: a kérés nem dolgozható fel, mert a "Kérelem aránya nagy". Ebben a helyzetben fontos, hogy lássuk, milyen műveletek és azok mennyisége okozza ezt a problémát. A portálon metrikákkal rendelkező felhasznált kapacitásról képet kaphat. Ezután biztosítania kell, hogy a kapacitás szinte egyenlően kerüljön felhasználásra az összes alapul szolgáló partíción. Ha látja, hogy az átviteli hang nagy részét egy partíció használja fel, akkor a munkaterhelés ferde.

Metrikák érhetők el, amelyek megmutatják, hogyan átviteli órákban, napokon és hét naponként, partíciók on vagy összesítve. További információ: [Figyelés és hibakeresés metrikákkal az Azure Cosmos DB-ben.](use-metrics.md)

A diagnosztikai naplók az [Azure Cosmos DB diagnosztikai naplózási](logging.md) cikkben találhatók.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Az elsődleges kulcs leképezi az Azure Cosmos DB partíciókulcs-koncepcióját?

Igen, a partíciókulcs az entitás megfelelő helyre való elhelyezéséhez használatos. Az Azure Cosmos DB-ben a fizikai partíción tárolt megfelelő logikai partíció megkereséséhez használható. A particionálási koncepció jól ismert az [Azure Cosmos DB-cikkben](partition-data.md) a partíció és a méretezés. Az alapvető elvenni itt az, hogy a logikai partíció nem megy át a 10 GB-os korlátot ma.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Mi történik, ha a kvóta teljes" értesítést kapok, amely jelzi, hogy egy partíció megtelt?

Az Azure Cosmos DB egy SLA-alapú rendszer, amely korlátlan skálázást biztosít, garanciákkal a késés, az átviteli, a rendelkezésre állás és a konzisztencia. Ez a korlátlan tárterület alapul horizontális horizontális adatskálázás particionálás, mint a kulcs fogalom használatával. A particionálási koncepció jól ismert az [Azure Cosmos DB-cikkben](partition-data.md) a partíció és a méretezés.

A 10 GB-os korlát az entitások vagy elemek logikai partíciónként i kell tartania. Annak érdekében, hogy az alkalmazás méretezése jól, azt javasoljuk, hogy *ne* hozzon létre egy gyorsválasztó partíciót az összes információ tárolása egy partíción, és lekérdezi azt. Ez a hiba csak akkor jöhet, ha az adatok ferdeek: azaz sok adat&nbsp;van egy partíciókulcshoz (több mint 10 GB). Az adatok eloszlása a tárolóportálon található. A hiba megoldása a tábla újbóli létrehozása és egy részletes elsődleges (partíciókulcs) kiválasztása, amely lehetővé teszi az adatok jobb elosztását.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Használható-e a Cassandra API kulcsérték-tárolóként több millió vagy milliárd egyedi partíciókulcssal?

Az Azure Cosmos DB korlátlan adattárolást biztosíthat a tárterület horizontális felskálázásával. Ez független az átviteli. Igen, mindig használhatja a Cassandra API-t a kulcs/értékek tárolásához és lekéréséhez a jobb elsődleges/partíciókulcs megadásával. Ezek az egyes kulcsok kap saját logikai partíciót, és üljön a fizikai partíció problémák nélkül.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Létrehozhat egynél több táblát az Azure Cosmos DB Apache Cassandra API-jával?

Igen, az Apache Cassandra API-val több táblát is létrehozhat. Minden ilyen táblák az átviteli és tárolási egységként kezelik.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Lehet-e egymás után több táblát létrehozni?

Az Azure Cosmos DB az adat- és vezérlősík-tevékenységek erőforrás által szabályozott rendszere. Tárolók, például a gyűjtemények, táblák futásidejű entitások, amelyek ki vannak építve adott átviteli kapacitás. Ezek a tárolók létrehozása gyors egymásutánban nem várható tevékenység és szabályozott. Ha olyan tesztjei vannak, amelyek azonnal eldobják/létre hozzák a táblákat, próbálja meg kiterezni őket.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Mi a létrehozható táblák maximális száma?

Nincs fizikai korlátozás a táblák száma, küldjön [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) egy e-mailt, ha nagy számú táblák (ahol a teljes állandó mérete meghaladja a 10 TB-os adatok), hogy létre kell hozni a szokásos 10s vagy 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Mi a maximális # keyspace, hogy tudunk létrehozni?

Nincs fizikai korlát a kulcsterek száma, mivel azok metaadat-tárolók, küldjön egy e-mailt, [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) ha valamilyen okból nagy számú kulcsterülettel rendelkezik.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Lehetséges-e sok adatot behozni a normál táblából való kiindulat után?

Igen, feltéve, hogy az egyenletesen elosztott partíciók, a tárolókapacitás automatikusan kezeli, és növekszik, ahogy több adatot. Így magabiztosan importálhat annyi adatot, amennyire szüksége van a csomópontok kezelése és kiépítése nélkül, és így tovább. Ha azonban sok azonnali adatnövekedésre számít, akkor több értelme van [közvetlenül a várható átviteli sebességgel való ellátásnak,](set-throughput.md) ahelyett, hogy alacsonyabb anamnézist indítana, és azonnal növelné azt.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Meg adható yaml fájlbeállítások az Azure Cosmos DB viselkedésapache Casssandra API-jának konfigurálásához?

Az Azure Cosmos DB Apache Cassandra API-ja egy platformszolgáltatás. Protokollszintű kompatibilitást biztosít a műveletek végrehajtásához. Elrejti a felügyelet, a figyelés és a konfiguráció összetettségét. Fejlesztőként/felhasználóként nem kell aggódnia a rendelkezésre állás, a törlési kövek, a kulcsgyorsítótár, a sorgyorsítótár, a virágzásszűrő és az egyéb beállítások sokasága miatt. Az Azure Cosmos DB Apache Cassandra API-ja olyan olvasási és írási teljesítmény biztosítására összpontosít, amelya konfiguráció és a felügyelet többletterhelése nélkül szükséges.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Az Apache Cassandra API az Azure Cosmos DB-hez támogatja a csomópont-összeadást/fürt állapot/csomópont állapotparancsokat?

Az Apache Cassandra API egy platformszolgáltatás, amely megkönnyíti a kapacitástervezést, és megfelel az átviteli & tárolási rugalmassági igényeknek. Az Azure Cosmos DB-vel kikell építenie az átviteli, szükséges. Ezután a nap folyamán tetszőleges számú alkalommal skálázhatja anélkül, hogy aggódnia kellene a csomópontok hozzáadása/törlése vagy kezelése miatt. Ez azt jelenti, hogy nem kell használnia a csomópontot, a fürtkezelő eszközt is.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Mi történik a keyspace létrehozása különböző konfigurációs beállításaival kapcsolatban, például az egyszerű/hálózat?

Az Azure Cosmos DB globális terjesztést biztosít a rendelkezésre állás és az alacsony késés miatt. Nem kell replikákat vagy más dolgokat beállítania. Minden írási mindig tartósan kvórum elkötelezett minden olyan régióban, ahol ír, miközben teljesítménygaranciákat.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Mi történik a tábla metaadatainak különböző beállításaival kapcsolatban, például a virágzásszűrővel, a gyorsítótárazással, az olvasási javítási módosítással, a gc_grace, a tömörítési memtable_flush_period stb.

Az Azure Cosmos DB az olvasási/írási és átviteli teljesítmény teljesítményét anélkül biztosítja, hogy meg kellene érintenie a konfigurációs beállításokat, és véletlenül manipulálnia kellene őket.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Az élő (TTL) támogatja a Cassandra táblák?

Igen, a TTL támogatott.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Lehet-e figyelni a csomópont állapotát, replika állapotát, a gc és az operációs rendszer paramétereit korábban különböző eszközökkel? Mit kell most ellenőrizni?

Az Azure Cosmos DB egy platformszolgáltatás, amely segít növelni a termelékenységet, és nem kell aggódnia az infrastruktúra kezelése és figyelése miatt. Csak a portálmetrikákon elérhető átviteli teljesítményről kell gondoskodnia, hogy megkeresse, ha szabályozásalá kerül, és növeli vagy csökkenti az átviteli fésületi.
Monitor [SlAs](monitor-accounts.md).
[Metrikák](use-metrics.md) használata [Diagnosztikai naplók használata](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Mely ügyfél-SDK-k működhetnek az Azure Cosmos DB Apache Cassandra API-jával?

Az Apache Cassandra SDK CQLv3-at használó ügyfél-illesztőprogramjait ügyfélprogramokhoz használták. Ha más illesztőprogramokat is használ, vagy ha problémákkal [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)szembesül, küldjön e-mailt a alkalmazásba.

### <a name="is-composite-partition-key-supported"></a>Támogatott az összetett partíciókulcs?

Igen, a normál szintaxissal összetett partíciókulcsot hozhat létre.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Használhatom a sstableloadert az adatok betöltéséhez?

Nem, a sstableloader nem támogatott.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Párosítható egy helyszíni Apache Cassandra-fürt az Azure Cosmos DB Cassandra API-jával?

Jelenleg az Azure Cosmos DB egy optimalizált élményt felhőalapú környezetben, a műveletek terhelése nélkül. Ha párosításra van szüksége, [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) küldjön e-mailt a forgatókönyv leírásával. Dolgozunk kínál kínál, hogy segítsen párosítani a helyszíni/különböző felhő Cassandra klaszter Cosomos DB Cassandra API.We are working on offering to help pair the on-premises/different cloud Cassandra cluster to Cosomos DB's Cassandra API.

### <a name="does-cassandra-api-provide-full-backups"></a>A Cassandra API teljes biztonsági mentést biztosít?

Az Azure Cosmos DB két ingyenes teljes biztonsági mentést biztosít, amelyeket ma négy órás időközönként az összes API-ban készít. Ez biztosítja, hogy nem kell biztonsági mentési ütemezést és egyéb dolgokat beállítania.
Ha módosítani szeretné a megőrzést és [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) a gyakoriságot, küldjön egy e-mailt, vagy emelje fel a támogatási esetet. A biztonsági mentési képességről az Automatikus online biztonsági mentés és visszaállítás az [Azure Cosmos DB](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) cikkben található.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hogyan kezeli a Cassandra API-fiók feladatátvételt, ha egy régió leáll?

Az Azure Cosmos DB Cassandra API az Azure Cosmos DB globálisan elosztott platformjáról kölcsönöz. Annak érdekében, hogy az alkalmazás tolerálja az adatközpont állásidejét, engedélyezze legalább még egy régiót a fiókhoz az Azure Cosmos DB portál [fejlesztése többrégiós Azure Cosmos DB-fiókokkal.](high-availability.md) Beállíthatja a régió prioritását a [Fejlesztés többrégiós Azure Cosmos DB-fiókokkal portál](high-availability.md)használatával.

Annyi régiót adhat hozzá, amennyit csak szeretne a fiókhoz, és azt a vezérlőt, ahol feladatátvételi prioritás térhet át. Az adatbázis használatához ott is meg kell adnia egy alkalmazást. Ha így tesz, az ügyfelek nem tapasztalnak leállást.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Az Apache Cassandra API alapértelmezés szerint indexeli egy entitás összes attribútumát?

Nem. Cassandra API támogatja a [másodlagos indexek](cassandra-secondary-index.md), amely nagyon hasonló módon viselkedik az Apache Cassandra. Alapértelmezés szerint nem indexel minden attribútumot.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Használhatom az új Cassandra API SDK-t helyileg az emulátorral?

Igen, ez támogatott. Itt talál részleteket arról, hogyan lehet [ezt](local-emulator.md#cassandra-api) engedélyezni


### <a name="how-can-i-migrate-data-from-their-apache-cassandra-clusters-to-cosmos-db"></a>Hogyan telepíthetek át adatokat az Apache Cassandra-fürtökről a Cosmos DB-be?

Az áttelepítési lehetőségekről [itt olvashat.](cassandra-import-data.md)


### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>A hagyományos Cassandra API x funkciója nem működik, mint ma, hol lehet visszajelzést adni?

Visszajelzés küldése [a felhasználói hangvisszajelzésen](https://feedback.azure.com/forums/263030-azure-cosmos-db)keresztül.

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>További lépések

- Az [Azure Cosmos DB Cassandra API-fiók rugalmas méretezésével ismerkedjenek meg.](manage-scale-cassandra.md)
