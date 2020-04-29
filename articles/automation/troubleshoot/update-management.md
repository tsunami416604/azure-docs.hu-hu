---
title: Hibaelhárítási Azure Automation Update Management
description: Megtudhatja, hogyan lehet elhárítani és elhárítani a Update Management megoldással kapcsolatos problémákat Azure Automationban.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 91ecff311b8820d3b97e1de0e4b4e87c150e749b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678924"
---
# <a name="troubleshoot-issues-with-the-update-management-solution"></a>A Update Management megoldással kapcsolatos problémák elhárítása

Ez a cikk a Update Management-megoldás használatakor esetlegesen futtatott problémákat ismerteti. A hibrid Runbook-feldolgozó ügynöknek van egy ügynök-hibakeresője a mögöttes probléma meghatározásához. A hibaelhárítással kapcsolatos további tudnivalókért tekintse meg a [Windows Update Agent problémáinak elhárítása](update-agent-issues.md) és a [Linux frissítési ügynökkel kapcsolatos problémák elhárítása](update-agent-issues-linux.md)című témakört. Más bevezetési problémák esetén lásd: a megoldás előkészítésének [hibaelhárítása](onboarding.md).

>[!NOTE]
>Ha a megoldás virtuális gépen (VM) való bevezetésével kapcsolatos problémákat tapasztal, tekintse meg a helyi számítógép **alkalmazás-és szolgáltatások naplójának** **Operations Manager** naplóját. Keresse meg a 4502-as AZONOSÍTÓJÚ eseményt és a benne `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`található esemény részleteit.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](../automation-update-azure-modules.md).

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>Forgatókönyv: a "nem sikerült engedélyezni a frissítési megoldást" hibaüzenet jelenik meg

### <a name="issue"></a>Probléma

Ha az Automation-fiókban megpróbálja engedélyezni a Update Management megoldást, a következő hibaüzenet jelenik meg:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

* Lehetséges, hogy nem megfelelően van konfigurálva a hálózati tűzfalra vonatkozó követelmények a Log Analytics ügynökhöz. Ez a helyzet arra vezethet, hogy az ügynök meghibásodik a DNS URL-címeinek feloldásakor.

* A megoldások célzása helytelenül van konfigurálva, és a gép nem fogadja a várt frissítéseket.

* Azt is megfigyelheti, hogy a gép a `Non-compliant` **megfelelőség**alatt látható állapotot jeleníti meg. Ugyanakkor az **Agent Desktop Analytics** az ügynököt a következőként jelenti `Disconnected`:.

### <a name="resolution"></a>Megoldás:

