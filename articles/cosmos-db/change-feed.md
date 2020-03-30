---
title: A módosítási hírcsatorna támogatásának támogatása az Azure Cosmos DB-ben
description: Az Azure Cosmos DB módosítási hírcsatorna-támogatással nyomon követheti a dokumentumok változásait, eseményalapú feldolgozást, például eseményindítókat, és naprakészen tarthatja a gyorsítótárakat és az analitikus rendszereket
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 898dfe7a619981b93af98effa942fdecbeb42dde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368128"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Változáscsatorna az Azure Cosmos DB-ben – Áttekintés

A változáscsatorna támogatása az Azure Cosmos DB-ben egy Azure Cosmos-tároló változásainak figyelésével működik. Ezután a módosításuk sorrendjében felsorolja a módosított dokumentumokat. A módosítások meg lesznek őrizve, feldolgozhatók aszinkron és fokozatos módon is, a kimenet pedig több fogyasztó között is elosztható a párhuzamos feldolgozás érdekében. 

Az Azure Cosmos DB kiválóan alkalmas IoT-, játék-, kiskereskedelmi és operatív naplózási alkalmazásokhoz. Ezekben az alkalmazásokban gyakori tervezési minta az adatok módosításai további műveletek indításához. További műveletek például a következők:

* Értesítés vagy API-hívás aktiválása, egy elem beszúrásakor vagy frissítésekor.
* Valós idejű adatfolyam-feldolgozás IoT-hez vagy valós idejű elemzési feldolgozáshoz a működési adatokon.
* További adatmozgatás a gyorsítótárral vagy a keresőmotorral vagy adattárházlal való szinkronizálással, vagy az adatok hidegtárba való archiválásával.

Az Azure Cosmos DB módosítási hírcsatornája lehetővé teszi, hogy hatékony és méretezhető megoldásokat hozzon létre ezekhez a mintákhoz, ahogy az az alábbi képen látható:

