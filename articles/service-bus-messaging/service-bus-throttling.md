---
title: Az Azure Service Bus szabályozásának áttekintése | Microsoft dokumentumok
description: A Service Bus-szabályozás áttekintése – Standard és prémium szintű csomagok.
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: f852ad70b2eb97e2b8b3e40d086e98b3836c3592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598289"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Szabályozási műveletek az Azure Service Buson

A natív megoldások a korlátlan erőforrások fogalmát biztosítják, amelyek a számítási feladatokkal skálázhatók. Bár ez a fogalom több igaz a felhőben, mint a helyszíni rendszerek, még mindig vannak korlátozások, amelyek léteznek a felhőben.

Ezek a korlátozások az ügyfélalkalmazás-kérelmek szabályozását okozhatják standard és prémium szintű csomagokban is, ahogy azt ebben a cikkben tárgyaljuk. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Szabályozás az Azure Service Bus standard szintjén

Az Azure Service Bus standard szintű szintje több-bérlős beállításként működik, és csak felosztó-kitöltődíj-szabási modellel működik. Itt több névterek ugyanabban a fürtben osztoznak a lefoglalt erőforrásokat. Standard szint az ajánlott választás fejlesztői, tesztelési és minőségbiztosítási környezetek mellett az alacsony átviteli-átmenő éles rendszerek.

A múltban az Azure Service Bus durva sávszélesség-szabályozási korlátok kal kettec. Azonban lehetőség van a szabályozási logika finomítására, és kiszámítható szabályozási viselkedést biztosít minden olyan névterek, amelyek megosztják ezeket az erőforrásokat.

Annak érdekében, hogy biztosítsa az erőforrások tisztességes használatát és elosztását az azure Service Bus Standard névterei között, amelyek ugyanazokat az erőforrásokat használják, a szabályozási logika hitelalapúnak lett módosítva.

> [!NOTE]
> Fontos megjegyezni, hogy a szabályozás ***nem új*** az Azure Service Bus, vagy bármely natív felhőalapú szolgáltatás szabályozása.
>
> A hitelalapú szabályozás egyszerűen csak finomítja azt, ahogyan a különböző névterek megosztják az erőforrásokat egy több-bérlős standard rétegű környezetben, és ezáltal lehetővé teszi a méltányos használatot az erőforrásokat megosztó összes névtér számára.

### <a name="what-is-credit-based-throttling"></a>Mi az a hitelalapú szabályozás?

A hitelalapú szabályozás korlátozza az adott névtérben egy adott időszakban elvégezhető műveletek számát. 

Az alábbiakban a hitelalapú szabályozás munkafolyamata látható - 

  * Minden időszak elején bizonyos számú kreditet adunk az egyes névtérhez.
  * A küldő vagy a fogadó ügyfélalkalmazás által végrehajtott műveleteket a rendszer beszámítja ezekbe a kreditekbe (és kivonja a rendelkezésre álló kreditekből).
  * Ha a kreditek kimerülnek, a következő műveletek a következő időszak kezdetéig lesznek szabályozva.
  * A jóváírások a következő időszak elején kerülnek feltöltésre.

### <a name="what-are-the-credit-limits"></a>Mik a hitelkeretek?

A hitelkeret jelenleg "1000" kreditre van beállítva másodpercenként (névtérenként).

Nem minden művelet jön létre egyenlőként. Itt vannak az egyes műveletek hitelköltségei - 

| Művelet | Követel költség|
|-----------|-----------|
| Adatműveletek (Küldés, SendAsync, Fogadás, ReceiveAsync, Betekintés) |1 kredit üzenetenként |
| Felügyeleti műveletek (létrehozás, olvasás, frissítés, törlés várólistákon, témakörök, előfizetések, szűrők) | 10 kredit |

> [!NOTE]
> Kérjük, vegye figyelembe, hogy a témakörbe való küldéskor minden üzenetet a szűrő(k) alapján értékel(t ki, mielőtt elérhetővé válik az Előfizetésben).
> Minden egyes szűrőértékelés a hitelkerethez is beleszámít (azaz szűrőnként 1 kredit).
>

### <a name="how-will-i-know-that-im-being-throttled"></a>Honnan fogom tudni, hogy fojtogatnak?

Az ügyfélalkalmazás-kérelmek szabályozása során az alkalmazás megkapja az alábbi kiszolgálóválaszt, és naplózza.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Hogyan kerülhetem el, hogy megfojtsanak?

A megosztott erőforrások, fontos, hogy valamilyen tisztességes használat kényszerítése a különböző Service Bus névterek, amelyek megosztják ezeket az erőforrásokat. Szabályozása biztosítja, hogy egy kiugrás egyetlen számítási feladatok nem okoz más számítási feladatok ugyanazon erőforrásokon szabályozása.

Acikk későbbi részében említettek szerint nincs kockázat a szabályozás, mert az ügyfél SDK-k (és más Azure PaaS-ajánlatok) rendelkeznek az alapértelmezett újrapróbálkozási szabályzat beépített ek. A rendszer exponenciális visszalépéssel újra próbálkozik a szabályozott kérelmekkel, és végül a kreditek feltöltésekor fog átmenni.

Érthető, hogy egyes alkalmazások érzékenyek lehetnek a szabályozásra. Ebben az esetben ajánlott [áttelepíteni az aktuális Service Bus Standard névteret a Prémium verzióra.](service-bus-migrate-standard-premium.md) 

