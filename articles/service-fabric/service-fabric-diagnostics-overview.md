---
title: "Azure Service Fabric figyelése és diagnosztika áttekintése |} Microsoft Docs"
description: "Tudnivalók a figyelés és az Azure Service Fabric fürt, alkalmazások és szolgáltatások diagnosztika."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2017
ms.author: dekapur
ms.openlocfilehash: 88f4a23f89a1c8fd88db1df3a7ff03ae5df64c0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Megfigyelési és diagnosztikai az Azure Service Fabric

Megfigyelési és diagnosztikai létfontosságúak fejleszt, tesztelése és telepítése az alkalmazások és szolgáltatások minden környezetben. Service Fabric-megoldás a legmegfelelőbb tervezése és megvalósítása a figyelési és diagnosztika, amelyek segítenek győződjön meg róla, alkalmazásokat, és szolgáltatások vannak a várt módon működik a helyi környezet vagy a termelési.

A fő figyelése és diagnosztikai céljai számára:
* Észlelheti és diagnosztizálhatja a hardver- és infrastruktúra-problémák
* Szoftver- és alkalmazás problémák észlelése, csökkentheti az állásidőt szolgáltatás
* Erőforrás használati és súgó meghajtó műveletek döntések fontossága
* Alkalmazás, szolgáltatás és az infrastruktúra teljesítményének optimalizálása
* Üzleti elemzések készítése és területeinek


Az általános munkafolyamat figyelési és diagnosztika három lépésből áll:

1. **Az eseménygenerálás**: Ez magában foglalja az események (naplókat, nyomkövetéseket, egyéni események), az infrastruktúra (fürt), a platform és az alkalmazás / szolgáltatás szintjén
2. **Esemény összesítési**: létrehozott események összegyűjtésének és azok megjelenítése előtt összesítve kell
3. **Elemzés**: események kell lenniük a feladatkonfigurációkat és néhány formátumban engedélyezése elemzéshez ki és jelenítheti meg igény szerint elérhető

Több termék érhető el, amelyek vonatkoznak ezek a területek három, és válassza ki a különböző technológiák az egyes szabadon. Fontos, győződjön meg arról, hogy a különböző darabok működnek együtt a fürt egy végpont figyelési megoldást biztosítanak.

## <a name="event-generation"></a>Az eseménygenerálás

Az első lépés a figyelési, diagnosztikai munkafolyamat létrehozását és az események és a naplók generációs. Ezen események, a naplókat, és a nyomkövetési adatokat két szintű jönnek létre: a platform réteg (beleértve a fürt, a gépek vagy a Service Fabric műveletek) vagy az alkalmazási rétegre (bármely instrumentation hozzáadódik az alkalmazások és szolgáltatások központi telepítése a fürt). Minden szinten eseményeket is testre szabható, bár a Service Fabric adjon meg néhány instrumentation alapértelmezés szerint.

Tudjon meg többet az [platform szintű események](service-fabric-diagnostics-event-generation-infra.md) és [szintű alkalmazásesemények](service-fabric-diagnostics-event-generation-app.md) megtudhatja, hogy milyen áll rendelkezésre, és további instrumentation hozzáadása.

Miután meghozná a döntést a naplózás szolgáltatót szeretne használni, győződjön meg arról, hogy a naplókat a rendszer éppen kell összesíteni, és megfelelően tárolja.

## <a name="event-aggregation"></a>Esemény aggregáció

A naplók és a rendszer az alkalmazások és a fürt által létrehozott események gyűjtéséhez általában javasoljuk [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) (ügynök-alapú naplógyűjtést több hasonló) vagy [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) () a folyamaton belüli naplógyűjtést).

Azure Diagnostics kiterjesztéssel alkalmazás naplók gyűjtésére esetén a Service Fabric szolgáltatások jó választás napló források és célok nem változik gyakran és egy egyszerű megfeleltetés a források és a célhelyek között. Az OK az Azure Diagnostics konfigurálását ez történik, az erőforrás-kezelő rétegben, a konfiguráció jelentős módosítása szükséges, frissíteni vagy újratelepíteni a fürt. Emellett az legjobb első meggyőződött arról, hogy a naplók a valahol további állandó, ha azok elérhetők, elemzés különböző platformokon való tárolását. Ez azt jelenti, hogy akkor fejeződik be a csővezeték-nél kevésbé hatékonyak egy lehetőséghez hasonlóan EventFlow is folyamatban.

