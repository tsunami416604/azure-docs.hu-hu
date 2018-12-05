---
title: Azure-bA az OMS-portálon |} A Microsoft Docs
description: Az OMS-portálon az összes funkciót, az Azure-portálra sunsetted folyamatban van. Ez a cikk részletesen az átállás.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: bd97de9680eb2fe5f5e6da3b7cba48dc7280e398
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888715"
---
# <a name="oms-portal-moving-to-azure"></a>Azure-bA az OMS-portálon

> [!NOTE]
> Ez a cikk az Azure nyilvános felhő és a kormányzati felhő, kivéve, ha nincs másként jelölve vonatkozik.

Az Azure Portalon a központ, Azure-szolgáltatásokhoz, és a egy gazdag felügyeleti funkciókat biztosítanak, mint az irányítópultokat a erőforrások, az intelligens keresési eredmény erőforrások és a resource Management-környezetet kínál. Összefogása és egyszerűsíthetők a figyelési és felügyeleti munkafolyamat, hogy elindult az OMS-portál funkciókkal be az Azure Portalra. Az a Funkciók, az OMS-portál most már az Azure Portalon részét képezik. Sőt az új funkciók, például a Traffic Analytics némelyike csak az Azure Portalon érhető el. Mindent az OMS-portálon az Azure Portalon, és további csinált elérnie fogja. Ha ezt még nem tette meg, először érdemes az Azure portal használatát még ma!

**Az OMS-portálon a 2019. január 15 hivatalosan megszűnik.** Az Azure Portalra, és hatással vannak a Váltás könnyen izgatottak vagyunk. De tisztában vagyunk azzal módosítások nehéz és zavart okozhatnak. Esetleges kérdéseivel, visszajelzés, és a Küldés **LAUpgradeFeedback@microsoft.com**. Ez a cikk a főbb forgatókönyvek megvalósítását, és az átállás ütemterv keresztül haladnak.

## <a name="what-is-changing"></a>Mi változik? 
A következő módosításokat vannak alatt bejelentette, az OMS-portálon elavulása. Ezek a változások leírását az alábbi szakaszokban részletesebben.

