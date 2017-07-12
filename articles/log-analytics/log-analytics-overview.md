---
title: "Mi az Operations Management Suite (OMS) részét képező Log Analytics? | Microsoft Docs"
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
ms.date: 06/12/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 391870380280d6783223849ce383113bf1dd6d31
ms.contentlocale: hu-hu
ms.lasthandoff: 06/13/2017


---
<a id="what-is-log-analytics" class="xliff"></a>

# Mi az a Log Analytics?
A Log Analytics az [Operations Management Suite\(OMS\)](../operations-management-suite/operations-management-suite-overview.md) egy szolgáltatása, amely figyeli a felhőbeli és helyszíni környezeteket az elérhetőségük és a teljesítményük fenntartása érdekében.  A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson.  Ez a cikk tömören összefoglalja a Log Analytics nyújtotta értékeket, áttekintés nyújt a működéséről, és részletes tartalmakra mutató hivatkozásokat tartalmaz, így még többet tudhat meg.

<a id="is-log-analytics-for-you" class="xliff"></a>

## A Log Analytics az Önnek megfelelő szolgáltatás?
Ha jelenleg nincs megfigyelés üzembe helyezve az Azure-környezetében, érdemes az [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) szolgáltatással kezdeni, amely az Azure-erőforrások figyelési adatait gyűjti és elemzi.  A Log Analytics [adatokat gyűjt az Azure Monitorból](log-analytics-azure-storage.md), hogy összehasonlítsa őket egyéb adatokkal, valamint további elemzést hajtson végre.

Ha szeretné figyelni a helyszíni környezetet, illetve már megvalósította a figyelést az olyan szolgáltatások használatával, mint például az Azure Monitor vagy a System Center Operations Manager, akkor a Log Analytics jelentős értéket képviselhet.  Képes egyetlen tárházba gyűjteni a közvetlenül az ügynökökből és ezekből az egyéb eszközökből származó adatokat.  A Log Analytics elemzőeszközei (például naplókeresés, nézetek és megoldások) az összes begyűjtött adattal működnek, így lehetővé teszik a teljes környezet központosított elemzését.


<a id="using-log-analytics" class="xliff"></a>

## A Log Analytics használata
A Log Analytics szolgáltatást elérheti az OMS-portálon vagy az Azure Portalon keresztül, amelyek bármely böngészőben futnak, és hozzáférést biztosítanak a konfigurációs beállításokhoz és több eszközhöz, amelyekkel elemezheti az összegyűjtött adatokat, és műveleteket végezhet el rajtuk.  A portálon [naplókereséseket](log-analytics-log-searches.md) használhat, ahol lekérdezéseket hozhat létre az összegyűjtött adatok és az [irányítópultok](log-analytics-dashboards.md) elemzéséhez. Ezeket a legértékesebb kereséseinek grafikus nézeteivel, valamint a további funkciókat és elemzőeszközöket biztosító [megoldásaival](log-analytics-add-solutions.md) testre is szabhatja.

