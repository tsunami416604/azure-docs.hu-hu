---
title: Csatornatervezési minták módosítása az Azure Cosmos DB-ben
description: A közös változáscsatorna-tervezési minták áttekintése
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 012d27b44ecfbdd460adf241742df397880f78c6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450351"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Csatornatervezési minták módosítása az Azure Cosmos DB-ben

Az Azure Cosmos DB változáscsatorna lehetővé teszi a nagy adatkészletek hatékony feldolgozását nagy mennyiségű írási adatokkal. A módosítási hírcsatorna a teljes adatkészlet lekérdezésének alternatívájaként is kínál, hogy azonosítsa a változásokat. Ez a dokumentum a gyakori változáscsatorna-tervezési mintákra, a tervezési kompromisszumokra és a hírcsatorna-korlátozások módosítására összpontosít.

Az Azure Cosmos DB kiválóan alkalmas IoT-, játék-, kiskereskedelmi és operatív naplózási alkalmazásokhoz. Ezekben az alkalmazásokban gyakori tervezési minta az adatok módosításai további műveletek indításához. További műveletek például a következők:

* Értesítés vagy API-hívás aktiválása, egy elem beszúrásakor vagy frissítésekor.
* Valós idejű adatfolyam-feldolgozás IoT-hez vagy valós idejű elemzési feldolgozáshoz a működési adatokon.
* Az adatok mozgása, például szinkronizálás gyorsítótárral, keresőmotorral, adattárháztal vagy hűtőházlal.

Az Azure Cosmos DB módosítási hírcsatornája lehetővé teszi, hogy hatékony és méretezhető megoldásokat hozzon létre ezekhez a mintákhoz, ahogy az az alábbi képen látható:

![Az Azure Cosmos DB módosítási hírcsatornájának használata a valós idejű elemzések és az eseményvezérelt számítástechnikai forgatókönyvek teljesítményéhez](./media/change-feed/changefeedoverview.png)

## <a name="event-computing-and-notifications"></a>Esemény-számítástechnika és értesítések

Az Azure Cosmos DB változáscsatorna egyszerűsítheti forgatókönyvek, amelyek egy értesítés vagy egy API-hívás egy adott esemény alapján. A [Változáscsatorna-folyamat tár](change-feed-processor.md) használatával automatikusan lejátszhatja a tárolót a módosításokról, és meghívhat egy külső API-t minden alkalommal, amikor írásvagy frissítés történik.

Emellett szelektíven is aktiválhat egy értesítést, vagy adott feltételek alapján hívást küldhet egy API-nak. Ha például az [Azure Functions](change-feed-functions.md)használatával a módosítási hírcsatornából olvas, a logikát a függvénybe helyezheti, hogy csak akkor küldjön értesítést, ha egy adott feltétel teljesült. Míg az Azure Function-kód minden egyes írási és frissítési idő alatt futna, az értesítést csak akkor küldi el, ha bizonyos feltételek nek megfelelőek lettek volna.

## <a name="real-time-stream-processing"></a>Valós idejű adatfolyam-feldolgozás

Az Azure Cosmos DB változáscsatorna használható valós idejű stream feldolgozás iot vagy valós idejű elemzési feldolgozás a működési adatok.
Például események adatait fogadhatja és tárolhatja az eszközökről, érzékelőkről, infrastruktúráról és alkalmazásokról, és valós időben dolgozhatja fel ezeket az eseményeket a [Spark](../hdinsight/spark/apache-spark-overview.md)használatával. Az alábbi képen bemutatja, hogyan valósíthat meg egy lambda architektúrát az Azure Cosmos DB használatával a módosítási hírcsatorna segítségével:

![Azure Cosmos DB-alapú lambda-folyamat betöltéshez és lekérdezéshez](./media/change-feed/lambda.png)

Sok esetben a streamelési implementációk először nagy mennyiségű bejövő adatot kapnak egy ideiglenes üzenetvárólistába, például az Azure Event Hubvagy az Apache Kafka. A változáscsatorna egy nagyszerű alternatíva, mivel az Azure Cosmos DB képes támogatni a tartósan magas adatátviteli garantált alacsony olvasási és írási késleltetés. Az Azure Cosmos DB módosítási hírcsatornájának előnyei az üzenetvárólistán keresztül a következők:

### <a name="data-persistence"></a>Adatmegőrzés