* Futtassa a [Windows](update-agent-issues.md#troubleshoot-offline) vagy [Linux](update-agent-issues-linux.md#troubleshoot-offline)rendszerhez készült hibakeresőt az operációs rendszertől függően.

* Nyissa meg a [hálózat megtervezése](../automation-hybrid-runbook-worker.md#network-planning) című témakört, amelyből megtudhatja, mely címeket és portokat kell engedélyezni a Update Management működéséhez.  

* Nyissa meg a [hálózat megtervezése](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) című témakört, amelyből megtudhatja, hogy mely címeket és portokat kell engedélyezni a log Analytics-ügynök működéséhez.

* A hatókör-konfigurációs problémák keresése. A [hatókör-konfiguráció](../automation-onboard-solutions-from-automation-account.md#scope-configuration) meghatározza, hogy mely gépek legyenek konfigurálva a megoldáshoz. Ha a gép megjelenik a munkaterületen, de nem a * * Update Management portálon, be kell állítania a hatókör-konfigurációt a gépek célzásához. A hatókör-konfigurációval kapcsolatos további tudnivalókért tekintse meg [a munkaterületen lévő számítógépeket](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)ismertető témakört.

* A [hibrid runbook-feldolgozó törlésének](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)lépéseit követve távolítsa el a munkavégző konfigurációt. 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Forgatókönyv: hiányzóként jelzett, felülírt frissítés Update Management

### <a name="issue"></a>Probléma

A régi frissítések egy olyan Automation-fióknál jelennek meg, amely hiányzik annak ellenére, hogy felváltották őket. A felülírt frissítés az egyik, hogy nem kell telepítenie, mert egy későbbi frissítés is elérhető, amely kijavította ugyanazt a biztonsági rést. Update Management figyelmen kívül hagyja a felülírt frissítést, és nem alkalmazható a felülírt frissítés mellett. További információ a kapcsolódó problémákról: a [frissítés felülírva](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Ok

A felülírt frissítések nem megfelelően vannak jelezve, hogy ne legyenek alkalmazhatók.

### <a name="resolution"></a>Megoldás:

Ha a felváltott frissítés 100%-os lesz, a frissítés jóváhagyási állapotát a következőre kell módosítani `Declined`:. Az összes frissítés jóváhagyási állapotának módosítása:

1. Az Automation-fiókban válassza a **Update Management** lehetőséget a gép állapotának megtekintéséhez. Lásd: [frissítési felmérések megtekintése](../manage-update-multi.md#view-an-update-assessment).

2. Ellenőrizze a felülírt frissítést, és győződjön meg arról, hogy az 100%-os nem alkalmazható. 

3. A frissítés elutasításának megjelölése, ha kérdése van a frissítéssel kapcsolatban. 

4. Válassza a **számítógépek** lehetőséget, és a **megfelelőség** oszlopban ellenőrizze, hogy a megfelelőségi ellenőrzés ki van-e kényszerítve. Lásd: [több gép frissítéseinek kezelése](../manage-update-multi.md).

5. Ismételje meg a fenti lépéseket a többi felülírt frissítésnél.

6. Futtassa a Lemezkarbantartó varázslót, hogy törölje a fájlokat a visszautasított frissítésekről. 

7. Windows Server Update Services (WSUS) esetében manuálisan törölje az összes felülírt frissítést az infrastruktúra frissítéséhez.

8. Ismételje meg az eljárást rendszeresen a megjelenítési probléma kijavításához, és csökkentse az Update Management által használt lemezterület méretét.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Forgatókönyv: a gépek nem jelennek meg a portálon Update Management

### <a name="issue"></a>Probléma

A gépek a következő tünetekkel rendelkeznek:

* A gép a `Not configured` virtuális gép Update Management nézetét jeleníti meg.

* A gépek hiányoznak a Azure Automation fiókjának Update Management nézetéről.

* Vannak olyan gépek, amelyek a `Not assessed` **megfelelőség**alatt jelennek meg. Azonban a Szívveréses adatAzure Monitor naplókban láthatja a hibrid Runbook-feldolgozót, de nem Update Management.

### <a name="cause"></a>Ok

Ezt a problémát a helyi konfigurációs problémák vagy a nem megfelelően konfigurált hatókör-konfiguráció okozhatja. A lehetséges konkrét okok a következők:

* Előfordulhat, hogy újra kell regisztrálnia és újra kell telepítenie a hibrid Runbook-feldolgozót.

* Lehet, hogy meghatározott egy kvótát a munkaterületen, amely el lett érve, és ez megakadályozza a további adattárolás megadását.

### <a name="resolution"></a>Megoldás:

1. Futtassa a [Windows](update-agent-issues.md#troubleshoot-offline) vagy [Linux](update-agent-issues-linux.md#troubleshoot-offline)rendszerhez készült hibakeresőt az operációs rendszertől függően.

2. Győződjön meg arról, hogy a számítógép a megfelelő munkaterületre küld jelentést. Az adott aspektus ellenőrzésével kapcsolatos útmutatásért lásd: az [ügynök kapcsolatának ellenőrzése log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Győződjön meg arról is, hogy ez a munkaterület a Azure Automation-fiókjához van csatolva. A megerősítéshez nyissa meg az Automation-fiókját, és a **kapcsolódó erőforrások**területen válassza a **csatolt munkaterület** lehetőséget.

3. Győződjön meg arról, hogy a gépek megjelennek az Automation-fiókhoz társított Log Analytics munkaterületen. Futtassa a következő lekérdezést a Log Analytics munkaterületen.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. Ha nem látja a gépet a lekérdezés eredményei között, a közelmúltban nem volt bejelölve. Valószínűleg van egy helyi konfigurációs probléma, és újra kell [telepítenie az ügynököt](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). 

5. Ha a gép megjelenik a lekérdezés eredményei között, ellenőrizze a hatókör-konfigurációs problémákat. A [hatókör-konfiguráció](../automation-onboard-solutions-from-automation-account.md#scope-configuration) határozza meg, hogy mely gépek vannak konfigurálva a megoldáshoz. 

6. Ha a gép megjelenik a munkaterületen, de nem Update Managementban, akkor a hatókör-konfigurációt úgy kell konfigurálnia, hogy az a gépet célozza meg. Ennek megismeréséhez tekintse meg [a munkaterületen található gépek](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)előkészítését ismertető témakört.

7. A munkaterületen futtassa ezt a lekérdezést.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. Ha ezt az `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` eredményt kapja, elérte a munkaterületen definiált kvótát, amely leállította az adatok mentését. A munkaterületen válassza az **adatmennyiség-kezelés** a **használat és a becsült költségek**lehetőséget, majd módosítsa vagy távolítsa el a kvótát.

9. Ha a probléma továbbra is megoldatlan, kövesse a [Windows Hybrid Runbook Worker telepítése](../automation-windows-hrw-install.md) a hibrid feldolgozó Windows rendszerre való újratelepítésének lépéseit. Linux esetén kövesse a [Linux Hybrid Runbook Worker üzembe helyezése](../automation-linux-hrw-install.md)című témakör lépéseit.

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Forgatókönyv: nem sikerült regisztrálni az Automation erőforrás-szolgáltatót az előfizetésekhez

### <a name="issue"></a>Probléma

Ha az Automation-fiókban található megoldásokkal dolgozik, a következő hiba történik:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Ok

Az Automation erőforrás-szolgáltató nincs regisztrálva az előfizetésben.

### <a name="resolution"></a>Megoldás:

Az Automation erőforrás-szolgáltató regisztrálásához hajtsa végre az alábbi lépéseket a Azure Portal.

1. A portál alján található Azure-szolgáltatások listájában válassza a **minden szolgáltatás**lehetőséget, majd válassza az **előfizetések** lehetőséget az általános szolgáltatás csoportban.

2. Válassza ki előfizetését.

3. A **Beállítások**területen válassza az **erőforrás-szolgáltatók**elemet.

4. Az erőforrás-szolgáltatók listájában ellenőrizze, hogy a Microsoft. Automation erőforrás-szolgáltató regisztrálva van-e.

5. Ha nem szerepel a felsorolásban, regisztrálja a Microsoft. Automation szolgáltatót az [erőforrás-szolgáltatói regisztráció](/azure/azure-resource-manager/resource-manager-register-provider-errors)során felmerülő hibák elhárítása című témakörben leírtak szerint.

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Forgatókönyv: az ütemezett frissítés dinamikus ütemezéssel kihagyott néhány gépet

### <a name="issue"></a>Probléma

A frissítés előzetes verziójában található gépek nem jelennek meg az ütemezett futtatás során kijavított gépek listájában.

### <a name="cause"></a>Ok

A probléma a következő okok egyike lehet:

* A dinamikus lekérdezés hatókörében definiált előfizetések nincsenek konfigurálva a regisztrált Automation erőforrás-szolgáltatóhoz.

* A gépek nem voltak elérhetők, vagy az ütemterv végrehajtásakor nem voltak megfelelő címkék.

### <a name="resolution"></a>Megoldás:

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>A regisztrált Automation erőforrás-szolgáltatóhoz nem konfigurált előfizetések

Ha az előfizetése nincs konfigurálva az Automation erőforrás-szolgáltatóhoz, nem kérdezheti le és nem kérheti le az adott előfizetéshez tartozó gépekre vonatkozó információkat. Az előfizetés regisztrációjának ellenőrzéséhez kövesse az alábbi lépéseket.

1. A [Azure Portalban](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)nyissa meg az Azure-szolgáltatások listáját.

2. Válassza a **minden szolgáltatás**lehetőséget, majd az általános szolgáltatási csoportban válassza az **előfizetések** lehetőséget. 

3. Keresse meg az üzemelő példány hatókörében meghatározott előfizetést.

4. A **Beállítások**területen válassza az **erőforrás-szolgáltatók**lehetőséget.

5. Ellenőrizze, hogy a Microsoft. Automation erőforrás-szolgáltató regisztrálva van-e.

6. Ha nem szerepel a felsorolásban, regisztrálja a Microsoft. Automation szolgáltatót az [erőforrás-szolgáltatói regisztráció](/azure/azure-resource-manager/resource-manager-register-provider-errors)során felmerülő hibák elhárítása című témakörben leírtak szerint.

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>A gép nem érhető el, vagy nem megfelelően van címkézve a végrehajtás közben

Ha az előfizetése az Automation erőforrás-szolgáltatóhoz van konfigurálva, az alábbi eljárást követve, de a frissítési ütemterv futtatása a megadott [dinamikus csoportokkal](../automation-update-management-groups.md) kimaradt néhány gépen.

1. A Azure Portal nyissa meg az Automation-fiókot, és válassza a **Update Management**lehetőséget.

2. Tekintse meg [Update Management előzményeket](https://docs.microsoft.com/azure/automation/manage-update-multi#view-results-of-an-update-deployment) , és határozza meg a frissítés központi telepítésének pontos idejét. 

3. A Update Management által kihagyott gépek esetében az Azure Resource Graph (ARG) használatával [Keresse meg a gép módosításait](https://docs.microsoft.com/azure/governance/resource-graph/how-to/get-resource-changes#find-detected-change-events-and-view-change-details). 

4. A frissítések központi telepítésének futtatása előtt jelentős időszakon, például egy napon belül megkeresheti a módosításokat.

5. Tekintse meg a keresési eredményeket a rendszermódosítások, például a frissítések törlési vagy frissítési módosításai esetében ebben az időszakban. Ezek a módosítások megváltoztathatják a gépek állapotát vagy címkéit, hogy a gépek ne legyenek kijelölve a számítógépek listájában a frissítések telepítésekor.

6. Szükség szerint módosítsa a gépek és az erőforrás beállításait a gép állapotának és a címkézési hibáknak megfelelően.

7. Futtassa újra a frissítési ütemtervet, és győződjön meg arról, hogy a megadott dinamikus csoportokkal történő telepítés minden gépet tartalmaz.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Forgatókönyv: a várt gépek nem jelennek meg a dinamikus csoport előzetes verziójában

### <a name="issue"></a>Probléma

A dinamikus csoport kiválasztott hatókörökhöz tartozó virtuális gépek nem jelennek meg a Azure Portal előnézet listájában. Ez a lista a kijelölt hatókörökhöz tartozó ARG-lekérdezés által beolvasott összes gépet tartalmazza. A hatókörök szűrése olyan gépek esetében történik, amelyeken telepítve vannak a hibrid Runbook-feldolgozók, és amelyhez hozzáférési jogosultságokkal rendelkezik. 

### <a name="cause"></a>Ok
 
A probléma lehetséges okai a következők:

* Nem rendelkezik a megfelelő hozzáféréssel a kijelölt hatókörökhöz.
* Az ARG-lekérdezés nem kéri le a várt gépeket.
* A hibrid Runbook Worker nincs telepítve a gépeken.

### <a name="resolution"></a>Megoldás: 

#### <a name="incorrect-access-on-selected-scopes"></a>Helytelen hozzáférés a kijelölt hatókörökön

A Azure Portal csak azokat a gépeket jeleníti meg, amelyekhez írási hozzáférése van egy adott hatókörben. Ha nem rendelkezik megfelelő hozzáféréssel a hatókörhöz, tekintse meg az [oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a RBAC és a Azure Portal használatával](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

#### <a name="arg-query-doesnt-return-expected-machines"></a>Az ARG-lekérdezés nem ad vissza várt gépeket

Az alábbi lépéseket követve ellenőrizheti, hogy a lekérdezések megfelelően működnek-e.

1. Futtasson egy, az alábbi ábrán látható ARG-lekérdezést az Azure Portal erőforrás-diagram Explorer paneljén. Ez a lekérdezés a dinamikus csoport Update Management való létrehozásakor kiválasztott szűrőket utánozza. Lásd: [dinamikus csoportok használata a Update Management használatával](https://docs.microsoft.com/azure/automation/automation-update-management-groups). 

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   Például:

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```
 
2. Ellenőrizze, hogy a keresett gépek szerepelnek-e a lekérdezés eredményei között. 

3. Ha a gépek nincsenek felsorolva, valószínűleg probléma van a dinamikus csoportban kiválasztott szűrővel. Szükség szerint módosítsa a csoport konfigurációját.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>A hibrid Runbook Worker nincs telepítve a gépeken

A gépek az ARG-lekérdezés eredményeiben jelennek meg, de még mindig nem jelennek meg a dinamikus csoport előzetes verziójában. Ebben az esetben előfordulhat, hogy a gépek nem a hibrid feldolgozóként vannak megjelölve, ezért nem futtathatják Azure Automation és Update Management feladatokat. Győződjön meg arról, hogy a látni kívánt gépek hibrid Runbook-feldolgozóként vannak beállítva:

1. A Azure Portal lépjen egy olyan gép Automation-fiókjára, amely nem megfelelően jelenik meg.

2. Válassza a **hibrid munkavégző csoportok** lehetőséget a **folyamat automatizálása**alatt.

3. Válassza ki a **System Hybrid Worker groups** fület.

4. Ellenőrizze, hogy a hibrid feldolgozó megtalálható-e a gépen.

5. Ha a gép nem hibrid feldolgozóként van beállítva, a [hibrid Runbook-feldolgozók használatával hajtsa végre az adatközpontban vagy a felhőben lévő erőforrások automatizálására](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)vonatkozó utasításokat.

6. Csatlakoztassa a gépet a hibrid Runbook Worker csoportjához.

7. Ismételje meg a fenti lépéseket minden olyan gépen, amely nem jelenik meg az előzetes verzióban.

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Forgatókönyv: a Update Management megoldáshoz tartozó összetevők engedélyezve vannak, míg a virtuális gép továbbra is konfigurálva van

### <a name="issue"></a>Probléma

A következő üzenetet a virtuális gépen 15 perccel a bevezetést követően továbbra is megtekintheti:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

* Az Automation-fiókkal folytatott kommunikáció blokkolása folyamatban van.

* A számítógép neve eltérő forrásoldali számítógép-azonosítóval rendelkezik. Ez a forgatókönyv akkor fordul elő, ha egy adott számítógépnévvel rendelkező virtuális gépet különböző erőforráscsoportok hoznak létre, és az előfizetés azonos logisztikai ügynök munkaterületére jelent jelentést.

* Előfordulhat, hogy az előkészítés alatt álló virtuálisgép-rendszerkép olyan klónozott gépről származik, amely nem lett előkészítve a rendszer-előkészítővel (Sysprep), és a Windows Log Analytics ügynöke telepítve van.

### <a name="resolution"></a>Megoldás:

A virtuális géppel kapcsolatos pontos probléma meghatározásához futtassa az alábbi lekérdezést az Automation-fiókjához csatolt Log Analytics munkaterületen.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Az Automation-fiókkal folytatott kommunikáció blokkolva

Nyissa meg a [hálózat megtervezése](../automation-update-management.md#ports) című témakört, amelyből megtudhatja, mely címeket és portokat kell engedélyezni a Update Management működéséhez.

#### <a name="duplicate-computer-name"></a>Ismétlődő számítógépnév

Nevezze át a virtuális gépeket, hogy a környezetében egyedi nevek legyenek.

#### <a name="onboarded-image-from-cloned-machine"></a>Bekészített rendszerkép klónozott gépről

Ha klónozott képet használ, a különböző számítógépnevek ugyanazzal a forrásoldali számítógép-AZONOSÍTÓval rendelkeznek. Ebben az esetben:

1. A Log Analytics munkaterületen távolítsa el a virtuális gépet a `MicrosoftDefaultScopeConfig-Updates` hatókör-konfiguráció mentett keresésével, ha az megjelenik. A mentett keresések a munkaterület **általános** területén találhatók.

2. Futtassa a következő parancsmagot.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Az `Restart-Service HealthService` állapotfigyelő szolgáltatás újraindításához futtassa a parancsot. A művelet újból létrehozza a kulcsot, és létrehoz egy új UUID-t.

4. Ha ez a megközelítés nem működik, először futtassa a Sysprept a rendszerképen, majd telepítse az MMA-t.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Forgatókönyv: egy másik Azure-bérlőben lévő gépekhez tartozó frissítési központi telepítés létrehozásakor a rendszer csatolt előfizetési hibát kap

### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg, amikor megpróbál létrehozni egy másik Azure-bérlőben lévő gépekhez tartozó frissítési központi telepítést:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, amikor olyan frissítési központi telepítést hoz létre, amely egy másik bérlőn található Azure-beli virtuális gépekkel rendelkezik.

### <a name="resolution"></a>Megoldás:

A következő megkerülő megoldással kérheti le ezeket az elemeket. A [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationschedule?view=azps-3.7.0) parancsmagot használhatja a `ForUpdateConfiguration` paraméterrel az ütemterv létrehozásához. Ezután használja a [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) parancsmagot, és adja át a többi bérlőben található gépeket a `NonAzureComputer` paraméternek. Az alábbi példa bemutatja, hogyan teheti meg ezt:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Forgatókönyv: megmagyarázhatatlan újraindítások

### <a name="issue"></a>Probléma

Annak ellenére, hogy az **Újraindítás-vezérlési** lehetőséget állította be, hogy **Soha ne induljon újra**, a számítógépek még a frissítések telepítése után is újraindulnak.

### <a name="cause"></a>Ok

Windows Update több beállításkulcs is módosítható, amelyek közül bármelyik módosíthatja az újraindítási viselkedést.

### <a name="resolution"></a>Megoldás:

Tekintse át az [Automatikus frissítések konfigurálása](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) szakaszban felsorolt beállításkulcsokat, és szerkessze az [újraindításhoz használt](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) beállításjegyzéket és beállításkulcsokat, és győződjön meg arról, hogy a gépek megfelelően vannak konfigurálva.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Forgatókönyv: a gép a "nem sikerült elindítani" hibaüzenetet jeleníti meg egy frissítés központi telepítésében

### <a name="issue"></a>Probléma

A gép `Failed to start` állapotot jelenít meg. Amikor megtekinti a gép konkrét részleteit, a következő hibaüzenet jelenik meg:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok valamelyike miatt jelentkezhet:

* A gép már nem létezik.
* A gép ki van kapcsolva, és nem érhető el.
* A gép hálózati kapcsolattal rendelkezik, ezért a hibrid feldolgozó a gépen nem érhető el.
* Frissült az MMA, amely megváltoztatta a forrásoldali számítógép AZONOSÍTÓját.
* A frissítési kísérlet szabályozása megtörtént, ha elér egy Automation-fiók 2000 egyidejű feladatának korlátját. Az egyes központi telepítések feladatoknak minősülnek, és a frissítések központi telepítésének minden gépe feladatoknak számít. Az Automation-fiókban jelenleg futó egyéb automatizálási feladatok vagy frissítési üzembe helyezések az egyidejű feladatok korlátja felé mutatnak.

### <a name="resolution"></a>Megoldás:

Ha alkalmazható, használjon [dinamikus csoportokat](../automation-update-management-groups.md) a frissítés központi telepítéséhez. Emellett az alábbi lépéseket is végrehajthatja.

1. Győződjön meg arról, hogy a gép még létezik, és elérhető. 
2. Ha a gép nem létezik, szerkessze az üzemelő példányt, és távolítsa el a gépet.
3. Tekintse meg a [hálózati tervezés](../automation-update-management.md#ports) szakaszt a Update Managementhoz szükséges portok és címek listájának megtekintéséhez, majd ellenőrizze, hogy a gép megfelel-e a követelményeknek.
4. Ellenőrizze a hibrid Runbook-feldolgozóval való kapcsolatot a hibrid Runbook Worker Agent-hibakereső használatával. A hibaelhárítással kapcsolatos további tudnivalókért tekintse meg a [frissítési ügynökkel kapcsolatos problémák elhárítása](update-agent-issues.md)című témakört.
5. Futtassa a következő lekérdezést a Log Analyticsban azon gépek megkereséséhez, amelyekben a forrás számítógép azonosítója megváltozott. Keressen olyan számítógépeket, amelyek azonos `Computer` értékkel rendelkeznek, `SourceComputerId` de eltérő értékkel rendelkeznek.

   ```kusto
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

6. Miután megtalálta az érintett gépeket, szerkessze az ezeket a gépeket tároló frissítési központi telepítéseket, majd távolítsa `SourceComputerId` el és olvassa el őket, hogy a megfelelő értéket tükrözze.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Forgatókönyv: a frissítések központi telepítés nélkül települnek

### <a name="issue"></a>Probléma

Ha Update Managementban regisztrál egy Windows-gépet, a frissítések telepítése nélkül települnek.

### <a name="cause"></a>Ok

Windows rendszeren a frissítések automatikusan települnek, amint elérhetők. Ez a viselkedés zavart okozhat, ha nem ütemezett frissítést a gépre való telepítéshez.

### <a name="resolution"></a>Megoldás:

A `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` beállításkulcs alapértelmezett értéke 4: `auto download and install`.

Update Management ügyfelek esetében javasoljuk, hogy a kulcs beállítását 3: `auto download but do not auto install`.

További információ: az [Automatikus frissítések konfigurálása](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Forgatókönyv: a gép már regisztrálva van egy másik fiókban

### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Ok

A gép már bekerült a Update Management egy másik munkaterületére.

### <a name="resolution"></a>Megoldás:

1. Kövesse a következő szakaszban leírt lépéseket [a portálon a Update Management alatt](#nologs) , hogy a gép a megfelelő munkaterületre legyen bejelentve.
2. Távolítsa el az összetevőket a gépen a [hibrid runbook törlésével](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group), majd próbálkozzon újra.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Forgatókönyv: a gép nem tud kommunikálni a szolgáltatással

### <a name="issue"></a>Probléma

A következő hibaüzenetek egyike jelenik meg:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Ok

Előfordulhat, hogy A proxy, az átjáró vagy a tűzfal blokkolja a hálózati kommunikációt. 

### <a name="resolution"></a>Megoldás:

Tekintse át a hálózatkezelést, és győződjön meg arról, hogy a megfelelő portok és címek engedélyezettek. A Update Management és a hibrid Runbook-feldolgozók által igényelt portok és címek listáját a [hálózati követelmények](../automation-hybrid-runbook-worker.md#network-planning) című témakörben tekintheti meg.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Forgatókönyv: nem hozható létre önaláírt tanúsítvány

### <a name="issue"></a>Probléma

A következő hibaüzenetek egyike jelenik meg:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Ok

A hibrid Runbook Worker nem tudott önaláírt tanúsítványt előállítani.

### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy a rendszerfiók rendelkezik-e olvasási hozzáféréssel a **C:\ProgramData\Microsoft\Crypto\RSA** mappához, és próbálkozzon újra.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Forgatókönyv: az ütemezett frissítés MaintenanceWindowExceeded hibával meghiúsult

### <a name="issue"></a>Probléma

A frissítések alapértelmezett karbantartási időszaka 120 perc. A karbantartási időszakot legfeljebb 6 órára növelheti, vagy 360 percet is igénybe vehet.

### <a name="resolution"></a>Megoldás:

Szerkessze a sikertelen ütemezett frissítések telepítését, és növelje a karbantartási időszakot.

További információ a karbantartási időszakokról: [Install Updates (frissítések telepítése](../automation-tutorial-update-management.md#schedule-an-update-deployment)).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Forgatókönyv: a gép "nincs kiértékelésre", és egy HRESULT-kivételt mutat be

### <a name="issue"></a>Probléma

* Vannak olyan gépek, amelyek a `Not assessed` **megfelelőség**alatt jelennek meg, és egy kivételt jelző üzenet jelenik meg.
* Megjelenik egy HRESULT-hibakód a portálon.

### <a name="cause"></a>Ok

A frissítési ügynök (Windows Update ügynök a Windows rendszeren; a Linux-disztribúcióhoz tartozó csomagkezelő) nincs megfelelően konfigurálva. Update Management a gép frissítési ügynökére támaszkodik, hogy megadja a szükséges frissítéseket, a javítás állapotát és a telepített javítások eredményét. Ezen információk nélkül Update Management nem tud megfelelően jelentést készíteni a szükséges vagy telepített javításokról.

### <a name="resolution"></a>Megoldás:

Próbálja helyileg végrehajtani a frissítéseket a gépen. Ha a művelet meghiúsul, általában azt jelenti, hogy a frissítési ügynök konfigurációs hibája van.

Ezt a problémát gyakran a hálózati konfiguráció és a tűzfal okozta problémák okozzák. A probléma elhárításához kövesse az alábbi ellenőrzéseket.

* Linux esetén ellenőrizze a megfelelő dokumentációt, és győződjön meg arról, hogy el tudja érni a csomag tárházának hálózati végpontját.

* Windows esetén tekintse meg az ügynök konfigurációját a [frissítések nem töltik le az intranetes végpontról (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).

  * Ha a gépek Windows Updatehoz vannak konfigurálva, győződjön meg arról, hogy elérheti a [http/proxy szolgáltatással kapcsolatos problémákban](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)leírt végpontokat.
  * Ha a gépek Windows Server Update Services (WSUS) használatára vannak konfigurálva, győződjön meg arról, hogy elérheti a [WUServer beállításkulcs](/windows/deployment/update/waas-wu-settings)által konfigurált WSUS-kiszolgálót.

Ha megjelenik egy HRESULT, kattintson duplán a pirosban megjelenő kivételre a teljes kivétel üzenet megjelenítéséhez. Tekintse át a következő táblázatot a lehetséges megoldásokhoz vagy ajánlott műveletekhez.

|Kivétel  |Megoldás vagy művelet  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | A kivétel okának további részleteiért keresse meg a megfelelő hibakódot a [Windows Update hibakódok listájában](https://support.microsoft.com/help/938205/windows-update-error-code-list) .        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Ezek hálózati kapcsolati problémákat jeleznek. Győződjön meg arról, hogy a számítógépének van hálózati kapcsolata Update Managementhoz. A szükséges portok és címek listájának megtekintéséhez tekintse meg a [hálózati tervezés](../automation-update-management.md#ports) szakaszt.        |
|`0x8024001E`| A frissítési művelet nem fejeződött be, mert a szolgáltatás vagy a rendszer leállítása megtörtént.|
|`0x8024002E`| Windows Update szolgáltatás le van tiltva.|
|`0x8024402C`     | Ha WSUS-kiszolgálót használ, győződjön meg arról, hogy a beállításjegyzék `WUServer` -kulcs `WUStatusServer` értéke a `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` megfelelő WSUS-kiszolgáló, illetve a beállításkulcs alatt van megadva.        |
|`0x80072EE2`|Hálózati kapcsolati probléma vagy probléma történt a konfigurált WSUS-kiszolgálóval való kommunikáció során. Ellenőrizze a WSUS beállításait, és ellenőrizze, hogy a szolgáltatás elérhető-e az ügyfélről.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Ellenőrizze, hogy a Windows Update szolgáltatás (wuauserv) fut-e, és nincs-e letiltva.        |
|`0x80070005`| A hozzáférés-megtagadási hibát a következők egyike okozhatja:<br> Fertőzött számítógép<br> A Windows Update beállítások nincsenek megfelelően konfigurálva<br> Fájl engedélyével kapcsolatos hiba a%WinDir%\SoftwareDistribution mappában<br> Nincs elég szabad lemezterület a rendszermeghajtón (C:).
|Bármely más általános kivétel     | Futtasson keresést az interneten a lehetséges megoldásokért, és működjön együtt a helyi informatikai támogatással.         |

A **%windir%\WindowsUpdate.log** -fájl áttekintése segíthet a lehetséges okok meghatározásában is. További információ a napló beolvasásáról: [a windowsupdate. log fájl olvasása](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

Emellett letöltheti és futtathatja a [Windows Update-hibakeresőt](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) , hogy megkeresse a gépen Windows Update kapcsolatos problémákat.

> [!NOTE]
> A [Windows Update-hibakereső](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) dokumentációja azt jelzi, hogy a Windows-ügyfeleken használható, de a Windows Serveren is működik.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Forgatókönyv: a frissítés futtatása sikertelen állapotot ad vissza (Linux)

### <a name="issue"></a>Probléma

A frissítési kísérlet elindul, de hibákat észlel a Futtatás során.

### <a name="cause"></a>Ok

Lehetséges okok:

* A csomagkezelő állapota nem kifogástalan.
* A frissítési ügynök (WUA for Windows, disztribúció-specifikus csomagkezelő for Linux) hibásan van konfigurálva.
* A konkrét csomagok nem akadályozzák a felhőalapú javításokat.
* A gép nem érhető el.
* A frissítések nem oldották meg a függőségeket.

### <a name="resolution"></a>Megoldás:

Ha a frissítés sikeres elindítása után hibák lépnek fel, ellenőrizze a Futtatás során az érintett gép [kimenetét](../manage-update-multi.md#view-results-of-an-update-deployment) . Előfordulhat, hogy olyan hibaüzeneteket talál a gépekről, amelyeken kutatást végezhet, és műveleteket hajthat végre. Update Management megköveteli, hogy a Package Manager Kifogástalan állapotba kerüljön a sikeres frissítések központi telepítéséhez.

Ha az adott javítások, csomagok vagy frissítések közvetlenül a feladatok végrehajtása előtt láthatók, kihagyhatja [ezeket az](../automation-tutorial-update-management.md#schedule-an-update-deployment) elemeket a következő frissítés központi telepítésében. A naplófájlok Windows Updateból való összegyűjtéséhez lásd: [Windows Update naplófájlok](/windows/deployment/update/windows-update-logs).

Ha nem tudja feloldani a javítási problémát, készítsen másolatot a **/var/opt/Microsoft/omsagent/Run/automationworker/omsupdatemgmt.log** fájlról, és őrizze meg a hibaelhárítási célból, mielőtt a következő frissítés telepítése megkezdődik.

## <a name="patches-arent-installed"></a>Nincsenek telepítve a javítások

### <a name="machines-dont-install-updates"></a>A gépek nem telepítenek frissítéseket

Próbálja meg közvetlenül a gépen lefuttatni a frissítéseket. Ha a gép nem tudja alkalmazni a frissítéseket, tekintse [át a lehetséges hibák listáját a hibaelhárítási útmutatóban](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).

Ha a frissítések helyileg futnak, próbálja meg eltávolítani és újratelepíteni az ügynököt a gépen a [virtuális gép eltávolítása a Update Managementról](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources)című témakör útmutatását követve.

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Tudom, hogy vannak-e elérhető frissítések, de nem jelennek meg a saját gépeken

Ez általában akkor fordul elő, ha a számítógépek WSUS-vagy Microsoft-végponttól érkező frissítések beszerzésére vannak konfigurálva, Configuration Manager azonban a WSUS és a Configuration Manager nem hagyta jóvá a frissítéseket.

Annak ellenőrzéséhez, hogy a gépek konfigurálva vannak-e a WSUS-hez és a SCCM- `UseWUServer` hez, a [jelen cikk beállításjegyzékének szerkesztésével megtekintheti az automatikus frissítések konfigurálása szakaszban](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)található beállításkulcsokat.

Ha a WSUS nem jóváhagyja a frissítéseket, azok nincsenek telepítve. A nem jóváhagyott frissítéseket a Log Analytics a következő lekérdezés futtatásával tekintheti meg.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>A frissítések telepítettként jelennek meg, de nem találom őket a számítógépen

A frissítéseket gyakran felülírják más frissítések. További információ: a [frissítés felülírva](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) a Windows Update hibaelhárítási útmutatójában.

### <a name="installing-updates-by-classification-on-linux"></a>Frissítések telepítése besorolás szerint Linuxon

Ha Linuxon dolgozik, a frissítések besorolás szerinti („kritikus és biztonsági frissítések”) üzembe helyezésekor fontos kikötéseket kell figyelembe venni, különösen CentOS használata esetén. Ezek a korlátozások dokumentálva vannak a [Update Management áttekintés oldalon](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>A KB2267602 konzisztensen hiányzik

A KB2267602 a [Windows Defender definíciófrissítése](https://www.microsoft.com/wdsi/definitions). Naponta frissül.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz.

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* A szolgáltatással való együttműködéshez [@AzureSupport](https://twitter.com/azuresupport)a hivatalos Microsoft Azure fiók a felhasználói élmény javítása érdekében.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
