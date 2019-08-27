---
title: Log Analytics munkaterületek kezelése Azure Monitorban | Microsoft Docs
description: A Azure Monitor Log Analytics munkaterületeken tárolt adataihoz erőforrás, munkaterület vagy tábla szintű engedélyek használatával kezelheti a hozzáférést. Ez a cikk részletesen ismerteti a következő témakört:.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: magoedte
ms.openlocfilehash: 9bf278b76846b98f58126957c589df87524bb8a4
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034718"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>A naplózási és munkaterületekhez való hozzáférés kezelése Azure Monitor

A Azure Monitor [](data-platform-logs.md) egy log Analytics-munkaterületen tárolja a naplózási adatokat, amely lényegében egy olyan tároló, amely adatokat és konfigurációs adatokat tartalmaz. A naplózási adatokhoz való hozzáférés kezeléséhez a munkaterülethez kapcsolódó különféle felügyeleti feladatokat hajthat végre.

Ez a cikk a naplókhoz való hozzáférés kezelését és az azokat tartalmazó munkaterületek felügyeletét ismerteti, beleértve a következőket:

* Hozzáférés biztosítása olyan felhasználók számára, akiknek hozzáférésre van szükségük az adott erőforrásokból származó adatok naplózásához az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használatával.

* Hozzáférés engedélyezése a munkaterülethez munkaterület-engedélyek használatával.

* Hozzáférés biztosítása azokhoz a felhasználókhoz, akiknek hozzáférésre van szükségük a munkaterület adott táblájához az Azure RBAC használatával.

## <a name="configure-access-control-mode"></a>Hozzáférés-vezérlési mód konfigurálása

A munkaterületen konfigurált hozzáférés-vezérlési módot a Azure Portal vagy a Azure PowerShell használatával tekintheti meg.  Ezt a beállítást a következő támogatott módszerek egyikével módosíthatja:

* Azure Portal

* Azure PowerShell

* Azure Resource Manager-sablon

### <a name="from-the-azure-portal"></a>A Azure Portal

Az aktuális munkaterület-hozzáférés-vezérlési módot a munkaterület **Áttekintés** lapján tekintheti meg a **log Analytics munkaterület** menüjében.

![Munkaterület hozzáférés-vezérlési módjának megtekintése](media/manage-access/view-access-control-mode.png)

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
1. A Azure Portal válassza Log Analytics munkaterületek > a munkaterületet.

Ezt a beállítást a munkaterület **Tulajdonságok** lapján módosíthatja. Ha nem rendelkezik a munkaterület konfigurálásához szükséges engedéllyel, a rendszer letiltja a beállítás módosítását.

