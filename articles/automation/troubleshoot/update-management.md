---
title: Hibák elhárítása az Azure Update Management szolgáltatással
description: Ismerje meg, hogyan háríthatja el és oldhatja meg az Azure-beli frissítéskezelési megoldással kapcsolatos problémákat.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c9ff05591c98fda8be39e32f26da484f56e0831b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984623"
---
# <a name="troubleshooting-issues-with-update-management"></a>A frissítéskezeléssel kapcsolatos problémák elhárítása

Ez a cikk az Update Management használata során felmerülő problémák megoldásait ismerteti.

Van egy ügynök hibaelhárító a hibrid feldolgozó ügynök határozza meg a mögöttes problémát. A hibaelhárítóról a [Frissítési ügynökkel kapcsolatos problémák elhárítása című témakörben](update-agent-issues.md)olvashat bővebben. Minden egyéb probléma esetén kövesse az alábbi hibaelhárítási útmutatót.

Ha problémákat talál, amikor a megoldás virtuális gépen (VM) történő bevezetésével kapcsolatban, ellenőrizze az **Operations Manager** naplóját az **Alkalmazás- és szolgáltatásnaplók** a helyi számítógépen. Keresse meg a 4502-es eseményazonosítójú `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`eseményeket és az esemény részleteit tartalmazó eseményeket.

A következő szakasz kiemeli a konkrét hibaüzeneteket és az egyes lehetséges megoldásokat. További bevezetési problémákról a [Megoldásbevezetés elhárítása című témakörben](onboarding.md)találja.

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>Eset: A "Nem sikerült engedélyezni a frissítési megoldást" hibaüzenet jelenik meg

### <a name="issue"></a>Probléma

Amikor megpróbálja engedélyezni az Update Management megoldást az Automation-fiókban, a következő hibaüzenet jelenik meg:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

* Előfordulhat, hogy a Log Analytics-ügynök hálózati tűzfalkövetelményei nincsenek megfelelően konfigurálva. Ez a helyzet az ügynök sikertelensítését okozhatja a DNS-URL-címek feloldásakor.

* A megoldás célzása helytelenül van konfigurálva, és a számítógép nem kapja meg a várt módon a frissítéseket.

* Azt is észreveheti, hogy `Non-compliant` a készülék **megfelelőségi**állapotot mutat. Ugyanakkor az **Agent Desktop Analytics** az `Disconnected`ügynököt a .

### <a name="resolution"></a>Megoldás:

