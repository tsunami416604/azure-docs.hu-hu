---
title: Mi az a Log Analytics az Azure-ban? | Microsoft Docs
description: "A Log Analytics az Azure egy szolgáltatása, amely segít összegyűjteni és elemezni a felhőben és a helyszíni környezetekben található erőforrások által létrehozott működési adatokat.  Ez a cikk tömör áttekintést nyújt a Log Analytics különböző összetevőiről, és részletes tartalmakra mutató hivatkozásokat tartalmaz."
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
ms.date: 01/24/2018
ms.author: bwren
ms.openlocfilehash: a95528f5bd259a36ea96c7bc0660ca082c09d6e6
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="what-is-log-analytics"></a>Mi az a Log Analytics?
A Log Analytics az Azure egy szolgáltatása, amely figyeli a felhőbeli és helyszíni környezeteket a rendelkezésre állásuk és teljesítményük fenntartása érdekében.  A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson.  Ez a cikk tömören összefoglalja a Log Analytics nyújtotta értékeket, áttekintés nyújt a működéséről, és részletes tartalmakra mutató hivatkozásokat tartalmaz, így még többet tudhat meg.

## <a name="is-log-analytics-for-you"></a>A Log Analytics az Önnek megfelelő szolgáltatás?
Ha jelenleg nincs monitorozás üzembe helyezve az Azure-környezetében, érdemes az [Azure Monitorral](../monitoring-and-diagnostics/monitoring-overview.md) kezdenie, amely az Azure-erőforrások figyelési adatait gyűjti és elemzi.  A Log Analytics [adatokat gyűjt az Azure Monitorból](log-analytics-azure-storage.md), hogy összehasonlítsa őket egyéb adatokkal, valamint további elemzést hajtson végre.

Ha szeretné figyelni a helyszíni környezetet, illetve már megvalósította a figyelést az olyan szolgáltatások használatával, mint például az Azure Monitor vagy a System Center Operations Manager, akkor a Log Analytics jelentős értéket képviselhet.  Képes egyetlen tárházba gyűjteni a közvetlenül az ügynökökből és ezekből az egyéb eszközökből származó adatokat.  A Log Analytics elemzőeszközei (például naplókeresés, nézetek és megoldások) az összes begyűjtött adattal működnek, így lehetővé teszik a teljes környezet központosított elemzését.


## <a name="using-log-analytics"></a>A Log Analytics használata
A Log Analytics szolgáltatást elérheti az Azure Portalon keresztül, amely bármely böngészőben futtatható, és hozzáférést biztosít a konfigurációs beállításokhoz és több eszközhöz, amelyekkel elemezheti az összegyűjtött adatokat, és műveleteket végezhet el rajtuk.  A portálon [naplókereséseket](log-analytics-log-searches.md) használhat, ahol lekérdezéseket hozhat létre az összegyűjtött adatok és az [irányítópultok](log-analytics-dashboards.md) elemzéséhez. Ezeket a legértékesebb kereséseinek grafikus nézeteivel, valamint a további funkciókat és elemzőeszközöket biztosító [megoldásaival](log-analytics-add-solutions.md) testre is szabhatja.

