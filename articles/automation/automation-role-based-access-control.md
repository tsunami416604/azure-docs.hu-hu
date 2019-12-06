---
title: Szerepköralapú hozzáférés-vezérlés az Azure Automationben
description: A Szerepköralapú hozzáférés-vezérlés (RBAC) hozzáférés-vezérlést biztosít az Azure-erőforrásokhoz. Ez a cikk ismerteti az RBAC beállítását az Azure Automationben.
keywords: automation rbac, szerepköralapú hozzáférés-vezérlés, azure rbac
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0ee524768f46de965b1755f2cfffdf9e2034bec8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850788"
---
# <a name="role-based-access-control-in-azure-automation"></a>Szerepköralapú hozzáférés-vezérlés az Azure Automationben

A Szerepköralapú hozzáférés-vezérlés (RBAC) hozzáférés-vezérlést biztosít az Azure-erőforrásokhoz. A [RBAC](../role-based-access-control/overview.md)segítségével elkülönítheti a feladatait a csapaton belül, és csak a felhasználókhoz, csoportokhoz és alkalmazásokhoz férhet hozzá, amelyekre szükségük van a feladatok elvégzéséhez. A szerepköralapú hozzáférés az Azure Portal, az Azure parancssori segédeszközök vagy az Azure felügyeleti API-k segítségével adható a felhasználóknak.

## <a name="roles-in-automation-accounts"></a>Az Automation-fiókok szerepkörei

Az Azure Automationben a hozzáférés biztosításához a megfelelő RBAC-szerepkörök rendelhetők az Automation-fiók tartományában lévő felhasználókhoz, csoportokhoz és alkalmazásokhoz. Alább láthatók az Automation-fiók által támogatott beépített szerepkörök:

| **Szerepkör** | **Leírás** |
|:--- |:--- |
| Tulajdonos |A tulajdonosi szerepkör lehetővé teszi az Automation-fiókon belüli összes erőforrás és művelet elérését, beleértve az Automation-fiók kezeléséhez szükséges más felhasználókhoz, csoportokhoz és alkalmazásokhoz való hozzáférést is. |
| Közreműködő |A közreműködői szerepkör segítségével minden elemet kezelhet, csak más felhasználók Automation-fiókokra vonatkozó hozzáférési jogosultságait nem módosíthatja. |
| Olvasó |Az olvasói szerep lehetővé teszi az összes erőforrás megtekintését egy Automation-fiókban, de módosítás nem hajtható vele végre. |
| Automation-operátor |Az Automation-operátori szerepkör lehetővé teszi a runbook nevének és tulajdonságainak megtekintését, valamint feladatok létrehozását és kezelését egy Automation-fiókban lévő összes runbookok. Ez a szerepkör akkor hasznos, ha meg szeretné akadályozni, hogy az Automation-fiók erőforrásokat, például a hitelesítő adategységeket és a forgatókönyveket megtekintsék vagy módosítsák, de továbbra is engedélyezi a szervezet tagjai számára a forgatókönyvek végrehajtását. |
|Automation-feladatok operátora|Az Automation-feladat operátori szerepkör lehetővé teszi feladatok létrehozását és kezelését egy Automation-fiókban lévő összes runbookok.|
|Automation Runbook operátor|Az Automation Runbook-kezelő szerepkör lehetővé teszi a Runbook nevének és tulajdonságainak megtekintését.|
| Log Analytics közreműködő | A Log Analytics közreműködő szerepkör lehetővé teszi az összes figyelési adat olvasását és a figyelési beállítások szerkesztését. A figyelési beállítások szerkesztése magában foglalja a virtuálisgép-bővítmény virtuális gépekhez való hozzáadását, a Storage-fiók kulcsainak beolvasását, amelyekkel konfigurálhatja az Azure Storage-ból származó naplók gyűjteményét, automatizálhatja és konfigurálhatja az Automation-fiókokat, valamint megoldásokat adhat minden Azure-erőforrás.|
| Log Analytics olvasó | A Log Analytics olvasó szerepkör lehetővé teszi az összes figyelési adat megtekintését és keresését, valamint a figyelési beállítások megtekintését. Ez magában foglalja az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson. |
| Közreműködő figyelése | A figyelési közreműködő szerepkör lehetővé teszi az összes figyelési adat olvasását és a figyelési beállítások frissítését.|
| Figyelő olvasó | A figyelési olvasó szerepkör lehetővé teszi az összes figyelési adat olvasását. |
| Felhasználói hozzáférés rendszergazdája |A felhasználói hozzáférés rendszergazdájának szerepköre lehetővé teszi, hogy kezelje a felhasználói hozzáférést az Azure Automation-fiókokhoz. |

