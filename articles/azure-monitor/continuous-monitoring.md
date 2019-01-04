---
title: Az Azure Monitor használatával végzett folyamatos figyelés |} A Microsoft Docs
description: Az Azure Monitor használatával engedélyezze a folyamatos figyelés a munkafolyamatok során adott lépéseit ismerteti.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: bwren
ms.openlocfilehash: 2d58a39efca8733902d157083489e59bf22ef161
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002280"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Folyamatos figyelés az Azure Monitor szolgáltatással

A folyamat és technológia révén a fejlesztési és üzemeltetési minden fázisában keresztül történő figyelés szükséges a folyamatos figyelés hivatkozik, és informatikai műveleteket életciklusának. Segít a folyamatosan biztosítani az állapotával, teljesítményével és az alkalmazás és az infrastruktúra megbízhatóságát, áthelyezi a fejlesztéstől az éles környezetbe. A folyamatos integráció és Készregyártás (CI/CD) a fogalmak, amelyek segítséget nyújtanak a folyamatos figyelési buildek fejlesztése és szoftverszolgáltatás gyorsabb és megbízhatóbb módon eljuttassa folytonos érték biztosít a felhasználók számára.

[Az Azure Monitor](overview.md) az egyesített figyelési megoldás az összes alkalmazás teljes körű, Élesítés observability biztosító Azure-ban és a felhőben és a helyszíni infrastruktúra. Problémamentesen működik együtt a [Visual Studio és a Visual Studio Code](https://visualstudio.microsoft.com/) során fejlesztési és tesztelési és integrálható [Azure DevOps](/azure/devops/user-guide/index) kiadáskezelés és munkatétel-kezelési üzembe helyezés során, és műveletek. Az ITSM és SIEM eszközök problémák nyomon követése és a meglévő IT-folyamatok belüli incidensek tetszőleges is integrálódnak.

A cikk lépései során a munkafolyamatok folyamatos figyelés engedélyezése az Azure Monitor használatával. Egyéb dokumentáció, amely ismerteti a különböző funkciók megvalósítása mutató hivatkozásokat tartalmazza.


## <a name="enable-monitoring-for-all-your-applications"></a>Engedélyezze a monitorozást az összes alkalmazás
Ahhoz, hogy kapjon observability környezete egészében, a webes alkalmazások és szolgáltatások figyelését engedélyeznie kell. Ez lehetővé teszi, hogy könnyen megjelenítheti a végpontok közötti tranzakció és kapcsolatok termékcsalád összes tagjára.

- [Az Azure DevOps Projects](../devops-project/overview.md) biztosítanak a meglévő kódot és a Git-tárház egy egyszerűsített élményt, vagy létrehoz egy folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamatot az Azure-bA mintaalkalmazásból közül választhat.
- [Folyamatos figyelés a DevOps-kiadási folyamatok](../application-insights/app-insights-vsts-continuous-monitoring.md) lehetővé teszi, hogy kapu vagy visszaállítani az üzembe helyezett szolgáltatáspéldányt a figyelési adatok.
- [Az állapotfigyelő](../azure-monitor/app/monitor-performance-live-website-now.md) lehetővé teszi, hogy a Windows az Azure Application Insights élő .NET-alkalmazás kialakítása módosítása vagy a kód ismételt telepítése nélkül.
- Ha az alkalmazás rendelkezik hozzáféréssel a kódot, majd engedélyezze a teljes körű figyelési [Application Insights](../application-insights/app-insights-overview.md) az Azure Monitor készült Application Insights SDK telepítésével [.NET](../application-insights/quick-monitor-portal.md), [Java ](../application-insights/app-insights-java-quick-start.md), [Node.js](../application-insights/app-insights-nodejs-quick-start.md), vagy [bármely más programnyelv](../azure-monitor/app/platforms.md). Ez lehetővé teszi, hogy adja meg az egyéni eseményeket, metrikákat vagy lapmegtekintés, amely az alkalmazás és a cége a.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>A teljes infrastruktúra figyelés engedélyezése
Alkalmazások csak olyan megbízható, mint hogy az alapul szolgáló infrastruktúra. Figyelés engedélyezve van a teljes infrastruktúra között kellene segítségével teljes observability eléréséhez, és könnyebben felderíteni egy lehetséges okát, ha hiba lép fel. Az Azure Monitor segítségével nyomon követheti az állapotát és teljesítményét a teljes hibrid infrastruktúráját, beleértve például a virtuális gépek, tárolók, tárolási és hálózati erőforrásokat.

- Automatikusan kap [platform metrikákat, a vizsgálati naplók és a diagnosztikai naplók](platform/data-sources.md) a legtöbb Azure-erőforrások konfiguráció nélkül.
- Engedélyezze a figyelést a virtuális gépek [-beli virtuális gépek az Azure Monitor](insights/vminsights-overview.md).
-  Engedélyezze a figyelést az AKS-fürt a [-tárolókhoz az Azure Monitor](insights/container-insights-overview.md).
- Adjon hozzá [figyelési megoldások](insights/solutions-inventory.md) különböző alkalmazások és szolgáltatások a környezetben.


[Infrastruktúra mint kód](/devops/learn/what-is-infrastructure-as-code) leíró modelleket, beleértve az azonos versioning használatával fejlesztési és üzemeltetési csapatok forráskód használatát az infrastruktúra felügyeleti. Ez a megbízhatóság és méretezhetőség ad hozzá a környezetben, és lehetővé teszi, hogy az alkalmazások kezelésére szolgáló hasonló folyamatokat.

-  Használat [Resource Manager-sablonok](platform/template-workspace-configuration.md) figyelés és riasztások konfigurálása keresztül erőforrások nagy készletét.
- Használat [Azure Policy](../governance/policy/overview.md) a különböző szabályok kényszerítenek ki az erőforrásokat. Ez biztosítja, hogy ezek az erőforrások megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek maradjon. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Az Azure-erőforráscsoportok erőforrások egyesítése
Egy tipikus alkalmazást az Azure-ban még ma például a virtuális gépek és az App Services vagy a Cloud Services, az AKS-fürt vagy a Service Fabric futó mikroszolgáltatásokat több erőforrást tartalmaz. Ezek az alkalmazások gyakran függőségei, mint az Event Hubs, Storage, SQL és a Service Bus használatára.

- A teljes körű betekintést nyerhet a különböző alkalmazások alkotó összes erőforrásra inAzure erőforrások erőforráscsoportok össze. [Az erőforráscsoportok az Azure Monitor](../azure-monitor/insights/resource-group-insights.md) állapotának és teljesítményének a teljes teljes körű, Élesítés alkalmazás, és lehetővé teszi, hogy a vizsgálatok megfelelő összetevőből kapott vagy hibakeresés nyomon követheti, hogy egyszerű módszert kínál.

## <a name="ensure-quality-through-continuous-deployment"></a>Biztosítandó folyamatos üzembe helyezés keresztül
Folyamatos integráció / folyamatos üzembe helyezés lehetővé teszi, hogy automatikusan integráció és üzembe helyezése az kódmódosításokat az alkalmazás az automatikus vizsgálat eredményei alapján. Ez leegyszerűsíti a telepítési folyamat, és biztosítja a minőségi változásokat, mielőtt azokat éles környezetben helyezze át.


- Használat [Azure folyamatok](/azure/devops/pipelines) folyamatos üzembe helyezés implementálását és automatizálását a CI/CD-tesztek alapján éles kód véglegesítésétől a teljes folyamatot.
- Használat [minőségi kapuk](/devops/pipelines/release/approvals/gates) integrálható a központi telepítés előtti vagy utáni figyelést. Ez biztosítja, hogy a kulcs egészségügyi/teljesítmény-mérőszámokat (KPI-k) a megfelel, az alkalmazások éles és az infrastruktúra-környezetben a különbségeket válthatnak fejlesztési vagy a méretezési csoport nem negatív negatív hatással van a KPI-ket.
- [Különálló figyelési példány karbantartása](../application-insights/app-insights-separate-resources.md) például fejlesztési, tesztelési, Kanári és éles a különböző üzemi környezetek között. Ez biztosítja, hogy összegyűjtött adatok megfelelő társított alkalmazások és infrastruktúra között. Ha adatainak korreláltatására környezetek van szüksége, használhatja [több erőforrás-diagramok a Metrikaböngésző](../azure-monitor/platform/metrics-charts.md) , vagy hozzon létre [erőforrások közötti lekérdezések a Log Analytics](log-query/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Műveletek a gyakorlatban hasznosítható riasztások létrehozása
Monitorozás kritikus fontosságú tényezője van pontos rendszergazdák aktuális és előrejelzett problémákról. 

- Hozzon létre [az Azure monitorban riasztásokat](../azure-monitor/platform/alerts-overview.md) naplók és metrikák azonosítása sikertelen előrelátható állapotok alapján. A célja az, hogy az összes riasztás végrehajtható, ami azt jelenti, hogy tényleges kritikus feltételeket képviselő, és csökkenti a vakriasztások igyekszik kell rendelkeznie. Használat [dinamikus küszöbértékek](platform/alerts-dynamic-thresholds.md) metrikaadatok helyett a saját statikus küszöbértékek alaptervek automatikusan kiszámításához. 
- Riasztások használata a leghatékonyabb azt jelenti, hogy a rendszergazdák értesítése a műveletek határozhatók meg. Rendelkezésre álló [értesítési műveletek](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) SMS, e-mailek, leküldéses értesítéseket vagy hanghívás vannak.
- Speciális műveletek használata [az ITSM-eszközhöz való csatlakoztatása](platform/itsmc-overview.md) vagy más riasztáskezelés-rendszerekhez [webhookok](platform/activity-log-alerts-webhook.md).
- A riasztásokat, valamint az azonosított helyzetekben szervizelése [Azure Automation-runbookok](../automation/automation-webhooks.md) vagy [Logic Apps](/connectors/custom-connectors/create-webhook-trigger) , amely a riasztást, webhookok segítségével lehet elindítani. 
- Használat [az automatikus skálázás](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md) dinamikusan növelheti és csökkentheti a számítási erőforrások gyűjtött metrikák alapján.

## <a name="prepare-dashboards-and-workbooks"></a>Irányítópultok és munkafüzetek előkészítése
Arról, hogy a fejlesztési és operatív rendelkezik-e a hozzáférést ugyanazon telemetriát és eszközök lehetővé teszi a környezete egészében minták megtekintése és az időt a észlelése (MTTD) és átlagos idő a visszaállítás (MTTR) minimalizálása érdekében.

- Készítse elő [egyéni irányítópultokat](../application-insights/app-insights-tutorial-dashboards.md) gyakori metrikák és naplók a szervezet a eltérő szerepkörök alapján. Az irányítópultok az összes Azure-erőforrások származó adatok is kombinálhatók.
- Készítse elő [munkafüzetek](../application-insights/app-insights-usage-workbooks.md) , hogy fejlesztési és a műveletek közötti adatmegosztás. Ezek a sikerült, dinamikus jelentéseket a mérőszám-diagramok és naplólekérdezések, vagy akár a hibaelhárítási útmutatók alapszintű problémák kezeléséhez útmutatás nyújtása a támogatási, illetve műveletek fejlesztők által készített elő kell készíteni.

## <a name="continuously-optimize"></a>Folyamatos optimalizálást végezhet
 Figyelés az egyik Összefoglalásul népszerű Build mérték további filozófia, amely javasolja folyamatosan nyomon követheti a KPI-k és a felhasználói viselkedés mérőszámok, és ezután mindent megtesz tervezési ismétlések segítségével optimalizálja őket. Az Azure Monitor segítséget nyújt a metrikák és a megfelelő, az üzleti és szükség szerint a következő központi telepítésben lévő új adatpontok hozzáadása naplóinak összegyűjtésére.

- Az Application Insights eszközzel [nyomon követheti a végfelhasználói viselkedés és engagement](../application-insights/app-insights-tutorial-users.md).
- Használat [hatáselemzés](../application-insights/app-insights-usage-impact.md) segítséget rangsorolhatja mely területek kiemelten fontos KPI-k meghajtót be.


## <a name="next-steps"></a>További lépések

- További információ a különbség a összetevői [Azure Monitor](overview.md).
- [Folyamatos megfigyelés hozzáadása](../application-insights/app-insights-vsts-continuous-monitoring.md) a kiadási folyamathoz.