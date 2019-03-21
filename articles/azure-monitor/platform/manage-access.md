---
title: Az Azure monitorban Log Analytics munkaterületeinek kezeléséhez |} A Microsoft Docs
description: Kezelheti a Log Analytics-munkaterületek az Azure Monitor használatával különféle felügyeleti feladatokat a felhasználók, fiókok, munkaterületeken és Azure-fiókokon.
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
ms.date: 02/07/2019
ms.author: magoedte
ms.openlocfilehash: be285b6a51ae5a0f4239b841ce64100f1875d785
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294348"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Naplóadatok és az Azure monitorban munkaterületek kezelése
Az Azure Monitor-tárolók adatok jelentkezzen be a Log Analytics-munkaterület, amely lényegében, ha egy tároló, amely adatot és konfigurációs információt tartalmazza. Az adatok hozzáférésének kezelése, munkaterületekhez kapcsolódó különféle adminisztratív feladatokat végezhet. Ön vagy a szervezet más tagjai több munkaterületet is használhatnak az informatikai infrastruktúra egészéből vagy egyes részeiből begyűjtött különböző adatkészletek kezeléséhez.

Ez a cikk bemutatja, hogyan naplók elérése és felügyelheti azokat a munkaterületeket. 

## <a name="create-a-workspace"></a>Munkaterület létrehozása
Log Analytics-munkaterület létrehozásához kell tennie:

1. Azure-előfizetés.
2. Egy választott név a munkaterületnek.
3. A munkaterület társítása az egyik az előfizetésekhez és erőforráscsoportokhoz.
4. Földrajzi hely kiválasztása.

A munkaterület létrehozásával kapcsolatos részletekért a következő cikkekben talál:

- [Log Analytics-munkaterület létrehozása az Azure Portalon](../learn/quick-create-workspace.md)
- [Log Analytics-munkaterület létrehozásához Azure CLI 2.0 használatával](../learn/quick-create-workspace-cli.md)
- [Log Analytics-munkaterület létrehozása az Azure PowerShell használatával](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>A szükséges munkaterületek számának meghatározása
Log Analytics-munkaterületet egy Azure-erőforrás és egy tárolót az adatokat, gyűjtött, összesítése, elemzése, és jelenik meg az Azure monitorban. Azure-előfizetésenként több munkaterülettel rendelkezhet, és lehetővé teszi az egyszerű lekérdezésére, egynél több munkaterülethez férhet. Ez a szakasz azt ismerteti, hogy mikor lehet hasznos egynél több munkaterület létrehozása.

Log Analytics-munkaterületet biztosít:

* Egy földrajzi helyet az adattárolás számára.
* Az adatok elkülönítését a különböző felhasználói hozzáférési jogosultságokat adja meg a munkaterület-központú módban. Nem érvényes, ha az erőforrás-központú módban működik.
* Például a konfigurációs beállítások hatókörének [tarifacsomag](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [megőrzési](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) és [adatok kapacitástól](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap).
* A munkaterület-erőforráshoz az adatbetöltést és -megőrzési kapcsolódó költségek készülnek el.

Felhasználás szempontjából javasoljuk, hogy a lehető legkevesebb munkaterületek hoz létre. Gyorsabb és egyszerűbb felügyelet és a lekérdezések adatbeolvasási. Azonban a fenti jellemzők alapján, érdemes több munkaterületet létrehozni:

* Globális és az adatok szuverenitását vagy megfelelőségi okok miatt külön régiókban tárolt adatok kell jelentkezik.
* Az Azure-t használja, és a kimenő adatátviteli díjak elkerülése érdekében ugyanabban a régióban szeretne létesíteni egy munkaterületet, amelyikben az általa kezelt Azure-erőforrás is található.
* Felügyelt szolgáltatást kínál, és az egyes felügyelt ügyfelek naplóelemzési adatait külön kell tárolnia a többi ügyfél adataitól.
* Több ügyfelet is kezel, és azt szeretné, hogy minden ügyfél / részleg / üzleti csoportok lássák a saját adatok, de nem más, az adatokat, és nem üzleti kell az összevont közötti ügyfél / részleg és üzleti csoportok nézet. ".

Ha az adatgyűjtéshez Windows-ügynököket használ, [konfigurálhatja úgy az egyes ügynököket, hogy egy vagy több munkaterületnek küldjenek jelentést](../../azure-monitor/platform/agent-windows.md).

A System Center Operations Manager használata esetén az Operations Manager egyes felügyeleti csoportjai csak egy munkaterülethez csatlakoztathatók. Telepítheti a Microsoft Monitoring Agentet az Operations Manager által felügyelt számítógépekre, és beállíthatja az ügynököt úgy, hogy az Operations Managernek és egy másik Log Analytics-munkaterületnek is küldjön jelentést.

Ha a munkaterület architektúra van definiálva, a szabályzat az Azure-erőforrások kell kényszerítéséhez [Azure Policy](../../governance/policy/overview.md). Ez egy beépített definíció automatikusan vonatkoznak az összes Azure-erőforrások is nyújt. Például egy szabályzatot, hogy az Azure-erőforrások egy adott régióban egy adott munkaterülethez a diagnosztikai naplók küldése sikerült állítsa be.

## <a name="view-workspace-details"></a>Munkaterület adatainak megtekintése
Bár az adatok elemzése a Log Analytics-munkaterületet a a **Azure Monitor** az Azure portal menüjében létrehozásához és kezeléséhez a munkaterületeket, a **Log Analytics-munkaterületek** menü.
 

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) kattintson **minden szolgáltatás**. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza ki **Log Analytics** munkaterületek.  

    ![Azure Portal](media/manage-access/azure-portal-01.png)  

3. Válassza ki a munkaterületet a listából.

4. A munkaterület oldalán a munkaterület adatai, első lépések, konfigurációs, és hivatkozások további információkat jeleníti meg.  

    ![Munkaterület részletei](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>A munkaterület-engedélyek és hatókör
Az adatokat, amely a felhasználó hozzáféréssel rendelkezik az alábbi táblázatban felsorolt számos tényező határozza meg. Az egyes alábbi szakaszokban ismertetjük.

| Tényező | Leírás |
|:---|:---|
| [Hozzáférési mód](#access-modes) | Módszer, amellyel a felhasználók fér hozzá a munkaterülethez.  A rendelkezésre álló adatok köre és a hozzáférési mód alkalmazott határoz meg. |
| [Hozzáférés-vezérlési mód](#access-control-mode) | A beállítás a munkaterülethez, amely meghatározza, hogy engedélyeket a munkaterületet, vagy az erőforrás szintre vonatkoznak. |
| [Engedélyek](#manage-accounts-and-users) | Alkalmazza a személy vagy a munkaterület vagy az erőforrás a felhasználói csoportok engedélyeit. Határozza meg, milyen adatokat a felhasználó hozzáférést kap. |
| [Táblázatok szintjén RBAC](#table-level-rbac) | Hozzáférési mód vagy a hozzáférés-vezérlési mód függetlenül minden felhasználóra vonatkozik, nem kötelező részletes engedélyeket. Egy felhasználó hozzáférhessen adattípusokat határozza meg. |



## <a name="access-modes"></a>Hozzáférési mód
A _hozzáférési mód_ vonatkozik, hogy egy felhasználó hozzáfér a Log Analytics-munkaterület és a hozzáférhető adatok köre hatókörének meghatározása. 

**Workspace-centric**: Ebben a módban a felhasználók megtekinthetik az összes napló, amely engedélyekkel rendelkeznek a munkaterületen. Ebben a módban lekérdezések hatóköre a munkaterület összes táblájában lévő összes adatot. Ez a hozzáférési mód használatos, ha a naplók érhetők el a munkaterület hatókörként, például amikor kiválasztja a **naplók** származó a **Azure Monitor** menü az Azure Portalon.

**Resource-centric**: Amikor hozzáfér a munkaterület egy adott erőforráshoz, például ha **naplók** az Azure Portalon egy erőforrás menüben megtekintheti az adott erőforráshoz csak naplók hozzáféréssel rendelkező összes tábla. Ebben a módban lekérdezések hatóköre csak az adott erőforráshoz kapcsolódó adatokat. Ebben a módban a részletes szerepköralapú hozzáférés-vezérlés (RBAC) emellett lehetővé teszi. 

> [!NOTE]
> Naplók érhetők el erőforrás-központú lekérdezések csak, mintha azok a megfelelő erőforrás megfelelően társítva. A következő erőforrások jelenleg korlátozásokkal rendelkezik: 
> - Az Azure-on kívüli gépeken
> - Service Fabric
> - Application Insights
> - Containers
> - HTTP-adatgyűjtő API által létrehozott egyéni naplók
>
> Ha a naplók rendelt megfelelően az erőforrás egy lekérdezés futtatásával és a rekordok vizsgálatával érdekli tesztelheti. Ha a helyes erőforrás-azonosító szerepel a [_ResourceId](log-standard-properties.md#_resourceid) tulajdonság, majd az adatok érhető el az erőforrás-központú lekérdezéseket.

### <a name="comparing-access-modes"></a>Hozzáférési módok összehasonlítása

A következő táblázat összefoglalja a hozzáférési mód:

| | Workspace-centric | Erőforrás-központú |
|:---|:---|:---|
| Kinek szól a mindegyik modellt? | Központi adminisztrációs. Rendszergazdák, akik konfigurálnia kell az adatgyűjtést és a felhasználók, akiknek szükség van a számos különböző erőforrásokhoz való hozzáférést. Emellett jelenleg szükséges naplók az Azure-on kívüli erőforrások eléréséhez rendelkező felhasználók számára. | Alkalmazás olyan csoportok számára. A rendszergazdák a figyelt Azure-erőforrások. |
| Mi a felhasználó igényel a naplók megtekintéséhez? | Engedélyek a munkaterülethez. Lásd: **a munkaterület-engedélyek** a [fiókok és felhasználók kezeléséhez](#manage-accounts-and-users). | Olvasási hozzáférést az erőforráshoz. Lásd: **erőforrás-engedélyek** a [fiókok és felhasználók kezeléséhez](#manage-accounts-and-users). Lehet engedélyeket örökölt (például tartalmazó erőforráscsoport), vagy közvetlenül az erőforráshoz rendelt. A naplókban az erőforrás számára automatikusan hozzá lesz rendelve. |
| Mi az engedélyek hatóköre? | Munkaterület. A munkaterülethez hozzáféréssel rendelkező felhasználók lekérdezheti az összes napló táblákból származó munkaterület, amely engedélyekkel rendelkeznek. Lásd: [tábla hozzáférés-vezérlés](#table-access-control) | Azure-erőforrás. Felhasználó lekérdezheti a naplók az erőforrások a bármely munkaterülethez hozzá, de nem tudják lekérdezni a naplók az egyéb erőforrások. |
| Hogyan lehet a felhasználó hozzáférési naplók? | Indítsa el **naplók** a **Azure Monitor** menü vagy **Log Analytics-munkaterületek**. | Indítsa el **naplók** az Azure-beli erőforráshoz a menüből. |


## <a name="access-control-mode"></a>Hozzáférés-vezérlési mód
A _hozzáférés-vezérlési mód_ egy beállítás az egyes munkaterületek, amely meghatározza, milyen engedélyek határozzák meg az adott munkaterületben.

**Munkaterület-engedélyeket igényelnek**:  Ez a vezérlő mód nem engedélyezi a részletes RBAC. Egy felhasználó a munkaterület eléréséhez azok a munkaterületet, vagy adott táblák engedélyeket kell adni. 

Ha egy felhasználó hozzáfér a munkaterületet a munkaterület-központú módban, fog hozzáféréssel rendelkeznek az összes adat bármely táblákhoz, hogy kapott hozzáférést az. Ha egy felhasználó hozzáfér a munkaterület erőforrás-központú módban, rendelkeznek az adott erőforráshoz csak adatokhoz való hozzáférés bármely táblákban, azok hozzáférést szerez.

Ez az alapértelmezés szerint minden március 2019 előtt létrehozott munkaterületeket a.

**Erőforrás vagy a munkaterület engedélyeket használ**: Ez a vezérlő mód lehetővé teszi, hogy a részletes RBAC. Felhasználók kapjanak csak az megtekinthetik az Azure-engedélyek, erőforrások rendelkeznek erőforrásokhoz kapcsolódó adatokhoz való hozzáférés `read` engedéllyel. 

Amikor egy felhasználó hozzáfér a munkaterületet a munkaterület-központú módban, a munkaterület-engedélyek érvényesek. Amikor egy felhasználó hozzáfér a munkaterület erőforrás-központú módban, csak a megfelelő engedélyekkel a rendszer ellenőrzi, és figyelmen kívül hagyja a munkaterület-engedélyek. RBAC engedélyezi a felhasználó számára eltávolítja a hatóköröket a munkaterület-engedélyek, és lehetővé teszi a megfelelő engedélyekkel, hogy a.

Ez az az alapértelmezett beállítás a március 2019 után létrehozott összes munkaterületet.

> [!NOTE]
> Ha a felhasználó csak a megfelelő engedélyekkel rendelkezik a munkaterülethez, csak fogják tudni elérni a munkaterület használatával [erőforrás-központú mód](#access-modes).


### <a name="define-access-control-mode-in-azure-portal"></a>Adja meg a hozzáférés-vezérlési mód az Azure Portalon
Megtekintheti a jelenlegi munkaterület hozzáférési mód a **áttekintése** a munkaterület oldalán a **Log Analytics-munkaterület** menü.

![Nézet munkaterület hozzáférési mód](media/manage-access/view-access-control-mode.png)

Ezt a beállítást módosíthatja a a **tulajdonságok** a munkaterülethez tartozó oldal. A beállítás módosítása esetén a rendszer letiltja nincs engedélye a munkaterület konfigurálása.

![Módosítsa a munkaterület hozzáférési mód](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>Adja meg a hozzáférés-vezérlési mód a PowerShellben

A következő paranccsal vizsgálja meg a hozzáférést az előfizetés minden munkaterülethez mód:

```PowerShell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

A következő parancsfájl használatával állítsa be a hozzáférést egy adott munkaterület mód:

```PowerShell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Használja a következő szkriptet az előfizetésben a hozzáférést minden munkaterülethez mód beállítása

```PowerShell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Adjon meg a hozzáférési mód a Resource Manager-sablon
A hozzáférési mód az Azure Resource Manager-sablon konfigurálásához állítsa a **enableLogAccessUsingOnlyResourcePermissions** funkció jelzőt a munkaterület az alábbi értékek egyikére.

- **FALSE (hamis)**: Munkaterület-központú engedélyek beállítása a munkaterületen. Ez az az alapértelmezett beállítást, ha a jelző nincs beállítva.
- **Igaz**: Erőforrás-központú engedélyek beállítása a munkaterületen.


## <a name="manage-accounts-and-users"></a>Fiókok és felhasználók kezelése
Az engedélyek a munkaterülethez egy adott felhasználóra alkalmazott határozza meg a hozzáférési mód és a [hozzáférés-vezérlési mód](#access-control-mode) a munkaterület. **A munkaterület-engedélyek** lépnek, amikor egy felhasználó hozzáfér minden olyan munkaterület használatával **munkaterület-központú** a [munkaterület-központú mód](#access-modes). **Erőforrás-engedélyek** lépnek, amikor egy felhasználó hozzáfér egy munkaterület **erőforrás vagy a munkaterület engedélyeket használ** [hozzáférés-vezérlési mód](#access-control-mode) használatával [erőforrás-központú mód ](#access-modes).

### <a name="workspace-permissions"></a>A munkaterület-engedélyek
Minden munkaterülethez társítva több fiókot lehet, és minden egyes fiók férhet hozzá több munkaterülettel. Access-n keresztül kezelt [Azure szerepköralapú hozzáférés-](../../role-based-access-control/role-assignments-portal.md). 


Az alábbi tevékenységek szintén Azure-engedélyeket igényelnek:

| Műveletek                                                          | Azure-engedélyek szükségesek | Megjegyzések |
|-----------------------------------------------------------------|--------------------------|-------|
| Hozzáadása és eltávolítása a figyelési megoldások                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Az engedélyeknek az erőforráscsoport vagy előfizetés szinteken kell megadva lenniük. |
| Tarifacsomag módosítása                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Adatok megtekintése a *Backup* és a *Site Recovery* megoldások csempéin | Rendszergazda / Társadminisztrátor | A klasszikus üzemi modellel üzembe helyezett erőforrásokhoz fér hozzá |
| Munkaterület létrehozása az Azure Portalon                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Az Azure-engedélyekkel a Log Analytics-munkaterület-hozzáférés kezelése 
Ha Azure-engedélyekkel kíván hozzáférést biztosítani a Log Analytics-munkaterülethez, kövesse [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../../role-based-access-control/role-assignments-portal.md) című rész lépéseit.

Az Azure Log Analytics-munkaterületek a két beépített felhasználói szerepkörökhöz rendelkezik:
- Log Analytics olvasó
- Log Analytics közreműködő

A *Log Analytics olvasó* szerepkör tagjai a következőket végezhetik el:
- Az összes monitorozási adat megtekintése és keresése 
- A monitorozási beállítások megtekintése, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson.

A Log Analytics olvasó szerepkör az alábbi Azure műveleteket tartalmazza:

| Típus    | Engedély | Leírás |
| ------- | ---------- | ----------- |
| Műveletek | `*/read`   | Lehetővé teszi az összes Azure-erőforrások és erőforrás-konfiguráció megtekintése. Ez a következők megtekintését foglalja magában: <br> Virtuális gépi bővítmény állapota <br> Az Azure Diagnostics konfigurációja az erőforrásokon <br> Az összes erőforrás tulajdonságai és beállításai |
| Műveletek | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Log Search v2-lekérdezések végrehajtása |
| Műveletek | `Microsoft.OperationalInsights/workspaces/search/action` | Log Search v1-lekérdezések végrehajtása |
| Műveletek | `Microsoft.Support/*` | Támogatási esetek nyitása |
|Nem művelet | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Olvasását meggátolja az adatgyűjtési API használatához és az ügynökök telepítéséhez szükséges kulcs. Ez megakadályozza, hogy a felhasználó új erőforrásokat ad hozzá a munkaterülethez |


A *Log Analytics közreműködő* szerepkör tagjai a következőket végezhetik el:
- Ahogy a Log Analytics olvasó is az összes monitorozási adat olvasása  
- Automation-fiókok létrehozása és konfigurálása  
- Felügyeleti megoldások hozzáadása és eltávolítása    
    > [!NOTE] 
    > Annak érdekében, hogy sikeresen megtörtént az utolsó két műveletekre, ezt az engedélyt kell adni az erőforrás-csoportba vagy előfizetésbe szinten.  

- A tárfiókkulcsok olvasása   
- Az Azure Storage-naplók gyűjtésének konfigurálása  
- Azure-erőforrások monitorozási beállításainak szerkesztése, beleértve a következőket:
  - Virtuális gépi bővítmények hozzáadása a virtuális gépekhez
  - Az Azure Diagnostics konfigurálása az összes Azure-erőforráson

> [!NOTE] 
> Ezzel a funkcióval virtuális gépi bővítményt adhat a virtuális gépekhez a virtuális gép teljes körű irányítása érdekében.

A Log Analytics-közreműködő szerepkört az Azure a következőket tartalmazza:

| Engedély | Leírás |
| ---------- | ----------- |
| `*/read`     | Az összes erőforrás és erőforrás-konfiguráció megtekintése. Ez a következők megtekintését foglalja magában: <br> Virtuális gépi bővítmény állapota <br> Az Azure Diagnostics konfigurációja az erőforrásokon <br> Az összes erőforrás tulajdonságai és beállításai |
| `Microsoft.Automation/automationAccounts/*` | Azure Automation-fiókok létrehozása és konfigurálása, beleértve runbookok hozzáadását és szerkesztését |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Virtuális gépi bővítmények hozzáadása, frissítése és eltávolítása, beleértve a Microsoft Monitoring Agent bővítményt és a Linuxhoz készült OMS-ügynök bővítményt |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | A tárfiókkulcs megtekintése. A Log Analytics az Azure Storage-fiókok naplóinak olvasására való konfigurálásához szükséges |
| `Microsoft.Insights/alertRules/*` | Riasztási szabályok hozzáadása, frissítése és eltávolítása |
| `Microsoft.Insights/diagnosticSettings/*` | Diagnosztikai beállítások hozzáadása, frissítése és eltávolítása az Azure-erőforrásokról |
| `Microsoft.OperationalInsights/*` | Konfigurációk hozzáadása, frissítése és eltávolítása a Log Analytics-munkaterületekről |
| `Microsoft.OperationsManagement/*` | Felügyeleti megoldások hozzáadása és eltávolítása |
| `Microsoft.Resources/deployments/*` | Üzemelő példányok létrehozása és törlése. Megoldások, munkaterületek és Automation-fiókok hozzáadásához és eltávolításához szükséges |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Üzemelő példányok létrehozása és törlése. Megoldások, munkaterületek és Automation-fiókok hozzáadásához és eltávolításához szükséges |

Felhasználók a felhasználói szerepkörökhöz való hozzáadásához `Microsoft.Authorization/*/Delete` és `Microsoft.Authorization/*/Write` engedély szükséges.

Ezen szerepkörökkel különféle hatókörökben biztosíthat hozzáférést a felhasználók számára:
- Előfizetés – Hozzáférés az előfizetésben lévő összes munkaterülethez
- Erőforráscsoport – Hozzáférés az erőforráscsoportban lévő összes munkaterülethez
- Erőforrás – Hozzáférés kizárólag az adott erőforráshoz

Végre kell hajtania az erőforrásszintek simítása (munkaterület) ahhoz, hogy biztosítsa pontos hozzáférés-vezérlés-hozzárendelést.  Az [egyéni szerepkörök](../../role-based-access-control/custom-roles.md) segítségével létrehozhat a szükséges egyedi engedélyekkel rendelkező szerepköröket.

### <a name="resource-permissions"></a>Erőforrás-engedélyek 
Felhasználók lekérdezés bejelentkezésekor egy munkaterületről, erőforrás-központú hozzáféréssel rendelkeznek a következő engedélyeket az erőforráson:

| Engedély | Leírás |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Példák:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Lehetővé teszi az erőforrás minden adatainak megtekintéséhez.  |


Ezt általában az engedélyt, amely tartalmaz egy szerepkörből  _\*/olvasási vagy_ _\*_ például a beépített engedélyek [olvasó](../../role-based-access-control/built-in-roles.md#reader) és [ Közreműködői](../../role-based-access-control/built-in-roles.md#contributor) szerepköröket. Vegye figyelembe, hogy egyéni szerepköröket, amelyek bizonyos műveleteket, vagy dedikált beépített szerepkörök előfordulhat, hogy ez az engedély nem vonatkozik.

Lásd: [tábla hozzáférés-vezérlés meghatározása](#defining-per-table-access-control) ha alább, kíván létrehozni a különböző táblák különböző hozzáférés-vezérlést.


## <a name="table-level-rbac"></a>Táblázatok szintjén RBAC
**Tábla szint RBAC** lehetővé teszi, hogy az más engedélyek mellett a Log Analytics-munkaterület az adatok részletesebb vezérléshez. Ez a vezérlő lehetővé teszi meghatározott adattípusok, amelyek csak a felhasználók adott csoportja számára elérhető definiálása.

Tábla hozzáférés-vezérlés megvalósítása [Azure egyéni szerepkörök](../../role-based-access-control/custom-roles.md) engedélyezéséhez, vagy megtagadja a hozzáférést az adott [táblák](../log-query/log-query-overview.md#how-azure-monitor-log-data-is-organized) a munkaterületen. Ezek a szerepkörök érvénybe lépnek a munkaterület-központú vagy erőforrás-központú munkaterületek [ellenőrzési mód eléréséhez](#access-control-modes) függetlenül a felhasználói [hozzáférési mód](#access-mode).

Hozzon létre egy [egyéni szerepkör](../../role-based-access-control/custom-roles.md) a hozzáférés-vezérlés táblában való hozzáférés meghatározásához a következő műveleteket.

- Egy tábla hozzáférést, adja meg a a **műveletek** a szerepkör-definíció szakaszában.
- Engedélyezi a hozzáférést egy táblához, például a a **NotActions** a szerepkör-definíció szakaszában.
- Használat *, adja meg az összes tábla.

Például hozzon létre egy szerepkörhöz való hozzáféréssel a _szívverés_ és _AzureActivity_ táblákat, hozzon létre egy egyéni biztonsági szerepkört a következő műveletekkel:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Szerepkör létrehozása a hozzáférést csak _SecurityBaseline_ és más táblák, hozzon létre egy egyéni szerepkör segítségével a következő műveleteket:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
```

### <a name="custom-logs"></a>Egyéni naplók
 Egyéni naplók hozza létre az adatforrás, például egyéni naplók és a HTTP-adatgyűjtő API. A legegyszerűbben úgy azonosíthatja a napló típusa van felsorolva a táblák ellenőrzésével [egyéni naplói a napló sémában](../log-query/get-started-portal.md#understand-the-schema).

 Jelenleg nem lehet engedélyezni, vagy megtagadja a hozzáférést az egyes egyéni naplók, de engedélyezheti vagy megtagadhatja a hozzáférést az összes egyéni naplókkal. Egy szerepkör az összes egyéni napló-hozzáféréssel rendelkező, hozzon létre egy egyéni szerepkör segítségével a következő műveleteket:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Megfontolandó szempontok

- Ha egy felhasználó számára biztosított globális olvasási engedélyt, a standard szintű olvasó és közreműködő szerepkörökhöz, amelyek tartalmazzák a  _\*/olvasási_ műveletet, az felül fogja írni a tábla hozzáférés-vezérlés és azokat elérhetővé teheti az összes naplózási adatokat.
- Ha egy felhasználó tábla hozzáférés, de semmilyen más engedélyt kap, azok tudná hozzáférés a naplóadatokhoz, az API-ból, de nem az Azure Portalról. Biztosíthat hozzáférést az Azure Portalon, az alap szerepkör Log Analytics olvasó használjuk.
- Az előfizetés rendszergazdái férhetnek összes adattípusok semmilyen más engedélyt beállításaitól függetlenül.
- Munkaterület tulajdonosok mint minden más felhasználónak, tábla hozzáférés-vezérlést kell kezelni.
- Hozzárendelések száma csökkentése érdekében egyéni felhasználók helyett biztonsági csoportokat kell szerepköröket rendelhet. Ez akkor is hasznos, meglévő csoportházirend-felügyeleti eszközök használatával konfigurálhatja, és a hozzáférés ellenőrzéséhez.




## <a name="next-steps"></a>További lépések
* Lásd: [Log Analytics-ügynök – áttekintés](../../azure-monitor/platform/log-analytics-agent.md) gyűjthet velük adatokat az adatközpontban vagy egyéb felhőalapú környezetben lévő számítógépekről.
* Az adatgyűjtés Azure-beli virtuális gépekről való konfigurálásáról lásd: [Adatgyűjtés Azure-beli virtuális gépekről](../../azure-monitor/learn/quick-collect-azurevm.md).  

