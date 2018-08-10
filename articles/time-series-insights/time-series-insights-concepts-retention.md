---
title: Az Azure Time Series Insights-környezet az adatmegőrzés ismertetése |} A Microsoft Docs
description: Ez a cikk ismerteti az adatok megőrzése az Azure Time Series Insights-környezet szabályozó két beállítást.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 204a9d64d14fe190cb0de73c964bb95b4b9b475f
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628766"
---
# <a name="understand-data-retention-in-time-series-insights"></a>A Time Series Insightsban az adatmegőrzés ismertetése
Ez a cikk ismerteti a két beállítás, amely hatással van az adatok megőrzésére a Time Series Insights (TSI) környezetben.

Minden egyes TSI-környezet rendelkezik egy beállítást, amely szabályozza, **adatmegőrzési idő**. Az érték 1 és 400 nap általi. Az adatok törlődnek a környezet tárolási kapacitás vagy a megőrzési időtartamát (1 – 400) alapján, amelyiket hamarabb.

A TSI-környezeteket egy további beállítása megfelelő-e **tárolási kapacitása túllépve viselkedés**. Ezzel a beállítással a bejövő és a végleges törlés viselkedését szabályozza, ha eléri a maximális kapacitás, a környezetnek. Nincsenek két viselkedésmódok közül választhat:
- **Régi adatok törlése** (alapértelmezett)  
- **Felfüggesztés bejövő forgalom**

> [!NOTE]
> Alapértelmezés szerint, egy új környezet létrehozásakor, a megőrzési van konfigurálva **régi adatok törlése**. Ez a beállítás az Azure Portalon, a létrehozás ideje után szükség szerint bekapcsolható az **konfigurálása** lap a TSI-környezet.

A Váltás megőrzési viselkedések további információért tekintse át [megőrzés konfigurálása a Time Series Insights](time-series-insights-how-to-configure-retention.md).

Hasonlítsa össze az adatok megőrzési viselkedés:

## <a name="purge-old-data"></a>Régi adatok törlése
- Ez a viselkedés a TSI-környezetek és az azonos viselkedés TSI környezetek felmerült indított nyilvános előzetes verziója óta tárgyi bizonyítékokat alapértelmezett viselkedése.  
- Ez a viselkedés részesíti előnyben, amikor a felhasználók meg szeretnék bármikor megtekintheti azok *legfrissebb adatok* a TSI környezetben. 
- Ez a viselkedés *kiürítése* adatok egyszer a környezet korlátait (megőrzési idő, méret, vagy count, amelyiket hamarabb elérik) elérésekor. Adatmegőrzési alapértelmezés szerint 30 nap van beállítva. 
- A legrégebbi a feldolgozott adatokat van üríti ki az első (FIFO megközelítés).

### <a name="example-1"></a>1. példa:
Fontolja meg egy példakörnyezetet megőrzési viselkedés **továbbra is a bejövő és a régi adatok törlése**: Ebben a példában **adatmegőrzési idő** 400 napos értékre van állítva. **Kapacitás** S1-egység, amely tartalmazza a teljes kapacitás 30 GB értékre van állítva.   Tegyük fel, a bejövő adatok gyűlnek 500 MB naponta átlagosan. Ebben a környezetben csak is megőrizheti az adatok adott mértéke a bejövő adatokat, mert elérte a maximális kapacitás 60 napon belül 60 napos. A bejövő adatokat gyűlnek: 500 MB-ot minden nap × 60 nap = 30 GB. 

Ebben a példában a 61st napon a környezet a legfrissebb adatokat jelenít meg, de a legrégebbi, 60 napnál régebbi adatok kiürítése. A kiürítés, hogy az új adatok továbbra is vizsgálhatók lehetővé teszi a hely számára az új adatok, a streamelési. 

Ha a felhasználó által blokkolni adatokat tovább is megtartja, kiegészítő egységek hozzáadásával a környezet méretének növeléséhez, vagy kevesebb adatot küldhet.  

### <a name="example-2"></a>2. példa
Vegye figyelembe a környezet is konfigurált megőrzési viselkedés **továbbra is a bejövő és a régi adatok törlése**. Ebben a példában **adatmegőrzési idő** 180 napos egy alacsonyabb értékre van állítva. **Kapacitás** S1-egység, amely tartalmazza a teljes kapacitás 30 GB értékre van állítva. Annak érdekében, hogy az adatok tárolásához a teljes, 180 nap a napi bejövő forgalom naponta legfeljebb 0.166 GB (166 MB).  

