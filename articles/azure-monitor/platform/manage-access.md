---
title: Log Analytics munkaterületek kezelése Azure Monitorban | Microsoft Docs
description: A Azure Monitor Log Analytics munkaterületeket a felhasználók, fiókok, munkaterületek és Azure-fiókok számos felügyeleti feladatának használatával kezelheti.
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
ms.date: 07/16/2019
ms.author: magoedte
ms.openlocfilehash: fbfbd8e26ab3e92f06194322be7ec2be2fb180fd
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68254466"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>A naplózási és munkaterületek kezelése Azure Monitor
A Azure Monitor egy Log Analytics-munkaterületen tárolja a naplózási adatokat, amely lényegében egy olyan tároló, amely adatokat és konfigurációs adatokat tartalmaz. A naplózási adatokhoz való hozzáférés kezeléséhez különböző felügyeleti feladatokat hajthat végre a munkaterületekhez kapcsolódóan. Ön vagy a szervezet más tagjai több munkaterületet is használhatnak az informatikai infrastruktúra egészéből vagy egyes részeiből begyűjtött különböző adatkészletek kezeléséhez.

Ez a cikk a naplókhoz való hozzáférés kezelését és az azokat tartalmazó munkaterületek felügyeletét ismerteti. 

## <a name="create-a-workspace"></a>Munkaterület létrehozása
Log Analytics munkaterület létrehozásához a következőket kell tennie:

1. Azure-előfizetés.
2. Egy választott név a munkaterületnek.
3. A munkaterület társítása az egyik az előfizetésekhez és erőforráscsoportokhoz.
4. Földrajzi hely kiválasztása.

A munkaterület létrehozásával kapcsolatos részletekért tekintse meg a következő cikkeket:

