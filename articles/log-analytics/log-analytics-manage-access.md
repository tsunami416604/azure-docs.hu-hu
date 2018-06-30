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
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 3b4e0f978cc7d23d0157b78fd2dff27096d2768b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132925"
---
# <a name="manage-workspaces"></a>Munkaterületek kezelése

A Log Analyticshez való hozzáférés kezeléséhez a munkaterületekhez kapcsolódó különféle adminisztratív feladatokat kell végrehajtania. Ez a témakör praktikus tanácsokat és ajánlott eljárásokat tartalmaz a munkaterületek kezelésére vonatkozóan. A munkaterület lényegében egy tároló, amely a fiók adatait és egyszerű konfigurációs adatait tartalmazza. Ön vagy a szervezet más tagjai több munkaterületet is használhatnak az informatikai infrastruktúra egészéből vagy egyes részeiből begyűjtött különböző adatkészletek kezeléséhez.

Munkaterület létrehozásához az alábbiak szükségesek:

1. Azure-előfizetés.
2. Egy választott név a munkaterületnek.
3. A munkaterület társítani a előfizetésekhez és erőforráscsoportokhoz egyikét.
4. Földrajzi hely kiválasztása.

## <a name="determine-the-number-of-workspaces-you-need"></a>A szükséges munkaterületek számának meghatározása
A munkaterület egy Azure-erőforrás, továbbá egy tároló is, amelyben az adatok összegyűjtése, összesítése, elemzése és az Azure Portalon való megjelenítése történik.

Azure-előfizetésenként több munkaterülettel rendelkezhet, több munkaterülethez is hozzáférhet, és ezek között egyszerűen hajthat végre lekérdezéseket. Ez a szakasz azt ismerteti, hogy mikor lehet hasznos egynél több munkaterület létrehozása.

A munkaterületek jelenleg a következőket biztosítják:

* Egy földrajzi helyet az adattárolás számára
* Adatok elkülönítése különböző felhasználói hozzáférési jogosultságokat meghatározásához
* Például a megőrzési és határértékét adatok konfiguráció hatókör

A felhasználás szempontjából ajánlott a lehető legkevesebb munkaterületek létrehozásához. A felügyeleti teszi, és a lekérdezés élményének egyszerűbb és gyorsabb. De az előző jellemzők alapján, érdemes lehet több munkaterületek létrehozására, ha:

* Globális vállalatként az adatokat elkülönítési vagy megfelelőségi okok miatt külön régiókban kell tárolnia.
* Az Azure-t használja, és a kimenő adatátviteli díjak elkerülése érdekében ugyanabban a régióban szeretne létesíteni egy munkaterületet, amelyikben az általa kezelt Azure-erőforrás is található.
* Használat alapján szeretné meghatározni a különböző részlegekre vagy üzleti csoportokra eső költségeket. Amikor hoz létre minden részleg vagy üzleti csoport egy munkaterület a saját Azure-előfizetésben.
* Felügyelt szolgáltatást kínál, és az egyes felügyelt ügyfelek naplóelemzési adatait külön kell tárolnia a többi ügyfél adataitól.
* Több ügyfelet is kezel, és azt szeretné, hogy az egyes ügyfelek, részlegek és üzleti csoportok lássák ugyan a saját adataikat, de a másokét ne.

Ha az adatgyűjtéshez Windows-ügynököket használ, [konfigurálhatja úgy az egyes ügynököket, hogy egy vagy több munkaterületnek küldjenek jelentést](log-analytics-windows-agents.md).

A System Center Operations Manager használata esetén az Operations Manager egyes felügyeleti csoportjai csak egy munkaterülethez csatlakoztathatók. Telepítheti a Microsoft Monitoring Agentet az Operations Manager által felügyelt számítógépekre, és beállíthatja az ügynököt úgy, hogy az Operations Managernek és egy másik Log Analytics-munkaterületnek is küldjön jelentést.

### <a name="workspace-information"></a>Munkaterület-információk

A munkaterületek adatait az Azure Portalon tekintheti meg. 

#### <a name="view-workspace-information-in-the-azure-portal"></a>Munkaterületek adatainak megtekintése az Azure Portalon

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com) Azure-előfizetésével.
2. A **központi** menüben kattintson a **További szolgáltatások** elemre, majd az erőforrások listájába írja be a következőt: **Log Analytics**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Kattintson a **Log Analytics** elemre.  
    ![Azure-központ](./media/log-analytics-manage-access/hub.png)  