![Az Azure Cosmos DB módosítási hírcsatornájának használata a valós idejű elemzések és az eseményvezérelt számítástechnikai forgatókönyvek teljesítményéhez](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Támogatott API-k és ügyfél SDK-k

Ezt a funkciót jelenleg a következő Azure Cosmos DB API-k és ügyfél SDK-k támogatják.

| **Ügyfélillesztők** | **Azure CLI** | **SQL API** | **Az Azure Cosmos DB Cassandra-hoz elérhető API-ja** | **MongoDB-hez készült Azure Cosmos DB API** | **Gremlin API**|**Tábla API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | NA | Igen | Igen | Igen | Igen | Nem |
|Java|NA|Igen|Igen|Igen|Igen|Nem|
|Python|NA|Igen|Igen|Igen|Igen|Nem|
|Csomópont/JS|NA|Igen|Igen|Igen|Igen|Nem|

## <a name="change-feed-and-different-operations"></a>A hírcsatorna és a különböző műveletek módosítása

Ma láthatja az összes műveletet a változás hírcsatornában. Az a funkció, amelyen szabályozhatja a változáscsatornát, bizonyos műveletekhez, például csak a frissítésekhez, és nem a beszúrásokhoz, még nem érhető el. Hozzáadhat egy "puha jelölőt" az elemhez a frissítésekhez, és szűrhet a módosítási hírcsatorna elemeinek feldolgozásakor. Jelenleg a módosítási hírcsatorna nem naplózza a törléseket. Az előző példához hasonlóan hozzáadhat egy lágy jelölőt a törölt elemekhez, például hozzáadhat egy attribútumot a "törölt" elemhez, és beállíthatja azt "igaz" értékre, és beállíthat egy TTL-t az elemen, hogy automatikusan törölhető legyen. Elolvashatja a korábbi elemek módosítási hírcsatornáját (a cikknek megfelelő legutóbbi módosítás, nem tartalmazza a köztes módosításokat), például az öt évvel ezelőtt hozzáadott cikkeket. Ha az elem nem törlődik, a módosítási hírcsatornát a tároló eredetéig olvashatja.

### <a name="sort-order-of-items-in-change-feed"></a>A módosítási hírfolyamelemeinek rendezési sorrendje

A módosítási hírcsatorna-elemek a módosítási idejük sorrendjében érkeznek. Ez a rendezési sorrend logikai partíciókulcsonként garantált.

### <a name="consistency-level"></a>Konzisztenciaszint

A módosítási hírcsatorna végső konzisztenciaszinten történő bevitele közben ismétlődő események lehetnek a későbbi változáscsatorna olvasási műveletek között (az egyik olvasási művelet utolsó eseménye jelenik meg a következő közül az elsőként).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Csatorna módosítása több régióra kiterjedő Azure Cosmos-fiókokban

Egy több régióból álló Azure Cosmos-fiókban, ha egy írási régió átadja a feladatátvételt, a módosítási hírcsatorna a manuális feladatátvételi műveletben fog működni, és összefüggő lesz.

### <a name="change-feed-and-time-to-live-ttl"></a>A hírcsatorna és az élő idő módosítása (TTL)

Ha egy TTL (Time to Live) tulajdonság -1 értékre van állítva, a módosítási hírcsatorna örökre megmarad. Ha az adatok nem törlődnek, a módosítási hírcsatornában maradnak.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Hírcsatorna és _etag módosítása, _lsn vagy _ts

A _etag formátum belső, és nem szabad függőséget vállalnia tőle, mert bármikor megváltozhat. _ts módosítás vagy létrehozási időbélyeg. A _ts időrendi összehasonlításra is használhatja. _lsn olyan kötegazonosító, amely csak a módosítási hírcsatorna számára van hozzáadva; a tranzakcióazonosítót jelöli. Sok elem nek lehet ugyanaz a _lsn. Az ETag a FeedResponse-on eltér az elemen látható _etag. _etag egy belső azonosító, és az egyidejűség-ellenőrzéshez a cikk verziójáról szól, míg az ETag a hírcsatorna szekvenálásához használatos.

## <a name="change-feed-use-cases-and-scenarios"></a>Hírcsatorna-használati esetek és esetek módosítása

A változáscsatorna lehetővé teszi a nagy adathalmazok hatékony feldolgozását nagy mennyiségű írással. A módosítási hírcsatorna a teljes adatkészlet lekérdezésének alternatívájaként is kínál, hogy azonosítsa a változásokat.

### <a name="use-cases"></a>Használati esetek

A módosítási hírcsatornával például a következő feladatokat végezheti el hatékonyan:

* Frissítse a gyorsítótárat, frissítsen egy keresési indexet, vagy frissítsen egy adatraktárt az Azure Cosmos DB-ben tárolt adatokkal.

* Alkalmazásszintű adatrétegezést és archiválást valósítson meg, például "gyakori adatokat" tároljon az Azure Cosmos DB-ben, és "hideg adatokat" ítson más tárolórendszerekre, például [az Azure Blob Storage-ba.](../storage/common/storage-introduction.md)

* Nulla állásidő-áttelepítést hajtson végre egy másik Azure Cosmos-fiókba vagy egy másik logikai partíciós kulccsal rendelkező másik Azure Cosmos-tárolóba.

* A [lambda architektúra megvalósítása](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) az Azure Cosmos DB használatával, ahol az Azure Cosmos DB támogatja a valós idejű, a kötegelt és a lekérdezéskiszolgáló rétegeket, így lehetővé téve a lambda architektúrát alacsony TCO-val.

* Eseményadatok fogadása és tárolása eszközökről, érzékelőkről, infrastruktúráról és alkalmazásokról, valamint az események valós idejű feldolgozása, például a [Spark](../hdinsight/spark/apache-spark-overview.md)használatával.  Az alábbi képen bemutatja, hogyan valósíthatja meg a lambda architektúrát az Azure Cosmos DB használatával a módosítási hírcsatorna segítségével:

![Azure Cosmos DB-alapú lambda-folyamat betöltéshez és lekérdezéshez](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Forgatókönyvek

Az alábbiakban néhány olyan forgatókönyvet, amelyet könnyedén megvalósíthat a módosítási hírcsatornával:

* A [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) webes vagy mobilalkalmazásokon belül nyomon követheti az olyan eseményeket, mint például az ügyfél profiljának, preferenciáinak vagy helyének összes változása, és bizonyos műveleteket indíthat el, például leküldéses értesítéseket küldhet az eszközeiknek az [Azure Functions](change-feed-functions.md)használatával.

* Ha az Azure Cosmos DB-t használja egy játék létrehozásához, használhatja például a változáscsatornát a befejezett játékok pontszámai alapján valós idejű ranglisták megvalósításához.


## <a name="working-with-change-feed"></a>A módosítási hírcsatorna megmunkálata

A módosítási hírcsatornával az alábbi beállításokkal dolgozhat:

* [Módosítási hírcsatorna használata az Azure Functions funkcióval](change-feed-functions.md)
* [Módosítási hírcsatorna használata a hírcsatorna-feldolgozóval](change-feed-processor.md) 

A változáscsatorna a tárolón belül minden logikai partíciókulcshoz elérhető, és egy vagy több fogyasztó között elosztható párhuzamos feldolgozásra, ahogy az az alábbi képen látható.

![Az Azure Cosmos DB módosítási hírcsatornájának elosztott feldolgozása](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>A változáshírcsatorna jellemzői

* A módosítási hírcsatorna alapértelmezés szerint engedélyezve van az összes Azure Cosmos-fiókhoz.

* A kiosztott [átviteli-állás](request-units.md) segítségével a változáscsatorna, mint bármely más Azure Cosmos DB-művelet, az Azure Cosmos-adatbázishoz társított régiók bármelyikében.

* A módosítási hírcsatorna beszúrásokat és frissítési műveleteket tartalmaz a tárolón belüli elemeken. A törléseket úgy rögzítheti, hogy az elemeken (például dokumentumokon) belül egy "helyreállítható törlés" jelzőt állít be a törlések helyett. Másik lehetőségként beállíthat egy véges lejárati időszakot az elemekhez a [TTL funkcióval.](time-to-live.md) Például 24 óra, és használja az értékét, hogy a tulajdonság rögzíti törli. Ezzel a megoldással a módosításokat a TTL lejárati időszaknál rövidebb időintervallumon belül kell feldolgoznia. 

* Az elem minden módosítása pontosan egyszer jelenik meg a módosítási hírcsatornában, és az ügyfeleknek kezelniük kell az ellenőrzőpont-kezelési logikát. Ha el szeretné kerülni az ellenőrzőpontok kezelésének összetettségét, a változáscsatorna-feldolgozó automatikus ellenőrzőpontokat és "legalább egyszer" szemantikát biztosít. Lásd: [a változáscsatorna használata a változáscsatorna-processzorral](change-feed-processor.md).

* A változásnapló csak az adott elem legutóbbi módosítása szerepel. Előfordulhat, hogy a köztes módosítások nem érhetők el.

* A módosítási hírcsatorna az egyes logikai partíciókulcs-értékeken belüli módosítási sorrend szerint van rendezve. Nincs garantált sorrend a partíciókulcs-értékek között.

* A módosítások bármely időpontban szinkronizálhatók, azaz nincs rögzített adatmegőrzési időszak, amelyhez módosítások érhetők el.

* A módosítások párhuzamosan érhetők el az Azure Cosmos-tároló összes logikai partíciókulcsához. Ez a funkció lehetővé teszi, hogy a nagy tárolók módosításait több fogyasztó párhuzamosan dolgozza fel.

* Az alkalmazások egyszerre több módosítási hírcsatornát is kérhetnek ugyanazon a tárolón. A ChangeFeedOptions.StartTime a kezdeti kiindulási pont megadására használható. Például egy adott óraidőnek megfelelő folytatási token megkereséséhez. A ContinuationToken, ha meg van adva, megnyeri a StartTime és a StartFromBeginning értékeket. A ChangeFeedOptions.StartTime pontossága ~5 másodperc. 

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>A Cassandra és a MongoDB API-kban való módosítása

A változáscsatorna-funkció változásfolyamként kerül felszínre a MongoDB API-ban és a Queryben a Cassandra API predikátumával. Ha többet szeretne megtudni a MongoDB API megvalósítási részleteiről, tekintse meg a [Streamek módosítása az Azure Cosmos DB API-ban a MongoDB-hoz című témakört.](mongodb-change-streams.md)

A natív Apache Cassandra biztosítja a változási adatok rögzítését (CDC), egy olyan mechanizmust, amely meghatározott táblákat jelöl meg archiváláshoz, valamint elutasítja az írásokat ezekre a táblákra, amint a CDC-napló konfigurálható méretű lemeze eléri. A cassandrai Azure Cosmos DB API változáscsatorna-szolgáltatása növeli a módosítások lekérdezésének lehetőségét a CQL-en keresztüli predikátummal. Ha többet szeretne megtudni a megvalósítás részleteiről, olvassa [el a Hírcsatorna módosítása az Azure Cosmos DB API cassandra.](cassandra-change-feed.md)

## <a name="next-steps"></a>További lépések

Most a következő cikkekben olvashat bővebben a változáscsatornáról:

* [A módosítási hírcsatorna olvasásának beállításai](read-change-feed.md)
* [Módosítási hírcsatorna használata az Azure Functions funkcióval](change-feed-functions.md)
* [A módosítási adatcsatorna-processzor használata](change-feed-processor.md)
