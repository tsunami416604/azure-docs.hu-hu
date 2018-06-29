---
title: Hibaelhárítás – az Azure Automation hibrid forgatókönyv-feldolgozók
description: Ez a cikk ismerteti az Azure Automation hibrid forgatókönyv-feldolgozók hibaelhárítási információ
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1cae7253a4bfcb4f83baf003a4d9d3c367d8f014
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063847"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Hibrid forgatókönyv-feldolgozók hibaelhárítása

Ez a cikk tájékoztatást nyújt a hibrid forgatókönyv-feldolgozók problémák elhárításához.

## <a name="general"></a>Általános kérdések

A hibrid forgatókönyv-feldolgozó attól függ, hogy egy ügynök kommunikáljon az Automation-fiók regisztrálása a munkavégző, runbook-feladatok fogadása és jelentse állapotát. Ez az ügynök Windows, a Microsoft Monitoring Agent esetén. A Linux az OMS-ügynököt, Linux esetén.

###<a name="runbook-execution-fails"></a>Forgatókönyv: A Runbook végrehajtása sikertelen lesz.

#### <a name="issue"></a>Probléma

A Runbook végrehajtása sikertelen, és a következő hibaüzenetet kapja:

```
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

A runbook hamarosan után kísérel meg végrehajtani, akkor állítsa be háromszor fel van függesztve. Feltételek vonatkoznak, amelyek megszakíthatja a runbook sikeres befejezését, és a kapcsolódó hibaüzenet tartalmazza, amely jelzi, hogy miért további adatokat.

#### <a name="cause"></a>Ok

Lehetséges lehetséges okok a következők:

* A runbookok nem tudja hitelesíteni a helyi erőforrások

* A hibrid feldolgozó a proxy vagy az tűzfal mögött van

* A runbookok nem tudja hitelesíteni a helyi erőforrások

* A hibrid forgatókönyv-feldolgozó szolgáltatás futtatására kijelölt számítógép megfelel a minimális hardverkövetelményeknek.

#### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy a számítógép *.azure-automation.net kimenő hozzáféréssel rendelkezzen a 443-as porton.

A hibrid forgatókönyv-feldolgozó futtató számítógépeken meg kell felelnie a minimális hardverkövetelményeknek, ez a szolgáltatás futtatásához kijelölése előtt. Ellenkező esetben az attól függően, hogy az erőforrás-használat más háttérfolyamatot és végrehajtása során runbookok által okozott versengés, a számítógép túlterhelt válik, és runbook-feladat késleltetés vagy időtúllépések okozhat.

Győződjön meg arról, a hibrid forgatókönyv-feldolgozó szolgáltatás futtatására kijelölt számítógép megfelel a minimális hardverkövetelményeknek. Ha igen, figyelheti a Processzor- és memóriafelhasználását a hibrid forgatókönyv-feldolgozó folyamat teljesítményét és a Windows között a korrelációs meghatározásához. Ha memória vagy a CPU-terhelés, jelezheti, hogy a szükséges frissítése vagy további processzorok hozzáadásával, vagy növelje a memória erőforrás szűk cím, és hárítsa el a hibát. Azt is megteheti válassza ki a különböző számítási erőforrása, amely támogathatja a minimális követelményeknek és a skála, ha terheléshez növelését szükség.

Ellenőrizze a **Microsoft-SMA** egy kapcsolódó esemény leírása az eseménynaplóban *Win32 a folyamat kilépett a következő kód [4294967295]*. Ez a hiba oka még nem konfigurált hitelesítési a runbookok vagy a hibrid feldolgozócsoport a futtató hitelesítő adatokat adott. Felülvizsgálati [Runbookokra vonatkozó engedélyek](../automation-hrw-run-runbooks.md#runbook-permissions) a runbookok megfelelően konfigurált hitelesítési megerősítéséhez.

## <a name="linux"></a>Linux

A Linux hibrid forgatókönyv-feldolgozó attól függ, hogy az OMS Linux-ügynök kommunikáljon az Automation-fiók regisztrálása a munkavégző, runbook-feladatok fogadása és jelentse állapotát. A dolgozó regisztrálása meghiúsul, ha az alábbiakban néhány a hiba lehetséges okai:

###<a name="oms-agent-not-running"></a>Forgatókönyv: A Linux OMS-ügynök nem fut

Ha Linux az OMS-ügynök nem fut, ez megakadályozza, hogy a Linux hibrid forgatókönyv-feldolgozó Azure Automation kommunikál. Ellenőrizze az ügynök fut-e a következő parancs beírásával: `ps -ef | grep python`. A következő, a python folyamatok hasonló kimenetnek kell megjelennie **nxautomation** felhasználói fiókot. A frissítéskezelés vagy az Azure Automation megoldások nem engedélyezettek, ha nincs a következő folyamatokat is futnak.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Az alábbi listában láthatók az olyan folyamat számára a Linux hibrid forgatókönyv-feldolgozók. Az összes találhatók a `/var/opt/microsoft/omsagent/state/automationworker/` könyvtár.

* **OMS.conf** – Ez a munkafolyamat-kezelő, ez közvetlenül a DSC elindult.

* **Worker.conf** – Ez a folyamat az automatikus regisztrált hibrid munkavégző folyamat, a worker-kezelő elindult. Ez a folyamat felügyeletéhez használja, és átlátható a felhasználó. Ez a folyamat nincs jelen, ha a frissítés-kezelési megoldás nincs engedélyezve a számítógépen.

* **diy/Worker.conf** – Ez a folyamat a DIY hibrid munkavégző folyamatban. A DIY hibrid munkavégző folyamat hajthatók végre a felhasználó a runbookok a hibrid forgatókönyv-feldolgozót a. Csak eltér az automatikus regisztrált hibrid munkavégző folyamat a kulcs részletesen, amely használja egy másik konfigurációt. Ez a folyamat nincs jelen, ha nincs engedélyezve az Azure Automation-megoldást, és a Linux DIY Hibridfeldolgozó nincs regisztrálva.

Ha az OMS-ügynököt a Linux nem fut, a következő parancsot a szolgáltatás elindítása: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

###<a name="class-does-not-exist"></a>Forgatókönyv: A megadott osztály nem létezik.

Ha a hibát látja: **a megadott osztály nem létezik.** az a `/var/opt/microsoft/omsconfig/omsconfig.log` majd Linux OMS-ügynököt frissíteni kell. A következő paranccsal telepítse újra az OMS-ügynököt:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

A Windows hibrid forgatókönyv-feldolgozó attól függ, hogy a Microsoft figyelési ügynök kommunikáljon az Automation-fiók regisztrálása a munkavégző, runbook-feladatok fogadása és jelentse állapotát. A dolgozó regisztrálása meghiúsul, ha az alábbiakban néhány a hiba lehetséges okai:

###<a name="mma-not-running"></a>Forgatókönyv: A Microsoft Monitoring Agent nem fut.

#### <a name="issue"></a>Probléma

A `healthservice` a hibrid forgatókönyv-feldolgozó számítógépen nem fut a szolgáltatás.

#### <a name="cause"></a>Ok

Ha a Microsoft figyelési ügynök Windows szolgáltatás nem fut, ez megakadályozza, hogy a hibrid forgatókönyv-feldolgozó Azure Automation kommunikál.

#### <a name="resolution"></a>Megoldás:

Ellenőrizze az ügynök fut-e a következő parancs beírásával PowerShell: `Get-Service healthservice`. Ha a szolgáltatás leáll, adja meg a következő parancsot a PowerShell elindítani a szolgáltatást: `Start-Service healthservice`.

###<a name="event-4502"></a> Az esemény 4502 Operations Manager

#### <a name="issue"></a>Probléma

Az a **alkalmazások és szolgáltatások Logs\Operations kezelője** Eseménynapló, látni esemény 4502 és EventMessage tartalmazó **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**következő leírást: *a szolgáltatás által bemutatott tanúsítványt \<wsid\>. oms.opinsights.azure.com nem a Microsoft-szolgáltatásokhoz használt hitelesítésszolgáltató állította ki. Lépjen kapcsolatba a hálózati rendszergazdával, ha a proxy, amely elfogja a TLS/SSL-kommunikáció futnak. A kb3126513 jelű további információkat talál a csatlakozási problémák.*

#### <a name="cause"></a>Ok

Ezt okozhatja a proxy vagy a hálózati tűzfal blokkolja a Microsoft Azure-kommunikációt. Ellenőrizze, hogy a számítógép *.azure-automation.net kimenő hozzáféréssel rendelkezzen a 443-as porton.

#### <a name="resolution"></a>Megoldás:

Naplók minden hibridfeldolgozó C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes, helyileg tárolja. Ellenőrizheti, hogy vannak-e bármilyen figyelmeztetés vagy írni hibaesemények a **alkalmazások és szolgáltatások Logs\Microsoft-SMA\Operations** és **alkalmazások és szolgáltatások Logs\Operations kezelője** esemény naplózása, amelyek azt jelzi, a kapcsolattal vagy más probléma érdekében, hogy a szerepkört az Azure Automation vagy probléma befolyásolja a normál műveletek során.

[Runbook kimenet és üzenetek](../automation-runbook-output-and-messages.md) az Azure Automation hibrid küldi munkavállalók hasonlóan a runbook-feladatok futtatása a felhőben. A részletes és az állapot adatfolyamokat a módon más runbookok is engedélyezheti.

## <a name="next-steps"></a>További lépések

Ha nem talál a problémát, vagy nem tudja megoldani a problémát, látogasson el a következő csatornák további támogatásért:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, az Azure támogatási incidens fájl is. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az beszerzése**.