Az alábbi képen a munkaterületen telepített [megoldások](#add-functionality-with-management-solutions) összegző információit megjelenítő Áttekintés képernyő látható.  Bármelyik csempére kattinthat, hogy további részleteket kapjon a megoldáshoz tartozó adatokról.

![OMS-portál](media/log-analytics-overview/portal.png)

A Log Analyticsben található lekérdezési nyelv segítségével adatokat olvashat be és vonhat össze a tárházban.  [Naplókeresések](log-analytics-log-searches.md) létrehozásával és mentésével közvetlenül elemezhetők az adatok a portálon, illetve automatikus futtatásukkal riasztás hozható létre, ha a lekérdezési eredmények fontos feltételt jeleznek.

![Naplókeresés](media/log-analytics-overview/log-search.png)

A Log Analyticsen kívüli adatelemzésekhez olyan eszközökbe exportálhatja az adatokat, mint a [Power BI](log-analytics-powerbi.md) vagy az Excel.  Használhatja a [Naplókeresési API](log-analytics-log-search-api.md)-t is, ha a Log Analytics adatait felhasználó egyéni megoldásokat szeretne kiépíteni, illetve ha más rendszerekkel szeretne integrációt létrehozni.

## <a name="add-functionality-with-management-solutions"></a>Funkciók hozzáadása felügyeleti megoldásokkal
A [Felügyeleti megoldások](log-analytics-add-solutions.md) további funkciókkal bővítik a Log Analytics szolgáltatást, további adat- és elemzőeszközöket biztosítva a Log Analytics számára.  Ezen felül új, összegyűjtendő rekordtípusokat is meghatározhatnak, amelyek Naplókeresésekkel, illetve az irányítópulton lévő megoldás által biztosított további felhasználói felület segítségével elemezhetők.  Az alábbi mintaábrán a [Változáskövetési megoldás](log-analytics-change-tracking.md) látható.

![Változáskövetési megoldás](media/log-analytics-overview/change-tracking.png)

A megoldások változatos funkciókkal érhetők el, valamint a további hozzáadott megoldások listája is folyamatosan bővül.  Az Azure Marketplace-en keresztül könnyűszerrel böngészhet az elérhető megoldások között, majd [hozzáadhatja őket a munkaterülethez](log-analytics-add-solutions.md).  Számos megoldást automatikusan telepíti a rendszer, és azonnal elkezdenek működni, míg mások némi konfigurálást igényelnek.

![Megoldástár](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-components"></a>A Log Analytics összetevői
A Log Analytics középpontjában az összegyűjtött adatokat tartalmazó adattár áll, amelyet az Azure-felhő üzemeltet.  Az adatok a csatlakozó forrásokból származnak az adatforrások konfigurálása és a megoldások előfizetésbe való felvétele révén.  Az adatforrások és megoldások egyaránt különböző rekordtípusokat fognak létrehozni, amelyek saját tulajdonsághalmazzal rendelkeznek, de mégis elemezhetők együtt a tárházra irányuló lekérdezésekben.  Ez lehetővé teszi, hogy ugyanazokat az eszközöket és módszereket használva dolgozzon a különböző források által gyűjtött különböző típusú adatokkal.

![A Log Analytics összetevői](media/log-analytics-overview/overview.png)

A csatlakoztatott források azok a számítógépek és egyéb erőforrások, amelyek Log Analytics által összegyűjtött adatokat generálnak.  Ilyenek lehetnek például a közvetlenül csatlakozó [Windows](log-analytics-windows-agent.md)- és [Linux](log-analytics-linux-agents.md)-rendszerű számítógépeken telepített, illetve az egy [csatlakoztatott System Center Operations Manager felügyeleti csoportban](log-analytics-om-agents.md) lévő ügynökök.  Az Azure-erőforrásokhoz a Log Analytics az [Azure Monitor és Azure Diagnostics](log-analytics-azure-storage.md) szolgáltatásokból gyűjt adatot.

[Adatforrások](log-analytics-data-sources.md) alatt az egyes csatlakoztatott forrásokból gyűjtött adatok különböző fajtáit értjük.  Ezek magukban foglalják a [Windows](log-analytics-data-sources-windows-events.md)- és Linux-ügynököktől származó [eseményeket](log-analytics-data-sources-windows-events.md) és [teljesítményadatokat](log-analytics-data-sources-performance-counters.md), továbbá az olyan forrásokat, mint az [IIS-naplók](log-analytics-data-sources-iis-logs.md) és az [egyéni szövegnaplók](log-analytics-data-sources-custom-logs.md).  Konfigurálhatja az összegyűjteni kívánt egyes adatforrásokat, és a konfiguráció automatikusan érvénybe lép minden egyes csatlakoztatott forráson.

Egyéni követelmények érvényesülése esetén a [HTTP-adatgyűjtő API](log-analytics-data-collector-api.md) használatával írhat adatokat az adattárba egy REST API-ügyfélből.

## <a name="log-analytics-architecture"></a>Log Analytics-architektúra
A Log Analytics telepítési követelményei minimálisak, hiszen a központi összetevőket az Azure-felhő üzemelteti.  Ez a szolgáltatásokon túl magában foglalja a tárházat is, amely lehetővé teszi az összegyűjtött adatok összevetésének és elemzésének elvégzését.  A portál bármilyen böngészőből elérhető, így nincsenek ügyfélszoftverre vonatkozó követelmények.

[Windows](log-analytics-windows-agent.md)- és [Linux](log-analytics-linux-agents.md)-rendszerű számítógépeken ügynököket kell telepítenie, ám azok a számítógépek, amelyek már egy [csatlakoztatott System Center Operations Manager felügyeleti csoport](log-analytics-om-agents.md) tagjai, nem igényelnek további ügynököket.  Az Operations Manager-ügynökök továbbra is a felügyeleti kiszolgálókkal kommunikálnak, amelyek a Log Analyticsnek továbbítják az adatokat.  Néhány megoldás esetében azonban ügynökökre lesz szükség a Log Analytics-szel való közvetlen kommunikációhoz.  A kommunikáció követelményeit az egyes megoldásokra vonatkozó dokumentáció fogja megadni.

A [Log Analytics-regisztráció](log-analytics-get-started.md) során egy munkaterületet fog létrehozni.  A munkaterületre tekinthet úgy, mint egy saját adattárházzal, -forrásokkal és megoldásokkal rendelkező egyedülálló Log Analytics-környezetre. Több munkaterületet is létrehozhat az előfizetésében, melyekkel több környezetet, például munka- vagy tesztelési környezeteket támogathat.

![Log Analytics-architektúra](media/log-analytics-overview/architecture.png)

## <a name="next-steps"></a>További lépések
* [Regisztráljon ingyenes Log Analytics-fiókra](log-analytics-get-started.md), és saját környezetében tesztelheti működését.
* Tekintse meg a különböző elérhető [Adatforrásokat](log-analytics-data-sources.md), amelyek az adatokat a Log Analytics-be gyűjtik.
* [Böngésszen a Megoldástárban az elérhető megoldások között](log-analytics-add-solutions.md), ha funkciókkal szeretné bővíteni a Log Analytics-szolgáltatást.

