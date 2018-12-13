---
title: Munkaterületek kezelése az Azure Log Analyticsben és az OMS-portálon | Microsoft Docs
description: Az Azure Log Analytics és az OMS-portál munkaterületeinek kezeléséhez különféle adminisztratív feladatokat végezhet el a felhasználókon, fiókokon, munkaterületeken és Azure-fiókokon.
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
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 71987fcde08c5098d98d21405ce79e61d3094424
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186055"
---
# <a name="manage-workspaces"></a>Munkaterületek kezelése

A Log Analyticshez való hozzáférés kezeléséhez a munkaterületekhez kapcsolódó különféle adminisztratív feladatokat kell végrehajtania. Ez a cikk praktikus tanácsokat és kezelheti a munkaterületeket. A munkaterület lényegében egy tároló, amely a fiók adatait és egyszerű konfigurációs adatait tartalmazza. Ön vagy a szervezet más tagjai több munkaterületet is használhatnak az informatikai infrastruktúra egészéből vagy egyes részeiből begyűjtött különböző adatkészletek kezeléséhez.

Munkaterület létrehozásához az alábbiak szükségesek:

1. Azure-előfizetés.
2. Egy választott név a munkaterületnek.
3. A munkaterület társítása az egyik az előfizetésekhez és erőforráscsoportokhoz.
4. Földrajzi hely kiválasztása.

## <a name="determine-the-number-of-workspaces-you-need"></a>A szükséges munkaterületek számának meghatározása
A munkaterület egy Azure-erőforrás, továbbá egy tároló is, amelyben az adatok összegyűjtése, összesítése, elemzése és az Azure Portalon való megjelenítése történik.

Azure-előfizetésenként több munkaterülettel rendelkezhet, több munkaterülethez is hozzáférhet, és ezek között egyszerűen hajthat végre lekérdezéseket. Ez a szakasz azt ismerteti, hogy mikor lehet hasznos egynél több munkaterület létrehozása.

A munkaterületek jelenleg a következőket biztosítják:

* Egy földrajzi helyet az adattárolás számára
* Az adatok elkülönítését a különböző felhasználói hozzáférési jogosultságokat meghatározása
* A beállítások, például megőrzésére és az adatok kapacitástól konfiguráció hatóköre

Felhasználás szempontjából javasoljuk, hogy a lehető legkevesebb munkaterületek hoz létre. Gyorsabb és egyszerűbb felügyelet és a lekérdezések adatbeolvasási. Azonban a fenti jellemzők alapján, érdemes több munkaterületet létrehozni:

* Globális vállalatként az adatokat elkülönítési vagy megfelelőségi okok miatt külön régiókban kell tárolnia.
* Az Azure-t használja, és a kimenő adatátviteli díjak elkerülése érdekében ugyanabban a régióban szeretne létesíteni egy munkaterületet, amelyikben az általa kezelt Azure-erőforrás is található.
* Szeretné költségeket különböző részlegekre vagy üzleti csoportokra használat alapján hozzon létre egy munkaterületet, az egyes részlegek vagy üzleti csoport a saját Azure-előfizetésében.
* Felügyelt szolgáltatást kínál, és az egyes felügyelt ügyfelek naplóelemzési adatait külön kell tárolnia a többi ügyfél adataitól.
* Több ügyfelet is kezel, és azt szeretné, hogy minden ügyfél / szervezeti egység / üzleti csoportok lássák a saját adatok, de nem más tagjaitól származó adatokat.

Ha az adatgyűjtéshez Windows-ügynököket használ, [konfigurálhatja úgy az egyes ügynököket, hogy egy vagy több munkaterületnek küldjenek jelentést](../../azure-monitor/platform/agent-windows.md).

A System Center Operations Manager használata esetén az Operations Manager egyes felügyeleti csoportjai csak egy munkaterülethez csatlakoztathatók. Telepítheti a Microsoft Monitoring Agentet az Operations Manager által felügyelt számítógépekre, és beállíthatja az ügynököt úgy, hogy az Operations Managernek és egy másik Log Analytics-munkaterületnek is küldjön jelentést.

## <a name="workspace-information"></a>Munkaterület-információk

A munkaterületek adatait az Azure Portalon tekintheti meg. 

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.  

    ![Azure Portal](media/manage-access/azure-portal-01.png)  

3. Az a Log Analytics-előfizetések ablaktábláján válasszon egy munkaterületet.

4. A munkaterület oldalán a kezdeti lépések, a konfiguráció és a hivatkozások további információkat jeleníti meg.  

    ![Munkaterület részletei](./media/manage-access/workspace-overview-page.png)  

