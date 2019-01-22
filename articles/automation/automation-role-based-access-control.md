---
title: Szerepköralapú hozzáférés-vezérlés az Azure Automationben
description: A Szerepköralapú hozzáférés-vezérlés (RBAC) hozzáférés-vezérlést biztosít az Azure-erőforrásokhoz. Ez a cikk ismerteti az RBAC beállítását az Azure Automationben.
keywords: automation rbac, szerepköralapú hozzáférés-vezérlés, azure rbac
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4fb90dbdb02fc0a0448b8cb6723c980c0fe41bd6
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424319"
---
# <a name="role-based-access-control-in-azure-automation"></a>Szerepköralapú hozzáférés-vezérlés az Azure Automationben

A Szerepköralapú hozzáférés-vezérlés (RBAC) hozzáférés-vezérlést biztosít az Azure-erőforrásokhoz. Használatával [RBAC](../role-based-access-control/overview.md), feladatköröket a csapaton belül, és csak olyan mértékű hozzáférést biztosítson a felhasználók, csoportok, és az alkalmazásokat, amelyek szükségük van a feladataik elvégzéséhez. A szerepköralapú hozzáférés az Azure Portal, az Azure parancssori segédeszközök vagy az Azure felügyeleti API-k segítségével adható a felhasználóknak.

## <a name="roles-in-automation-accounts"></a>Szerepkörök az Automation-fiókok

Az Azure Automationben a hozzáférés biztosításához a megfelelő RBAC-szerepkörök rendelhetők az Automation-fiók tartományában lévő felhasználókhoz, csoportokhoz és alkalmazásokhoz. Alább láthatók az Automation-fiók által támogatott beépített szerepkörök:

| **Szerepkör** | **Leírás** |
|:--- |:--- |
| Tulajdonos |A tulajdonosi szerepkör segítségével hozzáférhet minden erőforráshoz és művelethez egy Automation-fiókot, például, hogy hozzáférést biztosíthat más felhasználók, csoportok és alkalmazások számára az Automation-fiók kezeléséhez belül. |
| Közreműködő |A közreműködői szerepkör segítségével minden elemet kezelhet, csak más felhasználók Automation-fiókokra vonatkozó hozzáférési jogosultságait nem módosíthatja. |
| Olvasó |Az olvasói szerep lehetővé teszi az összes erőforrás megtekintését egy Automation-fiókban, de módosítás nem hajtható vele végre. |
| Automation-operátor |Az Automation-operátori szerepkör lehetővé teszi, hogy a runbook neve és a Tulajdonságok megtekintése, valamint hogy az Automation-fiókot az összes runbook feladatok létrehozása és kezelése. Ez a szerepkör akkor hasznos, ha meg szeretné akadályozni, hogy az Automation-fiók erőforrásokat, például a hitelesítő adategységeket és a forgatókönyveket megtekintsék vagy módosítsák, de továbbra is engedélyezi a szervezet tagjai számára a forgatókönyvek végrehajtását. |
|Az Automation-feladat operátora|Az Automation-feladat operátor szerepkör lehetővé teszi, hogy az Automation-fiókot az összes runbook feladatok létrehozása és kezelése.|
|Az Automation-runbook operátora|A Runbook Automation-operátori szerepkör lehetővé teszi, hogy a runbook nevét és a tulajdonságok megtekintéséhez.|
| Log Analytics közreműködő | A Log Analytics-közreműködő szerepkör lehetővé teszi, hogy az összes monitorozási adat olvasása és szerkesztése a figyelési beállításokat. Figyelési beállítások szerkesztése magában foglalja a virtuális gépek, tudják naplógyűjtemény konfigurálása céljából az Azure storage-ból tárfiók kulcsainak beolvasását az létrehozása és konfigurálása az Automation-fiókok, megoldások hozzáadása és Azure diagnostics konfigurálása az ad hozzá a Virtuálisgép-bővítmény az összes Azure-erőforrások.|
| Log Analytics olvasó | A Log Analytics olvasó szerepkör lehetővé teszi, hogy megtekintése és keresése az összes monitorozási adat, valamint megtekintheti a figyelési beállításokat. Ez magában foglalja az Azure diagnostics konfigurációjának megtekintését az összes Azure-erőforrások. |
| Figyelési közreműködő | A figyelés közreműködői szerepkör lehetővé teszi, hogy olvassa el az összes olyan figyelési adathoz és figyelési beállításainak frissítése.|
| Figyelési olvasó | A Monitoring Reader szerepkör lehetővé teszi, hogy az összes monitorozási adat olvasása. |
| Felhasználói hozzáférés rendszergazdája |A felhasználói hozzáférés rendszergazdájának szerepköre lehetővé teszi, hogy kezelje a felhasználói hozzáférést az Azure Automation-fiókokhoz. |

