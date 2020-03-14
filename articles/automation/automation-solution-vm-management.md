---
title: Start/Stop VMs during off-hours megoldás
description: Ez a virtuálisgép-felügyeleti megoldás elindítja és leállítja a Azure Resource Manager virtuális gépeket az ütemterven, és proaktív módon figyeli Azure Monitor naplókból.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2020
ms.topic: conceptual
ms.openlocfilehash: cbf181b9a6d3860854c7b61cca0e6c50810cced9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278544"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Start/Stop VMs during off-hours megoldás a Azure Automation

A Start/Stop VMs during off-hours megoldás az Azure-beli virtuális gépek felhasználó által meghatározott ütemterveken való elindítását és leállítását, Azure Monitor naplókon keresztüli elemzéseket tesz lehetővé, és nem kötelező e-maileket küld a [műveleti csoportok](../azure-monitor/platform/action-groups.md)használatával. A legtöbb esetben a Azure Resource Manager és a klasszikus virtuális gépeket is támogatja. A megoldás klasszikus virtuális gépekkel való használatához klasszikus futtató fiókra van szükség, amely alapértelmezés szerint nem jön létre. Klasszikus futtató fiók létrehozásával kapcsolatos utasításokért tekintse meg a [klasszikus futtató fiókok](automation-create-standalone-account.md#classic-run-as-accounts)című témakört.

> [!NOTE]
> A Start/Stop VMs during off-hours megoldás az Automation-fiókba importált Azure-modulokkal lett tesztelve a megoldás telepítésekor. A megoldás jelenleg nem működik az Azure-modul újabb verzióival. Ez csak az Start/Stop VMs during off-hours megoldás futtatásához használt Automation-fiókra vonatkozik. Továbbra is használhatja az Azure-modul újabb verzióit a többi Automation-fiókban a következő témakörben ismertetett módon: [Azure PowerShell modulok frissítése Azure Automation](automation-update-azure-modules.md)

Ez a megoldás egy decentralizált, alacsony költségű automatizálási lehetőséget biztosít azon felhasználók számára, akik optimalizálni szeretnék a virtuális gépek költségeit. Ezzel a megoldással a következőket teheti:

- Virtuális gépek indítása és leállítása.
- A virtuális gépeket úgy ütemezhet, hogy az Azure-címkék használatával növekvő sorrendben induljon el és álljon le (a klasszikus virtuális gépek nem támogatottak).
- Virtuális gépek alapleállása alacsony CPU-használat alapján.

A jelenlegi megoldás korlátai a következők:

- Ez a megoldás bármely régióban felügyeli a virtuális gépeket, de csak a Azure Automation-fiókkal megegyező előfizetésben használható.
- Ez a megoldás az Azure-ban és a AzureGov bármely olyan régió számára elérhető, amely támogatja a Log Analytics munkaterületet, a Azure Automation-fiókot és a riasztásokat. A AzureGov-régiók jelenleg nem támogatják az e-mail-funkciókat.

> [!NOTE]
> Ha a megoldást a klasszikus virtuális gépekhez használja, akkor az összes virtuális gép soronként lesz feldolgozva a Cloud Service-ben. A virtuális gépeket a rendszer továbbra is párhuzamosan dolgozza fel a különböző felhőalapú szolgáltatások között. Ha a Cloud Service-ben több mint 20 virtuális gép van, javasoljuk, hogy több ütemtervet hozzon létre a szülő runbook **ScheduledStartStop_Parent** és ütemezzen 20 virtuális gépet. Az ütemterv tulajdonságainál válassza a vesszővel tagolt listaként a virtuális gépek nevét a **VMList** paraméterben. Ellenkező esetben, ha a megoldás automatizálási feladata több mint három órát futtat, átmenetileg el lesz távolítva vagy leállítva a [méltányos megosztási](automation-runbook-execution.md#fair-share) korláton belül.
>
> Azure Cloud Solution Provider (Azure CSP) előfizetések csak a Azure Resource Manager modellt támogatják, a nem Azure Resource Manager szolgáltatások nem érhetők el a programban. Ha a Start/Stop megoldás fut, előfordulhat, hogy hibákat kap, mivel parancsmagokkal rendelkezik a klasszikus erőforrások kezeléséhez. A CSP-vel kapcsolatos további tudnivalókért tekintse meg a [CSP-előfizetésekben elérhető szolgáltatások](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)című témakört. Ha CSP-előfizetést használ, az üzembe helyezés után a [**External_EnableClassicVMs**](#variables) változót **false (hamis** ) értékre kell módosítania.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

A megoldás runbookok egy Azure-beli [futtató fiókkal](automation-create-runas-account.md)működik. A futtató fiók az előnyben részesített hitelesítési módszer, mert a tanúsítvány-hitelesítést használ olyan jelszó helyett, amely esetleg lejáró vagy gyakran változhat.

Javasoljuk, hogy használjon külön Automation-fiókot a virtuálisgép-indítási és leállítási megoldáshoz. Ennek az az oka, hogy az Azure-modul verziói gyakran frissülnek, és a paraméterek változhatnak. A virtuálisgép-indítási és-leállítási megoldás nem frissül ugyanazon a ritmuson, így előfordulhat, hogy az általa használt parancsmagok újabb verziói nem működnek. Javasoljuk továbbá, hogy tesztelje a modul frissítéseit egy tesztelési Automation-fiókban, mielőtt azokat az üzemi Automation-fiók (ok) ba importálja.

### <a name="permissions-needed-to-deploy"></a>A telepítéséhez szükséges engedélyek

Bizonyos engedélyek szükségesek ahhoz, hogy a felhasználóknak a virtuális gépek elindítása/leállítása a munkaidőn kívüli megoldáson keresztül történjen. Ezek az engedélyek eltérőek, ha egy előre létrehozott Automation-fiókot és Log Analytics munkaterületet használ, vagy újakat hoz létre az üzembe helyezés során. Ha Ön az előfizetéshez tartozó közreműködő, és a Azure Active Directory bérlő globális rendszergazdája, nem kell konfigurálnia a következő engedélyeket. Ha nem rendelkezik ezekkel a jogosultságokkal, és nem kell egyéni szerepkört konfigurálnia, tekintse meg az alább szükséges engedélyeket.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Már meglévő Automation-fiók és Log Analytics munkaterület

Ha a virtuális gépek elindítása/leállítása a munkaidőn kívüli megoldásban egy meglévő Automation-fiókra és Log Analytics munkaterületre kívánja telepíteni, a megoldást telepítő felhasználónak a következő engedélyekkel kell rendelkeznie az **erőforráscsoporthoz**. A szerepkörökkel kapcsolatos további tudnivalókért tekintse meg [Az Azure-erőforrások egyéni szerepkörei](../role-based-access-control/custom-roles.md)című témakört.

| Engedély | Hatókör|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Erőforráscsoport |
| Microsoft. Automation/automationAccounts/változók/írás | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/schedules/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/runbooks/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/connections/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/certificates/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/modules/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/modules/read | Erőforráscsoport |
| Microsoft.automation/automationAccounts/jobSchedules/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/jobs/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/jobs/read | Erőforráscsoport |
| Microsoft.OperationsManagement/solutions/write | Erőforráscsoport |
| Microsoft. OperationalInsights/munkaterületek/* | Erőforráscsoport |
| Microsoft. bepillantások/diagnosticSettings/írás | Erőforráscsoport |
| Microsoft.Insights/ActionGroups/Write | Erőforráscsoport |
| Microsoft.Insights/ActionGroups/read | Erőforráscsoport |
| Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoport |
| Microsoft.Resources/deployments/* | Erőforráscsoport |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Új Automation-fiók és új Log Analytics munkaterület

Ha a virtuális gépek elindítása/leállítása a munkaidőn kívüli megoldásban egy új Automation-fiókra és Log Analytics munkaterületre kívánja telepíteni, a megoldást telepítő felhasználónak az előző szakaszban meghatározott engedélyekkel, valamint a következő engedélyekkel kell rendelkeznie:

- Társ-rendszergazda az előfizetésben – csak akkor kell létrehoznia a klasszikus futtató fiókot, ha a klasszikus virtuális gépeket fogja kezelni. A [klasszikus futtató fiókok](automation-create-standalone-account.md#classic-run-as-accounts) már nem jönnek létre alapértelmezés szerint.
- A [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) **alkalmazás fejlesztői** szerepkörének tagja. A futtató fiókok konfigurálásával kapcsolatos további információkért lásd: [engedélyek a futtató fiókok konfigurálására](manage-runas-account.md#permissions).
- Közreműködő az előfizetésen vagy a következő engedélyeken.

| Engedély |Hatókör|
| --- | --- |
| Microsoft.Authorization/Operations/read | Előfizetést|
| Microsoft. Authorization/engedélyek/olvasás |Előfizetést|
| Microsoft.Authorization/roleAssignments/read | Előfizetést |
| Microsoft.Authorization/roleAssignments/write | Előfizetést |
| Microsoft.Authorization/roleAssignments/delete | Előfizetést |
| Microsoft.Automation/automationAccounts/connections/read | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/certificates/read | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/write | Erőforráscsoport |
| Microsoft.OperationalInsights/workspaces/write | Erőforráscsoport |

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

A következő lépések végrehajtásával adja hozzá a Start/Stop VMs during off-hours megoldást az Automation-fiókjához, majd konfigurálja a változókat a megoldás testreszabásához.

1. Egy Automation-fiókból válassza a **virtuális gép indítása/leállítása** **kapcsolódó erőforrások**lehetőséget. Innen kattintson **a további információ és a megoldás engedélyezése**lehetőségre. Ha már van üzembe helyezett indítási/leállítási virtuálisgép-megoldás, kiválaszthatja a **megoldás kezelése** elemre kattintva, és megkeresheti azt a listában.

   ![Engedélyezés az Automation-fiókból](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Azt is megteheti, hogy az **erőforrás létrehozása**elemre kattintva a Azure Portal bárhonnan létrehozhatja. A piactér lapon írjon be egy kulcsszót (például **Indítás** vagy **Indítás/Leállítás**). Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Azt is megteheti, hogy egy vagy több kulcsszót ír be a megoldás teljes nevéből, majd lenyomja az ENTER billentyűt. A keresési eredmények közül válassza a **Start/Stop VMS During off-hours** lehetőséget.

2. A kiválasztott megoldás **Start/Stop VMS During off-hours** oldalán tekintse át az összegzési információkat, majd kattintson a **Létrehozás**gombra.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

3. Megjelenik a **megoldás hozzáadása** lap. A rendszer felszólítja a megoldás konfigurálására, mielőtt importálni tudja az Automation-előfizetésbe.

   ![Virtuálisgép-felügyelet – megoldás hozzáadása lap](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. A **megoldás hozzáadása** lapon válassza a **munkaterület**lehetőséget. Válasszon olyan Log Analytics munkaterületet, amely ugyanahhoz az Azure-előfizetéshez van csatolva, amelyhez az Automation-fiók tartozik. Ha nem rendelkezik munkaterülettel, válassza az **Új munkaterület létrehozása**lehetőséget. A **log Analytics munkaterület** lapon hajtsa végre a következő lépéseket:
   - Adja meg az új **log Analytics munkaterület**nevét, például: "ContosoLAWorkspace".
   - Válassza ki azt az **előfizetést** , amelyre hivatkozni szeretne a legördülő listából, ha az alapértelmezett beállítás nem megfelelő.
   - **Erőforráscsoport**esetén létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévőt.
   - Válasszon ki egy **helyet**.
   - Válasszon egy tarifacsomagot a **Tarifacsomag** területen. Válassza a **GB-os (önálló)** lehetőséget. Azure Monitor naplók frissültek a [díjszabással](https://azure.microsoft.com/pricing/details/log-analytics/) , és a GB-onként az egyetlen lehetőség.

   > [!NOTE]
   > A megoldások engedélyezésekor csak bizonyos régiók esetén lehet összekapcsolni egy Log Analytics-munkaterületet és egy Automation-fiókot.
   >
   > A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](how-to/region-mappings.md).

5. A szükséges információk megadása után a **log Analytics munkaterület** lapon kattintson a **Létrehozás**gombra. A menü **értesítések** részén nyomon követheti a folyamat állapotát, amely visszaadja a **megoldás hozzáadása** lapra, ha elkészült.
6. A **megoldás hozzáadása** lapon válassza az **Automation-fiók**lehetőséget. Ha új Log Analytics munkaterületet hoz létre, létrehozhat egy új Automation-fiókot, amelyhez társítva van, vagy olyan meglévő Automation-fiókot választhat, amely még nincs Log Analytics-munkaterülethez csatolva. Válasszon ki egy meglévő Automation-fiókot, vagy kattintson az Automation-fiók **létrehozása**lehetőségre, majd az **Automation-fiók hozzáadása** lapon adja meg a következő információkat:
   - A **Név** mezőbe írja be az Automation-fiók nevét.

     A rendszer a kijelölt Log Analytics munkaterület alapján automatikusan kitölti az összes többi beállítást. Ezek a beállítások nem módosíthatók. A megoldásban szereplő runbookok alapértelmezett hitelesítési módszere egy Azure-futtatófiók. Miután rákattintott az **OK gombra**, a rendszer érvényesíti a konfigurációs beállításokat, és létrehozza az Automation-fiókot. Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását.

7. Végül a **megoldás hozzáadása** oldalon válassza a **Konfigurálás**lehetőséget. Megjelenik a **Paraméterek** lap.

   ![Paraméterek lap a megoldáshoz](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   A rendszer a következőket kéri:
   - Határozza meg a **cél ResourceGroup nevét**. Ezek az értékek olyan erőforráscsoport-nevek, amelyek a megoldás által felügyelni kívánt virtuális gépeket tartalmazzák. Több nevet is megadhat, és vesszővel elválaszthatja őket (az értékek nem megkülönböztetik a kis-és nagybetűket). Helyettesítő karaktert is használhat, ha az előfizetés összes erőforráscsoportjában lévő virtuális gépeket szeretné megadni. Ezt az értéket a **External_Start_ResourceGroupNames** és a **External_Stop_ResourceGroupNames** változó tárolja.
   - Határozza meg a **virtuális gép kizárási listáját (karakterlánc)** . Ez az érték egy vagy több virtuális gép neve a cél erőforráscsoporthoz. Több nevet is megadhat, és vesszővel elválaszthatja őket (az értékek nem megkülönböztetik a kis-és nagybetűket). Helyettesítő karakter használata támogatott. Ezt az értéket a **External_ExcludeVMNames** változó tárolja.
   - Válasszon ki egy **ütemtervet**. Adja meg az ütemterv dátumát és időpontját. A rendszer a kiválasztott időponttól kezdve ismétlődő napi ütemtervet hoz létre. Egy másik régió kiválasztása nem érhető el. Ha a megoldás konfigurálása után szeretné beállítani az ütemtervet a megadott időzónára, tekintse meg [az indítási és leállítási ütemterv módosítása](#modify-the-startup-and-shutdown-schedules)című témakört.
   - Ha **e-mail-értesítéseket** szeretne kapni egy műveleti csoportból, fogadja el az alapértelmezett értéket **, és adjon** meg egy érvényes e-mail-címet. Ha a **nem** lehetőséget választja, de az e-mail-értesítések fogadását követően később dönt, akkor az érvényes e-mail-címekkel létrehozott [műveleti csoportot](../azure-monitor/platform/action-groups.md) a vesszővel elválasztva frissítheti. A következő riasztási szabályokat is engedélyeznie kell:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > A **célként megadott ResourceGroup** alapértelmezett értéke **&ast;** . Ez egy előfizetésben lévő összes virtuális gépet célozza meg. Ha nem szeretné, hogy a megoldás az előfizetésben lévő összes virtuális gépet megcélozza, ezt az értéket frissíteni kell az erőforráscsoport-nevek listájára az ütemtervek engedélyezése előtt.

8. Miután konfigurálta a megoldáshoz szükséges kezdeti beállításokat, kattintson az **OK** gombra a **Paraméterek** lap bezárásához, majd válassza a **Létrehozás**lehetőséget. Az összes beállítás érvényesítése után a megoldás üzembe lesz helyezve az előfizetésében. Ez a folyamat több másodpercig is eltarthat, és a menü **értesítések** részén nyomon követheti a folyamat állapotát.

> [!NOTE]
> Ha Azure Cloud Solution Provider (Azure CSP) előfizetéssel rendelkezik, az üzembe helyezés befejezése után az Automation-fiókjában lépjen a **változók** elemre a **megosztott erőforrások** területen, és állítsa a [**External_EnableClassicVMs**](#variables) változót **hamis**értékre. Ezzel leállítja a megoldást a klasszikus virtuálisgép-erőforrások keresésére.

## <a name="scenarios"></a>Forgatókönyvek

A megoldás három különböző forgatókönyvet tartalmaz. Ezek a forgatókönyvek a következők:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>1\. forgatókönyv: virtuális gépek indítása/leállítása ütemterv szerint

Ez a forgatókönyv az alapértelmezett konfiguráció a megoldás első telepítésekor. Beállíthatja például, hogy leállítsa az összes virtuális gépet egy előfizetésen belül, amikor az esti munkát elhagyja, és reggel, amikor visszatért az irodába. Ha az üzembe helyezés során ütemezi a **ütemezett StartVM** és az **ütemezett StopVM** , a rendszer elindítja és leállítja a célként megadott virtuális gépeket. Ha ezt a megoldást csak a virtuális gépek leállítására szeretné konfigurálni, tekintse meg [az indítási és leállítási ütemtervek módosítása](#modify-the-startup-and-shutdown-schedules) témakört, amelyből megtudhatja, hogyan konfigurálhat egyéni

> [!NOTE]
> Az időzóna az aktuális időzóna, amikor konfigurálja az ütemterv időpontja paramétert. A Azure Automation azonban UTC formátumban van tárolva. Nem szükséges időzóna-átalakítást végeznie, mivel ezt a rendszer az üzembe helyezés során kezeli.

A következő változók konfigurálásával szabályozhatja, hogy mely virtuális gépek tartoznak a hatókörbe: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**és **External_ExcludeVMNames**.

Engedélyezheti a műveletet egy előfizetéshez és az erőforráscsoporthoz, vagy megcélozhatja a virtuális gépek egy adott listáját, de mindkettőt nem.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>A kezdési és a leállítási műveletek célzása egy előfizetéshez és az erőforráscsoporthoz

1. Konfigurálja a **External_Stop_ResourceGroupNames** és **External_ExcludeVMNames** változókat a célként megadott virtuális gépek megadásához.
2. Az **ütemezett StartVM** és az **ütemezett StopVM** ütemezések engedélyezése és frissítése.
3. Futtassa a **ScheduledStartStop_Parent** runbook a **Start** értékre beállított Action paraméterrel, és a WHATIF paraméter értéke **true (igaz** ), hogy megtekintse a módosításokat.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>A Start és a stop művelet megcélzása virtuálisgép-lista alapján

1. Futtassa a **ScheduledStartStop_Parent** runbook a **Start**értékre beállított Action paraméterrel, adja hozzá a virtuális gépek vesszővel tagolt listáját a *VMList* paraméterben, majd állítsa a WHATIF paramétert **true (igaz**) értékre. A módosítások előnézete.
1. Konfigurálja a **External_ExcludeVMNames** paramétert a virtuális gépek vesszővel tagolt listájával (VM1, VM2, VM3).
1. Ez a forgatókönyv nem tartja tiszteletben a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupnames** változókat. Ebben a forgatókönyvben létre kell hoznia a saját automatizálási ütemtervét. Részletekért lásd: [Runbook ütemezése Azure Automationban](../automation/automation-schedules.md).

> [!NOTE]
> A célként megadott **ResourceGroup-nevek** értéke a **External_Start_ResourceGroupNames** és a **External_Stop_ResourceGroupNames**értékének megfelelően tárolódik. A további részletesség érdekében módosíthatja ezeket a változókat a különböző erőforráscsoportok megcélzásához. Az indítási művelethez használja a **External_Start_ResourceGroupNames**, és a Leállítás művelethez használja a **External_Stop_ResourceGroupNames**. A virtuális gépek automatikusan hozzáadódnak az indítási és leállítási ütemtervekhez.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>2\. forgatókönyv: virtuális gépek indítása és leállítása címkék használatával

Egy olyan környezetben, amely több, elosztott munkaterhelést támogató virtuális gépen található kettő vagy több összetevőt tartalmaz, és amely támogatja az összetevők indításának és leállításának sorrendjét. Ezt a forgatókönyvet a következő lépések végrehajtásával hajthatja végre:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>A kezdési és a leállítási műveletek célzása egy előfizetéshez és az erőforráscsoporthoz

1. Adjon hozzá egy **sequencestart** és egy **sequencestop** -címkét pozitív egész értékkel olyan virtuális gépekhez, amelyek **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupNames** változókat céloznak meg. A kezdési és a leállítási műveletek növekvő sorrendben lesznek végrehajtva. A virtuális gépek címkézésével kapcsolatos további információkért lásd: [Windows rendszerű virtuális gép címkézése az Azure-ban](../virtual-machines/windows/tag.md) , és Linux rendszerű [virtuális gép címkézése az Azure-ban](../virtual-machines/linux/tag.md).
1. Módosítsa az ütemezett **StartVM** és a **sorozatos StopVM** az igényeinek megfelelő dátumra és időpontra, és engedélyezze az ütemtervet.
1. Futtassa a **SequencedStartStop_Parent** runbook a **Start** értékre beállított Action paraméterrel, és a WHATIF paraméter értéke **true (igaz** ), hogy megtekintse a módosításokat.
1. Tekintse meg a műveletet, és végezze el a szükséges módosításokat az éles virtuális gépeken való megvalósítás előtt. Ha elkészült, manuálisan hajtsa végre a runbook a (z) paraméterrel a **false**értékre állítva, vagy hagyja, hogy az automatizálás ütemezett **StartVM** és **szekvenciális StopVM** automatikusan fusson az előírt ütemezése után.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>A Start és a stop művelet megcélzása virtuálisgép-lista alapján

1. Adjon hozzá egy **sequencestart** és egy **sequencestop** -címkét pozitív egész értékkel a **VMList** paraméterhez hozzáadni kívánt virtuális gépekhez.
1. Futtassa a **SequencedStartStop_Parent** runbook a **Start**értékre beállított Action paraméterrel, adja hozzá a virtuális gépek vesszővel tagolt listáját a *VMList* paraméterben, majd állítsa a WHATIF paramétert **true (igaz**) értékre. A módosítások előnézete.
1. Konfigurálja a **External_ExcludeVMNames** paramétert a virtuális gépek vesszővel tagolt listájával (VM1, VM2, VM3).
1. Ez a forgatókönyv nem tartja tiszteletben a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupnames** változókat. Ebben a forgatókönyvben létre kell hoznia a saját automatizálási ütemtervét. Részletekért lásd: [Runbook ütemezése Azure Automationban](../automation/automation-schedules.md).
1. Tekintse meg a műveletet, és végezze el a szükséges módosításokat az éles virtuális gépeken való megvalósítás előtt. Ha elkészült, manuálisan hajtsa végre a monitoring-and-Diagnostics/monitoring-Action-groupsrunbook paramétert a **false**értékre állítva, vagy hagyja, hogy az automatizálási ütemezett **StartVM** és **szekvenciális StopVM** automatikusan fusson az előírt ütemezése után.

## <a name="solution-components"></a>Megoldás-összetevők

Ez a megoldás előre konfigurált runbookok, ütemterveket és integrációt tartalmaz Azure Monitor naplókkal, így az üzleti igényeknek megfelelően testre szabható a virtuális gépek indítása és leállítása.

### <a name="runbooks"></a>Runbookok

A következő táblázat felsorolja a megoldás által az Automation-fiókba telepített runbookok. Ne módosítsa a runbook-kódot. Ehelyett saját runbook írhat az új funkciókhoz.

> [!IMPORTANT]
> Ne futtasson közvetlenül semmilyen olyan runbook, amelynek a neve hozzá van fűzve a "Child" kifejezéssel.

Az összes szülő runbookok tartalmazza a _whatIf_ paramétert. Ha **igaz**értékre van állítva, a _whatIf_ támogatja a runbook által az _whatIf_ paraméter nélkül futtatott pontos viselkedést, és ellenőrzi, hogy a megfelelő virtuális gépeket célozza-e meg. A runbook csak akkor hajtja végre a definiált műveleteit, ha a _whatIf_ paraméter értéke **false (hamis**).

|Forgatókönyv | Paraméterek | Leírás|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Meghívva a szülő runbook. Ez a runbook a riasztásokat erőforrás-alapon hozza létre az autostop forgatókönyvhöz.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: true vagy FALSE  | Létrehozza vagy frissíti az Azure riasztási szabályokat a célként megadott előfizetésben vagy erőforráscsoportok virtuális gépeken. <br> VMList: a virtuális gépek vesszővel tagolt listája. Például: _VM1, VM2, vm3_.<br> A *whatIf* végrehajtás nélkül érvényesíti a runbook logikát.|
|AutoStop_Disable | Nincs | Letiltja az autostop riasztásokat és az alapértelmezett ütemtervet.|
|AutoStop_StopVM_Child | WebHookData | Meghívva a szülő runbook. A riasztási szabályok ezt a runbook hívják meg a virtuális gép leállítására.|
|Bootstrap_Main | Nincs | Egyszer használatos a rendszerindítási konfigurációk, például a webhookURI beállításához, amelyek általában nem érhetők el Azure Resource Managerból. A sikeres telepítés után a rendszer automatikusan eltávolítja ezt a runbook.|
|ScheduledStartStop_Child | VMName <br> Művelet: indítás vagy leállítás <br> ResourceGroupName | Meghívva a szülő runbook. Végrehajt egy indítási vagy leállítási műveletet az ütemezett leállítás előtt.|
|ScheduledStartStop_Parent | Művelet: indítás vagy leállítás <br>VMList <br> WhatIf: true vagy FALSE | Ez a beállítás hatással van az előfizetésben lévő összes virtuális gépre. Szerkessze a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupNames** , hogy csak ezen a célcsoportokon legyen végrehajtva. Az **External_ExcludeVMNames** változó frissítésével meghatározott virtuális gépeket is kizárhat.<br> VMList: a virtuális gépek vesszővel tagolt listája. Például: _VM1, VM2, vm3_.<br> A _whatIf_ végrehajtás nélkül érvényesíti a runbook logikát.|
|SequencedStartStop_Parent | Művelet: indítás vagy leállítás <br> WhatIf: true vagy FALSE<br>VMList| Hozzon létre a **sequencestart** és a **sequencestop** nevű címkéket minden olyan virtuális gépen, amelynek el szeretné készíteni a Start/Stop tevékenységeket. A címkék nevei megkülönböztetik a kis-és nagybetűket. A címke értékének pozitív egész számnak (1, 2, 3) kell lennie, amely megfelel az elindítani vagy leállítani kívánt sorrendnek. <br> VMList: a virtuális gépek vesszővel tagolt listája. Például: _VM1, VM2, vm3_. <br> A _whatIf_ végrehajtás nélkül érvényesíti a runbook logikát. <br> **Megjegyzés**: a virtuális gépeknek External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames és External_ExcludeVMNames Azure Automation változókban definiált erőforrás-csoportokon belül kell lenniük. A megfelelő címkékkel kell rendelkezniük a műveletek életbe léptetéséhez.|

### <a name="variables"></a>Változók

A következő táblázat felsorolja az Automation-fiókban létrehozott változókat. Csak a **külső**előrögzített változók módosítása. A **belső** előrögzített változók módosítása nem kívánt effektusokat eredményez.

|Változó | Leírás|
|---------|------------|
|External_AutoStop_Condition | Egy riasztás elindítása előtt a feltétel konfigurálásához szükséges feltételes operátor. Elfogadható értékek: **GreaterThan**, **GreaterThanOrEqual**, **LessThan**és **LessThanOrEqual**.|
|External_AutoStop_Description | A virtuális gép leállítására vonatkozó riasztás, ha a CPU-hányad meghaladja a küszöbértéket.|
|External_AutoStop_MetricName | Annak a teljesítménymutatónak a neve, amelyhez az Azure-riasztási szabályt konfigurálni kell.|
|External_AutoStop_Threshold | Az _External_AutoStop_MetricName_változóban megadott Azure riasztási szabály küszöbértéke. A százalékos értékek 1-től 100-ig terjedhetnek.|
|External_AutoStop_TimeAggregationOperator | Az időösszesítési operátor, amelyet a rendszer a kijelölt ablak méretére alkalmaz a feltétel kiértékeléséhez. Az elfogadható értékek: **Average**, **min**, **Max**, **Total**és **Last**.|
|External_AutoStop_TimeWindow | Az ablak mérete, amely alatt az Azure elemzi a kiválasztott mérőszámokat a riasztások aktiválásához. Ez a paraméter TimeSpan formátumban fogadja a bemenetet. A lehetséges értékek 5 perc és 6 óra között vannak.|
|External_EnableClassicVMs| Azt határozza meg, hogy a klasszikus virtuális gépeket a megoldás célozza-e. Az alapértelmezett érték True. Ezt a beállítást hamis értékre kell állítani a CSP-előfizetések esetében. A klasszikus virtuális gépek [klasszikus futtató fiókot](automation-create-standalone-account.md#classic-run-as-accounts)igényelnek.|
|External_ExcludeVMNames | Adja meg a kizárni kívánt virtuális gépek nevét, a neveket szóközök nélkül vesszővel elválasztva. Ez legfeljebb 140 virtuális gépre vonatkozik. Ha több mint 140 virtuális gépet ad hozzá ehhez a vesszővel tagolt listához, a kizárni kívánt virtuális gépeket a rendszer véletlenül elindíthatja vagy leállíthatja.|
|External_Start_ResourceGroupNames | Megad egy vagy több erőforráscsoportot, amely az értékeket vesszővel, a kezdési műveletekhez megcélozva választja el.|
|External_Stop_ResourceGroupNames | Megad egy vagy több erőforráscsoportot, amely az értékeket vesszővel, a leállítási műveletekhez megcélozva választja el.|
|Internal_AutomationAccountName | Megadja az Automation-fiók nevét.|
|Internal_AutoSnooze_WebhookUri | A webhook URI azonosítóját adja meg az autostop forgatókönyvhöz.|
|Internal_AzureSubscriptionId | Megadja az Azure-előfizetés AZONOSÍTÓját.|
|Internal_ResourceGroupName | Az Automation-fiók erőforráscsoport-nevét adja meg.|

Az összes forgatókönyvben a **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**és **External_ExcludeVMNames** változók szükségesek a virtuális gépek célzásához, kivéve, ha a virtuális gépek vesszővel tagolt listáját biztosítják a **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**és **ScheduledStartStop_Parent** runbookok. Ez azt eredményezi, hogy a virtuális gépeknek meg kell egyezniük a cél erőforráscsoportok között az indítási és leállítási műveletek elvégzéséhez. A logika az Azure Policy szolgáltatáshoz hasonlóan működik, és az előfizetést vagy az erőforráscsoportot is megcélozhatja, és az újonnan létrehozott virtuális gépek által örökölt műveleteket is elvégezheti. Ezzel a módszerrel elkerülhető, hogy minden virtuális gép esetében külön ütemtervet kell fenntartani, és a felügyelet megkezdődik, és a méretezési folyamat leáll.

### <a name="schedules"></a>Ütemezések

A következő táblázat az Automation-fiókban létrehozott alapértelmezett ütemterveket sorolja fel. Módosíthatók, vagy létrehozhatók saját egyéni ütemtervek is. Alapértelmezés szerint az összes ütemterv le van tiltva, kivéve **Scheduled_StartVM** és **Scheduled_StopVM**.

Ne engedélyezze az összes ütemtervet, mert ez átfedésben lévő ütemezett műveleteket is létrehozhat. Érdemes megállapítani, hogy mely optimalizálásokat szeretné elvégezni, és ennek megfelelően módosítani. További magyarázatért tekintse meg az Áttekintés szakaszban található példákat.

|Ütemterv neve | Frequency | Leírás|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8 óránként | A AutoStop_CreateAlert_Parent runbook 8 óránként futtatja, ami leállítja a virtuális gépeken alapuló értékeket Azure Automation változók External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames és External_ExcludeVMNames. Másik lehetőségként megadhatja a virtuális gépek vesszővel tagolt listáját a VMList paraméter használatával.|
|Scheduled_StopVM | Felhasználó által definiált, napi | A Scheduled_Parent runbook egy olyan paraméterrel futtatja, amely minden nap _leállítja_ a megadott időpontot. Automatikusan leállítja az összes olyan virtuális gépet, amely megfelel az eszköz változói által meghatározott szabályoknak. A kapcsolódó ütemezés ( **ütemezett StartVM**) engedélyezése.|
|Scheduled_StartVM | Felhasználó által definiált, napi | A (z) Scheduled_Parent runbook a megadott időpontban minden nap _indításának_ paraméterével futtatja. A automatikusan elindítja az összes olyan virtuális gépet, amely megfelel a megfelelő változók által meghatározott szabályoknak. A kapcsolódó ütemezés ( **ütemezett StopVM**) engedélyezése.|
|Sequenced-StopVM | 1:00 AM (UTC), minden pénteken | A Sequenced_Parent runbook egy olyan paraméterrel futtatja, amely minden pénteken _leállítja_ a megadott időpontot. A szekvenciálisan (növekvő) leállítja az összes virtuális gépet a megfelelő változók által definiált **SequenceStop** címkével. További információ a címkézési értékekről és az adategység változókról: Runbookok szakasz. Engedélyezze a kapcsolódó ütemezéseket, a **Sequenced-StartVM**.|
|Sorozatos – StartVM | 1:00 PM (UTC), minden hétfőn | A (z) Sequenced_Parent runbook futtatja, és a megadott időpontban minden hétfőn _kezdődik_ . A szekvenciálisan (csökkenő) elindítja az összes virtuális gépet a megfelelő változók által meghatározott **SequenceStart** . További információ a címkézési értékekről és az adategység változókról: Runbookok szakasz. Engedélyezze a kapcsolódó ütemezéseket, a **Sequenced-StopVM**.|

## <a name="azure-monitor-logs-records"></a>Azure Monitor rekordok naplózása

Az Automation két típusú rekordot hoz létre a Log Analytics munkaterületen: a feladatok naplóit és a feladatok folyamait.

### <a name="job-logs"></a>Feladatnaplók

|Tulajdonság | Leírás|
|----------|----------|
|Hívó |  A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
|Kategória | Az adattípus besorolása. Az Automation esetében az érték JobLogs.|
|CorrelationId | GUID, amely a runbook-feladatokhoz tartozó korrelációs azonosító.|
|JobId | A runbook-feladatokhoz tartozó GUID azonosító.|
|operationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az Automation esetében az érték a job.|
|resourceId | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
|ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat. Az Automation esetében az érték Azure Automation.|
|ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|resultType | A runbook-feladat állapota. Lehetséges értékek:<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>- Sikeres|
|resultDescription | Ismerteti a runbook-feladat eredményállapotát. Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött|
|RunbookName | Megadja a runbook-feladat nevét.|
|SourceSystem | Megadja az elküldött adatok forrásrendszerét. Az Automation esetében az érték a OpsManager|
|StreamType | Megadja az esemény típusát. Lehetséges értékek:<br>- Részletes<br>- Kimenet<br>- Hiba<br>- Figyelmeztetés|
|SubscriptionId | Megadja a feladat előfizetési azonosítóját.
|Time | A runbook-feladat végrehajtásának dátuma és időpontja.|

### <a name="job-streams"></a>Feladatstreamek

|Tulajdonság | Leírás|
|----------|----------|
|Hívó |  A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
|Kategória | Az adattípus besorolása. Az Automation esetében az érték JobStreams.|
|JobId | A runbook-feladatokhoz tartozó GUID azonosító.|
|operationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az Automation esetében az érték a job.|
|ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
|resourceId | Megadja az erőforrás-azonosítót az Azure-ban. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat. Az Automation esetében az érték Azure Automation.|
|ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|resultType | A runbook-feladat eredménye az esemény létrehozásának időpontjában. Lehetséges érték:<br>- Folyamatban|
|resultDescription | A runbook kimeneti streamjét tartalmazza.|
|RunbookName | A runbook neve.|
|SourceSystem | Megadja az elküldött adatok forrásrendszerét. Az Automation esetében az érték a OpsManager.|
|StreamType | A feladatstream típusa. Lehetséges értékek:<br>– Folyamat<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes|
|Time | A runbook-feladat végrehajtásának dátuma és időpontja.|

Ha olyan naplóbeli keresést hajt végre, amely a **JobLogs** vagy a **JobStreams**kategóriába tartozó rekordokat ad vissza, akkor kiválaszthatja a **JobLogs** vagy a **JobStreams** nézetet, amely a keresés által visszaadott frissítések összegzését jeleníti meg.

## <a name="sample-log-searches"></a>Naplókeresési minták

A következő táblázat a megoldás által összegyűjtött feladatrekordokkal kapcsolatos naplókeresési mintákat tartalmazza.

|Lekérdezés | Leírás|
|----------|----------|
|A sikeresen befejezett runbook ScheduledStartStop_Parent feladatok keresése | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|A sikeresen befejezett runbook SequencedStartStop_Parent feladatok keresése | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>A megoldás megtekintése

A megoldás eléréséhez navigáljon az Automation-fiókjához, és válassza a **munkaterület** elemet a **kapcsolódó erőforrások**területen. A log Analytics lapon válassza a **megoldások** **általános**lehetőséget. A **megoldások** lapon válassza a listáról a **Start-Stop-VM [munkaterület]** megoldást.

A megoldás kiválasztásakor megjelenik a **Start-Stop-VM [Workspace]** megoldás lapja. Itt tekintheti át a fontos adatokat, például a **StartStopVM** csempét. A Log Analytics munkaterülethez hasonlóan ez a csempe a megkezdett és sikeresen befejezett megoldás runbook-feladatainak számát és grafikus megjelenítését jeleníti meg.

![Automation Update Management megoldás lapja](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Itt a fánk csempére kattintva további elemzést végezhet a feladatok rekordjairól. A megoldás irányítópultján láthatók a feladatok előzményei és az előre definiált naplózott keresési lekérdezések. Váltson a log Analytics speciális portálra a keresési lekérdezések alapján történő kereséshez.

## <a name="configure-email-notifications"></a>E-mail-értesítések konfigurálása

Ha módosítani szeretné az e-mail-értesítéseket a megoldás üzembe helyezése után, módosítsa az üzembe helyezés során létrehozott műveleti csoportot.  

> [!NOTE]
> A Azure Government-felhőben lévő előfizetések nem támogatják a megoldás e-mail-funkcióit.

A Azure Portal navigáljon a figyelő-> műveleti csoportokhoz. Válassza ki a **StartStop_VM_Notication**címet viselő műveleti csoportot.

![Automation Update Management megoldás lapja](media/automation-solution-vm-management/azure-monitor.png)

A **StartStop_VM_Notification** **lapon kattintson a részletek** elemre. **Details** Ekkor megnyílik az **e-mail/SMS/leküldés/hang** lap. Frissítse az e-mail-címet, és kattintson **az OK** gombra a módosítások mentéséhez.

![Automation Update Management megoldás lapja](media/automation-solution-vm-management/change-email.png)

Másik lehetőségként további műveleteket is hozzáadhat a műveleti csoporthoz, hogy többet tudjon meg a műveleti csoportokról, lásd: [műveleti csoportok](../azure-monitor/platform/action-groups.md)

A következő példa egy olyan e-mailt küld, amelyet a megoldás a virtuális gépek leállításakor küld.

![Automation Update Management megoldás lapja](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>Virtuális gépek hozzáadása/kizárása

A megoldás lehetővé teszi, hogy a megoldás által célzott virtuális gépeket vagy kifejezetten a megoldásból kizárja a gépeket.

### <a name="add-a-vm"></a>Virtuális gép hozzáadása

Néhány lehetőség közül választhat, amelyekkel meggyőződhet arról, hogy a rendszer a futtatáskor a Start/Stop megoldásban egy virtuális gépet tartalmaz.

* A megoldás minden szülő [Runbookok](#runbooks) **VMList** paraméterrel rendelkezik. A virtuális gépek neveinek vesszővel tagolt listáját átadhatja erre a paraméterre, ha ütemezi a megfelelő fölérendelt runbook az adott helyzetben, és ezek a virtuális gépek a megoldás futtatásakor szerepelni fognak.

* Több virtuális gép kiválasztásához állítsa be a **External_Start_ResourceGroupNames** és a **External_Stop_ResourceGroupNamest** azokkal az erőforráscsoport-nevekkel, amelyek tartalmazzák az elindítani vagy leállítani kívánt virtuális gépeket. Ezt az értéket beállíthatja úgy is, hogy `*`, hogy a megoldás az előfizetésben lévő összes erőforráscsoport esetében fusson.

### <a name="exclude-a-vm"></a>Virtuális gép kizárása

Ha ki szeretne zárni egy virtuális gépet a megoldásból, adja hozzá a **External_ExcludeVMNames** változóhoz. Ez a változó a Start/Stop megoldásból kizárandó adott virtuális gépek vesszővel tagolt listája. Ez a lista 140 virtuális gépre korlátozódik. Ha több mint 140 virtuális gépet ad hozzá ehhez a vesszővel tagolt listához, a kizárni kívánt virtuális gépeket a rendszer véletlenül elindíthatja vagy leállíthatja.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Az indítási és leállítási ütemtervek módosítása

Az indítási és leállítási ütemezések kezelése ebben a megoldásban ugyanazokat a lépéseket követi, mint az [Runbook ütemezése Azure Automationban](automation-schedules.md). A virtuális gépek elindításához és leállításához külön ütemtervre van szükség.

Ha a megoldást úgy konfigurálja, hogy a virtuális gépeket csak bizonyos időben állítsa le, a rendszer támogatja. Ebben a forgatókönyvben csak egy **leállítási** ütemezést hoz létre, és nem fog megfelelő **kezdést** ütemezni. Ehhez a következőket kell tennie:

1. Győződjön meg arról, hogy felvette a virtuális gépekhez tartozó erőforráscsoportokat a **External_Stop_ResourceGroupNames** változóban való leállításhoz.
2. Hozzon létre saját ütemtervet a virtuális gépek leállításához szükséges időpontra.
3. Navigáljon a **ScheduledStartStop_Parent** runbook, és kattintson az **ütemterv**gombra. Ez lehetővé teszi az előző lépésben létrehozott ütemterv kiválasztását.
4. Válassza a **Paraméterek lehetőséget, és futtassa a beállításokat** , és állítsa a művelet paramétert "Leállítás" értékre.
5. Kattintson az **OK** gombra a módosítások mentéséhez.

## <a name="update-the-solution"></a>A megoldás frissítése

Ha a megoldás egy korábbi verzióját telepítette, először törölnie kell azt a fiókjából a frissített kiadás telepítése előtt. Kövesse a [megoldás eltávolításához](#remove-the-solution) szükséges lépéseket, majd kövesse a fenti lépéseket a [megoldás telepítéséhez](#deploy-the-solution).

## <a name="remove-the-solution"></a>A megoldás eltávolítása

Ha úgy dönt, hogy már nincs szüksége a megoldás használatára, törölheti azt az Automation-fiókból. A megoldás törlése csak a runbookok távolítja el. Nem törli a megoldás hozzáadásakor létrehozott ütemterveket és változókat. Ezeket az eszközöket manuálisan kell törölni, ha nem használja őket más runbookok.

A megoldás törléséhez hajtsa végre a következő lépéseket:

1. Az Automation-fiókban a **kapcsolódó erőforrások**területen válassza a **csatolt munkaterület**elemet.
1. Válassza **a ugrás munkaterületre**lehetőséget.
1. Az **általános**területen válassza a **megoldások**elemet. 
1. A **megoldások** lapon válassza a **Start-Stop-VM [munkaterület]** megoldást. A **VMManagementSolution [munkaterület]** lapon, a menüből válassza a **Törlés**lehetőséget.<br><br> ![a VM mgmt-megoldás törlése](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. A **megoldás törlése** ablakban erősítse meg, hogy törölni kívánja a megoldást.
1. Az információk ellenőrzése és a megoldás törlése után nyomon követheti az előrehaladást a menü **értesítések** részén. A megoldás elindításának folyamata után visszatér a **megoldások** oldalára.

Az Automation-fiók és a Log Analytics munkaterület nem törlődik a folyamat részeként. Ha nem szeretné megőrizni a Log Analytics munkaterületet, manuálisan kell törölnie. Ezt a Azure Portal lehet elvégezni:

1. A Azure Portal területen keresse meg és válassza ki **log Analytics munkaterületeket**.
1. A **log Analytics munkaterületek** lapon válassza ki a munkaterületet.
1. Válassza a **Törlés** lehetőséget a munkaterület Beállítások lapjának menüjében.

Ha nem szeretné megőrizni a Azure Automation fiók összetevőit, manuálisan is törölheti azokat. A megoldás által létrehozott runbookok, változók és ütemtervek listáját itt találja: [megoldás-összetevők](#solution-components).

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni a különböző keresési lekérdezések létrehozásáról és az Automation-feladatok naplóinak Azure Monitor-naplókkal való áttekintéséről, tekintse meg a következő témakört: [keresések Azure monitor naplókban](../log-analytics/log-analytics-log-searches.md).
- A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza.
- Ha többet szeretne megtudni Azure Monitor a naplókról és az adatgyűjtési forrásokról, tekintse meg [Az Azure Storage-adatok gyűjtése Azure monitor naplók áttekintését](../azure-monitor/platform/collect-azure-metrics-logs.md)ismertető cikket.
