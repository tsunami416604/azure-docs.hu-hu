---
title: Virtuális gépek indítása/leállítása munkaidőn kívüli megoldás közben
description: Ez a virtuálisgép-kezelési megoldás elindítja és leállítja az Azure Resource Manager virtuális gépek ütemezés szerint, és proaktív módon figyeli az Azure Monitor naplók.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2020
ms.topic: conceptual
ms.openlocfilehash: cbf181b9a6d3860854c7b61cca0e6c50810cced9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278544"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Megoldás a virtuális gépek munkaidőn kívüli indítására és leállítására az Azure Automation szolgáltatásban

A virtuális gépek indítása/leállítása munkaidőn kívüli megoldás ban indítsa el és állítsa le az Azure virtuális gépek et a felhasználó által meghatározott ütemezések, betekintést nyújt az Azure Monitor naplókon keresztül, és elküldi a választható e-maileket [a műveletcsoportok](../azure-monitor/platform/action-groups.md)használatával. Támogatja az Azure Resource Manager és a klasszikus virtuális gépek a legtöbb forgatókönyv. A megoldás klasszikus virtuális gépekkel való használatához klasszikus RunAs-fiókra van szükség, amely alapértelmezés szerint nem jön létre. A klasszikus RunAs-fiókok létrehozásáról a Klasszikus futtatási fiókok című témakörben talál [útmutatást.](automation-create-standalone-account.md#classic-run-as-accounts)

> [!NOTE]
> A kezdő/leállító virtuális gépek munkaidőn kívüli megoldás tesztelése megtörtént az Automation-fiókba importált Azure-modulokkal a megoldás üzembe helyezésekor. A megoldás jelenleg nem működik az Azure-modul újabb verzióival. Ez csak az Automation-fiók, amely a virtuális gépek indítása/leállítása munkaidőn kívüli megoldás futtatásához használt. Továbbra is használhatja az Azure-modul újabb verzióit a többi Automation-fiókban, ahogyan az [Azure PowerShell-modulok frissítése](automation-update-azure-modules.md) az Azure Automationben című részben ismerteti.

Ez a megoldás egy decentralizált, alacsony költségű automatizálási lehetőséget biztosít azoknak a felhasználóknak, akik optimalizálni szeretnék a virtuális gép költségeit. Ezzel a megoldással a következőket teheti:

- Ütemezze a virtuális gépekindítását és leállítását.
- Ütemezze a virtuális gépeket, hogy növekvő sorrendben induljanak el és állítsanak le az Azure-címkék használatával (nem támogatott a klasszikus virtuális gépek).
- Automatikus rendszerleállási virtuális gépek alacsony CPU-használat alapján.

A jelenlegi megoldással kapcsolatban a következők ben korlátozások vannak:

- Ez a megoldás kezeli a virtuális gépek bármely régióban, de csak akkor használható ugyanabban az előfizetésben, mint az Azure Automation-fiók.
- Ez a megoldás az Azure-ban és az AzureGov-ban érhető el minden olyan régióban, amely támogatja a Log Analytics-munkaterületet, az Azure Automation-fiókot és a riasztásokat. Az AzureGov-régiók jelenleg nem támogatják az e-mail funkciókat.

> [!NOTE]
> Ha a megoldást a klasszikus virtuális gépek, majd az összes virtuális gép lesz feldolgozva egymás után felhőalapú szolgáltatás. A virtuális gépek feldolgozása továbbra is párhuzamosan történik a különböző felhőszolgáltatások között. Ha felhőszolgáltatásonként 20-nál több virtuális géppel rendelkezik, azt javasoljuk, hogy hozzon létre több ütemezést a szülő runbook **ScheduledStartStop_Parent,** és ütemezésenként 20 virtuális gépet adjon meg. Az ütemezési tulajdonságokban adja meg vesszővel tagolt listaként a **VMList** paraméter ben szereplő virtuálisgép-neveket. Ellenkező esetben, ha az automation-feladat ehhez a megoldáshoz fut több mint három órán át, hogy ideiglenesen kiürült, vagy leállította a [méltányos megosztási](automation-runbook-execution.md#fair-share) korláton ként.
>
> Az Azure Cloud Solution Provider (Azure CSP) előfizetések csak az Azure Resource Manager modellt támogatják, a nem Azure Resource Manager-szolgáltatások nem érhetők el a programban. A Start/Stop megoldás futtatásakor hibákat kaphat, mivel parancsmagokkal rendelkezik a klasszikus erőforrások kezeléséhez. A kriptaverzióról további információ: [Available services in CSP subscriptions](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Ha csp-előfizetést használ, a központi telepítés után módosítsa a [**External_EnableClassicVMs**](#variables) **változót Hamis** értékre.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

A megoldás runbookjai egy [Azure Run As fiókkal működnek.](automation-create-runas-account.md) A Futtatás másként fiók az előnyben részesített hitelesítési módszer, mivel tanúsítványhitelesítést használ a jelszó helyett, amely lejárhat vagy gyakran változhat.

Azt javasoljuk, hogy egy külön automation-fiók a virtuális gép indítási és leállítási megoldás. Ennek az az oka, hogy az Azure-modul verzióit gyakran frissítik, és paramétereik változhatnak. A Start/Stop virtuálisgép-megoldás nem frissül ugyanazon a lépésszámon, így előfordulhat, hogy nem működik az általa használt parancsmagok újabb verzióival. Azt is javasoljuk, hogy tesztelje a modul frissítéseit egy tesztautomatizálási fiókban, mielőtt importálná őket a termelési automatizálási fiók(oka)ra.

### <a name="permissions-needed-to-deploy"></a>A telepítéshez szükséges engedélyek

Vannak bizonyos engedélyek, amelyek a felhasználónak kell telepítenie a virtuális gépek indítása/leállítása munkaidőn kívül megoldás. Ezek az engedélyek eltérőek, ha egy előre létrehozott Automation-fiók és a Log Analytics munkaterületet használ, vagy újakat hoz létre a központi telepítés során. Ha ön az előfizetés közreműködője és az Azure Active Directory-bérlő globális rendszergazdája, nem kell konfigurálnia a következő engedélyeket. Ha nem rendelkezik ezekkel a jogokkal, vagy egyéni szerepkört kell konfigurálnia, tekintse meg az alábbi engedélyeket.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Már meglévő Automation-fiók és Log Analytics-munkaterület

Ha a virtuális gépeket munkaidőn kívüli megoldásban szeretné telepíteni egy meglévő Automation-fiók- és Log Analytics-munkaterületre, a megoldást üzembe helyező felhasználónak a következő engedélyekre van szüksége az **erőforráscsoporthoz.** A szerepkörökről az [Egyéni szerepkörök az Azure-erőforrásokhoz](../role-based-access-control/custom-roles.md)című témakörben olvashat bővebben.

| Engedély | Hatókör|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/variables/write | Erőforráscsoport |
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
| Microsoft.OperationalInsights/munkaterületek/* | Erőforráscsoport |
| Microsoft.Insights/diagnosticBeállítások/írás | Erőforráscsoport |
| Microsoft.Insights/ActionGroups/Írás | Erőforráscsoport |
| Microsoft.Insights/ActionGroups/read | Erőforráscsoport |
| Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoport |
| Microsoft.Resources/deployments/* | Erőforráscsoport |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Új automatizálási fiók és új Log Analytics-munkaterület

A virtuális gépek indítási/leállítási idejéni megoldás új Automation-fiók- és Log Analytics-munkaterületre történő telepítéséhez a megoldást üzembe helyező felhasználónak szüksége van az előző szakaszban meghatározott engedélyekre, valamint a következő engedélyekre:

- Társ-rendszergazda az előfizetés – Csak akkor van szükség, hogy a Klasszikus futtatás fiókként, ha a klasszikus virtuális gépek kezelése. [A klasszikus RunAs-fiókok](automation-create-standalone-account.md#classic-run-as-accounts) alapértelmezés szerint már nem jönnek létre.
- Az Azure [Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) **alkalmazásfejlesztői** szerepkör tagja. A Futtatás fiókokként beállításáról a [Futtatás másként fiókok konfigurálása című témakörben](manage-runas-account.md#permissions)talál további információt.
- Közreműködő az előfizetésben vagy a következő engedélyekben.

| Engedély |Hatókör|
| --- | --- |
| Microsoft.Authorization/Operations/read | Előfizetés|
| Microsoft.Authorization/permissions/read |Előfizetés|
| Microsoft.Authorization/roleAssignments/read | Előfizetés |
| Microsoft.Authorization/roleAssignments/write | Előfizetés |
| Microsoft.Authorization/roleAssignments/delete | Előfizetés |
| Microsoft.Automation/automationAccounts/connections/read | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/certificates/read | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/write | Erőforráscsoport |
| Microsoft.OperationalInsights/munkaterületek/írás | Erőforráscsoport |

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Hajtsa végre az alábbi lépéseket a kezdő/leállító virtuális gépek munkaidőn kívüli megoldás hozzáadása az Automation-fiókhoz, majd konfigurálja a változókat a megoldás testreszabásához.

1. Egy Automation-fiókban válassza **a Virtuális gép indítása/leállítása** lehetőséget a **Kapcsolódó erőforrások csoportban.** Itt kattintson a További információ a **megoldásról és engedélyezése**elemre. Ha már rendelkezik üzembe helyezett virtuálisgép-indítási/leállítási megoldással, kiválaszthatja azt **a Megoldás kezelése** gombra kattintva, és megtalálhatja a listában.

   ![Engedélyezés automatizálási fiókból](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Az Azure Portalon bárhonnan létrehozhatja, ha az **Erőforrás létrehozása**elemre kattint. A Piactér lapon írjon be egy kulcsszót, például **a Start** vagy **a Start/Stop kulcsszót.** Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Másik lehetőségként beírhat egy vagy több kulcsszót a megoldás teljes nevéből, majd nyomja le az Enter billentyűt. Válassza **a virtuális gépek indítása/leállítása munkaidőn kívül** lehetőséget a keresési eredmények között.

2. A kiválasztott megoldás **kezdési/leállítási/leállítási/leállítási** oldalán tekintse át az összesítő információkat, majd kattintson a **Létrehozás gombra.**

   ![Azure portál](media/automation-solution-vm-management/azure-portal-01.png)

3. Megjelenik **a Megoldás hozzáadása** lap. A rendszer kéri, hogy konfigurálja a megoldást, mielőtt importálhatja azt az Automation-előfizetésbe.

   ![Virtuálisgép-kezelés megoldás hozzáadása lapja](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. A **Megoldás hozzáadása** lapon válassza a **Munkaterület**lehetőséget. Válasszon ki egy Log Analytics-munkaterületet, amely ugyanahhoz az Azure-előfizetéshez kapcsolódik, amelyben az Automation-fiók található. Ha nem rendelkezik munkaterülettel, válassza **az Új munkaterület létrehozása**lehetőséget. A **Log Analytics munkaterület-lapon** hajtsa végre az alábbi lépéseket:
   - Adja meg az új **Log Analytics-munkaterület**nevét, például a "ContosoLAWorkspace".Specify a name for the new Log Analytics workspace , such as "ContosoLAWorkspace".
   - Válassza ki **azt az előfizetést,** amelyre hivatkozni szeretne a legördülő listából való kijelöléssel, ha a kiválasztott alapértelmezett beállítás nem megfelelő.
   - Az **Erőforráscsoport**csoport ban létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévőt.
   - Válasszon egy **helyet**.
   - Válasszon egy **tarifacsomagot**. Válassza a **Gb-onként (önálló)** lehetőséget. Az Azure Monitor-naplók frissített [díjszabási](https://azure.microsoft.com/pricing/details/log-analytics/) és a GB-onkénti szint az egyetlen lehetőség.

   > [!NOTE]
   > A megoldások engedélyezésekor csak bizonyos régiók esetén lehet összekapcsolni egy Log Analytics-munkaterületet és egy Automation-fiókot.
   >
   > A támogatott leképezési párok listáját az [Automation-fiók és a Log Analytics munkaterület területleképezése című témakörben tájékírja.](how-to/region-mappings.md)

5. Miután megadta a szükséges információkat a **Log Analytics munkaterület** imát, kattintson a **Létrehozás gombra.** A folyamat előrehaladását az **Értesítések** menüben követheti nyomon, amely akkor tér vissza a **Megoldás hozzáadása** lapra, amikor elkészült.
6. A **Megoldás hozzáadása** lapon válassza az **Automation-fiók lehetőséget.** Ha új Log Analytics-munkaterületet hoz létre, létrehozhat egy új Automation-fiókot, amelyet hozzá kell társviszonyba venni, vagy kiválaszthat egy meglévő Automation-fiókot, amely még nincs csatolva a Log Analytics-munkaterülethez. Jelöljön ki egy meglévő Automation-fiókot, vagy kattintson **az Automatizálási fiók létrehozása gombra,** és az **Automatizálási fiók hozzáadása** lapon adja meg a következő információkat:
   - A **Név** mezőbe írja be az Automation-fiók nevét.

     Az összes többi beállítás automatikusan kitöltődik a kiválasztott Log Analytics munkaterület alapján. Ezek a beállítások nem módosíthatók. A megoldásban szereplő runbookok alapértelmezett hitelesítési módszere egy Azure-futtatófiók. Miután az **OK**gombra kattintott, a konfigurációs beállítások érvényesítésre kerülnek, és létrejön nek az Automation-fiók. Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását.

7. Végül a **Megoldás hozzáadása** lapon válassza a **Konfiguráció**lehetőséget. Megjelenik **a Paraméterek** lap.

   ![A megoldás paraméterei lap](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Itt a rendszer a következő utasításokat kéri:
   - Adja meg a **célerőforráscsoport nevét**. Ezek az értékek olyan erőforráscsoportnevek, amelyek a megoldás által kezelendő virtuális gépeket tartalmaznak. Több nevet is megadhat, és vesszővel elválaszthatja őket (az értékek nem különböztetik meg a kis- és nagybetűket). Helyettesítő karaktert is használhat, ha az előfizetés összes erőforráscsoportjában lévő virtuális gépeket szeretné megadni. Ez az érték a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupNames** változókban tárolódik.
   - Adja meg a **virtuális gép kizárási listáját (karakterlánc)**. Ez az érték a célerőforrás-csoport egy vagy több virtuális gépének a neve. Több nevet is megadhat, és vesszővel elválaszthatja őket (az értékek nem különböztetik meg a kis- és nagybetűket). Helyettesítő karakter használata támogatott. Ez az érték a **External_ExcludeVMNames** változóban tárolódik.
   - Jelöljön ki egy **Ütemezést**. Válassza ki az ütemezés dátumát és időpontját. A program a kiválasztott idővel kezdődően ismétlődő napi ütemezést hoz létre. Egy másik régió kiválasztása nem érhető el. Ha a megoldás konfigurálása után az adott időzónára szeretné beállítani az ütemezést, olvassa [el az Indítási és leállítási ütemezés módosítása című témakört.](#modify-the-startup-and-shutdown-schedules)
   - Ha **e-mailes értesítéseket** szeretne kapni egy műveletcsoporttól, fogadja el az **Igen** alapértelmezett értéket, és adjon meg egy érvényes e-mail címet. Ha a **Nem** lehetőséget választja, de egy későbbi időpontban úgy dönt, hogy e-mailértesítést szeretne kapni, frissítheti az érvényes e-mail címekkel létrehozott [műveletcsoportot,](../azure-monitor/platform/action-groups.md) vesszővel elválasztva. A következő riasztási szabályokat is engedélyeznie kell:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > A **célerőforráscsoport nevek** alapértelmezett **&ast;** értéke a . Ez az előfizetésben szereplő összes virtuális gépet célozza meg. Ha nem szeretné, hogy a megoldás az előfizetésben lévő összes virtuális gépet célozza meg, ezt az értéket frissíteni kell az erőforráscsoportok nevének listájára az ütemezések engedélyezése előtt.

8. Miután konfigurálta a megoldáshoz szükséges kezdeti beállításokat, kattintson az **OK** gombra a **Paraméterek** lap bezárásához, és válassza a **Létrehozás lehetőséget.** Az összes beállítás érvényesítése után a megoldás telepítve van az előfizetésben. Ez a folyamat több másodpercig is eltarthat, és nyomon követheti a folyamat előrehaladását az **Értesítések** menüből.

> [!NOTE]
> Ha rendelkezik egy Azure Cloud Solution Provider (Azure CSP) előfizetéssel, a telepítés befejezése után az Automation-fiókban lépjen **a Változók** a **Megosztott erőforrások** csoportban, és állítsa a [**External_EnableClassicVMs**](#variables) **változót False**értékre. Ez megakadályozza, hogy a megoldás klasszikus virtuálisgép-erőforrásokat keres.

## <a name="scenarios"></a>Forgatókönyvek

A megoldás három különböző forgatókönyvet tartalmaz. Ezek a forgatókönyvek a következők:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>1. forgatókönyv: Virtuális gépek indítása/leállítása ütemezés szerint

Ez a forgatókönyv az alapértelmezett konfiguráció a megoldás első üzembe helyezésekor. Beállíthatja például, hogy állítsa le az összes virtuális gépet az előfizetésben, amikor este elhagyja a munkát, és reggel kezdi el őket, amikor újra az irodában van. Az **ütemezett-startvm** és az **ütemezett-stopvm** ütemezések konfigurálásakor a központi telepítés során elindulnak és leállítják a célzott virtuális gépeket. A megoldás konfigurálása a virtuális gépek leállításához támogatott, olvassa [el az indítási és leállítási ütemezések módosítása című témakört,](#modify-the-startup-and-shutdown-schedules) amelyből megtudhatja, hogyan konfigurálhat egyéni ütemezést.

> [!NOTE]
> Az időzóna az aktuális időzóna az ütemezési idő paraméter konfigurálásakor. Azonban az Azure Automation-ben UTC formátumban tárolja. Nem kell semmilyen időzóna-átalakítást végeznie, mivel ez a központi telepítés során történik.

A következő változók konfigurálásával szabályozhatja, hogy mely virtuális gépek vannak hatókörben: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**és **External_ExcludeVMNames.**

Engedélyezheti a művelet célzását egy előfizetés és egy erőforráscsoport ellen, vagy a virtuális gépek egy adott listáját, de mindkettőt nem.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>A kezdési és leállítási műveletek célzása előfizetési és erőforráscsoporthoz

1. Konfigurálja a **External_Stop_ResourceGroupNames** és **External_ExcludeVMNames** változókat a cél virtuális gépek megadásához.
2. Engedélyezze és frissítse az **ütemezett StartVM** és **az Ütemezett stopvm** ütemezéseket.
3. Futtassa a **ScheduledStartStop_Parent** runbookot úgy, hogy az ACTION paraméter **elindul,** és a WHATIF paraméter **true** értékre van állítva a módosítások előnézetének megtekintéséhez.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>A kezdési és leállítási művelet célzása a virtuális gépek listájával

1. Futtassa a **ScheduledStartStop_Parent** runbookot az ACTION paraméter **elindításához,** adjon hozzá egy vesszővel tagolt virtuális gépek listáját a *VMList* paraméterben, majd állítsa a WHATIF paramétert **True**értékre. Tekintse meg a módosítások előnézetét.
1. Konfigurálja a **External_ExcludeVMNames** paramétert a virtuális gépek vesszővel tagolt listájával (VM1, VM2, VM3).
1. Ez a forgatókönyv nem felel meg a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupnames** változók. Ebben a forgatókönyvben létre kell hoznia a saját Automation-ütemezés. További információt a [Runbook ütemezése az Azure Automationben című témakörben](../automation/automation-schedules.md)talál.

> [!NOTE]
> A **célerőforráscsoport nevek** értéke **mind a External_Start_ResourceGroupNames,** mind a **External_Stop_ResourceGroupNames**értékeként tárolódik. További részletesség érdekében módosíthatja ezeket a változókat a különböző erőforráscsoportok megcélzásához. A művelet elindításához használja **a External_Start_ResourceGroupNames,** és állítsa le a műveletet, **használja a External_Stop_ResourceGroupNames.** A virtuális gépek automatikusan hozzáadódnak a kezdési és leállítási ütemezésekhez.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>2. forgatókönyv: VMS indítása/leállítása egymás után címkék használatával

Olyan környezetben, amely két vagy több összetevőt tartalmaz több virtuális gépen, amelyek támogatják az elosztott számítási feladatokat, fontos az összetevők indítási és leállításás sorrendjének támogatása. Ezt a forgatókönyvet a következő lépések végrehajtásával valósíthatja meg:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>A kezdési és leállítási műveletek célzása előfizetési és erőforráscsoporthoz

1. Adjon hozzá egy **sequencestart** és egy **sequencestop** címkét pozitív egész értékkel a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupNames** változókban megcélzott virtuális gépekhez. A kezdési és leállítási műveletek növekvő sorrendben történnek. A virtuális gépek címkézésének módjáról az [Azure-beli Windows-virtuális gép címkézése](../virtual-machines/windows/tag.md) és [az Azure-beli Linux-virtuális gép címkézése.](../virtual-machines/linux/tag.md)
1. Módosítsa az **ütemezések Szveszvenált-StartVM** és **szekvenált-StopVM** a dátum és az idő, amely megfelel a követelményeknek, és lehetővé teszi az ütemezés.
1. Futtassa a **SequencedStartStop_Parent** runbookot úgy, hogy az ACTION paraméter **elindul,** és a WHATIF paraméter **true** értékre van állítva a módosítások előnézetének megtekintéséhez.
1. Tekintse meg a műveletet, és a szükséges módosításokat, mielőtt az éles virtuális gépek en implementálása. Ha készen áll, manuálisan hajtsa végre a runbookot a **Hamis**paraméterrel, vagy hagyja, hogy az Automation **ütemezése Szekvenciális-StartVM** és **Szekvenált-StopVM** automatikusan fusson az előírt ütemezés szerint.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>A kezdési és leállítási művelet célzása a virtuális gépek listájával

1. Adjon hozzá egy **sequencestart** és egy **sequencestop** címkét pozitív egész értékkel a **Virtuálisgép-paraméterhez** hozzáadni kívánt virtuális gépekhez.
1. Futtassa a **SequencedStartStop_Parent** runbookot az ACTION paraméter **elindításához,** adjon hozzá egy vesszővel tagolt virtuális gépek listáját a *VMList* paraméterben, majd állítsa a WHATIF paramétert **True**értékre. Tekintse meg a módosítások előnézetét.
1. Konfigurálja a **External_ExcludeVMNames** paramétert a virtuális gépek vesszővel tagolt listájával (VM1, VM2, VM3).
1. Ez a forgatókönyv nem felel meg a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupnames** változók. Ebben a forgatókönyvben létre kell hoznia a saját Automation-ütemezés. További információt a [Runbook ütemezése az Azure Automationben című témakörben](../automation/automation-schedules.md)talál.
1. Tekintse meg a műveletet, és a szükséges módosításokat, mielőtt az éles virtuális gépek en implementálása. Ha készen áll, manuálisan hajtsa végre a figyelés-és diagnosztika/monitoring-action-groupsrunbook a paraméter értéke **Hamis,** vagy hagyja, hogy az automation **ütemezése Szekvenciális-StartVM** és **szekvenált-StopVM** automatikusan követi az előírt ütemezést.

## <a name="solution-components"></a>Megoldás-összetevők

Ez a megoldás előre konfigurált runbookokat, ütemezéseket és integrációt tartalmaz az Azure Monitor naplóival, így a virtuális gépek indítását és leállítását az üzleti igényekhez igazíthatja.

### <a name="runbooks"></a>Runbookok

Az alábbi táblázat felsorolja a megoldás által az Automation-fiókba telepített runbookokat. Ne módosítsa a runbook-kódot. Ehelyett írja meg saját runbook ját az új funkciókhoz.

> [!IMPORTANT]
> Ne futtasson közvetlenül olyan runbookot, amelynek nevéhez "gyermek" van csatolva.

Minden szülő runbookok tartalmazzák a _WhatIf_ paraméter. Ha **értéke Igaz**, _WhatIf_ támogatja részletezi a pontos viselkedését a runbook veszi, ha fut a _WhatIf_ paraméter nélkül, és ellenőrzi a megfelelő virtuális gépek vannak célozva. A runbook csak akkor hajtja végre a definiált műveleteket, ha a _WhatIf_ paraméter **értéke Hamis**.

|Forgatókönyv | Paraméterek | Leírás|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject objektum <br> AlertAction művelet <br> WebHookURI között | A szülő runbookból hívva. Ez a runbook erőforrásonkénti riasztásokat hoz létre az automatikus leállítási forgatókönyvhöz.|
|AutoStop_CreateAlert_Parent | Virtuális géplista<br> WhatIf: Igaz vagy hamis  | Létrehozza vagy frissíti az Azure riasztási szabályokat a virtuális gépek a célzott előfizetési vagy erőforráscsoportok. <br> VMList: A virtuális gépek vesszővel tagolt listája. Például _vm1, vm2, vm3_.<br> *A WhatIf* a runbook logikáját a végrehajtás nélkül érvényesíti.|
|AutoStop_Disable | Nincs | Letiltja az automatikus leállítási riasztásokat és az alapértelmezett ütemezést.|
|AutoStop_StopVM_Child | WebHookData | A szülő runbookból hívva. Riasztási szabályok hívja meg ezt a runbookot a virtuális gép leállításához.|
|Bootstrap_Main | Nincs | Használt egy alkalommal a bootstrap konfigurációk, például a webhookURI, amelyek általában nem érhetők el az Azure Resource Manager. Ez a runbook automatikusan törlődik a sikeres üzembe helyezésután.|
|ScheduledStartStop_Child | VMName <br> Művelet: Indítás vagy leállítás <br> ResourceGroupName | A szülő runbookból hívva. Indítási vagy leállítási műveletet hajt végre az ütemezett leállításhoz.|
|ScheduledStartStop_Parent | Művelet: Indítás vagy leállítás <br>Virtuális géplista <br> WhatIf: Igaz vagy hamis | Ez a beállítás az előfizetésben lévő összes virtuális gépet érinti. A **External_Start_ResourceGroupNames** és **a External_Stop_ResourceGroupNames** csak ezeken a célzott erőforráscsoportokon hajtsa végre. Adott virtuális gépeket is kizárhat a **External_ExcludeVMNames** változó frissítésével.<br> VMList: A virtuális gépek vesszővel tagolt listája. Például _vm1, vm2, vm3_.<br> _A WhatIf_ a runbook logikáját a végrehajtás nélkül érvényesíti.|
|SequencedStartStop_Parent | Művelet: Indítás vagy leállítás <br> WhatIf: Igaz vagy hamis<br>Virtuális géplista| Hozzon létre **nevű címkéksequencestart** és **sequencestop** minden virtuális gép, amelynek sorrendbe szeretné venni a start/stop tevékenység. Ezek a címkenevek nem idéken különböznek a kis- és nagybetűktől. A címke értékének pozitív egész számnak (1, 2, 3) kell lennie, amely megfelel annak a sorrendnek, amelyben el szeretné kezdeni vagy le állítani. <br> VMList: A virtuális gépek vesszővel tagolt listája. Például _vm1, vm2, vm3_. <br> _A WhatIf_ a runbook logikáját a végrehajtás nélkül érvényesíti. <br> **Megjegyzés:** A virtuális gépeknek External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames és External_ExcludeVMNames az Azure Automation-változókban External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames és External_ExcludeVMNames definiált erőforráscsoportokon belül kell lenniük. Rendelkezniük kell a műveletek hez szükséges címkékkel.|

### <a name="variables"></a>Változók

Az alábbi táblázat az Automation-fiókban létrehozott változókat sorolja fel. Csak a **Külső**programmal előtaggal előtaggal rendelkező változókat módosítsa. A **belső** vel előtaggal előtaggal rendelkező változók módosítása nemkívánatos hatásokat okoz.

|Változó | Leírás|
|---------|------------|
|External_AutoStop_Condition | A feltételes operátor konfigurálása szükséges a feltétel, mielőtt riasztást. Az elfogadható értékek a következők: **GreaterThan**, **GreaterThanOrEqual**, **LessThan**és **LessThanOrEqual**.|
|External_AutoStop_Description | A riasztást, hogy állítsa le a virtuális gép, ha a CPU százalékos meghaladja a küszöbértéket.|
|External_AutoStop_MetricName | Annak a teljesítménymutatónak a neve, amelyhez az Azure Alert szabályt konfigurálni kell.|
|External_AutoStop_Threshold | Az Azure Alert szabály External_AutoStop_MetricName megadott _küszöbértéke._ A százalékos értékek 1 és 100 között lehetnek.|
|External_AutoStop_TimeAggregationOperator | Az időösszesítésoperátor, amely a kiválasztott ablakméretre lesz alkalmazva a feltétel kiértékeléséhez. Az elfogadható értékek az **átlag**, **a minimum,** **a maximum,** **az összes**és **az utolsó**.|
|External_AutoStop_TimeWindow | Az ablak mérete, amelynek során az Azure elemzi a kiválasztott metrikák riasztás indításához. Ez a paraméter időfesztávolságformátumban fogadja a bemenetet. A lehetséges értékek 5 perc és 6 óra között lehetnek.|
|External_EnableClassicVMs| Itt adható meg, hogy a klasszikus virtuális gépeket a megoldás célozza-e meg. Az alapértelmezett érték Igaz. Ezt hamis értékre kell állítani a CSP-előfizetésekhez. A klasszikus virtuális gépekhez [klasszikus futtatási fiók](automation-create-standalone-account.md#classic-run-as-accounts)szükséges.|
|External_ExcludeVMNames | Írja be a kizárandó virtuális gépneveket, és válassza el a neveket szóközök nélküli vesszővel. Ez legfeljebb 140 virtuális gépre korlátozódik. Ha 140-nél több virtuális gépet ad hozzá ehhez a vesszővel tagolt listához, előfordulhat, hogy a kizárt virtuális gépek véletlenül elindulnak vagy leállnak.|
|External_Start_ResourceGroupNames | Egy vagy több erőforráscsoportot ad meg, amelyek vesszővel választják el az értékeket, és amelyeket a műveletek indítására céloznak.|
|External_Stop_ResourceGroupNames | Egy vagy több erőforráscsoportot ad meg, amelyek vesszővel választják el az értékeket, és amelyek et a stop műveletekre használnak.|
|Internal_AutomationAccountName | Megadja az Automation-fiók nevét.|
|Internal_AutoSnooze_WebhookUri | Megadja az automatikus leállítási forgatókönyvhöz meghívott Webhook URI-t.|
|Internal_AzureSubscriptionId | Megadja az Azure-előfizetés-azonosítót.|
|Internal_ResourceGroupName | Megadja az Automation-fiók erőforráscsoportjának nevét.|

A **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**és **External_ExcludeVMNames** változók minden esetben szükségesek a virtuális gépek célzásához, kivéve a **AutoStop_CreateAlert_Parent,** **SequencedStartStop_Parent**és **ScheduledStartStop_Parent** runbookok virtuális gépeinek vesszővel tagolt listáját. Ez azt, hogy a virtuális gépek nek a célerőforrás-csoportokban kell lennie a kezdési és leállítási műveletek hez. A logika hasonlóan működik az Azure-szabályzat, az, hogy megcélozhatja az előfizetés vagy erőforráscsoport, és az újonnan létrehozott virtuális gépek által örökölt műveleteket. Ez a megközelítés elkerüli, hogy minden virtuális géphez külön ütemezést kell fenntartania, és kezelnie kell a skálázási indításokat és leállásokat.

### <a name="schedules"></a>Ütemezések

Az alábbi táblázat az Automation-fiókban létrehozott összes alapértelmezett ütemezést sorolja fel.Módosíthatja őket, vagy létrehozhat saját egyéni ütemezéseket.Alapértelmezés szerint az összes ütemezés le van tiltva, kivéve **a Scheduled_StartVM** és **a Scheduled_StopVM**.

Ne engedélyezze az összes ütemezést, mert ez egymást átfedő ütemezési műveleteket hozhat létre. A legjobb, ha meghatározza, hogy mely optimalizálásokat szeretné végrehajtani és ennek megfelelően módosítani. További magyarázat a példaforgatókönyvek az áttekintő szakaszban.

|Ütemezés neve | Frequency | Leírás|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8 óránként | A AutoStop_CreateAlert_Parent runbook 8 óránként fut, amely viszont leállítja a virtuális gép-alapú értékeket External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames és External_ExcludeVMNames az Azure Automation-változók. Másik lehetőségként megadhat egy vesszővel tagolt virtuális gépek listáját a VMList paraméter használatával.|
|Scheduled_StopVM | Felhasználó által definiált, napi | Futtatja a Scheduled_Parent runbook egy paraméter _stop_ minden nap a megadott időpontban.Automatikusan leállítja az összes virtuális gépet, amely megfelel az eszközváltozók által meghatározott szabályoknak.Engedélyezze a kapcsolódó **ütemezést, az Ütemezett-StartVM programot.**|
|Scheduled_StartVM | Felhasználó által definiált, napi | Futtatja a Scheduled_Parent runbook egy paraméter a _start_ minden nap a megadott időpontban. Automatikusan elindítja az összes virtuális gépet, amely megfelel a megfelelő változók által meghatározott szabályoknak.Engedélyezze a kapcsolódó **ütemezést, az Scheduled-StopVM-et.**|
|Szekvenált-StopVM | Minden pénteken 1:00 (UTC), | Futtatja a Sequenced_Parent runbook egy paraméter _stop_ minden pénteken a megadott időpontban.Egymás után (növekvő) leállítja az összes virtuális gépet a megfelelő változók által meghatározott **SequenceStop** címkével. A címkeértékekről és az eszközváltozókról a Runbooks szakaszban talál további információt.Engedélyezze a kapcsolódó ütemezést, **a Szekvenciális-StartVM.**|
|Szekvenált-StartVM | 13:00 (UTC), minden hétfőn | Futtatja a Sequenced_Parent runbook egy paramétera _Start_ minden hétfőn a megadott időpontban. Egymás után (csökkenő) elindítja az összes virtuális gépet a megfelelő változók által meghatározott **SequenceStart** címkével. A címkeértékekről és az eszközváltozókról a Runbooks szakaszban talál további információt. Engedélyezze a kapcsolódó **ütemezést, a Szekvenciális stopvm-et.**|

## <a name="azure-monitor-logs-records"></a>Az Azure Monitor naplózza a rekordokat

Az Automatizálás kétféle rekordot hoz létre a Log Analytics-munkaterületen: a feladatnaplókat és a feladatadatfolyamokat.

### <a name="job-logs"></a>Feladatnaplók

|Tulajdonság | Leírás|
|----------|----------|
|Hívó |  A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
|Kategória | Az adattípus besorolása. Az Automation esetében az érték JobLogs.|
|CorrelationId | GUID, amely a runbook-feladat korrelációs azonosítója.|
|JobId | GUID, amely a runbook-feladat azonosítója.|
|operationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az automatizálás esetében az érték A feladat.|
|resourceId | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
|ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat. Az Automation esetében az érték Azure Automation.|
|ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|resultType | A runbook-feladat állapota. Lehetséges értékek:<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>- Sikeres|
|resultDescription | Ismerteti a runbook-feladat eredményállapotát. Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött|
|RunbookName | Megadja a runbook-feladat nevét.|
|SourceSystem | Megadja az elküldött adatok forrásrendszerét. Az Automatizálás esetében az érték OpsManager|
|StreamType | Megadja az esemény típusát. Lehetséges értékek:<br>- Részletes<br>- Kimenet<br>- Hiba<br>- Figyelmeztetés|
|SubscriptionId | Megadja a feladat előfizetési azonosítóját.
|Time | A runbook-feladat végrehajtásának dátuma és időpontja.|

### <a name="job-streams"></a>Feladatstreamek

|Tulajdonság | Leírás|
|----------|----------|
|Hívó |  A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
|Kategória | Az adattípus besorolása. Az Automation esetében az érték JobStreams.|
|JobId | GUID, amely a runbook-feladat azonosítója.|
|operationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az automatizálás esetében az érték A feladat.|
|ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
|resourceId | Megadja az erőforrás-azonosítót az Azure-ban. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat. Az Automation esetében az érték Azure Automation.|
|ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|resultType | A runbook-feladat eredménye az esemény létrehozásának időpontjában. A lehetséges érték a következő:<br>- Folyamatban|
|resultDescription | A runbook kimeneti streamjét tartalmazza.|
|RunbookName | A runbook neve.|
|SourceSystem | Megadja az elküldött adatok forrásrendszerét. Az Automatizálás esetében az érték opsmanager.|
|StreamType | A feladatstream típusa. Lehetséges értékek:<br>- Haladás<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes|
|Time | A runbook-feladat végrehajtásának dátuma és időpontja.|

Ha olyan naplókeresést hajt végre, amely a JobLogs vagy **a JobStreams** **kategóriarekordjait** adja vissza, kiválaszthatja a **JobLogs** vagy **a JobStreams nézetet,** amely a keresés által visszaadott frissítéseket összegző csempéket jeleníti meg.

## <a name="sample-log-searches"></a>Naplókeresési minták

A következő táblázat a megoldás által összegyűjtött feladatrekordokkal kapcsolatos naplókeresési mintákat tartalmazza.

|Lekérdezés | Leírás|
|----------|----------|
|Sikeresen befejezett runbook ScheduledStartStop_Parent feladatkeresése | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Sikeresen befejezett runbook SequencedStartStop_Parent feladatának keresése | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>A megoldás megtekintése

A megoldás eléréséhez keresse meg az Automation-fiókot, és válassza **a Munkaterület** lehetőséget a KAPCSOLÓDÓ **ERŐFORRÁSOK csoportban.** A naplóelemzési lapon válassza a **Megoldások** **csoportáltalános**lehetőséget. A **Megoldások** lapon válassza ki a **Start-Stop-VM[munkaterület]** megoldást a listából.

A megoldás kiválasztása a **Start-Stop-VM[workspace]** megoldáslapot jeleníti meg. Itt áttekintheti a fontos részleteket, például a **StartStopVM** csempét. A Log Analytics-munkaterülethez ugyanúgy, mint a Log Analytics-munkaterület, ez a csempe a runbook-feladatok számát és grafikus ábrázolását jeleníti meg a sikeresen elkezdett és sikeresen befejeződött megoldáshoz.

![Automation Update Management megoldás lap](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Innen a fánkcsempére kattintva további elemzéseket végezhet a feladatrekordokról. A megoldás irányítópultja a feladatelőzményeket és az előre definiált naplókeresési lekérdezéseket jeleníti meg. Váltson át a naplóelemzés speciális portáljára a keresési lekérdezések alapján való kereséshez.

## <a name="configure-email-notifications"></a>E-mail-értesítések konfigurálása

Ha módosítani szeretné az e-mail értesítéseket a megoldás telepítése után, módosítsa a telepítés során létrehozott műveletcsoportot.  

> [!NOTE]
> Az Azure Government Cloud előfizetései nem támogatják a megoldás e-mail funkcióit.

Az Azure Portalon keresse meg a monitor-> műveletcsoportok. Jelölje ki a **StartStop_VM_Notication**nevű műveletcsoportot.

![Automation Update Management megoldás lap](media/automation-solution-vm-management/azure-monitor.png)

A **StartStop_VM_Notification** lapon kattintson a **Részletek szerkesztése** gombra a **Részletek csoportban.** Ezzel megnyitja az **E-mail/SMS/Push/Voice** oldalt. Frissítse az e-mail címet, és kattintson az **OK** gombra a módosítások mentéséhez.

![Automation Update Management megoldás lap](media/automation-solution-vm-management/change-email.png)

Másik lehetőségként további műveleteket is hozzáadhat a műveletcsoporthoz, ha többet szeretne megtudni a műveletcsoportokról, olvassa el a [műveletcsoportokat.](../azure-monitor/platform/action-groups.md)

Az alábbiakban egy példa e-mailt, amely akkor küldi el, amikor a megoldás leállítja a virtuális gépek.

![Automation Update Management megoldás lap](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Virtuális gépek hozzáadása/kizárása

A megoldás lehetővé teszi, hogy a megoldás által megcélzott virtuális gépek et adjon hozzá, vagy kifejezetten kizárja a gépeket a megoldásból.

### <a name="add-a-vm"></a>Virtuális gép hozzáadása

Van néhány lehetőség, amely segítségével győződjön meg arról, hogy a virtuális gép szerepel a Start/Stop megoldás futtatásakor.

* A megoldás minden szülő [runbookja](#runbooks) rendelkezik egy **VMList** paraméterrel. A virtuális gépnevek vesszővel tagolt listáját átadhatja ennek a paraméternek, amikor a megfelelő szülő runbookot ütemezi a helyzethez, és ezek a virtuális gépek a megoldás futtatásakor lesznek.

* Több virtuális gép kijelöléséhez állítsa be a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupNames** az elindítani vagy leállítani kívánt virtuális gépeket tartalmazó erőforráscsoportnevekkel. Ezt az értéket is `*`beállíthatja a értékre, hogy a megoldás az előfizetés összes erőforráscsoportján fusson.

### <a name="exclude-a-vm"></a>Virtuális gép kizárása

Ha ki szeretne zárni egy virtuális gép a megoldásból, hozzáadhatja azt a **External_ExcludeVMNames** változó. Ez a változó egy vesszővel tagolt lista az egyes virtuális gépek, hogy kizárják a Start/Stop megoldás. Ez a lista legfeljebb 140 virtuális gépre korlátozódik. Ha 140-nél több virtuális gépet ad hozzá ehhez a vesszővel tagolt listához, előfordulhat, hogy a kizárt virtuális gépek véletlenül elindulnak vagy leállnak.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Az indítási és leállítási ütemezés módosítása

Az indítási és leállítási ütemezések kezelése ebben a megoldásban ugyanazokat a lépéseket követi, mint amelyeket a [Runbook ütemezése](automation-schedules.md)az Azure Automationben című dokumentumban ismertetett. A virtuális gépek indításához és leállításához külön ütemezésre van szükség.

A megoldás konfigurálása, hogy csak állítsa le a virtuális gépek egy bizonyos időpontban támogatott. Ebben a forgatókönyvben csak hozzon létre egy **stop** ütemezést, és nincs megfelelő **kezdési** ütemezés. Ehhez a következőket kell tennie:

1. Győződjön meg arról, hogy hozzáadta az erőforráscsoportokat a virtuális gépek leállításához a **External_Stop_ResourceGroupNames** változóban.
2. Hozzon létre saját ütemezést a virtuális gépek leállításának idejére vonatkozóan.
3. Nyissa meg a **ScheduledStartStop_Parent** a runbookot, és kattintson **az Ütemezés gombra.** Ez lehetővé teszi az előző lépésben létrehozott ütemezés kiválasztását.
4. Válassza a **Paraméterek lehetőséget, és futtassa a beállításokat,** és állítsa az ACTION paramétert "Stop" beállításra.
5. Kattintson az **OK** gombra a módosítások mentéséhez.

## <a name="update-the-solution"></a>A megoldás frissítése

Ha a megoldás egy korábbi verzióját telepítette, először törölnie kell azt a fiókjából, mielőtt egy frissített kiadást telepítene. Kövesse a [lépéseket a megoldás eltávolításához,](#remove-the-solution) majd kövesse a fenti lépéseket [a megoldás üzembe helyezéséhez.](#deploy-the-solution)

## <a name="remove-the-solution"></a>A megoldás eltávolítása

Ha úgy dönt, hogy már nem kell használnia a megoldást, törölheti azt az Automation-fiókból. A megoldás törlése csak eltávolítja a runbookok. Nem törli a megoldás hozzáadásakor létrehozott ütemezéseket vagy változókat. Ezeket az eszközöket manuálisan kell törölnie, ha nem használja őket más runbookokkal.

A megoldás törléséhez hajtsa végre az alábbi lépéseket:

1. Az Automation-fiók **Kapcsolódó erőforrások csoportban**válassza a **Csatolt munkaterület**lehetőséget.
1. Válassza **az Ugrás munkaterületre**lehetőséget.
1. Az **Általános csoportban**válassza a **Megoldások**lehetőséget. 
1. A **Megoldások** lapon válassza ki a **Start-Stop-VM[Workspace]** megoldást. A **VMManagementSolution[Workspace]** lap Törlés parancsa menüpontból válassza a **Törlés parancsot.**<br><br> ![Virtuálisgép Mgmt-megoldás törlése](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. A **Megoldás törlése** ablakban ellenőrizze, hogy törölni szeretné-e a megoldást.
1. Amíg az adatok at ellenőriztük és a megoldást törölték, a menü **Értesítések** menüjében nyomon követheti annak előrehaladását. A megoldás eltávolításának folyamata után visszatér a **Megoldások** lapra.

Az Automation-fiók és a Log Analytics munkaterület nem törlődik a folyamat részeként. Ha nem szeretné megőrizni a Log Analytics munkaterületet, manuálisan kell törölnie. Ez az Azure Portalon valósítható meg:

1. Az Azure Portalon keresse meg és válassza **a Log Analytics-munkaterületeket.**
1. A **Log Analytics-munkaterületek** lapon jelölje ki a munkaterületet.
1. Válassza a Munkaterület beállításai lap **Törlés** parancsát.

Ha nem szeretné megtartani az Azure Automation-fiók összetevőit, manuálisan törölheti őket. A megoldás által létrehozott runbookok, változók és ütemezések listáját a [Solution összetevői](#solution-components)című témakörben találja.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne tudni arról, hogyan hozhat létre különböző keresési lekérdezéseket, és hogyan tekintheti át az Automation-feladatnaplókat az Azure Monitor naplóival, olvassa el [a Keresésnaplózás az Azure Monitor naplóiban című témakört.](../log-analytics/log-analytics-log-searches.md)
- A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza.
- Ha többet szeretne megtudni az Azure Monitor naplóiról és adatgyűjtési forrásairól, olvassa [el az Azure Storage-adatok gyűjtése az Azure Monitor naplóinak áttekintése című témakört.](../azure-monitor/platform/collect-azure-metrics-logs.md)
