---
title: Mi az a Log Analytics? | Microsoft Docs
description: "A Log Analytics az Operations Management Suite (OMS) egy szolgáltatása, amely segít összegyűjteni és elemezni a felhőben és a helyszíni környezetekben található erőforrások által létrehozott működési adatokat.  Ez a cikk tömör áttekintést nyújt a Log Analytics különböző összetevőiről, és részletes tartalmakra mutató hivatkozásokat tartalmaz."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: bd90b460-bacf-4345-ae31-26e155beac0e
ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/06/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 09a2b3ccd2048ab2638dd91d1905483f10d6d339


---
# <a name="what-is-log-analytics"></a>Mi az a Log Analytics?
A Log Analytics az [Operations Management Suite\(OMS\)](../operations-management-suite/operations-management-suite-overview.md) egy szolgáltatása, amely segít összegyűjteni és elemezni a felhőben és a helyszíni környezetekben található erőforrások által létrehozott adatokat. A szolgáltatás valós idejű elemzéseket biztosít. Integrált keresést és egyéni irányítópultokat használ az adatok millióinak könnyű elemzéséhez az összes számítási feladat és kiszolgáló esetében, azok fizikai helyétől függetlenül.

## <a name="log-analytics-components"></a>A Log Analytics összetevői
A Log Analytics középpontjában az OMS-tárház áll, amelyet az Azure-felhő üzemeltet.  Az adatok a csatlakozó forrásokból kerülnek be a tárházba az adatforrások konfigurálása és a megoldások előfizetésbe való felvétele révén.  Az adatforrások és megoldások egyaránt különböző rekordtípusokat fognak létrehozni, amelyek saját tulajdonsághalmazzal rendelkeznek, de mégis elemezhetők együtt a tárházra irányuló lekérdezésekben.  Ez lehetővé teszi, hogy ugyanazokat az eszközöket és módszereket használva dolgozzon a különböző források által gyűjtött különböző típusú adatokkal.

![OMS-tárház](media/log-analytics-overview/overview.png)

A csatlakoztatott források azok a számítógépek és egyéb erőforrások, amelyek Log Analytics által összegyűjtött adatokat generálnak.  Ilyenek lehetnek például a közvetlenül csatlakozó [Windows](log-analytics-windows-agents.md)- és [Linux](log-analytics-linux-agents.md)-rendszerű számítógépeken telepített, illetve az egy [csatlakoztatott System Center Operations Manager felügyeleti csoportban](log-analytics-om-agents.md) lévő ügynökök.  A Log Analytics az [Azure Storage](log-analytics-azure-storage.md)-ból is gyűjthet adatokat.

[Adatforrások](log-analytics-data-sources.md) alatt az egyes csatlakoztatott forrásokból gyűjtött adatok különböző fajtáit értjük.  Ez magában foglalja a [Windows](log-analytics-data-sources-windows-events.md)- és Linux-ügynököktől származó eseményeket és [teljesítményadatokat](log-analytics-data-sources-performance-counters.md), továbbá az olyan forrásokat, mint az [IIS-naplók](log-analytics-data-sources-iis-logs.md) és az [egyéni szövegnaplók](log-analytics-data-sources-custom-logs.md).  Konfigurálhatja az összegyűjteni kívánt egyes adatforrásokat, és a konfiguráció automatikusan érvénybe lép minden egyes csatlakoztatott forráson.

## <a name="analyzing-log-analytics-data"></a>Log Analytics-adatok elemzése
A Log Analytics-szal többnyire az OMS-portálon keresztül fog együttműködni, amely bármilyen böngészőben fut, és amely konfigurációs beállításokhoz és több eszközhöz biztosít hozzáférést annak érdekében, hogy az összegyűjtött adatokat elemezni tudja, illetve, hogy műveletet végezhessen rajtuk.  A portálon [naplókereséseket](log-analytics-log-searches.md) használhat, ahol lekérdezéseket hozhat létre az összegyűjtött adatok és az [irányítópultok](log-analytics-dashboards.md) elemzéséhez. Ezeket a legértékesebb kereséseinek grafikus nézeteivel, valamint a további funkciókat és elemzőeszközöket biztosító [megoldásaival](log-analytics-add-solutions.md) testre is szabhatja.

![OMS-portál](media/log-analytics-overview/portal.png)