- [Log Analytics munkaterület létrehozása a Azure Portal](../learn/quick-create-workspace.md)
- [Log Analytics munkaterület létrehozása az Azure CLI 2,0-vel](../learn/quick-create-workspace-cli.md)
- [Log Analytics munkaterület létrehozása Azure PowerShell](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>A szükséges munkaterületek számának meghatározása
A Log Analytics munkaterület egy Azure-erőforrás, és egy olyan tároló, amelyben az adatok gyűjtése, összesítése, elemzése és megjelenítése történik a Azure Monitorban. Azure-előfizetések több munkaterülettel is rendelkezhetnek, és több munkaterülethez is hozzáférhet, így egyszerűen lekérdezheti őket. Ez a szakasz azt ismerteti, hogy mikor lehet hasznos egynél több munkaterület létrehozása.

A Log Analytics munkaterület A következőket biztosítja:

* Az adattárolás földrajzi helye.
* Adatelkülönítés a különböző felhasználói hozzáférési jogosultságok definiálásához a munkaterület-központú módban. Nem releváns, ha erőforrás-központú módban dolgozik.
* A beállítások, például az [árképzési szintek](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) a megőrzési és [az](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap)adatkorláti konfiguráció hatóköre.
* Az adatfeldolgozással és-megőrzéssel kapcsolatos díjak a munkaterület-erőforráson történnek.

Felhasználás szempontjából javasoljuk, hogy a lehető legkevesebb munkaterületek hoz létre. Gyorsabb és egyszerűbb felügyelet és a lekérdezések adatbeolvasási. Azonban a fenti jellemzők alapján, érdemes több munkaterületet létrehozni:

* Ön globális vállalat, és az adatszuverenitási vagy megfelelőségi okokból az adott régiókban tárolt adatnaplózási adataira van szüksége.
* Az Azure-t használja, és a kimenő adatátviteli díjak elkerülése érdekében ugyanabban a régióban szeretne létesíteni egy munkaterületet, amelyikben az általa kezelt Azure-erőforrás is található.
* Felügyelt szolgáltatást kínál, és az egyes felügyelt ügyfelek naplóelemzési adatait külön kell tárolnia a többi ügyfél adataitól.
* Több ügyfelet is kezelhet, és azt szeretné, hogy minden ügyfél/részleg/üzleti csoport megtekintse a saját adatait, de ne legyenek másoktól származó adatok, és nem igényelnek üzleti igényeket konszolidált több ügyfél/részleg/üzleti csoport nézetben. "

Ha az adatgyűjtéshez Windows-ügynököket használ, [konfigurálhatja úgy az egyes ügynököket, hogy egy vagy több munkaterületnek küldjenek jelentést](../../azure-monitor/platform/agent-windows.md).

A System Center Operations Manager használata esetén az Operations Manager egyes felügyeleti csoportjai csak egy munkaterülethez csatlakoztathatók. Telepítheti a Microsoft Monitoring Agentet az Operations Manager által felügyelt számítógépekre, és beállíthatja az ügynököt úgy, hogy az Operations Managernek és egy másik Log Analytics-munkaterületnek is küldjön jelentést.

A munkaterület-architektúra meghatározása után a szabályzatot az Azure-erőforrásokra kell kényszeríteni [Azure Policyokkal](../../governance/policy/overview.md). Ez egy beépített definíciót is biztosít, amely automatikusan az összes Azure-erőforrásra érvényes lesz. Beállíthat például egy házirendet annak biztosítására, hogy egy adott régióban az összes Azure-erőforrás egy adott munkaterületre küldje el a diagnosztikai naplókat.

## <a name="view-workspace-details"></a>Munkaterület megtekintése – részletek
A Log Analytics munkaterületen lévő adatok elemzésekor a Azure Portal **Azure monitor** menüjében munkaterületeket hoz létre és kezel a **log Analytics** munkaterületek menüben.
 

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és kattintson a **minden szolgáltatás**elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics** munkaterületek lehetőséget.  

    ![Azure Portal](media/manage-access/azure-portal-01.png)  

3. Válassza ki a munkaterületet a listából.

4. A munkaterület oldal a munkaterület részleteit jeleníti meg, az első lépéseket, a konfigurációt és a további információk hivatkozásait.  

    ![Munkaterület részletei](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>Munkaterület engedélyei és hatóköre
A felhasználó által a következő táblázatban felsorolt tényezők határozzák meg, hogy milyen adatokhoz férhet hozzá. Mindegyiket az alábbi szakasz ismerteti.

| Tényező | Leírás |
|:---|:---|
| [Hozzáférési mód](#access-modes) | A felhasználó által a munkaterülethez való hozzáféréshez használt metódus.  Meghatározza az elérhető adatmennyiséget és az alkalmazott hozzáférés-vezérlési módot. |
| [Hozzáférés-vezérlési mód](#access-control-mode) | A munkaterületre vonatkozó beállítás, amely meghatározza, hogy a rendszer az engedélyeket a munkaterületen vagy az erőforrás szintjén alkalmazza-e. |
| [Engedélyek](#manage-accounts-and-users) | A munkaterülethez vagy erőforráshoz tartozó egyéni vagy felhasználói csoportokra alkalmazott engedélyek. Meghatározza, hogy a felhasználó milyen adatelérési pontokhoz férhet hozzá. |
| [Táblázat szintű RBAC](#table-level-rbac) | Az összes felhasználóra vonatkozó opcionális részletes engedélyek, függetlenül azok hozzáférési módjától vagy hozzáférés-vezérlési módjától. Meghatározza, hogy a felhasználó mely adattípusokhoz férhet hozzá. |



## <a name="access-modes"></a>Hozzáférési módok
A _hozzáférési mód_ arra utal, hogy a felhasználó hogyan fér hozzá egy log Analytics munkaterülethez, és meghatározza az általuk elérhetővé vált adatmennyiséget. 

**Munkaterület-** központú: Ebben a módban a felhasználó megtekintheti a munkaterületen lévő összes olyan naplót, amelyhez engedéllyel rendelkezik. Az ebben a módban lévő lekérdezések hatóköre a munkaterület összes tábláján lévő összes értékre vonatkozik. Ez a hozzáférési mód akkor használható, ha a naplók a munkaterülettel való hozzáférés hatókörként vannak megadva, például ha a Azure Portal **Azure monitor** menüjében a **naplók** elemet választja.

**Erőforrás-** központú: Ha egy adott erőforráshoz fér hozzá a munkaterülethez, például ha a Azure Portal erőforrás menüjéből kiválasztja a **naplók** lehetőséget, akkor a naplókat csak az összes olyan táblában tekintheti meg, amelyhez hozzáfér. Az ebben a módban lévő lekérdezések hatóköre csak az adott erőforráshoz társított adatmennyiségre vonatkozik. Ez a mód a részletes szerepköralapú hozzáférés-vezérlést (RBAC) is lehetővé teszi. 

> [!NOTE]
> A naplók csak akkor érhetők el az erőforrás-központú lekérdezésekhez, ha megfelelően vannak társítva az adott erőforráshoz. Jelenleg az alábbi erőforrások korlátai vannak: 
> - Az Azure-on kívüli számítógépek
> - Service Fabric
> - Application Insights
> - Containers
>
> Ellenőrizheti, hogy a naplók megfelelően vannak-e társítva az erőforráshoz egy lekérdezés futtatásával és a kívánt rekordok vizsgálatával. Ha a megfelelő erőforrás-azonosító a [_ResourceId](log-standard-properties.md#_resourceid) tulajdonságban van, akkor az adatok elérhetők az erőforrás-központú lekérdezések számára.

### <a name="comparing-access-modes"></a>Hozzáférési módok összehasonlítása

A következő táblázat összefoglalja a hozzáférési módokat:

| | Munkaterület-központú | Erőforrás-központú |
|:---|:---|:---|
| Kik az egyes modellek? | Központi felügyelet. Azok a rendszergazdák, akiknek olyan adatgyűjtést és felhasználókat kell konfigurálniuk, akiknek számos erőforráshoz kell hozzáféréssel rendelkezniük. Jelenleg olyan felhasználók számára is szükséges, akik az Azure-on kívüli erőforrásokhoz is hozzáférnek a naplóhoz. | Alkalmazás-csapatok. A figyelt Azure-erőforrások rendszergazdái. |
| Mire van szükség a felhasználók számára a naplók megtekintéséhez? | Engedélyeket a munkaterületre. Lásd: **munkaterület-engedélyek** a [fiókok és felhasználók kezeléséhez](#manage-accounts-and-users). | Olvasási hozzáférés az erőforráshoz. Lásd: **erőforrás-engedélyek** a [fiókok és felhasználók kezeléséhez](#manage-accounts-and-users). Az engedélyek örökölhető (például a tartalmazó erőforráscsoporthoz) vagy közvetlenül az erőforráshoz rendelve. A rendszer automatikusan hozzárendeli az erőforrás naplóihoz tartozó engedélyeket. |
| Mi az engedélyek hatóköre? | Munkaterület. A munkaterülethez hozzáféréssel rendelkező felhasználók a munkaterületen lévő összes naplót lekérhetik olyan táblákból, amelyekhez engedélyük van. Lásd: [Table Access Control](#table-level-rbac) | Azure-erőforrás. A felhasználó bármilyen munkaterületről lekérdezheti a naplókat azokhoz az erőforrásokhoz, amelyekhez hozzáférésük van, de más erőforrásokhoz nem lehet lekérdezni |
| Hogyan férhet hozzá a felhasználói naplókhoz? | **Naplók** indítása **Azure Monitor** menüből vagy **log Analytics**munkaterületről. | Indítsa **** el a naplókat az Azure-erőforrás menüjéből. |


## <a name="access-control-mode"></a>Hozzáférés-vezérlési mód
A _hozzáférés-vezérlési mód_ az egyes munkaterületeken olyan beállítás, amely meghatározza, hogy az adott munkaterülethez milyen engedélyek vannak meghatározva.

**Munkaterület-engedélyek**megkövetelése:  Ez a vezérlési mód nem engedélyezi a szemcsés RBAC. Ahhoz, hogy egy felhasználó hozzáférhessen a munkaterülethez, engedélyeket kell adni a munkaterülethez vagy adott táblákhoz. 

Ha a felhasználó munkaterület-központú módban fér hozzá a munkaterülethez, akkor minden olyan táblához hozzáférhet, amelyhez hozzáférést kapott. Ha egy felhasználó erőforrás-központú módban fér hozzá a munkaterülethez, akkor az adott erőforráshoz tartozó összes olyan táblához hozzáférhet, amelyhez hozzáférést kapott.

Ez az alapértelmezett beállítás az összes olyan munkaterülethez, amelyet a március 2019. előtt hoztak létre.

**Erőforrás-vagy munkaterület-engedélyek használata**: Ez a vezérlési mód lehetővé teszi a szemcsés RBAC. A felhasználók csak olyan erőforrásokhoz férhetnek hozzá a hozzáféréshez, amelyeket az Azure-engedélyek, az azokhoz `read` tartozó erőforrások megtekintésére jogosultak. 

Ha a felhasználó munkaterület-központú módban fér hozzá a munkaterülethez, a rendszer a munkaterület engedélyeit alkalmazza. Amikor egy felhasználó erőforrás-központú módban fér hozzá a munkaterülethez, csak az erőforrás-engedélyek lesznek ellenőrizve, és a munkaterület engedélyei figyelmen kívül lesznek hagyva. A RBAC engedélyezése a felhasználók számára a munkaterület engedélyeiből való eltávolításával, valamint az erőforrás-engedélyek felismerésének engedélyezésével.

Ez az alapértelmezett beállítás minden, a március 2019. után létrehozott munkaterülethez.

> [!NOTE]
> Ha egy felhasználó csak erőforrás-jogosultságokkal rendelkezik a munkaterülethez, akkor csak az erőforrás [-központú mód](#access-modes)használatával férhet hozzá a munkaterülethez.


### <a name="define-access-control-mode-in-azure-portal"></a>Hozzáférés-vezérlési mód definiálása Azure Portal
Az aktuális munkaterület-hozzáférés-vezérlési módot a munkaterület **Áttekintés** lapján tekintheti meg a **log Analytics munkaterület** menüjében.

![Munkaterület hozzáférés-vezérlési módjának megtekintése](media/manage-access/view-access-control-mode.png)

Ezt a beállítást a munkaterület **Tulajdonságok** lapján módosíthatja. Ha nem rendelkezik a munkaterület konfigurálásához szükséges engedéllyel, a rendszer letiltja a beállítás módosítását.

![Munkaterület-hozzáférési mód módosítása](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>Hozzáférés-vezérlési mód definiálása a PowerShellben

Az alábbi paranccsal ellenőrizheti az előfizetés összes munkaterületének hozzáférés-vezérlési módját:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

A következő szkripttel állíthatja be egy adott munkaterület hozzáférés-vezérlési módját:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

A következő szkripttel állíthatja be az előfizetés összes munkaterületének hozzáférés-vezérlési módját

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Hozzáférési mód definiálása a Resource Manager-sablonban
Ha Azure Resource Manager-sablonban szeretné konfigurálni a hozzáférési módot, állítsa be a **enableLogAccessUsingOnlyResourcePermissions** szolgáltatás jelölőjét a munkaterületen az alábbi értékek egyikére.

- **hamis**: A munkaterület beállítása munkaterület-központú engedélyekre. Ez az alapértelmezett beállítás, ha a jelző nincs beállítva.
- **igaz**: Állítsa a munkaterületet erőforrás-központú engedélyekre.


## <a name="manage-accounts-and-users"></a>Fiókok és felhasználók kezelése
Az adott felhasználóra vonatkozó engedélyeket a munkaterület hozzáférési módja és a munkaterület [hozzáférés-vezérlési módja](#access-control-mode) határozza meg. A **munkaterület engedélyei** akkor lépnek életbe, amikor a felhasználó a munkaterület- **centrikus** munkaterület-központú [módban](#access-modes)fér hozzá bármely munkaterülethez. Az **erőforrás-engedélyek** akkor lépnek életbe, amikor egy felhasználó [erőforrás-centrikus mód](#access-modes)használatával hozzáférés **-** [vezérlési módot](#access-control-mode) használó munkaterülethez fér hozzá.

### <a name="workspace-permissions"></a>Munkaterület engedélyei
Minden munkaterülethez társítva több fiókot lehet, és minden egyes fiók férhet hozzá több munkaterülettel. Access-n keresztül kezelt [Azure szerepköralapú hozzáférés-](../../role-based-access-control/role-assignments-portal.md). 


Az alábbi tevékenységek szintén Azure-engedélyeket igényelnek:

||Műveletek |Azure-engedélyek szükségesek |Megjegyzések |
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



#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Log Analytics munkaterület hozzáférésének kezelése az Azure-engedélyekkel 
Ha Azure-engedélyekkel kíván hozzáférést biztosítani a Log Analytics-munkaterülethez, kövesse [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../../role-based-access-control/role-assignments-portal.md) című rész lépéseit.

Az Azure két beépített felhasználói szerepkörrel rendelkezik Log Analytics munkaterületekhez:
- Log Analytics olvasó
- Log Analytics közreműködő

A *Log Analytics olvasó* szerepkör tagjai a következőket végezhetik el:
- Az összes monitorozási adat megtekintése és keresése 
- A monitorozási beállítások megtekintése, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson.

A Log Analytics olvasó szerepkör az alábbi Azure műveleteket tartalmazza:

| Típus    | Engedély | Leírás |
| ------- | ---------- | ----------- |
| Műveletek | `*/read`   | Lehetővé teszi az összes Azure-erőforrások és erőforrás-konfiguráció megtekintése. Ez a következők megtekintését foglalja magában: <br> Virtuális gépi bővítmény állapota <br> Az Azure Diagnostics konfigurációja az erőforrásokon <br> Az összes erőforrás tulajdonságai és beállításai. <br> A munkaterületek számára lehetővé teszi, hogy a teljes korlátozás alá eső engedélyek beolvassák a munkaterület beállításait, és lekérdezéseket végezzenek az adatokon. Lásd a fenti részletesebb beállításokat. |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Elavult, nem kell hozzárendelni őket a felhasználókhoz. s |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | Elavult, nincs szükség a felhasználókhoz való hozzárendelésre. |
| Action | `Microsoft.Support/*` | Támogatási esetek nyitása |
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
| `*/read`     | Lehetővé teszi az összes Azure-erőforrások és erőforrás-konfiguráció megtekintése. Ez a következők megtekintését foglalja magában: <br> Virtuális gépi bővítmény állapota <br> Az Azure Diagnostics konfigurációja az erőforrásokon <br> Az összes erőforrás tulajdonságai és beállításai. <br> A munkaterületek számára lehetővé teszi, hogy a teljes korlátozás alá eső engedélyek beolvassák a munkaterület beállításait, és lekérdezéseket végezzenek az adatokon. Lásd a fenti részletesebb beállításokat. |
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

A pontos hozzáférés-vezérlés biztosításához az erőforrás szintjén (munkaterület) kell végrehajtania a hozzárendeléseket.  Az [egyéni szerepkörök](../../role-based-access-control/custom-roles.md) segítségével létrehozhat a szükséges egyedi engedélyekkel rendelkező szerepköröket.

### <a name="resource-permissions"></a>Erőforrás-engedélyek 
Ha a felhasználók az erőforrás-központú hozzáférés használatával kérdeznek le naplókat egy munkaterületről, a következő engedélyek lesznek az erőforráshoz:

| Engedély | Leírás |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Példák:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Képes megtekinteni az erőforrás összes naplózási adatforrását.  |
| `Microsoft.Insights/diagnosticSettings/write ` | A diagnosztikai beállítások konfigurálásának lehetősége az erőforráshoz tartozó naplók beállításának engedélyezéséhez. |

Ezt az engedélyt általában olyan szerepkör biztosítja, amely  _\*/Read vagy_ _\*_ engedélyeket tartalmaz, például a beépített [olvasó](../../role-based-access-control/built-in-roles.md#reader) és [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkört. Vegye figyelembe, hogy az adott műveleteket vagy dedikált beépített szerepköröket tartalmazó egyéni szerepkörök nem tartalmazzák ezt az engedélyt.

Ha eltérő hozzáférés-vezérlést szeretne létrehozni különböző táblákhoz, tekintse meg a [táblázatos hozzáférés-vezérlés meghatározása](#table-level-rbac) című szakaszt.


## <a name="table-level-rbac"></a>Táblázat szintű RBAC
A **táblázat szintű RBAC** lehetővé teszi, hogy részletesebb szabályozást nyújtson egy log Analytics munkaterületen lévő adatokhoz a többi engedély mellett. Ez a vezérlő lehetővé teszi olyan konkrét adattípusok meghatározását, amelyek csak egy adott felhasználói csoport számára érhetők el.

A táblázatos hozzáférés-vezérlést az [Egyéni Azure](../../role-based-access-control/custom-roles.md) -szerepkörökkel implementálva megadhatja vagy megtagadhatja a hozzáférést a munkaterület adott [tábláihoz](../log-query/logs-structure.md) . Ezeket a szerepköröket a rendszer a munkaterület-központú vagy az erőforrás-központú [hozzáférés-vezérlési móddal](#access-control-mode) rendelkező munkaterületekre alkalmazza, a felhasználó [hozzáférési módjától](#access-modes)függetlenül.

Hozzon létre egy [Egyéni szerepkört](../../role-based-access-control/custom-roles.md) a következő műveletekkel a Table Access Control elérésének definiálásához.

- Egy táblához való hozzáférés megadásához vegye fel azt a szerepkör-definíció **műveletek** szakaszába.
- Egy táblához való hozzáférés megtagadásához adja meg azt **** a szerepkör-definícióban található, a nem tapintatok szakaszban.
- A * használatával az összes tábla megadására használható.

Ha például egy olyan szerepkört szeretne létrehozni, amely hozzáféréssel __ rendelkezik a szívverési és _AzureActivity_ táblákhoz, hozzon létre egy egyéni szerepkört a következő műveletek használatával:

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
 Az egyéni naplók az adatforrások, például az egyéni naplók és a HTTP-adatgyűjtő API-k alapján jönnek létre. A naplók típusának legegyszerűbb azonosításához ellenőrizze az egyéni naplók területen felsorolt táblákat [a log sémában](../log-query/get-started-portal.md#understand-the-schema).

 Jelenleg nem engedélyezheti vagy tilthatja le az egyéni naplókhoz való hozzáférést, de megadhatja vagy megtagadhatja az összes egyéni napló elérését. Ha az összes egyéni naplóhoz hozzáféréssel rendelkező szerepkört szeretne létrehozni, hozzon létre egy egyéni szerepkört a következő műveletek használatával:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Megfontolandó szempontok

- Ha a felhasználó globális olvasási engedéllyel rendelkezik a  _\*/Read_ műveletet tartalmazó szabványos olvasó vagy közreműködői szerepkörökkel, a rendszer felülbírálja a táblán belüli hozzáférés-vezérlést, és hozzáférést biztosít számukra az összes naplózási adathoz.
- Ha egy felhasználó számára engedélyezett a táblán belüli hozzáférés, de nincs más engedélye, akkor hozzáférhetnek a naplóbeli adatokhoz az API-ból, de nem a Azure Portal. Ahhoz, hogy hozzáférést biztosítson a Azure Portalhoz, használja a Log Analytics olvasót alapszerepkörként.
- Az előfizetés rendszergazdái minden más engedélytől függetlenül hozzáférhetnek az összes adattípushoz.
- A munkaterület tulajdonosai úgy vannak kezelve, mint bármely más felhasználó a táblán belüli hozzáférés-vezérléshez.
- Az egyes felhasználók helyett szerepköröket kell hozzárendelni a biztonsági csoportokhoz a hozzárendelések számának csökkentése érdekében. Ez segítséget nyújt a meglévő csoport-felügyeleti eszközök használatához a hozzáférés konfigurálásához és ellenőrzéséhez.




## <a name="next-steps"></a>További lépések
* Lásd: [Log Analytics-ügynök – áttekintés](../../azure-monitor/platform/log-analytics-agent.md) gyűjthet velük adatokat az adatközpontban vagy egyéb felhőalapú környezetben lévő számítógépekről.
* Az adatgyűjtés Azure-beli virtuális gépekről való konfigurálásáról lásd: [Adatgyűjtés Azure-beli virtuális gépekről](../../azure-monitor/learn/quick-collect-azurevm.md).  