## <a name="manage-accounts-and-users"></a>Fiókok és felhasználók kezelése
Minden munkaterülethez társítva több fiókot lehet, és minden egyes fiók férhet hozzá több munkaterülettel. Access-n keresztül kezelt [Azure szerepköralapú hozzáférés-](../../role-based-access-control/role-assignments-portal.md). A hozzáférési jogosultságok vonatkozik, mind az Azure Portalon, és az API-hozzáférés.


Az alábbi tevékenységek szintén Azure-engedélyeket igényelnek:

| Műveletek                                                          | Azure-engedélyek szükségesek | Megjegyzések |
|-----------------------------------------------------------------|--------------------------|-------|
| Felügyeleti megoldások hozzáadása és eltávolítása                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Az engedélyeknek az erőforráscsoport vagy előfizetés szinteken kell megadva lenniük. |
| Tarifacsomag módosítása                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Adatok megtekintése a *Backup* és a *Site Recovery* megoldások csempéin | Rendszergazda / Társadminisztrátor | A klasszikus üzemi modellel üzembe helyezett erőforrásokhoz fér hozzá |
| Munkaterület létrehozása az Azure Portalon                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>A Log Analyticshez való hozzáférés szabályozása Azure-engedélyekkel
Ha Azure-engedélyekkel kíván hozzáférést biztosítani a Log Analytics-munkaterülethez, kövesse [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../../role-based-access-control/role-assignments-portal.md) című rész lépéseit.

Az Azure két beépített felhasználói szerepkört biztosít a Log Analyticshez:
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

Azt javasoljuk, hogy a pontos hozzáférés-vezérlés biztosítása érdekében az erőforrás szintjén (munkaterület) végezze el a hozzárendeléseket.  Az [egyéni szerepkörök](../../role-based-access-control/custom-roles.md) segítségével létrehozhat a szükséges egyedi engedélyekkel rendelkező szerepköröket.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Meglévő munkaterület csatolása Azure-előfizetéshez
Minden 2016. szeptember 26. után létrehozott munkaterület a létrehozásukkor kell társítani egy Azure-előfizetéshez. Az ezen dátum előtt létrehozott munkaterületeket a bejelentkezéskor kell társítani egy munkaterülethez. Amikor az Azure Portalon hoz létre egy munkaterületet, vagy ha munkaterületét egy Azure-előfizetéshez társítja, az Azure Active Directory a szervezeti fiókjaként lesz társítva.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Munkaterület társítása egy Azure-előfizetéshez az Azure Portalon
1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.  

2. A Log Analytics-előfizetések ablaktábláján kattintson **Hozzáadás**.  

    ![munkaterületek listája](./media/manage-access/workspace-link-existing-01.png)

3. Az a **Log Analytics-munkaterület** ablaktáblán kattintson a **meglévő társítása**.  

4. Kattintson a **Kötelező beállítások konfigurálása** lehetőségre.  

5. Megjelenik a munkaterület listája, amelyek még nincsenek társítva az Azure-fiókhoz. Válassza ki a munkaterületet.  
   
6. Ha szükséges, a következő elemek értékeit módosíthatja:
   * Előfizetés
   * Erőforráscsoport
   * Hely
   * Tarifacsomag  

7. Kattintson az **OK** gombra. A munkaterület az Azure-fiókhoz van társítva.