![Munkaterület-hozzáférési mód módosítása](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>A PowerShell használata

Az alábbi paranccsal ellenőrizheti az előfizetés összes munkaterületének hozzáférés-vezérlési módját:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

A kimenetnek az alábbihoz hasonlónak kell lennie:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

A érték `False` azt jelenti, hogy a munkaterület a munkaterület-környezet elérési módjával van konfigurálva.  A érték `True` azt jelenti, hogy a munkaterület az erőforrás-kontextus hozzáférési móddal van konfigurálva.

> [!NOTE]
> Ha egy munkaterületet logikai érték nélkül ad vissza, és üres, akkor ez egy `False` érték eredményeinek is megfelel.
>

A következő parancsfájllal állíthatja be a hozzáférés-vezérlési módot egy adott munkaterülethez az erőforrás-környezet engedélyhez:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

A következő szkripttel állíthatja be az előfizetés összes munkaterületének hozzáférés-vezérlési módját az erőforrás-környezet engedélyre:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="using-a-resource-manager-template"></a>Resource Manager-sablon használata

Ha Azure Resource Manager-sablonban szeretné konfigurálni a hozzáférési módot, állítsa be a **enableLogAccessUsingOnlyResourcePermissions** szolgáltatás jelölőjét a munkaterületen az alábbi értékek egyikére.

* **hamis**: A munkaterület beállítása a munkaterület-környezet engedélyeire. Ez az alapértelmezett beállítás, ha a jelző nincs beállítva.
* **igaz**: Állítsa be a munkaterületet erőforrás-környezeti engedélyekre.

## <a name="manage-access-using-workspace-permissions"></a>Hozzáférés kezelése a munkaterület engedélyeivel

Minden munkaterülethez társítva több fiókot lehet, és minden egyes fiók férhet hozzá több munkaterülettel. A hozzáférés az [Azure szerepköralapú hozzáférés](../../role-based-access-control/role-assignments-portal.md)használatával felügyelhető.

Az alábbi tevékenységek szintén Azure-engedélyeket igényelnek:

|Műveletek |Azure-engedélyek szükségesek |Megjegyzések |
|-------|-------------------------|------|
| Figyelési megoldások hozzáadása és eltávolítása | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Az engedélyeknek az erőforráscsoport vagy előfizetés szinteken kell megadva lenniük. |
| Tarifacsomag módosítása | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Adatok megtekintése a *Backup* és a *Site Recovery* megoldások csempéin | Rendszergazda / Társadminisztrátor | A klasszikus üzemi modellel üzembe helyezett erőforrásokhoz fér hozzá |
| Munkaterület létrehozása az Azure Portalon | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| A munkaterület alapszintű tulajdonságainak megtekintése és a munkaterület panel beírása a portálon | `Microsoft.OperationalInsights/workspaces/read` ||
| Naplók lekérdezése bármely felület használatával | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Minden naplózási típus elérése lekérdezések használatával | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Hozzáférés egy adott naplófájlhoz | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| A munkaterülethez tartozó naplók küldésének engedélyezéséhez olvassa el a munkaterület kulcsait | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Hozzáférés kezelése az Azure-engedélyekkel

Ha Azure-engedélyekkel kíván hozzáférést biztosítani a Log Analytics-munkaterülethez, kövesse [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../../role-based-access-control/role-assignments-portal.md) című rész lépéseit. Példa egyéni szerepkörökre: [példa egyéni szerepkörökre](#custom-role-examples)

Az Azure két beépített felhasználói szerepkörrel rendelkezik Log Analytics munkaterületekhez:

* Log Analytics olvasó
* Log Analytics közreműködő

A *Log Analytics olvasó* szerepkör tagjai a következőket végezhetik el:

* Az összes monitorozási adat megtekintése és keresése
* A monitorozási beállítások megtekintése, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson.

A Log Analytics olvasó szerepkör az alábbi Azure műveleteket tartalmazza:

| Típus    | Engedély | Leírás |
| ------- | ---------- | ----------- |
| Műveletek | `*/read`   | Lehetővé teszi az összes Azure-erőforrások és erőforrás-konfiguráció megtekintése. Ez a következők megtekintését foglalja magában: <br> Virtuális gépi bővítmény állapota <br> Az Azure Diagnostics konfigurációja az erőforrásokon <br> Az összes erőforrás tulajdonságai és beállításai. <br> A munkaterületek számára lehetővé teszi, hogy a teljes korlátozás alá eső engedélyek beolvassák a munkaterület beállításait, és lekérdezéseket végezzenek az adatokon. Lásd a fenti részletesebb beállításokat. |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Elavult, nincs szükség a felhasználókhoz való hozzárendelésre. |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | Elavult, nincs szükség a felhasználókhoz való hozzárendelésre. |
| Action | `Microsoft.Support/*` | Támogatási esetek nyitása |
|Nem művelet | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Olvasását meggátolja az adatgyűjtési API használatához és az ügynökök telepítéséhez szükséges kulcs. Ez megakadályozza, hogy a felhasználó új erőforrásokat ad hozzá a munkaterülethez |

A *Log Analytics közreműködő* szerepkör tagjai a következőket végezhetik el:

* Ahogy a Log Analytics olvasó is az összes monitorozási adat olvasása
* Automation-fiókok létrehozása és konfigurálása
* Felügyeleti megoldások hozzáadása és eltávolítása

    > [!NOTE]
    > Annak érdekében, hogy sikeresen megtörtént az utolsó két műveletekre, ezt az engedélyt kell adni az erőforrás-csoportba vagy előfizetésbe szinten.

* A tárfiókkulcsok olvasása
* Az Azure Storage-naplók gyűjtésének konfigurálása
* Azure-erőforrások monitorozási beállításainak szerkesztése, beleértve a következőket:
  * Virtuális gépi bővítmények hozzáadása a virtuális gépekhez
  * Az Azure Diagnostics konfigurálása az összes Azure-erőforráson

> [!NOTE]
> Ezzel a funkcióval virtuális gépi bővítményt adhat a virtuális gépekhez a virtuális gép teljes körű irányítása érdekében.

A Log Analytics-közreműködő szerepkört az Azure a következőket tartalmazza:

| Engedély | Leírás |
| ---------- | ----------- |
| `*/read`     | Az összes erőforrás és erőforrás-konfiguráció megtekintése. Ez a következők megtekintését foglalja magában: <br> Virtuális gépi bővítmény állapota <br> Az Azure Diagnostics konfigurációja az erőforrásokon <br> Az összes erőforrás tulajdonságai és beállításai. <br> A munkaterületek számára lehetővé teszi, hogy a teljes korlátozás alá eső engedélyek beolvassák a munkaterület beállítását, és lekérdezéseket végezzenek az adatokon. Lásd a fenti részletesebb beállításokat. |
| `Microsoft.Automation/automationAccounts/*` | Azure Automation-fiókok létrehozása és konfigurálása, beleértve runbookok hozzáadását és szerkesztését |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Virtuális gépi bővítmények hozzáadása, frissítése és eltávolítása, beleértve a Microsoft Monitoring Agent bővítményt és a Linuxhoz készült OMS-ügynök bővítményt |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | A tárfiókkulcs megtekintése. A Log Analytics az Azure Storage-fiókok naplóinak olvasására való konfigurálásához szükséges |
| `Microsoft.Insights/alertRules/*` | Riasztási szabályok hozzáadása, frissítése és eltávolítása |
| `Microsoft.Insights/diagnosticSettings/*` | Diagnosztikai beállítások hozzáadása, frissítése és eltávolítása az Azure-erőforrásokról |
| `Microsoft.OperationalInsights/*` | Log Analytics-munkaterületek konfigurációjának hozzáadása, frissítése és eltávolítása. A munkaterület speciális beállításainak szerkesztéséhez a felhasználónak `Microsoft.OperationalInsights/workspaces/write`szüksége van. |
| `Microsoft.OperationsManagement/*` | Felügyeleti megoldások hozzáadása és eltávolítása |
| `Microsoft.Resources/deployments/*` | Üzemelő példányok létrehozása és törlése. Megoldások, munkaterületek és Automation-fiókok hozzáadásához és eltávolításához szükséges |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Üzemelő példányok létrehozása és törlése. Megoldások, munkaterületek és Automation-fiókok hozzáadásához és eltávolításához szükséges |

Felhasználók a felhasználói szerepkörökhöz való hozzáadásához `Microsoft.Authorization/*/Delete` és `Microsoft.Authorization/*/Write` engedély szükséges.

Ezen szerepkörökkel különféle hatókörökben biztosíthat hozzáférést a felhasználók számára:

* Előfizetés – Hozzáférés az előfizetésben lévő összes munkaterülethez
* Erőforráscsoport – Hozzáférés az erőforráscsoportban lévő összes munkaterülethez
* Erőforrás – Hozzáférés kizárólag az adott erőforráshoz

A pontos hozzáférés-vezérlés biztosításához az erőforrás szintjén (munkaterület) kell végrehajtania a hozzárendeléseket.  Az [egyéni szerepkörök](../../role-based-access-control/custom-roles.md) segítségével létrehozhat a szükséges egyedi engedélyekkel rendelkező szerepköröket.

### <a name="resource-permissions"></a>Erőforrás-engedélyek

Ha a felhasználók erőforrás-kontextusos hozzáférés használatával kérdeznek le naplókat egy munkaterületről, a következő engedélyek lesznek az erőforráshoz:

| Engedély | Leírás |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Példák:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Képes megtekinteni az erőforrás összes naplózási adatforrását.  |
| `Microsoft.Insights/diagnosticSettings/write` | A diagnosztikai beállítások konfigurálásának lehetősége az erőforráshoz tartozó naplók beállításának engedélyezéséhez. |

`/read`az engedélyt általában olyan szerepkör biztosítja, amely  _\*/Read vagy_ _\*_ engedélyeket tartalmaz, például a beépített [olvasó](../../role-based-access-control/built-in-roles.md#reader) és [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkört. Vegye figyelembe, hogy az adott műveleteket vagy dedikált beépített szerepköröket tartalmazó egyéni szerepkörök nem tartalmazzák ezt az engedélyt.

Ha eltérő hozzáférés-vezérlést szeretne létrehozni különböző táblákhoz, tekintse meg a [táblázatos hozzáférés-vezérlés meghatározása](#table-level-rbac) című szakaszt.

## <a name="custom-role-examples"></a>Egyéni szerepkörre vonatkozó példák

1. A következő műveletek végrehajtásával biztosíthatja, hogy a felhasználók hozzáférhessenek az erőforrásokból származó adatok naplózásához:

    * Munkaterület hozzáférés-vezérlési módjának konfigurálása **munkaterület vagy erőforrás-engedélyek használatára**

    * Felhasználók `*/read` vagy`Microsoft.Insights/logs/*/read` engedélyek megadása az erőforrásokhoz. Ha már hozzá vannak rendelve a [log Analytics olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör a munkaterületen, elegendő.

2. Ha hozzáférést szeretne biztosítani a felhasználóknak az erőforrásaik adataihoz, és konfigurálniuk kell az erőforrásaikat a munkaterületre való küldéshez, hajtsa végre a következőket:

    * Munkaterület hozzáférés-vezérlési módjának konfigurálása **munkaterület vagy erőforrás-engedélyek használatára**

    * Adja meg a felhasználók számára a következő engedélyeket a `Microsoft.OperationalInsights/workspaces/read` munkaterületen: és `Microsoft.OperationalInsights/workspaces/sharedKeys/action`. Ezekkel az engedélyekkel a felhasználók nem hajthatnak végre munkaterület-szintű lekérdezéseket.

    * Adja meg a felhasználóknak a következő engedélyeket az erőforrásaik `Microsoft.Insights/diagnosticSettings/write`számára: `Microsoft.Insights/logs/*/read` és. Ha már hozzá vannak rendelve a [log Analytics közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkör ezen az erőforráson, elegendő.

3. Ha hozzáférést szeretne adni a felhasználónak az erőforrásaik adataihoz, olvassa el az összes Azure AD-bejelentkezést, és olvassa el Update Management a megoldás naplójának adatait, és végezze el a következőket:

    * Munkaterület hozzáférés-vezérlési módjának konfigurálása **munkaterület vagy erőforrás-engedélyek használatára**

    * Adja meg a felhasználók számára a következő engedélyeket a munkaterületen: 

        * `Microsoft.OperationalInsights/workspaces/read`– kötelező, hogy a használat felszámolja a munkaterületet, és megnyitja a munkaterület panelt a Azure Portal
        * `Microsoft.OperationalInsights/workspaces/query/read`– kötelező minden olyan felhasználó számára, aki lekérdezéseket futtathat
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read`– Az Azure AD bejelentkezési naplói olvasásához
        * `Microsoft.OperationalInsights/workspaces/query/Update/read`– a Update Management-megoldási naplók olvasásához
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read`– a Update Management-megoldási naplók olvasásához
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read`– az Update Management-naplók olvasásához
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read`– a Update Management megoldás használatához szükséges
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read`– a Update Management megoldás használatához szükséges

    * Adja meg a felhasználóknak a következő engedélyeket az erőforrásaik `Microsoft.Insights/logs/*/read`számára: `*/read` vagy. Ha a munkaterületen a [log Analytics olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör van hozzárendelve, elegendő.

## <a name="table-level-rbac"></a>Táblázat szintű RBAC

A **táblázat szintű RBAC** lehetővé teszi, hogy részletesebb szabályozást adjon meg egy log Analytics munkaterületen lévő adatokhoz a többi engedély mellett. Ez a vezérlő lehetővé teszi olyan konkrét adattípusok meghatározását, amelyek csak egy adott felhasználói csoport számára érhetők el.

A táblázatos hozzáférés-vezérlést az [Egyéni Azure](../../role-based-access-control/custom-roles.md) -szerepkörökkel implementálva megadhatja vagy megtagadhatja a hozzáférést a munkaterület adott [tábláihoz](../log-query/logs-structure.md) . Ezeket a szerepköröket a rendszer a munkaterület vagy az erőforrás-környezet [hozzáférés-vezérlési móddal](design-logs-deployment.md#access-control-mode) rendelkező munkaterületekre alkalmazza, a felhasználó [hozzáférési módjától](design-logs-deployment.md#access-mode)függetlenül.

Hozzon létre egy [Egyéni szerepkört](../../role-based-access-control/custom-roles.md) a következő műveletekkel a Table Access Control elérésének definiálásához.

* Egy táblához való hozzáférés megadásához vegye fel azt a szerepkör-definíció **műveletek** szakaszába.
* Egy táblához való hozzáférés megtagadásához adja meg azt a szerepkör-definícióban található, a nem tapintatok szakaszban.
* A * használatával az összes tábla megadására használható.

Ha például egy olyan szerepkört szeretne létrehozni, amely hozzáféréssel rendelkezik a szívverési és _AzureActivity_ táblákhoz, hozzon létre egy egyéni szerepkört a következő műveletek használatával:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Ha olyan szerepkört szeretne létrehozni, amely csak _SecurityBaseline_ és más táblákat használ, hozzon létre egyéni szerepkört a következő műveletek végrehajtásával:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>Egyéni naplók

 Az egyéni naplók az adatforrásokból, például az egyéni naplókból és a HTTP-adatgyűjtő API-ból jönnek létre. A naplók típusának legegyszerűbb azonosításához ellenőrizze az egyéni naplók területen felsorolt táblákat [a log sémában](../log-query/get-started-portal.md#understand-the-schema).

 Jelenleg nem engedélyezheti vagy tilthatja le az egyéni naplókhoz való hozzáférést, de megadhatja vagy megtagadhatja az összes egyéni napló elérését. Ha az összes egyéni naplóhoz hozzáféréssel rendelkező szerepkört szeretne létrehozni, hozzon létre egy egyéni szerepkört a következő műveletek használatával:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Megfontolandó szempontok

* Ha a felhasználó globális olvasási engedéllyel rendelkezik a  _\*/Read_ műveletet tartalmazó szabványos olvasó vagy közreműködői szerepkörökkel, a rendszer felülbírálja a táblán belüli hozzáférés-vezérlést, és hozzáférést biztosít számukra az összes naplózási adathoz.
* Ha egy felhasználó számára engedélyezett a táblán belüli hozzáférés, de nincs más engedélye, akkor hozzáférhetnek a naplóbeli adatokhoz az API-ból, de nem a Azure Portal. A Azure Portal való hozzáférés biztosításához használja az Log Analytics olvasót alapszerepkörként.
* Az előfizetés rendszergazdái hozzáférhetnek az összes adattípushoz, függetlenül attól, hogy más engedélyek is érvényesek.
* A munkaterület tulajdonosai úgy vannak kezelve, mint bármely más felhasználó a táblán belüli hozzáférés-vezérléshez.
* Az egyes felhasználók helyett szerepköröket kell hozzárendelni a biztonsági csoportokhoz a hozzárendelések számának csökkentése érdekében. Ez segítséget nyújt a meglévő csoport-felügyeleti eszközök használatához a hozzáférés konfigurálásához és ellenőrzéséhez.

## <a name="next-steps"></a>További lépések

* Lásd: [Log Analytics-ügynök – áttekintés](../../azure-monitor/platform/log-analytics-agent.md) gyűjthet velük adatokat az adatközpontban vagy egyéb felhőalapú környezetben lévő számítógépekről.

* Lásd: adatok [gyűjtése az Azure Virtual Machines](../../azure-monitor/learn/quick-collect-azurevm.md) szolgáltatásról az Azure-beli virtuális gépekről történő adatgyűjtés konfigurálásához.
