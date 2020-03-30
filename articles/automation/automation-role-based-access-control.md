---
title: Szerepköralapú hozzáférés-vezérlés az Azure Automationben
description: A Szerepköralapú hozzáférés-vezérlés (RBAC) hozzáférés-vezérlést biztosít az Azure-erőforrásokhoz. Ez a cikk ismerteti az RBAC beállítását az Azure Automationben.
keywords: automation rbac, szerepköralapú hozzáférés-vezérlés, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 05/17/2018
ms.topic: conceptual
ms.openlocfilehash: 8caf502db91ab09eea48fc8a902dacf6bf40f24c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278635"
---
# <a name="role-based-access-control-in-azure-automation"></a>Szerepköralapú hozzáférés-vezérlés az Azure Automationben

A Szerepköralapú hozzáférés-vezérlés (RBAC) hozzáférés-vezérlést biztosít az Azure-erőforrásokhoz. Az [RBAC](../role-based-access-control/overview.md)használatával elkülönítheti a feladatokat a csapaton belül, és csak a feladatok elvégzéséhez szükséges felhasználók, csoportok és alkalmazások elérésének mennyiségét biztosíthatja. Szerepköralapú hozzáférést adhat a felhasználóknak az Azure Portalon, az Azure parancssori eszközein vagy az Azure Management API-kon keresztül.

## <a name="roles-in-automation-accounts"></a>Szerepkörök az Automation-fiókokban

Az Azure Automationben a hozzáférés biztosításához a megfelelő RBAC-szerepkörök rendelhetők az Automation-fiók tartományában lévő felhasználókhoz, csoportokhoz és alkalmazásokhoz. Alább láthatók az Automation-fiók által támogatott beépített szerepkörök:

| **Szerepkör** | **Leírás** |
|:--- |:--- |
| Tulajdonos |A Tulajdonos szerepkör lehetővé teszi a hozzáférést az Automation-fiókon belüli összes erőforráshoz és művelethez, beleértve a hozzáférést más felhasználók, csoportok és alkalmazások számára az Automation-fiók kezeléséhez. |
| Közreműködő |A közreműködői szerepkör segítségével minden elemet kezelhet, csak más felhasználók Automation-fiókokra vonatkozó hozzáférési jogosultságait nem módosíthatja. |
| Olvasó |Az olvasói szerep lehetővé teszi az összes erőforrás megtekintését egy Automation-fiókban, de módosítás nem hajtható vele végre. |
| Automation-operátor |Az Automation Operator szerepkör lehetővé teszi a runbook oktatásának és tulajdonságainak megtekintését, valamint az Automation-fiók összes runbookjának létrehozását és kezelését. Ez a szerepkör akkor hasznos, ha meg szeretné védeni az Automation-fiók erőforrásait, például a hitelesítő adatokat és a runbookokat a megtekintéstől vagy a módosítástól, de továbbra is lehetővé teszi a szervezet tagjai számára, hogy végrehajtsák ezeket a runbookokat. |
|Automatizálási feladat operátor|Az Automation-feladat operátor szerepkör lehetővé teszi, hogy hozzon létre és kezelje a feladatokat az összes runbookok egy Automation-fiókban.|
|Automatizálási runbook-operátor|Az Automation Runbook Operator szerepkör lehetővé teszi a runbook nevének és tulajdonságainak megtekintését.|
| Log Analytics közreműködő | A Log Analytics közreműködői szerepkör lehetővé teszi az összes figyelési adat olvasását és a figyelési beállítások szerkesztését. A figyelési beállítások szerkesztése magában foglalja a virtuális gép bővítmény virtuális gépekhez való hozzáadását, a tárfiók kulcsainak olvasását az Azure storage-ból származó naplók gyűjteményének konfigurálásához, az Automation-fiókok létrehozásához és konfigurálásához, a megoldások hozzáadásához és az Azure-diagnosztika konfigurálásához az összes Azure-erőforrást.|
| Log Analytics olvasó | A Log Analytics-olvasó szerepkör lehetővé teszi az összes figyelési adat megtekintését és keresését, valamint a figyelési beállítások megtekintését. Ez magában foglalja az Azure-diagnosztika konfigurációjának megtekintését az összes Azure-erőforráson. |
| Közreműködő figyelése | A figyelésközreműködő szerepkör lehetővé teszi az összes figyelési adat olvasását és a figyelési beállítások frissítését.|
| Monitoring olvasó | A Figyelési olvasó szerepkör lehetővé teszi az összes figyelési adat olvasását. |
| Felhasználói hozzáférés rendszergazdája |A felhasználói hozzáférés rendszergazdájának szerepköre lehetővé teszi, hogy kezelje a felhasználói hozzáférést az Azure Automation-fiókokhoz. |