* Futtassa a [Windows](update-agent-issues.md#troubleshoot-offline) vagy [Linux](update-agent-issues-linux.md#troubleshoot-offline)hibaelhárítót , az operációs rendszertől függően.

* A [Hálózattervezés](../automation-hybrid-runbook-worker.md#network-planning) oldalon megtudhatja, hogy mely címeket és portokat kell engedélyezni az Update Management működéséhez.  

* Nyissa meg [a Hálózattervezés című](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) ablakot, amelyből megtudhatja, hogy a Log Analytics-ügynök működéséhez mely címeket és portokat kell engedélyezni.

* Ellenőrizze a hatókör konfigurációs problémáit. [A hatókör konfigurációja](../automation-onboard-solutions-from-automation-account.md#scope-configuration) határozza meg, hogy mely gépek vannak konfigurálva a megoldáshoz. Ha a gép megjelenik a munkaterületen, de nem a **Update Management Portal, be kell állítania a hatókör konfigurációját a gépek célzásához. A hatókör konfigurációjáról a [munkaterület i alaplapi gépei című témakörben olvashat.](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)

* Távolítsa el a dolgozó konfigurációját a [hibrid runbook-feldolgozó törlése](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)című lépés lépéseit követve. 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Eset: Az Update Management szolgáltatásban hiányzóként jelzett, helyettesített frissítés

### <a name="issue"></a>Probléma

A régi frissítések hiányzóként jelennek meg egy Automation-fiókesetében, annak ellenére, hogy felül lettek helyezve. A helyettesített frissítést nem kell telepítenie, mert egy későbbi frissítés, amely kijavítja ugyanazt a biztonsági rést. Update Management figyelmen kívül hagyja a felülhagyott frissítést, és teszi, hogy nem alkalmazható javára a felüllépő frissítés. A kapcsolódó problémáról a [Frissítés felvan oltva](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)című témakörben talál további információt.

### <a name="cause"></a>Ok

A levédett frissítések et nem megfelelően jelöltük elutasítottként, hogy azok nem tekinthetők alkalmazhatónak.

### <a name="resolution"></a>Megoldás:

Ha egy felülfelügyelt frissítés 100 százalékban nem alkalmazhatóvá válik, módosítsa `Declined`a frissítés jóváhagyási állapotát a rendszerre. Az összes frissítés jóváhagyási állapotának módosítása:

1. Az Automation-fiókban válassza a **Kezelés frissítése** lehetőséget a számítógép állapotának megtekintéséhez. Lásd: [Frissítési felmérések megtekintése](../manage-update-multi.md#view-an-update-assessment).

2. Ellenőrizze a leadott frissítést, és győződjön meg arról, hogy az 100 százalékban nem alkalmazható. 

3. Jelölje meg a frissítést elutasítottként, hacsak nincs kérdése a frissítéssel kapcsolatban. 

4. Válassza **a Számítógépek** lehetőséget, és a Megfelelőség **oszlopban** kényszerítse a rescan-t a megfelelőség érdekében. Lásd: [Frissítések kezelése több géphez.](../manage-update-multi.md)

5. Ismételje meg a fenti lépéseket a többi helyettesített frissítések.

6. Futtassa a törlési varázslót az elutasított frissítésekfájljaiból való törléshez. 

7. Windows Server Update Services (WSUS) esetén manuálisan tisztítsa meg az összes lehelyettesített frissítést az infrastruktúra frissítéséhez.

8. Ismételje meg ezt az eljárást rendszeresen a megjelenítési probléma kijavításához és a frissítéskezeléshez felhasznált lemezterület minimalizálásához.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Eset: A gépek nem jelennek meg a portálon az Update Management csoportban

### <a name="issue"></a>Probléma

A gépek a következő tünetek:

* A gép `Not configured` a virtuális gép frissítéskezelési nézetéből jelenik meg.

* A gépek hiányoznak az Azure Automation-fiók Frissítés-kezelés nézetéből.

* Vannak olyan gépei, amelyek a Megfelelőség alatt `Not assessed` **jelennek meg.** Szívverési adatok at az Azure Monitor naplók a hibrid Runbook-feldolgozó, de nem a frissítési kezelés.

### <a name="cause"></a>Ok

Ezt a problémát helyi konfigurációs problémák vagy a nem megfelelően konfigurált hatókör-konfiguráció okozhatja. A lehetséges konkrét okok a következők:

* Előfordulhat, hogy újra regisztrálnia kell, és újra kell telepítenie a hibrid Runbook-feldolgozót.

* Előfordulhat, hogy a munkaterületen meghatározta az elért kvótát, amely megakadályozza a további adattárolást.

### <a name="resolution"></a>Megoldás:

1. Futtassa a [Windows](update-agent-issues.md#troubleshoot-offline) vagy [Linux](update-agent-issues-linux.md#troubleshoot-offline)hibaelhárítót , az operációs rendszertől függően.

2. Győződjön meg arról, hogy a gép a megfelelő munkaterületre jelentés. A szempont ellenőrzéséhez az [Ügynök-kapcsolat ellenőrzése a Log Analytics szolgáltatással kapcsolatban( Verify agent connectivity) (Ügynökkapcsolat ellenőrzése)](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)témakörben talál útmutatást. Győződjön meg arról is, hogy ez a munkaterület kapcsolódik az Azure Automation-fiókhoz. A megerősítéshez nyissa meg az Automation-fiókot, és válassza a **Csatolt munkaterület** lehetőséget a Kapcsolódó **erőforrások csoportban.**

3. Győződjön meg arról, hogy a gépek megjelennek az Automation-fiókhoz kapcsolódó Log Analytics-munkaterületen. Futtassa a következő lekérdezést a Log Analytics-munkaterületen.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. Ha nem látja a gépet a lekérdezés eredményében, akkor nemrég nem jelentkezett be. Valószínűleg helyi konfigurációs probléma van, ezért újra kell [telepítenie az ügynököt.](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows) 

5. Ha a gép megjelenik a lekérdezés eredményében, ellenőrizze a hatókör konfigurációs problémáit. [A hatókör konfigurációja](../automation-onboard-solutions-from-automation-account.md#scope-configuration) határozza meg, hogy mely gépek vannak konfigurálva a megoldáshoz. 

6. Ha a gép megjelenik a munkaterületen, de nem az Update Management, konfigurálnia kell a hatókör konfigurációját a gép célzásához. Ennek módjáról a munkaterület i Alaplapi gépek című [témakörben olvashat.](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)

7. A munkaterületen futtassa ezt a lekérdezést.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. Ha `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` eredményt kap, elérte a munkaterületen meghatározott kvótát, amely megakadályozta az adatok mentését. A munkaterületen nyissa meg az **adatmennyiség-kezelést** **a Használat és a becsült költségek**csoportban, és módosítsa vagy távolítsa el a kvótát.

9. Ha a probléma továbbra is feloldatlan, kövesse a [Windows hibrid runbook-feldolgozó telepítése](../automation-windows-hrw-install.md) című témakör lépéseit a Hibrid feldolgozó Windows-alapú újratelepítéséhez. Linux esetén kövesse a [Linux hibrid runbook-feldolgozó telepítése](../automation-linux-hrw-install.md)című lépéseit.

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Eset: Nem lehet regisztrálni az Automation erőforrás-szolgáltatót az előfizetésekhez

### <a name="issue"></a>Probléma

Amikor az Automation-fiókban dolgozik megoldásokkal, a következő hiba történik:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Ok

Az Automation erőforrás-szolgáltató nincs regisztrálva az előfizetésben.

### <a name="resolution"></a>Megoldás:

Az Automation erőforrás-szolgáltató regisztrálásához kövesse az alábbi lépéseket az Azure Portalon.

1. Az Azure-szolgáltatás listában a portál alján válassza a **Minden szolgáltatás**lehetőséget, majd válassza az **Előfizetések** lehetőséget az Általános szolgáltatáscsoportban.

2. Válassza ki előfizetését.

3. A **Beállítások csoportban**válassza **az Erőforrás-szolgáltatók**lehetőséget.

4. Az erőforrás-szolgáltatók listájából ellenőrizze, hogy a Microsoft.Automation erőforrás-szolgáltató regisztrálva van-e.

5. Ha nem szerepel a listában, regisztrálja a Microsoft.Automation szolgáltatót az [Erőforrás-szolgáltató regisztrációjával kapcsolatos hibák megoldása](/azure/azure-resource-manager/resource-manager-register-provider-errors)című lépés lépéseit követve.

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Eset: Ütemezett frissítés dinamikus ütemezéssel, néhány gépet kihagyott

### <a name="issue"></a>Probléma

A frissítési előzetes verzióban szereplő gépek nem jelennek meg az ütemezett futtatás során javított gépek listájában.

### <a name="cause"></a>Ok

A probléma okai lehetnek:

* A hatókörben definiált dinamikus lekérdezésben definiált előfizetések nincsenek konfigurálva a regisztrált Automation erőforrás-szolgáltatóhoz.

* A gépek nem voltak elérhetők, vagy nem rendelkeztek megfelelő címkékkel az ütemezés végrehajtásakor.

### <a name="resolution"></a>Megoldás:

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>A regisztrált Automation-erőforrás-szolgáltatóhoz nincs konfigurálva előfizetések

Ha az előfizetés nincs konfigurálva az Automation erőforrás-szolgáltatóhoz, nem kérdezhet le vagy nem kaphat le adatokat az adott előfizetésben lévő gépeken. Az alábbi lépésekkel ellenőrizheti az előfizetés regisztrációját.

1. Az [Azure Portalon](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)az Azure-szolgáltatáslista elérése.

2. Válassza a **Minden szolgáltatás**lehetőséget, majd az Általános szolgáltatáscsoport **Előfizetések** csoportjában lehetőséget. 

3. Keresse meg a központi telepítés hatókörében definiált előfizetést.

4. A **Beállítások csoportban**válassza **az Erőforrás-szolgáltatók**lehetőséget.

5. Ellenőrizze, hogy a Microsoft.Automation erőforrás-szolgáltató regisztrálva van-e.

6. Ha nem szerepel a listában, regisztrálja a Microsoft.Automation szolgáltatót az [Erőforrás-szolgáltató regisztrációjával kapcsolatos hibák megoldása](/azure/azure-resource-manager/resource-manager-register-provider-errors)című lépés lépéseit követve.

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>A gépek nem érhetők el, vagy nem megfelelően vannak címkézve az ütemezés végrehajtásakor

Kövesse az alábbi eljárást, ha az előfizetés az Automation erőforrás-szolgáltatóhoz van konfigurálva, de a frissítési ütemezés futtatása a megadott [dinamikus csoportokkal](../automation-update-management-groups.md) kihagyott néhány gépet.

1. Az Azure Portalon nyissa meg az Automation-fiókot, és válassza **a Frissítéskezelés lehetőséget.**

2. Ellenőrizze [a frissítéskezelés előzményeit](https://docs.microsoft.com/azure/automation/manage-update-multi#view-results-of-an-update-deployment) a frissítés központi telepítésének pontos időpontjának meghatározásához. 

3. Az update management által kihagyott gépek esetében az Azure Resource Graph (ARG) segítségével [keresse meg a számítógép módosításait.](https://docs.microsoft.com/azure/governance/resource-graph/how-to/get-resource-changes#find-detected-change-events-and-view-change-details) 

4. A frissítés központi telepítésének futtatása előtt jelentős ideig, például egy napon keresztül keressen módosításokat.

5. Ellenőrizze a keresési eredményeket, hogy vannak-e rendszerszintű változások, például a gépek módosításai törlése vagy frissítése ebben az időszakban. Ezek a módosítások módosíthatják a számítógép állapotát vagy a címkéket, így a frissítések telepítésekor a gépek nem lesznek kiválasztva a számítógéplistában.

6. Szükség szerint módosítsa a gépek és az erőforrás-beállításokat a gép állapotával vagy a címkeproblémákkal kapcsolatos hibák korrigálása érdekében.

7. Futtassa újra a frissítési ütemezést, hogy a megadott dinamikus csoportokkal való központi telepítés tartalmazza-e az összes gépet.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Eset: A várt gépek nem jelennek meg előnézetben a dinamikus csoporthoz

### <a name="issue"></a>Probléma

Egy dinamikus csoport kiválasztott hatóköreinek virtuális gépei nem jelennek meg az Azure Portal előzetes verzióinak listájában. Ez a lista a kijelölt hatókörök ARG-lekérdezése által beolvasott összes gépet tartalmazza. A hatókörök szűrve vannak a hibrid Runbook-feldolgozók telepített gépekhez, amelyekhez hozzáférési engedélyekkel rendelkezik. 

### <a name="cause"></a>Ok
 
A probléma lehetséges okai:

* Nem rendelkezik a megfelelő hozzáféréssel a kijelölt hatókörökhöz.
* Az ARG-lekérdezés nem olvassa be a várt gépeket.
* A hibrid Runbook-feldolgozó nincs telepítve a gépekre.

### <a name="resolution"></a>Megoldás: 

#### <a name="incorrect-access-on-selected-scopes"></a>Helytelen hozzáférés a kijelölt hatókörökön

Az Azure Portalon csak azokat a gépeket jeleníti meg, amelyekhez írási hozzáféréssel rendelkezik egy adott hatókörben. Ha nem rendelkezik a megfelelő hozzáféréssel egy hatókörhöz, olvassa el az [Oktatóanyag: Felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz az RBAC és az Azure Portal használatával című témakört.](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

#### <a name="arg-query-doesnt-return-expected-machines"></a>Az ARG-lekérdezés nem adja vissza a várt gépeket

Az alábbi lépéseket követve megtudhatja, hogy a lekérdezések megfelelően működnek-e.

1. Futtasson egy ARG-lekérdezést az Azure Portal Erőforrásgráf-kezelő panelen látható módon formázva. Ez a lekérdezés az Update Management dinamikus csoportjának létrehozásakor kijelölt szűrőket utánozza. Lásd: [Dinamikus csoportok használata frissítéskezeléssel](https://docs.microsoft.com/azure/automation/automation-update-management-groups). 

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
 
2. Ellenőrizze, hogy a keresett gépek szerepelnek-e a lekérdezés eredményében. 

3. Ha a gépek nem szerepelnek a listában, valószínűleg probléma van a dinamikus csoportban kiválasztott szűrővel. Szükség szerint módosítsa a csoport konfigurációját.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>A hibrid Runbook-feldolgozó nincs telepítve a gépekre

A gépek megjelennek az ARG-lekérdezés eredményében, de még mindig nem jelennek meg a dinamikus csoport előnézetében. Ebben az esetben előfordulhat, hogy a gépek nem vannak megjelölve hibrid dolgozókként, és így nem futtathatnak Azure Automation és Update Management feladatokat. Annak érdekében, hogy a várt gépek hibrid Runbook-feldolgozókként legyenek beállítva:

1. Az Azure Portalon nyissa meg az Automation-fiókot egy olyan géphez, amely nem megfelelően jelenik meg.

2. Válassza a **Hibrid munkavégző csoportok lehetőséget** a **Folyamatautomatizálás csoportban.**

3. Válassza a **Rendszer hibrid munkavégző csoportok** lapot.

4. Ellenőrizze, hogy a hibrid feldolgozó jelen van-e a géphez.

5. Ha a gép nincs hibrid feldolgozóként beállítva, az [adatközpontban vagy a felhőben lévő erőforrások automatizálása](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)utasítások használatával a Hibrid Runbook-feldolgozó használatával módosításokat kell végrehajtania.

6. Csatlakozzon a géphez a hibrid Runbook-feldolgozó csoporthoz.

7. Ismételje meg a fenti lépéseket minden olyan gépen, amely nem jelenik meg az előzetes verzióban.

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Eset: Az Update Management megoldás összetevői engedélyezve vannak, míg a virtuális gép továbbra is konfigurálva jelenik meg

### <a name="issue"></a>Probléma

A következő üzenet jelenik meg a virtuális gépen 15 perccel a bevezetés után:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

* Az Automation-fiókkal való kommunikáció le van tiltva.

* Van egy ismétlődő számítógépnév különböző forrásszámítógép-azonosítókkal. Ez a forgatókönyv akkor fordul elő, ha egy adott számítógépnévvel rendelkező virtuális gép különböző erőforráscsoportokban jön létre, és az előfizetésben ugyanannak a logisztikai ügynöknek a munkaterületnek jelent.

* Előfordulhat, hogy a beépített virtuálisgép-lemezkép olyan klónozott gépről származik, amely nem készült el a Rendszer-előkészítés (sysprep) segítségével, és a Microsoft Monitoring Agent (MMA) telepítve van.

### <a name="resolution"></a>Megoldás:

A virtuális gép pontos problémájának meghatározásához futtassa a következő lekérdezést az Automation-fiókhoz kapcsolódó Log Analytics-munkaterületen.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Kommunikáció az Automation-fiókkal letiltva

A [Hálózattervezés](../automation-update-management.md#ports) oldalon megtudhatja, hogy mely címeket és portokat kell engedélyezni az Update Management működéséhez.

#### <a name="duplicate-computer-name"></a>Duplikált számítógépnév

Nevezze át a virtuális gépeket, hogy biztosítsa az egyedi neveket a környezetükben.

#### <a name="onboarded-image-from-cloned-machine"></a>Beépített kép klónozott gépről

Ha klónozott lemezképet használ, a különböző számítógépnevek nek ugyanaz a forrásszámítógép-azonosítója. Ebben az esetben:

1. A Log Analytics-munkaterületen távolítsa el a virtuális `MicrosoftDefaultScopeConfig-Updates` gép a mentett keresés a hatókör konfigurációját, ha látható. A mentett keresések a munkaterület **Általános** területén találhatók.

2. Futtassa a következő parancsmast.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Futtassa `Restart-Service HealthService` az állapotfigyelő szolgáltatás újraindításához. Ez a művelet újra létrehozza a kulcsot, és létrehoz egy új UUID-t.

4. Ha ez a megközelítés nem működik, először futtassa a Sysprep programot a lemezképen, majd telepítse az MMA-t.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Eset: Egy csatolt előfizetési hibaüzenet jelenik meg, amikor egy másik Azure-bérlőben lévő gépek hez hoz létre frissítési központi telepítést

### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg, amikor egy másik Azure-bérlőben lévő gépekhez próbál létrehozni egy frissítési központi telepítést:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha olyan frissítési központi telepítést hoz létre, amely egy másik bérlőben rendelkezik Azure-virtuális gépekkel, amely egy frissítési központi telepítésben szerepel.

### <a name="resolution"></a>Megoldás:

Az alábbi kerülő megoldás segítségével ütemezheti ezeket az elemeket. A [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) parancsmag használatával `ForUpdate` ütemezést hozhat létre. Ezután használja a [New-AzureRmAutomationSoftwareSoftwareConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) parancsmag, és adja át `NonAzureComputer` a gépeket a másik bérlőben a paraméter. A következő példa bemutatja, hogyan kell ezt megtenni:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Forgatókönyv: Megmagyarázhatatlan újraindítások

### <a name="issue"></a>Probléma

Annak ellenére, hogy az **Újraindítás vezérlés** beállítást **soha újraindításra**állította, a frissítések telepítése után a gépek továbbra is újraindulnak.

### <a name="cause"></a>Ok

A Windows Update több beállításkulcssal is módosítható, amelyek bármelyike módosíthatja az újraindítás viselkedését.

### <a name="resolution"></a>Megoldás:

Tekintse át az [Automatikus frissítések konfigurálása](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) című témakörben felsorolt beállításkulcsokat az újraindítás kezeléséhez használt beállításjegyzék- és [beállításkulcsok](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) szerkesztésével, és győződjön meg arról, hogy a gépek megfelelően vannak konfigurálva.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Eset: A számítógép "Nem sikerült elindítani" lehetőséget jeleníti meg egy frissítési központi telepítésben

### <a name="issue"></a>Probléma

A gép `Failed to start` állapotot mutat. A gép konkrét részleteinek megtekintésekor a következő hiba jelenik meg:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok valamelyike miatt jelentkezhet:

* A gép már nem létezik.
* A készülék ki van kapcsolva, és nem érhető el.
* A gép hálózati kapcsolati probléma, és ezért a hibrid dolgozó a gépen nem érhető el.
* Az MMA-t frissítették, amely megváltoztatta a forrásszámítógép azonosítóját.
* A frissítési futtatás tágítja, ha eléri a 2000 egyidejű feladatok egy Automation-fiókban. Minden központi telepítés feladatnak minősül, és a frissítésközponti telepítés minden gépe feladatnak számít. Bármely más automatizálási feladat vagy frissítési központi telepítés, amely jelenleg fut az Automation-fiókban, beleszámít az egyidejű feladatkorlátba.

### <a name="resolution"></a>Megoldás:

Adott esetben használjon [dinamikus csoportokat](../automation-update-management-groups.md) a frissítési központi telepítésekhez. Ezenkívül a következő lépéseket is megteheti.

1. Ellenőrizze, hogy a gép továbbra is létezik-e és elérhető-e. 
2. Ha a gép nem létezik, szerkesztheti az üzembe helyezést, és távolítsa el a gépet.
3. Tekintse meg a [hálózattervezés](../automation-update-management.md#ports) szakaszban az Update Management hez szükséges portok és címek listáját, majd ellenőrizze, hogy a gép megfelel-e ezeknek a követelményeknek.
4. Ellenőrizze a hibrid Runbook-feldolgozó val a hibrid Runbook feldolgozó ügynök hibaelhárító használatával. A hibaelhárítóról a [Frissítési ügynökkel kapcsolatos problémák elhárítása című témakörben](update-agent-issues.md)olvashat bővebben.
5. Futtassa a következő lekérdezést a Log Analytics a környezetben található olyan gépek megkereséséhez, amelyek a forrásszámítógép-azonosító megváltozott. Keresse meg az azonos `Computer` értékű, `SourceComputerId` de eltérő értékű számítógépeket.

   ```kusto
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

6. Miután megtalálta az érintett gépeket, szerkesztheti a frissítések központi telepítéseit, `SourceComputerId` amelyek ezeket a gépeket célozzák meg, majd távolítsa el és olvassa őket, hogy tükrözze a megfelelő értéket.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Eset: A frissítések telepítése telepítés nélkül

### <a name="issue"></a>Probléma

Amikor windowsos gépet regisztrál az Update Management szolgáltatásban, a frissítések telepítés nélkül települnek.

### <a name="cause"></a>Ok

A Windows rendszerben a frissítések automatikusan települnek, amint elérhetővé válikk. Ez a viselkedés zavart okozhat, ha nem ütemezte be a számítógépre való üzembe helyezést.

### <a name="resolution"></a>Megoldás:

A `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` rendszerleíró kulcs alapértelmezés szerint 4- es érték: `auto download and install`.

Update Management ügyfelek esetén azt javasoljuk, `auto download but do not auto install`hogy ezt a kulcsot 3-ra tállítva: .

További információt az [Automatikus frissítések konfigurálása című témakörben talál.](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates)

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Eset: A gép már regisztrálva van egy másik fiókhoz

### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Ok

A gép már be van táblázva egy másik frissítés-kezelési munkaterületre.

### <a name="resolution"></a>Megoldás:

1. Kövesse a [Gépek nem jelennek meg a portálon az Update Management csoportban,](#nologs) és győződjön meg arról, hogy a gép a megfelelő munkaterületre jelentést tesz.
2. A [hibrid runbook-csoport törlésével](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)törölje a műtermékeket a számítógépen, majd próbálkozzon újra.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Eset: A gép nem tud kommunikálni a szolgáltatással

### <a name="issue"></a>Probléma

Az alábbi hibaüzenetek egyike jelenik meg:

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

Lehet, hogy egy proxy, átjáró vagy tűzfal blokkolja a hálózati kommunikációt. 

### <a name="resolution"></a>Megoldás:

Tekintse át a hálózatot, és győződjön meg arról, hogy a megfelelő portok és címek engedélyezettek. Tekintse meg a [hálózati követelmények](../automation-hybrid-runbook-worker.md#network-planning) listáját portok és címek, amelyek által megkövetelt frissítési felügyelet és a hibrid Runbook dolgozók.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Eset: Nem lehet létrehozni önaláírt tanúsítványt

### <a name="issue"></a>Probléma

Az alábbi hibaüzenetek egyike jelenik meg:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Ok

A hibrid Runbook-feldolgozó nem tudott önaláírt tanúsítványt létrehozni.

### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy a rendszerfiók rendelkezik-e olvasási hozzáféréssel a **C:\ProgramData\Microsoft\Crypto\RSA** mappához, majd próbálkozzon újra.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Eset: Az ütemezett frissítés nem sikerült maintenanceWindowExceeded hibával

### <a name="issue"></a>Probléma

A frissítések alapértelmezett karbantartási időszaka 120 perc. A karbantartási időszakot legfeljebb 6 órára, azaz 360 percre növelheti.

### <a name="resolution"></a>Megoldás:

Szerkesztheti a hibás ütemezett frissítési központi telepítések, és növeli a karbantartási időszak.

A karbantartási időszakokról a [Frissítések telepítése című](../automation-tutorial-update-management.md#schedule-an-update-deployment)témakörben talál további információt.

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Eset: A gép "Nem értékelt" néven jelenik meg, és egy HRESULT kivételt jelenít meg

### <a name="issue"></a>Probléma

* Vannak olyan gépek, `Not assessed` amelyek **a Megfelelőség**alatt jelennek meg, és egy kivételüzenet jelenik meg alattuk.
* Megjelenik egy HRESULT hibakód a portálon.

### <a name="cause"></a>Ok

Az Update Agent (Windows Update Agent windowson; a Linux-disztribúció csomagkezelője) nincs megfelelően konfigurálva. Update Management támaszkodik a gép update agent, hogy a szükséges frissítéseket, a javítás állapotát, és az eredmények et telepített javítások. Ezen információk nélkül az Update Management nem tud megfelelően jelentést tenni a szükséges vagy telepített javításokról.

### <a name="resolution"></a>Megoldás:

Próbálja meg helyileg végrehajtani a frissítéseket a számítógépen. Ha ez a művelet sikertelen, általában azt jelenti, hogy frissítési ügynök konfigurációs hiba van.

Ezt a problémát gyakran a hálózati konfigurációval és a tűzfallal kapcsolatos problémák okozzák. A probléma kijavításához használja az alábbi ellenőrzéseket.

* Linux esetén ellenőrizze a megfelelő dokumentációt, hogy biztosan elérhesse a csomagtár hálózati végpontját.

* Windows esetén ellenőrizze, hogy az ügynök konfigurációja a [Frissítések nem az intranetes végpontról (WSUS/SCCM) töltődik-e le.](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)

  * Ha a gépek a Windows Update szolgáltatáshoz vannak konfigurálva, győződjön meg arról, hogy el tudja érni a [HTTP/proxyval kapcsolatos problémák](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)című részben ismertetett végpontokat.
  * Ha a gépek a Windows Server Update Services (WSUS) szolgáltatáshoz vannak konfigurálva, győződjön meg arról, hogy el tudja érni a [WUServer beállításkulcs](/windows/deployment/update/waas-wu-settings)által konfigurált WSUS-kiszolgálót.

Ha megjelenik egy HRESULT, kattintson duplán a piros színnel megjelenített kivételre a teljes kivételüzenet megjelenítéséhez. Tekintse át az alábbi táblázatot a lehetséges megoldásokért vagy az ajánlott műveletekért.

|Kivétel  |Felbontás vagy művelet  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | A megfelelő hibakódban a [Windows update hibakódlistájában](https://support.microsoft.com/help/938205/windows-update-error-code-list) további részleteket talál a kivétel okáról.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Ezek hálózati kapcsolódási problémákat jeleznek. Győződjön meg arról, hogy a gép rendelkezik hálózati kapcsolattal az Update Management szolgáltatáshoz. A szükséges portok és címek listáját a [hálózattervezés](../automation-update-management.md#ports) című részben található.        |
|`0x8024001E`| A frissítési művelet nem fejeződött be, mert a szolgáltatás vagy a rendszer leállt.|
|`0x8024002E`| A Windows Update szolgáltatás le van tiltva.|
|`0x8024402C`     | Ha WSUS-kiszolgálót használ, győződjön meg `WUServer` arról, hogy a beállításkulcs és `WUStatusServer` a `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` beállításkulcs alatti beállításjegyzék-értékek a megfelelő WSUS-kiszolgálót adják meg.        |
|`0x80072EE2`|Hálózati kapcsolati probléma vagy egy konfigurált WSUS-kiszolgálóval való beszélgetés problémája van. Ellenőrizze a WSUS-beállításokat, és győződjön meg arról, hogy a szolgáltatás elérhető az ügyféltől.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Győződjön meg arról, hogy a Windows Update szolgáltatás (wuauserv) fut, és nem le van tiltva.        |
|`0x80070005`| A hozzáférés-megtagadási hibát az alábbi lehetőségek bármelyike okozhatja:<br> Fertőzött számítógép<br> A Windows Update beállításai nincsenek megfelelően konfigurálva<br> Fájlengedély-hiba a következővel: %WinDir%\SoftwareDistribution mappa<br> Nincs elegendő lemezterület a rendszermeghajtón (C:).
|Bármely más általános kivétel     | Keressen rá az interneten a lehetséges megoldásokra, és működjön együtt a helyi informatikai támogatással.         |

A **%Windir%\Windowsupdate.log** fájl áttekintése segíthet a lehetséges okok meghatározásában is. A napló olvasásáról a [Windowsupdate.log fájl olvasása című](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file)témakörben olvashat bővebben.

A [Windows Update hibaelhárító](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) letöltheti és futtathatja is, hogy ellenőrizze, vannak-e problémák a Windows Update szolgáltatással kapcsolatban a számítógépen.

> [!NOTE]
> A [Windows Update hibaelhárító](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) dokumentációja azt jelzi, hogy Windows-ügyfeleken használható, de Windows Server rendszeren is használható.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Eset: Update run visszaadja sikertelen állapot (Linux)

### <a name="issue"></a>Probléma

A frissítés futtatása elindul, de a futtatás során hibákba ütközik.

### <a name="cause"></a>Ok

Lehetséges okok:

* A csomagkezelő nem kifogástalan.
* Update Agent (WUA for Windows, disztribúció-specifikus csomagkezelő Linux) helytelenül van konfigurálva.
* Bizonyos csomagok zavarják a felhőalapú javításokat.
* A gép nem érhető el.
* A frissítések függőségei nem oldódtak meg.

### <a name="resolution"></a>Megoldás:

Ha a sikeres indítás után egy frissítés futása során hibák lépnek fel, ellenőrizze az érintett gép [kimenetét](../manage-update-multi.md#view-results-of-an-update-deployment) a futás során. Előfordulhat, hogy a gépekről konkrét hibaüzeneteket talál, amelyeket felkutathat, és műveleteket tehet. Az Update Management megköveteli, hogy a csomagkezelő kifogástalan állapotban legyen a sikeres frissítési telepítések hez.

Ha bizonyos javítások, csomagok vagy frissítések közvetlenül a feladat sikertelensítése előtt láthatók, [megpróbálhatja kizárni](../automation-tutorial-update-management.md#schedule-an-update-deployment) ezeket az elemeket a következő frissítés központi telepítéséből. A Windows Update naplóadatainak gyűjtéséhez olvassa el a [Windows Update naplófájljait.](/windows/deployment/update/windows-update-logs)

Ha nem tudja megoldani a javítási problémát, készítsen másolatot a **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** fájlról, és őrizze meg hibaelhárítási célokra a következő frissítés telepítésének megkezdése előtt.

## <a name="patches-arent-installed"></a>Nincs enek telepítve a javítások

### <a name="machines-dont-install-updates"></a>A gépek nem telepítenek frissítéseket

Próbálja meg közvetlenül a gépen lefuttatni a frissítéseket. Ha a készülék nem tudja alkalmazni a frissítéseket, olvassa el [a hibaelhárítási útmutatóban szereplő lehetséges hibák listáját.](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult)

Ha a frissítések helyileg futnak, próbálja meg eltávolítani és újratelepíteni az ügynököt a számítógépen a Virtuális gép eltávolítása az [update management szolgáltatásból](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources)című témakör útmutatását követve.

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Tudom, hogy a frissítések elérhetők, de nem jelennek meg elérhetőként a gépeimen

Ez gyakran előfordul, ha a gépek úgy vannak konfigurálva, hogy frissítéseket kapjanak a WSUS vagy a Microsoft Endpoint Configuration Manager szolgáltatástól, de a WSUS és a Configuration Manager nem hagyta jóvá a frissítéseket.

A cikk `UseWUServer` [Beállításjegyzék-szolgáltatásának szerkesztésével](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)ellenőrizheti, hogy a gépek konfigurálva vannak-e a WSUS és az SCCM szolgáltatáshoz, ha a cikk beállításjegyzék-szakaszában a rendszerleíró kulcsot a beállításjegyzék-kulcsokhoz veti.

Ha a wsus nem hagyja jóvá a frissítéseket, akkor nincsenek telepítve. A nem jóváhagyott frissítések et a Log Analytics szolgáltatásban a következő lekérdezés futtatásával ellenőrizheti.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>A frissítések telepítettként jelennek meg, de nem találom őket a számítógépen

A frissítéseket gyakran felülírják más frissítések. További információt a Windows Update hibaelhárítási útmutatójában a [Frissítés felezésével](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) című témakörben talál.

### <a name="installing-updates-by-classification-on-linux"></a>Frissítések telepítése besorolás szerint Linuxon

Ha Linuxon dolgozik, a frissítések besorolás szerinti („kritikus és biztonsági frissítések”) üzembe helyezésekor fontos kikötéseket kell figyelembe venni, különösen CentOS használata esetén. Ezeket a korlátozásokat a [Frissítéskezelés áttekintése lapon dokumentálja.](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 rendszerkövetkezetesen hiányzik

A KB2267602 a [Windows Defender definíciófrissítése](https://www.microsoft.com/wdsi/definitions). Naponta frissül.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, próbálkozzon az alábbi csatornák egyikével további támogatásért.

* Válaszokat kaphat az Azure szakértőitől [az Azure Forums segítségével.](https://azure.microsoft.com/support/forums/)
* Lépjen [@AzureSupport](https://twitter.com/azuresupport)kapcsolatba a használatával, a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében.
* Az Azure-támogatási incidens fájlja. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**
