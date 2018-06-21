---
title: Az adatmegőrzés Azure idő adatsorozat Insights környezetében megértése |} Microsoft Docs
description: Ez a cikk ismerteti az adatmegőrzés Azure idő adatsorozat Insights környezetében szabályozó két beállításokat.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: kfile
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: c8fad0d626fc0b9754f7f04c8000f48976eb766b
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293095"
---
# <a name="understand-data-retention-in-time-series-insights"></a>Adatok megőrzési idő adatsorozat insightsban ismertetése
Ez a cikk ismerteti a két beállítás, amely hatással van az adatok megőrzési idő adatsorozat Insights (ÁME) környezetében.

Minden egyes ÁME környezet rendelkezik a beállítást, amely szabályozza **adatmegőrzési időtartam**. Az érték 1 és 400 nap is. Az adatok törlése környezet tárolási kapacitás vagy az adatmegőrzési időtartam alapján (1-400), amelyik előbb következik be.

Minden egyes ÁME környezet rendelkezik egy további beállítás **tárolási korlátja túllépve viselkedés**. Ez a beállítás be- és végleges törlése viselkedését szabályozza, a maximális kapacitás, a környezet elérésekor. Nincsenek két viselkedésmódok közül választhat:
- **Régi adatok törlése** (alapértelmezett)  
- **Felfüggesztés érkező**

> [!NOTE]
> Alapértelmezés szerint egy új környezet létrehozásakor, a megőrzési van konfigurálva **régi adatok törlése**. Ez a beállítás az Azure-portált használja, a létrehozás ideje után szükség szerint bekapcsolható a **konfigurálása** a ÁME környezet oldalán.

A megőrzési viselkedések váltás információkért tekintse át [konfigurálása a megőrzési idő adatsorozat insightsban](time-series-insights-how-to-configure-retention.md).

Hasonlítsa össze a megőrzési működéshez:

## <a name="purge-old-data"></a>Régi adatok törlése
- Ez a viselkedés az alapértelmezett működést és egyező viselkedés ÁME környezetekhez kiállította elindított nyilvános előzetes verzió óta exhibits ÁME környezetben.  
- Ez a viselkedés részesíti előnyben, ahhoz, hogy mindig megtekintheti a *adatok* az ÁME környezetben. 
- Ez a viselkedés *üríti* adatok egyszer a környezet korlátai (megőrzési idő, méret, vagy száma, amelyik előbb következik be) elérésekor. Megőrzési alapértelmezés szerint 30 napra van állítva. 
- A legrégebbi feldolgozott adatok (FIFO megközelítés) első törlődnek.

### <a name="example-1"></a>1. példa:
Vegye figyelembe a megőrzési viselkedése például környezet **továbbra is érkező és a régi adatok törlése**: Ebben a példában **adatmegőrzési időtartam** 400 nap. **Kapacitás** S1 egység, amely tartalmazza a teljes kapacitás 30 GB értékre van állítva.   Tegyük fel, a bejövő adatok összesít 500 MB-ra naponta átlagosan. Ebben a környezetben csak az adatok mértéke a bejövő adatok, mert 60 nap eléri a maximális kapacitás 60 napos továbbra is. A bejövő adatok összesít: 500 MB x nap 60 nap = 30 GB alapú egységárát. 

Ebben a példában a 61st napon a környezet jeleníti meg a legfrissebb adatokat, de üríti a legrégebbi adatokat, a régebbi, mint 60 nap. A kiürítési lehetővé teszi az új adatok adatfolyamként történő, illetve, hogy az új adatok továbbra is vizsgálják. 

Ha a felhasználó által a kívánt hosszabb adatok megőrzése mellett, további egységek hozzáadásával növelhető a környezet mérete, vagy tolható kevesebb adatot.  

### <a name="example-2"></a>2. példa
Fontolja meg egy olyan környezetben is konfigurált megőrzési viselkedés **továbbra is érkező és a régi adatok törlése**. Ebben a példában **adatmegőrzési időtartam** 180 napos alacsonyabb értékre van beállítva. **Kapacitás** S1 egység, amely tartalmazza a teljes kapacitás 30 GB értékre van állítva. Ahhoz, hogy az adatok a teljes 180 napig vannak tárolva, a napi belépési naponta legfeljebb 0.166 GB (166 MB).  