## <a name="role-permissions"></a>Szerepkör-engedélyek

Az alábbi táblázatok az egyes szerepkörhöz megadott engedélyeket ismertetik. Ez magában foglalhatja a műveleteket, amelyek engedélyeket adnak, és a NotActions, amelyek korlátozzák őket.

### <a name="owner"></a>Tulajdonos

A tulajdonos mindent kezelhet, beleértve a hozzáférést is. Az alábbi táblázat a szerepkörhöz megadott engedélyeket mutatja be:

|Műveletek|Leírás|
|---|---|
|Microsoft.Automation/automationAccounts/|Minden típusú erőforrás létrehozása és kezelése.|

### <a name="contributor"></a>Közreműködő

A közreműködő a hozzáférés kivételével mindent kezelhet. Az alábbi táblázat a szerepkörhöz megadott és elutasított engedélyeket mutatja be:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Minden típusú erőforrás létrehozása és kezelése|
|**Nem műveletek**||
|Microsoft.Authorization/*/Delete| Szerepkörök és szerepkör-hozzárendelések törlése.       |
|Microsoft.Authorization/*/Írás     |  Szerepkörök és szerepkör-hozzárendelések létrehozása.       |
|Microsoft.Authorization/eevateAccess/Action    | Megtagadja a felhasználói hozzáférés rendszergazdájának létrehozását.       |

### <a name="reader"></a>Olvasó

Az olvasó megtekintheti az Automation-fiók összes erőforrását, de nem módosíthatja azerőforrásokat.

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Az összes erőforrás megtekintése egy Automation-fiókban. |

### <a name="automation-operator"></a>Automation-operátor