3. Válasszon egy munkaterületet a Log Analytics-előfizetések panelen.
4. A munkaterület paneljén a munkaterület adatai és a további információkra mutató hivatkozások szerepelnek.  
    ![munkaterület részletei](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Fiókok és felhasználók kezelése
Egyes munkaterületeken rendelkezhet több fiók társítva, és minden felhasználói fiókhoz is elérheti az több munkaterületek. Hozzáférés-féle felügyelete [Azure szerepköralapú hozzáférés-](../active-directory/role-based-access-control-configure.md). A következő hozzáférési jogokat vonatkozik, mind az Azure portálon, és az API-hozzáférést.


Az alábbi tevékenységek szintén Azure-engedélyeket igényelnek:

| Műveletek                                                          | Azure-engedélyek szükségesek | Megjegyzések |
|-----------------------------------------------------------------|--------------------------|-------|
| Felügyeleti megoldások hozzáadása és eltávolítása                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Az engedélyeknek az erőforráscsoport vagy előfizetés szinteken kell megadva lenniük. |
| Tarifacsomag módosítása                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Adatok megtekintése a *Backup* és a *Site Recovery* megoldások csempéin | Rendszergazda / Társadminisztrátor | A klasszikus üzemi modellel üzembe helyezett erőforrásokhoz fér hozzá |
| Munkaterület létrehozása az Azure Portalon                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>A Log Analyticshez való hozzáférés szabályozása Azure-engedélyekkel
Ha Azure-engedélyekkel kíván hozzáférést biztosítani a Log Analytics-munkaterülethez, kövesse [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../active-directory/role-based-access-control-configure.md) című rész lépéseit.

Az Azure két beépített felhasználói szerepkört biztosít a Log Analyticshez:
- Log Analytics olvasó
- Log Analytics közreműködő

A *Log Analytics olvasó* szerepkör tagjai a következőket végezhetik el:
- Az összes monitorozási adat megtekintése és keresése 
- A monitorozási beállítások megtekintése, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson.

A napló Analytics olvasó szerepkört a következő Azure műveleteket tartalmazza:

| Típus    | Engedély | Leírás |
| ------- | ---------- | ----------- |
| Műveletek | `*/read`   | Az összes Azure-erőforrások és erőforrás-konfigurációval megtekintésének képességét. Ez a következők megtekintését foglalja magában: <br> Virtuális gépi bővítmény állapota <br> Az Azure Diagnostics konfigurációja az erőforrásokon <br> Az összes erőforrás tulajdonságai és beállításai |
| Műveletek | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Log Search v2-lekérdezések végrehajtása |
| Műveletek | `Microsoft.OperationalInsights/workspaces/search/action` | Log Search v1-lekérdezések végrehajtása |
| Műveletek | `Microsoft.Support/*` | Támogatási esetek nyitása |
|Nem művelet | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Megakadályozza, hogy a munkaterület olvasása az adatgyűjtést API használatával, és az ügynökök telepítéséhez szükséges kulcs. Ez megakadályozza, hogy a felhasználó új erőforrások hozzáadása a munkaterület |


A *Log Analytics közreműködő* szerepkör tagjai a következőket végezhetik el:
- Összes figyelési adatokat olvasni, a napló Analytics olvasó is  
- Automation-fiókok létrehozása és konfigurálása  
- Felügyeleti megoldások hozzáadása és eltávolítása    
    > [!NOTE] 
    > Ahhoz, hogy sikeresen végezze el az utolsó két műveleteket, ezt az engedélyt kell adni az erőforrás csoport vagy az előfizetés szintjén.  

- A tárfiókkulcsok olvasása   
- Az Azure Storage-naplók gyűjtésének konfigurálása  
- Azure-erőforrások monitorozási beállításainak szerkesztése, beleértve a következőket:
  - Virtuális gépi bővítmények hozzáadása a virtuális gépekhez
  - Az Azure Diagnostics konfigurálása az összes Azure-erőforráson

> [!NOTE] 
> Ezzel a funkcióval virtuális gépi bővítményt adhat a virtuális gépekhez a virtuális gép teljes körű irányítása érdekében.

A napló Analytics közreműködő szerepkört a következő Azure műveleteket tartalmazza:

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

Azt javasoljuk, hogy a pontos hozzáférés-vezérlés biztosítása érdekében az erőforrás szintjén (munkaterület) végezze el a hozzárendeléseket.  Az [egyéni szerepkörök](../active-directory/role-based-access-control-custom-roles.md) segítségével létrehozhat a szükséges egyedi engedélyekkel rendelkező szerepköröket.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Meglévő munkaterület csatolása Azure-előfizetéshez
Minden 2016. szeptember 26. után létrehozott munkaterület a létrehozásukkor kell társítani egy Azure-előfizetéshez. Az ezen dátum előtt létrehozott munkaterületeket a bejelentkezéskor kell társítani egy munkaterülethez. Amikor az Azure Portalon hoz létre egy munkaterületet, vagy ha munkaterületét egy Azure-előfizetéshez társítja, az Azure Active Directory a szervezeti fiókjaként lesz társítva.

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

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Munkaterület frissítése fizetős díjcsomagra
Az OMS három munkaterület díjcsomagtípust ismer: **Ingyenes**, **Önálló** és **OMS**.  Amennyiben *Ingyenes* díjcsomagot használ, naponta legfeljebb 500 MB adatot küldhet a Log Analytics szolgáltatásnak.  Ha túllépi ezt a mennyiséget, munkaterületét fizetős díjcsomagra kell módosítania, hogy ennél több adatot gyűjthessen be. A díjcsomag típusa bármikor módosítható.  Az OMS díjszabásról a [díjszabás](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing) leírásában tájékozódhat.

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

Az OMS-előfizetés jogosultságai nem jelennek meg az Azure- vagy OMS-portálon. A jogosultságokat és használatot az Enterprise Portalon tekintheti meg.  

Ha módosítania kell az Azure-előfizetést, amelyhez a munkaterületet társította, használja az Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) parancsmagját.

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Azure kötelezettségvállalás használata egy Nagyvállalati szerződésből
Amennyiben Ön nem rendelkezik OMS-előfizetéssel, az OMS egyes összetevőiért külön-külön kell fizetnie, és a használat megjelenik az Azure-számláján.

