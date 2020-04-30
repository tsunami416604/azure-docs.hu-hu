---
title: Azure Automation felügyeleti megoldások bevezetésének hibaelhárítása
description: Útmutató a megoldás-előkészítési hibák elhárításához.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679234"
---
# <a name="troubleshoot-solution-onboarding"></a>Megoldások előkészítésének megoldása

Előfordulhat, hogy a Update Management megoldás, illetve a Change Tracking és a leltározási megoldás bevezetéséhez hibákat kell kapnia. Ez a cikk az esetlegesen előforduló különböző hibákat és azok megoldását ismerteti.

## <a name="known-issues"></a>Ismert problémák

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Forgatókönyv: a regisztrált csomópontok átnevezéséhez regisztrációt kell törölni, vagy újra regisztrálni kell

#### <a name="issue"></a>Probléma

Egy csomópont regisztrálva van Azure Automation, majd megváltoztatják az operációs rendszer számítógépének nevét. A csomópont jelentései továbbra is az eredeti névvel jelennek meg.

#### <a name="cause"></a>Ok

A regisztrált csomópontok átnevezése nem frissíti a csomópont nevét a Azure Automationban.

#### <a name="resolution"></a>Megoldás:

Törölje a csomópont regisztrációját Azure Automation állapot-konfigurációból, majd regisztrálja újra. A szolgáltatásban közzétett jelentések már nem lesznek elérhetők.

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Forgatókönyv: a tanúsítványoknak a https-proxyn keresztül történő ismételt aláírása nem támogatott

#### <a name="issue"></a>Probléma

Ha olyan proxy-megoldáson keresztül csatlakozik, amely leállítja a HTTPS-forgalmat, majd újra titkosítja a forgalmat egy új tanúsítvánnyal, a szolgáltatás nem engedélyezi a kapcsolatot.

#### <a name="cause"></a>Ok

Azure Automation nem támogatja a forgalom titkosításához használt tanúsítványok ismételt aláírását.

#### <a name="resolution"></a>Megoldás:

A probléma jelenleg nem kerül megkerülő megoldással.

## <a name="general-errors"></a>Általános hibák

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Forgatókönyv: az előkészítés sikertelen az üzenettel – a megoldás nem engedélyezhető.

#### <a name="issue"></a>Probléma

A következő üzenetek egyike jelenik meg, amikor megkísérli bevezetni egy virtuális gépet egy megoldásba:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Ok

