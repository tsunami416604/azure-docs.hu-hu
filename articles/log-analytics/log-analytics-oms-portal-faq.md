---
title: OMS-portálon való váltás az Azure portálon a Log Analytics felhasználók kapcsolatos gyakori kérdések |} A Microsoft Docs
description: Váltás az OMS-portálon az Azure Portalra a Log Analytics-felhasználók számára a gyakori kérdésekre adott válaszok.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 503d5913efe67bd0de738f68921b9631c63acfa8
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116044"
---
# <a name="common-questions-for-transition-from-oms-portal-to-azure-portal-for-log-analytics-users"></a>OMS-portálon való váltás az Azure portálon a Log Analytics felhasználók kapcsolatos gyakori kérdések
A log Analytics kezdetben a saját Portalt használta az OMS-portál nevű felügyelje a konfigurációt, és elemezheti az összegyűjtött adatokat.  Ezen a portálon az összes funkció át lett helyezve az Azure Portalra, továbbra is ki kell alakítani.

Ez a cikk gyakori kérdésekre ad választ a felhasználók számára, így az átállás.  Ha követte a Log Analytics az OMS-portálon, majd választ kaphat itt a hogyan végrehajtani a feladatot az Azure Portalon.

## <a name="do-i-need-to-migrate-anything"></a>Kell áttelepíteni semmit?
Nem. Nem változtak, a Log Analyticshez való kerül sor, így semmit nem kell áttelepíteni. Változnak egyedül a felület, amellyel-e férni. Sőt most már az Azure portal használata az azonos munkaterületek, a megoldások, a nézetek elérésére, és naplókeresésekkel, amelyet használhat az OMS-portálon még ma.

## <a name="where-do-i-find-log-analytics-in-azure"></a>Hol találom a Log Analytics az Azure-ban?
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen.  Kattintson a **minden szolgáltatás**, és az erőforrások listájában írja be a **Log Analytics**. Válassza ki **Log Analytics** , és válassza ki a munkaterülethez. A munkaterület összegzés lapján jelenik meg.

![Azure-beli monitorozási munkaterület](media/log-analytics-new-portal/log-analytics.png)

