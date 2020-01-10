---
title: A Azure Automation felügyeleti megoldások bevezetésének megoldása
description: Ismerje meg, hogyan lehet elhárítani a bevezetési hibákat a Update Management-, Change Tracking-és leltározási megoldásokkal
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 737b963074a2bec851882bddd78ad0b89f48d1d9
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769897"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>Hibák elhárítása Update Management, Change Tracking és leltár bevezetéséhez

Hibák merülhetnek fel az olyan megoldások bevezetéséhez, mint a Update Management vagy a Change Tracking és a leltár. Ez a cikk az esetlegesen előforduló különböző hibákat és azok megoldását ismerteti.

## <a name="known-issues"></a>Ismert problémák

### <a name="node-rename"></a>Forgatókönyv: a regisztrált csomópont átnevezéséhez újra kell regisztrálnia/regisztrálnia

#### <a name="issue"></a>Probléma

Egy csomópont regisztrálva van Azure Automation, majd az operációs rendszer számítógépnévje módosul.  A csomópont jelentései továbbra is az eredeti névvel jelennek meg.

#### <a name="cause"></a>Ok

A regisztrált csomópontok átnevezése nem frissíti a csomópont nevét a Azure Automationban.

#### <a name="resolution"></a>Felbontás

Törölje a csomópont regisztrációját Azure Automation állapot-konfigurációból, majd regisztrálja újra.  A szolgáltatásban közzétett jelentések már nem lesznek elérhetők.


### <a name="resigning-cert"></a>Forgatókönyv: a tanúsítványoknak a https-proxyn keresztül történő ismételt aláírása nem támogatott

#### <a name="issue"></a>Probléma

Az ügyfelek arról számoltak be, hogy amikor egy proxy-megoldáson keresztül csatlakozik a https-forgalom megszakításához, majd újratitkosítja a forgalmat egy új tanúsítvánnyal, a szolgáltatás nem teszi lehetővé a kapcsolat használatát.

#### <a name="cause"></a>Ok

Azure Automation nem támogatja a forgalom titkosításához használt tanúsítványok ismételt aláírását.

#### <a name="resolution"></a>Felbontás

Ehhez a hibához nem kerül megkerülő megoldás.

## <a name="general-errors"></a>Általános hibák

### <a name="missing-write-permissions"></a>Forgatókönyv: az előkészítés sikertelen az üzenettel – a megoldás nem engedélyezhető.

#### <a name="issue"></a>Probléma

A következő üzenetek egyike jelenik meg a virtuális gépek megoldásba való bevezetésének megkísérlése során:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Ok

Ezt a hibát a virtuális gép, a munkaterület vagy a felhasználó számára helytelen vagy hiányzó engedélyek okozzák.

#### <a name="resolution"></a>Felbontás

