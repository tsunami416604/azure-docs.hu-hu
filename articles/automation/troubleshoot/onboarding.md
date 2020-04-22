---
title: Az Azure Automation felügyeleti megoldásainak bevezetésével kapcsolatos hibaelhárítás
description: További információ a megoldás bevezetési hibáinak elhárításáról.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679234"
---
# <a name="troubleshoot-solution-onboarding"></a>Megoldás bevezetési hibáinak elhárítása

Előfordulhat, hogy hibaüzenetet kap a Frissítéskezelés vagy a Változáskövetés és készlet megoldás bevezetésével. Ez a cikk ismerteti a különböző hibákat, amelyek előfordulhatnak, és hogyan lehet megoldani őket.

## <a name="known-issues"></a>Ismert problémák

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Eset: A regisztrált csomópont átnevezéséhez törölni vagy újra regisztrálni kell

#### <a name="issue"></a>Probléma

Egy csomópont regisztrálva van az Azure Automationben, majd az operációs rendszer számítógépének neve megváltozik. A csomópontról származó jelentések továbbra is megjelennek az eredeti névvel.

#### <a name="cause"></a>Ok

A regisztrált csomópontok átnevezése nem frissíti a csomópont nevét az Azure Automationben.

#### <a name="resolution"></a>Megoldás:

Törölje a csomópont regisztrációját az Azure Automation-állapotkonfigurációból, majd regisztrálja újra. A szolgáltatásban ezen időpont előtt közzétett jelentések a továbbiakban nem lesznek elérhetők.

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Eset: A tanúsítványok https proxyn keresztüli újbóli aláírása nem támogatott

#### <a name="issue"></a>Probléma

Ha olyan proxymegoldáson keresztül csatlakozik, amely megszünteti a HTTPS-forgalmat, majd egy új tanúsítvánnyal újra titkosítja a forgalmat, a szolgáltatás nem engedélyezi a kapcsolatot.

#### <a name="cause"></a>Ok

Az Azure Automation nem támogatja a forgalom titkosításához használt tanúsítványok újraaláírását.

#### <a name="resolution"></a>Megoldás:

A probléma jelenleg nem kerülő megoldás.

## <a name="general-errors"></a>Általános hibák

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Eset: A bevezetés sikertelen az üzenettel – A megoldás nem engedélyezhető

#### <a name="issue"></a>Probléma

Az alábbi üzenetek egyike jelenik meg, amikor egy virtuális gép rekedegy megoldásba:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Ok

