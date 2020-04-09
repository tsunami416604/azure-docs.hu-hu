---
title: A módosítási hírcsatorna támogatásának támogatása az Azure Cosmos DB-ben
description: Az Azure Cosmos DB módosítási hírcsatorna-támogatással nyomon követheti a dokumentumok változásait, eseményalapú feldolgozást, például eseményindítókat, és naprakészen tarthatja a gyorsítótárakat és az analitikus rendszereket
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: e36e95aeb25c83ccd94f11e25bfe9f1b8f7bfdad
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984861"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Változáscsatorna az Azure Cosmos DB-ben

A változáscsatorna támogatása az Azure Cosmos DB-ben egy Azure Cosmos-tároló változásainak figyelésével működik. Ezután a módosításuk sorrendjében felsorolja a módosított dokumentumokat. A módosítások meg lesznek őrizve, feldolgozhatók aszinkron és fokozatos módon is, a kimenet pedig több fogyasztó között is elosztható a párhuzamos feldolgozás érdekében.

További információ a [hírcsatorna tervezési mintáinak módosításáról.](change-feed-design-patterns.md)

## <a name="supported-apis-and-client-sdks"></a>Támogatott API-k és ügyfél SDK-k

Ezt a funkciót jelenleg a következő Azure Cosmos DB API-k és ügyfél SDK-k támogatják.

| **Ügyfélillesztők** | **SQL API** | **Az Azure Cosmos DB Cassandra-hoz elérhető API-ja** | **MongoDB-hez készült Azure Cosmos DB API** | **Gremlin API**|**Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Igen | Igen | Igen | Igen | Nem |
|Java|Igen|Igen|Igen|Igen|Nem|
|Python|Igen|Igen|Igen|Igen|Nem|
|Csomópont/JS|Igen|Igen|Igen|Igen|Nem|

## <a name="change-feed-and-different-operations"></a>A hírcsatorna és a különböző műveletek módosítása

Ma az összes beszúrás és frissítés megjelenik a módosítási hírcsatornában. Egy adott művelettípushoz nem szűrheti a módosítási hírcsatornát. Az egyik lehetséges alternatíva az, hogy adjunk egy "puha marker" az elem a frissítések és a szűrő alapján, hogy a feldolgozás során elemeket a változás feed.

Jelenleg a módosítási hírcsatorna nem naplózza a törléseket. Az előző példához hasonlóan lágy jelölőt is hozzáadhat a törölt elemekhez. Hozzáadhat például egy attribútumot a "törölt" elemhez, és beállíthatja azt "true" értékre, és beállíthatja a TTL értéket az elemen, hogy automatikusan törölhető legyen. Elolvashatja a korábbi elemek módosítási hírcsatornáját (a cikknek megfelelő legutóbbi módosítás, nem tartalmazza a köztes módosításokat), például az öt évvel ezelőtt hozzáadott cikkeket. A módosítási hírcsatornát a tároló eredetéig olvashatja el, de ha egy elemet törölnek, az törlődik a módosítási hírcsatornából.

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

* Az alkalmazások egyszerre több módosítási hírcsatornát is kérhetnek ugyanazon a tárolón. A ChangeFeedOptions.StartTime a kezdeti kiindulási pont megadására használható. Például egy adott óraidőnek megfelelő folytatási token megkereséséhez. A ContinuationToken, ha meg van adva, elsőbbséget élvez a StartTime és a StartFromBeginning értékekkel szemben. A ChangeFeedOptions.StartTime pontossága ~5 másodperc.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>A Cassandra és a MongoDB API-kban való módosítása

A változáscsatorna-funkció változásfolyamként kerül felszínre a MongoDB API-ban és a Queryben a Cassandra API predikátumával. Ha többet szeretne megtudni a MongoDB API megvalósítási részleteiről, tekintse meg a [Streamek módosítása az Azure Cosmos DB API-ban a MongoDB-hoz című témakört.](mongodb-change-streams.md)

A natív Apache Cassandra biztosítja a változási adatok rögzítését (CDC), egy olyan mechanizmust, amely meghatározott táblákat jelöl meg archiváláshoz, valamint elutasítja az írásokat ezekre a táblákra, amint a CDC-napló konfigurálható méretű lemeze eléri. A cassandrai Azure Cosmos DB API változáscsatorna-szolgáltatása növeli a módosítások lekérdezésének lehetőségét a CQL-en keresztüli predikátummal. Ha többet szeretne megtudni a megvalósítás részleteiről, olvassa [el a Hírcsatorna módosítása az Azure Cosmos DB API cassandra.](cassandra-change-feed.md)

## <a name="next-steps"></a>További lépések

Most a következő cikkekben olvashat bővebben a változáscsatornáról:

* [A módosítási hírcsatorna olvasásának beállításai](read-change-feed.md)
* [Módosítási hírcsatorna használata az Azure Functions funkcióval](change-feed-functions.md)
* [A módosítási adatcsatorna-processzor használata](change-feed-processor.md)
