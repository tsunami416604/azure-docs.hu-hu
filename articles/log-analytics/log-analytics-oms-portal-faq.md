---
title: Átmenet OMS-portálon az Azure-portál a Log Analyticshez felhasználók gyakori kérdések |} Microsoft Docs
description: A Naplóelemzési felhasználók számára az OMS-portálon az Azure portálra való áttérés menetének gyakori kérdésre kaphat választ.
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
ms.date: 06/07/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 6479db44681071932ff92ee17ea0aec6518dc74e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128639"
---
# <a name="common-questions-for-transition-from-oms-portal-to-azure-portal-for-log-analytics-users"></a>Átmenet OMS-portálon az Azure-portál a Log Analyticshez felhasználók gyakori kérdések
Naplóelemzési kezdetben a saját portál néven az OMS-portálon kezelése a configuration és használt összegyűjtött adatok elemzése.  Ezen a portálon az összes funkciót át lett helyezve az Azure portálon ahol továbbra is ki kell alakítani.

Ez a cikk a felhasználók számára, hogy ez a változás gyakori kérdésekre ad választ.  Ha Log Analyticshez az OMS-portálon, majd választ kaphat itt az, hogy milyen feladatokat végezheti el az ugyanabban az Azure portálon.

## <a name="where-do-i-find-log-analytics-in-azure"></a>Hol található az Azure Log Analytics?
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen.  Kattintson a **minden szolgáltatás**, írja be az erőforrások listájához, **Naplóelemzési**. Válassza ki **Naplóelemzési** , és válassza a munkaterületen. A munkaterület az összefoglalás lapon jelenik meg.

![Azure-beli monitorozási munkaterület](media/log-analytics-new-portal/log-analytics.png)