## <a name="how-do-i-manage-permissions"></a>Hogyan kezelhetem engedélyeket?
A Log Analytics-munkaterületet az Azure Portalon nem érheti el, ha szüksége konfigurálja az engedélyeik [Azure szerepköralapú hozzáférés-](../active-directory/role-based-access-control-configure.md). A munkaterület-engedélyek kezelésének részletes ismertetéséért lásd: [munkaterületeinek kezeléséhez](../log-analytics/log-analytics-manage-access.md#manage-accounts-and-users). A riasztásokra vonatkozó engedélyekkel kezeléséről további információért lásd: [szerepkörök, engedélyek és biztonság az Azure Monitor használatának első lépései](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md).

## <a name="how-do-i-create-a-new-workspace"></a>Hogyan hozhatok létre egy új munkaterületet? 
A munkaterület listája, az Azure Portalon, kattintson **Hozzáadás** munkaterületek listájában.  További információkat lásd: [Log Analytics-munkaterület létrehozása az Azure Portalon](../log-analytics/log-analytics-quick-create-workspace.md).

![– Áttekintés oldalra](media/log-analytics-new-portal/new-workspace.png)

## <a name="where-is-my-overview-page"></a>Hol található az Áttekintés lapon?
Az OMS-portálon fő képernyőjén jeleníti meg az telepítve van a munkaterületet, és az egyéni nézetek létrehozott felügyeleti megoldások csempéi. Ez a nézet az Azure Portalon érhető el. A munkaterületen válassza ki a **munkaterület összefoglalás**.

![– Áttekintés oldalra](media/log-analytics-new-portal/overview.png)

## <a name="how-do-i-open-log-search-and-view-designer"></a>Hogyan nyitható meg a naplóbeli keresés és az adatforrásnézet-tervezőből?
Mindkét **naplóbeli keresés** és **adatforrásnézet-tervezőből** érhetők el a fő oldalon, és az Azure Portalon, a munkaterület bal oldali menüben lévő közvetlenül ezután a **áttekintése**.

## <a name="where-do-i-find-settings"></a>Hol találom meg a beállításokat?
Számos, a beállítások a **beállítások** szakaszában az OMS-portálon érhetők el a **speciális beállítások** menüben a munkaterület az Azure Portalon.

![Speciális beállítások](media/log-analytics-new-portal/advanced-settings.png)

Az alábbi szakaszok nyújtanak, hogyan férhet hozzá a korábban elérhető beállítások teljes listáját a **beállítások** szakaszában az OMS-portálon.

### <a name="accounts"></a>Fiókok 
Fiókok beállításait kezeli az Azure Portalon különböző helyeken az alábbi táblázatban leírtak szerint.

| A beállítás az OMS-portálon | Az Azure Portalon megfelelője |
|:---|:---|
| Automation-fiók | **Automation-fiók** a munkaterület menüje. |
| Az Azure-előfizetés és adatforgalmi Díjcsomagra | **A tarifacsomag** a munkaterület menüje. |
| Felhasználók kezelése | Használja az Azure szerepköralapú hozzáférés az [a munkaterületre vonatkozó engedélyek kezelése](#how-do-i-manage-permissions). |
| Munkaterület-információk | A rendelkezésre álló információk **OMS-munkaterület** a munkaterület menüje. |

### <a name="alerts"></a>Riasztások
Riasztási szabályok alapján a Log Analytics-lekérdezések már kezelhetők a [egységes felhasználói élményt riasztási](#how-do-i-create-and-manage-alerts). 

### <a name="computer-groups"></a>Számítógépcsoportok
A számítógépcsoportok kezelése a **speciális beállítások** a munkaterület menüje. 

### <a name="connected-sources"></a>Összekapcsolt források
A legtöbb csatlakoztatott adatforrás-beállítások kezelése a a a **speciális beállítások** a munkaterület menüje. Az alábbi táblázat részletesen lásd ebben a menüben.

| A beállítás az OMS-portálon | Az Azure Portalon megfelelője |
|:---|:---|
| Windowsos kiszolgálók   | **Speciális beállítások** a munkaterület menüje. |
| Linux-kiszolgálók   | **Speciális beállítások** a munkaterület menüje. |
| Azure Storage     | **Speciális beállítások** a munkaterület menüje. |
| System Center     | **Speciális beállítások** a munkaterület menüje. |
| Office 365        | Tekintse meg a [Office 365-kezelési megoldásra vonatkozó dokumentáció](../operations-management-suite/oms-solution-office-365.md) konfigurációját. |
| Windows-telemetria | Még nem érhető el az Azure Portalon. |
| ITSM-összekötő    | Lásd: [csatlakozás ITSM termékekkel/szolgáltatásokkal rendelkező informatikai szolgáltatásfelügyeleti összekötő](../log-analytics/log-analytics-itsmc-connections.md) vonatkozó utasításokat az ITSM-szolgáltatás csatlakoztatása a Log Analytics használatával. |

### <a name="data"></a>Adatok
A legtöbb adat beállításainak kezelése a a a **speciális beállítások** a munkaterület menüje. Az alábbi táblázat részletesen lásd ebben a menüben.

| A beállítás az OMS-portálon | Az Azure Portalon megfelelője |
|:---|:---|
| Windows-eseménynaplók           | **Speciális beállítások** a munkaterület menüje. |
| Windows-teljesítményszámlálók | **Speciális beállítások** a munkaterület menüje. |
| Linux-teljesítményszámlálók   | **Speciális beállítások** a munkaterület menüje. |
| IIS-naplók                     | **Speciális beállítások** a munkaterület menüje. |
| Egyéni mezők                | **Speciális beállítások** a munkaterület menüje. |
| Egyéni naplók                  | **Speciális beállítások** a munkaterület menüje. |
| Rendszernapló                       | **Speciális beállítások** a munkaterület menüje. |
| Application Insights         | Ez a megoldás most, hogy a Log Analytics és az Application Insights megoszthatja az ugyanazon motor már elavult.  |
| Windows változáskövetés        | **A változáskövetés** menü az Azure Automationben. Lásd: [a környezetében a Change Tracking megoldás révén nyomon követésére](../automation/automation-change-tracking.md) részleteiről. |
| Windows beállításjegyzék-követés        | **A változáskövetés** menü az Azure Automationben. Lásd: [a környezetében a Change Tracking megoldás révén nyomon követésére](../automation/automation-change-tracking.md) részleteiről. |
| Linuxos fájlkövetés          | **A változáskövetés** menü az Azure Automationben. Lásd: [a környezetében a Change Tracking megoldás révén nyomon követésére](../automation/automation-change-tracking.md) részleteiről. |

### <a name="solutions"></a>Megoldások
A megoldások kezelése az **megoldások** a munkaterület menüje. 

## <a name="how-do-i-install-and-remove-management-solutions"></a>Hogyan telepítése és eltávolítása a felügyeleti megoldások?
Az OMS-portálon az eszközfelügyeleti megoldások telepítéséhez kövesse a megoldástárban, és eltávolítja azokat **beállítások**. Az Azure Portalon [felügyeleti megoldások telepítése](../monitoring/monitoring-solutions.md#install-a-management-solution) az Azure Marketplace-ről. [Távolítsa el a megoldások](../monitoring/monitoring-solutions.md#remove-a-management-solution) telepített megoldások listájából.

## <a name="how-do-i-create-and-manage-alerts"></a>Hogyan hozzon létre és kezelheti a riasztásokat?
Riasztási szabályok alapján a Log Analytics-lekérdezések már kezelhetők a [egységes felhasználói élményt riasztási](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). Lásd: [bővítése a riasztások a Log Analytics az Azure Alerts](../monitoring-and-diagnostics/monitoring-alerts-extend-tool.md) konfigurálása és riasztások használatával az Azure Portalon.

## <a name="how-do-i-access-my-dashboards"></a>Hogyan érhetem el az irányítópultokat?
[Az irányítópultok](../log-analytics/log-analytics-dashboards.md) a Log Analytics már elavult.  Adatok a Log Analytics használatával jelenítheti meg [adatforrásnézet-tervezőből](../log-analytics/log-analytics-view-designer.md) amely további funkciókat és a lekérdezés rögzítése és az Azure-irányítópultok nézetek rendelkezik.

## <a name="how-do-i-check-my-usage"></a>Hogyan tudom ellenőrizni, a használat?
Is most már egyszerűen megtekinthető és kezelhető a használat és a Log Analytics költsége kiválasztásával **felhasználás és becsült költségek** a munkaterületén.

![Használat és becsült költségek](media/log-analytics-new-portal/usage.png)


## <a name="can-i-still-use-the-classic-portal"></a>Továbbra is használható a klasszikus portálon?
Korlátozott ideig továbbra is elérheti a portálon keresztül az URL-címet, a saját munkaterület nevére: https://\<a munkaterület neve\>. portal.mms.microsoft.com. Azt javasoljuk, ha az Azure portal használatával, és biztosítja a számunkra, küldjön visszajelzést LAUpgradeFeedback@microsoft.com az olyan problémák elhárítását.

## <a name="next-steps"></a>További lépések

- [Keresse meg és telepítse a felügyeleti megoldások](../monitoring/monitoring-solutions.md) az Azure portal használatával.
- Ismerje meg [az Azure Portal Naplókeresési](log-analytics-log-search-portals.md).