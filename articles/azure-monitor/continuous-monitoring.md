---
title: Folyamatos figyelés az Azure Monitorsegítségével | Microsoft dokumentumok
description: Az Azure Monitor használatával a folyamatos figyelés engedélyezésének konkrét lépéseit ismerteti a munkafolyamatokban.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: b9ca8a703ed8a84148abd23e90114402d8806bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667193"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Folyamatos figyelés az Azure Monitorral

A folyamatos figyelés a DevOps és az IT-műveletek életciklusának minden fázisába a figyelés beépítéséhez szükséges folyamatra és technológiára vonatkozik. Segít folyamatosan biztosítani az alkalmazás és az infrastruktúra állapotát, teljesítményét és megbízhatóságát, ahogy a fejlesztésről a termelésre kerül. A folyamatos felügyelet a folyamatos integráció és a folyamatos üzembe helyezés (CI/CD) koncepcióira épül, amelyek segítségével gyorsabban és megbízhatóbban fejleszthet és szállíthat szoftvereket, hogy folyamatos értéket biztosítson a felhasználók számára.

[Az Azure Monitor](overview.md) az Azure egységes ített figyelési megoldása, amely teljes körű megfigyelhetőséget biztosít a felhőben és a helyszínen található alkalmazások és infrastruktúra között. Zökkenőmentesen működik a Visual Studio és a Visual Studio code fejlesztése és tesztelése [során,](https://visualstudio.microsoft.com/) és integrálható [az Azure DevOps-szal](/azure/devops/user-guide/index) a kiadáskezeléshez és a munkaelemek kezeléséhez az üzembe helyezés és a műveletek során. Még az Ön által választott ITSM és SIEM eszközökbe is integrálható, hogy segítsen nyomon követni a problémákat és incidenseket a meglévő informatikai folyamatokon belül.

Ez a cikk az Azure Monitor használatával a folyamatos figyelés engedélyezésére szolgáló konkrét lépéseket ismerteti a munkafolyamatokban. Ez magában foglalja a hivatkozásokat más dokumentációt, amely részletesen ismerteti a végrehajtási különböző funkciókat.


## <a name="enable-monitoring-for-all-your-applications"></a>A figyelés engedélyezése az összes alkalmazásban
Annak érdekében, hogy a teljes környezetben megfigyelhetőséget nyerjen, engedélyeznie kell az összes webes alkalmazás és szolgáltatás figyelését. Ez lehetővé teszi, hogy könnyen láthatóvá teljes körű tranzakciók és kapcsolatok az összes összetevő.

- [Az Azure DevOps-projektek](../devops-project/overview.md) egyszerűsített élményt nyújtanak a meglévő kóddal és gittárral, vagy választhatnak a mintaalkalmazások közül a folyamatos integrációs (CI) és a folyamatos kézbesítési (CD) folyamat létrehozásához az Azure-ba.
- [Folyamatos figyelés a DevOps kiadási folyamat](../azure-monitor/app/continuous-monitoring.md) lehetővé teszi, hogy a figyelési adatok alapján a központi telepítés gate vagy visszaállítása.
- [Az Állapotfigyelő](../azure-monitor/app/monitor-performance-live-website-now.md) lehetővé teszi, hogy az Azure Application Insights segítségével egy élő .NET alkalmazást eszközítsen windowsos rendszeren anélkül, hogy módosítania vagy újra telepítenie kellene a kódot.
- Ha rendelkezik hozzáféréssel az alkalmazás kódjához, engedélyezze a teljes figyelést az [Application Insights](../azure-monitor/app/app-insights-overview.md) segítségével az Azure Monitor Application Insights SDK [.NET,](../azure-monitor/learn/quick-monitor-portal.md) [Java,](../azure-monitor/app/java-get-started.md) [Node.js](../azure-monitor/learn/nodejs-quick-start.md)vagy [bármely más programozási nyelv](../azure-monitor/app/platforms.md)telepítésével. Ez lehetővé teszi, hogy egyéni eseményeket, mutatókat vagy oldalnézeteket adjon meg, amelyek relevánsak az alkalmazás és a vállalkozás szempontjából.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>A figyelés engedélyezése a teljes infrastruktúrára
Az alkalmazások csak olyan megbízhatóak, mint az alapul szolgáló infrastruktúrák. Miután a figyelés engedélyezve van a teljes infrastruktúrában segít elérni a teljes megfigyelhetőséget, és megkönnyíti a potenciális kiváltó ok felfedezését, ha valami nem sikerül. Az Azure Monitor segítségével nyomon követheti a teljes hibrid infrastruktúra állapotát és teljesítményét, beleértve az erőforrásokat, például a virtuális gépeket, a tárolókat, a tárolókat és a hálózatot.

- Automatikusan megkapja [a platformmetrikákat, a tevékenységnaplókat és a diagnosztikai naplókat](platform/data-sources.md) az Azure-erőforrások többségéből konfiguráció nélkül.
- A virtuális gépek mélyebb figyelésének engedélyezése [az Azure Monitor szolgáltatással.](insights/vminsights-overview.md)
-  Az [Azure Monitor tárolókhoz](insights/container-insights-overview.md)való használatával engedélyezheti az AKS-fürtök mélyebb figyelését.
- [Figyelési megoldások hozzáadása](insights/solutions-inventory.md) a környezetkülönböző alkalmazásaihoz és szolgáltatásaihoz.


[Infrastruktúra kódként](/azure/devops/learn/what-is-infrastructure-as-code) az infrastruktúra kezelése egy leíró modell, ugyanazt a verziószámozást használva, mint devops csapatok a forráskódhoz használt. Megbízhatóságot és méretezhetőséget biztosít a környezethez, és lehetővé teszi az alkalmazások kezeléséhez használt hasonló folyamatok kihasználását.

-  [Az Erőforrás-kezelő sablonjaisegítségével](platform/template-workspace-configuration.md) engedélyezheti a figyelést, és riasztásokat konfigurálhat az erőforrások nagy készletén.
- Az [Azure-szabályzat használatával](../governance/policy/overview.md) különböző szabályokat alkalmazhat az erőforrásokra. Ez biztosítja, hogy ezek az erőforrások megfeleljenek a vállalati szabványoknak és a szolgáltatásiszint-szerződéseknek. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Erőforrások kombinálása az Azure erőforráscsoportokban
Egy tipikus alkalmazás az Azure-ban ma több erőforrást is tartalmaz, például virtuális gépeket és appszolgáltatásokat, vagy a Felhőszolgáltatásokon, az AKS-fürtökön vagy a Service Fabric-en üzemeltetett mikroszolgáltatásokat. Ezek az alkalmazások gyakran használják a függőségeket, például az Event Hubs, a Storage, az SQL és a Service Bus.

- Az Azure Resource Groups erőforrásainak kombinálásával teljes körű láthatóságot kaphat a különböző alkalmazásokat összeget felhasználó összes erőforrásában. [Az Azure Monitor erőforráscsoportokhoz](../azure-monitor/insights/resource-group-insights.md) egyszerű módja a teljes veremben futó teljes körű alkalmazás állapotának és teljesítményének nyomon követésére, és lehetővé teszi a megfelelő összetevők lebontását a vizsgálatokhoz vagy a hibakereséshez.

## <a name="ensure-quality-through-continuous-deployment"></a>A minőség biztosítása folyamatos telepítéssel
Folyamatos integráció / folyamatos üzembe helyezés lehetővé teszi, hogy automatikusan integrálja és telepítse a kódváltozásokat az alkalmazás eredményei alapján az automatizált tesztelés. Egyszerűsíti a telepítési folyamatot, és biztosítja a módosítások minőségét, mielőtt azok éles környezetbe lépnének.


- Az [Azure Pipelines](/azure/devops/pipelines) használatával folyamatos üzembe helyezést valósíthat meg, és automatizálhatja a teljes folyamatot a kódvéglegesítéstől az éles környezetig a CI/CD-tesztek alapján.
- [A Minőségi kapukkal](/azure/devops/pipelines/release/approvals/gates) integrálhatja a figyelést az előtelepítés vagy a telepítés utáni üzembe helyezés beépülésébe. Ez biztosítja, hogy megfelel a legfontosabb állapot-és teljesítménymutatóknak (KPI-k), ahogy az alkalmazások a fejlesztési területről az éles környezetre térnek át, és az infrastruktúra-környezet vagy a méretezés közötti különbségek nem befolyásolják hátrányosan a Fő teljesítménymutatókat.
- [Külön figyelési példányok karbantartása](../azure-monitor/app/separate-resources.md) a különböző központi telepítési környezetek között, például a fejlesztési, tesztelési, kanári- és prod. Ez biztosítja, hogy az összegyűjtött adatok relevánsak legyenek a kapcsolódó alkalmazások és infrastruktúra számára. Ha az adatokat a környezetek között kell korrelálnia, [többerőforrásos diagramokat használhat a Metrics Explorerben,](../azure-monitor/platform/metrics-charts.md) vagy [létrehozhat több erőforrást tartalmazó lekérdezéseket az Azure Monitorban.](log-query/cross-workspace-query.md)


## <a name="create-actionable-alerts-with-actions"></a>Végrehajtható riasztások létrehozása műveletekkel
A figyelés kritikus szempontja, hogy proaktív módon értesítse a rendszergazdákat az aktuális és előre jelzett problémákról. 

- [Riasztások létrehozása az Azure Monitorban](../azure-monitor/platform/alerts-overview.md) naplók és metrikák alapján a kiszámítható hibaállapotok azonosításához. A cél az, hogy minden riasztás perelhető, ami azt jelenti, hogy azok a tényleges kritikus feltételeket, és igyekeznek csökkenteni a hamis pozitív. [A dinamikus küszöbértékek](platform/alerts-dynamic-thresholds.md) használatával automatikusan kiszámíthatja az alapterveket a metrikaadatokon ahelyett, hogy saját statikus küszöbértékeket határozna meg. 
- Határozza meg a riasztások hoz a leghatékonyabb módon a rendszergazdák értesítésére szolgáló műveleteket. [Az értesítésekhez](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) rendelkezésre álló műveletek az SMS-ek, az e-mailek, a leküldéses értesítések vagy a hanghívások.
- A speciálisabb műveletekkel [webhookokon](platform/activity-log-alerts-webhook.md)keresztül [csatlakozhat az ITSM eszközhöz](platform/itsmc-overview.md) vagy más riasztáskezelő rendszerekhez.
- A riasztásokban azonosított helyzetek, valamint az [Azure Automation runbookok](../automation/automation-webhooks.md) vagy [logikai alkalmazások,](/connectors/custom-connectors/create-webhook-trigger) amelyek webhookok használatával indítható riasztást. 
- [Az automatikus skálázás](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md) segítségével dinamikusan növelheti és csökkentheti a számítási erőforrásokat az összegyűjtött metrikák alapján.

## <a name="prepare-dashboards-and-workbooks"></a>Irányítópultok és munkafüzetek előkészítése
Annak biztosítása, hogy a fejlesztés és a műveletek ugyanazokhoz a telemetriai adatokhoz és eszközökhöz férhessenek hozzá, és lehetővé teszi számukra, hogy a mintákat a teljes környezetben megtekintsék, és minimalizálják az észlelési idő (MTTD) és a visszaállítási idő (MTTR) átlagidejét.

- Egyéni [irányítópultok](../azure-monitor/learn/tutorial-app-dashboards.md) előkészítése közös metrikák és naplók alapján a szervezet különböző szerepköreihez. Az irányítópultok az összes Azure-erőforrás ból származó adatokat egyesíthetik.
- [Munkafüzetek készítése a](../azure-monitor/app/usage-workbooks.md) fejlesztés és a műveletek közötti tudásmegosztás biztosítására. Ezek készíthetők dinamikus jelentések metrikus diagramokkal és naplólekérdezésekkel, vagy akár hibaelhárítási útmutatókként, amelyeket a fejlesztők készítenek, és segítik az ügyfélszolgálatot vagy a műveleteket az alapvető problémák kezelésében.

## <a name="continuously-optimize"></a>Folyamatosan optimalizálja
 A figyelés a népszerű Build-Measure-Learn filozófia egyik alapvető aspektusa, amely azt javasolja, hogy folyamatosan kövesse nyomon a KPI-ket és a felhasználói viselkedési mutatókat, majd törekedjen azok optimalizálására az ismétlések tervezésével. Az Azure Monitor segítségével összegyűjtheti a vállalkozásának megfelelő metrikákat és naplókat, és szükség szerint új adatpontokat adhat hozzá a következő üzembe helyezéshez.

- Az Application Insights eszközeivel nyomon követheti a [végfelhasználó viselkedését és elköteleződését.](../azure-monitor/learn/tutorial-users.md)
- [Az Impact Analysis segítségével](../azure-monitor/app/usage-impact.md) rangsorolhatja, hogy mely területekre összpontosítson a fontos fő értékpi-k hez való vezetéshez.


## <a name="next-steps"></a>További lépések

- Ismerje meg az [Azure Monitor](overview.md)eltérő összetevőit.
- [Folyamatos figyelés hozzáadása](../azure-monitor/app/continuous-monitoring.md) a kiadási folyamathoz.