- Létrehozhat új [munkaterületek csak](#new-workspaces) az Azure Portalon.
- Az Új riasztási megoldást [váltja fel a riasztás-kezelési megoldás](#changes-to-alerts).
- [Felhasználói hozzáférés-kezelés](#user-access-and-role-migration) mostantól az Azure Portalon az Azure szerepköralapú hozzáférés-vezérlés használatával történik.
- A [Application Insights-összekötő már nem szükséges](#application-insights-connector-and-solution) óta ugyanazokat a funkciókat több munkaterületen lekérdezések keresztül engedélyezhető.
- A [OMS Mobilalkalmazásról](#oms-mobile-app) hamarosan elavulttá válik. 
- A [NSG megoldás váltja](#azure-network-security-group-analytics) Traffic Analytics megoldás keresztül elérhető fejlett funkciókkal.
- A System Center Operations Manager Log Analytics és az új kapcsolat szükséges [frissített felügyeleti csomagok](#system-center-operations-manager).
- Lásd: [az OMS Frissítéstelepítések Migrálása az Azure-bA](../automation/migrate-oms-update-deployments.md) módosításai részleteiért [az Update Management](../automation/automation-update-management.md).


## <a name="what-should-i-do-now"></a>Mit tegyek most?
Bár a legtöbb funkciót továbbra is működni bármilyen migrálás végrehajtása nélkül, kell a következő feladatokat:

- Kell [áttelepíteni a felhasználói engedélyek](#user-access-and-role-migration) az Azure Portalra.
- Lásd: [Azure-bA Migrálhatja az OMS Frissítéstelepítések](../automation/migrate-oms-update-deployments.md) az Update Management megoldás transitioning részleteiért.

Tekintse meg [gyakori kérdések a Log Analytics-felhasználók az Azure Portalon OMS-portálon való váltás](../log-analytics/log-analytics-oms-portal-faq.md) az Azure Portalon való áttéréssel kapcsolatos információkat. Küldése a olyan visszajelzést, kérdéseket vagy problémákat **LAUpgradeFeedback@microsoft.com**.

## <a name="user-access-and-role-migration"></a>Felhasználói hozzáférés és a szerepkör áttelepítése
Az Azure portal hozzáférés-kezelés gazdagabb és hatékonyabb, mint a hozzáférés-kezelés az OMS-portálon. Lásd: [munkaterületeinek kezeléséhez](log-analytics-manage-access.md#manage-accounts-and-users) részletes hozzáférés-kezelés a Log Analyticsben.

> [!NOTE]
> Ez a cikk korábbi verzióinak említettük, hogy az engedélyeket szeretne automatikusan alakítható át az OMS-portálon az Azure Portalon. Az Automatikus átalakítás már nem tervezünk, és saját maga az átalakítást kell végrehajtania.

Előfordulhat, hogy már rendelkezik megfelelő hozzáféréssel az Azure Portalon ebben az esetben nem kell módosítani. Van néhány lehetséges, hogy nem rendelkezik megfelelő hozzáféréssel az ebben az esetben a rendszergazdai engedélyeket kell rendelnie azt.

- ReadOnly felhasználó engedélyeit az OMS-portálon, de nincs engedélye az Azure Portalon rendelkezik. 
- Közreműködői engedélyekkel az OMS-portálon, de az Azure Portalon csak olvasó hozzáféréssel rendelkezik.
 
Mindkét ezekben az esetekben a rendszergazdának manuálisan, a megfelelő szerepkör hozzárendelése a következő táblázatból. Azt javasoljuk, hogy hozzárendelje ezt a szerepkört az erőforrás-csoportba vagy előfizetésbe szinten.  Több előírásszerű útmutató hamarosan mindkét esetben biztosítjuk.

| OMS-portál engedély | Azure-szerepkörök |
|:---|:---|
| ReadOnly | Log Analytics olvasó |
| Közreműködő | Log Analytics közreműködő |
| Rendszergazda | Tulajdonos | 
 

## <a name="new-workspaces"></a>Új munkaterületek
Van már nem hozhat létre új munkaterületek az OMS-portálon. Kövesse az útmutató [Log Analytics-munkaterület létrehozása az Azure Portalon](log-analytics-quick-create-workspace.md) új munkaterület létrehozása az Azure Portalon.

## <a name="changes-to-alerts"></a>Riasztások módosításait

### <a name="alert-extension"></a>Riasztások kiterjesztése  

> [!NOTE]
> Riasztások most már teljes mértékben bővültek be az Azure Portalra, a nyilvános felhőben. Meglévő riasztási szabályait tekinthetnek meg az OMS-portálon, de azok csak az Azure Portalon lehet kezelni. Riasztások az Azure Portalon történő kiterjesztését az Azure government cloud 2018 október kezdődik.

Riasztások lett [kiterjeszthetők az Azure Portalon](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Miután ez befejeződött, a riasztásokat a felügyeleti műveletek csak az Azure Portalon érhető el. Meglévő riasztásokat továbbra is megjelennek az OMS-portálon. Ha a riasztásokat programozott a Log Analytics Alert REST API vagy a Log Analytics riasztási Resource-sablon használatával, szüksége Műveletcsoportok használata az API-hívások, Azure Resource Manager-sablonok és PowerShell-parancsok műveletei helyett.

### <a name="alert-management-solution"></a>Riasztáskezelés megoldás
Helyett a [felügyeleti megoldás riasztás](../azure-monitor/platform/alert-management-solution.md), használható [Azure Monitor riasztási felület egyesített](../monitoring-and-diagnostics/monitoring-overview-alerts.md) jelenítheti meg és kezelheti a riasztásokat. Az új felületet is beleértve naplóriasztások az Azure Log Analytics belül több forrásból származó összesíti azokat a riasztásokat. Tekintse meg a riasztások disztribúciók, intelligens csoportok keresztül kapcsolódó riasztások Automatikus csoportosítás előnyeit, és megtekintheti a riasztásokat több előfizetésre kiterjedő gazdag szűrők alkalmazása közben. Ezek a szolgáltatások előzetes verziója 2018. június 4 kezdve érhetők el. A riasztási felügyeleti megoldás nem lesz elérhető az Azure Portalon. 

A riasztási felügyeleti megoldások (riasztás típussal rendelkező rekordok) által gyűjtött adatok továbbra is a Log Analytics mindaddig, amíg a megoldás telepítve van-e a munkaterületen. 2018 augusztus kezdődően a munkaterületek, egységes riasztásokban riasztások streamelési engedélyezve lesz, és cserélje le ezt a funkciót. Néhány sémaváltozások várható, és később lesz bejelentve.

## <a name="oms-mobile-app"></a>OMS mobilalkalmazást
Az OMS mobilalkalmazást lesz sunsetted együtt az OMS-portálon. Helyett az OMS mobilalkalmazást az informatikai infrastruktúra, irányítópultok és mentett lekérdezések eléréséhez keresztül elérhető az Azure Portalon közvetlenül a böngészőből a mobil eszközére. Értesítéseket kaphat, konfigurálnia kell [Azure Action Groups általi](../monitoring-and-diagnostics/monitoring-action-groups.md) értesítések fogadásához, SMS vagy hanghívás formájában

## <a name="application-insights-connector-and-solution"></a>Application Insights-összekötő és megoldás
[Application Insights-összekötő](../azure-monitor/platform/app-insights-connector.md) lehetővé teszi az Application Insights adatokat importálnak a Log Analytics-munkaterületet. Az adatdeduplikáció volt szükség infrastruktúra és az alkalmazás adatok láthatóságának engedélyezése.

A támogatási [erőforrások közötti lekérdezések](../azure-monitor/log-query/cross-workspace-query.md), már nem létezik a szükséges adatokat. Mint ilyen a meglévő Application Insights-megoldáshoz elavulttá válik. Október kezdve nem lesz új Application Insights-erőforrások összekapcsolása a Log Analytics-munkaterületeket. Meglévő kapcsolatokat, és az irányítópultok továbbra is működik, a 2019. január 15-ig.


## <a name="azure-network-security-group-analytics"></a>Azure Network Security Group Analytics
A [Azure hálózati biztonsági csoport Analytics megoldás](../azure-monitor/insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) fogja írni a nemrégiben megjelent a [Traffic Analytics](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) amelyek felhasználói és alkalmazástevékenységekbe rálátást biztosít a felhőalapú hálózatokhoz. A TRAFFIC Analytics segítségével a szervezet hálózati tevékenység, a biztonságos alkalmazások és adatok naplózása, a számítási feladatok teljesítményének optimalizálása és a megfelelnek. 

Ez a megoldás elemzi a hálózati biztonsági csoportok naplóinak és elemezheti a következő.

- A hálózatok között az Azure és az Internet, nyilvános felhő-régiók, virtuális hálózatok és alhálózatok közötti forgalmat.
- Alkalmazások és protokollok a hálózaton, szimatolók vagy dedikált folyamat gyűjtemény berendezéseket szükségessége nélkül.
- Fő kapcsolattartók, forgalmas alkalmazások, virtuális gép beszélgetések a felhőben, a legaktívabb.
- Forrásaként és céljaként a forgalom virtuális hálózatai, kritikus fontosságú üzleti szolgáltatásokat és alkalmazásokat között helyek közötti kapcsolatok között.
- Biztonság, többek között a rosszindulatú adatforgalom, az internethez, az alkalmazások és a virtuális gépek Internet-hozzáférés megkísérlése nyitott portokkal.
- Kapacitás használata, amely segít a problémák kiépítés vagy alulkihasználtságának kiküszöbölése.

Továbbra is számíthat a diagnosztikai beállítások NSG-naplók a Log Analytics szolgáltatásba, így a meglévő mentett keresések, riasztások, az irányítópultok továbbra is működni fog küldeni. Ügyfelek, akik már telepítették a megoldás továbbra is használhatja azt további értesítésig. Szeptember 5-től kezdődően a hálózati biztonsági csoport Analytics megoldás a rendszer eltávolítja a marketplace-ről és a Közösség keresztül elérhetővé tett egy [Azure gyorsindítási sablon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Ha [Log Analytics az Operations Manager felügyeleti csoport csatlakoztatva](../azure-monitor/platform/om-agents.md), akkor továbbra is működik, nem kell módosítania. Az új kapcsolatok, kövesse az útmutató [konfigurálása az Operations Management Suite a Microsoft System Center Operations Manager felügyeleti csomag](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/).

## <a name="next-steps"></a>További lépések
- Lásd: [gyakori kérdések a Log Analytics-felhasználók az Azure Portalon OMS-portálon való váltás](log-analytics-oms-portal-faq.md) útmutatást áthelyezését az OMS-portálon az Azure Portalon.
