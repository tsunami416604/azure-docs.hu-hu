---
title: "Az Azure Event Hubs – gyakori kérdések |} Microsoft Docs"
description: "Az Azure Event Hubs gyakran ismételt kérdések (GYIK)"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: bfa10984-eb22-4671-861a-f377a90d9372
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: c4faa071c4f2401fe3e852e787e3b7d4da0c7d44
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-frequently-asked-questions"></a>Az Event Hubs gyakran ismételt kérdések

## <a name="general"></a>Általános kérdések

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Mi az az Event Hubs egyszerű és szabványos rétegek közötti különbség?

Azure Event Hubs a Standard szint az alapszintű rétegben elérhető mértéket szolgáltatásokat biztosítja. Standard a következő funkciók találhatók:
* Esemény hosszabb megőrzési
* További közvetített kapcsolatok, a több része egy keretét díjat
* Több mint egy egyetlen felhasználói csoport
* [Rögzítése](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

További információ a tarifacsomagok, beleértve az Event Hubs dedikált, tekintse meg a [díjszabása Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="what-are-event-hubs-throughput-units"></a>Mik az Event Hubs átviteli egységek?

Explicit módon válassza ki az Event Hubs átviteli egységek, vagy az Azure-portálon vagy az Event Hubs Resource Manager-sablonok használatával. Átviteli egységek az Event Hubs-névtér összes eseményközpontjában vonatkoznak, és minden átviteli egység-felügyeleti lehetőségeket kínál a névtér a következő funkciókat:

* Másolatot érkező jelzések (események küldése eseményközpontba), de nem 1000-nél több érkező jelzések, a felügyeleti műveleteket vagy a vezérlő másodpercenként 1 MB-nál API meghívja a másodpercenkénti számát.
* A kimenő forgalom események (felhasznált az eseményközpontban lévő események) másodpercenként legfeljebb 2 MB.
* Legfeljebb 84 GB esemény storage (az alapértelmezett 24 órás megőrzési időszak elegendő).

Event Hubs átviteli egységek vannak számlázva óránként, a megadott órán belül kijelölt egységek maximális száma alapján. Automatikusan is [növelje a szám átviteli egységek](event-hubs-auto-inflate.md) , a használati növekszik.

### <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Hogyan tartatja be az Event Hubs átviteli egység korlátai?
Amennyiben a teljes érkező átviteli vagy a teljes érkező események száma egy névtér összes eseményközpontjában között meghaladja a teljes átviteli egység támogatás, feladók szabályozott és hibáját jelzik, hogy a bejövő adatok kvóta túl lett lépve.

Ha a teljes kimenő átviteli vagy a teljes kimenő eseménygyakoriság között egy névtér összes eseményközpontjában meghaladja a teljes átviteli egység támogatás, a fogadók szabályozott, és a hiba, amely azt jelzi, hogy a kimenő forgalom kvóta túl lett lépve. Bemenő és kimenő kvóták külön-külön tartatja be, hogy a feladó okozhat események felhasználásához lassítsa le, és nem a fogadó megakadályozhatja események küldése eseményközpontba.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Korlátozva van az igénybe vehető kapacitásegységek száma?
Nincs a névtér 20 átviteli egység alapértelmezett kvótát. Átviteli egységek nagyobb kvótát egy támogatási jegy tervátalakítási által kérhet. A 20 átviteli egység határértéken túlmenően kötegek már 20 és 100 átviteli egységek érhetők el. Vegye figyelembe, hogy több mint 20 átviteli egységek használatával megszünteti az átviteli egységek számának módosítása egy támogatási jegy bejelentés nélkül.

Használja a [automatikus megnöveli](event-hubs-auto-inflate.md) szolgáltatást, automatikusan növelhető a szám átviteli egységek, a használati növekszik.

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Használhatok egy AMQP kapcsolódás küldeni és fogadni több event hubs?
Igen, mindaddig, amíg az event hubs szerepelnek ugyanazt a névteret.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Mi az az események maximális megőrzési időtartama?
Event Hubs Standard csomagra jelenleg támogatja a maximális megőrzési időtartam 7 nap. Vegye figyelembe, hogy az event hubs nem feltétlenül állandó adattárként. 24 óránál hosszabb megőrzési időtartamú szánt forgatókönyvek, amelyben célszerű a visszajátszani az eseménystream azonos rendszerekbe; például betanítása, vagy a meglévő adatok új gépi tanulási modell ellenőrzése. Megőrzési 7 napon túl kell hibaüzenet, amely lehetővé teszi [Event Hubs rögzítése](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) az esemény hub az eseményközpont a tárfiókhoz vagy Azure Data Lake szolgáltatásfiók a adatait kéri le. Rögzítési engedélyezése azt eredményezi, a megvásárolt átviteli egység alapján díj azok háromszorosa.

### <a name="where-is-azure-event-hubs-available"></a>Ahol lehetőség van az Azure Event Hubs?
Az Azure Event Hubs minden támogatott Azure-régió nem áll rendelkezésre. A listáját, és látogasson el a [Azure-régiók](https://azure.microsoft.com/regions/) lap.  

## <a name="best-practices"></a>Ajánlott eljárások

### <a name="how-many-partitions-do-i-need"></a>Hány partíciókkal van szükségem?
Ne feledje, hogy a partíció száma eseményközpontban adja a telepítés után nem módosítható. Vele szem előtt fontos gondolja, hogy hány partíciók kapcsolatos első lépések előtt szüksége. 

Az Event Hubs egy fogyasztói csoporton egypartíciós olvasó lehetővé teszi. A legtöbb esetben használja az alapértelmezett beállítás négy partíció is használhatók. Ha a méretezési az események feldolgozásával, érdemes lehet vegyen fel további partíciókat. Nincs megadott átviteli korlát partíciók, azonban a teljes átviteli sebesség a névtér korlátozza az átviteli egységek száma. Átviteli egységek számának növelésével a névtérben, érdemes lehet további partíciókat a saját maximális átviteli sebesség eléréséhez egyidejű olvasók engedélyezéséhez.

Azonban ha egy modell, amelyben az alkalmazás rendelkezik egy adott partícióra kapcsolatot, a partíciók számának növelése nem lehet az Ön számára hasznos. További információ: [rendelkezésre állás és a konzisztencia](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Díjszabás

### <a name="where-can-i-find-more-pricing-information"></a>Hol találnak további információkat?
Az Event Hubs árazással kapcsolatos részletes információkért tekintse meg a [díjszabása Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Az Event Hubs események megőrzése 24 óránál díjat van?
Az Event Hubs Standard csomagra lehetővé teszi üzenetek megőrzési 7 nap legfeljebb 24 óránál hosszabb. Tárolt események teljes száma a mérete meghaladja a tárolási támogatás kijelölt átviteli egységek (átviteli egységenként 84 GB) száma, ha a megadott méret meghaladja a támogatás a közzétett Azure Blob storage díj fel van töltve. A tárolási támogatás átviteli egységenként tartalmazza az összes tárolási költségek 24 órás megőrzési ideig (alapbeállítás) akkor is, ha az átviteli egység a maximális érkező támogatásra van használt.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Hogyan az Event Hubs tárméret kiszámítása és felszámított?
Minden tárolt eseményeket, többek között a bármely esemény fejlécek vagy a lemez tárolási struktúrák belső terhelés az összes eseményközpontjában teljes mérete ismétlődnek a nap folyamán mérése történik. A nap végén megállapítjuk az aznap foglalt tárméret maximumát. A napi tárolási támogatás alapján van kiszámítva az átviteli egységek (átviteli egységenként biztosít egy 84 GB-os támogatás) a nap folyamán kijelölt minimális számát. Összesített mérete meghaladja a számított napi tárolási juttatásra, ha a túlzott tárolási lesz számlázva, használja az Azure Blob storage díjszabásának (a a **helyileg redundáns tárolás** sebessége).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Az Event Hubs érkező jelzések kiszámítási módját?
Az eseményközpontok felé küldött események számlázható üzenet számít. Egy *érkező esemény* , amely kisebb vagy egyenlő, mint 64 KB-os adategység típusúként van definiálva. Bármely esemény, amely legfeljebb 64 KB-nál több számlázható esemény tekinthető. Ha az esemény 64 KB-nál nagyobb, a számlázható események száma szerint 64 KB-os többszörösei a eseményméret kiszámítása. Például egy eseményt az event hubs küldött 8 KB-os esemény terheli, de a két esemény terheli 96 KB üzeneteket az eseményközpontba.

Az eseményközpontok felé, valamint a felügyeleti műveleteket és a vezérlő indított, például az ellenőrzőpontok nem számítanak számlázható érkező jelzések, de legfeljebb az átviteli egység támogatás keletkeznek a felhasznált események.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Az Event Hubs vonatkoznak közvetített kapcsolat díjak?
Kapcsolat díjak vonatkoznak, csak ha az AMQP protokollt használja. Nincsenek nincs kapcsolat költségekkel függetlenül küld a rendszer vagy az eszközök számát a HTTP protokollal események küldése. Ha azt tervezi, hogy (például a hatékonyabb esemény streaming eléréséhez, vagy lehetőséget kétirányú kommunikáció IoT parancsban és forgatókönyvek szabályozása) AMQP használatához tekintse meg a [árakról Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) lap hány vonatkozó további információért kapcsolatok száma az egyes szolgáltatásszinteken szerepelnek.

### <a name="how-is-event-hubs-capture-billed"></a>Hogyan számítjuk az Event Hubs Rögzítés díját?
Rögzítés engedélyezve van, ha bármely eseményközpont névtér engedélyezve van a rögzítési paraméter. Event Hubs rögzítése lesz számlázva óránkénti megvásárolt átviteli egységenként. Az átviteli egységek száma nő vagy csökken, mivel számlázási Event Hubs rögzítése a módosításokat a teljes óránkénti növekményekben jeleníti meg. Event Hubs rögzítése számlázással kapcsolatos további információkért lásd: [árakról Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="will-i-be-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>I alapján számlázzuk ki a tárfiók jelölhető ki az Event Hubs rögzítése?
Rögzítési megadnia, ha az eseményközpontok a storage-fiókot használ. Mivel ez a tárfiók, ehhez a konfigurációhoz módosításokat számlázása az Azure-előfizetéshez.

## <a name="quotas"></a>Kvóták

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Minden Event Hubs társított kvóták vannak?
Minden Event Hubs kvóták listájáért lásd: [kvóták](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Melyek azok a kivételeket, az Event Hubs és a javasolt lépések által generált?
A lehetséges az Event Hubs kivételek listájáért lásd: [kivételek áttekintése](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Diagnosztikai naplók
Az Event Hubs támogatja a két típusú [diagnosztikai naplók](event-hubs-diagnostic-logs.md) -rögzítse a hibanaplókat és a műveleti naplókat - mindkettőnek jelennek meg a JSON-ban, és is be kell kapcsolni az Azure portálon keresztül.

### <a name="support-and-sla"></a>Támogatás és szolgáltatásszintek
Az Event Hubs számára a technikai támogatási szolgálathoz keresztül érhető el a [közösségi fórumok](https://social.msdn.microsoft.com/forums/azure/home). A számlázás és az előfizetések kezelésének támogatása díjmentesen igénybe vehető.

Az SLA-t kapcsolatos további tudnivalókért tekintse meg a [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/) lap.

## <a name="next-steps"></a>Következő lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hub létrehozása](event-hubs-create.md)
* [Event Hubs automatikus megnöveli](event-hubs-auto-inflate.md)
