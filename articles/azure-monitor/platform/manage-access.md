---
title: Log Analytics munkaterületek kezelése Azure Monitorban | Microsoft Docs
description: A Azure Monitor Log Analytics munkaterületen tárolt adataihoz erőforrás, munkaterület vagy tábla szintű engedélyek használatával kezelheti a hozzáférést. Ez a cikk részletesen ismerteti a befejezését.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/22/2019
ms.openlocfilehash: 3a75efc8c73c96bfff0ba94ca3e9753ea536fd53
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289118"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>A naplózási és munkaterületekhez való hozzáférés kezelése Azure Monitor

A Azure Monitor Log Analytics munkaterületen tárolja a [naplózási](data-platform-logs.md) adattárakat. A munkaterület egy olyan tároló, amely adatokat és konfigurációs adatokat tartalmaz. A naplózási adatokhoz való hozzáférés kezeléséhez a munkaterülethez kapcsolódó különféle felügyeleti feladatokat hajthat végre.

Ez a cikk a naplókhoz való hozzáférés kezelését és az azokat tartalmazó munkaterületek felügyeletét ismerteti, beleértve a következőkhöz való hozzáférés biztosítását: 

* Munkaterület-engedélyek használatával.
* Azok a felhasználók, akiknek hozzáférésre van szükségük az adott erőforrásokból származó adatok naplózásához az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használatával.
* Azok a felhasználók, akiknek hozzáférésre van szükségük a munkaterületen lévő adott tábla adataihoz az Azure RBAC használatával.

## <a name="configure-access-control-mode"></a>Hozzáférés-vezérlési mód konfigurálása

A munkaterületen konfigurált hozzáférés-vezérlési módot a Azure Portal vagy a Azure PowerShell használatával tekintheti meg.  Ezt a beállítást a következő támogatott módszerek egyikével módosíthatja:

* Azure Portal

* Azure PowerShell

* Azure Resource Manager-sablon

### <a name="from-the-azure-portal"></a>Az Azure Portalról

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

`False` érték azt jelenti, hogy a munkaterület a munkaterület-környezet elérési módjával van konfigurálva.  A `True` érték azt jelenti, hogy a munkaterület az erőforrás-kontextus hozzáférési móddal van konfigurálva.

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

* **hamis**: állítsa be a munkaterületet munkaterület-környezet engedélyeire. Ez az alapértelmezett beállítás, ha a jelző nincs beállítva.
* **true (igaz**): állítsa be a munkaterületet erőforrás-környezeti engedélyekre.

## <a name="manage-access-using-workspace-permissions"></a>Hozzáférés kezelése a munkaterület engedélyeivel

Minden munkaterülethez több fiók is társítható, és mindegyik fiók több munkaterülethez is hozzáférhet. A hozzáférés az [Azure szerepköralapú hozzáférés](../../role-based-access-control/role-assignments-portal.md)használatával felügyelhető.

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

A Log Analytics olvasó szerepkör a következő Azure-műveleteket tartalmazza:

| Type (Típus)    | Engedély | Leírás |
| ------- | ---------- | ----------- |
| Műveletek | `*/read`   | Az összes Azure-erőforrás és erőforrás-konfiguráció megtekintésének lehetősége. Ez a következők megtekintését foglalja magában: <br> Virtuális gépi bővítmény állapota <br> Az Azure Diagnostics konfigurációja az erőforrásokon <br> Az összes erőforrás tulajdonságai és beállításai. <br> A munkaterületek számára lehetővé teszi, hogy a teljes korlátozás alá eső engedélyek beolvassák a munkaterület beállításait, és lekérdezéseket végezzenek az adatokon. Lásd a fenti részletesebb beállításokat. |
| Műveletek | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Elavult, nincs szükség a felhasználókhoz való hozzárendelésre. |
| Műveletek | `Microsoft.OperationalInsights/workspaces/search/action` | Elavult, nincs szükség a felhasználókhoz való hozzárendelésre. |
| Műveletek | `Microsoft.Support/*` | Támogatási esetek nyitása |
|Nem művelet | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Az adatgyűjtési API használatához és az ügynökök telepítéséhez szükséges munkaterület-kulcs olvasásának megakadályozása. Ez megakadályozza, hogy a felhasználó új erőforrásokat adjon a munkaterülethez |

A *Log Analytics közreműködő* szerepkör tagjai a következőket végezhetik el:

* A *log Analytics olvasó szerepkör*összes jogosultságát tartalmazza, így a felhasználó beolvashatja az összes figyelési adatát
* Automation-fiókok létrehozása és konfigurálása
* Felügyeleti megoldások hozzáadása és eltávolítása

    > [!NOTE]
    > Az utolsó két művelet sikeres végrehajtásához ezt az engedélyt az erőforráscsoport vagy az előfizetés szintjén kell megadni.

