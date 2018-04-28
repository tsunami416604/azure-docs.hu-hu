---
title: Munkaterületek kezelése az Azure Log Analyticsben | Microsoft Docs
description: Az Azure Log Analytics munkaterületeinek kezeléséhez különféle adminisztratív feladatokat futtathat a felhasználókon, fiókokon, munkaterületeken és Azure-fiókokon.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2017
ms.author: magoedte
ms.openlocfilehash: af648e97f5913ef7413f72db8e19aa5ea69d6d09
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="manage-workspaces"></a>Munkaterületek kezelése

A Log Analyticshez való hozzáférés kezeléséhez a munkaterületekhez kapcsolódó különféle adminisztratív feladatokat kell végrehajtania. Ez a témakör praktikus tanácsokat és ajánlott eljárásokat tartalmaz a munkaterületek kezelésére vonatkozóan. A munkaterület lényegében egy tároló, amely a fiók adatait és egyszerű konfigurációs adatait tartalmazza. Ön vagy a szervezet más tagjai több munkaterületet is használhatnak az informatikai infrastruktúra egészéből vagy egyes részeiből begyűjtött különböző adatkészletek kezeléséhez.

Munkaterület létrehozásához az alábbiak szükségesek:

1. Azure-előfizetés.
2. Egy választott név a munkaterületnek.
3. A munkaterület társítása az előfizetéshez.
4. Földrajzi hely kiválasztása.

## <a name="determine-the-number-of-workspaces-you-need"></a>A szükséges munkaterületek számának meghatározása
A munkaterület egy Azure-erőforrás, továbbá egy tároló is, amelyben az adatok összegyűjtése, összesítése, elemzése és az Azure Portalon való megjelenítése történik.

Azure-előfizetésenként több munkaterülettel rendelkezhet, és több munkaterülethez is hozzáférhet. Korábban csak az aktuális munkaterületen belüli adatokat lehetett elemezni, ami korlátozta az előfizetésben megadott többi munkaterület lekérdezését. Most már [több munkaterületről hajthat végre lekérdezést](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-cross-workspace-search), így a teljes rendszerre kiterjedően megtekintheti az adatait. Ez a szakasz azt ismerteti, hogy mikor lehet hasznos egynél több munkaterület létrehozása.

A munkaterületek jelenleg a következőket biztosítják:

* Egy földrajzi helyet az adattárolás számára
* A számlázáshoz szükséges részletes adatokat
* Az adatok elkülönítését
* Konfiguráció hatóköre

A fenti jellemzők alapján a következő esetekben lehet érdemes több munkaterületet létrehozni:

* Globális vállalatként az adatokat elkülönítési vagy megfelelőségi okok miatt külön régiókban kell tárolnia.
* Az Azure-t használja, és a kimenő adatátviteli díjak elkerülése érdekében ugyanabban a régióban szeretne létesíteni egy munkaterületet, amelyikben az általa kezelt Azure-erőforrás is található.
* Használat alapján szeretné meghatározni a különböző részlegekre vagy üzleti csoportokra eső költségeket. Ha külön munkaterületet hoz létre az egyes részlegek vagy üzleti csoportok számára, az Azure-számlán és a használati kimutatásban külön jelennek meg az egyes munkaterületekre vonatkozó költségek.
* Felügyelt szolgáltatást kínál, és az egyes felügyelt ügyfelek Log Analytics-adatait külön kell tárolnia a többi ügyfél adataitól.
* Több ügyfelet is kezel, és azt szeretné, hogy az egyes ügyfelek, részlegek és üzleti csoportok lássák ugyan a saját adataikat, de a másokét ne.

Ha az adatgyűjtéshez ügynököket használ, [konfigurálhatja úgy az egyes ügynököket, hogy egy vagy több munkaterületnek küldjenek jelentést](log-analytics-windows-agent.md).

