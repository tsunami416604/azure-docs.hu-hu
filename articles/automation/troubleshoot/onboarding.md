---
title: Hibák bevezetése az Update Management, Change Tracking and Inventory hibaelhárítása
description: Ismerje meg, az Update Management, Change Tracking és Inventory megoldások bevezetése hibáinak elhárítása
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: eaafee304f606ae4d511a6cea1824c26db838635
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793146"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Hibák elhárítása során megoldások bevezetése

Bevezetési megoldások, például az Update Management vagy a Change Tracking and Inventory során felmerülő hibákat. Ez a cikk ismerteti a különböző előforduló hibákat és azok megoldását.

## <a name="general-errors"></a>Általános hiba

### <a name="missing-write-permissions"></a>Forgatókönyv: Bevezetési meghiúsul, és az üzenet – a megoldást nem lehet engedélyezni.

#### <a name="issue"></a>Probléma

Ön kapni az alábbi üzenetek egyike a munkába egy megoldást a virtuális gép:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Ok

Ez a hiba oka helytelenek vagy hiányoznak a virtuális gépen, a munkaterületet, vagy a felhasználó engedélyeit.

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy megfelelő engedélyekkel ahhoz, előkészítheti a virtuális gép. Tekintse át a [előkészítheti a gépeket szükséges engedélyeket](../automation-role-based-access-control.md#onboarding) , és ismételje üzembe helyezni a megoldást. Ha a hibaüzenet `The solution cannot be enabled on this VM because the permission to read the workspace is missing`, ellenőrizze, hogy a `Microsoft.OperationalInsights/workspaces/read` tudja megtalálni, ha a virtuális gép-e előkészítve a munkaterület számára.

### <a name="computer-group-query-format-error"></a>Forgatókönyv: ComputerGroupQueryFormatError

#### <a name="issue"></a>Probléma

Erről a hibakódról azt jelenti, hogy az a megoldás célozzák mentett keresés számítógépcsoport-lekérdezés formázása nem megfelelő. 

#### <a name="cause"></a>Ok

Előfordulhat, hogy módosította a lekérdezést, vagy, előfordulhat, hogy módosította a rendszer.

#### <a name="resolution"></a>Megoldás:

Törölheti a lekérdezés a megoldáshoz, és a reonboard a megoldást, amely a lekérdezést újból létrehozza. A lekérdezés is található a munkaterület alatt **mentett keresések**. A lekérdezés neve **MicrosoftDefaultComputerGroup**, és a lekérdezés a kategória a lekérdezéshez tartozó megoldás nevére. Ha több megoldás engedélyezve vannak, a **MicrosoftDefaultComputerGroup** jeleníti meg többször a **mentett keresések**.

### <a name="policy-violation"></a>Forgatókönyv: PolicyViolation

#### <a name="issue"></a>Probléma

Erről a hibakódról azt jelenti, hogy a központi telepítés egy vagy több szabályzat megsértése miatt nem sikerült.

#### <a name="cause"></a>Ok 

A házirend van érvényben, amely blokkolja a művelet végrehajtását.

#### <a name="resolution"></a>Megoldás:

A megoldás sikeresen üzembe kell figyelembe venni a jelzett szabályzat módosítása. Annyi házirendek, amelyek számos különböző típusú, az egyes módosításokat szükséges a házirendet, amely sérül függenek. Például ha egy házirend már definiálva lett egy erőforráscsoportot, amely engedéllyel az adott erőforráscsoporton belüli erőforrások bizonyos típusú tartalmának módosítására, sikerült, például szeretne a következő:

* Távolítsa el teljesen a szabályzatot.
* Próbáljon üzembe helyezni egy másik erőforráscsoportba.
* Vizsgálja felül a házirendet, például:
  * Újra célzó a szabályzat egy adott erőforrás (például egy meghatározott Automation-fiók).
  * A készlet indítják az erőforrások az adott házirendnek megtagadásához lett konfigurálva.

Ellenőrizze az Azure Portal jobb felső sarokban az értesítéseket, vagy keresse meg az erőforráscsoportot, amely tartalmazza az automation-fiókot, és válassza **központi telepítések** alatt **beállítások** megtekintéséhez a sikertelen a központi telepítés. További információ az Azure Policy látogasson el: [Az Azure Policy áttekintése](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="mma-extension-failures"></a>Az MMA-bővítményekkel kapcsolatos hibák

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

A megoldás üzembe helyezésekor, a kapcsolódó erőforrások különböző vannak üzembe helyezve. Ezek az erőforrások egyike a Microsoft Monitoring Agent bővítményt és a Log Analytics Linux-ügynököt. Ezek a felelős a konfigurált Log Analytics-munkaterület újabb összehangolását a bináris fájlok letöltése céljából kommunikál a virtuális gép Vendégügynökét által telepített virtuális gépi bővítmények és egyéb fájlokat, a megoldás Ön bevezetési függenek, a végrehajtás megkezdése után.
Általában először tudomást az MMA és a Log Analytics-ügynök Linux telepítési hibák az értesítési központ megjelenő értesítésekből. Az értesítések kattintva további információt nyújt az adott problémát. Az erőforráscsoportok erőforráshoz, majd azt a központi telepítések elemet a navigációs is részleteket nyújt a telepítési hibákat, amelyek történt.
Az MMA és a Log Analytics Linux-ügynök telepítése nem sikerül, annak számos okból, és ezek a hibák elhárításához szereplő lépések eltérnek attól függően, a probléma. Kövesse az adott hibaelhárítási lépéseket.

Az alábbi szakasz között közben visszatérhet, amikor a bevezetési okozó hiba az MMA-bővítmény a központi telepítésben lévő különböző kapcsolatos problémákat ismerteti.

### <a name="webclient-exception"></a>Forgatókönyv: Kivétel történt egy webes kérelem során

Az MMA bővítményt a virtuális gép nem tud kommunikálni külső erőforrások és az üzembe helyezés sikertelen.

#### <a name="issue"></a>Probléma

A következő példák visszaadott hibaüzenetek:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Ok

Ez a hiba, néhány lehetséges okok a következők:

* Nincs proxy beállítva a virtuális gépen, amely csak bizonyos portokat engedélyez.

* Egy tűzfalbeállítás letiltotta a hozzáférést a szükséges portok és címét.

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy rendelkezik a megfelelő portokat és a címek nyissa meg a kommunikációhoz. Portok és -címek listáját lásd: [hálózat megtervezése](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Forgatókönyv: A telepítés egy átmeneti környezet problémák miatt nem sikerült

Üzembe helyezés során egy másik telepítés vagy blokkolja a telepítési művelet miatt nem sikerült a Microsoft Monitoring Agent bővítmény telepítése

#### <a name="issue"></a>Probléma

Az alábbiakban visszaadott hibaüzenetek példái:

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

Ez a hiba, néhány lehetséges okok a következők:

* Egy másik telepítés már folyamatban van
* Akkor aktiválódik, a rendszer az újraindításra a sablon telepítése

#### <a name="resolution"></a>Megoldás:

Ez a hiba nem átmeneti hiba jellegűek. Próbálkozzon újra a telepítéssel, a bővítmény telepítésére.

### <a name="installation-timeout"></a>Forgatókönyv: Telepítés időkorlátja

Az MMA-bővítmény telepítése időtúllépés miatt nem fejeződött be.

#### <a name="issue"></a>Probléma

Az alábbi példában van egy visszaadott hibaüzenet:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, mert a virtuális gép telepítése során a nagy terhelés alatt folyamatban.

### <a name="resolution"></a>Megoldás:

Kísérlet történt az MMA-bővítményének telepítése során a virtuális gép egy alacsonyabb terhelés alatt van.

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.