* Storage-fiók kulcsainak olvasása
* Naplók gyűjteményének konfigurálása az Azure Storage-ból
* Azure-erőforrások monitorozási beállításainak szerkesztése, beleértve a következőket:
  * Virtuális gépi bővítmények hozzáadása a virtuális gépekhez
  * Az Azure Diagnostics konfigurálása az összes Azure-erőforráson

> [!NOTE]
> Ezzel a funkcióval virtuális gépi bővítményt adhat a virtuális gépekhez a virtuális gép teljes körű irányítása érdekében.

A Log Analytics közreműködő szerepkör a következő Azure-műveleteket tartalmazza:

| Engedély | Leírás |
| ---------- | ----------- |
| `*/read`     | Az összes erőforrás és erőforrás-konfiguráció megtekintése. Ez a következők megtekintését foglalja magában: <br> Virtuális gépi bővítmény állapota <br> Az Azure Diagnostics konfigurációja az erőforrásokon <br> Az összes erőforrás tulajdonságai és beállításai. <br> A munkaterületek számára lehetővé teszi, hogy a teljes korlátozás alá eső engedélyek beolvassák a munkaterület beállítását, és lekérdezéseket végezzenek az adatokon. Lásd a fenti részletesebb beállításokat. |
| `Microsoft.Automation/automationAccounts/*` | Azure Automation-fiókok létrehozása és konfigurálása, beleértve runbookok hozzáadását és szerkesztését |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Virtuális gépi bővítmények hozzáadása, frissítése és eltávolítása, beleértve a Microsoft Monitoring Agent bővítményt és a Linuxhoz készült OMS-ügynök bővítményt |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | A tárfiókkulcs megtekintése. A Log Analytics az Azure Storage-fiókok naplóinak olvasására való konfigurálásához szükséges |
| `Microsoft.Insights/alertRules/*` | Riasztási szabályok hozzáadása, frissítése és eltávolítása |
| `Microsoft.Insights/diagnosticSettings/*` | Diagnosztikai beállítások hozzáadása, frissítése és eltávolítása az Azure-erőforrásokról |
| `Microsoft.OperationalInsights/*` | Log Analytics-munkaterületek konfigurációjának hozzáadása, frissítése és eltávolítása. A munkaterület speciális beállításainak szerkesztéséhez a felhasználónak `Microsoft.OperationalInsights/workspaces/write`ra van szüksége. |
| `Microsoft.OperationsManagement/*` | Felügyeleti megoldások hozzáadása és eltávolítása |
| `Microsoft.Resources/deployments/*` | Üzemelő példányok létrehozása és törlése. Megoldások, munkaterületek és Automation-fiókok hozzáadásához és eltávolításához szükséges |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Üzemelő példányok létrehozása és törlése. Megoldások, munkaterületek és Automation-fiókok hozzáadásához és eltávolításához szükséges |

Felhasználók a felhasználói szerepkörökhöz való hozzáadásához `Microsoft.Authorization/*/Delete` és `Microsoft.Authorization/*/Write` engedély szükséges.

Ezen szerepkörökkel különféle hatókörökben biztosíthat hozzáférést a felhasználók számára:

* Előfizetés – Hozzáférés az előfizetésben lévő összes munkaterülethez
* Erőforráscsoport – Hozzáférés az erőforráscsoportban lévő összes munkaterülethez
* Erőforrás – Hozzáférés kizárólag az adott erőforráshoz

Javasoljuk, hogy az erőforrás szintjén (munkaterület) végezze el a hozzárendeléseket, hogy biztosítsa a pontos hozzáférés-vezérlést. Az [egyéni szerepkörök](../../role-based-access-control/custom-roles.md) segítségével létrehozhat a szükséges egyedi engedélyekkel rendelkező szerepköröket.

### <a name="resource-permissions"></a>Erőforrás-engedélyek

Ha a felhasználók erőforrás-kontextusos hozzáférés használatával kérdeznek le naplókat egy munkaterületről, a következő engedélyek lesznek az erőforráshoz:

| Engedély | Leírás |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Példák:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Képes megtekinteni az erőforrás összes naplózási adatforrását.  |
| `Microsoft.Insights/diagnosticSettings/write` | A diagnosztikai beállítások konfigurálásának lehetősége az erőforráshoz tartozó naplók beállításának engedélyezéséhez. |