## <a name="role-permissions"></a>Szerepkör-engedélyek

A következő táblázatok ismertetik az egyes szerepkörökhöz adott engedélyeket. Ebbe beletartozhatnak olyan műveletek, amelyek az engedélyeket és a nem megfelelő jogosultságokat biztosítanak.

### <a name="owner"></a>Tulajdonos

A tulajdonosok mindent kezelhetnek, beleértve a hozzáférést is. A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|Műveletek|Leírás|
|---|---|
|Microsoft. Automation/automationAccounts/|Minden típusú erőforrás létrehozása és kezelése.|

### <a name="contributor"></a>Közreműködő

A közreműködők A hozzáférés kivételével mindent kezelhetnek. A következő táblázat a szerepkörhöz megadott és megtagadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/|Minden típusú erőforrás létrehozása és kezelése|
|**Nem műveletek**||
|Microsoft. Authorization/*/delete| Szerepkörök és szerepkör-hozzárendelések törlése.       |
|Microsoft. Authorization/*/Write     |  Szerepkörök és szerepkör-hozzárendelések létrehozása.       |
|Microsoft. Authorization/elevateAccess/művelet    | Megtagadja a felhasználói hozzáférés rendszergazdájának létrehozását.       |

### <a name="reader"></a>Olvasó

Az olvasó megtekintheti egy Automation-fiók összes erőforrását, de nem végezhet módosításokat.

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/READ|Egy Automation-fiók összes erőforrásának megtekintése. |

### <a name="automation-operator"></a>Automation-operátor