Használatával [EventFlow](https://github.com/Azure/diagnostics-eventflow) lehetővé teszi a elküldeni a naplókat, közvetlenül az elemzést, a képi megjelenítés platform, illetve tárolási szolgáltatásokat. Más függvénytárak (ILogger, Serilog stb.) használhatók, például az ugyanerre a célra, de EventFlow rendelkezik az előnye, hogy tervezték, kifejezetten a folyamaton belüli naplógyűjtést és a Service Fabric-szolgáltatásokat támogatja. Ez általában számos lehetséges előnye van:

* Egyszerű konfiguráció és a központi telepítés
    * Diagnosztikai adatok gyűjtésének konfigurálása most nem része a szolgáltatás konfigurációját. Könnyen mindig legyen "szinkronizálva" a többi alkalmazáshoz
    * Alkalmazás vagy a szolgáltatás konfigurációja, könnyen elérhető
    * Adatok célok EventFlow keresztül konfigurálása csak a megfelelő NuGet-csomag hozzáadása és módosítása a *eventFlowConfig.json* fájl
* Rugalmasság
    * Az alkalmazás is küldheti az adatokat, ahol nyissa meg kell, mindaddig, amíg egy ügyfél könyvtár, amely támogatja a célként megadott tárolási rendszereket. Új célokat felveheti igény szerint
    * Összetett rögzítési, szűrési és adatösszesítés szabályok is kell végrehajtani.
* Belső alkalmazás adatokhoz és a környezetben
    * A diagnosztikai alrendszer, az alkalmazás/kiszolgáló folyamat-keretrendszeren belül fut. könnyen is kiegészítheti a nyomkövetési adatokat környezetfüggő adatokkal

Egy dolog, ami arról értesít, hogy e két beállítás nincsenek kölcsönösen kizárja egymást, majd is előfordulhat, hogy egy hasonló feladat befejezése az egyiket használja, akkor lehetett is értelme ahhoz, hogy mindkét beállítása. A legtöbb esetben ügynök kombinálásával folyamaton belüli gyűjteménnyel előfordulhat, hogy megbízhatóbb figyelési munkafolyamat. Az Azure Diagnostics-bővítmény (ügynök) lehet platform szintű naplók a kiválasztott elérési EventFlow (a folyamat gyűjtemény) használata közben a szintű alkalmazásnaplók. Miután ki, hogy mi a legmegfelelőbb az Ön rendelkezik szerepelnek, idő jelenik meg és elemezheti az adatokat módját is.

## <a name="event-analysis"></a>Esemény elemzése

Nincsenek számos nagy platformra, amelyek a piacon szerepel, amikor az elemzést, a képi megjelenítés figyelése és diagnosztikai adatok. Javasolt a két rendszer [OMS](service-fabric-diagnostics-event-analysis-oms.md) és [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) miatt a jobb integráció a Service Fabric, de meg is megismerhetők a [rugalmas verem](https://www.elastic.co/products) () különösen akkor, ha tervezi, hogy a fürt egy kapcsolat nélküli környezetben futó), [Splunk](https://www.splunk.com/), vagy bármilyen más platformon igény szerint.

A legfontosabb bármely platformhoz úgy dönt, hogyan kényelmes áll a felhasználói felület és a beállítások lekérdezése adato és könnyen olvasható irányítópultokat és javítása érdekében a figyelést biztosítanak további eszközöket kell tartalmaznia például az automatikus lehetőséget.

Úgy dönt, a platform mellett a Service Fabric-fürt Azure erőforrásként beállításakor azt is regisztrálásával hozzáférhet a Azure out-of-az-box monitoring gépekhez, amely lehet hasznos, ha adott, és metrika figyelése.

### <a name="azure-monitor"></a>Azure Monitor

Használhat [Azure figyelő](../monitoring-and-diagnostics/monitoring-overview.md) számos, amelyen a Service Fabric-fürt épül Azure-erőforrások figyelése. A metrikák készlete a [virtuálisgép-méretezési csoport](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) és egyéni [virtuális gépek](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) automatikusan gyűjti, és az Azure-portálon jelenik meg. Az Azure-portálon az összegyűjtött információk megtekintéséhez válassza ki a Service Fabric-fürt tartalmazó erőforráscsoportot. Ezután válassza ki a megtekinteni kívánt virtuálisgép-méretezési csoport. Az a **figyelés** szakaszban jelölje be **metrikák** megtekintheti az értékek.

![A metrika az összegyűjtött információk az Azure portál nézet](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

A diagram testreszabásához kövesse a [a Microsoft Azure-ban mérőszámok](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Hozhat létre a fenti metrikák alapján riasztások leírtak [hozzon létre riasztásokat Azure figyelése az Azure-szolgáltatások](../monitoring-and-diagnostics/insights-alerts-portal.md). Küldhet riasztások értesítési szolgáltatás webes hurkok használatával, a [egy webes hook konfigurálása Azure metrika riasztást](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Az Azure használatát támogatja csak egyetlen előfizetéssel. Ha több előfizetéssel figyelnie kell, vagy ha további szolgáltatásokat szeretne [Naplóelemzési](https://azure.microsoft.com/documentation/services/log-analytics/), részben a Microsoft Operations Management Suite nyújt egy körű informatikai felügyeleti megoldás a helyszíni és felhőalapú infrastruktúra. Adatokat az Azure-figyelő közvetlenül a Naplóelemzési, így látható metrikák és a naplókat a teljes környezet egyetlen helyen irányíthatja.

## <a name="next-steps"></a>Következő lépések

### <a name="watchdogs"></a>Watchdogs

A figyelő egy olyan különálló szolgáltatás, tekintse meg a rendszerállapot és a semmit, a health modell hierarchiában különböző szolgáltatások és a jelentés állapotának betöltése. Ez segítséget nyújt a nézet egy egyetlen szolgáltatás alapján, amely nem észlelhető hiba. Watchdogs egyaránt remek állomás kódot, amely végrehajtja a javító műveleteket (például bizonyos időközönként törölje a naplófájlokat tároló) felhasználói beavatkozás nélkül. A minta-figyelő szolgáltatás megvalósításának található [Itt](https://github.com/Azure-Samples/service-fabric-watchdog-service).

Ismerkedés a megértése, hogyan események és a naplók beolvasása létre a [platformot szintű](service-fabric-diagnostics-event-generation-infra.md) és a [alkalmazásszintű](service-fabric-diagnostics-event-generation-app.md).