Áttelepítéskor dedikált erőforrásokat rendelhet a Service Bus névtérhez, és megfelelően felskálázhatja az erőforrásokat, ha a munkaterhelés kiugró, és csökkenti a szabályozás valószínűségét. Emellett ha a számítási feladatok normál szintre csökkennek, csökkentheti a névtérhez rendelt erőforrásokat.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Szabályozás az Azure Service Bus prémium csomagjában

Az [Azure Service Bus premium](service-bus-premium-messaging.md) szint dedikált erőforrásokat rendel az üzenetküldési egységek tekintetében az ügyfél által beállított minden egyes névtérbeállításhoz. Ezek a dedikált erőforrások kiszámítható átviteli és késési, és ajánlott a nagy átviteli sebességű vagy érzékeny termelési minőségű rendszerek.

Emellett a prémium szintű is lehetővé teszi az ügyfelek számára, hogy az átviteli kapacitás tágítása esetén az átviteli kapacitás.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Hogyan működik a szabályozás a Service Bus Premium szolgáltatásban?

A Service Bus Premium kizárólagos erőforrás-allokációjával a szabályozást kizárólag a névtérhez rendelt erőforrások korlátai határozzák meg.

Ha a kérelmek száma több, mint az aktuális erőforrások kiszolgálása, majd a kérelmek lesznek szabályozva.

### <a name="how-will-i-know-that-im-being-throttled"></a>Honnan fogom tudni, hogy fojtogatnak?

Az Azure Service Bus Premium szolgáltatásban többféleképpen is azonosíthatja a szabályozást – 
  * **A szabályozott kérelmek megjelennek** az [Azure Monitor Request metrikákon,](service-bus-metrics-azure-monitor.md#request-metrics) hogy azonosítsa, hogy hány kérelem volt szabályozva.
  * A magas **CPU-használat** azt jelzi, hogy az aktuális erőforrás-elosztás magas, és a kérelmek et előfordulhat, hogy szabályozható, ha az aktuális számítási feladatok nem csökken.
  * A magas **memóriahasználat** azt jelzi, hogy az aktuális erőforrás-foglalás magas, és a kérelmek et szabályozhatja, ha az aktuális munkaterhelés nem csökken.

### <a name="how-can-i-avoid-being-throttled"></a>Hogyan kerülhetem el, hogy megfojtsanak?

Mivel a Service Bus Premium névtér már rendelkezik dedikált erőforrásokkal, csökkentheti a szabályozás lehetőségét a névtérhez rendelt üzenetküldési egységek számának növelése esetén (vagy a munkaterhelés kiugrása esetén).

Felskálázás/le skálázás érhető el [runbookok](../automation/automation-create-alert-triggered-runbook.md) létrehozásával, amelyek a fenti metrikák változásai által kiváltott.

## <a name="faqs"></a>Gyakori kérdések

### <a name="how-does-throttling-affect-my-application"></a>Hogyan befolyásolja a szabályozás az alkalmazásomat?

Ha egy kérelem szabályozása van, azt jelenti, hogy a szolgáltatás foglalt, mert több kéréssel szembesül, mint amennyit az erőforrások lehetővé tesznek. Ha ugyanazt a műveletet néhány pillanat múlva újra próbálkozik, miután a szolgáltatás az aktuális munkaterhelésen keresztül működött, akkor a kérés megadható.

Mivel a szabályozás bármely natív felhőalapú szolgáltatás várható viselkedése, az újrapróbálkozási logikát az Azure Service Bus SDK-ba építettük. Az alapértelmezett beállítás az automatikus újrapróbálkozás egy exponenciális back-off annak érdekében, hogy nem ugyanazt a kérést, hogy minden alkalommal szabályozott.

Az alapértelmezett újrapróbálkozási logika minden műveletre vonatkozik.

### <a name="does-throttling-result-in-data-loss"></a>A szabályozás adatvesztést eredményez?

Az Azure Service Bus a megőrzésre van optimalizálva, biztosítjuk, hogy a Service Busnak küldött összes adat le legyen kötelezve a tárolásra, mielőtt a szolgáltatás tudomásul veszi a kérelem sikerességét.

Miután a kérés sikeresen "ACK" (nyugtázott) a Service Bus, ez azt jelenti, hogy a Service Bus sikeresen feldolgozta a kérelmet. Ha a Service Bus egy "NACK" (hiba) értéket ad vissza, akkor az azt jelenti, hogy a Service Bus nem tudta feldolgozni a kérelmet, és az ügyfélalkalmazásnak újra meg kell próbálnia a kérelmet.

Ha azonban egy kérelem szabályozása van, a szolgáltatás azt jelenti, hogy nem tudja elfogadni és feldolgozni a kérelmet most erőforrás-korlátozások miatt. Ez **nem** jelent semmiféle adatvesztést, mert a Service Bus egyszerűen nem vizsgálta meg a kérést. Ebben az esetben a Service Bus SDK alapértelmezett újrapróbálkozási házirendje biztosítja, hogy a kérelem feldolgozása végül.

## <a name="next-steps"></a>További lépések

A Service Bus üzenetküldési szolgáltatással kapcsolatos további tudnivalókat és példákat az alábbi témakörökben talál:

* [A Service Bus üzenetküldése – áttekintés](service-bus-messaging-overview.md)
* [Rövid útmutató: Üzenetek küldése és fogadása az Azure Portallal és a .NET-tel](service-bus-quickstart-portal.md)
* [Oktatóanyag: Leltár frissítése az Azure Portal és témakörök/előfizetések használatával](service-bus-tutorial-topics-subscriptions-portal.md)