Az alábbi, OMS-portálról származó képen az irányítópult látható, amely a munkaterületen telepített [megoldások](#add-functionality-with-management-solutions) összegző információit jeleníti meg  Bármelyik csempére kattinthat, hogy további részleteket kapjon a megoldáshoz tartozó adatokról.

![OMS-portál](media/log-analytics-overview/portal.png)

A Log Analyticsben található lekérdezési nyelv segítségével adatokat olvashat be és vonhat össze a tárházban.  [Naplókeresések](log-analytics-log-searches.md) létrehozásával és mentésével közvetlenül elemezhetők az adatok a portálon, illetve automatikus futtatásukkal riasztás hozható létre, ha a lekérdezési eredmények fontos feltételt jeleznek.

![Naplókeresés](media/log-analytics-overview/log-search.png)

Ha teljes környezetének állapotáról szeretne gyors grafikus nézetet kapni, az [irányítópultra](log-analytics-dashboards.md) a mentett naplóbeli keresésekre vonatkozó képi megjelenítéseket vehet fel.   

![Irányítópult](media/log-analytics-overview/dashboard.png)

Ahhoz, hogy a Log Analyticsen kívül végezhessen adatelemzéseket, az OMS-tárházból olyan eszközökbe exportálhat adatokat, mint a [Power BI](log-analytics-powerbi.md) vagy az Excel.  Használhatja a [Naplókeresési API](log-analytics-log-search-api.md)-t is, ha a Log Analytics adatait felhasználó egyéni megoldásokat szeretne kiépíteni, illetve ha más rendszerekkel szeretne integrációt létrehozni.

<a id="add-functionality-with-management-solutions" class="xliff"></a>

## Funkciók hozzáadása felügyeleti megoldásokkal
A [Felügyeleti megoldások](log-analytics-add-solutions.md) további funkciókkal bővítik az OMS-t, további adat- és elemzőeszközöket biztosítva a Log Analytics számára.  Ezen felül új, összegyűjtendő rekordtípusokat is meghatározhatnak, amelyek Naplókeresésekkel, illetve az irányítópulton lévő megoldás által biztosított további felhasználói felület segítségével elemezhetők.  Az alábbi mintaábrán a [Változáskövetési megoldás](log-analytics-change-tracking.md) látható.

![Változáskövetési megoldás](media/log-analytics-overview/change-tracking.png)

A megoldások változatos funkciókkal érhetők el, valamint a további hozzáadott megoldások listája is folyamatosan bővül.  A Megoldástárból vagy az Azure Marketplace-en keresztül könnyűszerrel böngészhet az elérhető megoldások között, majd [hozzáadhatja őket az OMS-munkaterülethez](log-analytics-add-solutions.md).  Számos megoldást automatikusan telepíti a rendszer, és azonnal elkezdenek működni, míg mások némi konfigurálást igényelnek.

![Megoldástár](media/log-analytics-overview/solution-gallery.png)

<a id="log-analytics-components" class="xliff"></a>

## A Log Analytics összetevői
A Log Analytics középpontjában az OMS-tárház áll, amelyet az Azure-felhő üzemeltet.  Az adatok a csatlakozó forrásokból kerülnek be a tárházba az adatforrások konfigurálása és a megoldások előfizetésbe való felvétele révén.  Az adatforrások és megoldások egyaránt különböző rekordtípusokat fognak létrehozni, amelyek saját tulajdonsághalmazzal rendelkeznek, de mégis elemezhetők együtt a tárházra irányuló lekérdezésekben.  Ez lehetővé teszi, hogy ugyanazokat az eszközöket és módszereket használva dolgozzon a különböző források által gyűjtött különböző típusú adatokkal.

![OMS-tárház](media/log-analytics-overview/overview.png)

A csatlakoztatott források azok a számítógépek és egyéb erőforrások, amelyek Log Analytics által összegyűjtött adatokat generálnak.  Ilyenek lehetnek például a közvetlenül csatlakozó [Windows](log-analytics-windows-agents.md)- és [Linux](log-analytics-linux-agents.md)-rendszerű számítógépeken telepített, illetve az egy [csatlakoztatott System Center Operations Manager felügyeleti csoportban](log-analytics-om-agents.md) lévő ügynökök.  Az Azure-erőforrásokhoz a Log Analytics az [Azure Monitor és Azure Diagnostics](log-analytics-azure-storage.md) szolgáltatásokból gyűjt adatot.

[Adatforrások](log-analytics-data-sources.md) alatt az egyes csatlakoztatott forrásokból gyűjtött adatok különböző fajtáit értjük.  Ezek magukban foglalják a [Windows](log-analytics-data-sources-windows-events.md)- és Linux-ügynököktől származó [eseményeket](log-analytics-data-sources-windows-events.md) és [teljesítményadatokat](log-analytics-data-sources-performance-counters.md), továbbá az olyan forrásokat, mint az [IIS-naplók](log-analytics-data-sources-iis-logs.md) és az [egyéni szövegnaplók](log-analytics-data-sources-custom-logs.md).  Konfigurálhatja az összegyűjteni kívánt egyes adatforrásokat, és a konfiguráció automatikusan érvénybe lép minden egyes csatlakoztatott forráson.

Egyéni követelmények érvényesülése esetén a [HTTP-adatgyűjtő API](log-analytics-data-collector-api.md) használatával írhat adatokat az adattárba egy REST API-ügyfélből.

<a id="log-analytics-architecture" class="xliff"></a>

## Log Analytics-architektúra
A Log Analytics telepítési követelményei minimálisak, hiszen a központi összetevőket az Azure-felhő üzemelteti.  Ez a szolgáltatásokon túl magában foglalja a tárházat is, amely lehetővé teszi az összegyűjtött adatok összevetésének és elemzésének elvégzését.  A portál bármilyen böngészőből elérhető, így nincsenek ügyfélszoftverre vonatkozó követelmények.

[Windows](log-analytics-windows-agents.md)- és [Linux](log-analytics-linux-agents.md)-rendszerű számítógépeken ügynököket kell telepítenie, ám azok a számítógépek, amelyek már egy [csatlakoztatott SCOM-felügyeleti csoport](log-analytics-om-agents.md) tagjai, nem igényelnek további ügynököket.  Az SCOM-ügynökök továbbra is a felügyeleti kiszolgálókkal fognak kommunikálni, amelyek adataikat a Log Analyticsnek fogják továbbítani.  Néhány megoldás esetében azonban ügynökökre lesz szükség a Log Analytics-szel való közvetlen kommunikációhoz.  A kommunikáció követelményeit az egyes megoldásokra vonatkozó dokumentáció fogja megadni.

[Log Analytics-regisztrációkor](log-analytics-get-started.md) egy OMS-munkaterületet fog létrehozni.  A munkaterületre tekinthet úgy, mint egy saját adattárházzal, -forrásokkal és megoldásokkal rendelkező egyedülálló Log Analytics-környezetre. Több munkaterületet is létrehozhat az előfizetésében, melyekkel több környezetet, például munka- vagy tesztelési környezeteket támogathat.

![Log Analytics-architektúra](media/log-analytics-overview/architecture.png)

<a id="next-steps" class="xliff"></a>

## Következő lépések
* [Regisztráljon ingyenes Log Analytics-fiókra](log-analytics-get-started.md), és saját környezetében tesztelheti működését.
* Tekintse meg a különböző elérhető, az adatokat az OMS-tárházba gyűjtő [Adatforrásokat](log-analytics-data-sources.md).
* [Böngésszen a Megoldástárban az elérhető megoldások között](log-analytics-add-solutions.md), ha funkciókkal szeretné bővíteni a Log Analytics-szolgáltatást.