Az Automation-operátorok feladatokat hozhatnak létre és kezelhetnek, valamint runbook-neveket és-tulajdonságokat olvashatnak az Automation-fiókokban lévő összes runbookok.  Megjegyzés: Ha a kezelőt egyéni runbookok szeretné vezérelni, akkor ne állítsa be ezt a szerepkört, hanem használja az "Automation-feladat operátora" és az "Automation Runbook operátor" szerepkört. A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft. Authorization/*/READ|Olvasási engedély.|
|Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/READ|Hibrid Runbook Worker-erőforrások olvasása.|
|Microsoft. Automation/automationAccounts/feladatok/olvasás|A runbook feladatok listázása.|
|Microsoft. Automation/automationAccounts/feladatok/folytatás/művelet|Szüneteltetett feladat folytatása.|
|Microsoft. Automation/automationAccounts/feladatok/leállítás/művelet|Feladat megszakítása folyamatban.|
|Microsoft. Automation/automationAccounts/feladatok/streamek/olvasás|Olvassa el a feladatok adatfolyamait és kimenetét.|
|Microsoft. Automation/automationAccounts/feladatok/kimenet/olvasás|A feladatok kimenetének beolvasása.|
|Microsoft. Automation/automationAccounts/Jobs/felfüggesztés/művelet|Feladat felfüggesztése folyamatban.|
|Microsoft. Automation/automationAccounts/feladatok/írás|Hozzon létre feladatokat.|
|Microsoft. Automation/automationAccounts/jobSchedules/READ|Azure Automation-feladatütemezés beolvasása.|
|Microsoft. Automation/automationAccounts/jobSchedules/Write|Azure Automation feladatok ütemtervének létrehozása|
|Microsoft. Automation/automationAccounts/linkedWorkspace/READ|Az Automation-fiókhoz kapcsolódó munkaterület beolvasása.|
|Microsoft. Automation/automationAccounts/READ|Azure Automation fiók beszerzése.|
|Microsoft. Automation/automationAccounts/runbookok/READ|Azure Automation runbook beolvasása.|
|Microsoft. Automation/automationAccounts/ütemezett/beolvasás|Azure Automation ütemezett eszköz beolvasása.|
|Microsoft. Automation/automationAccounts/ütemterv/írás|Azure Automation ütemezett eszköz létrehozása vagy frissítése.|
|Microsoft. Resources/Subscriptions/resourceGroups/READ      |Szerepkörök és szerepkör-hozzárendelések olvasása.         |
|Microsoft. Resources/üzemelő példány/*      |Erőforráscsoport-telepítések létrehozása és kezelése.         |
|Microsoft. bepillantások/alertRules/*      | Riasztási szabályok létrehozása és kezelése.        |
|Microsoft. support/* |Támogatási jegyek létrehozása és kezelése.|

### <a name="automation-job-operator"></a>Automation-feladatok operátora

Az Automation-feladatok operátori szerepköre az Automation-fiók hatókörében adható meg. Ez lehetővé teszi, hogy az operátor engedélyeket hozzon létre és kezeljen a fiók összes runbookok. A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft. Authorization/*/READ|Olvasási engedély.|
|Microsoft. Automation/automationAccounts/feladatok/olvasás|A runbook feladatok listázása.|
|Microsoft. Automation/automationAccounts/feladatok/folytatás/művelet|Szüneteltetett feladat folytatása.|
|Microsoft. Automation/automationAccounts/feladatok/leállítás/művelet|Feladat megszakítása folyamatban.|
|Microsoft. Automation/automationAccounts/feladatok/streamek/olvasás|Olvassa el a feladatok adatfolyamait és kimenetét.|
|Microsoft. Automation/automationAccounts/Jobs/felfüggesztés/művelet|Feladat felfüggesztése folyamatban.|
|Microsoft. Automation/automationAccounts/feladatok/írás|Hozzon létre feladatokat.|
|Microsoft. Resources/Subscriptions/resourceGroups/READ      |  Szerepkörök és szerepkör-hozzárendelések olvasása.       |
|Microsoft. Resources/üzemelő példány/*      |Erőforráscsoport-telepítések létrehozása és kezelése.         |
|Microsoft. bepillantások/alertRules/*      | Riasztási szabályok létrehozása és kezelése.        |
|Microsoft. support/* |Támogatási jegyek létrehozása és kezelése.|

### <a name="automation-runbook-operator"></a>Automation Runbook operátor

A Runbook-hatókörben az Automation Runbook operátori szerepkört kell megadni. Az Automation Runbook-kezelők megtekinthetik a Runbook nevét és tulajdonságait.  Ez a szerepkör az "Automation Job Operator" szerepkörrel együtt lehetővé teszi, hogy az operátor a runbook is hozzon létre és kezeljen feladatokat. A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/runbookok/READ     | A runbookok listázása.        |
|Microsoft. Authorization/*/READ      | Olvasási engedély.        |
|Microsoft. Resources/Subscriptions/resourceGroups/READ      |Szerepkörök és szerepkör-hozzárendelések olvasása.         |
|Microsoft. Resources/üzemelő példány/*      | Erőforráscsoport-telepítések létrehozása és kezelése.         |
|Microsoft. bepillantások/alertRules/*      | Riasztási szabályok létrehozása és kezelése.        |
|Microsoft. support/*      | Támogatási jegyek létrehozása és kezelése.        |

### <a name="log-analytics-contributor"></a>Log Analytics közreműködő

Egy Log Analytics közreműködő az összes figyelési és a figyelési beállítások szerkesztésével képes olvasni. A figyelési beállítások szerkesztése magában foglalja a virtuálisgép-bővítmény virtuális gépekhez való hozzáadását. a Storage-fiók kulcsainak beolvasása az Azure Storage-ból származó naplók gyűjtésének konfigurálásához; Automation-fiókok létrehozása és konfigurálása; megoldások hozzáadása; és az Azure Diagnostics konfigurálása az összes Azure-erőforráshoz. A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/read|Az összes típusú erőforrás beolvasása, kivéve a titkokat.|
|Microsoft. Automation/automationAccounts/*|Automation-fiókok kezelése.|
|Microsoft. ClassicCompute/virtualMachines/Extensions/*|Virtuálisgép-bővítmények létrehozása és kezelése.|
|Microsoft. ClassicStorage/storageAccounts/Listkeys műveletének beolvasása/művelet|Klasszikus Storage-fiók kulcsainak listázása.|
|Microsoft. számítás/virtualMachines/Extensions/*|Klasszikus virtuálisgép-bővítmények létrehozása és kezelése.|
|Microsoft. bepillantások/alertRules/*|Riasztási szabályok olvasása/írása/törlése.|
|Microsoft. bepillantások/diagnosticSettings/*|Diagnosztikai beállítások olvasása/írása/törlése.|
|Microsoft. OperationalInsights/*|Azure Monitor naplók kezelése.|
|Microsoft. OperationsManagement/*|Megoldások kezelése a munkaterületeken.|
|Microsoft. Resources/üzemelő példány/*|Erőforráscsoport-telepítések létrehozása és kezelése.|
|Microsoft. Resources/Subscriptions/resourcegroups/Deployments/*|Erőforráscsoport-telepítések létrehozása és kezelése.|
|Microsoft. Storage/storageAccounts/Listkeys műveletének beolvasása/művelet|A Storage-fiók kulcsainak listázása.|
|Microsoft. support/*|Támogatási jegyek létrehozása és kezelése.|

### <a name="log-analytics-reader"></a>Log Analytics olvasó

Egy Log Analytics olvasó megtekintheti és megkeresheti az összes figyelési adatait, valamint megtekintheti a figyelési beállításokat, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson. A következő táblázat a szerepkörhöz megadott vagy megtagadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/read|Az összes típusú erőforrás beolvasása, kivéve a titkokat.|
|Microsoft. OperationalInsights/munkaterületek/elemzés/lekérdezés/művelet|Azure Monitor naplókban lévő lekérdezések kezelése.|
|Microsoft. OperationalInsights/munkaterületek/keresés/művelet|Keressen Azure Monitor naplózási adatként.|
|Microsoft. support/*|Támogatási jegyek létrehozása és kezelése.|
|**Nem műveletek**| |
|Microsoft. OperationalInsights/munkaterületek/sharedKeys/olvasás|Nem sikerült beolvasni a közös hozzáférési kulcsokat.|

### <a name="monitoring-contributor"></a>Közreműködő figyelése

Egy figyelő közreműködői beolvashatja az összes figyelési és frissítési figyelési beállítást. A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/read|Az összes típusú erőforrás beolvasása, kivéve a titkokat.|
|Microsoft. AlertsManagement/riasztások/*|Riasztások kezelése.|
|Microsoft. AlertsManagement/alertsSummary/*|A riasztási irányítópult kezelése.|
|Microsoft. bepillantások/AlertRules/*|Riasztási szabályok kezelése.|
|Microsoft. bepillantások/összetevők/*|Application Insights-összetevők kezelése.|
|Microsoft. bepillantások/DiagnosticSettings/*|Diagnosztikai beállítások kezelése.|
|Microsoft. bepillantások/eventtypes/*|Az előfizetésben szereplő tevékenység-naplózási események (kezelési események) listázása. Ez az engedély mind a programozási, mind a portálhoz való hozzáférésre alkalmazható a tevékenység naplójában.|
|Microsoft. bepillantások/LogDefinitions/*|Ez az engedély olyan felhasználók számára szükséges, akiknek a portálon keresztül kell hozzáférnie a tevékenység naplóihoz. A műveletnapló kategóriáinak listázása a tevékenység naplójában.|
|Microsoft. bepillantások/MetricDefinitions/*|A metrikai definíciók (az adott erőforráshoz elérhető metrikai típusok listája) olvasása.|
|Microsoft. bepillantások/mérőszámok/*|Erőforrás metrikáinak olvasása.|
|Microsoft. elemzések/regisztráció/művelet|Regisztrálja a Microsoft. bepillantások szolgáltatóját.|
|Microsoft. bepillantások/webteszt/*|Application Insights webes tesztek kezelése.|
|Microsoft. OperationalInsights/munkaterületek/intelligencepacks/*|Kezelheti Azure Monitor naplók megoldásának csomagjait.|
|Microsoft. OperationalInsights/munkaterületek/savedSearches/*|Azure Monitor naplókat mentett keresések kezelése.|
|Microsoft. OperationalInsights/munkaterületek/keresés/művelet|Keressen Log Analytics munkaterületeket.|
|Microsoft. OperationalInsights/munkaterületek/sharedKeys/művelet|Log Analytics munkaterület kulcsainak listázása.|
|Microsoft. OperationalInsights/munkaterületek/storageinsightconfigs/*|A Azure Monitor naplózza a tárolási Insight-konfigurációkat.|
|Microsoft. support/*|Támogatási jegyek létrehozása és kezelése.|
|Microsoft. WorkloadMonitor/munkaterhelések/*|Munkaterhelések kezelése.|

### <a name="monitoring-reader"></a>Figyelő olvasó

A figyelési olvasó az összes figyelési adattal képes olvasni. A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/read|Az összes típusú erőforrás beolvasása, kivéve a titkokat.|
|Microsoft. OperationalInsights/munkaterületek/keresés/művelet|Keressen Log Analytics munkaterületeket.|
|Microsoft. support/*|Támogatási jegyek létrehozása és kezelése|

### <a name="user-access-administrator"></a>Felhasználói hozzáférés rendszergazdája

A felhasználói hozzáférés rendszergazdája az Azure-erőforrásokhoz való felhasználói hozzáférést is képes kezelni. A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/read|Az összes erőforrás olvasása|
|Microsoft. Authorization/*|Engedélyezés kezelése|
|Microsoft. support/*|Támogatási jegyek létrehozása és kezelése|

## <a name="onboarding"></a>Előkészítés

A következő táblázatok a virtuális gépek a Change Tracking vagy az Update Management megoldásokhoz való bevezetéséhez szükséges minimálisan szükséges engedélyeket mutatják be.

### <a name="onboarding-from-a-virtual-machine"></a>Bevezetés virtuális gépről

|**Művelet**  |**Engedéllyel**  |**Minimális hatókör**  |
|---------|---------|---------|
|Új központi telepítés írása      | Microsoft. Resources/üzemelő példány/*          |Előfizetés          |
|Új erőforráscsoport írása      | Microsoft. Resources/Subscriptions/resourceGroups/Write        | Előfizetés          |
|Új alapértelmezett munkaterület létrehozása      | Microsoft. OperationalInsights/munkaterületek/írás         | Erőforráscsoport         |
|Új fiók létrehozása      |  Microsoft. Automation/automationAccounts/írás        |Erőforráscsoport         |
|Munkaterület és fiók összekapcsolása      |Microsoft. OperationalInsights/munkaterületek/írás</br>Microsoft. Automation/automationAccounts/READ|Munkaterület</br>Automation-fiók
|Megoldás létrehozása      | Microsoft. OperationalInsights/munkaterületek/intelligencepacks/írás |Erőforráscsoport          |
|MMA-bővítmény létrehozása      | Microsoft. számítás/virtualMachines/írás         | Virtual Machine         |
|Mentett keresés létrehozása      | Microsoft. OperationalInsights/munkaterületek/írás          | Munkaterület         |
|Hatókör-konfiguráció létrehozása      | Microsoft. OperationalInsights/munkaterületek/írás          | Munkaterület         |
|Megoldás csatolása a hatókör-konfigurációhoz      | Microsoft. OperationalInsights/munkaterületek/intelligencepacks/írás         | Megoldás         |
|Bevezetési állapot-ellenőrzési munkaterület      | Microsoft. OperationalInsights/munkaterületek/olvasás         | Munkaterület         |
|Bevezetési állapot – a fiók csatolt munkaterület-tulajdonságának beolvasása     | Microsoft. Automation/automationAccounts/READ      | Automation-fiók        |
|Bevezetési állapot-ellenőrzési megoldás      | Microsoft. OperationalInsights/munkaterületek/intelligencepacks/olvasás          | Megoldás         |
|Bevezetési állapot-ellenőrzési virtuális gép      | Microsoft. számítás/virtualMachines/olvasás         | Virtual Machine         |
|Bevezetési állapot-ellenőrzési fiók      | Microsoft. Automation/automationAccounts/READ  |  Automation-fiók   |
| Az<sup>1</sup> . virtuális gép előkészítési munkaterületének keresése       | Microsoft. OperationalInsights/munkaterületek/olvasás         | Előfizetés         |
| A Log Analytics-szolgáltató regisztrálása |Microsoft. elemzések/regisztráció/művelet | Előfizetés|

<sup>1</sup> ez az engedély szükséges a virtuálisgép-portál felületén.

### <a name="onboarding-from-automation-account"></a>Bevezetés az Automation-fiókból

|**Művelet**  |**Engedéllyel** |**Minimális hatókör**  |
|---------|---------|---------|
|Új központi telepítés létrehozása     | Microsoft. Resources/üzemelő példány/*        | Előfizetés         |
|Új erőforráscsoport létrehozása     | Microsoft. Resources/Subscriptions/resourceGroups/Write         | Előfizetés        |
|AutomationOnboarding panel – Új munkaterület létrehozása     |Microsoft. OperationalInsights/munkaterületek/írás           | Erőforráscsoport        |
|AutomationOnboarding panel – csatolt munkaterület olvasása     | Microsoft. Automation/automationAccounts/READ        | Automation-fiók       |
|AutomationOnboarding panel – olvasási megoldás     | Microsoft. OperationalInsights/munkaterületek/intelligencepacks/olvasás         | Megoldás        |
|AutomationOnboarding panel – munkaterület olvasása     | Microsoft. OperationalInsights/munkaterületek/intelligencepacks/olvasás        | Munkaterület        |
|Munkaterület és fiók hivatkozásának létrehozása     | Microsoft. OperationalInsights/munkaterületek/írás        | Munkaterület        |
|Shoebox-fiók írása      | Microsoft. Automation/automationAccounts/írás        | Fiók        |
|Megoldás létrehozása      | Microsoft. OperationalInsights/munkaterületek/intelligencepacks/írás        | Erőforráscsoport         |
|Mentett keresés létrehozása/szerkesztése     | Microsoft. OperationalInsights/munkaterületek/írás        | Munkaterület        |
|Hatókör-konfiguráció létrehozása/szerkesztése     | Microsoft. OperationalInsights/munkaterületek/írás        | Munkaterület        |
|Megoldás csatolása a hatókör-konfigurációhoz      | Microsoft. OperationalInsights/munkaterületek/intelligencepacks/írás         | Megoldás         |
| A Log Analytics-szolgáltató regisztrálása |Microsoft. elemzések/regisztráció/művelet | Előfizetés|
|**2. lépés – több virtuális gép előkészítése**     |         |         |
|VMOnboarding panel – MMA-bővítmény létrehozása     | Microsoft. számítás/virtualMachines/írás           | Virtual Machine        |
|Mentett keresés létrehozása/szerkesztése     | Microsoft. OperationalInsights/munkaterületek/írás           | Munkaterület        |
|Hatókör-konfiguráció létrehozása/szerkesztése  | Microsoft. OperationalInsights/munkaterületek/írás   | Munkaterület|

## <a name="update-management"></a>Frissítéskezelés

Az Update Management több szolgáltatásra is kiterjed a szolgáltatás nyújtásához. A következő táblázat az Update Management-telepítések kezeléséhez szükséges engedélyeket mutatja be:

|**Erőforrás**  |**Szerepkör**  |**Hatókör**  |
|---------|---------|---------|
|Automation-fiók     | Log Analytics közreműködő       | Automation-fiók        |
|Automation-fiók    | Virtuális gépek közreműködője        | A fiókhoz tartozó erőforráscsoport        |
|Log Analytics-munkaterület     | Log Analytics közreműködő| Log Analytics-munkaterület        |
|Log Analytics-munkaterület |Log Analytics olvasó| Előfizetés|
|Megoldás     |Log Analytics közreműködő         | Megoldás|
|Virtual Machine     | Virtuális gépek közreműködője        | Virtual Machine        |

## <a name="configure-rbac-for-your-automation-account"></a>Az Automation-fiók RBAC konfigurálása

A következő szakasz bemutatja, hogyan konfigurálhatja az Automation-fiók RBAC a [portál](#configure-rbac-using-the-azure-portal) és a [PowerShell](#configure-rbac-using-powershell) használatával

### <a name="configure-rbac-using-the-azure-portal"></a>RBAC konfigurálása a Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és nyissa meg az Automation-fiókját az Automation-fiókok lapról.
2. Kattintson a bal felső sarokban található **hozzáférés-vezérlés (iam)** vezérlőre. Ekkor megnyílik a **hozzáférés-vezérlés (iam)** oldal, ahol új felhasználókat, csoportokat és alkalmazásokat adhat hozzá az Automation-fiók kezeléséhez, és megtekintheti az Automation-fiókhoz konfigurálható meglévő szerepköröket.
3. Kattintson a **Szerepkör-hozzárendelések** fülre.

   ![Hozzáférés gomb](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Új felhasználó hozzáadása és szerepkör hozzárendelése

1. A **hozzáférés-vezérlés (iam)** lapon kattintson a **+ szerepkör-hozzárendelés hozzáadása** elemre a **szerepkör-hozzárendelés hozzáadása** lap megnyitásához, ahol hozzáadhat egy felhasználót, csoportot vagy alkalmazást, és hozzárendelhet hozzájuk egy szerepkört.

2. Válasszon egy szerepkört az elérhető szerepkörök listájáról. Kiválaszthatja az Automation-fiók által támogatott elérhető beépített szerepköröket, illetve az Ön által definiált egyéni szerepkört is.

3. Írja be annak a felhasználónak a felhasználónevét, akinek engedélyeket szeretne adni a **Select** mezőben. Válassza ki a felhasználót a listából, és kattintson a **Mentés**gombra.

   ![Felhasználók hozzáadása](media/automation-role-based-access-control/automation-04-add-users.png)

   Most meg kell jelennie a **felhasználók** laphoz hozzáadott felhasználónak a kijelölt szerepkörhöz rendelve

   ![Felhasználók listázása](media/automation-role-based-access-control/automation-05-list-users.png)

   A felhasználóhoz a **Szerepkörök** lapról is hozzárendelhet szerepkört.
4. Kattintson a **szerepkörök** elemre a **hozzáférés-vezérlés (iam)** lapon a **szerepkörök** lap megnyitásához. Itt megtekintheti a szerepkör nevét, és az adott szerepkörhöz rendelt felhasználók és csoportok számát.

    ![Szerepkör hozzárendelése a Felhasználók lapról](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > A szerepköralapú hozzáférés-vezérlés csak az Automation-fiók hatókörében állítható be, és nem az Automation-fiók alatti erőforrásokhoz.

#### <a name="remove-a-user"></a>Felhasználó eltávolítása

Eltávolíthat egy olyan felhasználó hozzáférési engedélyét, aki nem kezeli az Automation-fiókot, vagy akik már nem működnek a szervezeten belül. Alább láthatók egy felhasználó eltávolításának lépései:

1. A **hozzáférés-vezérlés (iam)** lapon válassza ki az eltávolítani kívánt felhasználót, és kattintson az **Eltávolítás**gombra.
2. A hozzárendelés részleteit megjelenítő lapon kattintson az **Eltávolítás** gombra.
3. Az **Igen** gombra kattintva erősítse meg az eltávolítást.

   ![Felhasználók eltávolítása](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>RBAC konfigurálása a PowerShell használatával

A szerepköralapú hozzáférés egy Automation-fiókra is konfigurálható az alábbi [Azure PowerShell parancsmagok](../role-based-access-control/role-assignments-powershell.md)használatával:

A [Get-AzureRmRoleDefinition](/previous-versions/azure/mt603792(v=azure.100)) felsorolja a Azure Active Directoryban elérhető összes RBAC-szerepkört. Ezt a parancsot használhatja a **Name** (Név) tulajdonsággal, ha fel szeretné sorolni az összes műveletet, amelyet egy adott szerepkörrel lehet elvégezni.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

A következő példában a kimenet látható:

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

A [Get-AzureRmRoleAssignment](/previous-versions/azure/mt619413(v=azure.100)) felsorolja az Azure ad-RBAC szerepkör-hozzárendeléseket a megadott hatókörben. Paraméterek nélkül ez a parancs az előfizetéshez tartozó összes szerepkör-hozzárendelést visszaadja. Az **ExpandPrincipalGroups** paraméter segítségével felsorolhatja egy adott felhasználó vagy a felhasználó csoportjának összes hozzáférés-hozzárendelését.
    **Példa:** Az alábbi paranccsal felsorolhatja az összes felhasználót és azok szerepkörét egy Automation-fiókon belül.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A következő példában a kimenet látható:

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

[New-AzureRmRoleAssignment](/previous-versions/azure/mt603580(v=azure.100)) a felhasználókhoz, csoportokhoz és alkalmazásokhoz való hozzáférés egy adott hatókörhöz való hozzárendelését.
    **Példa:** A következő parancs használatával rendelje hozzá az Automation-operátor szerepkört egy felhasználóhoz az Automation-fiók hatókörében.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A következő példában a kimenet látható:

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

A [Remove-AzureRmRoleAssignment](/previous-versions/azure/mt603781(v=azure.100)) használatával távolíthatja el egy adott felhasználó, csoport vagy alkalmazás hozzáférését egy adott hatókörből.
    **Példa:** A következő parancs használatával távolítsa el a felhasználót az Automation-fiók hatókörében található Automation-operátor szerepkörből.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Az előző példákban cserélje le a **bejelentkezési azonosítót**, az **előfizetés azonosítóját**, az **erőforráscsoport nevét**és az **Automation-fiók nevét** a fiókja adataira. Ha a rendszer rákérdez, hogy tényleg szeretné-e törölni a szerepkör-hozzárendelést, kattintson az **Igen** gombra.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Az Automation-kezelő szerepkör-Automation-fiók felhasználói felülete

Ha az Automation-fiók hatókörében az Automation-operátori szerepkörhöz hozzárendelt felhasználó megtekinti a hozzájuk rendelt Automation-fiókot, csak az Automation-fiókban létrehozott runbookok, runbook-feladatok és-ütemtervek listáját tekinthetik meg, de nem tekinthetik meg definition. Elindíthatják, leállíthatják, felfüggeszthetik, folytathatják és ütemezhetik a runbook-feladatokat. A felhasználó nem férhet hozzá más Automation-erőforrásokhoz, például konfigurációkhoz, hibrid feldolgozói csoportokhoz vagy DSC-csomópontokhoz.

![Nincs hozzáférése az erőforrásokhoz](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>A Runbookok RBAC konfigurálása

Azure Automation lehetővé teszi, hogy a RBAC adott runbookok rendeljen hozzá. Ehhez futtassa a következő szkriptet egy felhasználó adott runbook való hozzáadásához. Az alábbi szkriptet egy Automation-fiók rendszergazdája vagy egy bérlői rendszergazda futtathatja.

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

Ha futott, a felhasználó bejelentkezhet a Azure Portalba, és megtekintheti az **összes erőforrást**. A listában láthatók azok a Runbook, amelyeket a rendszer **Automation Runbook-kezelőként** adott hozzá.

![Runbook RBAC a portálon](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Felhasználói élmény az Automation-operátori szerepkörhöz – Runbook

Ha egy felhasználó, aki az Automation-operátori szerepkörhöz van hozzárendelve a Runbook-hatókörben, akkor a hozzárendelt Runbook csak a Runbook indíthatók el, és megtekinthetik a Runbook feladatokat.

![Csak az indításhoz van hozzáférése](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Következő lépések

* Az RBAC for Azure Automation konfigurálásának különböző módjaira vonatkozó további információért lásd [az RBAC Azure PowerShellel folytatott kezelésével](../role-based-access-control/role-assignments-powershell.md) foglalkozó témakört.
* További információk a forgatókönyvek elindításának különböző módjairól: [Forgatókönyv elindítása](automation-starting-a-runbook.md).
* További információk a különböző forgatókönyv-típusokról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)

