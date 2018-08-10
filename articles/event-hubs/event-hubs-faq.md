---
title: Az Azure Event Hubs – gyakori kérdések |} A Microsoft Docs
description: Az Azure Event Hubs – gyakori kérdések (GYIK)
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 06/07/2018
ms.author: shvija
ms.openlocfilehash: 3e98d27c297e223231e0990adc51f8765678b884
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006446"
---
# <a name="event-hubs-frequently-asked-questions"></a>Az Event Hubs – gyakori kérdések

## <a name="general"></a>Általános kérdések

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Mi a különbség az alapszintű Event Hubs és a Standard szint között?

Az Azure Event Hubs Standard szintű csomagban érhető el az alapszintű csomag biztosít. A Standard jelennek meg a következő funkciókat:

* Esemény hosszabb megőrzés
* További felügyelt kapcsolatok a kerettúllépési díjat számítunk fel több, mint a-szám
* Több [fogyasztói csoportot](event-hubs-features.md#consumer-groups)
* [rögzítése](event-hubs-capture-overview.md)

Csomagok – köztük az Event Hubs dedikált, a díjszabással kapcsolatban további információt talál a [az Event Hubs szolgáltatás díjszabását](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="what-are-event-hubs-throughput-units"></a>Mik azok az Event Hubs átviteli egység?

Explicit módon válassza ki az Event Hubs átviteli egységeket az Azure Portalon vagy az Event Hubs Resource Manager-sablonok felületén keresztül. Event Hubs-névtér összes event hubs átviteli egységek érvényesek, és minden kapacitásegység az alábbi képességekkel feljogosítja a névtér:

* Akár 1 MB / s belépő események (egy eseményközpontba küldött események), de több mint 1000 beáramlási események, műveletek vagy vezérlő, az API-hívások / másodperc.
* Akár 2 MB / s kapacitás a kilépő események (egy adott eseményközpontból beolvasott események), de legfeljebb csak 4096 kapacitás a kilépő események.
* Akár 84 GB-nyi eseménytár (ami a gyakorlatban szokásos és egyben alapértelmezett 24 órányi adatmegőrzéshez elegendő).

Event Hubs átviteli egységek számlázása óránként, alapján az adott órában kiválasztott egységek maximális száma. Képes automatikusan [növelheti a szám átviteli egységek használatával, az automatikus feltöltési](event-hubs-auto-inflate.md) , növeli a használatot.

### <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Hogyan tartatja be az Event Hubs átviteli egység korlátai?

Ha a belépő kapacitás vagy a belépő üzenetek gyakorisága összességében egy névtér összes event hubs meghaladja az összesített átviteli egység a kedvezmények, feladók szabályozott, és hibajelzésekben, hogy a belépési kapacitáskvóta túllépését.

Ha a kilépő kapacitás vagy a kilépő üzenetek gyakorisága egy névtér összes event hubs között meghaladja az összesített átviteli egység a kedvezmények, fogadók szabályozott, és hibajelzésekben, hogy a kilépési kapacitáskvóta túllépését. Bejövő és kimenő kvóták külön-külön érvényben vannak, így nincsenek sem tudja lassítani eseményfeldolgozás, és egyetlen külső eseményfogyasztó megakadályozhatja az eseményeket egy eseményközpontba küldött.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Korlátozva van az igénybe vehető kapacitásegységek száma?

Van egy alapértelmezett kvóta névterenként 20 kapacitásegység rendelhető. Nagyobb kvótát több kapacitásegység is kérhető az ügyfélszolgálatunknak küldött támogatási kérésen. A 20 átviteli egység gyűjthessen csomagjaiból 20 és 100 átviteli egységek érhető el. Vegye figyelembe, hogy több mint 20 átviteli egység használatával eltávolítja az átviteli egységek számának módosítása nélkül támogatási kérésen lehetővé teszi.

Használatával a [automatikus feltöltésről](event-hubs-auto-inflate.md) funkció, a szám, a memóriahasználat átviteli egységek automatikusan növelhető.

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Használhatom-e egyetlen AMQP-kapcsolaton küldése és fogadása az event hubs-eseményközpontokból több?

Igen, mindaddig, amíg az event hubs a ugyanazon névterében van.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Mi az a maximális adatmegőrzési időtartam eseményeket?

Event Hubs Standard csomag jelenleg támogatja a maximális adatmegőrzési időtartam 7 nap. Fontos mindazonáltal megjegyezni, hogy az eseményközpontok nem permanens adattárolásra vannak kialakítva. 24 óránál hosszabb megőrzési időszakok forgatókönyvek, amelyben célszerű visszajátszani egy eseménysorozatot az eredeti rendszerekben; lettek tervezve. Ha például használatával betanítani vagy ellenőrizni egy új gépi tanulási modellt a meglévő adatok. Adatmegőrzési 7 napos időszak letelte után kell üzenetet, ha engedélyezése [Event Hubs Capture](event-hubs-capture-overview.md) az esemény-hub kér le az adatokat az eseményközpontból érkező az a Storage-fiók vagy a tetszőleges Azure Data Lake szolgáltatás fiók. Rögzítés engedélyezése kötelezettséggel jár a megvásárolt átviteli egységek alapján.

### <a name="where-is-azure-event-hubs-available"></a>Hol érhető el az Azure Event Hubs?

Azure Event hubs szolgáltatás az összes támogatott Azure-régióban érhető el. Nyissa meg a listáját, a [Azure-régiók](https://azure.microsoft.com/regions/) lapot.  

## <a name="best-practices"></a>Ajánlott eljárások

### <a name="how-many-partitions-do-i-need"></a>Hogyan sok partíció van szükségem?

Vegye figyelembe, hogy a partíciók száma az eseményközpont beállítása után nem módosítható. Az adott szem előtt fontos úgy gondolja, hogy hány partíciók kapcsolatos első lépések előtt szüksége. 

Az Event Hubs egy fogyasztói csoportot egy egyetlen partícióolvasónak nyilván lehetővé teszi. A legtöbb használati esetek négy partíciót, az alapértelmezett beállítás is elegendő. Ha az eseményfeldolgozás horizontális, érdemes érdemes lehet hozzáadni a két további partíció. Nincs konkrét átviteli korlátozva a partíción, azonban az átviteli egységek száma korlátozza az összesített átviteli sebességre, a névtérben. Növeli az átviteli egységek száma a névtérben, előfordulhat, hogy szeretne további partíciókat, hogy az egyidejű olvasók saját maximális átviteli sebesség eléréséhez.

Előfordulhat azonban, ha a modell, amelyben az alkalmazás rendelkezik egy adott partíció való kapcsolatot, a partíciók számának növelésével nem minden javát. További információ: [rendelkezésre állás és konzisztencia](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Díjszabás

### <a name="where-can-i-find-more-pricing-information"></a>Hol találhatok további díjszabási információk?

Event Hubs-díjszabás kapcsolatos részletes információkért lásd: a [az Event Hubs szolgáltatás díjszabását](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Van valamilyen díja az Event Hubs-események megőrzése 24 óránál tovább áll?

Az Event Hubs Standard csomag lehetővé teszi, hogy üzenetmegőrzés 7 nap legfeljebb 24 óránál hosszabb ideig. Ha a tárolt események teljes száma meghaladja a tároló (kapacitásegységenként 84 GB) a kiválasztott kapacitásegységek száma, a mérete, amely meghaladja a díja a közzétett Azure Blob storage sebességét. A keretet az egyes kapacitásegységek 24 órányi megőrzéshez költségeit tartalmazza (az alapértelmezett beállítás) még akkor is, ha a Kapacitásegység a legnagyobb bejövő keretet.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Hogyan az Event Hubs tárméret kiszámítása és díját?

Minden tárolt események, belső, adminisztratív kapcsolódóan tárakon lemezt beleértve minden event hubs, amelyek összméretén egész nap folyamatosan mérjük. A nap végén megállapítjuk az aznap foglalt tárméret maximumát. A napi tárkeretet az adott napra választott kapacitásegységek számából állapítjuk meg – ha egy adott nap folyamán többször több különböző értéket jelöltek ki, akkor a legkisebbet vesszük figyelembe. Egy kapacitásegység tárkerete 84 GB. Ha a teljes mérete meghaladja a számított napi keretet, a blobtárolói díjszabásán számoljuk használatával az Azure Blob tárolási díjait (címen a **helyileg redundáns tárolás** arány).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Hogyan számítják a belépő események Event Hubs?

Az eseményközpontba érkező minden egyes esemény egy üzenetnek számít. Egy *belépő esemény* , amely legfeljebb 64 KB méretű adategység típusúként van definiálva. Bármely esemény, amely legfeljebb 64 KB méretű tekinthető egy esemény számlázható. Ha az esemény 64 KB-nál nagyobb, számlázható események száma értendő az esemény mérete a 64 KB hányszorosa. Például az event hubs küldött 8 KB-os esemény egy eseménynek történik, de egy 96 KB a az event hubs van számlázva két eseménynek számít.

Ezek az eseményközpontok felé, valamint a felügyeleti műveletek és a vezérlő hívásokat, például az ellenőrzőpontok nem számítanak belépő üzenetnek események, de beleszámítanak a kapacitáskeretbe legfeljebb események.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Az Event Hubs vonatkoznak felügyelt kapcsolati díjat?

Kapcsolat a díjak vonatkoznak, csak ha az AMQP protokollt használja. Az események HTTP-n keresztül történő küldése – a küldő rendszerek és eszközök számától függetlenül – díjmentes. Ha azt tervezi, használhatja az AMQP (például a hatékonyabb eléréséhez, vagy a kétirányú kommunikációt az IoT-parancsban, és szabályozhatja a forgatókönyvek), a [díjszabási információk az Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) hány részleteiért lap kapcsolatok az egyes szolgáltatásszinteken szerepelnek.

### <a name="how-is-event-hubs-capture-billed"></a>Hogyan számítjuk az Event Hubs Rögzítés díját?

Rögzítés engedélyezve van, ha a névtérben található bármely eseményközpont rögzítési lehetősége engedélyezve van. Az Event Hubs Capture számlázása óránként megvásárolt kapacitásegységenként. A kapacitásegységek számának növelése vagy csökkenése, az Event Hubs rögzítés árváltozásaként mutatkozik meg, egész órára kerekítve tartalmazza. Az Event Hubs Capture számlázással kapcsolatos további információkért lásd: [díjszabási információk az Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="will-i-be-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>I díjat kell fizetni a az Event Hubs rögzítéshez választott tárfiókot?

Rögzítés, adja meg, ha engedélyezve van az eseményközpontban tárfiókot használ. Mivel ez a tárfiók, ehhez a konfigurációhoz módosítások számítjuk fel az Azure-előfizetéshez.

## <a name="quotas"></a>Kvóták

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Milyen kvóták társított Event Hubs?

Minden Event Hubs-kvótákról listáját lásd: [kvóták](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Mik az Event Hubs és a javasolt lépések által előállított kivételeket?

Lehetséges az Event Hubs-kivételek listáját lásd: [kivételek áttekintése](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Diagnosztikai naplók

Az Event Hubs két típusát támogatja [diagnosztikai naplók](event-hubs-diagnostic-logs.md) -hibanaplók és műveleti naplók –, amelyek szerepelnek a JSON-ban, és az Azure Portalon lehet bekapcsolni.

### <a name="support-and-sla"></a>Támogatás és szolgáltatásszintek

Az Event Hubs technikai támogatás érhető el a [közösségi fórumokat is talál](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). A számlázás és az előfizetések kezelésének támogatása díjmentesen igénybe vehető.

SZERZŐDÉSÜNK kapcsolatos további információkért tekintse meg a [szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/) lapot.

## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hub létrehozása](event-hubs-create.md)
* [Event Hubs az automatikus feltöltési](event-hubs-auto-inflate.md)