Az Automation Operátor képes feladatokat létrehozni és kezelni, és olvasni a runbookok nevét és tulajdonságait az Automation-fiók összes runbookja számára.  Megjegyzés: Ha szabályozni szeretné az operátorok hozzáférését az egyes runbookokhoz, akkor ne állítsa be ezt a szerepkört, hanem használja az "Automation Job Operator" és az "Automation Runbook Operator" szerepköröket. Az alábbi táblázat a szerepkörhöz megadott engedélyeket mutatja be:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft.Authorization/*/olvasás|Olvassa be az engedélyt.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Olvassa el a hibrid Runbook feldolgozó erőforrások.|
|Microsoft.Automation/automationAccounts/jobs/read|A runbook feladatainak listázása.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Szüneteltetett feladat folytatása.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Folyamatban lévő feladat megszakítása.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Olvassa el a feladatstreamek és kimenet.|
|Microsoft.Automation/automationAccounts/jobs/output/read|Szerezd meg a kimenet egy munkát.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Folyamatban lévő feladat szüneteltetése.|
|Microsoft.Automation/automationAccounts/jobs/write|Munkahelyeket teremt.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Azure Automation-feladatütemezés beszerezni.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Hozzon létre egy Azure Automation-feladatütemezést.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Az Automation-fiókhoz kapcsolódó munkaterület beszerezni.|
|Microsoft.Automation/automationAccounts/read|Szerezzen be egy Azure Automation-fiókot.|
|Microsoft.Automation/automationAccounts/runbooks/read|Szerezzen be egy Azure Automation-runbookot.|
|Microsoft.Automation/automationAccounts/schedules/read|Szerezzen be egy Azure Automation-ütemezési eszközt.|
|Microsoft.Automation/automationAccounts/schedules/write|Hozzon létre vagy frissítsen egy Azure Automation-ütemezési eszközt.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |A szerepkörök és a szerepkör-hozzárendelések olvasása.         |
|Microsoft.Resources/deployments/*      |Erőforráscsoport-telepítések létrehozása és kezelése.         |
|Microsoft.Insights/alertRules/*      | Riasztási szabályok létrehozása és kezelése.        |
|Microsoft.Támogatás/* |Támogatási jegyek létrehozása és kezelése.|

### <a name="automation-job-operator"></a>Automatizálási feladat operátor

Automation-feladat operátori szerepkör az Automation-fiók hatókörén van megadva.Ez lehetővé teszi, hogy az operátor engedélyeket hozzon létre és kezeljen a fiók összes runbookja számára. Az alábbi táblázat a szerepkörhöz megadott engedélyeket mutatja be:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft.Authorization/*/olvasás|Olvassa be az engedélyt.|
|Microsoft.Automation/automationAccounts/jobs/read|A runbook feladatainak listázása.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Szüneteltetett feladat folytatása.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Folyamatban lévő feladat megszakítása.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Olvassa el a feladatstreamek és kimenet.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Folyamatban lévő feladat szüneteltetése.|
|Microsoft.Automation/automationAccounts/jobs/write|Munkahelyeket teremt.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  A szerepkörök és a szerepkör-hozzárendelések olvasása.       |
|Microsoft.Resources/deployments/*      |Erőforráscsoport-telepítések létrehozása és kezelése.         |
|Microsoft.Insights/alertRules/*      | Riasztási szabályok létrehozása és kezelése.        |
|Microsoft.Támogatás/* |Támogatási jegyek létrehozása és kezelése.|

### <a name="automation-runbook-operator"></a>Automatizálási runbook-operátor

Automation Runbook operátor szerepkör a Runbook hatókörén. Az Automation Runbook Operátor megtekintheti a runbook nevét és tulajdonságait.Ez a szerepkör az "Automation Job Operator" szerepkörrel kombinálva lehetővé teszi az operátor számára, hogy a runbookhoz is hozzon létre és kezeljen feladatokat. Az alábbi táblázat a szerepkörhöz megadott engedélyeket mutatja be:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Sorolja fel a runbookokat.        |
|Microsoft.Authorization/*/olvasás      | Olvassa be az engedélyt.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |A szerepkörök és a szerepkör-hozzárendelések olvasása.         |
|Microsoft.Resources/deployments/*      | Erőforráscsoport-telepítések létrehozása és kezelése.         |
|Microsoft.Insights/alertRules/*      | Riasztási szabályok létrehozása és kezelése.        |
|Microsoft.Támogatás/*      | Támogatási jegyek létrehozása és kezelése.        |

### <a name="log-analytics-contributor"></a>Log Analytics közreműködő

A Log Analytics közreműködője leolvashatja az összes figyelési adatot, és szerkesztheti a figyelési beállításokat. A figyelési beállítások szerkesztése magában foglalja a virtuális gépbővítmény virtuális gépekhez való hozzáadását; a tárfiók kulcsainak olvasása az Azure Storage-ból származó naplók gyűjteményének konfigurálásához; Automatizálási fiókok létrehozása és konfigurálása; megoldások hozzáadása; és konfigurálja az Azure-diagnosztikát az összes Azure-erőforráson. Az alábbi táblázat a szerepkörhöz megadott engedélyeket mutatja be:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/olvasás|A titkok kivételével minden típusú erőforrást elkell olvasni.|
|Microsoft.Automation/automationAccounts/*|Automatizálási fiókok kezelése.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Virtuálisgép-bővítmények létrehozása és kezelése.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Klasszikus tárfiók kulcsok listázása.|
|Microsoft.Compute/virtualMachines/extensions/*|Klasszikus virtuálisgép-bővítmények létrehozása és kezelése.|
|Microsoft.Insights/alertRules/*|Olvasási/írási/törlési figyelmeztetési szabályok.|
|Microsoft.Insights/diagnosticSettings/*|Diagnosztikai beállítások olvasása/írása/törlése.|
|Microsoft.OperationalInsights/*|Az Azure Monitor naplóinak kezelése.|
|Microsoft.OperationsManagement/*|Megoldások kezelése a munkaterületeken.|
|Microsoft.Resources/deployments/*|Erőforráscsoport-telepítések létrehozása és kezelése.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Erőforráscsoport-telepítések létrehozása és kezelése.|
|Microsoft.Storage/storageAccounts/listKeys/action|A tárfiók kulcsainak listázása.|
|Microsoft.Támogatás/*|Támogatási jegyek létrehozása és kezelése.|

### <a name="log-analytics-reader"></a>Log Analytics olvasó

A Log Analytics-olvasó megtekintheti és keresheti az összes figyelési adatot, valamint megtekintheti a figyelési beállításokat, beleértve az Azure-diagnosztika konfigurációjának megtekintését az összes Azure-erőforráson. Az alábbi táblázat a szerepkörhöz megadott vagy elutasított engedélyeket mutatja be:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/olvasás|A titkok kivételével minden típusú erőforrást elkell olvasni.|
|Microsoft.OperationalInsights/munkaterületek/elemzés/lekérdezés/művelet|Lekérdezések kezelése az Azure Monitor naplóiban.|
|Microsoft.OperationalInsights/munkaterületek/keresés/művelet|Keresés az Azure Monitor naplóadatai között.|
|Microsoft.Támogatás/*|Támogatási jegyek létrehozása és kezelése.|
|**Nem műveletek**| |
|Microsoft.OperationalInsights/munkaterületek/sharedKeys/read|Nem lehet olvasni a megosztott hozzáférési kulcsokat.|

### <a name="monitoring-contributor"></a>Közreműködő figyelése

A figyelési közreműködő leolvashatja az összes figyelési adatot, és frissítheti a figyelési beállításokat. Az alábbi táblázat a szerepkörhöz megadott engedélyeket mutatja be:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/olvasás|A titkok kivételével minden típusú erőforrást elkell olvasni.|
|Microsoft.AlertsManagement/alerts/*|Riasztások kezelése.|
|Microsoft.AlertsManagement/alertsÖsszefoglaló/*|A Riasztás irányítópult kezelése.|
|Microsoft.Insights/AlertRules/*|Riasztási szabályok kezelése.|
|Microsoft.Insights/components/*|Az Application Insights-összetevők kezelése.|
|Microsoft.Insights/DiagnosticSettings/*|Diagnosztikai beállítások kezelése.|
|Microsoft.Insights/eventtypes/*|Tevékenységnapló-események (felügyeleti események) listázása egy előfizetésben. Ez az engedély a tevékenységnaplóhoz való programozott és portálos hozzáférésre is vonatkozik.|
|Microsoft.Insights/LogDefinitions/*|Ez az engedély szükséges azoknak a felhasználóknak, akiknek a portálon keresztül hozzáférésre van szükségük a tevékenységnaplókhoz. A naplókategóriák listázása a Tevékenységnaplóban.|
|Microsoft.Insights/MetricDefinitions/*|Metrikadefiníciók olvasása (egy erőforrás elérhető metrikatípusainak listája).|
|Microsoft.Insights/Metrics/*|Egy erőforrás metrikáinak olvasása.|
|Microsoft.Insights/Register/Action|Regisztrálja a Microsoft.Insights-szolgáltatót.|
|Microsoft.Insights/webtests/*|Az Application Insights webes tesztjeinek kezelése.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Az Azure Monitor naplóinak kezelése megoldáscsomagok.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Az Azure Monitor naplóinak kezelése a mentett kereséseken.|
|Microsoft.OperationalInsights/munkaterületek/keresés/művelet|Keresés a Naplóelemzési munkaterületeken.|
|Microsoft.OperationalInsights/munkaterületek/megosztottkulcsok/művelet|A Log Analytics-munkaterület kulcsainak listázása.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Az Azure Monitor naplóinak kezelése a tárolási betekintési konfigurációkban.|
|Microsoft.Támogatás/*|Támogatási jegyek létrehozása és kezelése.|
|Microsoft.WorkloadMonitor/workloads/*|Számítási feladatok kezelése.|

### <a name="monitoring-reader"></a>Monitoring olvasó

A figyelési olvasó képes olvasni az összes figyelési adatot. Az alábbi táblázat a szerepkörhöz megadott engedélyeket mutatja be:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/olvasás|A titkok kivételével minden típusú erőforrást elkell olvasni.|
|Microsoft.OperationalInsights/munkaterületek/keresés/művelet|Keresés a Naplóelemzési munkaterületeken.|
|Microsoft.Támogatás/*|Támogatási jegyek létrehozása és kezelése|

### <a name="user-access-administrator"></a>Felhasználói hozzáférés rendszergazdája

A felhasználói hozzáférés rendszergazdája kezelheti az Azure-erőforrásokhoz való felhasználói hozzáférést. Az alábbi táblázat a szerepkörhöz megadott engedélyeket mutatja be:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/olvasás|Az összes forrás elolvasása|
|Microsoft.Authorization/*|Engedélyezés kezelése|
|Microsoft.Támogatás/*|Támogatási jegyek létrehozása és kezelése|

## <a name="onboarding"></a>Előkészítés

Az alábbi táblázatok a változáskövetési vagy frissítéskezelési megoldásokhoz szükséges minimális engedélyeket mutatják be a virtuális gépek bevezetéséhez.

### <a name="onboarding-from-a-virtual-machine"></a>Bevezetés virtuális gépről

|**Művelet**  |**Engedély**  |**Minimális hatály**  |
|---------|---------|---------|
|Új központi telepítés írása      | Microsoft.Resources/deployments/*          |Előfizetés          |
|Új erőforráscsoport írása      | Microsoft.Resources/subscriptions/resourceGroups/write        | Előfizetés          |
|Új alapértelmezett munkaterület létrehozása      | Microsoft.OperationalInsights/munkaterületek/írás         | Erőforráscsoport         |
|Új fiók létrehozása      |  Microsoft.Automation/automationAccounts/write        |Erőforráscsoport         |
|Munkaterület és fiók csatolása      |Microsoft.OperationalInsights/munkaterületek/írás</br>Microsoft.Automation/automationAccounts/read|Munkaterület</br>Automation-fiók
|MMA-bővítmény létrehozása      | Microsoft.Compute/virtualMachines/write         | Virtuális gép         |
|Mentett keresés létrehozása      | Microsoft.OperationalInsights/munkaterületek/írás          | Munkaterület         |
|Hatókör-konfiguráció létrehozása      | Microsoft.OperationalInsights/munkaterületek/írás          | Munkaterület         |
|Bevezetési állapot ellenőrzése – Munkaterület olvasása      | Microsoft.OperationalInsights/workspaces/read         | Munkaterület         |
|Bevezetési állapot ellenőrzése – A fiók csatolt munkaterületi tulajdonságának olvasása     | Microsoft.Automation/automationAccounts/read      | Automation-fiók        |
|Bevezetési állapot-ellenőrzés - Olvassa el a megoldást      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Megoldás         |
|Bevezetési állapot ellenőrzése - Virtuális gép olvasása      | Microsoft.Compute/virtualMachines/read         | Virtuális gép         |
|Bevezetési állapot ellenőrzése - Olvasási fiók      | Microsoft.Automation/automationAccounts/read  |  Automation-fiók   |
| A virtuális gép<sup>1-es</sup> gépéna bevezetési munkaterület-ellenőrzés       | Microsoft.OperationalInsights/workspaces/read         | Előfizetés         |
| A Log Analytics-szolgáltató regisztrálása |Microsoft.Insights/register/action | Előfizetés|

<sup>1</sup> Ez az engedély szükséges a virtuális gép portálfelületén keresztül történő fedélzetre.

### <a name="onboarding-from-automation-account"></a>Bevezetés az Automation-fiókból

|**Művelet**  |**Engedély** |**Minimális hatókör**  |
|---------|---------|---------|
|Új telepítés létrehozása     | Microsoft.Resources/deployments/*        | Előfizetés         |
|Új erőforráscsoport létrehozása     | Microsoft.Resources/subscriptions/resourceGroups/write         | Előfizetés        |
|AutomationOnboarding panel – Új munkaterület létrehozása     |Microsoft.OperationalInsights/munkaterületek/írás           | Erőforráscsoport        |
|AutomationOnboarding panel - csatolt munkaterület olvasása     | Microsoft.Automation/automationAccounts/read        | Automation-fiók       |
|AutomationOnboarding penge - olvasási megoldás     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Megoldás        |
|AutomationOnboarding panel - munkaterület olvasása     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Munkaterület        |
|Hivatkozás létrehozása munkaterülethez és fiókhoz     | Microsoft.OperationalInsights/munkaterületek/írás        | Munkaterület        |
|A cipősdoboz számláinak írása      | Microsoft.Automation/automationAccounts/write        | Fiók        |
|Mentett keresés létrehozása/szerkesztése     | Microsoft.OperationalInsights/munkaterületek/írás        | Munkaterület        |
|Hatókör konfigurációjának létrehozása/szerkesztése     | Microsoft.OperationalInsights/munkaterületek/írás        | Munkaterület        |
| A Log Analytics-szolgáltató regisztrálása |Microsoft.Insights/register/action | Előfizetés|
|**2. lépés - Több virtuális gép beépített**     |         |         |
|VMOnboarding panel – MMA-bővítmény létrehozása     | Microsoft.Compute/virtualMachines/write           | Virtuális gép        |
|Mentett keresés létrehozása /szerkesztése     | Microsoft.OperationalInsights/munkaterületek/írás           | Munkaterület        |
|Hatókör konfigurációjának létrehozása/szerkesztése  | Microsoft.OperationalInsights/munkaterületek/írás   | Munkaterület|

## <a name="update-management"></a>Frissítéskezelés

A frissítéskezelés több szolgáltatáson keresztül is elérhető a szolgáltatás biztosítása érdekében. Az alábbi táblázat a frissítéskezelési telepítések kezeléséhez szükséges engedélyeket mutatja be:

|**Erőforrás**  |**Szerepkör**  |**Hatókör**  |
|---------|---------|---------|
|Automation-fiók     | Log Analytics közreműködő       | Automation-fiók        |
|Automation-fiók    | Virtuális gépek közreműködője        | A fiók erőforráscsoportja        |
|Log Analytics-munkaterület     | Log Analytics közreműködő| Log Analytics-munkaterület        |
|Log Analytics-munkaterület |Log Analytics olvasó| Előfizetés|
|Megoldás     |Log Analytics közreműködő         | Megoldás|
|Virtuális gép     | Virtuális gépek közreműködője        | Virtuális gép        |

## <a name="configure-rbac-for-your-automation-account"></a>Az RBAC konfigurálása az Automation-fiókhoz

A következő szakasz bemutatja, hogyan konfigurálhatja az RBAC-ot az Automation-fiókon a [portálon](#configure-rbac-using-the-azure-portal) és a [PowerShellen](#configure-rbac-using-powershell)keresztül.

### <a name="configure-rbac-using-the-azure-portal"></a>Az RBAC konfigurálása az Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és nyissa meg az Automation-fiókját az Automation-fiókok lapról.
2. Kattintson a **hozzáférés-vezérlés (IAM)** vezérlőre a bal felső sarokban a Hozzáférés-vezérlés (IAM) lap megnyitásához. Ezen a lapon új felhasználókat, csoportokat és alkalmazásokat vehet fel az Automation-fiók kezeléséhez és az Automation-fiókhoz konfigurálható meglévő szerepkörök megtekintéséhez.
3. Kattintson a **Szerepkör-hozzárendelések** fülre.

   ![Hozzáférés gomb](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Új felhasználó hozzáadása és szerepkör hozzárendelése

1. A Hozzáférés-vezérlés (IAM) lapon kattintson **a + Szerepkör-hozzárendelés hozzáadása gombra.** Ez a művelet megnyitja a Szerepkör-hozzárendelés hozzáadása lapot, ahol hozzáadhat egy felhasználót, csoportot vagy alkalmazást, és hozzárendelhet egy megfelelő szerepkört.

2. Válasszon egy szerepkört az elérhető szerepkörök listájáról. Az Automation-fiók által támogatott rendelkezésre álló beépített szerepkörök bármelyikét kiválaszthatja, vagy bármely egyéni szerepkört, amelyet definiált.

3. Írja be annak a felhasználónak a felhasználónevét, amelyiknek engedélyeket szeretne adni a **Kijelölés** mezőbe. Válassza ki a felhasználót a listából, és kattintson a **Mentés gombra.**

   ![Felhasználók hozzáadása](media/automation-role-based-access-control/automation-04-add-users.png)

   Most látnia kell a felhasználót a Felhasználók laphoz, a kijelölt szerepkör hozzárendelésével.

   ![Felhasználók listázása](media/automation-role-based-access-control/automation-05-list-users.png)

   A felhasználóhoz a Szerepkörök lapról is hozzárendelhet szerepkört.
4. A Szerepkörök lap megnyitásához kattintson a Hozzáférés-vezérlés (IAM) lapon a Szerepkörök lapon a **Szerepkörök** (IAM) elemre. Megtekintheti a szerepkör nevét, valamint a szerepkörhöz rendelt felhasználók és csoportok számát.

    ![Szerepkör hozzárendelése a Felhasználók lapról](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Csak az Automation-fiók hatókörén állíthatja be a szerepköralapú hozzáférés-vezérlést, és az Automation-fiók alatt egyetlen erőforrásnál sem.

#### <a name="remove-a-user"></a>Felhasználó eltávolítása

Eltávolíthatja egy olyan felhasználó hozzáférési engedélyét, aki nem kezeli az Automation-fiókot, vagy aki már nem dolgozik a szervezetnél. Alább láthatók egy felhasználó eltávolításának lépései:

1. A Hozzáférés-vezérlés (IAM) lapon jelölje ki az eltávolítani kívánt felhasználót, és kattintson az **Eltávolítás gombra.**
2. A hozzárendelés részleteit megjelenítő lapon kattintson az **Eltávolítás** gombra.
3. Az **Igen** gombra kattintva erősítse meg az eltávolítást.

   ![Felhasználók eltávolítása](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Az RBAC konfigurálása a PowerShell használatával

Szerepköralapú hozzáférést is konfigurálhat egy Automation-fiókhoz a következő [Azure PowerShell-parancsmagok](../role-based-access-control/role-assignments-powershell.md)használatával:

[Get-AzureRmRoleDefinition](/previous-versions/azure/mt603792(v=azure.100)) felsorolja az Azure Active Directoryban elérhető összes RBAC-szerepköröket. Ezt a parancsmalapot a *Name* paraméterrel együtt használhatja az adott szerepkör által végrehajtható összes művelet listázásához.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

A következő a példa kimenet:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzureRmRoleAssignment](/previous-versions/azure/mt619413(v=azure.100)) az Azure AD RBAC szerepkör-hozzárendelések a megadott hatókörön. Paraméterek nélkül ez a parancsmag az előfizetés alatt végrehajtott összes szerepkör-hozzárendelést adja vissza. Az *ExpandPrincipalGroups* paraméterrel listázza a megadott felhasználó hozzáférési hozzárendeléseit, valamint azokat a csoportokat, amelyekhez a felhasználó tartozik.

**Példa:** A következő parancsmag segítségével sorolja fel az összes felhasználót és azok szerepköreit egy Automation-fiókban.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A következő a példa kimenet:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

[A New-AzureRmRoleAssignment](/previous-versions/azure/mt603580(v=azure.100)) használatával hozzáférést rendelhet a felhasználókhoz, csoportokhoz és alkalmazásokhoz egy adott hatókörhöz.
    
**Példa:** A következő paranccsal rendelheti hozzá az "Automation Operator" szerepkört egy felhasználóhoz az Automation-fiók hatókörében.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A következő a példa kimenet:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

[Az Eltávolítás-AzureRmRoleAssignment](/previous-versions/azure/mt603781(v=azure.100)) használatával eltávolíthatja egy adott felhasználó, csoport vagy alkalmazás hozzáférését egy adott hatókörből.

**Példa:** A következő paranccsal eltávolíthatja a felhasználót az Automation-fiók hatókörének "Automation Operator" szerepköréből.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Az előző példákban cserélje le az eltávolítani kívánt felhasználó bejelentkezési azonosítóját,"SubscriptionID", "Erőforráscsoport neve" és Automation-fiók neve" a fiók adataival. Válassza **az igen** lehetőséget, amikor a rendszer a felhasználói szerepkör-hozzárendelések eltávolításának folytatása előtt megerősítést kér.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Felhasználói élmény az Automation operátori szerepkörhöz – Automatizálási fiók

Ha az Automation-fiók hatókörének Automation Operator szerepköréhez rendelt felhasználó megtekinti azt az Automation-fiókot, amelyhez hozzá van rendelve, a felhasználó csak az Automation-fiókban létrehozott runbookok, runbook-feladatok és ütemezések listáját tekintheti meg. Ez a felhasználó nem tekintheti meg ezeknek az elemeknek a definícióit. A felhasználó elindíthatja, leállíthatja, felfüggesztheti, folytathatja vagy ütemezheti a runbook-feladatot. A felhasználó azonban nem fér hozzá más Automation-erőforrásokhoz, például konfigurációkhoz, hibrid munkavégző csoportokhoz vagy DSC-csomópontokhoz.

![Nincs hozzáférés az erőforrásokhoz](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>RBAC konfigurálása runbookokhoz

Az Azure Automation lehetővé teszi, hogy rBAC-t rendeljen adott runbookokhoz. Ehhez futtassa a következő parancsfájlt, hogy egy felhasználót vegyen fel egy adott runbookhoz. Az automation-fiók rendszergazdája vagy egy bérlői rendszergazda futtathatja ezt a parancsfájlt.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Miután a parancsfájl futott, a felhasználó jelentkezzen be az Azure Portalon, és tekintse meg az **összes erőforrást.** A listában a felhasználó láthatja a runbookot, amelyhez automation Runbook operátorként lett hozzáadva.

![Runbook RBAC a portálon](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Felhasználói élmény az Automation operátori szerepkörhöz - Runbook

Ha a Runbook-hatókör Automation Operator szerepköréhez rendelt felhasználó egy hozzárendelt runbookot tekint meg, a felhasználó csak a runbookot és a Runbook-feladatokat tudja megtekinteni.

![Csak az indításhoz van hozzáférése](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>További lépések

* Az RBAC Azure Automation-hez való konfigurálásának módjairól az [RBAC kezelése az Azure PowerShell használatával](../role-based-access-control/role-assignments-powershell.md)című dokumentumban talál.
* A runbookok indításának módjairól a [Runbook indítása című témakörben](automation-starting-a-runbook.md)talál.
* A runbook-típusokról az [Azure Automation runbook-típusai](automation-runbook-types.md)című dokumentumban tájékozódhat.