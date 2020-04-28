---
title: Virtuális gépek indítása/leállítása munkaidőn kívüli megoldás esetén
description: Ez a virtuálisgép-kezelési megoldás elindítja és leállítja az Azure-beli virtuális gépeket, és proaktív módon figyeli Azure Monitor naplókból.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 631c9b37cf1fec0d39c3c362c6bc303a576d6b7c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187330"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Virtuális gépek indítása/leállítása munkaidőn kívüli megoldás esetén Azure Automation

A virtuális gépek **indítása és leállítása a munkaidőn kívüli** megoldás indításakor vagy leállításakor az Azure Virtual Machines szolgáltatás elindul vagy leáll. A felhasználó által meghatározott ütemterveken megkezdi vagy leállítja a gépeket, bepillantást Azure Monitor naplókba, és a választható e-maileket a [műveleti csoportok](../azure-monitor/platform/action-groups.md)használatával küldi el. A megoldás a legtöbb esetben támogatja a Azure Resource Manager és a klasszikus virtuális gépeket is. 

Ez a megoldás a [Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0) parancsmag használatával indítja el a virtuális gépeket. A [stop-AzureRmVM](https://docs.microsoft.com/powershell/module/AzureRM.Compute/Stop-AzureRmVM?view=azurermps-6.13.0) használatával állítja le a virtuális gépeket.

> [!NOTE]
> A **virtuális gépek indítása/leállítása a munkaidőn kívüli** megoldásban frissítve lett, hogy támogassa az elérhető Azure-modulok legújabb verzióit.

A megoldás egy decentralizált, alacsony költségű automatizálási lehetőséget biztosít azon felhasználók számára, akik optimalizálni szeretnék a virtuális gépek költségeit. Ezzel a megoldással a következőket teheti:

- [Virtuális gépek indítása és leállítása](automation-solution-vm-management-config.md#schedule).
- A virtuális gépeket úgy ütemezhet, hogy az Azure- [címkék használatával](automation-solution-vm-management-config.md#tags) növekvő sorrendben induljon el és álljon le (a klasszikus virtuális gépek nem támogatottak).
- Virtuális gépek alapleállása [alacsony CPU-használat](automation-solution-vm-management-config.md#cpuutil)alapján.

A jelenlegi megoldás korlátai a következők:

- Bármely régióban felügyeli a virtuális gépeket, de csak a Azure Automation-fiókkal megegyező előfizetésben használható.
- Az Azure-ban érhető el, és Azure Government bármely olyan régióban, amely támogatja a Log Analytics munkaterületet, Azure Automation fiókot és riasztásokat. Azure Government régió jelenleg nem támogatja az e-mail-funkciókat.

## <a name="solution-prerequisites"></a>Megoldás előfeltételei

A megoldás runbookok egy Azure-beli [futtató fiókkal](automation-create-runas-account.md)működik. A futtató fiók az előnyben részesített hitelesítési módszer, mert a tanúsítvány-hitelesítést használ olyan jelszó helyett, amely esetleg lejáró vagy gyakran változhat.

Javasoljuk, hogy használjon külön Automation-fiókot a **virtuális gépek indításához/leállításához a munkaidőn kívüli megoldás során** . Az Azure-modulok verziói gyakran frissülnek, és a paraméterek változhatnak. A megoldás nem frissül ugyanazon a ritmuson, és előfordulhat, hogy az általa használt parancsmagok újabb verziói nem működnek. Javasoljuk, hogy tesztelje a modul frissítéseit egy tesztelési Automation-fiókban, mielőtt azokat az üzemi Automation-fiók (ok) ba importálja.

## <a name="solution-permissions"></a>Megoldási engedélyek

Bizonyos engedélyekkel kell rendelkeznie a **virtuális gépek indítása és leállítása a munkaidőn kívüli** megoldásban való üzembe helyezéséhez. Az engedélyek eltérőek, ha a megoldás egy előre létrehozott Automation-fiókot és Log Analytics munkaterületet használ a szükséges engedélyekkel, ha a megoldás új fiókot és munkaterületet hoz létre az üzembe helyezés során. 

Nem kell konfigurálnia az engedélyeket, ha Ön közreműködő az előfizetésben és egy globális rendszergazda a Azure Active Directory-bérlőben. Ha nem rendelkezik ezekkel a jogosultságokkal, vagy egyéni szerepkört kell konfigurálnia, győződjön meg arról, hogy rendelkezik az alább ismertetett engedélyekkel.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>A meglévő Automation-fiókra és Log Analytics-munkaterületre vonatkozó engedélyek

Ha a **virtuális gépeket munkaidőn kívüli** megoldásként szeretné telepíteni egy meglévő Automation-fiókra és log Analytics munkaterületre, a megoldást telepítő felhasználónak a következő engedélyekkel kell rendelkeznie az erőforráscsoport hatókörében. A szerepkörökkel kapcsolatos további tudnivalókért tekintse meg [Az Azure-erőforrások egyéni szerepkörei](../role-based-access-control/custom-roles.md)című témakört.

| Engedély | Hatókör|
| --- | --- |
| Microsoft. Automation/automationAccounts/READ | Erőforráscsoport |
| Microsoft. Automation/automationAccounts/változók/írás | Erőforráscsoport |
| Microsoft. Automation/automationAccounts/ütemterv/írás | Erőforráscsoport |
| Microsoft. Automation/automationAccounts/runbookok/Write | Erőforráscsoport |
| Microsoft. Automation/automationAccounts/kapcsolatok/írás | Erőforráscsoport |
| Microsoft. Automation/automationAccounts/tanúsítványok/írás | Erőforráscsoport |
| Microsoft. Automation/automationAccounts/modulok/írás | Erőforráscsoport |
| Microsoft. Automation/automationAccounts/modulok/olvasás | Erőforráscsoport |
| Microsoft. Automation/automationAccounts/jobSchedules/Write | Erőforráscsoport |
| Microsoft. Automation/automationAccounts/feladatok/írás | Erőforráscsoport |
| Microsoft. Automation/automationAccounts/feladatok/olvasás | Erőforráscsoport |
| Microsoft. OperationsManagement/megoldások/írás | Erőforráscsoport |
| Microsoft. OperationalInsights/munkaterületek/* | Erőforráscsoport |
| Microsoft. bepillantások/diagnosticSettings/írás | Erőforráscsoport |
| Microsoft. bepillantások/ActionGroups/írás | Erőforráscsoport |
| Microsoft. bepillantások/ActionGroups/olvasás | Erőforráscsoport |
| Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoport |
| Microsoft. Resources/üzemelő példány/* | Erőforráscsoport |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Engedélyek új Automation-fiókhoz és új Log Analytics munkaterület

A **virtuális gépek indítása/leállítása munkaidőn kívüli** megoldásként egy új Automation-fiókra és log Analytics munkaterületre is telepíthető. Ebben az esetben a megoldást telepítő felhasználónak az előző szakaszban meghatározott engedélyekkel, valamint az ebben a szakaszban meghatározott engedélyekkel kell rendelkeznie. 

A megoldást telepítő felhasználónak a következő szerepkörökre van szüksége:

- Az előfizetés rendszergazdája. Ez a szerepkör szükséges a klasszikus futtató fiók létrehozásához, ha a klasszikus virtuális gépeket fogja kezelni. Alapértelmezés szerint a [klasszikus futtató fiókok](automation-create-standalone-account.md#create-a-classic-run-as-account) már nem jönnek létre.
- Tagság a [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) alkalmazás fejlesztői szerepkörben. A futtató fiókok konfigurálásával kapcsolatos további információkért lásd: [engedélyek a futtató fiókok konfigurálására](manage-runas-account.md#permissions).
- Közreműködő az előfizetésen vagy a következő engedélyeken.

| Engedély |Hatókör|
| --- | --- |
| Microsoft. Authorization/Operations/READ | Előfizetés|
| Microsoft. Authorization/engedélyek/olvasás |Előfizetés|
| Microsoft. Authorization/roleAssignments/olvasás | Előfizetés |
| Microsoft.Authorization/roleAssignments/write | Előfizetés |
| Microsoft. Authorization/roleAssignments/delete | Előfizetés |
| Microsoft. Automation/automationAccounts/kapcsolatok/olvasás | Erőforráscsoport |
| Microsoft. Automation/automationAccounts/tanúsítványok/olvasás | Erőforráscsoport |
| Microsoft. Automation/automationAccounts/írás | Erőforráscsoport |
| Microsoft. OperationalInsights/munkaterületek/írás | Erőforráscsoport |

## <a name="solution-components"></a>Megoldás-összetevők

A **virtuális gépek indítása/leállítása a munkaidőn kívüli megoldásban** előre konfigurált runbookok, ütemtervekkel és az Azure monitor naplókkal való integrációval rendelkezik. Ezekkel az elemekkel testre szabhatja a virtuális gépek indítását és leállítását az üzleti igényeknek megfelelően.

### <a name="runbooks"></a>Runbookok

A következő táblázat felsorolja azokat a runbookok, amelyeket a megoldás telepít az Automation-fiókjába. Ne módosítsa a runbook-kódot. Ehelyett saját runbook írhat az új funkciókhoz.

> [!IMPORTANT]
> Ne futtasson közvetlenül olyan runbook, amelynek a **gyermeke** hozzá van fűzve a nevéhez.

Az összes szülő runbookok tartalmazza `WhatIf` a paramétert. Ha igaz értékre van állítva, a paraméter támogatja a runbook által a paraméter nélkül futtatott pontos viselkedést, és ellenőrzi, hogy a megfelelő virtuális gépek célozva vannak-e. A runbook csak akkor hajtja végre a definiált műveleteit, ha a `WhatIf` paraméter értéke hamis.

|Forgatókönyv | Paraméterek | Leírás|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Meghívva a szülő runbook. Ez a runbook a riasztásokat erőforrás-alapon hozza létre az automatikus leállítási forgatókönyvhöz.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: true vagy FALSE  | Létrehozza vagy frissíti az Azure riasztási szabályokat a célként megadott előfizetésben vagy erőforráscsoportok virtuális gépeken. <br> `VMList`a a virtuális gépek vesszővel tagolt listája. Például: `vm1, vm2, vm3`.<br> `WhatIf`lehetővé teszi a runbook logika érvényesítését a végrehajtás nélkül.|
|AutoStop_Disable | None | Letiltja az automatikus leállítási riasztásokat és az alapértelmezett ütemtervet.|
|AutoStop_VM_Child | WebHookData | Meghívva a szülő runbook. A riasztási szabályok meghívja ezt a runbook egy klasszikus virtuális gép leállításához.|
|AutoStop_VM_Child_ARM | WebHookData |Meghívva a szülő runbook. A riasztási szabályok meghívja ezt a runbook egy virtuális gép leállítására.  |
|ScheduledStartStop_Base_Classic | Felhőszolgáltatásneve<br> Művelet: indítás vagy leállítás<br> VMList  | Elvégzi a művelet indítását vagy leállítását a klasszikus virtuálisgép-csoportban Cloud Services alapján. |
|ScheduledStartStop_Child | VMName <br> Művelet: indítás vagy leállítás <br> ResourceGroupName | Meghívva a szülő runbook. Végrehajt egy indítási vagy leállítási műveletet az ütemezett leállítás előtt.|
|ScheduledStartStop_Child_Classic | VMName<br> Művelet: indítás vagy leállítás<br> ResourceGroupName | Meghívva a szülő runbook. Indítási vagy leállítási műveletet hajt végre a klasszikus virtuális gépek ütemezett leállításakor. |
|ScheduledStartStop_Parent | Művelet: indítás vagy leállítás <br>VMList <br> WhatIf: true vagy FALSE | Elindítja vagy leállítja az előfizetésben lévő összes virtuális gépet. Szerkessze a `External_Start_ResourceGroupNames` változókat, és `External_Stop_ResourceGroupNames` csak ezekre a célcsoportokra legyen végrehajtva. Az egyes virtuális gépeket a `External_ExcludeVMNames` változó frissítésével is kizárhatja.|
|SequencedStartStop_Parent | Művelet: indítás vagy leállítás <br> WhatIf: true vagy FALSE<br>VMList| A **sequencestart** és a **sequencestop** nevű címkéket hoz létre minden olyan virtuális gépen, amelynél le kívánja állítani az indítási/leállítási tevékenységet. A címkék nevei megkülönböztetik a kis-és nagybetűket. A címke értékének pozitív egész számnak (1, 2, 3) kell lennie, amely megfelel az elindítani vagy leállítani kívánt sorrendnek. <br>**Megjegyzés**: a virtuális gépeknek a (z) `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames`, és `External_ExcludeVMNames` változóban definiált erőforráscsoport-csoportokon belül kell lenniük. A megfelelő címkékkel kell rendelkezniük a műveletek életbe léptetéséhez.|

### <a name="variables"></a>Változók

A következő táblázat felsorolja az Automation-fiókban létrehozott változókat. Csak az előtaggal rendelkező `External`változók módosítása. Az `Internal` előrögzített változók módosítása nem kívánt hatásokat okoz.

> [!NOTE]
> A virtuális gép nevének és az erőforráscsoport korlátozásai nagyrészt a változó méretének köszönhető. Lásd: [változó eszközök Azure Automationban](https://docs.microsoft.com/azure/automation/shared-resources/variables).

|Változó | Leírás|
|---------|------------|
|External_AutoStop_Condition | Egy riasztás elindítása előtt a feltétel konfigurálásához szükséges feltételes operátor. `GreaterThan`Elfogadható értékek `GreaterThanOrEqual` `LessThan`:,, és `LessThanOrEqual`.|
|External_AutoStop_Description | A virtuális gép leállítására vonatkozó riasztás, ha a CPU-hányad meghaladja a küszöbértéket.|
|External_AutoStop_Frequency | A szabály kiértékelésének gyakorisága Ez a paraméter TimeSpan formátumban fogadja a bemenetet. A lehetséges értékek 5 perc és 6 óra között vannak. |
|External_AutoStop_MetricName | Annak a teljesítménymutatónak a neve, amelyhez az Azure-riasztási szabályt konfigurálni kell.|
|External_AutoStop_Severity | A metrikai riasztás súlyossága, amely 0 és 4 között lehet. |
|External_AutoStop_Threshold | A változóban `External_AutoStop_MetricName`megadott Azure riasztási szabály küszöbértéke. A százalékos értékek 1-től 100-ig terjedhetnek.|
|External_AutoStop_TimeAggregationOperator | A kiválasztott ablak méretére alkalmazott időösszesítési operátor a feltétel kiértékeléséhez. Elfogadható értékek `Average` `Minimum` `Maximum`:,,, és `Last` `Total`|
|External_AutoStop_TimeWindow | Annak az ablaknak a mérete, amely alatt az Azure elemzi a kiválasztott mérőszámokat a riasztások aktiválásához. Ez a paraméter TimeSpan formátumban fogadja a bemenetet. A lehetséges értékek 5 perc és 6 óra között vannak.|
|External_EnableClassicVMs| Az érték azt határozza meg, hogy a klasszikus virtuális gépeket a megoldás célozza-e. Az alapértelmezett érték TRUE (igaz). Ezt a változót állítsa hamis értékre Azure Cloud Solution Provider (CSP) előfizetésekhez. A klasszikus virtuális gépek [klasszikus futtató fiókot](automation-create-standalone-account.md#create-a-classic-run-as-account)igényelnek.|
|External_ExcludeVMNames | A kizárni kívánt virtuálisgép-nevek vesszővel tagolt listája, amely 140 virtuális GÉPRE korlátozódik. Ha több mint 140 virtuális gépet ad hozzá a listához, előfordulhat, hogy a kizárni kívánt virtuális gépeket véletlenül elindítják vagy leállították.|
|External_Start_ResourceGroupNames | Egy vagy több indítási műveletre irányuló erőforráscsoport vesszővel tagolt listája.|
|External_Stop_ResourceGroupNames | Egy vagy több leállítási műveletet célzó erőforráscsoport vesszővel tagolt listája.|
|External_WaitTimeForVMRetrySeconds |Az a várakozási idő másodpercben, ameddig a virtuális gépeken végrehajtandó műveletek a **SequencedStartStop_Parent** runbook. Ez a változó lehetővé teszi a runbook számára, hogy a következő művelet folytatása előtt megvárja a gyermek műveleteit a megadott számú másodpercig. A maximális várakozási idő 10800, vagy három óra. Az alapértelmezett érték 2100 másodperc.|
|Internal_AutomationAccountName | Megadja az Automation-fiók nevét.|
|Internal_AutoSnooze_ARM_WebhookURI | A webhook URI-ja a virtuális gépek autostop forgatókönyvéhez lett meghívva.|
|Internal_AutoSnooze_WebhookUri | A webhook URI-ja a klasszikus virtuális gépekhez tartozó autostop forgatókönyvhöz lett meghívva.|
|Internal_AzureSubscriptionId | Az Azure-előfizetés azonosítója.|
|Internal_ResourceGroupName | Az Automation-fiók erőforráscsoport-neve.|

>[!NOTE]
>A változó `External_WaitTimeForVMRetryInSeconds`esetében az alapértelmezett érték 600 és 2100 között frissült. 

Minden forgatókönyvben a virtuális gépek `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`megcélzásához `External_ExcludeVMNames` szükséges változók, valamint a **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**és **ScheduledStartStop_Parent** runbookok vesszővel elválasztott virtuálisgép-listája szerepel. Ez azt eredményezi, hogy a virtuális gépeknek meg kell egyezniük a cél erőforráscsoporthoz az indítási és leállítási műveletek elvégzéséhez. A logika a Azure Policyhoz hasonlóan működik, amelyekben megcélozhatja az előfizetést vagy az erőforráscsoportot, valamint az újonnan létrehozott virtuális gépek által örökölt műveleteket is. Ezzel a módszerrel elkerülhető, hogy minden virtuális gép esetében külön ütemtervet kell fenntartani, és a felügyelet megkezdődik, és a méretezési folyamat leáll.

### <a name="schedules"></a>Ütemezések

A következő táblázat az Automation-fiókban létrehozott alapértelmezett ütemterveket sorolja fel.Módosíthatók, vagy létrehozhatók saját egyéni ütemtervek is.Alapértelmezés szerint az összes ütemterv le van tiltva, kivéve a **Scheduled_StartVM** és az **Scheduled_StopVM** ütemtervet.

Ne engedélyezze az összes ütemtervet, mert ez az átfedésben lévő ütemezett műveleteket is létrehozhatja. Érdemes eldönteni, hogy mely optimalizálásokat kívánja elvégezni, és ennek megfelelően módosítania kell azokat. További magyarázatért tekintse meg az Áttekintés szakaszban található példákat.

|Ütemterv neve | Frequency | Leírás|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8 óránként | A **AutoStop_CreateAlert_Parent** runbook 8 óránként futtatja, ami viszont leállítja a virtuálisgép-alapú értékeket `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames`és `External_ExcludeVMNames` változókat. Másik lehetőségként megadhatja a virtuális gépek vesszővel tagolt listáját a `VMList` paraméter használatával.|
|Scheduled_StopVM | Felhasználó által definiált, napi | A **ScheduledStopStart_Parent** runbook `Stop` minden nap paraméterével futtatja a megadott időpontban.A automatikusan leállítja az összes olyan virtuális gépet, amely megfelel a változó eszközök által meghatározott szabályoknak.A kapcsolódó ütemezett ütemezés engedélyezése **– StartVM**.|
|Scheduled_StartVM | Felhasználó által definiált, napi | A **ScheduledStopStart_Parent** runbook a megadott időpontban `Start` minden nap paraméter értékével futtatja. A automatikusan elindítja az összes olyan virtuális gépet, amely megfelel a változó eszközök által meghatározott szabályoknak.A kapcsolódó ütemezett ütemezés engedélyezése **– StopVM**.|
|Sorozatos – StopVM | 1:00 AM (UTC), minden pénteken | A **Sequenced_StopStop_Parent** runbook a megadott időpontban `Stop` minden pénteken található paraméter értékkel futtatja.A szekvenciálisan (növekvő) leállítja az összes virtuális gépet a megfelelő változók által definiált **SequenceStop** címkével. További információ a címkézési értékekről és az adategység változókról: [runbookok](#runbooks).Engedélyezze a kapcsolódó ütemezéseket, a **Sequenced-StartVM**.|
|Sorozatos – StartVM | 1:00 PM (UTC), minden hétfőn | A **SequencedStopStart_Parent** runbook a megadott időponthoz tartozó `Start` minden hétfőn lévő paraméter értékével futtatja. A szekvenciálisan (csökkenő) elindítja az összes virtuális gépet a megfelelő változók által meghatározott **SequenceStart** . További információ a címkék értékeiről és a változó eszközökről: [runbookok](#runbooks). Engedélyezze a kapcsolódó ütemezéseket, a **Sequenced-StopVM**.

## <a name="use-of-the-solution-with-classic-vms"></a>A megoldás használata klasszikus virtuális gépekkel

Ha a klasszikus virtuális gépek esetében a **virtuális gépek indítása/leállítása munkaidőn kívüli** megoldásként van használatban, akkor az Automation az összes virtuális gépet egymás után dolgozza fel a Cloud Service-ben. A virtuális gépek továbbra is párhuzamosan lesznek feldolgozva a különböző felhőalapú szolgáltatások között. 

A megoldás klasszikus virtuális gépekkel való használatához klasszikus futtató fiókra van szükség, amely alapértelmezés szerint nem jön létre. Klasszikus futtató fiók létrehozásával kapcsolatos utasításokért lásd: [klasszikus futtató fiók létrehozása](automation-create-standalone-account.md#create-a-classic-run-as-account).

Ha a Cloud Service-ben több mint 20 virtuális gép van, a következő javaslatok közül választhat:

* Hozzon létre több ütemtervet a szülő runbook **ScheduledStartStop_Parent** és ütemezzen 20 virtuális gépet. 
* Az ütemterv tulajdonságainál a `VMList` paraméter használatával vesszővel tagolt listaként adhatja meg a virtuális gépek nevét. 

Ellenkező esetben, ha a megoldás automatizálási feladata több mint három órát futtat, átmenetileg törlődik, vagy leállt a [méltányos megosztási](automation-runbook-execution.md#fair-share) korláton belül.

Az Azure CSP-előfizetések csak a Azure Resource Manager modellt támogatják. A nem Azure Resource Manager szolgáltatások nem érhetők el a programban. Ha a **virtuális gépek elindítása és leállítása a munkaidőn kívüli megoldás futtatása során** leáll, előfordulhat, hogy hibaüzeneteket kap, mivel a klasszikus erőforrások kezeléséhez parancsmagokkal rendelkezik. A CSP-vel kapcsolatos további tudnivalókért tekintse meg a [CSP-előfizetésekben elérhető szolgáltatások](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)című témakört. Ha CSP-előfizetést használ, a [External_EnableClassicVMs](#variables) változót false értékre kell állítania az üzembe helyezés után.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>A megoldás engedélyezése

A megoldás használatának megkezdéséhez kövesse a [virtuális gépek indítási/leállítási megoldásának engedélyezése](automation-solution-vm-management-enable.md)című témakör lépéseit.

## <a name="view-the-solution"></a>A megoldás megtekintése

A következő mechanizmusok egyikével férhet hozzá a megoldáshoz az engedélyezése után:

* Az Automation-fiókjából válassza a **virtuális gép indítása/leállítása** **kapcsolódó erőforrások**lehetőséget. A virtuális gép indítása/leállítása lapon válassza az oldal jobb oldalán található **megoldás kezelése** lehetőséget a virtuálisgép- **megoldások indítása/leállítása**lehetőség alatt.

* Navigáljon az Automation-fiókjához társított Log Analytics munkaterületre. Miután kiválasztotta a munkaterületet, válassza a bal oldali ablaktábla **megoldások** elemét. A megoldások lapon válassza a listáról a **Start-Stop-VM [Workspace]** megoldást.  

A megoldás kiválasztásakor megjelenik a **Start-Stop-VM [Workspace]** megoldás lapja. Itt áttekintheti a fontos adatokat, például a **StartStopVM** csempén található információkat. A Log Analytics munkaterülethez hasonlóan ez a csempe a megkezdett és sikeresen befejezett megoldás runbook-feladatainak számát és grafikus megjelenítését jeleníti meg.

![Automation Update Management megoldás lapja](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

A munkarekordok további elemzését a fánk csempére kattintva végezheti el. A megoldás irányítópultján a feladatok előzményei és az előre definiált naplók keresési lekérdezések láthatók. Váltson a log Analytics speciális portálra a keresési lekérdezések alapján történő kereséshez.

## <a name="update-the-solution"></a>A megoldás frissítése

Ha már telepítette a megoldás egy korábbi verzióját, a frissített kiadás telepítése előtt törölje azt a fiókból. Kövesse a [megoldás eltávolításához](#remove-the-solution) szükséges lépéseket, majd kövesse a lépéseket a [megoldás üzembe helyezéséhez](automation-solution-vm-management-enable.md).

## <a name="remove-the-solution"></a>A megoldás eltávolítása

Ha már nincs szüksége a megoldás használatára, törölheti az Automation-fiókból. A megoldás törlése csak a runbookok távolítja el. Nem törli a megoldás hozzáadásakor létrehozott ütemterveket és változókat. Ezeket az eszközöket manuálisan távolíthatja el, ha nem használja azokat más runbookok.

A megoldás törlése:

1. Az Automation-fiókban válassza a **kapcsolódó erőforrások**alatt található **csatolt munkaterület** lehetőséget.

2. Válassza **a ugrás munkaterületre**lehetőséget.

3. Kattintson az **általános**területen a **megoldások** elemre. 

4. A megoldások lapon válassza a **Start-Stop-VM [munkaterület]** megoldást. 

5. A **VMManagementSolution [munkaterület]** lapon válassza a **Törlés** lehetőséget a menüből.<br><br> ![VM mgmt-megoldás törlése](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. A **megoldás törlése** ablakban erősítse meg, hogy törölni kívánja a megoldást.

7. Az információk ellenőrzése és a megoldás törlése után nyomon követheti a menüből kiválasztott **értesítések**területen látható előrehaladást. A megoldás oldalának elindítása után visszatért a megoldás lapra.

Az Automation-fiók és a Log Analytics munkaterület nem törlődik a folyamat részeként. Ha nem szeretné megtartani a Log Analytics munkaterületet, manuálisan kell törölnie a Azure Portalból:

1. Keresse meg és válassza ki **log Analytics munkaterületeket**.

2. A Log Analytics munkaterület lapon válassza ki a munkaterületet.

3. Válassza a **Törlés** lehetőséget a munkaterület Beállítások lapjának menüjében.

4. Ha nem szeretné megtartani a Azure Automation fiók [megoldásának összetevőit](#solution-components), manuálisan is törölheti őket.

## <a name="next-steps"></a>További lépések

Az Azure-beli virtuális gépek esetében [engedélyezze](automation-solution-vm-management-enable.md) a **virtuális gépek indítását és leállítását munkaidőn kívüli** megoldásként.
