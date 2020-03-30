---
title: Log Analytics-munkaterületek kezelése az Azure Monitorban | Microsoft dokumentumok
description: Erőforrás-, munkaterület- vagy táblaszintű engedélyek használatával kezelheti a Log Analytics-munkaterületen tárolt adatokhoz való hozzáférést az Azure Monitorban. Ez a cikk részletezi, hogyan kell kitölteni.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/22/2019
ms.openlocfilehash: 1e559309b8e8d9768ca2f79dabfb01ec6086a961
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348717"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>A naplóadatokhoz és a munkaterületekhez való hozzáférés kezelése az Azure Monitorban

Az Azure Monitor [a naplóadatokat](data-platform-logs.md) egy Log Analytics-munkaterületen tárolja. A munkaterület olyan tároló, amely adatokat és konfigurációs adatokat tartalmaz. A naplóadatokhoz való hozzáférés kezeléséhez a munkaterülethez kapcsolódó különböző felügyeleti feladatokat hajt végre.

Ez a cikk bemutatja, hogyan kezelheti a naplókhoz való hozzáférést, és hogyan felügyelheti az azokat tartalmazó munkaterületeket, beleértve a hozzáférés engedélyezését: 

* Munkaterületi engedélyeket használó munkaterület.
* Azok a felhasználók, akiknek hozzáférésre van szükségük az azure-szerepköralapú hozzáférés-vezérlés (RBAC) használatával adott erőforrásokból származó adatok naplózásához.
* Azok a felhasználók, akiknek hozzáférésre van szükségük a naplóadatokhoz a munkaterület egy adott táblájában az Azure RBAC használatával.

## <a name="configure-access-control-mode"></a>Hozzáférés-vezérlési mód konfigurálása

Megtekintheti a munkaterületen konfigurált [hozzáférés-vezérlési módot](design-logs-deployment.md) az Azure Portalon vagy az Azure PowerShellsegítségével.  Ezt a beállítást az alábbi támogatott módszerek egyikével módosíthatja:

* Azure portál

* Azure PowerShell

* Azure Resource Manager-sablon

### <a name="from-the-azure-portal"></a>Az Azure Portalról

Az aktuális munkaterületi hozzáférés-vezérlési módot a munkaterület **Áttekintés lapján,** a Log Analytics munkaterület menüjében **tekintheti** meg.

![Munkaterület-hozzáférés-vezérlési mód megtekintése](media/manage-access/view-access-control-mode.png)

1. Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .
1. Az Azure Portalon válassza a Log Analytics-munkaterületeket > munkaterületét.

Ezt a beállítást a munkaterület **Tulajdonságok** lapján módosíthatja. Ha nincs engedélye a munkaterület konfigurálásához, a beállítás módosítása le lesz tiltva.

