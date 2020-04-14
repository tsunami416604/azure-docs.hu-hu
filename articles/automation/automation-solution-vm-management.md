---
title: Virtuális gépek indítása/leállítása munkaidőn kívüli megoldás közben
description: Ez a virtuálisgép-felügyeleti megoldás elindítja és leállítja az Azure virtuális gépek ütemezés szerint, és proaktív módon figyeli az Azure Monitor naplók.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: fbcd4ea174d4b6a2a45495c32f178ed1bd01bbe0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261346"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Virtuális gépek indítása/leállítása munkaidőn kívüli megoldás közben az Azure Automationben

A **kezdő és leállító virtuális gépek munkaidőn kívüli megoldás indítása** kor vagy leáll az Azure virtuális gépek. Elindítja vagy leállítja a gépeket a felhasználó által meghatározott ütemezések en, betekintést nyújt az Azure Monitor naplóin keresztül, és opcionális e-maileket küld [műveletcsoportok](../azure-monitor/platform/action-groups.md)használatával. A megoldás támogatja az Azure Resource Manager és a klasszikus virtuális gépek a legtöbb forgatókönyv. 

Ez a megoldás egy decentralizált, alacsony költségű automatizálási lehetőséget biztosít azoknak a felhasználóknak, akik optimalizálni szeretnék a virtuális gép költségeit. Ezzel a megoldással a következőket teheti:

- [A virtuális gépek indításának és leállításának ütemezése](automation-solution-vm-management-config.md#schedule).
- Ütemezze a virtuális gépeket, hogy növekvő sorrendben induljanak el és állítsanak le [az Azure-címkék használatával](automation-solution-vm-management-config.md#tags) (nem támogatott a klasszikus virtuális gépek).
- Automatikus számláló virtuális gépek alacsony [CPU-használat](automation-solution-vm-management-config.md#cpuutil)alapján .

> [!NOTE]
> A **kezdő/leállító virtuális gépek munkaidőn kívüli** megoldás frissítésre került, hogy támogassa az Azure-modulok elérhető legújabb verzióit.

A jelenlegi megoldással kapcsolatban a következők ben korlátozások vannak:

- A virtuális gépeket bármely régióban kezeli, de csak ugyanabban az előfizetésben használható, mint az Azure Automation-fiók.
- Az Azure-ban és az Azure Government-ben minden olyan régióban elérhető, amely támogatja a Log Analytics-munkaterületet, az Azure Automation-fiókot és a riasztásokat. Az Azure Government régiói jelenleg nem támogatják az e-mail funkciókat.

## <a name="solution-prerequisites"></a>Megoldás előfeltételei

A megoldás runbookjai egy [Azure Run As fiókkal működnek.](automation-create-runas-account.md) A Futtatás másként fiók az előnyben részesített hitelesítési módszer, mivel tanúsítványhitelesítést használ a jelszó helyett, amely lejárhat vagy gyakran változhat.

Azt javasoljuk, hogy egy külön Automation-fiókot a **kezdő/leállító virtuális gépek munkaidőn kívüli** megoldás során. Az Azure-modul verziói gyakran frissülnek, és a paraméterek változhatnak. A megoldás nem ugyanazon a lépésszámon frissül, és előfordulhat, hogy nem működik az általa használt parancsmagok újabb verzióival. Javasoljuk, hogy tesztelje a modulfrissítéseket egy tesztautomation-fiókban, mielőtt importálja őket a termelési automatizálási fiók(oka)ra.

## <a name="solution-permissions"></a>Megoldási engedélyek

Bizonyos engedélyekkel kell rendelkeznie a **virtuális gépek munkaidőn kívüli** megoldás során történő üzembe helyezéséhez. Az engedélyek eltérőek, ha a megoldás egy előre létrehozott Automation-fiókot és a Log Analytics-munkaterületet használ a szükséges engedélyektől, ha a megoldás új fiókot és munkaterületet hoz létre a telepítés során. 

Nem kell konfigurálnia az engedélyeket, ha az előfizetés közreműködője, és az Azure Active Directory-bérlő globális rendszergazdája. Ha nem rendelkezik ezekkel a jogokkal, vagy egyéni szerepkört kell konfigurálnia, győződjön meg arról, hogy rendelkezik az alább leírt engedélyekkel.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Engedélyek a már meglévő Automation-fiókhoz és a Log Analytics-munkaterülethez

A **virtuális gépek indítása/leállítása munkaidőn kívüli** megoldás ban egy meglévő Automation-fiókra és a Log Analytics-munkaterületre való üzembe helyezéséhez a következő engedélyekszükségesek az erőforráscsoport hatóköréhez. A szerepkörökről az [Egyéni szerepkörök az Azure-erőforrásokhoz](../role-based-access-control/custom-roles.md)című témakörben olvashat bővebben.

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

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Engedélyek az új Automation-fiókhoz és az új Log Analytics-munkaterülethez

A kezdő **és leállító virtuális gépek et munkaidőn kívüli** megoldás ban telepítheti egy új Automation-fiókba és a Log Analytics-munkaterületre. Ebben az esetben a megoldást üzembe helyező felhasználónak szüksége van az előző szakaszban meghatározott engedélyekre, valamint az ebben a szakaszban meghatározott engedélyekre. 

A megoldást üzembe helyező felhasználónak a következő szerepkörökre van szüksége:

- Társadminisztrátor az előfizetésen. Ez a szerepkör szükséges a Klasszikus futtatás mint fiók létrehozásához, ha klasszikus virtuális gépeket szeretne kezelni. [A klasszikus futtatási mint fiókok](automation-create-standalone-account.md#create-a-classic-run-as-account) alapértelmezés szerint már nem jönnek létre.
- Tagság az [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) alkalmazásfejlesztői szerepkörben. A Futtatás fiókokként beállításáról a [Futtatás másként fiókok konfigurálása című témakörben](manage-runas-account.md#permissions)talál további információt.
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

## <a name="solution-components"></a>Megoldás-összetevők

A **kezdő/leállított virtuális gépek munkaidőn kívüli megoldás** előre konfigurált runbookokat, ütemezéseket és az Azure Monitor-naplókkal való integrációt tartalmaz. Ezekkel az elemekkel testre szabhatja a virtuális gépek indítását és leállítását az üzleti igényeinek megfelelően.

### <a name="runbooks"></a>Runbookok

Az alábbi táblázat felsorolja azokat a runbookokat, amelyeket a megoldás telepít az Automation-fiókba. NE módosítsa a runbook-kódot. Ehelyett írja meg saját runbook ját az új funkciókhoz.

> [!IMPORTANT]
> Ne futtasson közvetlenül olyan runbookot, amelynek neve hozzáfűzve **gyermek.**

Minden szülő runbookok tartalmazzák a `WhatIf` paramétert. Ha értéke Igaz, a paraméter támogatja részletezi a pontos viselkedését a runbook veszi, ha fut a paraméter nélkül, és ellenőrzi, hogy a megfelelő virtuális gépek vannak célozva. A runbook csak akkor hajtja `WhatIf` végre a definiált műveleteket, ha a paraméter értéke Hamis.

|Forgatókönyv | Paraméterek | Leírás|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject objektum <br> AlertAction művelet <br> WebHookURI között | A szülő runbookból hívva. Ez a runbook erőforrásonkénti riasztásokat hoz létre az automatikus leállítási forgatókönyvhöz.|
|AutoStop_CreateAlert_Parent | Virtuális géplista<br> WhatIf: Igaz vagy hamis  | Létrehozza vagy frissíti az Azure riasztási szabályokat a virtuális gépek a célzott előfizetési vagy erőforráscsoportok. <br> `VMList`A virtuális gépek vesszővel tagolt listája. Például: `vm1, vm2, vm3`.<br> `WhatIf`lehetővé teszi a runbook-logika érvényesítését a végrehajtás nélkül.|
|AutoStop_Disable | None | Letiltja az automatikus leállítási riasztásokat és az alapértelmezett ütemezést.|
|AutoStop_VM_Child | WebHookData | A szülő runbookból hívva. Riasztási szabályok hívja meg ezt a runbookot egy klasszikus virtuális gép leállításához.|
|AutoStop_VM_Child_ARM | WebHookData |A szülő runbookból hívva. Riasztási szabályok hívja meg ezt a runbookot a virtuális gép leállításához.  |
|ScheduledStartStop_Base_Classic | CloudServiceName (Felhőszolgáltatásneve)<br> Művelet: Indítás vagy leállítás<br> Virtuális géplista  | A Cloud Services által a klasszikus virtuálisgép-csoportban végrehajtott művelet indítása vagy leállítása. |
|ScheduledStartStop_Child | VMName <br> Művelet: Indítás vagy leállítás <br> ResourceGroupName | A szülő runbookból hívva. Indítási vagy leállítási műveletet hajt végre az ütemezett leállításhoz.|
|ScheduledStartStop_Child_Classic | VMName<br> Művelet: Indítás vagy leállítás<br> ResourceGroupName | A szülő runbookból hívva. Indítási vagy leállítási műveletet hajt végre a klasszikus virtuális gépek ütemezett leállításához. |
|ScheduledStartStop_Parent | Művelet: Indítás vagy leállítás <br>Virtuális géplista <br> WhatIf: Igaz vagy hamis | Elindítja vagy leállítja az előfizetésben lévő összes virtuális gépet. A változók `External_Start_ResourceGroupNames` szerkesztése és `External_Stop_ResourceGroupNames` csak ezeken a célzott erőforráscsoportokon hajtható végre. A `External_ExcludeVMNames` változó frissítésével is kizárhat bizonyos virtuális gépeket.|
|SequencedStartStop_Parent | Művelet: Indítás vagy leállítás <br> WhatIf: Igaz vagy hamis<br>Virtuális géplista| Szekvenciák **indítása** és **sequencestop** nevű címkéket hoz létre minden olyan virtuális gépen, amelyhez a kezdési/leállítási tevékenységet szeretné szekvencia-szekvenciába állítani. Ezek a címkenevek nem idéken különböznek a kis- és nagybetűktől. A címke értékének pozitív egész számnak (1, 2, 3) kell lennie, amely megfelel annak a sorrendnek, amelyben el szeretné kezdeni vagy le állítani. <br>**Megjegyzés:** A virtuális gépeknek a `External_Start_ResourceGroupNames`ban `External_Stop_ResourceGroupNames`definiált erőforráscsoportokon és `External_ExcludeVMNames` változókon belül kell lenniük. Rendelkezniük kell a műveletek hez szükséges címkékkel.|

### <a name="variables"></a>Változók

Az alábbi táblázat az Automation-fiókban létrehozott változókat sorolja fel. Csak a előtaggal `External`előtaggal rendelkező változókat módosítsa. Az előtaggal előtaggal `Internal` ellátott változók módosítása nemkívánatos hatásokat okoz.

> [!NOTE]
> A virtuális gép nevére és erőforráscsoportjára vonatkozó korlátozások nagyrészt változó méretnek adják ki.

|Változó | Leírás|
|---------|------------|
|External_AutoStop_Condition | A feltételes operátor konfigurálása szükséges a feltétel, mielőtt riasztást. Az elfogadható `GreaterThan`értékek `GreaterThanOrEqual` `LessThan`a `LessThanOrEqual`, , , és .|
|External_AutoStop_Description | A riasztást, hogy állítsa le a virtuális gép, ha a CPU százalékos meghaladja a küszöbértéket.|
|External_AutoStop_Frequency | A szabály értékelési gyakorisága. Ez a paraméter időfesztávolságformátumban fogadja a bemenetet. A lehetséges értékek 5 perc és 6 óra között lehetnek. |
|External_AutoStop_MetricName | Annak a teljesítménymutatónak a neve, amelyhez az Azure Alert szabályt konfigurálni kell.|
|External_AutoStop_Severity | A metrikariasztás súlyossága, amely 0 és 4 között lehet. |
|External_AutoStop_Threshold | A változóban megadott Azure Alert szabály `External_AutoStop_MetricName`küszöbértéke. A százalékértékek 1 és 100 között mozognak.|
|External_AutoStop_TimeAggregationOperator | A feltétel kiértékeléséhez alkalmazott időösszesítési operátor a kiválasztott ablakméretre. Az elfogadható `Average`értékek `Minimum` `Maximum`a `Total`következők: , , , és `Last`.|
|External_AutoStop_TimeWindow | Az ablak mérete, amelynek során az Azure elemzi a kiválasztott metrikák riasztás indításához. Ez a paraméter időfesztávolságformátumban fogadja a bemenetet. A lehetséges értékek 5 perc és 6 óra között lehetnek.|
|External_EnableClassicVMs| Érték megadása, ha a klasszikus virtuális gépek a megoldás által célzott. Az alapértelmezett érték Igaz. Állítsa ezt a változót False for Azure Cloud Solution Provider (CSP) előfizetések. A klasszikus virtuális gépekhez [klasszikus futtatási mint fiók](automation-create-standalone-account.md#create-a-classic-run-as-account)szükséges.|
|External_ExcludeVMNames | A kizárandó virtuális gépnevek vesszővel tagolt listája, amely 140 virtuális gépre korlátozódik. Ha 140-nél több virtuális gépet ad hozzá a listához, előfordulhat, hogy a kizárt virtuális gépek véletlenül elindulnak vagy leállnak.|
|External_Start_ResourceGroupNames | A kezdő műveletekhez megcélzott egy vagy több erőforráscsoport vesszővel tagolt listája.|
|External_Stop_ResourceGroupNames | A stop műveletekhez megcélzott egy vagy több erőforráscsoport vesszővel tagolt listája.|
|External_WaitTimeForVMRetrySeconds |A várakozási idő másodpercben a műveleteket kell végrehajtani a virtuális gépek en a **SequencedStartStop_Parent** runbook. Ez a változó lehetővé teszi, hogy a runbook várjon az alárendelt műveletek egy megadott számú másodpercig, mielőtt folytatná a következő műveletet. A maximális várakozási idő 10800, vagy három óra. Az alapértelmezett érték 2100 másodperc.|
|Internal_AutomationAccountName | Megadja az Automation-fiók nevét.|
|Internal_AutoSnooze_ARM_WebhookURI | A webhook URI-t a virtuális gépek automatikus leállítási forgatókönyve hívta meg.|
|Internal_AutoSnooze_WebhookUri | A webhook URI a klasszikus virtuális gépek autostop forgatókönyvét hívta meg.|
|Internal_AzureSubscriptionId | Az Azure-előfizetés azonosítója.|
|Internal_ResourceGroupName | Az Automation-fiók erőforráscsoportjának neve.|

>[!NOTE]
>A változó `External_WaitTimeForVMRetryInSeconds`esetében az alapértelmezett érték 600-ról 2100-ra lett frissítve. 

Az összes forgatókönyvben a `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`változók `External_ExcludeVMNames` , és szükségesek a virtuális gépek célzásához, kivéve a **AutoStop_CreateAlert_Parent,** **SequencedStartStop_Parent**és **ScheduledStartStop_Parent** runbookok vesszővel tagolt virtuálisgép-listáit. Ez azt, hogy a virtuális gépeknek célerőforrás-csoportokhoz kell tartozniuk a kezdési és leállítási műveletek hez. A logika hasonlóan működik az Azure-szabályzat, az, hogy megcélozhatja az előfizetés vagy erőforráscsoport, és az újonnan létrehozott virtuális gépek által örökölt műveleteket. Ez a megközelítés elkerüli, hogy minden virtuális géphez külön ütemezést kell fenntartania, és kezelnie kell a skálázási indításokat és leállásokat.

### <a name="schedules"></a>Ütemezések

Az alábbi táblázat az Automation-fiókban létrehozott összes alapértelmezett ütemezést sorolja fel.Módosíthatja őket, vagy létrehozhat saját egyéni ütemezéseket.Alapértelmezés szerint az Scheduled_StartVM és **a** **Scheduled_StopVM** ütemezések kivételével minden ütemezés le van tiltva.

Ne engedélyezze az összes ütemezést, mert ez egymást átfedő ütemezési műveleteket hozhat létre. A legjobb, ha meghatározza, hogy mely optimalizálásokat szeretné végezni, és ennek megfelelően módosítani kell őket. További magyarázat a példaforgatókönyvek az áttekintő szakaszban.

|Ütemezés neve | Frequency | Leírás|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8 óránként | A **AutoStop_CreateAlert_Parent** runbook 8 óránként fut, ami viszont leállítja `External_Stop_ResourceGroupNames`a `External_ExcludeVMNames` virtuális gép-alapú `External_Start_ResourceGroupNames`értékeket a , és a változók. Másik lehetőségként megadhat egy vesszővel tagolt virtuális gépek `VMList` listáját a paraméter használatával.|
|Scheduled_StopVM | Felhasználó által definiált, naponta | Futtatja a **ScheduledStopStart_Parent** runbook `Stop` egy paramétert minden nap a megadott időpontban.Automatikusan leállítja az összes virtuális gépet, amely megfelel a változó eszközök által meghatározott szabályoknak.Engedélyezze a kapcsolódó **ütemezést Ütemezett-StartVM**.|
|Scheduled_StartVM | Felhasználó által definiált, naponta | A **ScheduledStopStart_Parent** runbook ot futtatja `Start` a megadott időpontban minden nap paraméterértékkel. Automatikusan elindítja az összes virtuális gépet, amely megfelel a változó eszközök által meghatározott szabályoknak.Engedélyezze a kapcsolódó **ütemezést Scheduled-StopVM**.|
|Szekvenált-StopVM | Minden pénteken 1:00 (UTC), | Futtatja a Sequenced_Parent runbook `Stop` egy paraméter értéke minden pénteken a megadott időpontban.Egymás után (növekvő) leállítja az összes virtuális gépet a megfelelő változók által meghatározott **SequenceStop** címkével. A címkeértékekről és az eszközváltozókról a Runbooks szakaszban talál további információt.Engedélyezze a kapcsolódó ütemezést, **a Szekvenciális-StartVM.**|
|Szekvenált-StartVM | 13:00 (UTC), minden hétfőn | A **SequencedStopStart_Parent** runbookot futtatja, amelynek `Start` paraméterértéke minden hétfőn a megadott időpontban van. Egymás után (csökkenő) elindítja az összes virtuális gépet a megfelelő változók által meghatározott **SequenceStart** címkével. A címkeértékekről és a változó eszközökről a [Runbookok című témakörben](#runbooks)talál további információt. Engedélyezze a kapcsolódó **ütemezést, a Szekvenciális stopvm-et.**

## <a name="use-of-the-solution-with-classic-vms"></a>A megoldás használata klasszikus virtuális gépekkel

Ha a **kezdő/leállító virtuális gépeket** használja a klasszikus virtuális gépek munkaidőn kívüli megoldása során, majd az Automation az összes virtuális gépet felhőalapú szolgáltatásonként egymás után dolgozza fel. A virtuális gépek feldolgozása továbbra is párhuzamosan történik a különböző felhőszolgáltatások között. 

A megoldás klasszikus virtuális gépek használatával, klasszikus futtatási fiók, amely alapértelmezés szerint nem jön létre. A klasszikus futtatási mint fiók létrehozásáról a [Klasszikus futtatás létrehozása mint fiók](automation-create-standalone-account.md#create-a-classic-run-as-account)című témakörben talál.

Ha felhőszolgáltatásonként 20-nál több virtuális géppel rendelkezik, az alábbiakat tetszhet:

* Hozzon létre több ütemezést a szülő runbook **ScheduledStartStop_Parent,** és ütemezésenként 20 virtuális gépet adjon meg. 
* Az ütemezési tulajdonságok, `VMList` a paraméter segítségével adja meg a virtuális gép nevét, mint egy vesszővel tagolt lista. 

Ellenkező esetben, ha az automation-feladat ehhez a megoldáshoz több mint három órát fut, ideiglenesen kirakodták vagy leállították a [méltányos megosztási](automation-runbook-execution.md#fair-share) korlát on.

Az Azure CSP-előfizetések csak az Azure Resource Manager modellt támogatják. Nem Azure Resource Manager-szolgáltatások nem érhetők el a programban. Amikor a **kezdő és leállító virtuális gépek munkaidőn kívüli megoldás futtatásakor,** előfordulhat, hogy hibaüzeneteket kap, mivel a klasszikus erőforrások kezelésére parancsmagokkal rendelkezik. A kriptaverzióról további információ: [Available services in CSP subscriptions](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Ha csp-előfizetést használ, a [External_EnableClassicVMs](#variables) változót a telepítés után hamis értékre kell állítania.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>A megoldás engedélyezése

A megoldás használatának megkezdéséhez kövesse a [Virtuálisgépek indításának/leállításának lépéseit.](automation-solution-vm-management-enable.md)

## <a name="view-the-solution"></a>A megoldás megtekintése

A megoldás engedélyezését követően az alábbi mechanizmusok egyikével érheti el a megoldást:

* Az Automation-fiókban válassza a **Virtuális gép indítása/leállítása** lehetőséget a **Kapcsolódó erőforrások csoportban.** A Virtuálisgép indítása/leállítása lapon válassza **a Megoldás kezelése a** lap jobb oldalán, a **Virtuálisgép-megoldások kezelése csoportban.**

* Nyissa meg az Automation-fiókhoz kapcsolódó Log Analytics-munkaterületet. Miután kiválasztotta a munkaterületet, válassza a **megoldások** lehetőséget a bal oldali ablaktáblából. A Megoldások lapon válassza ki a **Start-Stop-VM[munkaterület]** megoldást a listából.  

A megoldás kiválasztása a **Start-Stop-VM[workspace]** megoldáslapot jeleníti meg. Itt tekintheti át a fontos részleteket, például a **StartStopVM** csempén található információkat. A Log Analytics-munkaterülethez ugyanúgy, mint a Log Analytics-munkaterület, ez a csempe a runbook-feladatok számát és grafikus ábrázolását jeleníti meg a sikeresen elkezdett és sikeresen befejeződött megoldáshoz.

![Automation Update Management megoldás lap](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

A fánkcsempére kattintva további elemzéseket végezhet a feladatrekordokról. A megoldás irányítópultja a feladatelőzményeket és az előre definiált naplókeresési lekérdezéseket jeleníti meg. Váltson át a naplóelemzés speciális portáljára a keresési lekérdezések alapján való kereséshez.

## <a name="update-the-solution"></a>A megoldás frissítése

Ha a megoldás egy korábbi verzióját telepítette, törölje azt a fiókjából, mielőtt egy frissített kiadást telepítene. Kövesse a [lépéseket a megoldás eltávolításához,](#remove-the-solution) majd kövesse [a megoldás üzembe helyezéséhez](automation-solution-vm-management-enable.md)szükséges lépéseket.

## <a name="remove-the-solution"></a>A megoldás eltávolítása

Ha már nincs szüksége a megoldás használatára, törölheti azt az Automation-fiókból. A megoldás törlése csak eltávolítja a runbookok. Nem törli a megoldás hozzáadásakor létrehozott ütemezéseket vagy változókat. Távolítsa el ezeket az eszközöket manuálisan, ha nem használja őket más runbookokkal.

A megoldás törlése:

1. Az Automation-fiókban válassza a **Csatolt munkaterület** lehetőséget a Kapcsolódó **erőforrások területen.**

2. Válassza **az Ugrás munkaterületre**lehetőséget.

3. Kattintson **a Megoldások** **csoportBan az Általános gombra.** 

4. A Megoldások lapon válassza ki a **Start-Stop-VM[Workspace]** megoldást. 

5. A **VMManagementSolution[Workspace]** lapon válassza a menü **Törlés parancsát.**<br><br> ![Virtuálisgép Mgmt-megoldás törlése](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. A **Megoldás törlése** ablakban ellenőrizze, hogy törölni szeretné-e a megoldást.

7. Az adatok ellenőrzése és a megoldás törlése közben nyomon követheti az előrehaladást a **menüből**kiválasztott Értesítések területen. A megoldás eltávolításának folyamata után visszatér a Megoldások lapra.

Az Automation-fiók és a Log Analytics munkaterület nem törlődik a folyamat részeként. Ha nem szeretné megtartani a Log Analytics-munkaterületet, manuálisan kell törölnie az Azure Portalról:

1. Keresse meg és válassza **a Log Analytics-munkaterületeket.**

2. A **Log Analytics-munkaterületek** lapon jelölje ki a munkaterületet.

3. Válassza a Munkaterület beállításai lap **Törlés** parancsát.

4. Ha nem szeretné megtartani az Azure Automation-fiók összetevőit, manuálisan törölheti őket. Lásd: [Megoldás-összetevők](#solution-components).

## <a name="next-steps"></a>További lépések

[Engedélyezze](automation-solution-vm-management-enable.md) a **virtuális gépek indítása/leállítása munkaidőn kívüli** megoldás az Azure virtuális gépek.