## <a name="how-do-i-manage-permissions"></a>Hogyan kezeli az engedélyeket?
Ha az Azure-portálon a Naplóelemzési munkaterületet nem rendelkezik hozzáféréssel, szeretné-e a engedélyeik konfigurálása [Azure szerepköralapú hozzáférés-](../active-directory/role-based-access-control-configure.md). A munkaterület engedélyek kezelése, lásd: [munkaterületek kezelése](../log-analytics/log-analytics-manage-access.md#manage-accounts-and-users). A riasztásokra vonatkozó engedélyekkel kezeléséről további információért lásd: [Ismerkedés a szerepkörök, engedélyek és biztonsági Azure megfigyelővel](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md).

## <a name="how-do-i-create-a-new-workspace"></a>Hogyan hozható létre egy új munkaterületet? 
Az Azure portálon munkaterületek listájának megtekintéséhez kattintson **Hozzáadás** a munkaterületek listájában.  A részleteket lásd: [Naplóelemzési munkaterület létrehozása az Azure portálon](../log-analytics/log-analytics-quick-create-workspace.md).

![– Áttekintés oldalra](media/log-analytics-new-portal/new-workspace.png)

## <a name="where-is-my-overview-page"></a>Hol található a áttekintése lapon?
Az OMS-portálon a fő képernyőn megjelenik az összes telepíteni a munkaterületet, és az Ön által létrehozott egyéni nézetek a felügyeleti megoldásokra csempék. Ez a nézet érhető el az Azure portálon. Válassza ki a munkaterületről **munkaterület összegzés**.

![– Áttekintés oldalra](media/log-analytics-new-portal/overview.png)

## <a name="how-do-i-open-log-search-and-view-designer"></a>Hogyan meg naplófájl-keresési és adatforrásnézet-tervezőből?
Mindkét **naplófájl-keresési** és **adatforrásnézet-tervezőből** érhetők el fő lapján, és az Azure portálon, a munkaterület bal oldali menüben jobb mellett a **áttekintése**.

## <a name="where-do-i-find-settings"></a>Hol található beállítások?
Számos, a beállítás a **beállítások** az OMS-portálon szakaszában érhetők el a **speciális beállítások** az Azure-portálon a munkaterület menüjében.

![Speciális beállítások](media/log-analytics-new-portal/advanced-settings.png)

Az alábbiakban adja meg, hogyan férhet hozzá a korábban elérhető beállítások teljes listáját a **beállítások** szakasza az OMS-portálon.

### <a name="accounts"></a>Fiókok 
Fiókok beállításait a különböző helyeken az Azure portálon felügyeli a következő táblázatban ismertetett módon.

| A beállítás az OMS-portálon | Egyenértékű az Azure-portálon |
|:---|:---|
| Automation-fiók | **Automation-fiók** a munkaterület menüje. |
| Azure-előfizetés & adatforgalmi Díjcsomagra | **A tarifacsomag** a munkaterület menüje. |
| Felhasználók kezelése | Használja az Azure szerepköralapú hozzáférés [a munkaterület vonatkozó engedélyek](#how-do-i-manage-permissions). |
| Munkaterület-információk | Rendelkezésre álló információkat **OMS-munkaterület** a munkaterület menüje. |

### <a name="alerts"></a>Riasztások
A Naplóelemzési lekérdezések alapuló riasztási szabályok kezelése mostantól a [egységes élmény riasztási](#how-do-i-create-and-manage-alerts). 

### <a name="computer-groups"></a>Számítógépcsoportok
A számítógépcsoportok kezelése a **speciális beállítások** a munkaterület menüje. 

### <a name="connected-sources"></a>Összekapcsolt források
A legtöbb csatlakoztatott adatforrás-beállítások kezelése a a a **speciális beállítások** a munkaterület menüje. A következő táblázat az ebben a menüben minden részében részletes adatokat biztosít.

| A beállítás az OMS-portálon | Egyenértékű az Azure-portálon |
|:---|:---|
| Windowsos kiszolgálók   | **Speciális beállítások** a munkaterület menüje. |
| Linux-kiszolgálókon   | **Speciális beállítások** a munkaterület menüje. |
| Azure Storage     | **Speciális beállítások** a munkaterület menüje. |
| System Center     | **Speciális beállítások** a munkaterület menüje. |
| Office 365        | Tekintse meg a [Office 365 felügyeleti megoldása dokumentációjában](../operations-management-suite/oms-solution-office-365.md) konfigurációs részletek. |
| Windows-telemetria | Még nem érhető el az Azure portálon. |
| ITSM összekötő    | Lásd: [csatlakozás ITSM termékek vagy szolgáltatások IT Service Management-összekötő](../log-analytics/log-analytics-itsmc-connections.md) a ITSM szolgáltatás Log Analyticshez való csatlakozással kapcsolatban. |

### <a name="data"></a>Adatok
A legtöbb beállítások kezelése a a a **speciális beállítások** a munkaterület menüje. A következő táblázat az ebben a menüben minden részében részletes adatokat biztosít.

| A beállítás az OMS-portálon | Egyenértékű az Azure-portálon |
|:---|:---|
| Windows-eseménynaplók           | **Speciális beállítások** a munkaterület menüje. |
| Windows-teljesítményszámlálók | **Speciális beállítások** a munkaterület menüje. |
| Linux-teljesítményszámlálók   | **Speciális beállítások** a munkaterület menüje. |
| IIS-naplók                     | **Speciális beállítások** a munkaterület menüje. |
| Egyéni mezők                | **Speciális beállítások** a munkaterület menüje. |
| Egyéni naplók                  | **Speciális beállítások** a munkaterület menüje. |
| Rendszernapló                       | **Speciális beállítások** a munkaterület menüje. |
| Application Insights         | Ez a megoldás most, Naplóelemzés és az Application Insights osztani ugyanazt a adatok motort elavult.  |
| Windows változáskövetés        | **A változáskövetés** az Azure Automationben menü. Lásd: [követni a változásokat a változáskövetési megoldás a környezetében lévő](../automation/automation-change-tracking.md) részleteiről. |
| Windows beállításjegyzék-követés        | **A változáskövetés** az Azure Automationben menü. Lásd: [követni a változásokat a változáskövetési megoldás a környezetében lévő](../automation/automation-change-tracking.md) részleteiről. |
| Linuxos fájlkövetés          | **A változáskövetés** az Azure Automationben menü. Lásd: [követni a változásokat a változáskövetési megoldás a környezetében lévő](../automation/automation-change-tracking.md) részleteiről. |

### <a name="solutions"></a>Megoldások
A megoldások kezelése a **megoldások** a munkaterület menüje. 

## <a name="how-do-i-install-and-remove-management-solutions"></a>Hogyan telepítése és eltávolítása a felügyeleti megoldásokra?
Az OMS-portálon megoldások telepítése a megoldások gyűjteményből, és eltávolítja azokat **beállítások**. Az Azure-portálon [telepítési felügyeleti megoldások](../monitoring/monitoring-solutions.md#install-a-management-solution) az Azure piactérről. [Távolítsa el a megoldások](../monitoring/monitoring-solutions.md#remove-a-management-solution) telepített megoldások a listából.

## <a name="how-do-i-create-and-manage-alerts"></a>Hogyan hozzon létre és kezelheti a riasztásokat?
A Naplóelemzési lekérdezések alapuló riasztási szabályok kezelése mostantól a [egységes élmény riasztási](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). Lásd: [Naplóelemzési riasztások kiterjeszti Azure riasztások hogyan](../monitoring-and-diagnostics/monitoring-alerts-extend-tool.md) konfigurálása és riasztások segítségével az Azure portálon.

## <a name="how-do-i-access-my-dashboards"></a>Hogyan érhetem el saját irányítópultok?
[Irányítópultok](../log-analytics/log-analytics-dashboards.md) a Naplóelemzési elavultak.  Adatok Naplóelemzési használatával jelenítheti meg [adatforrásnézet-tervezőből](../log-analytics/log-analytics-view-designer.md) további funkciók és PIN-kód lekérdezés és Azure irányítópult-nézetet tartalmaz.

## <a name="how-do-i-check-my-usage"></a>Hogyan ellenőrzi a használatalapú?
Most egyszerűen megtekintheti és kezelése a használati és Naplóelemzési költségét kiválasztásával **használati és a becsült költség** a munkaterületen.

![Használat és becsült költségek](media/log-analytics-new-portal/usage.png)


## <a name="can-i-still-use-the-classic-portal"></a>Továbbra is használható a klasszikus portálon?
Korlátozott időre, továbbra is elérheti a portálon keresztül az URL-cím, a saját munkaterület nevével: https://\<a munkaterület neve\>. portal.mms.microsoft.com. Azt javasoljuk azonban, Azure-portálon, és meg kell adnia a visszajelzést a LAUpgradeFeedback@microsoft.com az olyan problémák elhárítását.

## <a name="next-steps"></a>További lépések

- [Megkeresését és telepítését a megoldások](../monitoring/monitoring-solutions.md) az Azure portál használatával.
- További tudnivalók [naplófájl-keresési az Azure portálon](log-analytics-log-search-portals.md).