Amikor ez a környezet napi bejövő forgalom 0.166 GB meghaladja a napi, adatokat nem lehet tárolni 180 napig, mivel bizonyos adatok kiürítése beolvasása. Fontolja meg az ugyanabban a környezetben egy foglalt időszakban. Tegyük fel, hogy egy átlagos 0.189 GB / nap növelhető a környezet bejövő forgalom. A foglalt időkereten belül az adatok körülbelül 158 napos megmaradnak (30GB/0.189 = 158,73 nap). Ezúttal nem éri el a kívánt adatok megőrzési időtartammal.

## <a name="pause-ingress"></a>Felfüggesztés bejövő forgalom
- Ez a viselkedés célja annak érdekében, hogy az adatok nem törlődnek, ha a mérete és száma korlátok elérésekor a megőrzési időszak előtt.  
- Ez a viselkedés biztosít a felhasználóknak, hogy a környezetükben kapacitásának növelése előtt az adatok akkor törlődnek a megőrzési idő megsértése miatt további időt
- Ez a viselkedés segít megvédeni az adatvesztés, de lehetőséget nyújt a legújabb adatok elvesztését létrehozza, ha a bejövő forgalom fel van függesztve, az eseményforrás a megőrzési időszak után.
- Azonban a környezet a maximális kapacitás elérésekor a környezet felfüggeszti a beérkező adatok mindaddig, amíg a további műveletek történnek: 
   - Növeli a maximális kapacitást a környezetben. További információkért lásd: [méretezése a Time Series Insights-környezet](time-series-insights-how-to-scale-your-environment.md) több skálázási egység hozzáadása.
   - Az adatok megőrzési időtartamot, és adatok törlődnek, így történő visszaállítását a környezet alább a maximális kapacitást.

### <a name="example-3"></a>3. példa:
Vegye figyelembe a környezet konfigurálva adatmegőrzési működése **bejövő szüneteltetése**. Ebben a példában a **Adatmegőrzés időtartama** 60 napra van konfigurálva. **Kapacitás** 3 egységet a S1 értékre van állítva. Tegyük fel, ebben a környezetben rendelkezik 2 GB-os adatforgalom minden nap. Ebben a környezetben a bejövő forgalom szüneteltetve van a maximális kapacitás elérésekor. Ekkor az a környezet bejövő folytatja, vagy amíg a "bejövő continue" engedélyezve van (amely régebbi adatokat, hogy helyet biztosítson az új adatok lenne végleges törlése) bemutatja az ugyanahhoz az adatkészlethez. 

Amikor a bejövő forgalom folytatja:
- Eseményforrás szerint érkezett sorrendben adatfolyamok
- Az események indexelt alapján időbélyegzőik, kivéve, ha az az eseményforrás túllépte az adatmegőrzési házirendek. További információ az esemény forrása megőrzési konfigurációjában [Event Hubs – gyakori kérdések](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Riasztások elkerülése érdekében a bejövő forgalom működését értesítenie kell beállítania. Adatvesztés, mivel az alapértelmezett megőrzési Azure eseményforrások 1 nap. Ezért miután bejövő szüneteltetve van, valószínűleg elveszíti a legfrissebb adatok, ha további műveletet. Kell növeli a kapacitást, vagy váltson viselkedésének **régi adatok törlése** az esetleges adatvesztés elkerülése érdekében.

Az érintett Event hubs, fontolja meg a **Üzenetmegőrzés** tulajdonságot minimális az adatvesztés a Time Series Insightsban szüneteltetése bejövő forgalom esetén.

![Event hub üzenetmegőrzés.](media/time-series-insights-contepts-retention/event-hub-retention.png)

Ha nincsenek megadva tulajdonságok az eseményforrás (timeStampPropertyName) vannak konfigurálva, a TSI alapértelmezés szerint az event hubs, az x tengely érkezés időbélyeghez. Ha valami mást timeStampPropertyName van konfigurálva, a környezet esemény értelmezésekor keresi a konfigurált timeStampPropertyName adatcsomag. 

Ha további kapacitást biztosít vagy megnöveli az adatmegőrzés, tekintse meg a környezet vertikális kell [méretezése a Time Series Insights-környezet](time-series-insights-how-to-scale-your-environment.md) további információt.  

## <a name="next-steps"></a>További lépések
A Váltás megőrzési viselkedése további információért tekintse át [megőrzés konfigurálása a Time Series Insights](time-series-insights-how-to-configure-retention.md).