## <a name="role-permissions"></a>Szerepkör-engedélyek

Az alábbi táblázatok ismertetik az egyes szerepkörökhöz meghatározott engedélyeket. Ez magában foglalhatja műveletek, amelyek az engedélyeket, és NotActions, amelyek korlátozzák a őket.

### <a name="owner"></a>Tulajdonos

Egy olyan tulajdonost mindent felügyelhetnek, beleértve a hozzáférést. Az alábbi táblázat a szerepkörhöz rendelt engedélyeket:

|Műveletek|Leírás|
|---|---|
|Microsoft.Automation/automationAccounts/|Erőforrások létrehozásához és kezeléséhez minden típusának.|

### <a name="contributor"></a>Közreműködő

Közreműködői hozzáférés kivételével mindent felügyelhetnek. Az alábbi táblázat az engedélyeket kap, és a szerepkör nem kap:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Bármilyen típusú erőforrások létrehozása és felügyelete|
|**Nincs művelet**||
|Microsoft.Authorization/*/Delete| Törölje a szerepkörök és szerepkör-hozzárendeléseket.       |
|Microsoft.Authorization/*/Write     |  Hozzon létre a szerepkörök és szerepkör-hozzárendeléseket.       |
|Microsoft.Authorization/elevateAccess/Action    | Képes létrehozni a felhasználói hozzáférés rendszergazdája megtagadja.       |

### <a name="reader"></a>Olvasó

Egy olvasó is összes erőforrás megtekintését egy Automation-fiókban, de nem módosíthatja.

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Az összes erőforrás megtekintését egy Automation-fiókot. |

### <a name="automation-operator"></a>Automation-operátor

