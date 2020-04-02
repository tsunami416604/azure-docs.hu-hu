---
title: Virtuális gépek indítása/leállítása munkaidőn kívüli megoldás közben
description: Ez a virtuálisgép-felügyeleti megoldás elindítja és leállítja az Azure virtuális gépek ütemezés szerint, és proaktív módon figyeli az Azure Monitor naplók.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: cef3176c99cd57ae229b602feb3c825081fcfe3e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548377"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Megoldás a virtuális gépek munkaidőn kívüli indítására és leállítására az Azure Automation szolgáltatásban

A virtuális gépek indítása/leállítása munkaidőn kívüli megoldás ban indítsa el és állítsa le az Azure virtuális gépek et a felhasználó által meghatározott ütemezések, betekintést nyújt az Azure Monitor naplókon keresztül, és elküldi a választható e-maileket [a műveletcsoportok](../azure-monitor/platform/action-groups.md)használatával. Támogatja az Azure Resource Manager és a klasszikus virtuális gépek a legtöbb forgatókönyv. 

A megoldás klasszikus virtuális gépekkel való használatához klasszikus RunAs-fiókra van szükség, amely alapértelmezés szerint nem jön létre. A klasszikus RunAs-fiókok létrehozásáról a Klasszikus futtatási fiókok című témakörben talál [útmutatást.](automation-create-standalone-account.md#classic-run-as-accounts)

> [!NOTE]
> A kezdő/leállító virtuális gépek munkaidőn kívüli megoldás frissítésre került, hogy támogassa az Azure-modulok elérhető legújabb verzióit.

Ez a megoldás egy decentralizált, alacsony költségű automatizálási lehetőséget biztosít azoknak a felhasználóknak, akik optimalizálni szeretnék a virtuális gép költségeit. Ezzel a megoldással a következőket teheti:

- [A virtuális gépek indításának és leállításának ütemezése](automation-solution-vm-management-config.md#schedule).
- Ütemezze a virtuális gépeket, hogy növekvő sorrendben induljanak el és állítsanak le [az Azure-címkék használatával](automation-solution-vm-management-config.md#tags) (nem támogatott a klasszikus virtuális gépek).
- Automatikus számláló virtuális gépek alacsony [CPU-használat](automation-solution-vm-management-config.md#cpuutil)alapján .

A jelenlegi megoldással kapcsolatban a következők ben korlátozások vannak:

- Ez a megoldás kezeli a virtuális gépek bármely régióban, de csak akkor használható ugyanabban az előfizetésben, mint az Azure Automation-fiók.
- Ez a megoldás az Azure-ban és az Azure Government-ben érhető el minden olyan régióban, amely támogatja a Log Analytics-munkaterületet, az Azure Automation-fiókot és a riasztásokat. Az Azure Government régiói jelenleg nem támogatják az e-mail funkciókat.

> [!NOTE]
> Ha a megoldást a klasszikus virtuális gépek, majd az összes virtuális gép lesz feldolgozva egymás után felhőalapú szolgáltatás. A virtuális gépek feldolgozása továbbra is párhuzamosan történik a különböző felhőszolgáltatások között. Ha felhőszolgáltatásonként 20-nál több virtuális géppel rendelkezik, azt javasoljuk, hogy hozzon létre több ütemezést a szülő runbook **ScheduledStartStop_Parent,** és ütemezésenként 20 virtuális gépet adjon meg. Az ütemezési tulajdonságokban adja meg vesszővel tagolt listaként a **VMList** paraméter ben szereplő virtuálisgép-neveket. Ellenkező esetben, ha az automation-feladat ehhez a megoldáshoz fut több mint három órán át, hogy ideiglenesen kiürült, vagy leállította a [méltányos megosztási](automation-runbook-execution.md#fair-share) korláton ként.
>
> Az Azure Cloud Solution Provider (Azure CSP) előfizetések csak az Azure Resource Manager modellt támogatják, a nem Azure Resource Manager-szolgáltatások nem érhetők el a programban. A Start/Stop megoldás futtatásakor hibákat kaphat, mivel parancsmagokkal rendelkezik a klasszikus erőforrások kezeléséhez. A kriptaverzióról további információ: [Available services in CSP subscriptions](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Ha csp-előfizetést használ, a központi telepítés után módosítsa a [**External_EnableClassicVMs**](#variables) **változót Hamis** értékre.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

A megoldás runbookjai egy [Azure Run As fiókkal működnek.](automation-create-runas-account.md) A Futtatás másként fiók az előnyben részesített hitelesítési módszer, mivel tanúsítványhitelesítést használ a jelszó helyett, amely lejárhat vagy gyakran változhat.

Azt javasoljuk, hogy egy külön automation-fiók a virtuális gép indítási és leállítási megoldás. Ennek az az oka, hogy az Azure-modul verzióit gyakran frissítik, és paramétereik változhatnak. A Start/Stop virtuálisgép-megoldás nem frissül ugyanazon a lépésszámon, így előfordulhat, hogy nem működik az általa használt parancsmagok újabb verzióival. Azt is javasoljuk, hogy tesztelje a modul frissítéseit egy tesztautomatizálási fiókban, mielőtt importálná őket a termelési automatizálási fiók(oka)ra.

### <a name="permissions"></a>Engedélyek

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

## <a name="solution-components"></a>Megoldás-összetevők

Ez a megoldás előre konfigurált runbookokat, ütemezéseket és integrációt tartalmaz az Azure Monitor naplóival, így a virtuális gépek indítását és leállítását az üzleti igényekhez igazíthatja.

### <a name="runbooks"></a>Runbookok

Az alábbi táblázat felsorolja a megoldás által az Automation-fiókba telepített runbookokat. Ne módosítsa a runbook-kódot. Ehelyett írja meg saját runbook ját az új funkciókhoz.

> [!IMPORTANT]
> Ne futtasson közvetlenül olyan runbookot, amelynek *neve* gyermekhozzá van fűzve.

Minden szülő runbookok tartalmazzák a _WhatIf_ paraméter. Ha **értéke Igaz**, _WhatIf_ támogatja részletezi a pontos viselkedését a runbook veszi, ha fut a _WhatIf_ paraméter nélkül, és ellenőrzi a megfelelő virtuális gépek vannak célozva. A runbook csak akkor hajtja végre a definiált műveleteket, ha a _WhatIf_ paraméter **értéke Hamis**.

|Forgatókönyv | Paraméterek | Leírás|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject objektum <br> AlertAction művelet <br> WebHookURI között | A szülő runbookból hívva. Ez a runbook erőforrásonkénti riasztásokat hoz létre az automatikus leállítási forgatókönyvhöz.|
|AutoStop_CreateAlert_Parent | Virtuális géplista<br> WhatIf: Igaz vagy hamis  | Létrehozza vagy frissíti az Azure riasztási szabályokat a virtuális gépek a célzott előfizetési vagy erőforráscsoportok. <br> VMList: A virtuális gépek vesszővel tagolt listája. Például _vm1, vm2, vm3_.<br> *A WhatIf* a runbook logikáját a végrehajtás nélkül érvényesíti.|
|AutoStop_Disable | Nincs | Letiltja az automatikus leállítási riasztásokat és az alapértelmezett ütemezést.|
|AutoStop_VM_Child | WebHookData | A szülő runbookból hívva. Riasztási szabályok hívja meg ezt a runbookot a klasszikus virtuális gép leállításához.|
|AutoStop_VM_Child_ARM | WebHookData |A szülő runbookból hívva. Riasztási szabályok hívja meg ezt a runbookot a virtuális gép leállításához.  |
|ScheduledStartStop_Base_Classic | CloudServiceName (Felhőszolgáltatásneve)<br> Művelet: Indítás vagy leállítás<br> Virtuális géplista  | Ez a runbook a Cloud Services klasszikus virtuálisgép-csoportjában történő indítási vagy leállítási művelet végrehajtásához használható.<br> VMList: A virtuális gépek vesszővel tagolt listája. Például _vm1, vm2, vm3_. |
|ScheduledStartStop_Child | VMName <br> Művelet: Indítás vagy leállítás <br> ResourceGroupName | A szülő runbookból hívva. Indítási vagy leállítási műveletet hajt végre az ütemezett leállításhoz.|
|ScheduledStartStop_Child_Classic | VMName<br> Művelet: Indítás vagy leállítás<br> ResourceGroupName | A szülő runbookból hívva. Indítási vagy leállítási műveletet hajt végre a klasszikus virtuális gépek ütemezett leállításához. |
|ScheduledStartStop_Parent | Művelet: Indítás vagy leállítás <br>Virtuális géplista <br> WhatIf: Igaz vagy hamis | Ez a beállítás az előfizetésben lévő összes virtuális gépet érinti. A **External_Start_ResourceGroupNames** és **a External_Stop_ResourceGroupNames** csak ezeken a célzott erőforráscsoportokon hajtsa végre. Adott virtuális gépeket is kizárhat a **External_ExcludeVMNames** változó frissítésével.<br> VMList: A virtuális gépek vesszővel tagolt listája. Például _vm1, vm2, vm3_.<br> _A WhatIf_ a runbook logikáját a végrehajtás nélkül érvényesíti.|
|SequencedStartStop_Parent | Művelet: Indítás vagy leállítás <br> WhatIf: Igaz vagy hamis<br>Virtuális géplista| Hozzon létre **nevű címkéksequencestart** és **sequencestop** minden virtuális gép, amelynek sorrendbe szeretné venni a start/stop tevékenység. Ezek a címkenevek nem idéken különböznek a kis- és nagybetűktől. A címke értékének pozitív egész számnak (1, 2, 3) kell lennie, amely megfelel annak a sorrendnek, amelyben el szeretné kezdeni vagy le állítani. <br> VMList: A virtuális gépek vesszővel tagolt listája. Például _vm1, vm2, vm3_. <br> _A WhatIf_ a runbook logikáját a végrehajtás nélkül érvényesíti. <br> **Megjegyzés:** A virtuális gépeknek External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames és External_ExcludeVMNames az Azure Automation-változókban External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames és External_ExcludeVMNames definiált erőforráscsoportokon belül kell lenniük. Rendelkezniük kell a műveletek hez szükséges címkékkel.|

### <a name="variables"></a>Változók

Az alábbi táblázat az Automation-fiókban létrehozott változókat sorolja fel. Csak a **Külső**programmal előtaggal előtaggal rendelkező változókat módosítsa. A **belső** vel előtaggal előtaggal rendelkező változók módosítása nemkívánatos hatásokat okoz.

|Változó | Leírás|
|---------|------------|
|External_AutoStop_Condition | A feltételes operátor konfigurálása szükséges a feltétel, mielőtt riasztást. Az elfogadható értékek a következők: **GreaterThan**, **GreaterThanOrEqual**, **LessThan**és **LessThanOrEqual**.|
|External_AutoStop_Description | A riasztást, hogy állítsa le a virtuális gép, ha a CPU százalékos meghaladja a küszöbértéket.|
|External_AutoStop_Frequency | A szabály értékelési gyakorisága. Ez a paraméter időfesztávolságformátumban fogadja a bemenetet. A lehetséges értékek 5 és 6 óra között lehetnek. |
|External_AutoStop_MetricName | Annak a teljesítménymutatónak a neve, amelyhez az Azure Alert szabályt konfigurálni kell.|
|External_AutoStop_Severity | A metrikariasztás súlyossága, amely 0 és 4 között lehet. |
|External_AutoStop_Threshold | Az Azure Alert szabály External_AutoStop_MetricName megadott _küszöbértéke._ A százalékos értékek 1 és 100 között lehetnek.|
|External_AutoStop_TimeAggregationOperator | Az időösszesítésoperátor, amely a kiválasztott ablakméretre lesz alkalmazva a feltétel kiértékeléséhez. Az elfogadható értékek az **átlag**, **a minimum,** **a maximum,** **az összes**és **az utolsó**.|
|External_AutoStop_TimeWindow | Az ablak mérete, amelynek során az Azure elemzi a kiválasztott metrikák riasztás indításához. Ez a paraméter időfesztávolságformátumban fogadja a bemenetet. A lehetséges értékek 5 perc és 6 óra között lehetnek.|
|External_EnableClassicVMs| Itt adható meg, hogy a klasszikus virtuális gépeket a megoldás célozza-e meg. Az alapértelmezett érték Igaz. Ezt hamis értékre kell állítani a CSP-előfizetésekhez. A klasszikus virtuális gépekhez [klasszikus futtatási fiók](automation-create-standalone-account.md#classic-run-as-accounts)szükséges.|
|External_ExcludeVMNames | Írja be a kizárandó virtuális gépneveket, és válassza el a neveket szóközök nélküli vesszővel. Ez legfeljebb 140 virtuális gépre korlátozódik. Ha 140-nél több virtuális gépet ad hozzá ehhez a vesszővel tagolt listához, előfordulhat, hogy a kizárt virtuális gépek véletlenül elindulnak vagy leállnak.|
|External_Start_ResourceGroupNames | Egy vagy több erőforráscsoportot ad meg, amelyek vesszővel választják el az értékeket, és amelyeket a műveletek indítására céloznak.|
|External_Stop_ResourceGroupNames | Egy vagy több erőforráscsoportot ad meg, amelyek vesszővel választják el az értékeket, és amelyek et a stop műveletekre használnak.|
|External_WaitTimeForVMRetrySeconds |A várakozási idő másodpercben a műveleteket kell végrehajtani a virtuális gépeken a szekvenciális start/stop runbook.<br> Az alapértelmezett érték 2100 másodperc, és támogatja az 10800 vagy három órás maximális értékre történő konfigurálást.|
|Internal_AutomationAccountName | Megadja az Automation-fiók nevét.|
|Internal_AutoSnooze_ARM_WebhookURI | Megadja a klasszikus virtuális gépek automatikus leállítási forgatókönyvéhez meghívott Webhook URI-t.|
|Internal_AutoSnooze_WebhookUri | Megadja az automatikus leállítási forgatókönyvhöz meghívott Webhook URI-t.|
|Internal_AzureSubscriptionId | Megadja az Azure-előfizetés-azonosítót.|
|Internal_ResourceGroupName | Megadja az Automation-fiók erőforráscsoportjának nevét.|

>[!NOTE]
>Az **External_WaitTimeForVMRetryInSeconds**változó esetében az alapértelmezett érték 600-ról 2100-ra lett frissítve. Ez a változó lehetővé teszi, hogy a **szekvenciális indítási/leállítási forgatókönyv** runbook várni a gyermek műveletek megadott számú másodpercig, mielőtt folytatná a következő műveletet.
>

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

## <a name="enable-the-solution"></a>A megoldás engedélyezése

A megoldás használatának megkezdéséhez hajtsa végre a [virtuális gépek indítása/leállítása megoldás](automation-solution-vm-management-enable.md)lépéseit.

## <a name="viewing-the-solution"></a>A megoldás megtekintése

A megoldás az alábbi módok on-át érhető el:

* Az Automation-fiókban válassza a **Virtuális gép indítása/leállítása** lehetőséget a **Kapcsolódó erőforrások csoportban.** A **Virtuálisgép indítása/leállítása** lapon válassza **a Megoldás kezelése a** lap jobb oldalán, a **Virtuálisgép-megoldások kezelése**című szakaszban.

* Nyissa meg az Automation-fiókhoz kapcsolódó Log Analytics-munkaterületet, és miután kiválasztotta a munkaterületet, válassza a **Megoldások** lehetőséget a bal oldali ablaktáblából. A **Megoldások** lapon válassza ki a **Start-Stop-VM[munkaterület]** megoldást a listából.  

A megoldás kiválasztása a **Start-Stop-VM[workspace]** megoldáslapot jeleníti meg. Itt áttekintheti a fontos részleteket, például a **StartStopVM** csempét. A Log Analytics-munkaterülethez ugyanúgy, mint a Log Analytics-munkaterület, ez a csempe a runbook-feladatok számát és grafikus ábrázolását jeleníti meg a sikeresen elkezdett és sikeresen befejeződött megoldáshoz.

![Automation Update Management megoldás lap](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Innen a fánkcsempére kattintva további elemzéseket végezhet a feladatrekordokról. A megoldás irányítópultja a feladatelőzményeket és az előre definiált naplókeresési lekérdezéseket jeleníti meg. Váltson át a naplóelemzés speciális portáljára a keresési lekérdezések alapján való kereséshez.

## <a name="update-the-solution"></a>A megoldás frissítése

Ha a megoldás egy korábbi verzióját telepítette, először törölnie kell azt a fiókjából, mielőtt egy frissített kiadást telepítene. Kövesse a [lépéseket a megoldás eltávolításához,](#remove-the-solution) majd kövesse [a megoldás üzembe helyezéséhez](automation-solution-vm-management-enable.md)szükséges lépéseket.

## <a name="remove-the-solution"></a>A megoldás eltávolítása

Ha úgy dönt, hogy már nem kell használnia a megoldást, törölheti azt az Automation-fiókból. A megoldás törlése csak eltávolítja a runbookok. Nem törli a megoldás hozzáadásakor létrehozott ütemezéseket vagy változókat. Ezeket az eszközöket manuálisan kell törölnie, ha nem használja őket más runbookokkal.

A megoldás törléséhez hajtsa végre az alábbi lépéseket:

1. Az Automation-fiók **Kapcsolódó erőforrások csoportban**válassza a **Csatolt munkaterület**lehetőséget.

2. Válassza **az Ugrás munkaterületre**lehetőséget.

3. Az **Általános csoportban**válassza a **Megoldások**lehetőséget. 

4. A **Megoldások** lapon válassza ki a **Start-Stop-VM[Workspace]** megoldást. A **VMManagementSolution[Workspace]** lap Törlés parancsa menüpontból válassza a **Törlés parancsot.**<br><br> ![Virtuálisgép Mgmt-megoldás törlése](media/automation-solution-vm-management/vm-management-solution-delete.png)

5. A **Megoldás törlése** ablakban ellenőrizze, hogy törölni szeretné-e a megoldást.

6. Amíg az adatok at ellenőriztük és a megoldást törölték, a menü **Értesítések** menüjében nyomon követheti annak előrehaladását. A megoldás eltávolításának folyamata után visszatér a **Megoldások** lapra.

Az Automation-fiók és a Log Analytics munkaterület nem törlődik a folyamat részeként. Ha nem szeretné megőrizni a Log Analytics munkaterületet, manuálisan kell törölnie. Ez az Azure Portalon valósítható meg:

1. Az Azure Portalon keresse meg és válassza **a Log Analytics-munkaterületeket.**

2. A **Log Analytics-munkaterületek** lapon jelölje ki a munkaterületet.

3. Válassza a Munkaterület beállításai lap **Törlés** parancsát.

Ha nem szeretné megtartani az Azure Automation-fiók összetevőit, manuálisan törölheti őket. A megoldás által létrehozott runbookok, változók és ütemezések listáját a [Solution összetevői](#solution-components)című témakörben találja.

## <a name="next-steps"></a>További lépések

[Engedélyezze](automation-solution-vm-management-enable.md) a Start/Stop munkaidőn kívüli megoldás az Azure virtuális gépek.
