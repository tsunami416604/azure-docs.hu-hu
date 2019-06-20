---
title: Az Azure Time Series Insights-környezet az adatmegőrzés ismertetése |} A Microsoft Docs
description: Ez a cikk ismerteti az adatok megőrzése az Azure Time Series Insights-környezet szabályozó két beállítást.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: dc192b1e8f5492cdfbfb434b5efb573182c51bb1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164579"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Az Azure Time Series Insightsban az adatmegőrzés ismertetése

Ez a cikk ismerteti a két beállítás, amely hatással van az adatok megőrzése az Azure Time Series Insights-környezetet.

## <a name="video"></a>Videó

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Az alábbi videó a Time Series Insights-adatok megőrzésére és az azt tervezéséről foglalja össze.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Az Azure Time Series-környezetek mindegyike rendelkezik egy beállítást, amely szabályozza, **adatmegőrzési idő**. Az érték 1, 400 napos általi. Az adatok törlődnek a környezet tárolási kapacitás vagy a megőrzési időtartama alapján, amelyiket hamarabb.

Emellett az Azure Time Series környezet rendelkezik egy **tárolási kapacitása túllépve viselkedés** beállítás. Szabályozza bejövő és a végleges törlés viselkedését, ha eléri a maximális kapacitás, a környezetnek. Nincsenek két viselkedésmódok közül választhat, hogy konfigurálásakor:

- **Régi adatok törlése** (alapértelmezett)  
- **Felfüggesztés bejövő forgalom**

> [!NOTE]
> Alapértelmezés szerint, egy új környezet létrehozásakor, a megőrzési van konfigurálva **régi adatok törlése**. Ez a beállítás az Azure Portalon, a létrehozás ideje után szükség szerint bekapcsolható az **konfigurálása** a Time Series Insights-környezet lapján.

A Váltás megőrzési viselkedések további információért tekintse át [megőrzés konfigurálása a Time Series Insights](time-series-insights-how-to-configure-retention.md).

Hasonlítsa össze az adatok megőrzési viselkedés:

## <a name="purge-old-data"></a>Régi adatok törlése

- Ez a viselkedés a Time Series Insights-környezetek esetében az alapértelmezett viselkedést.  
- Ez a viselkedés részesíti előnyben, amikor a felhasználók meg szeretnék bármikor megtekintheti azok *legfrissebb adatok* a Time Series Insights környezetben.
- Ez a viselkedés *kiürítése* adatok egyszer a környezet korlátait (megőrzési idő, méret, vagy count, amelyiket hamarabb elérik) elérésekor. Adatmegőrzési alapértelmezés szerint 30 nap van beállítva.
- A legrégebbi a feldolgozott adatokat van üríti ki az első (FIFO megközelítés).

### <a name="example-one"></a>Egy példa

Fontolja meg egy példakörnyezetet megőrzési viselkedés **továbbra is a bejövő és a régi adatok törlése**:

**Adatmegőrzési idő** 400 napos értékre van állítva. **Kapacitás** S1-egység, amely tartalmazza a teljes kapacitás 30 GB értékre van állítva.   Tegyük fel, a bejövő adatok gyűlnek 500 MB naponta átlagosan. Ebben a környezetben csak is megőrizheti az adatok adott mértéke a bejövő adatokat, mert elérte a maximális kapacitás 60 napon belül 60 napos. A bejövő adatokat összegzi: Minden nap × 60 nap = 30 GB 500 MB.

A 61st napon a környezet a legfrissebb adatokat jelenít meg, de a legrégebbi, 60 napnál régebbi adatok kiürítése. A kiürítés, hogy az új adatok továbbra is vizsgálhatók lehetővé teszi a hely számára az új adatok, a streamelési. Ha a felhasználó által blokkolni adatokat tovább is megtartja, kiegészítő egységek hozzáadásával a környezet méretének növeléséhez, vagy kevesebb adatot küldhet.  

### <a name="example-two"></a>A példában két

Vegye figyelembe a környezet is konfigurált megőrzési viselkedés **továbbra is a bejövő és a régi adatok törlése**. Ebben a példában **adatmegőrzési idő** 180 napos egy alacsonyabb értékre van állítva. **Kapacitás** S1-egység, amely tartalmazza a teljes kapacitás 30 GB értékre van állítva. Annak érdekében, hogy az adatok tárolásához a teljes, 180 nap a napi bejövő forgalom naponta legfeljebb 0.166 GB (166 MB).  