Ha rendelkezik Azure pénzügyi kerettel a nagyvállalati beléptetés részeként, amelyhez az Azure-előfizetést társította, akkor a Log Analytics automatikusan felhasználja az esetleg rendelkezésre álló pénzügyi keretet.

Ha módosítania kell az Azure-előfizetést, amelyhez a munkaterületet társította, használja az Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) parancsmagját.  

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-azure-portal"></a>Munkaterület módosítása fizetős tarifacsomagra az Azure Portalon
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Keresse meg a **Log Analytics** elemet, majd jelölje ki.
3. Ekkor megjelenik a meglévő munkaterületek listája. Jelöljön ki egy munkaterületet.  
4. A munkaterület panel **Általános** területén kattintson a **Tarifacsomag** elemre.  
5. A **Tarifacsomag** lapon kattintással jelölje ki a tarifacsomagot, majd kattintson a **Kiválasztás** gombra.  
    ![díjcsomag kiválasztása](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6. Amikor frissíti az Azure Portal nézetét, láthatja, hogy a **Tarifacsomag** a választott csomagra módosult.  
    ![frissített csomag](./media/log-analytics-manage-access/manage-access-change-plan04.png)

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
   ![előfizetés és adatforgalmi díjcsomagok](./media/log-analytics-manage-access/subscription-tab.png)

Az új adatforgalmi díjcsomag megjelenik az OMS-portál menüszalagján a webhely felső részén.

![OMS menüszalag](./media/log-analytics-manage-access/data-plan-changed.png)


## <a name="change-how-long-log-analytics-stores-data"></a>A Log Analytics adattárolási idejének módosítása

Az ingyenes tarifacsomagban a Log Analytics az utolsó hét nap adatait teszi elérhetővé.
A standard tarifacsomagban a Log Analytics az utolsó 30 nap adatait teszi elérhetővé.
A prémium tarifacsomagban a Log Analytics az utolsó 365 nap adatait teszi elérhetővé.
Az önálló és OMS tarifacsomagban a Log Analytics alapértelmezés szerint az utolsó 31 nap adatait teszi elérhetővé.

Az önálló és az OMS tarifacsomag használata esetén akár 2 évig (730 napig) is megőrizheti az adatokat. Az alapértelmezett 31 napnál tovább tárolt adatokért adatmegőrzési díjat számolunk fel. Az árakkal kapcsolatos további információkért lásd a [többletköltségek](https://azure.microsoft.com/pricing/details/log-analytics/) című szakaszt.

Az adatmegőrzés hosszának módosításáról lásd: [A költségek kezelése az adatmennyiség és megőrzés vezérlésével a Log Analyticsben](log-analytics-manage-cost-storage.md).


## <a name="delete-a-log-analytics-workspace"></a>Log Analytics-munkaterület törlése
Ha töröl egy Log Analytics-munkaterületet, 30 napon belül a munkaterülethez kapcsolódó összes adat törlődik a Log Analytics szolgáltatásból.

Ha Ön rendszergazda, és több felhasználó van társítva a munkaterülethez, a felhasználók és a munkaterület közötti társítás megszűnik. Ha a felhasználók más munkaterületekkel is társítva vannak, továbbra is használhatják a Log Analyticset azokkal a munkaterületekkel. Ha azonban nincsenek más munkaterületekkel társítva, a szolgáltatás használatához létre kell hozniuk egy munkaterületet. A munkaterületek törléséről lásd: [Azure Log Analytics-munkaterület törlése](log-analytics-manage-del-workspace.md)

## <a name="next-steps"></a>További lépések
* Az adatközpontban vagy más felhőalapú környezetben lévő számítógépekről való adatgyűjtésről lásd: [Adatok gyűjtése a környezetben lévő számítógépekről a Log Analytics használatával](log-analytics-concept-hybrid.md).
* Az adatgyűjtés Azure-beli virtuális gépekről való konfigurálásáról lásd: [Adatgyűjtés Azure-beli virtuális gépekről](log-analytics-quick-collect-azurevm.md).  
* A funkciók hozzáadásával és az adatgyűjtéssel kapcsolatban lásd: [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Log Analytics-megoldások hozzáadása a megoldástárból).