Egy Automation-operátor is képes létrehozni és feladatok kezelése és olvassa el a runbook nevét és az összes runbook Automation-fiók tulajdonságait.  Megjegyzés: Ha operátorral férhet hozzá az egyes runbookok majd ne állítsa be ezt a szerepkört, ezért használja inkább az "Automation-feladat operátor" és "Automation-Runbook operátora" szerepkörök együtt. Az alábbi táblázat a szerepkörhöz rendelt engedélyeket:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft.Authorization/*/read|Olvasási engedély.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Hibrid forgatókönyv-feldolgozó erőforrások olvasása.|
|Microsoft.Automation/automationAccounts/jobs/read|A runbook feladatok listázása.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Felfüggesztett feladat folytatása.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Folyamatban lévő feladatok megszakítása.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Olvassa el a Feladatstreamek és a kimenetet.|
|Microsoft.Automation/automationAccounts/jobs/output/read|A feladat kimenetének beolvasása.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Egy folyamatban lévő feladat felfüggesztése.|
|Microsoft.Automation/automationAccounts/jobs/write|Hozzon létre feladatokat.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Azure Automation-feladatütemezés beolvasása.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Hozzon létre egy Azure Automation-feladatütemezés.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Az automation-fiókhoz társítva, a munkaterület lekérése.|
|Microsoft.Automation/automationAccounts/read|Azure Automation-fiók beolvasása.|
|Microsoft.Automation/automationAccounts/runbooks/read|Azure Automation-runbook beolvasása.|
|Microsoft.Automation/automationAccounts/schedules/read|Egy Azure Automation szolgáltatásbeli ütemezési eszköz beolvasása.|
|Microsoft.Automation/automationAccounts/schedules/write|Hozzon létre, vagy egy Azure Automation szolgáltatásbeli ütemezési eszköz frissítése.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Olvassa el a szerepköröket és szerepkör-hozzárendeléseket.         |
|Microsoft.Resources/deployments/*      |Létrehozhat és kezelhet erőforráscsoportok üzemelő példányainak.         |
|Microsoft.Insights/alertRules/*      | Hozzon létre, és a riasztási szabályok kezelése.        |
|Microsoft.Support/* |Hozzon létre, és kezelhetik a támogatási jegyeket.|

### <a name="automation-job-operator"></a>Az Automation-feladat operátora

Egy Automation-feladat operátor szerepkör kapnak, az Automation-fiók hatókörében. Ez lehetővé teszi az üzemeltető engedélyekkel a fiókban lévő összes runbook feladatok létrehozása és kezelése. Az alábbi táblázat a szerepkörhöz rendelt engedélyeket:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft.Authorization/*/read|Olvasási engedély.|
|Microsoft.Automation/automationAccounts/jobs/read|A runbook feladatok listázása.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Felfüggesztett feladat folytatása.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Folyamatban lévő feladatok megszakítása.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Olvassa el a Feladatstreamek és a kimenetet.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Egy folyamatban lévő feladat felfüggesztése.|
|Microsoft.Automation/automationAccounts/jobs/write|Hozzon létre feladatokat.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Olvassa el a szerepköröket és szerepkör-hozzárendeléseket.       |
|Microsoft.Resources/deployments/*      |Létrehozhat és kezelhet erőforráscsoportok üzemelő példányainak.         |
|Microsoft.Insights/alertRules/*      | Hozzon létre, és a riasztási szabályok kezelése.        |
|Microsoft.Support/* |Hozzon létre, és kezelhetik a támogatási jegyeket.|

### <a name="automation-runbook-operator"></a>Az Automation-runbook operátora

Egy Automation-Runbook operátora szerepkör kap a Runbook-hatókörben. A runbook neve és tulajdonságai egy Automation-Runbook operátora tekintheti meg.  Ezt a szerepkört az "Automation-feladat operátor" szerepkör kombinálva lehetővé teszi, hogy az operátor számára is létrehozhat és kezelhet a runbook feladatokat. Az alábbi táblázat a szerepkörhöz rendelt engedélyeket:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | A runbookok listája.        |
|Microsoft.Authorization/*/read      | Olvasási engedély.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Olvassa el a szerepköröket és szerepkör-hozzárendeléseket.         |
|Microsoft.Resources/deployments/*      | Létrehozhat és kezelhet erőforráscsoportok üzemelő példányainak.         |
|Microsoft.Insights/alertRules/*      | Hozzon létre, és a riasztási szabályok kezelése.        |
|Microsoft.Support/*      | Hozzon létre, és kezelhetik a támogatási jegyeket.        |

### <a name="log-analytics-contributor"></a>Log Analytics közreműködő

A Log Analytics-közreműködő az összes monitorozási adat olvashatja és szerkesztheti a figyelési beállításokat. Figyelési beállítások szerkesztése magában foglalja a virtuális gépek; a Virtuálisgép-bővítmény hozzáadása tudni naplógyűjtemény konfigurálása céljából az Azure Storage; tárfiók kulcsainak beolvasását létrehozásáról és konfigurálásáról az Automation-fiókok; megoldások hozzáadását; és az Azure diagnostics konfigurálása az összes Azure-erőforrást. Az alábbi táblázat a szerepkörhöz rendelt engedélyeket:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/read|Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat.|
|Microsoft.Automation/automationAccounts/*|Az automation-fiókok kezelése.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Létrehozhat és kezelhet virtuálisgép-bővítmények.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Klasszikus tárfiók kulcsainak listázása.|
|Microsoft.Compute/virtualMachines/extensions/*|Létrehozása és kezelése a klasszikus virtuális gépi bővítmények.|
|Microsoft.Insights/alertRules/*|Olvasási, írási és törlési riasztási szabályok.|
|Microsoft.Insights/diagnosticSettings/*|Diagnosztikai beállítások olvasása/írása/törlése.|
|Microsoft.OperationalInsights/*|A Log Analytics felügyelete.|
|Microsoft.OperationsManagement/*|A munkaterületeken megoldások kezelése.|
|Microsoft.Resources/deployments/*|Létrehozhat és kezelhet erőforráscsoportok üzemelő példányainak.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Létrehozhat és kezelhet erőforráscsoportok üzemelő példányainak.|
|Microsoft.Storage/storageAccounts/listKeys/action|Tárfiókkulcsok listája.|
|Microsoft.Support/*|Hozzon létre, és kezelhetik a támogatási jegyeket.|

### <a name="log-analytics-reader"></a>Log Analytics olvasó

A Log Analytics olvasó megtekintheti és keresheti az összes figyelési adatot, valamint megtekintheti a figyelési beállításokat, beleértve az Azure diagnostics konfigurációjának megtekintését az összes Azure-erőforrások. Az alábbi táblázat az engedélyeket adott meg vagy Tagadott a szerepkörhöz:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/read|Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|A Log Analytics lekérdezések kezelése.|
|Microsoft.OperationalInsights/workspaces/search/action|Keressen rá a Log Analytics-adatok.|
|Microsoft.Support/*|Hozzon létre, és kezelhetik a támogatási jegyeket.|
|**Nincs művelet**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Nem sikerült beolvasni a megosztott hozzáférési kulcsokat.|

### <a name="monitoring-contributor"></a>Figyelési közreműködő

Közreműködő figyelése olvashatja az összes monitorozási adat, és frissítheti a figyelési beállításokat. Az alábbi táblázat a szerepkörhöz rendelt engedélyeket:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/read|Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat.|
|Microsoft.AlertsManagement/alerts/*|Riasztások kezelése.|
|Microsoft.AlertsManagement/alertsSummary/*|A riasztási irányítópult kezelése.|
|Microsoft.Insights/AlertRules/*|Riasztási szabályok kezelése.|
|Microsoft.Insights/components/*|Az Application Insights-összetevők kezeléséhez.|
|Microsoft.Insights/DiagnosticSettings/*|Diagnosztikai beállítások kezelése.|
|Microsoft.Insights/eventtypes/*|Tevékenységnapló eseményei (felügyeleti események) egy adott előfizetés listázása. Ezzel az engedéllyel csak a tevékenységnaplóban a szoftveres és a portál hozzáférést alkalmazható.|
|Microsoft.Insights/LogDefinitions/*|Erre az engedélyre szükség a felhasználók számára, akik hozzáférhetnek a vizsgálati naplók a portálon keresztül. Lista naplókategóriák a tevékenységnaplóban.|
|Microsoft.Insights/MetricDefinitions/*|(Erőforrás rendelkezésre álló metrika típusok listája) metrikadefiníciók olvasása.|
|Microsoft.Insights/Metrics/*|Olvassa el a erőforrás metrikáit.|
|Microsoft.Insights/Register/Action|Regisztrálja a Microsoft.Insights szolgáltató.|
|Microsoft.Insights/webtests/*|Az Application Insights-webtesztek kezelése.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|A Log Analytics megoldás-csomagok kezelése.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Log Analytics-beli mentett keresések kezelése.|
|Microsoft.OperationalInsights/workspaces/search/action|Keressen rá a Log Analytics-munkaterületeket.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Log Analytics-munkaterület kulcsainak listázása.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|A Log Analytics storage insight-konfigurációk kezelése.|
|Microsoft.Support/*|Hozzon létre, és kezelhetik a támogatási jegyeket.|
|Microsoft.WorkloadMonitor/workloads/*|Feladatok felügyeletét.|

### <a name="monitoring-reader"></a>Figyelési olvasó

A Monitoring Reader olvashatja az összes figyelési adatot. Az alábbi táblázat a szerepkörhöz rendelt engedélyeket:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/read|Olvassa el a titkos kulcsok kivételével az összes típusú erőforrásokat.|
|Microsoft.OperationalInsights/workspaces/search/action|Keressen rá a Log Analytics-munkaterületeket.|
|Microsoft.Support/*|Hozzon létre, és a támogatási jegyek kezelése|

### <a name="user-access-administrator"></a>Felhasználói hozzáférés rendszergazdája

A felhasználói hozzáférés rendszergazdája Azure-erőforrások felhasználói hozzáférését kezelheti. Az alábbi táblázat a szerepkörhöz rendelt engedélyeket:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/read|Minden erőforrás olvasása|
|Microsoft.Authorization/*|Hitelesítés|
|Microsoft.Support/*|Hozzon létre, és a támogatási jegyek kezelése|

## <a name="onboarding"></a>Előkészítés

Az alábbi táblázatok bemutatják a virtuális gépek előkészítése a Change tracking szükséges minimális szükséges engedélyekkel, vagy update management megoldást.

### <a name="onboarding-from-a-virtual-machine"></a>Bevezetés a virtuális gépről

|**Művelet**  |**Engedély**  |**Minimális hatókör**  |
|---------|---------|---------|
|Új központi telepítés írása      | Microsoft.Resources/deployments/*          |Előfizetés          |
|Új erőforráscsoport írása      | Microsoft.Resources/subscriptions/resourceGroups/write        | Előfizetés          |
|Új alapértelmezett munkaterület létrehozása      | Microsoft.OperationalInsights/workspaces/write         | Erőforráscsoport         |
|Új fiók létrehozása      |  Microsoft.Automation/automationAccounts/write        |Erőforráscsoport         |
|Hivatkozás munkaterületet és fiókot      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Munkaterület</br>Automation-fiók
|Megoldás létrehozása      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Erőforráscsoport          |
|Az MMA-bővítmény létrehozása      | Microsoft.Compute/virtualMachines/write         | Virtuális gép         |
|Mentett keresés létrehozása      | Microsoft.OperationalInsights/workspaces/write          | Munkaterület         |
|Hatókör-konfiguráció létrehozása      | Microsoft.OperationalInsights/workspaces/write          | Munkaterület         |
|Hatókör-konfigurációs hivatkozás megoldás      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Megoldás         |
|Regisztrációs állapot ellenőrzése – olvasási munkaterület      | Microsoft.OperationalInsights/workspaces/read         | Munkaterület         |
|Regisztrációs állapot ellenőrzése – olvasás csatolt munkaterület tulajdonság-fiók     | Microsoft.Automation/automationAccounts/read      | Automation-fiók        |
|Regisztrációs állapot ellenőrzése – olvasás megoldás      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Megoldás         |
|Regisztrációs állapot ellenőrzése – olvasás VM      | Microsoft.Compute/virtualMachines/read         | Virtuális gép         |
|Regisztrációs állapot ellenőrzése – olvasható fiók      | Microsoft.Automation/automationAccounts/read  |  Automation-fiók   |

### <a name="onboarding-from-automation-account"></a>Előkészítés Automation-fiók

|**Művelet**  |**Engedély** |**Minimális hatókör**  |
|---------|---------|---------|
|Új üzemelő példány létrehozása     | Microsoft.Resources/deployments/*        | Előfizetés         |
|Új erőforráscsoport létrehozása     | Microsoft.Resources/subscriptions/resourceGroups/write         | Előfizetés        |
|AutomationOnboarding panel – Új munkaterület létrehozása     |Microsoft.OperationalInsights/workspaces/write           | Erőforráscsoport        |
|AutomationOnboarding panel – olvassa el a csatolt munkaterületet     | Microsoft.Automation/automationAccounts/read        | Automation-fiók       |
|AutomationOnboarding panel – olvassa el a megoldás     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Megoldás        |
|AutomationOnboarding panel – olvassa el a munkaterületet     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Munkaterület        |
|Hivatkozás munkaterület és fiók létrehozása     | Microsoft.OperationalInsights/workspaces/write        | Munkaterület        |
|Fiók shoebox írása      | Microsoft.Automation/automationAccounts/write        | Fiók        |
|Megoldás létrehozása      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Erőforráscsoport         |
|Hozzon létre vagy mentett keresés szerkesztése     | Microsoft.OperationalInsights/workspaces/write        | Munkaterület        |
|Hatókör-konfiguráció létrehozása/szerkesztése     | Microsoft.OperationalInsights/workspaces/write        | Munkaterület        |
|Hatókör-konfigurációs hivatkozás megoldás      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Megoldás         |
|**2. lépés – bevezetése több virtuális gép**     |         |         |
|VMOnboarding panel – MMA létrehozása bővítmény     | Microsoft.Compute/virtualMachines/write           | Virtuális gép        |
|Létrehozása és a mentett keresés szerkesztése     | Microsoft.OperationalInsights/workspaces/write           | Munkaterület        |
|Létrehozása és hatókör-konfiguráció szerkesztése  | Microsoft.OperationalInsights/workspaces/write   | Munkaterület|

## <a name="update-management"></a>Frissítéskezelés

Az Update management eléri a szolgáltatásokat több szolgáltatás. Az alábbi táblázat az update management központi telepítések felügyeletéhez szükséges engedélyeket:

|**Erőforrás**  |**Szerepkör**  |**Hatókör**  |
|---------|---------|---------|
|Automation-fiók     | Log Analytics közreműködő       | Automation-fiók        |
|Automation-fiók    | Virtuális gépek közreműködője        | A fiókhoz tartozó erőforráscsoportot        |
|Log Analytics-munkaterület     | Log Analytics közreműködő| Log Analytics-munkaterület        |
|Log Analytics-munkaterület |Log Analytics olvasó| Előfizetés|
|Megoldás     |Log Analytics közreműködő         | Megoldás|
|Virtuális gép     | Virtuális gépek közreműködője        | Virtuális gép        |

## <a name="configure-rbac-for-your-automation-account"></a>Az Automation-fiókhoz tartozó RBAC konfigurálása

A következő szakasz bemutatja, hogyan RBAC konfigurálása az Automation-fiók révén a [portál](#configure-rbac-using-the-azure-portal) és [PowerShell](#configure-rbac-using-powershell)

### <a name="configure-rbac-using-the-azure-portal"></a>Az Azure portal használatával RBAC konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és nyissa meg az Automation-fiókját az Automation-fiókok lapról.
2. Kattintson a **hozzáférés-vezérlés (IAM)** vezérlőelem bal felső sarokban található. Ekkor megnyílik a **hozzáférés-vezérlés (IAM)** oldal, ahol hozzáadhat új felhasználókat, csoportokat, és az alkalmazások kezelheti az Automation-fiók, és megtekintheti a meglévő szerepköröket, amelyek konfigurálhatók az Automation-fiókot.
3. Kattintson a **szerepkör-hozzárendelések** fülre.

   ![Hozzáférés gomb](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Új felhasználó hozzáadása és szerepkör hozzárendelése

1. Az a **hozzáférés-vezérlés (IAM)** kattintson **+ szerepkör-hozzárendelés hozzáadása** megnyitásához a **szerepkör-hozzárendelés hozzáadása** oldal, ahol hozzáadhat egy felhasználó, csoport vagy alkalmazás, és szerepkör hozzárendelése hozzájuk.

2. Válasszon egy szerepkört az elérhető szerepkörök listájáról. Kiválaszthatja, hogy bármelyik elérhető szerepkört, amely támogatja az Automation-fiók, illetve bármely egyéni szerepkört, amelyet korábban definiált.

3. Írja be az engedélyt a kívánt felhasználó felhasználóneve a **kiválasztása** mező. Válassza ki a felhasználót a listából, és kattintson a **mentése**.

   ![Felhasználók hozzáadása](media/automation-role-based-access-control/automation-04-add-users.png)

   Most meg kell jelennie a hozzáadott felhasználónak a **felhasználók** , a kiválasztott szerepkörhöz hozzárendelt lap

   ![Felhasználók listázása](media/automation-role-based-access-control/automation-05-list-users.png)

   A felhasználóhoz a **Szerepkörök** lapról is hozzárendelhet szerepkört.
4. Kattintson a **szerepkörök** származó a **hozzáférés-vezérlés (IAM)** megnyitásához a **szerepkörök** lapot. Itt megtekintheti a szerepkör nevét, és az adott szerepkörhöz rendelt felhasználók és csoportok számát.

    ![Szerepkör hozzárendelése a Felhasználók lapról](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Szerepköralapú hozzáférés-vezérlés csak az Automation-fiók tartományában, és a az Automation-fiók alatti erőforrásoknál nem állítható be.

#### <a name="remove-a-user"></a>Felhasználó eltávolítása

Egy felhasználó, aki nem kezel az Automation-fiókot, vagy akik már nem működik a szervezet számára a hozzáférési engedély távolíthatja el. Alább láthatók egy felhasználó eltávolításának lépései:

1. Az a **hozzáférés-vezérlés (IAM)** lapra, jelölje be a távolítsa el, és kattintson a felhasználói kívánja **eltávolítása**.
2. A hozzárendelés részleteit megjelenítő lapon kattintson az **Eltávolítás** gombra.
3. Az **Igen** gombra kattintva erősítse meg az eltávolítást.

   ![Felhasználók eltávolítása](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>PowerShell-lel RBAC konfigurálása

Szerepköralapú hozzáférés is konfigurálható egy Automation-fiókot az alábbi [Azure PowerShell-parancsmagok](../role-based-access-control/role-assignments-powershell.md):

[Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) sorolja fel az Azure Active Directoryban elérhető összes RBAC-szerepkört. Ezt a parancsot használhatja a **Name** (Név) tulajdonsággal, ha fel szeretné sorolni az összes műveletet, amelyet egy adott szerepkörrel lehet elvégezni.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

A következő egy példa a kimenetre:

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

[Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) sorolja fel az Azure AD RBAC szerepkör-hozzárendelések a megadott hatókörben. Paraméterek nélkül ez a parancs az előfizetéshez tartozó összes szerepkör-hozzárendelést visszaadja. Az **ExpandPrincipalGroups** paraméter segítségével felsorolhatja egy adott felhasználó vagy a felhasználó csoportjának összes hozzáférés-hozzárendelését.
    **Példa** A következő paranccsal listázhatja az összes felhasználót és azok szerepkörét egy automation-fiókot.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A következő egy példa a kimenetre:

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

[Új-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) hozzáférés hozzárendelése a felhasználók, csoportok és alkalmazások egy adott hatókörhöz.
    **Példa** A következő paranccsal rendelje hozzá a "Automation-operátor" szerepkört az Automation-fiók tartományában lévő felhasználó számára.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A következő egy példa a kimenetre:

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

Használat [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) segítségével távolítsa el a megadott felhasználó, csoport vagy alkalmazás hozzáférését egy adott hatókörből.
    **Példa** A következő paranccsal eltávolítja a felhasználót az Automation-fiók tartományában lévő "Automation-operátor" szerepkör.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A fenti példákban cserélje le a **bejelentkezési azonosító**, **előfizetés-azonosítója**, **erőforráscsoport-név**, és **Automation-fiók neve** -a fiók adatai. Ha a rendszer rákérdez, hogy tényleg szeretné-e törölni a szerepkör-hozzárendelést, kattintson az **Igen** gombra.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Felhasználói élmény az Automation-operátori szerepkör – Automation-fiók

Ha egy felhasználó, aki hozzá van rendelve az Automation-operátori szerepkör az Automation-fiók hatókörében vannak rendelve az Automation-fiók, akkor csak megtekintheti a forgatókönyvek, forgatókönyv-feladatok listájának megtekintése és ütemezések létrehozása az Automation-fiók, de nem tekintheti meg a definíció. Elindíthatják, leállíthatják, felfüggeszthetik, folytathatják és ütemezhetik a runbook-feladatokat. A felhasználó nem rendelkezik például konfigurációk, hibrid munkacsoportokhoz vagy DSC-csomópontok Automation-erőforrásokhoz való hozzáférést.

![Erőforrásokhoz sem fér hozzá](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Runbookok tartozó RBAC konfigurálása

Az Azure Automation lehetővé teszi, hogy bizonyos runbookokra RBAC hozzárendelése. Ehhez futtassa a következő szkriptet a felhasználó hozzáadása egy adott runbookhoz. Az alábbi parancsfájlt is futtatni egy Automation-fiók rendszergazdai vagy bérlői rendszergazdával.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation Account
$automationAccountName ="<Automation Account Name>" # Name of the Automation Account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation Account resource
$aa = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be ran once per user.
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Miután futtatta, a felhasználót, jelentkezzen be az Azure portal és a nézet **összes erőforrás**. A listában megjelenik a Runbook addig adták hozzá, mint egy **Automation-Runbook operátora** számára.

![Runbook RBAC a portálon](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Felhasználói élmény az Automation-operátori szerepkör - Runbook

Ha egy felhasználó, aki a Runbook nézetek hatókörének meghatározásához vannak rendelve egy Runbook az Automation-operátori szerepkör van hozzárendelve, akkor csak a runbook elindításához és a runbook-feladatok megtekintése.

![Csak a start hozzáféréssel rendelkezik](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>További lépések

* Az RBAC for Azure Automation konfigurálásának különböző módjaira vonatkozó további információért lásd [az RBAC Azure PowerShellel folytatott kezelésével](../role-based-access-control/role-assignments-powershell.md) foglalkozó témakört.
* További információk a forgatókönyvek elindításának különböző módjairól: [Forgatókönyv elindítása](automation-starting-a-runbook.md).
* További információk a különböző forgatókönyv-típusokról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)

