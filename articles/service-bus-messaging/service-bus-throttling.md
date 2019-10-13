---
title: A Azure Service Bus szabályozásának áttekintése | Microsoft Docs
description: A Service Bus szabályozásának áttekintése – standard és prémium szintű csomagok.
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: 21a3bfd09e83571e489e15e9351e12220a99e563
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301255"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Szabályozási műveletek Azure Service Bus

A Felhőbeli natív megoldások olyan korlátlan erőforrások fogalmát adják meg, amelyek méretezhetők a számítási feladatokkal. Habár ez a fogalom a felhőben még mindig igaz, mint a helyszíni rendszerekkel, továbbra is léteznek korlátozások a felhőben.

Ezek a korlátozások a jelen cikkben leírtak szerint a standard és a prémium szintű ügyfélalkalmazások általi kérelmek szabályozását eredményezhetik. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Szabályozás a standard szintű Azure Service Bus

A standard szintű Azure Service Bus több-bérlős beállításként működik, utólagos elszámolású díjszabási modellel. Itt több, ugyanabban a fürtben található névtér osztozik a lefoglalt erőforrásokon. A standard szint a fejlesztői, tesztelési és QA környezetekhez ajánlott választás, valamint az alacsony átviteli sebességű rendszerek esetében.

A múltban Azure Service Bus a durva szabályozási korlátokat szigorúan az erőforrás-használattól függ. Lehetőség van azonban arra, hogy pontosítsa a szabályozási logikát, és kiszámítható szabályozási viselkedést biztosítson az ezeket az erőforrásokat megosztó névterekhez.

Annak érdekében, hogy biztosítsa az erőforrások tisztességes használatát és elosztását az összes olyan Azure Service Bus standard névtérben, amelyek ugyanazt az erőforrást használják, a szabályozási logikát úgy módosították, hogy kredit-alapú legyen.

> [!NOTE]
> Fontos megjegyezni, hogy a szabályozás ***nem új*** a Azure Service Bus vagy bármilyen Felhőbeli natív szolgáltatáshoz.
>
> A kredit alapú szabályozás egyszerűen finomítja a különböző névterek erőforrásainak egy több-bérlős standard szintű környezetben való megosztását, így lehetővé téve az erőforrások megosztását az összes névtér számára.

### <a name="what-is-credit-based-throttling"></a>Mi az a kredit alapú szabályozás?

A kredit-alapú szabályozás korlátozza az adott névtér adott névterében elvégezhető műveletek számát egy adott időszakban. 

Az alábbiakban a kredit-alapú szabályozás munkafolyamata látható. 

  * Az egyes időszakok elején bizonyos mennyiségű kreditet biztosítunk az egyes névterekhez.
  * A küldő vagy a fogadó ügyfélalkalmazások által végrehajtott műveletek a következő kreditek alapján lesznek felszámítva (és kivonhatók a rendelkezésre álló kreditből).
  * Ha a kreditek ki vannak merítve, a későbbi műveletek a következő időszak elejéig szabályozva lesznek.
  * A kreditek feltöltése a következő időszak elején történik.

### <a name="what-are-the-credit-limits"></a>Mik a kreditek korlátai?

A hitelkeretek jelenleg "1000" kreditre vannak beállítva másodpercenként (névtér).

Nem minden művelet jön létre. Az egyes műveletek jóváírási költségei: 

| Művelet | Kreditek díja|
|-----------|-----------|
| Adatműveletek (küldés, SendAsync, fogadás, ReceiveAsync, betekintés) |1 kredit/üzenet |
| Felügyeleti műveletek (létrehozás, olvasás, frissítés, törlés várólistákon, témakörök, előfizetések, szűrők) | 10 kredit |

### <a name="how-will-i-know-that-im-being-throttled"></a>Honnan tudom, hogy szabályozva vagyok?

Az ügyfélalkalmazás kérelmének szabályozása esetén az alkalmazás az alábbi kiszolgálói választ fogja fogadni, és naplózza.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Hogyan kerülhetem el a szabályozást?

A megosztott erőforrások esetében fontos kikényszeríteni bizonyos fajta tisztességes használatot a különböző Service Bus névterek között, amelyek osztoznak az adott erőforrásokon. A szabályozás biztosítja, hogy egyetlen munkaterhelés egyetlen terhelése ne okozzon más munkaterheléseket az azonos erőforrásokra vonatkozóan.

Ahogy azt a cikk későbbi részében már említettük, nincs kockázat a szabályozásban, mivel az ügyfél SDK-k (és az egyéb Azure-beli Pásti-ajánlatok) az alapértelmezett újrapróbálkozási szabályzattal vannak beépítve. A rendszer újrapróbálkozik a szabályozott kérelmekkel az exponenciális leállítási, és végül a kreditek feltöltésekor halad át.

Érthető, hogy egyes alkalmazások a szabályozás szempontjából érzékenyek lehetnek. Ebben az esetben javasoljuk, hogy [a jelenlegi Service Bus standard szintű névteret prémium szintre telepítse át](service-bus-migrate-standard-premium.md). 

Az áttelepítés során dedikált erőforrásokat foglalhat le a Service Bus névtérhez, és megfelelően méretezheti az erőforrásokat, ha van egy tüske a munkaterhelésben, és csökkenti a szabályozás valószínűségét. Emellett, ha a számítási feladat a normál szintekre csökken, a névtérhez lefoglalt erőforrásokat is csökkentheti.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Szabályozás a Azure Service Bus prémium szinten