Ezt a hibát a virtuális gép vagy munkaterület helytelen vagy hiányzó engedélyei, illetve a felhasználó hibája okozza.

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy megfelelő [engedélyekkel rendelkezik a gépek bedeszkázásához,](../automation-role-based-access-control.md#onboarding-permissions) majd próbálja meg újra beszállni a megoldást. Ha megjelenik `The solution cannot be enabled on this VM because the permission to read the workspace is missing`a hiba , `Microsoft.OperationalInsights/workspaces/read` győződjön meg arról, hogy rendelkezik az engedéllyel, hogy képes legyen megtalálni, ha a virtuális gép egy munkaterületre van beosztva.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Eset: A bevezetés sikertelen az üzenettel: Nem sikerült beállítani az automatizálási fiókot a diagnosztikai naplózáshoz

#### <a name="issue"></a>Probléma

A következő üzenet jelenik meg, amikor egy virtuális gép reklamál egy megoldásba:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Ok

Ez a hiba akkor okozható, ha a tarifacsomag nem egyezik meg az előfizetés számlázási modelljével. Lásd: [Használat figyelése és a becsült költségek az Azure Monitorban.](https://aka.ms/PricingTierWarning)

#### <a name="resolution"></a>Megoldás:

Hozza létre a Log Analytics-munkaterületet manuálisan, és ismételje meg a bevezetési folyamatot a létrehozott munkaterület kiválasztásához.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Eset: ComputerGroupQueryFormatError

#### <a name="issue"></a>Probléma

Ez a hibakód azt jelenti, hogy a megoldás megcélzásához használt mentett keresőszámítógép-csoport lekérdezése nincs megfelelően formázva. 

#### <a name="cause"></a>Ok

Lehet, hogy módosítottuk a lekérdezést, vagy a rendszer módosította azt.

#### <a name="resolution"></a>Megoldás:

Törölheti a megoldás lekérdezését, majd újra beviheti a megoldást, amely újra létrehozza a lekérdezést. A lekérdezés a munkaterület mentett **keresések**területén található. A lekérdezés neve **MicrosoftDefaultComputerGroup**, a lekérdezés kategóriája pedig a társított megoldás neve. Ha több megoldás van engedélyezve, a **MicrosoftDefaultComputerGroup** lekérdezés többször is megjelenik a **Mentett keresések csoportban.**

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Eset: Irányelvsértés

#### <a name="issue"></a>Probléma

Ez a hibakód azt jelzi, hogy a központi telepítés egy vagy több házirend megsértése miatt nem sikerült.

#### <a name="cause"></a>Ok 

Egy házirend blokkolja a művelet befejezését.

#### <a name="resolution"></a>Megoldás:

A megoldás sikeres üzembe helyezéséhez meg kell fontolnia a megadott házirend módosítását. Mivel számos különböző típusú szabályzatok, amelyek megadhatók, a szükséges módosítások attól függ, hogy a szabályzat megsérti. Ha például egy olyan erőforráscsoporton definiálva van egy házirend, amely megtagadja az egyes tartalmazott erőforrások tartalmának módosítására vonatkozó engedélyt, az alábbi javítások közül választhat:

* Távolítsa el a házirendet teljesen.
* Próbálja meg a megoldást egy másik erőforráscsoportba beszállni.
* Célozza meg újra a házirendet egy adott erőforrásra, például egy Automation-fiókra.
* Vizsgálja felül a házirend által megtagadandó erőforrások készletét.

Ellenőrizze az értesítéseket az Azure Portal jobb felső sarkában, vagy keresse meg az Automation-fiókot tartalmazó erőforráscsoportot, és válassza **a Központi telepítések lehetőséget** a **Beállítások** csoportban a sikertelen központi telepítés megtekintéséhez. Ha többet szeretne megtudni az Azure-szabályzatról, [olvassa el az Azure-szabályzat áttekintése című témakört.](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Eset: Hibák, amelyek egy munkaterület leválasztását kísérelik meg

#### <a name="issue"></a>Probléma

Munkaterület leválasztásakor a következő hibaüzenet jelenik meg:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha továbbra is aktív megoldások vannak a Log Analytics-munkaterületen, amelyek az Automation-fióktól és a Log Analytics-munkaterülettől függenek.

### <a name="resolution"></a>Megoldás:

Ha használja őket, távolítsa el az alábbi megoldásokat a munkaterületről:

* Frissítéskezelés
* Change Tracking és Inventory
* Virtuális gépek indítása és leállítása munkaidőn kívül

A megoldások eltávolítása után leválaszthatja a munkaterületet. Fontos, hogy törölje a meglévő összetevőket ezekből a megoldásokból a munkaterületről és az Automation-fiókból 

* Frissítéskezelés esetén távolítsa el a frissítések központi telepítések (ütemezések) az Automation-fiókból.
* A virtuális gépek munkaidőn kívüli indítása/leállítása esetén távolítsa el az Automation-fiók megoldásösszetevőinek zárolását a **Beállítások** > **zárolása**területen. Lásd: [Virtuális gépek indítása/leállítása munkaidőn kívüli megoldás közben.](../automation-solution-vm-management.md#remove-the-solution)

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Log Analytics windowsos bővítményhibákhoz

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

A Windows-bővítményhez készült Log Analytics-ügynök telepítése számos okból sikertelen lehet. A következő szakasz ismerteti a bevezetési problémákat, amelyek hibákat okozhatnak a Windows-naplóügynök-bővítmény üzembe helyezése során.

>[!NOTE]
>A Windows Log Analytics-ügynöka a Microsoft Monitoring Agent (MMA) Azure Automation szolgáltatásában jelenleg használt név.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Eset: Kivétel történt egy WebClient-kérelem során

A Virtuális gép Windows-alapú log analytics bővítménye nem tud kommunikálni a külső erőforrásokkal, és a központi telepítés sikertelen lesz.

#### <a name="issue"></a>Probléma

Az alábbi példák a visszaadott hibaüzenetekre mutatnak be:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Ok

A hiba néhány lehetséges oka:

* A virtuális gépben konfigurált proxy csak bizonyos portokat engedélyez.
* A tűzfalbeállítás letiltotta a szükséges portokhoz és címekhez való hozzáférést.

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy a megfelelő portok és címek nyitva vannak a kommunikációhoz. A portok és címek listáját a Hálózat tervezése témakörben [található.](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Eset: A telepítés átmeneti környezeti problémák miatt nem sikerült

A Log Analytics for Windows bővítmény telepítése a telepítés során nem sikerült, mert egy másik telepítés vagy művelet megakadályozta a telepítést

#### <a name="issue"></a>Probléma

Az alábbi példák példákat a hibaüzenetek visszaadhatja:

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

A hiba néhány lehetséges oka:

* Egy másik telepítés van folyamatban.
* A rendszer újraindul a sablon telepítése során.

#### <a name="resolution"></a>Megoldás:

Ez a hiba átmeneti jellegű. Próbálja meg újra a központi telepítést a bővítmény telepítéséhez.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Eset: Telepítési időhosszabbítás

A Windows-napló bővítmény telepítése időtúltöltés miatt nem fejeződött be.

#### <a name="issue"></a>Probléma

Az alábbi példa egy hibaüzenetet kaphat, amely et visszakaphatja:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Ok

Ez a hiba típusazért fordul elő, mert a virtuális gép a telepítés során nagy terhelés alatt áll.

### <a name="resolution"></a>Megoldás:

Próbálja meg telepíteni a Log Analytics-ügynök Windows-bővítményt, ha a virtuális gép kisebb terhelés alatt van.

## <a name="next-steps"></a>További lépések

Ha a fenti probléma nem jelenik meg, vagy nem tudja megoldani a problémát, próbálkozzon az alábbi csatornák egyikével további támogatásért:

* Válaszokat kaphat az Azure szakértőitől [az Azure Forums segítségével.](https://azure.microsoft.com/support/forums/)
* Lépjen [@AzureSupport](https://twitter.com/azuresupport)kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
* Az Azure-támogatási incidens fájlja. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**