> [!NOTE]
> Ha nem találja a társítani kívánt munkaterületet, akkor az Azure-előfizetése nem rendelkezik hozzáféréssel az OMS portálon létrehozott munkaterülethez.  További információ a fiók OMS-portálról való eléréséről: [Felhasználó hozzáadása meglévő munkaterülethez](#add-a-user-to-an-existing-workspace).
>
>

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Munkaterület frissítése fizetős díjcsomagra
Az OMS három munkaterület csomag típusa van: **Ingyenes**, **önálló**, és **OMS**.  Amennyiben *Ingyenes* díjcsomagot használ, naponta legfeljebb 500 MB adatot küldhet a Log Analytics szolgáltatásnak.  Ha túllépi ezt a mennyiséget, munkaterületét fizetős díjcsomagra kell módosítania, hogy ennél több adatot gyűjthessen be. A díjcsomag típusa bármikor módosítható.  Az OMS díjszabásról a [díjszabás](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing) leírásában tájékozódhat.

### <a name="using-entitlements-from-an-oms-subscription"></a>Az OMS-előfizetésből származó jogosultságok használata
Ha szeretné használni az OMS E1, OMS E2 OMS vagy a System Center OMS-bővítményének megvásárlásából származó jogosultságokat, válassza az OMS Log Analytics *OMS* csomagját.

OMS-előfizetés megvásárlásakor a jogosultságok bekerülnek a Nagyvállalati szerződésbe. A megállapodás hatálya alatt létrehozott Azure-előfizetések mind rendelkeznek ezekkel a jogosultságokkal. Ezen előfizetés összes munkaterülete OMS-jogosultságokat használ.

Ha biztosítani kívánja, hogy a munkaterületén érvényesek legyenek az OMS-előfizetésből származó jogosultságok, a következőket kell tennie:

1. Olyan Azure-előfizetésben hozza létre a munkaterületet, amely az OMS-előfizetést tartalmazó Nagyvállalati szerződés része

2. *OMS* adatforgalmi díjcsomag kiválasztása a munkaterület számára

> [!NOTE]
> Amennyiben munkaterületét 2016. szeptember 26. előtt hozta létre, és *Prémium* szintű Log Analytics tarifacsomagot használ, akkor ez a munkaterület a System Center OMS-bővítményéből származó jogosultságokat fogja használni. Jogosultságait az *OMS* tarifacsomagra való váltással is használhatja.
>
>

Az OMS-előfizetés jogosultságai nem láthatók az Azure Portalon. A jogosultságokat és használatot az Enterprise Portalon tekintheti meg.  

Ha módosítania kell az Azure-előfizetést, amelyhez a munkaterületet társította, használja az Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) parancsmagját.

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Azure kötelezettségvállalás használata egy Nagyvállalati szerződésből
Amennyiben Ön nem rendelkezik OMS-előfizetéssel, az OMS egyes összetevőiért külön-külön kell fizetnie, és a használat megjelenik az Azure-számláján.

Ha rendelkezik Azure pénzügyi kerettel a nagyvállalati beléptetés részeként, amelyhez az Azure-előfizetést társította, akkor a Log Analytics automatikusan felhasználja az esetleg rendelkezésre álló pénzügyi keretet.

Ha módosítania kell az Azure-előfizetést, amelyhez a munkaterületet társította, használja az Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) parancsmagját.  

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-azure-portal"></a>Munkaterület módosítása fizetős tarifacsomagra az Azure Portalon
1. Az Azure Portalon a Log Analytics-előfizetések panelen válasszon ki egy munkaterületet.

2. A munkaterület ablaktáblán alatt **általános**válassza **tarifacsomag**.  

3. A **tarifacsomag**válassza ki a tarifacsomagot, majd kattintson a **kiválasztása**.  
    ![A kijelölt tarifacsomag](./media/manage-access/workspace-pricing-tier-info.png)

> [!NOTE]
> Ha a munkaterület Automation-fiókhoz van társítva, az *Önálló (GB-alapú)* tarifacsomag kiválasztása előtt törölnie kell az összes **Automation and Control** megoldást, és meg kell szüntetnie az Automation-fiók társítását. A megoldások megtekintéséhez és törléséhez kattintson a munkaterület panel **Általános** területén a **Megoldások** elemre. Az Automation-fiók társításának megszüntetéséhez kattintson az Automation-fiók nevére a **Tarifacsomag** panelen.
>
>

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-oms-portal"></a>Munkaterület módosítása fizetős tarifacsomagra az OMS-portálon

A tarifacsomagnak az OMS-portál használatával történő módosításához rendelkeznie kell Azure-előfizetéssel.

1. Az OMS-portálon kattintson a **Beállítások** csempére.

2. Kattintson a **Fiókok** fülre, majd az **Azure-előfizetés és adatforgalmi díjcsomag** fülre.

3. Kattintson a használni kívánt tarifacsomagra.

4. Kattintson a **Save** (Mentés) gombra.  

    ![előfizetés és adatforgalmi díjcsomagok](./media/manage-access/subscription-tab.png)

Az új adatforgalmi díjcsomag megjelenik az OMS-portál menüszalagján a webhely felső részén.

![OMS menüszalag](./media/manage-access/data-plan-changed.png)

## <a name="next-steps"></a>További lépések
* Lásd: [Log Analytics-ügynök – áttekintés](../../azure-monitor/platform/log-analytics-agent.md) gyűjthet velük adatokat az adatközpontban vagy egyéb felhőalapú környezetben lévő számítógépekről.
* Az adatgyűjtés Azure-beli virtuális gépekről való konfigurálásáról lásd: [Adatgyűjtés Azure-beli virtuális gépekről](../../azure-monitor/learn/quick-collect-azurevm.md).  
* A funkciók hozzáadásával és az adatgyűjtéssel kapcsolatban lásd: [Add Log Analytics solutions from the Solutions Gallery](../../azure-monitor/insights/solutions.md) (Log Analytics-megoldások hozzáadása a megoldástárból).

