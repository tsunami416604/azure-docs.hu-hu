---
title: Bevezetési Azure Automation felügyeleti megoldások hibaelhárítása
description: A frissítéskezelési, változáskövetési és készletkezelési megoldásokkal kapcsolatos hibaelhárítási hibák elhárítása
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c949556949e0c187d7c23c4dd32436e245bfbb95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75889337"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>Hibaelhárítás a frissítéskezelés, a változáskövetés és a készlet bevezetéskor

Hibák léphetnek fel, amikor olyan bevezetési megoldásokat alkalmaz, mint a frissítéskezelés vagy a változáskövetés és a készlet. Ez a cikk ismerteti a különböző hibákat, amelyek előfordulhatnak, és hogyan lehet megoldani őket.

## <a name="known-issues"></a>Ismert problémák

### <a name="scenario-renaming-a-registered-node-requires-unregister--register-again"></a><a name="node-rename"></a>Forgatókönyv: A regisztrált csomópont átnevezéséhez újra regisztrálni / regisztrálni kell

#### <a name="issue"></a>Probléma

Egy csomópont regisztrálva van az Azure Automationben, majd az operációs rendszer számítógépneve megváltozik.  A csomópontról származó jelentések továbbra is megjelennek az eredeti névvel.

#### <a name="cause"></a>Ok

A regisztrált csomópontok átnevezése nem frissíti a csomópont nevét az Azure Automationben.

#### <a name="resolution"></a>Megoldás:

Törölje a csomópont regisztrációját az Azure Automation-állapotkonfigurációból, majd regisztrálja újra.  A szolgáltatásban ezen időpont előtt közzétett jelentések a továbbiakban nem lesznek elérhetők.


### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Eset: A tanúsítványok https proxyn keresztüli újbóli aláírása nem támogatott

#### <a name="issue"></a>Probléma

Az ügyfelek arról számoltak be, hogy amikor egy proxymegoldáson keresztül, amely megszünteti a https-forgalmat, majd újra titkosítja a forgalmat egy új tanúsítvány, a szolgáltatás nem teszi lehetővé a kapcsolatot.

#### <a name="cause"></a>Ok

Az Azure Automation nem támogatja a forgalom titkosításához használt tanúsítványok újraaláírását.

#### <a name="resolution"></a>Megoldás:

A probléma nem oldva.

## <a name="general-errors"></a>Általános hibák

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Eset: A bevezetés sikertelen az üzenettel – A megoldás nem engedélyezhető

#### <a name="issue"></a>Probléma

Az alábbi üzenetek egyike jelenik meg, amikor egy virtuális gépet próbál beültetett egy megoldásba:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Ok