Győződjön meg arról, hogy megfelelő engedélyekkel rendelkezik a virtuális gép előkészítéséhez. Tekintse át a gépek bevezetéséhez [szükséges engedélyeket](../automation-role-based-access-control.md#onboarding) , és próbálkozzon újra a megoldás bevezetésével. Ha `The solution cannot be enabled on this VM because the permission to read the workspace is missing`hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e a `Microsoft.OperationalInsights/workspaces/read` engedéllyel ahhoz, hogy a virtuális gép be legyen-e telepítve egy munkaterületre.

### <a name="diagnostic-logging"></a>Forgatókönyv: az előkészítés sikertelen az üzenettel – nem sikerült konfigurálni az Automation-fiókot a diagnosztikai naplózáshoz

#### <a name="issue"></a>Probléma

A következő üzenet jelenik meg, amikor kísérletet tesz egy virtuális gép egy megoldásba való bevezetésére:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Ok

Ez a hiba akkor okozható, ha a díjszabási csomag nem egyezik az előfizetés számlázási modelljével. További információ: [a használat figyelése és a becsült költségek Azure monitorban](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Felbontás

Hozza létre manuálisan a Log Analytics munkaterületet, és ismételje meg a bevezetési folyamatot a létrehozott munkaterület kiválasztásához.

### <a name="computer-group-query-format-error"></a>Forgatókönyv: ComputerGroupQueryFormatError

#### <a name="issue"></a>Probléma

Ez a hibakód azt jelenti, hogy a mentett keresési számítógépcsoport-lekérdezés a megoldás céljára való használata nem megfelelő formátumú. 

#### <a name="cause"></a>Ok

Lehetséges, hogy módosította a lekérdezést, vagy a rendszeren módosították.

#### <a name="resolution"></a>Felbontás

Törölheti a megoldás lekérdezését, és áthelyezheti a megoldást, amely újból létrehozza a lekérdezést. A lekérdezés a munkaterületen belül, a **mentett keresések**területen található. A lekérdezés neve **MicrosoftDefaultComputerGroup**, a lekérdezés kategóriája pedig a lekérdezéshez társított megoldás neve. Ha több megoldás van engedélyezve, a **MicrosoftDefaultComputerGroup** többször is megjelenik a **mentett keresések**alatt.

### <a name="policy-violation"></a>Forgatókönyv: PolicyViolation

#### <a name="issue"></a>Probléma

Ez a hibakód azt jelenti, hogy a központi telepítés egy vagy több házirend megsértése miatt meghiúsult.

#### <a name="cause"></a>Ok 

Olyan házirend van érvényben, amely blokkolja a műveletet.

#### <a name="resolution"></a>Felbontás

A megoldás sikeres üzembe helyezéséhez meg kell fontolnia a jelzett házirend módosítását. Mivel számos különböző típusú szabályzat definiálható, a megadott módosítások a megsértett házirendtől függenek. Ha például egy olyan erőforráscsoport-szabályzatot adott meg, amely megtagadta az adott erőforráscsoporthoz tartozó bizonyos típusú erőforrások tartalmának módosítását, akkor például a következők bármelyikét teheti:

* Távolítsa el a szabályzatot teljes egészében.
* Próbáljon meg bejelentkezni egy másik erőforráscsoporthoz.
* Módosítsa a szabályzatot, például:
  * A szabályzat ismételt megcélzása egy adott erőforráshoz (például egy adott Automation-fiókhoz).
  * A házirend által megtagadásra konfigurált erőforrások készletének módosítása.

Tekintse át az értesítéseket a Azure Portal jobb felső sarkában, vagy navigáljon az Automation-fiókját tartalmazó erőforráscsoporthoz, és válassza a **központi telepítés** lehetőséget a **Beállítások** területen a sikertelen telepítés megtekintéséhez. További információ a Azure Policy-látogatásról: [Azure Policy áttekintése](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="unlink"></a>Forgatókönyv: a munkaterület leválasztását megkísérelő hibák

#### <a name="issue"></a>Probléma

A munkaterület leválasztására tett kísérlet során a következő hibaüzenetet kapja:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a Log Analytics munkaterületen olyan megoldások vannak aktívak, amelyek az Automation-fióktól függenek, és Log Analytics a munkaterület csatolva van.

### <a name="resolution"></a>Felbontás

Ennek megoldásához el kell távolítania a következő megoldásokat a munkaterületről, ha használja őket:

* Frissítéskezelés
* Változáskövetés
* Virtuális gépek munkaidőn kívüli elindítása/leállítása

A megoldások eltávolítása után megszüntetheti a munkaterület összekapcsolását. Fontos, hogy a munkaterületről és az Automation-fiókból is törölje a meglévő összetevőkből származó összetevőket.  

* Frissítéskezelés
  * Frissítési központi telepítések (ütemtervek) eltávolítása az Automation-fiókból
* Virtuális gépek munkaidőn kívüli elindítása/leállítása
  * Távolítsa el a megoldás-összetevők zárolásait az Automation-fiókban a **beállítások** > **zárolások**területen.
  * A Start/Stop VMs during off-hours megoldás eltávolításának további lépéseiért tekintse meg [a virtuális gép elindítása/leállítása a munkaidőn kívüli megoldásban](../automation-solution-vm-management.md##remove-the-solution)című témakört.

## <a name="mma-extension-failures"></a>MMA-bővítmények hibái

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Megoldás telepítésekor a rendszer számos kapcsolódó erőforrást telepít. Az egyik ilyen erőforrás a Microsoft monitoring Agent bővítmény vagy Log Analytics Linux-ügynök. Ezek azok a virtuálisgép-bővítmények, amelyeket a virtuális gép a konfigurált Log Analytics munkaterülettel folytatott kommunikációért felelős, a bináris fájlok és más fájlok letöltésének későbbi koordinálása céljából. a bevezetési megoldás a végrehajtás megkezdése után függ.
Általában először az MMA-t vagy Log Analytics-ügynököt kell ismernie a Linux-telepítési hibákról az értesítési központban megjelenő értesítésekben. Az értesítésre kattintva további információkat tudhat meg az adott hibáról. Navigáljon az erőforráscsoportok erőforráshoz, majd az azon belüli központi telepítések elemhez is tartalmaz részleteket az észlelt telepítési hibákról.
A Linux rendszerhez készült MMA-vagy Log Analytics-ügynök telepítése számos okból meghiúsulhat, és a hibák megoldásához szükséges lépések eltérőek lehetnek a probléma függvényében. Az adott hibaelhárítási lépések követése.

A következő szakasz azokat a különböző problémákat ismerteti, amelyek az MMA-bővítmény központi telepítésének meghibásodását okozó bevezetésnél merülhetnek fel.

### <a name="webclient-exception"></a>Forgatókönyv: kivétel történt egy WebClient-kérelem során

A virtuális gépen lévő MMA-bővítmény nem tud kommunikálni a külső erőforrásokkal, és a telepítés meghiúsul.

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

* Van egy proxy konfigurálva a virtuális gépen, amely csak bizonyos portokat engedélyez.

* A tűzfalbeállítások letiltotta a szükséges portokhoz és címekhez való hozzáférést.

#### <a name="resolution"></a>Felbontás

Győződjön meg arról, hogy a megfelelő portok és címek nyitva vannak a kommunikációhoz. A portok és címek listáját a [hálózat megtervezése](../automation-hybrid-runbook-worker.md#network-planning)című témakörben tekintheti meg.

### <a name="transient-environment-issue"></a>Forgatókönyv: a telepítés átmeneti környezeti problémák miatt nem sikerült

A Microsoft monitoring Agent bővítmény telepítése nem sikerült a telepítés során, mert egy másik telepítés vagy művelet blokkolja a telepítést

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

* Egy másik telepítés folyamatban van
* A rendszer újraindítást kezdeményez a sablon központi telepítése során

#### <a name="resolution"></a>Felbontás

Ez a hiba átmeneti hiba a természetben. Próbálja megismételni a telepítést a bővítmény telepítéséhez.

### <a name="installation-timeout"></a>Forgatókönyv: telepítési időtúllépés

Az MMA bővítmény telepítése időtúllépés miatt nem fejeződött be.

#### <a name="issue"></a>Probléma

A következő példa a visszaadott hibaüzenetet jeleníti meg:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Ok

Ez a hiba azért fordul elő, mert a virtuális gép a telepítés során nagy terhelés alatt áll.

### <a name="resolution"></a>Felbontás

Próbálja meg telepíteni az MMA-bővítményt, ha a virtuális gép alacsonyabb terhelés alatt van.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
