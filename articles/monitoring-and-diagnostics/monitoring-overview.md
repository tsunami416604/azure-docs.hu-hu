---
title: "Figyelés a Microsoft Azure |} Microsoft Docs"
description: "Ha semmit, a Microsoft Azure-ban figyelni kívánt választási lehetőségek. A figyelő az Azure, az Application Insights és Naplóelemzési"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: robb
ms.openlocfilehash: e164cbd910ccc38610c7aef37d25ff1b4413038d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-monitoring-in-microsoft-azure"></a>A figyelés a Microsoft Azure-ban – áttekintés
Ez a cikk áttekintést nyújt az eszközök és a szolgáltatások holistically figyelése a Microsoft Azure részt. Ez a kategória:
- Azure-infrastruktúra és az alkalmazások figyeléséhez használt Azure-szolgáltatások
- A figyelő hibrid és az-Azure infrastruktúra és az alkalmazások Azure-szolgáltatások használata
- Azure-infrastruktúra és az alkalmazások figyeléséhez használt-Azure szolgáltatások

Azt ismerteti, amelyek a különböző termékek és szolgáltatások érhető el, és hogyan működnek együtt. Ez segít annak meghatározására, hogy mely eszközök legmegfelelőbb meg, milyen esetekben.  

## <a name="why-use-azures-monitoring-services"></a>Miért érdemes az Azure figyelési szolgáltatásokat használni?

A cloud app teljesítményével kapcsolatos problémákat hatással lehet az üzleti. A több összekapcsolt összetevőkkel és gyakori kiadásokban degradations bármikor fordulhat elő. És ha az alkalmazást, a felhasználók általában felderítése a tesztjei során nem talált problémákat. Meg kell ezekről a problémákról értesülnek azonnal, eszközöket, és diagnosztizálására és a problémák elhárításához. Továbbá az alkalmazás gyakran felmerülő problémák eredménye az alkalmazás mögötti infrastruktúra, amely ezeket az alkalmazásokat futtatja, így az alkalmazás és az infrastruktúra átfogó képet, akkor az az Azure-alapú környezetben figyelés kulcs. A Microsoft Azure tartománnyal rendelkező eszközök azonosítására és az ilyen problémák megoldásához.

## <a name="how-do-i-monitor-my-azure-environment"></a>Hogyan figyelhetek az Azure környezetben?

Nincsenek számos eszközt az Azure környezetben futó Azure-szolgáltatások és infrastruktúra, hogy a kód futtatásával a figyelésre. Ezek az eszközök együttműködése kínálnak a felhő átfogó figyelést, és adja meg:

-   **Az Azure figyelő** – az Azure-szolgáltatás, amely egy olyan összevont Azure-szolgáltatások összes figyelési adatot folyamatot működik. Metrikák és az Azure-infrastruktúra és használ az Azure szolgáltatások működését leíró események biztosít hozzáférést. Azure figyelő a figyelési adatok folyamatot az Azure környezetben, és kínál az adatok közvetlenül a Naplóelemzési, valamint a 3. fél eszközök, ahol adatok betekintést és összevonásához a helyszíni vagy más felhőalapú erőforrások adatait.

-   **Az Application Insights** – az Azure-szolgáltatás, amely az alkalmazás teljesítményének figyelése és a felhasználó analytics kínál. Azt figyeli, hogy a korábban írt kódot és az alkalmazások telepítése után az Azure-on vagy a helyszíni vagy más felhők. Az alkalmazás az Application Insights SDK-val tagolása juthat adatokat, többek között a válaszidők függőségek, a kivétel nyomkövetési adatokat, a hibakeresési pillanatképek és a végrehajtási profilok számos elérésére. Hatékony eszközöket biztosít a telemetriai elemzése közben fejleszt, és az alkalmazás működik. Ez mélyen integrálja a Visual Studio segítségével lehetővé teszik a probléma sor(ok) kód jobbra, hogy kijavíthassuk, és termék vezetőknek, valamint az alkalmazások vevői használati elemzése használatelemzési információkat nyújt.

-   **Elemzés jelentkezzen** -korábbi nevén az OMS szolgáltatáshoz, akkor az Azure-szolgáltatások, amelyek ingests naplóhoz és az Azure-szolgáltatások (az Azure figyelő) keresztül metrika az Azure virtuális gépeken, és a helyszíni vagy más felhőalapú infrastruktúra és az offers rugalmas napló keresési és kibővített-az-a Ezek az adatok felett analytics mezőbe. Gazdag eszközök adatelemzéshez forrás lehetővé teszi összetett lekérdezések összes naplófájlt, és a megadott feltételek proaktív riasztást küldhet biztosít.  Egyéni adatok azokat a központi tárházához lekérdezhetik és megjelenítheti azt, még akkor is gyűjtheti. Azonnali betekintést nyerhet a biztonsági napló elemzési beépített megoldások előnyeit és az infrastruktúra működését is igénybe vehet.

## <a name="accessing-monitoring-in-the-azure-portal"></a>Az Azure portálon figyelési elérése
Minden Azure figyelési szolgáltatásokat is elérhető a felhasználói felület egytáblás. Ez a terület elérésével további információkért lásd: [Ismerkedés az Azure-figyelő](monitoring-get-started.md). 

Az erőforrások figyelési funkciók kiemelése ezeket az erőforrásokat, és a figyelési lehetőségek adatlehatolás úgy is elérheti. 