Ezt a hibát a virtuális gépen, a munkaterületen vagy a felhasználó számára helytelen vagy hiányzó engedélyek okozzák.

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy megfelelő engedélyekkel rendelkezik a virtuális gép fedélzetének bedeszkázására. Tekintse át a [gépek hez szükséges engedélyeket,](../automation-role-based-access-control.md#onboarding) és próbálja meg újra a megoldást. Ha megjelenik `The solution cannot be enabled on this VM because the permission to read the workspace is missing`a hiba , `Microsoft.OperationalInsights/workspaces/read` győződjön meg arról, hogy rendelkezik az engedéllyel, hogy képes legyen megtalálni, ha a virtuális gép egy munkaterületre van beosztva.

### <a name="scenario-onboarding-fails-with-the-message---failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Eset: A bevezetés sikertelen az üzenettel – Nem sikerült konfigurálni az Automation-fiókot a diagnosztikai naplózáshoz

#### <a name="issue"></a>Probléma

A következő üzenet jelenik meg, amikor egy virtuális gépet próbál beültethet egy megoldásba:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Ok

Ez a hiba akkor okozható, ha a tarifacsomag nem egyezik meg az előfizetés számlázási modelljével. További információ: [Használat figyelése és becsült költségek az Azure Monitorban.](https://aka.ms/PricingTierWarning)

#### <a name="resolution"></a>Megoldás:

Hozza létre a Log Analytics-munkaterületet manuálisan, és ismételje meg a bevezetési folyamatot a létrehozott munkaterület kiválasztásához.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Eset: ComputerGroupQueryFormatError

#### <a name="issue"></a>Probléma

Ez a hibakód azt jelenti, hogy a megoldás megcélzásához használt mentett keresőszámítógép-csoport lekérdezése nem volt megfelelően formázva. 

#### <a name="cause"></a>Ok

Lehet, hogy módosítottuk a lekérdezést, vagy a rendszer módosította.

#### <a name="resolution"></a>Megoldás:

Törölheti a megoldás lekérdezését, és újrabeláthatja a lekérdezést újralétrehozó megoldást. A lekérdezés a munkaterületmentett **keresések**csoportban található. A lekérdezés neve **MicrosoftDefaultComputerGroup**, a lekérdezés kategóriája pedig a lekérdezéshez társított megoldás neve. Ha több megoldás van engedélyezve, a **MicrosoftDefaultComputerGroup** a Mentett keresések csoportban többször is **megjelenik.**

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Eset: Irányelvsértés

#### <a name="issue"></a>Probléma

Ez a hibakód azt jelenti, hogy a központi telepítés egy vagy több házirend megsértése miatt sikertelen volt.

#### <a name="cause"></a>Ok 

Olyan házirend van érvényben, amely megakadályozza a művelet végrehajtását.

#### <a name="resolution"></a>Megoldás:

A megoldás sikeres üzembe helyezéséhez meg kell fontolnia a megadott házirend módosítását. Mivel számos különböző típusú szabályzatok, amelyek meg adhatók, a szükséges konkrét módosítások attól függ, hogy a szabályzat, amely megsérti. Ha például egy olyan erőforráscsoporton definiált házirendet, amely megtagadta az erőforráscsoporton belüli bizonyos típusú erőforrások tartalmának módosítására vonatkozó engedélyt, például az alábbi esetekben teheti meg:

* Távolítsa el a házirendet teljesen.
* Próbáljon meg egy másik erőforráscsoportba lépni.
* Vizsgálja felül a szabályzatot, például:
  * A házirend újracélzása egy adott erőforrásra (például egy adott Automation-fiókra).
  * A házirend által megtagadandó erőforrások készletének felülvizsgálata.

Ellenőrizze az értesítéseket az Azure Portal jobb felső sarkában, vagy keresse meg az erőforráscsoportot, amely tartalmazza az automation-fiókot, és válassza **a Központi telepítések** a **Beállítások** csoportban a sikertelen központi telepítés megtekintéséhez. Ha többet szeretne megtudni az Azure Policy látogatás: [Az Azure-szabályzat áttekintése](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Eset: Hibák, amelyek egy munkaterület leválasztását kísérelik meg

#### <a name="issue"></a>Probléma

Munkaterület leválasztásakor a következő hibaüzenet jelenik meg:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha továbbra is aktív megoldások vannak a Log Analytics-munkaterületen, amelyek az Automation-fiók és a Log Analytics-munkaterület összekapcsolásától függenek.

### <a name="resolution"></a>Megoldás:

A probléma megoldásához a következő megoldásokat kell eltávolítania a munkaterületről, ha használja őket:

* Frissítéskezelés
* Változások követése
* Virtuális gépek indítása és leállítása munkaidőn kívül

Miután eltávolította a megoldásokat, leválaszthatja a munkaterületet. Fontos, hogy törölje a meglévő összetevőket ezekből a megoldásokból a munkaterületről és az Automation-fiókból is.  

* Frissítéskezelés
  * Frissítési telepítések (ütemezések) eltávolítása az Automation-fiókból
* Virtuális gépek indítása és leállítása munkaidőn kívül
  * Távolítsa el az Automatizálási fiók megoldásösszetevőinek zárolását a **Beállítások** > **zárolása**területen.
  * A virtuális gépek indítása/leállítása munkaidőn kívüli megoldás során a [Virtuális gép indítása/leállítása munkaidőn kívüli megoldás eltávolítása](../automation-solution-vm-management.md#remove-the-solution)című témakörben található további lépésekért.

## <a name="mma-extension-failures"></a><a name="mma-extension-failures"></a>MMA-bővítményhibák

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

A megoldás üzembe helyezésekor a kapcsolódó erőforrások széles választéka van telepítve. Az egyik ilyen erőforrás a Microsoft Monitoring Agent Extension vagy a Log Analytics ügynök Linux. Ezek a virtuális gép által telepített virtuálisgép-bővítmények, amelyeket a virtuális gép vendégügynöke telepít, és amely a konfigurált Log Analytics-munkaterülettel való kommunikációért felelős, a bináris fájlok és egyéb fájlok letöltésének későbbi összehangolása céljából. megoldás, amit a bevezetés függ, ha megkezdi a végrehajtást.
Általában először az Értesítési központban megjelenő értesítésből szerez tudomást a Linux-telepítési hibák MMA- vagy Log Analytics-ügynökéről. Az értesítésre kattintva további információkat talál az adott hibáról. Az Erőforráscsoportok erőforrásra való navigálás, majd a benne lévő Központi telepítések elem is részletesen ismerteti a központi telepítési hibákat.
Az MMA vagy a Log Analytics ügynök Linuxra történő telepítése számos okból sikertelen lehet, és a hibák megoldásához szükséges lépések a problémától függően eltérőek lehetnek. Bizonyos hibaelhárítási lépések következnek.

A következő szakasz ismerteti a különböző problémákat, amelyek akkor találkoznak, amikor a bevezetés, amelyek hibát okoznak az MMA-bővítmény üzembe helyezésében.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Eset: Kivétel történt egy WebClient-kérelem során

A virtuális gép MMA-bővítménye nem tud kommunikálni a külső erőforrásokkal, és a központi telepítés sikertelen.

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

* Van egy proxy konfigurálva a virtuális gép, amely csak bizonyos portok engedélyezi.

* A tűzfalbeállítás letiltotta a szükséges portokhoz és címekhez való hozzáférést.

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy a megfelelő portok és címek nyitva vannak a kommunikációhoz. A portok és címek listáját a Hálózat tervezése témakörben [található.](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Eset: A telepítés átmeneti környezeti problémák miatt nem sikerült

A Microsoft Monitoring Agent bővítmény telepítése a telepítés során nem sikerült, mert egy másik telepítés vagy művelet megakadályozta a telepítést

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

* Egy másik telepítés van folyamatban
* A rendszer újraindul a sablon telepítése során

#### <a name="resolution"></a>Megoldás:

Ez a hiba egy átmeneti hiba a természetben. Próbálja meg újra a központi telepítést a bővítmény telepítéséhez.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Eset: Telepítési időhosszabbítás

Az MMA-bővítmény telepítése időtúltöltés miatt nem fejeződött be.

#### <a name="issue"></a>Probléma

A következő példa egy visszaadott hibaüzenet:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Ok

Ez a hiba azért fordul elő, mert a virtuális gép a telepítés során nagy terhelés alatt áll.

### <a name="resolution"></a>Megoldás:

Próbálja meg telepíteni az MMA-bővítményt, ha a virtuális gép kisebb terhelés alatt van.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba – a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
* Ha további segítségre van szüksége, benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**