Amikor ez a környezet napi érkező meghaladja 0.166 GB-os napi, adatait nem lehet tárolni 180 napig tart, mert bizonyos adatok lekérdezi törlődnek. Egy foglalt időtartományban, fontolja meg az ugyanabban a környezetben. Tegyük fel, a környezet érkező sebessége megnövelheti az átlagos 0.189 GB, naponta. A megadott foglalt idő alatt, körülbelül 158 napnyi adat megmaradnak (30GB/0.189 = 158.73 napnyi megőrzési). Most nem éri el a kívánt adatok megőrzési időn.

## <a name="pause-ingress"></a>Felfüggesztés érkező
- Ez a viselkedés célja, hogy az adatok nem törlődnek, ha a megőrzési időszak előtt eléri a méretének és számának korlátai nem biztosítására.  
- Ez a viselkedés biztosít a felhasználók számára a környezet kapacitásának növelése előtt az adatok megőrzési idejének megsértése miatt törlődnek további időt
- Ez a viselkedés segít megvédeni a adatvesztés, de hoz létre a legfrissebb adatok elvesztését, lehetőséget, ha érkező fel van függesztve, az eseményforrás a megőrzési időtartamon túl.
- Azonban olyan környezetet maximális kapacitás elérésekor a környezet felfüggesztése adatok érkező további műveletek történnek meg, amíg: 
   - A környezet maximális kapacitás növelése. További információkért lásd: [idő adatsorozat Insights környezetét méretezése](time-series-insights-how-to-scale-your-environment.md) hozzáadása több skálázási egységet.
   - Az adatok megőrzési időtartamot, és az adatok törlődnek, így mihamarabb elérhetővé tenni a környezetben, a maximális kapacitásához alatt.

### <a name="example-3"></a>3. példa:
Fontolja meg egy olyan környezetben a konfigurált megőrzési viselkedése **érkező szüneteltetése**. Ebben a példában a **adatmegőrzési időtartam** 60 napra van konfigurálva. **Kapacitás** S1 3 egységek értékre van állítva. Tegyük fel, a környezet rendelkezik 2 GB-os adatok érkező minden nap. Ebben a környezetben érkező szünetel a maximális kapacitás elérésekor. Idő lejárta után a környezet érkező folytatása, vagy amíg a "continue érkező" engedélyezve van (amely a régebbi adatokat, hogy helyet biztosítsanak az új adatokat szeretné kiüríteni) mutatja az ugyanahhoz az adatkészlethez. 

Amikor érkező folytatja:
- Eseményforrás megkapta sorrendben adatfolyamok
- Az események indexelt alapján időbélyegzőik, kivéve, ha az eseményforrás a túllépte az adatmegőrzési házirendek. További információ az esemény adatmegőrzési beállítását [Event Hubs – gyakori kérdések](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Felfüggesztés érkező elkerülése érdekében biztosítanak riasztások kell beállítani. Adatvesztés lehetőség, mert az alapértelmezett megőrzési Azure eseményforrások 1 nap. Ezért ha érkező fel van függesztve, valószínűleg elvesznek a legfrissebb adatok kivéve, ha szükséges további művelet. Kell kapacitásbővítés, vagy váltson viselkedést **régi adatok törlése** esetleges adatvesztés elkerülése érdekében.

Az érintett Event Hubs, fontolja meg a **üzenet megőrzési** adatveszteség minimálisra idő adatsorozat insightsban szünet érkező esetén tulajdonságot.

![Event hub üzenet megőrzése.](media/time-series-insights-contepts-retention/event-hub-retention.png)

Ha nincsenek tulajdonságok konfigurálása a forrás (timeStampPropertyName), ÁME alapértelmezés szerint az event hubs eseményközpontot, az x tengely érkezés időbélyeg. Ha timeStampPropertyName konfigurálni, hogy egy másik, a környezet események elemzésekor keresi a konfigurált timeStampPropertyName adatcsomag. 

Ha a környezet növelheti olyan további kapacitást vagy megnöveli az adatmegőrzési, tekintse meg kell [idő adatsorozat Insights környezetét méretezése](time-series-insights-how-to-scale-your-environment.md) további információt.  

## <a name="next-steps"></a>További lépések
A megőrzési viselkedés váltás információkért tekintse át [konfigurálása a megőrzési idő adatsorozat insightsban](time-series-insights-how-to-configure-retention.md).