## <a name="examples-of-when-to-use-which-tool"></a>Példák a mely eszközzel 

A következő szakaszok bemutatják, néhány alapvető forgatókönyv, mely eszközök együtt használható. 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>A forgatókönyv 1 – javítás hibák fejlesztés alatt az Azure alkalmazásban   

**A legjobb lehetőség, hogy az Application Insights, az Azure-figyelő és a Visual Studio együtt használja**

Azure most kínál a felhőben a Visual Studio hibakereső teljes hatványa. Konfigurálja az Azure figyelő telemetriai adatokat küldhet az Application Insights részére. Visual Studio Application Insights SDK tartalmazza az alkalmazás engedélyezése. Az Application Insights, egyszer használhat az alkalmazás-hozzárendelés vizuálisan felderítésére, mely a futó alkalmazás részei sérült állapotban, vagy nem. Ezen részein, amelyek nem működik megfelelően hibákat és kivételeket már rendelkezésre állnak feltárási. Az Application Insights a különböző analytics segítségével mélyebben lépjen. Ha nem biztos a hibával kapcsolatos, a probléma további kód és a PIN-kód pontba nyomon követni a Visual Studio hibakereső segítségével. 

További információkért lásd: [webalkalmazások figyelése](../application-insights/app-insights-azure-web-apps.md) , és nézze meg a különböző típusú alkalmazások és nyelvek utasításokat a bal oldali tartalomjegyzék.  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>2 –. forgatókönyv hibakeresése az Azure .NET webalkalmazás, amelyek csak a termelési megjelenítése 

> [!NOTE]
> Ezek a funkciók még csak előzetes verziójúak. 

**A legjobb lehetőség, hogy használja a Application insights szolgáltatással, és ha lehetséges Visual Studio, a teljes hibakeresés tapasztalhat.**

Az Application Insights pillanatkép hibakereső segítségével az alkalmazás hibakeresését. Egy bizonyos Hibaküszöb éles összetevőkkel esetén a rendszer automatikusan rögzíti az idő "pillanatképek." nevű windows telemetriai A rögzített mérete egy éles felhő biztonságos, mivel már elég kicsi nincs hatása a teljesítményre, de elég jelentős nyomkövetésének engedélyezése.  A rendszer több pillanatkép is rögzítheti. Egy időben az Azure portálon keresse meg, vagy a Visual Studio eszközzel a teljes felület. A Visual Studio a fejlesztők is végezze el, hogy a pillanatkép, mintha csak azok a valós idejű volt hibakeresését. Helyi változók, a paraméterek, a memória és a keretek is elérheti. A fejlesztők a termelési adatok RBAC szerepet keresztül hozzáférést kell rendelni.  

További információkért lásd: [pillanatkép hibakeresés](../application-insights/app-insights-snapshot-debugger.md). 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>3 –. forgatókönyv-tárolók és mikroszolgáltatások használó Azure alkalmazás hibakeresése 

**Ugyanaz, mint 1. forgatókönyv. Az Application Insights, az Azure-figyelő és a Visual Studio együtt használja** Application Insights is támogatja a összegyűjtése telemetriai tárolókba futó folyamatok és mikroszolgáltatások (Kubernetes, Docker, Azure Service Fabric). További információ [tekintse meg ezt a videót a tárolók és mikroszolgáltatások hibakeresés](https://go.microsoft.com/fwlink/?linkid=848184). 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>Az Azure alkalmazásban kapcsolatos teljesítményproblémák javítás 4 –. forgatókönyv

A [Application Insights Profilkészítő](../application-insights/app-insights-profiler.md) célja segíteni az ilyen jellegű problémák elhárításához. Azonosítsa, és alkalmazásszolgáltatások (a Logic Apps a webalkalmazások Mobile Apps, az API Apps) és egyéb számítási erőforrásokat, például a virtuális gépek, virtuális gép méretezési csoportok (VMSS), a Cloud Services és a Service Fabric futó alkalmazások teljesítményével kapcsolatos hibaelhárítást. 

> [!NOTE]
> Virtuális gépek, virtuális gép méretezési csoportok (VMSS) felhőalapú szolgáltatások és szolgáltatások háló profil lehetősége jelenleg előzetes verzióban érhető.   

Ezenkívül proaktív értesíti, kapcsolatos hibákat, például a lassú lapbetöltési idők, bizonyos típusú e-mailben az intelligens Kártevőészlelő eszköz.  Nem kell tennie, hogy az eszköz a beállításra. További információkért lásd: [intelligens észlelési - Teljesítményanomáliákat](../application-insights/app-insights-proactive-performance-diagnostics.md).



## <a name="next-steps"></a>Következő lépések
További információ

* [Az ignite-on 2016 videó az Azure figyelője](https://myignite.microsoft.com/videos/4977)
* [Ismerkedés az Azure-figyelő](monitoring-get-started.md)
* [Az Azure Diagnostics](../azure-diagnostics.md) Ha a felhőalapú szolgáltatás, a virtuális gépet, a problémák diagnosztizálásához kívánt virtuális gép skálázása állítsa be, vagy a Service Fabric-alkalmazás.
* [Az Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) Ha kívánt diagnosztikai problémák az App Service Web app alkalmazásban.
* [Naplófájl Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) és a [Operations Management Suite](https://www.microsoft.com/oms/) üzemi felügyeleti megoldás