`/read` engedély általában olyan szerepkörből származik, amely _\*/Read vagy_ _\*_ engedélyeket tartalmaz, például a beépített [olvasó](../../role-based-access-control/built-in-roles.md#reader) -és [közreműködői](../../role-based-access-control/built-in-roles.md#contributor) szerepköröket. Előfordulhat, hogy az adott műveleteket vagy a dedikált beépített szerepköröket tartalmazó egyéni szerepkörök nem tartalmazzák ezt az engedélyt.

Ha eltérő hozzáférés-vezérlést szeretne létrehozni különböző táblákhoz, tekintse meg a [táblázatos hozzáférés-vezérlés meghatározása](#table-level-rbac) című szakaszt.

## <a name="custom-role-examples"></a>Egyéni szerepkörre vonatkozó példák

1. A következő műveletek végrehajtásával biztosíthatja, hogy a felhasználók hozzáférhessenek az erőforrásokból származó adatok naplózásához:

    * Munkaterület hozzáférés-vezérlési módjának konfigurálása **munkaterület vagy erőforrás-engedélyek használatára**

    * Adja meg a felhasználóknak `*/read` vagy `Microsoft.Insights/logs/*/read` engedélyeket az erőforrásaik számára. Ha már hozzá vannak rendelve a [log Analytics olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör a munkaterületen, elegendő.

2. Ha hozzáférést szeretne biztosítani a felhasználóknak az erőforrásaik adataihoz, és konfigurálniuk kell az erőforrásaikat a munkaterületre való küldéshez, hajtsa végre a következőket:

    * Munkaterület hozzáférés-vezérlési módjának konfigurálása **munkaterület vagy erőforrás-engedélyek használatára**

    * Adja meg a felhasználók számára a következő engedélyeket a munkaterületen: `Microsoft.OperationalInsights/workspaces/read` és `Microsoft.OperationalInsights/workspaces/sharedKeys/action`. Ezekkel az engedélyekkel a felhasználók nem hajthatnak végre munkaterület-szintű lekérdezéseket. Csak a munkaterületet tudják enumerálni, és a diagnosztikai beállítások vagy az ügynök konfigurációja célhelyként használhatók.

    * Adja meg a felhasználóknak a következő engedélyeket az erőforrásokhoz: `Microsoft.Insights/logs/*/read` és `Microsoft.Insights/diagnosticSettings/write`. Ha már hozzá vannak rendelve a [log Analytics közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkörhöz, hozzárendelve az olvasó szerepkörhöz, vagy `*/read` engedélyekkel rendelkeznek ezen az erőforráson, elegendő.

3. Ahhoz, hogy a felhasználók hozzáférhessenek az erőforrásaik adataihoz a biztonsági események beolvasása és az adatok elküldése nélkül, végezze el a következőket:

    * Munkaterület hozzáférés-vezérlési módjának konfigurálása **munkaterület vagy erőforrás-engedélyek használatára**

    * Adja meg a felhasználóknak a következő engedélyeket az erőforrásokhoz: `Microsoft.Insights/logs/*/read`.

    * Adja hozzá a következő nem műveletet, hogy a felhasználók beolvassák a SecurityEvent típusát: `Microsoft.Insights/logs/SecurityEvent/read`. A nem műveletnek ugyanabban az egyéni szerepkörben kell lennie, mint az olvasási engedélyt (`Microsoft.Insights/logs/*/read`) biztosító műveletnek. Ha a felhasználó egy másik, az erőforráshoz vagy az előfizetéshez vagy az erőforráscsoporthoz rendelt szerepkörből származó olvasási művelettel rendelkezik, akkor az összes naplózási típust el tudja olvasni. Ez akkor is igaz, ha a `*/read`öröklik, amelyek például az olvasó vagy a közreműködő szerepkörrel rendelkeznek.

4. A következő lépésekkel biztosíthatja, hogy a felhasználók hozzáférjenek az erőforrásaik adataihoz, és beolvassák az összes Azure AD-bejelentkezést, és beolvassák Update Management a megoldás naplójának adatait a munkaterületről:

    * Munkaterület hozzáférés-vezérlési módjának konfigurálása **munkaterület vagy erőforrás-engedélyek használatára**

    * Adja meg a felhasználók számára a következő engedélyeket a munkaterületen: 

        * `Microsoft.OperationalInsights/workspaces/read` – kötelező, hogy a használat felszámolja a munkaterületet, és megnyitja a munkaterület panelt a Azure Portal
        * `Microsoft.OperationalInsights/workspaces/query/read` – kötelező minden olyan felhasználó számára, aki lekérdezéseket futtathat
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read` – az Azure AD bejelentkezési naplói olvasásához
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` – a Update Management-megoldási naplók olvasásához
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` – a Update Management-megoldási naplók olvasásához
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read` – az Update Management-naplók olvasásához
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` – a Update Management megoldás használatához szükséges
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` – a Update Management megoldás használatához szükséges

    * Adja meg a felhasználóknak a következő engedélyeket az erőforrásokhoz: `*/read`, hozzárendelve az olvasó szerepkörhöz, vagy `Microsoft.Insights/logs/*/read`. 

## <a name="table-level-rbac"></a>Táblázat szintű RBAC

A **táblázat szintű RBAC** lehetővé teszi, hogy részletesebb szabályozást adjon meg egy log Analytics munkaterületen lévő adatokhoz a többi engedély mellett. Ez a vezérlő lehetővé teszi olyan konkrét adattípusok meghatározását, amelyek csak egy adott felhasználói csoport számára érhetők el.

A táblázatos hozzáférés-vezérlést az [Azure egyéni szerepköreivel](../../role-based-access-control/custom-roles.md) implementálva engedélyezheti a hozzáférést a munkaterület adott [tábláihoz](../log-query/logs-structure.md) . Ezeket a szerepköröket a rendszer a munkaterület vagy az erőforrás-környezet [hozzáférés-vezérlési móddal](design-logs-deployment.md#access-control-mode) rendelkező munkaterületekre alkalmazza, a felhasználó [hozzáférési módjától](design-logs-deployment.md#access-mode)függetlenül.

Hozzon létre egy [Egyéni szerepkört](../../role-based-access-control/custom-roles.md) a következő műveletekkel a Table Access Control elérésének definiálásához.

* Egy táblához való hozzáférés megadásához vegye fel azt a szerepkör-definíció **műveletek** szakaszába. Az engedélyezett **műveletekből**való kivonáshoz vegye fel **azt a** kizárások közé.
* Az összes tábla megadásához használja a Microsoft. OperationalInsights/workspaces/Query/* lekérdezést.

Ha például egy olyan szerepkört szeretne létrehozni, amely hozzáféréssel rendelkezik a _szívverési_ és _AzureActivity_ táblákhoz, hozzon létre egy egyéni szerepkört a következő műveletek használatával:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Hozzon létre egy egyéni szerepkört, amely csak a _SecurityBaseline_ táblához fér hozzá, a következő műveletek használatával:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```

### <a name="custom-logs"></a>Egyéni naplók

 Az egyéni naplók az adatforrásokból, például az egyéni naplókból és a HTTP-adatgyűjtő API-ból jönnek létre. A naplók típusának legegyszerűbb azonosításához ellenőrizze az egyéni naplók területen felsorolt táblákat [a log sémában](../log-query/get-started-portal.md#understand-the-schema).

 Jelenleg nem adhat hozzáférést az egyéni naplókhoz, de hozzáférést biztosíthat az összes egyéni naplóhoz. Ha az összes egyéni naplóhoz hozzáféréssel rendelkező szerepkört szeretne létrehozni, hozzon létre egy egyéni szerepkört a következő műveletek használatával:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```

### <a name="considerations"></a>Megfontolandó szempontok

* Ha a felhasználó globális olvasási engedéllyel rendelkezik a _\*/Read_ műveletet tartalmazó szabványos olvasó-vagy közreműködői szerepkörökkel, a rendszer felülbírálja a táblán belüli hozzáférés-vezérlést, és hozzáférést biztosít számukra az összes naplózási adathoz.
* Ha egy felhasználó számára engedélyezett a táblán belüli hozzáférés, de nincs más engedélye, akkor hozzáférhetnek a naplóbeli adatokhoz az API-ból, de nem a Azure Portal. A Azure Portal való hozzáférés biztosításához használja az Log Analytics olvasót alapszerepkörként.
* Az előfizetés rendszergazdái hozzáférhetnek az összes adattípushoz, függetlenül attól, hogy más engedélyek is érvényesek.
* A munkaterület tulajdonosai úgy vannak kezelve, mint bármely más felhasználó a táblán belüli hozzáférés-vezérléshez.
* Azt javasoljuk, hogy egyéni felhasználók helyett szerepköröket rendeljen a biztonsági csoportokhoz a hozzárendelések számának csökkentése érdekében. Ez segítséget nyújt a meglévő csoport-felügyeleti eszközök használatához a hozzáférés konfigurálásához és ellenőrzéséhez.

## <a name="next-steps"></a>Következő lépések

* Az adatközpontban vagy más felhőalapú környezetben található számítógépek adatainak összegyűjtéséhez tekintse meg [log Analytics ügynök áttekintését](../../azure-monitor/platform/log-analytics-agent.md) .

* Lásd: adatok [gyűjtése az Azure Virtual Machines](../../azure-monitor/learn/quick-collect-azurevm.md) szolgáltatásról az Azure-beli virtuális gépekről történő adatgyűjtés konfigurálásához.