A Log Analytics által biztosított lekérdezési szintaxis segítségével adatokat olvashat be és vonhat össze a tárházban.  [Naplókeresések](log-analytics-log-searches.md) létrehozásával és mentésével közvetlenül elemezhetők az adatok az OMS-portálon, illetve automatikus futtatásukkal riasztás hozható létre, ha a lekérdezési eredmények fontos feltételt jeleznek.

![Naplókeresés](media/log-analytics-overview/log-search.png)

Ha teljes környezetének állapotáról szeretne gyors grafikus nézetet megjeleníteni, az [irányítópultra](log-analytics-dashboards.md) a mentett naplóbeli keresésekre vonatkozó képi megjelenítéseket vehet fel.   

![Irányítópult](media/log-analytics-overview/dashboard.png)

Ahhoz, hogy a Log Analyticsen kívül végezhessen adatelemzéseket, az OMS-tárházból olyan eszközökbe exportálhat adatokat, mint a [Power BI](log-analytics-powerbi.md) vagy az Excel.  Használhatja a [Naplókeresési API](log-analytics-log-search-api.md)-t is, ha a Log Analytics adatait felhasználó egyéni megoldásokat szeretne kiépíteni, illetve ha más rendszerekkel szeretne integrációt létrehozni.

## <a name="solutions"></a>Megoldások
A megoldások funkciókkal bővítik ki a Log Analytics szolgáltatást.  Ezek elsősorban felhőben futnak, és az OMS-tárházban összegyűjtött adatok elemzését végzik el. Ezen felül új, összegyűjtendő rekordtípusokat is meghatározhatnak, amelyek Naplókeresésekkel, illetve az OMS-irányítópulton lévő megoldás által biztosított további felhasználói felület segítségével elemezhetők.  

![Változáskövetési megoldás](media/log-analytics-overview/change-tracking.png)

A megoldások változatos funkciókkal érhetők el, és a Megoldástárból könnyűszerrel böngészhet az elérhető megoldások között, majd [adhatja hozzá őket az OMS-munkaterülethez](log-analytics-add-solutions.md).  Közülük számosat automatikusan telepíti a rendszer, és azonnal elkezdenek működni, míg mások némi konfigurálást igényelnek.

![Megoldástár](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-architecture"></a>Log Analytics-architektúra
A Log Analytics telepítési követelményei minimálisak, hiszen a központi összetevőket az Azure-felhő üzemelteti.  Ez a szolgáltatásokon túl magában foglalja a tárházat is, amely lehetővé teszi az összegyűjtött adatok összevetésének és elemzésének elvégzését.  A portál bármilyen böngészőből elérhető, így nincsenek ügyfélszoftverre vonatkozó követelmények.

[Windows](log-analytics-windows-agents.md)- és [Linux](log-analytics-linux-agents.md)-rendszerű számítógépeken ügynököket kell telepítenie, ám azok a számítógépek, amelyek már egy [csatlakoztatott SCOM-felügyeleti csoport](log-analytics-om-agents.md) tagjai, nem igényelnek további ügynököket.  Az SCOM-ügynökök továbbra is a felügyeleti kiszolgálókkal fognak kommunikálni, amelyek adataikat a Log Analyticsnek fogják továbbítani.  Néhány megoldás esetében azonban ügynökökre lesz szükség a Log Analytics-szel való közvetlen kommunikációhoz.  A kommunikáció követelményeit az egyes megoldásokra vonatkozó dokumentáció fogja megadni.

[Log Analytics-regisztrációkor](log-analytics-get-started.md) egy OMS-munkaterületet fog létrehozni.  A munkaterületre tekinthet úgy, mint egy saját adattárházzal, -forrásokkal és megoldásokkal rendelkező egyedülálló OMS-környezetre. Több munkaterületet is létrehozhat az előfizetésében, melyekkel több környezetet, például munka- vagy tesztelési környezeteket támogathat.

![Log Analytics-architektúra](media/log-analytics-overview/architecture.png)

## <a name="next-steps"></a>Következő lépések
* [Regisztráljon ingyenes Log Analytics-fiókra](log-analytics-get-started.md), és saját környezetében tesztelheti működését.
* Tekintse meg a különböző elérhető, az adatokat az OMS-tárházba gyűjtő [Adatforrásokat](log-analytics-data-sources.md).
* [Böngésszen a Megoldástárban az elérhető megoldások között](log-analytics-add-solutions.md), ha funkciókkal szeretné bővíteni a Log Analytics-szolgáltatást.




<!--HONumber=Nov16_HO2-->