A System Center Operations Manager használata esetén az Operations Manager egyes felügyeleti csoportjai csak egy munkaterülethez csatlakoztathatók. Azonban a számítógépre telepített Microsoft Monitoring Agentet úgy is beállíthatja, hogy az Operations Managernek és egy másik Log Analytics-munkaterületnek is küldjön jelentést.  

### <a name="workspace-information"></a>Munkaterület-információk

A munkaterületek adatait az Azure Portalon tekintheti meg. 

#### <a name="view-workspace-information-in-the-azure-portal"></a>Munkaterületek adatainak megtekintése az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **Minden szolgáltatás** lehetőségre.  Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Kattintson a **Log Analytics** elemre.  
    ![Képernyőkép az Azure bal oldali menüjéről](./media/log-analytics-manage-access/hub.png)  
3. Válasszon egy munkaterületet a Log Analytics-előfizetések lapon.
4. A munkaterület oldalán a munkaterület adatai és a további információkra mutató hivatkozások szerepelnek.  
    ![munkaterület részletei](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Fiókok és felhasználók kezelése
Minden munkaterülethez több fiókot lehet hozzárendelni, és minden egyes fiók (Microsoft-fiók vagy szervezeti fiók) több munkaterülethez férhet hozzá.

Az alapértelmezett beállítások szerint a munkaterületet létrehozó Microsoft-fiók vagy szervezeti fiók lesz a munkaterület rendszergazdája.

A Log Analytics-munkaterületekhez történő hozzáférés két engedélyezési modellel szabályozható:

1. Örökölt Log Analytics felhasználói szerepkörök
2. [Azure szerepkör-alapú hozzáférés](../role-based-access-control/role-assignments-portal.md)

Az alábbi táblázat az egyes engedélyezési modellekkel beállítható hozzáféréseket foglalja össze:

|                          | Log Analytics-portál | Azure Portal | API (a PowerShellt is beleértve) |
|--------------------------|----------------------|--------------|----------------------------|
| Log Analytics-beli felhasználói szerepkörök | Igen                  | Nem           | Nem                         |
| Azure szerepkör-alapú hozzáférés  | Igen                  | Igen          | Igen                        |

> [!NOTE]
> A Log Analytics engedélyezési modellje a Log Analytics-beli felhasználói szerepkörök helyett az Azure-szerepkörökön alapuló hozzáférésre tér át.
>
>

A korábbi Log Analytics felhasználói szerepkörök kizárólag a [Log Analytics-portálon](https://mms.microsoft.com) végrehajtott tevékenységekhez való hozzáférést szabályozzák.

Az alábbi tevékenységek szintén Azure-engedélyeket igényelnek:

| Műveletek                                                          | Azure-engedélyek szükségesek | Megjegyzések |
|-----------------------------------------------------------------|--------------------------|-------|
| Felügyeleti megoldások hozzáadása és eltávolítása                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | |
| Tarifacsomag módosítása                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Adatok megtekintése a *Backup* és a *Site Recovery* megoldások csempéin | Rendszergazda / Társadminisztrátor | A klasszikus üzemi modellel üzembe helyezett erőforrásokhoz fér hozzá |
| Munkaterület létrehozása az Azure Portalon                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>A Log Analyticshez való hozzáférés szabályozása Azure-engedélyekkel
Ha Azure-engedélyekkel kíván hozzáférést biztosítani a Log Analytics-munkaterülethez, kövesse [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../role-based-access-control/role-assignments-portal.md) című rész lépéseit.

Az Azure két beépített felhasználói szerepkört biztosít a Log Analyticshez:
- Log Analytics olvasó
- Log Analytics közreműködő

A *Log Analytics olvasó* szerepkör tagjai a következőket végezhetik el:
- Az összes monitorozási adat megtekintése és keresése 
- A monitorozási beállítások megtekintése, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson.

| Típus    | Engedély | Leírás |
| ------- | ---------- | ----------- |
| Műveletek | `*/read`   | Az összes erőforrás és erőforrás-konfiguráció megtekintése. Ez a következők megtekintését foglalja magában: <br> Virtuális gépi bővítmény állapota <br> Az Azure Diagnostics konfigurációja az erőforrásokon <br> Az összes erőforrás tulajdonságai és beállításai |
| Műveletek | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Log Search v2-lekérdezések végrehajtása |
| Műveletek | `Microsoft.OperationalInsights/workspaces/search/action` | Log Search v1-lekérdezések végrehajtása |
| Műveletek | `Microsoft.Support/*` | Támogatási esetek nyitása |
|Nem művelet | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Meggátolja az adatgyűjtési API használatához és az ügynökök telepítéséhez szükséges munkaterületkulcsok olvasását |


A *Log Analytics közreműködő* szerepkör tagjai a következőket végezhetik el:
- Az összes monitorozási adat olvasása 
- Automation-fiókok létrehozása és konfigurálása
- Felügyeleti megoldások hozzáadása és eltávolítása
- A tárfiókkulcsok olvasása 
- Az Azure Storage-naplók gyűjtésének konfigurálása
- Azure-erőforrások monitorozási beállításainak szerkesztése, beleértve a következőket:
  - Virtuális gépi bővítmények hozzáadása a virtuális gépekhez
  - Az Azure Diagnostics konfigurálása az összes Azure-erőforráson

> [!NOTE] 
> Ezzel a funkcióval virtuális gépi bővítményt adhat a virtuális gépekhez a virtuális gép teljes körű irányítása érdekében.

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

Az [egyéni szerepkörök](../role-based-access-control/custom-roles.md) segítségével létrehozhat a szükséges egyedi engedélyekkel rendelkező szerepköröket.

### <a name="azure-user-roles-and-log-analytics-portal-user-roles"></a>Az Azure felhasználói szerepkörei és a Log Analytics-portál felhasználói szerepkörei
Ha legalább olvasási Azure-engedéllyel rendelkezik a Log Analytics-munkaterületen, az **OMS-portál** feladatra kattintva nyithatja meg az OMS-portált a Log Analytics-munkaterület megtekintésekor.

Az OMS-portál megnyitásával átvált a korábbi Log Analytics-felhasználói szerepkörök használatára. Ha nem rendelkezik szerepkör-hozzárendeléssel a Log Analytics-portálon, a szolgáltatás [ellenőrzi a munkaterületre érvényes Azure-engedélyeit](https://docs.microsoft.com/rest/api/authorization/permissions#Permissions_ListForResource).
Az OMS-portálon érvényes szerepkör-hozzárendelés meghatározása a következők használatával történik:

| Feltételek                                                   | Hozzárendelt Log Analytics-beli felhasználói szerepkör | Megjegyzések |
|--------------------------------------------------------------|----------------------------------|-------|
| A fiókja egy örökölt Log Analytics-beli felhasználói szerepkörhöz tartozik     | A megadott Log Analytics-beli felhasználói szerepkör | |
| A fiókja nem tartozik örökölt Log Analytics-beli felhasználói szerepkörhöz <br> Teljes körű Azure-engedélyek a munkaterülethez (`*` engedély <sup>1</sup>) | Rendszergazda ||
| A fiókja nem tartozik örökölt Log Analytics-beli felhasználói szerepkörhöz <br> Teljes körű Azure-engedélyek a munkaterülethez (`*` engedély <sup>1</sup>) <br> *nem a következő* műveletek: `Microsoft.Authorization/*/Delete` és `Microsoft.Authorization/*/Write` | Közreműködő ||
| A fiókja nem tartozik örökölt Log Analytics-beli felhasználói szerepkörhöz <br> Azure-beli olvasási engedély | Csak olvasási engedély ||
| A fiókja nem tartozik örökölt Log Analytics-beli felhasználói szerepkörhöz <br> Az Azure-engedélyeket nem ismerhetők fel | Csak olvasási engedély ||
| Felhőszolgáltató (CSP) által kezelt előfizetések esetén <br> A bejelentkezéshez használt fiók a munkaterülettel társított Azure Active Directoryban található | Rendszergazda | Jellemzően egy felhőszolgáltató ügyfele |
| Felhőszolgáltató (CSP) által kezelt előfizetések esetén <br> A bejelentkezéshez használt fiók a munkaterülettel társított Azure Active Directoryban található | Közreműködő | Jellemzően a felhőszolgáltató |

<sup>1</sup>A szerepkör-meghatározásokról az [Azure-engedélyeket](../role-based-access-control/custom-roles.md) ismertető cikk nyújt részletesebb tájékoztatást. A szerepkörök kiértékelésekor a `*` és a `Microsoft.OperationalInsights/workspaces/*` műveletek nem egyenértékűek.

Néhány dolog, amit érdemes észben tartani az Azure Portal kapcsán:

* Ha a http://mms.microsoft.com webhelyről jelentkezik be az OMS-portálra, akkor a **Munkaterület kiválasztása** lista jelenik meg. Ez a lista csak azokat a munkaterületeket tartalmazza, ahol Log Analytics felhasználói szerepkörrel rendelkezik. Az Azure-előfizetéssel elérhető munkaterületek csak akkor lesznek láthatók, ha az URL-cím részeként megad egy bérlőt. Például: `mms.microsoft.com/?tenant=contoso.com`. A bérlőazonosító gyakran a bejelentkezéshez használt e-mail-cím utolsó része.
* Amennyiben közvetlenül kíván elérni egy portált, amelyhez Azure-engedélyekkel rendelkezik, akkor meg kell adni az erőforrást az URL-cím részeként. Ezt az URL-címet a PowerShell használatával is elő lehet állítani.

  Például: `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  Az URL-cím az alábbihoz hasonló: `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`

### <a name="managing-users-in-the-oms-portal"></a>Az OMS-portál felhasználóinak kezelése
A felhasználók és csoportok kezelése a Beállítások oldal **Fiókok** lapjának **Felhasználók kezelése** fülére kattintva történik.   

![felhasználók kezelése](./media/log-analytics-manage-access/setup-workspace-manage-users.png)


#### <a name="add-a-user-to-an-existing-workspace"></a>Felhasználó hozzáadása meglévő munkaterülethez
A következő lépésekkel adhat hozzá egy felhasználót vagy csoportot egy munkaterülethez.

1. Az OMS-portálon kattintson a **Beállítások** csempére.
2. Kattintson a **Fiókok** fülre, majd a **Felhasználók kezelése** fülre.
3. A **Felhasználók kezelése** részen válassza ki a hozzáadni kívánt fióktípust: **Szervezeti fiók**, **Microsoft-fiók**, **Microsoft ügyfélszolgálat**.

   * Amennyiben a Microsoft-fiók lehetőséget választja, írja be Microsoft-fiókhoz tartozó felhasználó e-mail-címét.
   * Ha a Szervezeti fiók lehetőséget választja, elég beírnia a felhasználó vagy csoport nevének vagy e-mail-aliasának egy részét, és a legördülő menüben megjelenik a megfelelő felhasználók és csoportok listája. Válasszon ki egy felhasználót vagy csoportot.
   * A Microsoft ügyfélszolgálatán keresztül átmeneti hozzáférést biztosíthat a munkaterületéhez a Microsoft ügyfélszolgálat egyik szakemberének vagy más alkalmazottjának, hogy el tudja végezni a hibaelhárítást.

     > [!NOTE]
     > Az optimális teljesítmény érdekében legfeljebb három Active Directory-csoport legyen társítva egy OMS-fiókhoz. Az egyik legyen a rendszergazdák, a második a közreműködők, a harmadik pedig a csak olvasási hozzáféréssel rendelkező felhasználók csoportja. Ha ennél több csoportot használ, az befolyásolhatja a Log Analytics teljesítményét.
     >
     >
4. Válassza ki a hozzáadni kívánt felhasználó vagy csoport típusát: **Rendszergazda**, **Közreműködő** vagy **ReadOnly felhasználó**.  
5. Kattintson a **Hozzáadás** parancsra.

   Microsoft-fiók hozzáadásakor egy meghívót kap a munkaterület használatára a korábban megadott e-mail címére. Ha a felhasználó követi az OMS-meghívóban leírtakat, hozzáférhet a munkaterülethez.
   Szervezeti fiók hozzáadásakor a felhasználó azonnal hozzáférést kaphat a Log Analytics megoldáshoz.  

#### <a name="edit-an-existing-user-type"></a>Meglévő felhasználótípus hozzáadása
Az OMS-fiókjával lehetősége van módosítani egy társított felhasználó fiókjának szerepét. A következő szerepek közül választhat:

* *Rendszergazda*: Kezelheti a felhasználókat, látja a riasztásokat és reagálhat rájuk, hozzáadhat és eltávolíthat kiszolgálókat.
* *Közreműködő*: Látja a riasztásokat és reagálhat rájuk, hozzáadhat és eltávolíthat kiszolgálókat.
* *ReadOnly felhasználó*: A csak olvasási jogosultsággal rendelkező felhasználók nem tudják végrehajtani a következőket:

  1. Megoldások hozzáadása és eltávolítása. A megoldástár nem látható.
  2. Csempék hozzáadása, módosítása és eltávolítása a **Saját irányítópult** felületen.
  3. A **Beállítások** oldalak megtekintése. Ezek az oldalak rejtve maradnak.
  4. A Keresés nézetben a PowerBI konfigurálása, a Mentett keresések és a Riasztás feladatok rejtve maradnak.

#### <a name="to-edit-an-account"></a>Fiók szerkesztése
1. Az OMS-portálon kattintson a **Beállítások** csempére.
2. Kattintson a **Fiókok** fülre, majd a **Felhasználók kezelése** fülre.
3. Jelölje ki a felhasználó módosítani kívánt szerepét.
4. A megerősítési párbeszédpanelen kattintson az **Igen** gombra.

### <a name="remove-a-user-from-a-workspace"></a>Felhasználó eltávolítása a munkaterületről
A következő lépésekkel távolíthat el egy felhasználót egy munkaterületről. A felhasználó eltávolításával nem záródik be a munkaterület. Csupán eltávolítja a felhasználó és a munkaterület közötti társítást. Ha egy felhasználóhoz több munkaterület is társítva van, a felhasználó továbbra is bejelentkezhet az OMS-be, hogy megtekintse a többi munkaterületet.

1. Az OMS-portálon kattintson a **Beállítások** csempére.
2. Kattintson a **Fiókok** fülre, majd a **Felhasználók kezelése** fülre.
3. Kattintson az eltávolítani kívánt felhasználó neve mellett található **Eltávolítás** gombra.
4. A megerősítési párbeszédpanelen kattintson az **Igen** gombra.

### <a name="add-a-group-to-an-existing-workspace"></a>Csoport hozzáadása meglévő munkaterülethez
1. Kövesse az előző „Felhasználó hozzáadása meglévő munkaterülethez” című szakasz 1-4. lépésében megadottakat.
2. A **Felhasználó vagy csoport kiválasztása** pontban válassza a **Csoport** lehetőséget.  
   ![csoport hozzáadása meglévő munkaterülethez](./media/log-analytics-manage-access/add-group.png)
3. Írja be a hozzáadni kívánt csoport megjelenítendő nevét vagy e-mail-címét.
4. Jelölje ki a csoportot a kapott listán, majd kattintson a **Hozzáadás** gombra.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Meglévő munkaterület csatolása Azure-előfizetéshez
Minden 2016. szeptember 26. után létrehozott munkaterület a létrehozásukkor kell társítani egy Azure-előfizetéshez. Az ezen dátum előtt létrehozott munkaterületeket a bejelentkezéskor kell társítani egy előfizetéshez. Amikor az Azure Portalon hoz létre egy munkaterületet, vagy ha munkaterületét egy Azure-előfizetéshez társítja, az Azure Active Directory a szervezeti fiókjaként lesz társítva.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Munkaterület társítása egy Azure-előfizetéshez az OMS-portálon

- Amikor bejelentkezik az OMS-portálra, a rendszer felkéri egy Azure-előfizetés kiválasztására. Jelölje ki a munkaterülethez kapcsolni kívánt előfizetést, majd kattintson az **Összekapcsolás** gombra.  
    ![Azure-előfizetés társítása](./media/log-analytics-manage-access/required-link.png)

    > [!IMPORTANT]
    > Munkaterület társításához az Azure-fióknak már hozzáféréssel kell rendelkeznie a társítani kívánt munkaterülethez.  Az Azure Portal eléréséhez tehát **ugyanazt** a fiókot kell használnia, mint a munkaterület eléréséhez. Ha ez nem igaz, kövesse a [Felhasználó hozzáadása meglévő munkaterülethez](#add-a-user-to-an-existing-workspace) szakasz lépéseit.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Munkaterület társítása egy Azure-előfizetéshez az Azure Portalon
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Keresse meg a **Log Analytics** elemet, majd jelölje ki.
3. Ekkor megjelenik a meglévő munkaterületek listája. Kattintson a **Hozzáadás** parancsra.  
   ![munkaterületek listája](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4. Az **OMS-munkaterület** részen kattintson a **Vagy meglévő társítása** gombra.  
   ![meglévő társítása](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5. Kattintson a **Kötelező beállítások konfigurálása** lehetőségre.  
   ![kötelező beállítások megadása](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6. Megjelenik a munkaterület listája, amelyek még nincsenek társítva az Azure-fiókhoz. Jelöljön ki egy munkaterületet.  
   ![munkaterület kijelölése](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7. Ha szükséges, a következő elemek értékeit módosíthatja:
   * Előfizetés
   * Erőforráscsoport
   * Hely
   * Tarifacsomag  
     ![értékek módosítása](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8. Kattintson az **OK** gombra. A munkaterület az Azure-fiókhoz van társítva.

> [!NOTE]
> Ha nem találja a társítani kívánt munkaterületet, akkor az Azure-előfizetése nem rendelkezik hozzáféréssel az OMS portálon létrehozott munkaterülethez.  További információ a fiók OMS-portálról való eléréséről: [Felhasználó hozzáadása meglévő munkaterülethez](#add-a-user-to-an-existing-workspace).
>
>

## <a name="change-an-azure-active-directory-organization-for-a-workspace"></a>Munkaterülethez tartozó Azure Active Directory-szervezet módosítása

Igény szerint módosíthatja a munkaterülethez tartozó Azure Active Directory-szervezetet. Az Azure Active Directory-szervezet módosításával felhasználókat és csoportokat adhat hozzá a munkaterülethez az adott címtárból.

### <a name="to-change-the-azure-active-directory-organization-for-a-workspace"></a>Munkaterülethez tartozó Azure Active Directory-szervezet módosítása

1. Az OMS-portál Beállítások lapján kattintson a **Fiókok** fülre, majd a **Felhasználók kezelése** fülre.  
2. Tekintse át a szervezeti fiókok adatait, majd kattintson a **Szervezet módosítása** elemre.  
    ![szervezet módosítása](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Adja meg Azure Active Directory-tartománya rendszergazdájának identitását. Ezután megjelenik a visszaigazolás, hogy munkaterülete társítva lett Azure Active Directory-tartományához.  
    ![társított munkaterületek visszaigazolása](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

## <a name="next-steps"></a>További lépések
* [Az adathasználat értelmezését](log-analytics-usage.md) ismertető cikkből megtudhatja, hogyan elemezze a számítógépekről küldött és a megoldások által összegyűjtött adatköteteket.
* A [Log Analytics-felügyeleti megoldások az Azure Piactérről történő hozzáadását](log-analytics-add-solutions.md) ismertető cikk alapján új funkciókat adhat hozzá és adatokat gyűjthet.