![Munkaterület-hozzáférési mód módosítása](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>A PowerShell használata

A következő paranccsal megvizsgálhatja az előfizetés összes munkaterületének hozzáférés-vezérlési módját:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

A kimenetnek a következőhöz kell hasonlítania:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Az azt `False` jelenti, hogy a munkaterület a munkaterület-környezet hozzáférési móddal van konfigurálva.  Az azt `True` jelenti, hogy a munkaterület az erőforrás-környezet hozzáférési móddal van konfigurálva.

> [!NOTE]
> Ha egy munkaterület logikai érték nélkül kerül visszaadásra, és `False` üres, ez egy érték eredményének is megfelel.
>

A következő parancsfájl segítségével állítsa be egy adott munkaterület hozzáférés-vezérlési módját az erőforrás-környezet engedélyre:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Az alábbi parancsfájl segítségével állítsa be a hozzáférés-vezérlési módot az erőforrás-környezet engedélyre való előfizetés összes munkaterületéhez:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>Erőforrás-kezelő sablon használata

A hozzáférési mód konfigurálásához egy Azure Resource Manager-sablonban állítsa be az **enableLogAccessUsingOnlyResourcePermissions** szolgáltatás jelzőjét a munkaterületen az alábbi értékek egyikére.

* **false**: Állítsa be a munkaterületet munkaterület-környezet engedélyekre. Ez az alapértelmezett beállítás, ha a jelző nincs beállítva.
* **true**: Állítsa be a munkaterületet az erőforrás-környezet engedélyeire.

## <a name="manage-access-using-workspace-permissions"></a>Hozzáférés kezelése munkaterületi engedélyekkel

Minden munkaterülethez több fiók is tartozhat, és minden fiók több munkaterülethez is hozzáférhet. Az Access kezelése az [Azure szerepköralapú hozzáférésével történik.](../../role-based-access-control/role-assignments-portal.md)

Az alábbi tevékenységek szintén Azure-engedélyeket igényelnek:

|Műveletek |Azure-engedélyek szükségesek |Megjegyzések |
|-------|-------------------------|------|
| Felügyeleti megoldások hozzáadása és eltávolítása | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Az engedélyeknek az erőforráscsoport vagy előfizetés szinteken kell megadva lenniük. |
| Tarifacsomag módosítása | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Adatok megtekintése a *Backup* és a *Site Recovery* megoldások csempéin | Rendszergazda / Társadminisztrátor | A klasszikus üzemi modellel üzembe helyezett erőforrásokhoz fér hozzá |
| Munkaterület létrehozása az Azure Portalon | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| A munkaterület alapvető tulajdonságainak megtekintése és a munkaterület panel megadása a portálon | `Microsoft.OperationalInsights/workspaces/read` ||
| Lekérdezési naplók bármely felület használatával | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Az összes naplótípus elérése lekérdezésekkel | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Adott naplótábla elérése | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| A munkaterületi kulcsok olvasása a naplók elküldésének engedélyezéséhez | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Hozzáférés kezelése azure-engedélyekkel

Ha Azure-engedélyekkel kíván hozzáférést biztosítani a Log Analytics-munkaterülethez, kövesse [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../../role-based-access-control/role-assignments-portal.md) című rész lépéseit. Például egyéni szerepkörök, [lásd: Egyéni szerepkörök például](#custom-role-examples)

Az Azure két beépített felhasználói szerepkörrel rendelkezik a Log Analytics-munkaterületekhez:

* Log Analytics olvasó
* Log Analytics közreműködő

A *Log Analytics olvasó* szerepkör tagjai a következőket végezhetik el:

* Az összes monitorozási adat megtekintése és keresése
* A monitorozási beállítások megtekintése, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson.

A Log Analytics-olvasó szerepkör a következő Azure-műveleteket tartalmazza:

| Típus    | Engedély | Leírás |
| ------- | ---------- | ----------- |
| Műveletek | `*/read`   | Az összes Azure-erőforrás és erőforrás-konfiguráció megtekintésének lehetősége. Ez a következők megtekintését foglalja magában: <br> Virtuális gépi bővítmény állapota <br> Az Azure Diagnostics konfigurációja az erőforrásokon <br> Az összes erőforrás összes tulajdonsága és beállítása. <br> Munkaterületek esetén lehetővé teszi a munkaterület beállításainak olvasására és az adatok lekérdezésének végrehajtására vonatkozó teljes korlátlan engedélyeket. További részletes beállítások a fenti. |
| Műveletek | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Elavult, nem kell hozzárendelni őket a felhasználókhoz. |
| Műveletek | `Microsoft.OperationalInsights/workspaces/search/action` | Elavult, nem kell hozzárendelni őket a felhasználókhoz. |
| Műveletek | `Microsoft.Support/*` | Támogatási esetek nyitása |
|Nem művelet | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Megakadályozza az adatgyűjtési API használatához és az ügynökök telepítéséhez szükséges munkaterületi kulcs olvasását. Ez megakadályozza, hogy a felhasználó új erőforrásokat adjon a munkaterülethez |

A *Log Analytics közreműködő* szerepkör tagjai a következőket végezhetik el:

* Tartalmazza a *Log Analytics-olvasó szerepkör*összes jogosultságát, amely lehetővé teszi a felhasználó számára az összes figyelési adat olvasását
* Automatizálási fiókok létrehozása és konfigurálása
* Felügyeleti megoldások hozzáadása és eltávolítása

    > [!NOTE]
    > Az utolsó két művelet sikeres végrehajtásához ezt az engedélyt az erőforráscsoport vagy az előfizetés szintjén kell megadni.

* Tárfiók kulcsainak olvasása
* Naplók gyűjtésének konfigurálása az Azure Storage-ból
* Azure-erőforrások monitorozási beállításainak szerkesztése, beleértve a következőket:
  * Virtuális gépi bővítmények hozzáadása a virtuális gépekhez
  * Az Azure Diagnostics konfigurálása az összes Azure-erőforráson

> [!NOTE]
> Ezzel a funkcióval virtuális gépi bővítményt adhat a virtuális gépekhez a virtuális gép teljes körű irányítása érdekében.

A Log Analytics közreműködői szerepkör a következő Azure-műveleteket tartalmazza:

| Engedély | Leírás |
| ---------- | ----------- |
| `*/read`     | Az összes erőforrás és erőforrás-konfiguráció megtekintése. Ez a következők megtekintését foglalja magában: <br> Virtuális gépi bővítmény állapota <br> Az Azure Diagnostics konfigurációja az erőforrásokon <br> Az összes erőforrás összes tulajdonsága és beállítása. <br> Munkaterületek esetén lehetővé teszi a munkaterület-beállítás olvasására és lekérdezésvégrehajtására vonatkozó teljes, korlátlan engedélyeket. További részletes beállítások a fenti. |
| `Microsoft.Automation/automationAccounts/*` | Azure Automation-fiókok létrehozása és konfigurálása, beleértve runbookok hozzáadását és szerkesztését |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Virtuális gépi bővítmények hozzáadása, frissítése és eltávolítása, beleértve a Microsoft Monitoring Agent bővítményt és a Linuxhoz készült OMS-ügynök bővítményt |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | A tárfiókkulcs megtekintése. A Log Analytics az Azure Storage-fiókok naplóinak olvasására való konfigurálásához szükséges |
| `Microsoft.Insights/alertRules/*` | Riasztási szabályok hozzáadása, frissítése és eltávolítása |
| `Microsoft.Insights/diagnosticSettings/*` | Diagnosztikai beállítások hozzáadása, frissítése és eltávolítása az Azure-erőforrásokról |
| `Microsoft.OperationalInsights/*` | A Log Analytics-munkaterületek konfigurációjának hozzáadása, frissítése és eltávolítása. A munkaterület speciális beállításainak szerkesztéséhez a felhasználónak szüksége van rá. `Microsoft.OperationalInsights/workspaces/write` |
| `Microsoft.OperationsManagement/*` | Felügyeleti megoldások hozzáadása és eltávolítása |
| `Microsoft.Resources/deployments/*` | Üzemelő példányok létrehozása és törlése. Megoldások, munkaterületek és Automation-fiókok hozzáadásához és eltávolításához szükséges |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Üzemelő példányok létrehozása és törlése. Megoldások, munkaterületek és Automation-fiókok hozzáadásához és eltávolításához szükséges |

Felhasználók a felhasználói szerepkörökhöz való hozzáadásához `Microsoft.Authorization/*/Delete` és `Microsoft.Authorization/*/Write` engedély szükséges.

Ezen szerepkörökkel különféle hatókörökben biztosíthat hozzáférést a felhasználók számára:

* Előfizetés – Hozzáférés az előfizetésben lévő összes munkaterülethez
* Erőforráscsoport – Hozzáférés az erőforráscsoportban lévő összes munkaterülethez
* Erőforrás – Hozzáférés kizárólag az adott erőforráshoz

Javasoljuk, hogy erőforrásszinten (munkaterület) hajtson végre hozzárendeléseket a pontos hozzáférés-vezérlés biztosítása érdekében. Az [egyéni szerepkörök](../../role-based-access-control/custom-roles.md) segítségével létrehozhat a szükséges egyedi engedélyekkel rendelkező szerepköröket.

### <a name="resource-permissions"></a>Erőforrás-engedélyek

Amikor a felhasználók erőforrás-környezet-hozzáféréssel kérdezik le a naplókat egy munkaterületről, a következő engedélyekkel rendelkeznek az erőforráshoz:

| Engedély | Leírás |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Példák:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Az erőforrás összes naplóadatának megtekintésére szolgáló képesség.  |
| `Microsoft.Insights/diagnosticSettings/write` | Diagnosztikai beállítás konfigurálása az erőforrás naplóinak beállításához. |

`/read`az engedély általában olyan szerepkörből származik, amely tartalmazza _\*_ [Reader](../../role-based-access-control/built-in-roles.md#reader) [Contributor](../../role-based-access-control/built-in-roles.md#contributor) _ \*_ az /read vagy az engedélyeket, például a beépített Olvasó és közreműködő szerepköröket. Előfordulhat, hogy az adott műveleteket vagy dedikált beépített szerepköröket tartalmazó egyéni szerepkörök nem tartalmazzák ezt az engedélyt.

Ha különböző táblákhoz szeretne különböző hozzáférés-vezérlést létrehozni, olvassa el az alábbi [Táblaalapú hozzáférés-vezérlés megadása](#table-level-rbac) című témakört.

## <a name="custom-role-examples"></a>Példák egyéni szerepkörre

1. Ha hozzáférést szeretne adni a felhasználóknak az erőforrásaikból származó naplóadatokhoz, hajtsa végre az alábbiakat:

    * A munkaterület hozzáférés-vezérlési módjának konfigurálása **munkaterületi vagy erőforrás-engedélyek használatára**

    * Adjon `*/read` felhasználóknak vagy `Microsoft.Insights/logs/*/read` engedélyeket az erőforrásaiknak. Ha már hozzá vannak rendelve a [Log Analytics-olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör a munkaterületen, elegendő.

2. Ha hozzáférést szeretne adni a felhasználóknak az erőforrásaikból származó naplóadatokhoz, és konfigurálni szeretné az erőforrásokat a naplók munkaterületre küldésére, hajtsa végre az alábbi műveleteket:

    * A munkaterület hozzáférés-vezérlési módjának konfigurálása **munkaterületi vagy erőforrás-engedélyek használatára**

    * Adja meg a felhasználóknak a `Microsoft.OperationalInsights/workspaces/read` `Microsoft.OperationalInsights/workspaces/sharedKeys/action`következő engedélyeket a munkaterületre: és a. Ezekkel az engedélyekkel a felhasználók nem hajthatnak végre munkaterület-szintű lekérdezéseket. Csak a munkaterületet számba vehetik, és a diagnosztikai beállítások vagy az ügynök konfigurációjához használható.

    * Adja meg a felhasználóknak `Microsoft.Insights/logs/*/read` a `Microsoft.Insights/diagnosticSettings/write`következő engedélyeket az erőforrásaikhoz: és a. Ha már hozzá vannak rendelve a [Log Analytics közreműködői](../../role-based-access-control/built-in-roles.md#contributor) szerepkörhöz, hozzávannak rendelve az Olvasó szerepkörhöz, vagy engedélyeket kapnak `*/read` ehhez az erőforráshoz, elegendő.

3. Ha azt szeretné, hogy a felhasználók anélkül férhessenek hozzá az erőforrásaikból származó naplóadatokhoz, hogy nem olvashatnák a biztonsági eseményeket és küldhetnek adatokat, hajtsa végre az alábbi műveleteket:

    * A munkaterület hozzáférés-vezérlési módjának konfigurálása **munkaterületi vagy erőforrás-engedélyek használatára**

    * Adja meg a felhasználóknak `Microsoft.Insights/logs/*/read`a következő engedélyeket az erőforrásaikhoz: .

    * A következő NonAction hozzáadása a SecurityEvent típus `Microsoft.Insights/logs/SecurityEvent/read`olvasásának letiltásához: . A NonAction-nek ugyanabban az egyéni szerepkörben kell`Microsoft.Insights/logs/*/read`lennie, mint az olvasási engedélyt ( ) tartalmazó műveletnek. Ha a felhasználó rejlő olvasási művelet egy másik szerepkör, amely hozzá van rendelve az erőforrás vagy az előfizetés vagy erőforráscsoport, akkor képes lesz olvasni az összes naplótípust. Ez akkor is igaz, ha örökölnek `*/read`, amelyek például az Olvasó vagy a Közreműködő szerepkörrel léteznek.

4. Ha hozzáférést szeretne adni a felhasználónak az erőforrásokból származó naplóadatokhoz, és el szeretné olvasni az összes Azure AD bejelentkezési és az Update Management megoldásnapló adatait a munkaterületről, hajtsa végre az alábbi műveleteket:

    * A munkaterület hozzáférés-vezérlési módjának konfigurálása **munkaterületi vagy erőforrás-engedélyek használatára**

    * Adja meg a felhasználóknak a következő engedélyeket a munkaterületre: 

        * `Microsoft.OperationalInsights/workspaces/read`– szükséges, hogy a használat felsorolhassa a munkaterületet, és megnyithassa a munkaterületpanelt az Azure Portalon
        * `Microsoft.OperationalInsights/workspaces/query/read`- szükséges minden felhasználó számára, amely képes végrehajtani lekérdezések
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read`– az Azure AD bejelentkezési naplóinak olvasása
        * `Microsoft.OperationalInsights/workspaces/query/Update/read`- ahhoz, hogy olvasni Update Management megoldás naplók
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read`- ahhoz, hogy olvasni Update Management megoldás naplók
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read`- ahhoz, hogy olvasni Update management naplók
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read`– szükséges, hogy képes legyen használni Update Management megoldás
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read`– szükséges, hogy képes legyen használni Update Management megoldás

    * Adja meg a felhasználóknak `*/read`a következő engedélyeket az `Microsoft.Insights/logs/*/read`erőforrásaikhoz: , az Olvasó szerepkörhöz rendelve vagy . 

## <a name="table-level-rbac"></a>Táblázat szintű RBAC

**Az RBAC táblázatszintű RBAC** lehetővé teszi, hogy a többi engedély mellett részletesebb vezérlést is definiáljon a Log Analytics-munkaterületen lévő adatokhoz. Ez a vezérlő lehetővé teszi olyan adattípusok meghatározását, amelyek csak a felhasználók egy adott készlete számára érhetők el.

Táblahozzáférés-vezérlést valósíthat meg [az Azure egyéni szerepkörökkel,](../../role-based-access-control/custom-roles.md) hogy vagy hozzáférést biztosítson a munkaterület adott [tábláihoz.](../log-query/logs-structure.md) Ezek a szerepkörök munkaterület-környezetben vagy erőforrás-környezet [hozzáférés-vezérlési móddal](design-logs-deployment.md#access-control-mode) rendelkező munkaterületekre vonatkoznak, függetlenül a felhasználó [hozzáférési módjától.](design-logs-deployment.md#access-mode)

Hozzon létre egy [egyéni szerepkört](../../role-based-access-control/custom-roles.md) a következő műveletekkel a táblahozzáférés-vezérléshez való hozzáférés meghatározásához.

* Egy táblához való hozzáférés engedélyezéséhez adja azt a szerepkör-definíció **Műveletek** szakaszába. Ha ki szeretné vonni a hozzáférést az engedélyezett **műveletekből,** adja meg a **NotActions** szakaszban.
* A Microsoft.OperationalInsights/workspaces/query/* segítségével adja meg az összes táblát.

Ha például a _Szívverés_ és az _AzureActivity-táblákhoz_ való hozzáféréssel szeretne létrehozni egy szerepkört, hozzon létre egy egyéni szerepkört a következő műveletek használatával:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Ha csak a _SecurityBaseline_ táblához való hozzáféréssel szeretne létrehozni egy szerepkört, hozzon létre egy egyéni szerepkört a következő műveletekkel:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```

### <a name="custom-logs"></a>Egyéni naplók

 Az egyéni naplók adatforrásokból, például egyéni naplókból és HTTP-adatgyűjtő API-ból jönnek létre. A napló típusának azonosításának legegyszerűbb módja a [naplóséma Egyéni naplók csoportjában](../log-query/get-started-portal.md#understand-the-schema)felsorolt táblák ellenőrzése.

 Jelenleg nem adhat hozzáférést az egyes egyéni naplókhoz, de az összes egyéni naplóhoz hozzáférést adhat. Ha az összes egyéni naplóhoz hozzáféréssel rendelkező szerepkört szeretne létrehozni, hozzon létre egyéni szerepkört a következő műveletekkel:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```

### <a name="considerations"></a>Megfontolandó szempontok

* Ha egy felhasználó globális olvasási engedélyt kap a szabványos Reader vagy Közreműködő szerepkörökkel, amelyek tartalmazzák a _ \*/read_ műveletet, felülírja a táblaalapú hozzáférés-vezérlést, és hozzáférést biztosít számukra az összes naplóadathoz.
* Ha egy felhasználó table-enként i. hozzáféréssel rendelkezik, de más engedély nincs, hozzáférhet az API-ból származó naplóadatokhoz, de az Azure Portalról nem. Az Azure Portalról való hozzáférés biztosításához használja a Log Analytics-olvasót alapszerepkörként.
* Az előfizetés rendszergazdái minden adattípushoz hozzáférhetnek, függetlenül az egyéb engedélybeállításoktól.
* A munkaterület-tulajdonosokat ugyanúgy kezeli a rendszer, mint bármely más felhasználót a táblaalapú hozzáférés-vezérléshez.
* A hozzárendelések számának csökkentése érdekében azt javasoljuk, hogy az egyes felhasználók helyett szerepköröket rendelje a biztonsági csoportokhoz. Ez a meglévő csoportkezelő eszközök használatát is segíti a hozzáférés konfigurálásához és ellenőrzéséhez.

## <a name="next-steps"></a>További lépések

* A [Log Analytics-ügynök áttekintése](../../azure-monitor/platform/log-analytics-agent.md) az adatközpontban vagy más felhőbeli környezetben lévő számítógépekről történő adatok gyűjtése című témakörben tekinthető meg.

* Lásd: [Adatok gyűjtése az Azure virtuális gépekről](../../azure-monitor/learn/quick-collect-azurevm.md) az Azure virtuális gépekről történő adatgyűjtés konfigurálásához.