Az Azure Cosmos DB-be írt adatok megjelennek a módosítási hírcsatornában, és a törlésig megmaradnak. Az üzenetvárólisták megőrzési ideje általában maximális. Az [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) például legfeljebb 90 napos adatmegőrzést biztosít.

### <a name="querying-ability"></a>Lekérdezési képesség

A Cosmos-tároló módosítási hírcsatornájából való olvasás mellett sql-lekérdezéseket is futtathat az Azure Cosmos DB-ben tárolt adatokon. A változáscsatorna nem a tárolóban lévő adatok megkettőzése, hanem csak az adatok olvasásának egy másik mechanizmusa. Ezért ha adatokat olvas a változáscsatorna, mindig konzisztens lesz az azure Cosmos DB-tároló lekérdezéseivel.

### <a name="high-availability"></a>Magas rendelkezésre állás

Az Azure Cosmos DB akár 99,999%-os olvasási és írási rendelkezésre állást kínál. Sok üzenetvárólistával ellentétben az Azure Cosmos DB-adatok könnyen terjeszthetők és konfigurálhatók nulla [RTO (helyreállítási idő célkitűzés)](consistency-levels-tradeoffs.md#rto) segítségével.

A változáshírcsatorna elemeinek feldolgozása után létrehozhat egy materializált nézetet, és összesített értékeket őrizhet meg az Azure Cosmos DB-ben. Ha az Azure Cosmos DB-t használja egy játék létrehozásához, használhatja például a változáscsatornát a befejezett játékok pontszámai alapján valós idejű ranglisták megvalósításához.

## <a name="data-movement"></a>Adatáthelyezés

A valós idejű adatmozgatáshoz a módosítási hírcsatornából is olvashat.

A módosítási hírcsatorna például a következő feladatok hatékony elvégzésében segít:

* Frissítse na a gyorsítótárat, a keresési indexet vagy az adatraktárat az Azure Cosmos DB-ben tárolt adatokkal.

* Nulla állásidő-áttelepítést hajtson végre egy másik Azure Cosmos-fiókba vagy egy másik logikai partíciós kulccsal rendelkező másik Azure Cosmos-tárolóba.

* Alkalmazásszintű adatrétegezés és archiválás megvalósítása. Például tárolhatja a "forró adatok" az Azure Cosmos DB és a kor ki "hideg adatok" más tárolórendszerek, például az [Azure Blob Storage.](../storage/common/storage-introduction.md)

Ha [denormalizálnia kell az adatokat partíciók és tárolók között,](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)a tároló módosítási hírcsatornájából olvashat az adatreplikáció forrásaként. A valós idejű adatreplikáció a módosítási hírcsatornával csak a végleges konzisztenciát garantálja. [Figyelheti, hogy a változás-hírcsatorna processzor lemarad](how-to-use-change-feed-estimator.md) a cosmos-tárolóban a változások feldolgozása során.

## <a name="event-sourcing"></a>Eseménybeszerzés

Az [eseményforrás-beszerzési minta](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) magában foglalja a csak hozzáfűző tároló használatával rögzíti az adatokon végrehajtott műveletek teljes sorozatát. Az Azure Cosmos DB változási hírcsatornája egy nagyszerű választás, mint egy központi adattár eseménybeszerzési architektúrák, ahol az összes adatbetöltés isztiós (nincs frissítés vagy törlés). Ebben az esetben minden írásaz Azure Cosmos DB egy "esemény", és a módosítási hírcsatornában a múltbeli események teljes rekordja lesz. A központi eseménytároló által közzétett események tipikus felhasználási területe a materializált nézetek karbantartása vagy a külső rendszerekkel való integráció. Mivel a változáscsatornában nincs időkorlát megőrzése, az összes múltbeli eseményt újralejátszhatja a Cosmos-tároló módosítási hírcsatornájának elejétől olvasva.

Több [módosítási hírcsatorna-fogyasztó is előfizethet ugyanarra a tárolómódosítási hírcsatornára.](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers) A bérleti [tároló](change-feed-processor.md#components-of-the-change-feed-processor) kiosztott átviteli fazonán kívül nincs költség a változáscsatorna használatához. A módosítási hírcsatorna minden tárolóban elérhető, függetlenül attól, hogy az használja-e.

Az Azure Cosmos DB egy nagyszerű központi hozzáfűzés csak egyhelyen álló adattár az eseménybeszerzési mintában a horizontális méretezhetőség és a magas rendelkezésre állás erősségei miatt. Ezenkívül a változás-hírcsatorna-processzor könyvtár ["legalább egyszer"](change-feed-processor.md#error-handling) garanciát nyújt, biztosítva, hogy ne maradjon le az események feldolgozásáról.

## <a name="current-limitations"></a>Aktuális korlátozások

A módosítási hírcsatornának fontos korlátai vannak, amelyeket meg kell értenie. Míg a Cosmos-tárolóban lévő elemek mindig a változáscsatornában maradnak, a módosítási hírcsatorna nem egy teljes műveleti napló. A változáscsatornát használó alkalmazások tervezésekor fontos területeket kell figyelembe venni.

### <a name="intermediate-updates"></a>Köztes frissítések

A módosítási hírcsatorna csak az adott cikk legutóbbi módosítása szerepel. A módosítások feldolgozásakor olvassa el a legújabb elérhető cikkverziót. Ha rövid idő alatt több frissítés is történik ugyanannak a cikknek, akkor kihagyhatja a köztes frissítések feldolgozását. Ha nyomon szeretné követni a frissítéseket, és vissza szeretné játszani egy elem korábbi frissítéseit, javasoljuk, hogy ezeket a frissítéseket írások sorozataként modellezze.

### <a name="deletes"></a>Törli

A módosítási hírcsatorna nem rögzíti a törléseket. Ha töröl egy elemet a tárolóból, az is törlődik a módosítási hírcsatornából. Ennek kezelésének leggyakoribb módja a törölt elemek lágy jelölőjének hozzáadása. Hozzáadhat egy "törölt" tulajdonságot, és a törlés időpontjában "true" értékre állíthatja. Ez a dokumentumfrissítés megjelenik a módosítási hírcsatornában. Ezen az elemen beállíthat egy TTL-t, hogy később automatikusan törölhesse.

### <a name="guaranteed-order"></a>Garantált rendelés

A változási hírcsatorna garantált sorrendje egy partíciókulcs-értéken belül van, de nem a partíciókulcs-értékek között. Válasszon egy partíciókulcsot, amely értelmes rendelési garanciát ad.

Fontolja meg például egy kiskereskedelmi alkalmazás az esemény forrás tervezési minta használatával. Ebben az alkalmazásban a különböző felhasználói műveletek minden egyes "események", amelyek az Azure Cosmos DB-nek írási műveletekként vannak modellezve. Képzeld el, ha néhány példa esemény történt a következő sorrendben:

1. A vevő hozzáadja az A cikket a bevásárlókosarához
2. A vevő hozzáadja a B cikket a bevásárlókosárhoz
3. A vevő eltávolítja az A cikket a bevásárlókosarából
4. Vevői kijelentkezés és a bevásárlókocsi tartalma szállításra kerülnek

Az aktuális bevásárlókosár-tartalom materializált nézete minden ügyfél számára megmarad. Ennek az alkalmazásnak biztosítania kell, hogy ezeket az eseményeket a bekövetkezésük sorrendjében dolgozzák fel. Ha például a kosár kivételt az A cikk eltávolítása előtt kellett volna feldolgozni, akkor valószínű, hogy a vevő az A cikket szállította volna, szemben a kívánt B cikkel. Annak biztosítása érdekében, hogy ez a négy esemény előfordulásuk sorrendjében dolgozzák fel, ugyanabban a partíciókulcs-értékben kell lenniük. Ha a **partíciókulcsként a felhasználónevet** (minden ügyfél egyedi felhasználónévvel rendelkezik), garantálhatja, hogy ezek az események ugyanabban a sorrendben jelennek meg a módosítási csatornában, amelyben az Azure Cosmos DB-be írásra kerülnek.

## <a name="examples"></a>Példák

Íme néhány valós változás-hírcsatornakód példa, amelyek túlmutatnak a Microsoft-dokumentumokban megadott minták hatókörén:

- [Bevezetés a módosítási hírcsatornába](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [IoT használati eset a változáscsatorna köré](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Kiskereskedelmi használati eset a változáscsatorna köré](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>További lépések

* [Változáscsatorna áttekintése](change-feed.md)
* [A módosítási hírcsatorna olvasásának beállításai](read-change-feed.md)
* [Módosítási hírcsatorna használata az Azure Functions funkcióval](change-feed-functions.md)