Amikor ez a környezet napi bejövő forgalom 0.166 GB meghaladja a napi, adatokat nem lehet tárolni 180 napig, mivel bizonyos adatok kiürítése beolvasása. Fontolja meg az ugyanabban a környezetben egy foglalt időszakban. Tegyük fel, hogy egy átlagos 0.189 GB / nap növelhető a környezet bejövő forgalom. A foglalt időkereten belül az adatok körülbelül 158 napos megmaradnak (30GB/0.189 = 158,73 nap). Ezúttal nem éri el a kívánt adatok megőrzési időtartammal.

## <a name="pause-ingress"></a>Felfüggesztés bejövő forgalom

- A **bejövő szüneteltetése** beállítás célja annak biztosítása érdekében az adatok nem törlődnek, ha a mérete és száma korlátok elérésekor a megőrzési időszak előtt.  
- **Bejövő forgalom szüneteltetése** biztosít a felhasználóknak, hogy a környezetükben kapacitásának növelése előtt az adatok akkor törlődnek a megőrzési idő megsértése miatt további időt
- Ez segít megvédeni a adatvesztés, de hozhat létre az adatvesztés a legutóbbi lehetőséget, ha a bejövő forgalom fel van függesztve, az eseményforrás a megőrzési időszak után.
- Azonban a környezet a maximális kapacitás elérésekor a környezet felfüggeszti a beérkező adatok mindaddig, amíg a következő további műveletek történnek:

   - Növeli a maximális kapacitást a környezetben több skálázási egység leírtak szerint hozzáadandó [a Time Series Insights-környezet méretezése](time-series-insights-how-to-scale-your-environment.md).
   - Az adatok megőrzési időtartamot, és adatok törlődnek, és a környezet alább a maximális kapacitást.

### <a name="example-three"></a>A példában három

Vegye figyelembe a környezet konfigurálva adatmegőrzési működése **bejövő szüneteltetése**. Ebben a példában a **Adatmegőrzés időtartama** 60 napra van konfigurálva. **Kapacitás** S1-egység három (3) értékre van állítva. Tegyük fel, ebben a környezetben rendelkezik 2 GB-os adatforgalom minden nap. Ebben a környezetben a bejövő forgalom szüneteltetve van a maximális kapacitás elérésekor.

Ekkor az a környezet jeleníti meg az ugyanahhoz az adatkészlethez bejövő folytatja, vagy amíg **továbbra is a bejövő forgalom** engedélyezve van (amely a régebbi adatokat, hogy helyet biztosítson az új adatok lenne kiürítése).

Amikor a bejövő forgalom folytatja:

- Eseményforrás szerint érkezett sorrendben adatfolyamok
- Az események indexelt alapján időbélyegzőik, kivéve, ha az az eseményforrás túllépte az adatmegőrzési házirendek. További információ az esemény forrása megőrzési konfigurációjában [Event Hubs – gyakori kérdések](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Riasztások elkerülése érdekében a bejövő forgalom működését értesítenie kell beállítania. Adatvesztés, mivel az alapértelmezett megőrzési Azure eseményforrások 1 nap. Ezért miután bejövő szüneteltetve van, valószínűleg elveszíti a legfrissebb adatok, ha további műveletet. Kell növeli a kapacitást, vagy váltson viselkedésének **régi adatok törlése** az esetleges adatvesztés elkerülése érdekében.

Az érintett Event hubs, fontolja meg a **Üzenetmegőrzés** tulajdonságot minimális az adatvesztés a Time Series Insightsban szüneteltetése bejövő forgalom esetén.

[![Event hub üzenetmegőrzés.](media/time-series-insights-contepts-retention/event-hub-retention.png)](media/time-series-insights-contepts-retention/event-hub-retention.png#lightbox)

Ha nincsenek megadva tulajdonságok vannak konfigurálva az eseményforrás (`timeStampPropertyName`), Time Series Insights alapértelmezés szerint az event hubs, az x tengely érkezés időbélyeghez. Ha `timeStampPropertyName` úgy konfigurálva, hogy bármi más, a beállított környezeti keresi `timeStampPropertyName` adatcsomag esemény értelmezésekor.

Ha további kapacitást biztosít vagy megnöveli az adatmegőrzés, tekintse meg a környezet vertikális kell [méretezése a Time Series Insights-környezet](time-series-insights-how-to-scale-your-environment.md) további információt.  

## <a name="next-steps"></a>További lépések

- A konfigurálás vagy adatmegőrzési beállítások módosítása további információért tekintse át [megőrzés konfigurálása a Time Series Insights](time-series-insights-how-to-configure-retention.md).