A [Azure Service Bus prémium](service-bus-premium-messaging.md) szint dedikált erőforrásokat foglal le az üzenetkezelési egységek tekintetében az ügyfél által megadott összes névtérhez. Ezek a dedikált erőforrások kiszámítható átviteli sebességet és késést biztosítanak, és ajánlottak a nagy teljesítményű vagy érzékeny üzemi szintű rendszerek esetében.

Emellett a prémium szint is lehetővé teszi, hogy az ügyfelek Felskálázási kapacitásukat, amikor a számítási feladatok során tüskék jelentkeznek.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Hogyan működik a szabályozás a Service Bus Premiumban?

A Service Bus Premium kizárólagos erőforrás-kiosztásával a szabályozás kizárólag a névtérhez lefoglalt erőforrások korlátain alapul.

Ha a kérések száma nagyobb, mint az aktuális erőforrás, akkor a rendszer a kérelmeket fogja szabályozni.

### <a name="how-will-i-know-that-im-being-throttled"></a>Honnan tudom, hogy szabályozva vagyok?

A szabályozás több módon is azonosítható Azure Service Bus Premium-ban 
  * A **szabályozott kérelmek** megmutatják az [Azure monitor kérelmek metrikáit](service-bus-metrics-azure-monitor.md#request-metrics) , hogy megállapítsák, hány kérelem lett szabályozva.
  * A magas **CPU-használat** azt jelzi, hogy az aktuális erőforrás-lefoglalás magas, és a kérések leszabályozása is lehetséges, ha az aktuális munkaterhelés nem csökken.
  * A nagy **memóriahasználat** azt jelzi, hogy az aktuális erőforrás-elosztás magas, és a kérések leszabályozása akkor is lehetséges, ha az aktuális munkaterhelés nem csökken.

### <a name="how-can-i-avoid-being-throttled"></a>Hogyan kerülhetem el a szabályozást?

Mivel a Service Bus Premium-névtér már rendelkezik dedikált erőforrásokkal, csökkentheti a szabályozás lehetőségét azáltal, hogy a névtérhez lefoglalt üzenetküldési egységek számát a munkaterhelésben lévő tüske (vagy várható) alapján felskálázással csökkenti.

A fel/le skálázás olyan [runbookok](../automation/automation-create-alert-triggered-runbook.md) létrehozásával hajtható végre, amelyek a fenti mérőszámok változásai alapján indíthatók el.

## <a name="faqs"></a>Gyakori kérdések

### <a name="how-does-throttling-affect-my-application"></a>Hogyan befolyásolja a szabályozás az alkalmazást?

A kérések szabályozása esetén az azt jelenti, hogy a szolgáltatás foglalt, mivel az erőforrás-engedélyezésnél több kérésre van felhasználva. Ha ugyanezt a műveletet néhány pillanat múlva újra próbálkozik, ha a szolgáltatás a jelenlegi munkaterhelésen keresztül működött, akkor a kérést tiszteletben tarthatja.

Mivel a szabályozás a Felhőbeli natív szolgáltatás várható viselkedése, az újrapróbálkozási logikát az Azure Service Bus SDK-ba építettük. Az alapértelmezett érték az automatikus újrapróbálkozás egy exponenciális visszalépéssel, így biztosítható, hogy a rendszer minden alkalommal leszabályozza a kérést.

Az alapértelmezett újrapróbálkozási logika minden műveletre érvényes lesz.

### <a name="does-throttling-result-in-data-loss"></a>Az adatvesztést okozó szabályozás?

Azure Service Bus az adatmegőrzésre van optimalizálva, biztosítjuk, hogy a Service Bus eljuttatott összes adatok elkötelezettek legyenek a tárolóba, mielőtt a szolgáltatás visszaigazolja a kérés sikerességét.

Ha a kérés sikeres "ACK" (elismert) Service Bus, akkor azt jelenti, hogy Service Bus sikeresen feldolgozta a kérést. Ha a Service Bus "Nagyváradi" (hiba) értéket ad vissza, akkor azt jelenti, hogy Service Bus nem tudta feldolgozni a kérést, és az ügyfélalkalmazás újra kell próbálkoznia a kéréssel.

A kérelem szabályozása esetén azonban a szolgáltatás arra utal, hogy az erőforrás-korlátozások miatt most nem tudja elfogadni és feldolgozni a kérést. Ez semmilyen adatvesztést nem jelent, mivel Service Bus egyszerűen **nem** nézett a kérelemre. Ebben az esetben a Service Bus SDK alapértelmezett újrapróbálkozási szabályzata biztosítja, hogy a kérést a rendszer végül dolgozza fel.

## <a name="next-steps"></a>Következő lépések

További információt és példákat a Service Bus üzenetkezelés használatával kapcsolatban a következő speciális témakörökben talál:

* [A Service Bus üzenetkezelésének áttekintése](service-bus-messaging-overview.md)
* [Gyors útmutató: üzenetek küldése és fogadása a Azure Portal és a .NET használatával](service-bus-quickstart-portal.md)
* [Oktatóanyag: leltár frissítése Azure Portal és témakörök/előfizetések használatával](service-bus-tutorial-topics-subscriptions-portal.md)