Ezt a hibát a virtuális gép vagy munkaterület, illetve a felhasználó számára helytelen vagy hiányzó engedélyek okozzák.

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy megfelelő [engedélyekkel rendelkezik a számítógépek](../automation-role-based-access-control.md#onboarding-permissions) előkészítéséhez, majd próbálkozzon újra a megoldás bevezetésével. Ha a hibaüzenet `The solution cannot be enabled on this VM because the permission to read the workspace is missing`jelenik meg, ellenőrizze, hogy rendelkezik- `Microsoft.OperationalInsights/workspaces/read` e engedéllyel ahhoz, hogy a virtuális gép be legyen-e telepítve a munkaterületre.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Forgatókönyv: az előkészítés sikertelen a következő üzenettel: nem sikerült konfigurálni az Automation-fiókot a diagnosztikai naplózáshoz

#### <a name="issue"></a>Probléma

A következő üzenet jelenik meg, amikor kísérletet tesz a virtuális gép egy megoldásba való bevezetésére:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Ok

Ez a hiba akkor okozható, ha a díjszabási csomag nem egyezik az előfizetés számlázási modelljével. Lásd: [a használat figyelése és a becsült költségek Azure monitorban](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Megoldás:

Hozza létre manuálisan a Log Analytics munkaterületet, és ismételje meg a bevezetési folyamatot a létrehozott munkaterület kiválasztásához.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Forgatókönyv: ComputerGroupQueryFormatError

#### <a name="issue"></a>Probléma

Ez a hibakód azt jelenti, hogy a rendszer nem formázza megfelelően a mentett keresés számítógépcsoport-lekérdezést, amely a megoldás megcélzására szolgál. 

#### <a name="cause"></a>Ok

Lehetséges, hogy módosította a lekérdezést, vagy megváltoztatta a rendszeren.

#### <a name="resolution"></a>Megoldás:

Törölheti a megoldás lekérdezését, majd újra felkészítheti a megoldást, amely újból létrehozza a lekérdezést. A lekérdezés a munkaterületen, a **mentett keresések**területen található. A lekérdezés neve **MicrosoftDefaultComputerGroup**, a lekérdezés kategóriája pedig a társított megoldás neve. Ha több megoldás is engedélyezve van, a **MicrosoftDefaultComputerGroup** -lekérdezés többször is megjeleníti a **mentett keresések**alatt.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Forgatókönyv: PolicyViolation

#### <a name="issue"></a>Probléma

Ez a hibakód azt jelzi, hogy a központi telepítés egy vagy több házirend megsértése miatt meghiúsult.

#### <a name="cause"></a>Ok 

Egy házirend megakadályozza a művelet befejezését.

#### <a name="resolution"></a>Megoldás:

A megoldás sikeres üzembe helyezéséhez meg kell fontolnia a jelzett házirend módosítását. Mivel számos különböző típusú szabályzat definiálható, a szükséges módosítások a megsértett házirendtől függenek. Ha például egy olyan erőforráscsoport-házirend van meghatározva, amely megtagadja az egyes foglalt erőforrások tartalmának módosítására vonatkozó engedélyt, a következő javítások közül választhat:

* Távolítsa el a szabályzatot teljes egészében.
* Próbálja meg egy másik erőforráscsoporthoz bevezetni a megoldást.
* A szabályzatot egy adott erőforráshoz (például egy Automation-fiókhoz) újra megcélozhatja.
* Módosítsa azon erőforrások készletét, amelyeknek a szabályzata el van tiltva.

Tekintse át az értesítéseket a Azure Portal jobb felső sarkában, vagy navigáljon az Automation-fiókját tartalmazó erőforráscsoporthoz, és a **Beállítások** területen válassza a **központi telepítések** lehetőséget a sikertelen telepítés megtekintéséhez. További információ a Azure Policyről: [Azure Policy áttekintése](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Forgatókönyv: a munkaterület leválasztását megkísérelő hibák

#### <a name="issue"></a>Probléma

A munkaterület leválasztására tett kísérlet során a következő hibaüzenetet kapja:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a Log Analytics munkaterületen olyan megoldások vannak aktívak, amelyek az Automation-fióktól függenek, és Log Analytics a munkaterület csatolva van.

### <a name="resolution"></a>Megoldás:

Ha használja őket, távolítsa el a következő megoldásokat a munkaterületről:

* Frissítéskezelés
* Change Tracking és Inventory
* Virtuális gépek indítása és leállítása munkaidőn kívül

A megoldások eltávolítása után megszüntetheti a munkaterület összekapcsolását. Fontos, hogy a munkaterületről és az Automation-fiókból törölje a meglévő összetevőkből származó összetevőket. 

* Update Management esetén távolítsa el az Automation-fiókból a frissítési központi telepítéseket (ütemterveket).
* Start/Stop VMS During off-hours esetén távolítsa el a megoldás-összetevők zárolásait az Automation-fiókban a **Beállítások** > **zárolása**alatt. Lásd: [a Start/Stop VMS During off-hours megoldás eltávolítása](../automation-solution-vm-management.md#remove-the-solution).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>A Windows-bővítményekkel kapcsolatos hibák Log Analytics

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

A Windows-bővítmény Log Analytics ügynökének telepítése különböző okok miatt sikertelen lehet. A következő szakasz azokat a bevezetési problémákat ismerteti, amelyek meghibásodást okozhatnak a Windows-bővítmény Log Analytics ügynökének telepítésekor.

>[!NOTE]
>A Windows rendszerhez készült Log Analytics ügynök a Microsoft monitoring Agent (MMA) számára jelenleg Azure Automation használt név.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Forgatókönyv: kivétel történt egy WebClient-kérelem során

A virtuális gépen futó Windows-bővítmény Log Analytics nem tud kommunikálni a külső erőforrásokkal, és a telepítés meghiúsul.

#### <a name="issue"></a>Probléma

Az alábbi példák a visszaadott hibaüzenetekre mutatnak:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Ok

A hiba lehetséges okai a következők:

* A virtuális gépen konfigurált proxyk csak bizonyos portokat engedélyeznek.
* A tűzfalbeállítások letiltotta a szükséges portokhoz és címekhez való hozzáférést.

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy a megfelelő portok és címek nyitva vannak a kommunikációhoz. A portok és címek listáját a [hálózat megtervezése](../automation-hybrid-runbook-worker.md#network-planning)című témakörben tekintheti meg.

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Forgatókönyv: a telepítés átmeneti környezeti problémák miatt nem sikerült

Nem sikerült telepíteni a Windows rendszerhez készült Log Analytics bővítményt a telepítés során, mert egy másik telepítés vagy művelet blokkolja a telepítést

#### <a name="issue"></a>Probléma

A következő hibaüzenetek jelenhetnek meg:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Ok

A hiba lehetséges okai a következők:

* Egy másik telepítés folyamatban van.
* A rendszer újraindítást kezdeményez a sablon üzembe helyezésekor.

#### <a name="resolution"></a>Megoldás:

Ez a hiba átmeneti jellegű. Próbálja megismételni a telepítést a bővítmény telepítéséhez.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Forgatókönyv: telepítési időtúllépés

A Windows-bővítményhez tartozó Log Analytics telepítése időtúllépés miatt nem fejeződött be.

#### <a name="issue"></a>Probléma

A következő példa egy hibaüzenetet jelenít meg:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Ok

Ez a típusú hiba azért fordul elő, mert a virtuális gép a telepítés során nagy terhelés alatt áll.

### <a name="resolution"></a>Megoldás:

Ha a virtuális gép alacsonyabb terhelés alatt van, próbálja meg telepíteni a Windows-bővítmény Log Analytics-ügynökét.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* Az Azure [@AzureSupport](https://twitter.com/azuresupport)-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával, a hivatalos Microsoft Azure fiókkal való csatlakozással javíthatja az ügyfelek élményét: válaszokat, támogatást és szakértőket.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
