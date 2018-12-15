---
title: Hibaelhárítás – az Azure Automation hibrid Runbook-feldolgozók
description: Ez a cikk ismerteti az Azure Automation hibrid Runbook-feldolgozók hibaelhárítási információ
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a95c9f1edd6983c915316f2900885a8131245860
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437833"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Hibrid Runbook-feldolgozók hibaelhárítása

Ez a cikk a hibrid Runbook-feldolgozók elhárításának információkat biztosít.

## <a name="general"></a>Általános kérdések

A hibrid Runbook-feldolgozó attól függ, hogy az ügynök kommunikálni az Automation-fiók regisztrálása a feldolgozó, runbook-feladatok fogadására és állapotát. A Windows az ügynök a Microsoft Monitoring Agent. Linux esetén a Linuxhoz készült OMS-ügynök.

### <a name="runbook-execution-fails"></a>Forgatókönyv: Runbook végrehajtása sikertelen

#### <a name="issue"></a>Probléma

Runbook végrehajtása sikertelen, és a következő hibaüzenetet kapja:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

A runbook röviddel utána kísérel meg végrehajtani, három alkalommal fel van függesztve. Nincsenek feltételek, amelyek megszakíthatja a runbook sikeresen elvégezte a, és a kapcsolódó hibaüzenet nem tartalmazza a minden további információt, amely jelzi, hogy miért.

#### <a name="cause"></a>Ok

A lehetséges lehetséges okok a következők:

* A runbookok nem tudja hitelesíteni a helyi erőforrásokkal

* A hibrid feldolgozó proxy vagy tűzfal mögé van

* A runbookok nem tudja hitelesíteni a helyi erőforrásokkal

* A hibrid Runbook-feldolgozó szolgáltatás futtatására kijelölt számítógép megfelel a minimális hardverkövetelményeknek.

#### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy a számítógép *.azure-automation.net kimenő hozzáféréssel rendelkezik a 443-as porton.

A hibrid Runbook-feldolgozó futtató számítógépek meg kell felelnie a minimális hardverkövetelményeknek, hogy ez a szolgáltatás üzemeltetéséhez kijelölése előtt. Ellenkező esetben az függően más háttérfolyamatok és a versengés során a runbook végrehajtása is az erőforrás-használat oka a számítógép túlterhelt válik, és runbook-feladat késedelem és időtúllépéseket okoz.

Győződjön meg arról, a hibrid Runbook-feldolgozó szolgáltatás futtatására kijelölt számítógép megfelel a minimális hardverkövetelményeknek. Ha igen, figyelheti a CPU és memória kihasználtságáról, a hibrid Runbook-feldolgozó folyamat teljesítményét és a Windows bármely korrelációját meghatározásához. Ha nincs, memória és CPU nyomás, ez jelezheti frissítése vagy további processzorok hozzáadása, vagy növelje a memória oldja meg az erőforrás szűk keresztmetszetet, és hárítsa el a hibát. Azt is megteheti válassza ki a különböző számítási erőforrás által támogatható a minimális követelményeknek és a méretezési csoport, amikor terheléshez azt jelzik, hogy megnöveltük szükség.

Ellenőrizze a **Microsoft-SMA** eseménynaplót, amelyben egy megfelelő eseményt, leírás *Win32 folyamat kilépési kódja [4294967295]*. Ez a hiba oka még nincs konfigurálva a hitelesítést a runbookokban, vagy a hibrid feldolgozócsoport a futtató hitelesítő adatokat adott. Felülvizsgálat [Runbook-engedélyek](../automation-hrw-run-runbooks.md#runbook-permissions) , győződjön meg arról, hogy megfelelően konfigurálta hitelesítést a runbookokban.

## <a name="linux"></a>Linux

A hibrid Runbook-feldolgozója Linuxra attól függ, hogy az OMS-ügynök Linux kommunikálni az Automation-fiók regisztrálása a feldolgozó, runbook-feladatok fogadására és állapotát. Ha a feldolgozó regisztrációja meghiúsul, az alábbiakban néhány a hiba lehetséges okai:

### <a name="oms-agent-not-running"></a>Forgatókönyv: A Linuxhoz készült OMS-ügynök nem fut

Ha a Linuxhoz készült OMS-ügynök nem fut, a hibrid Runbook-feldolgozója Linuxra Ez megakadályozza az Azure Automation szolgáltatással folytatott kommunikációját. Ellenőrizze az ügynök fut-e a következő parancs beírásával: `ps -ef | grep python`. A python-folyamatok az alábbihoz hasonló kimenetnek kell megjelennie **nxautomation** felhasználói fiókot. Az Update Management vagy az Azure Automation-megoldások nem engedélyezett, ha nincs, az alábbi eljárások futnak.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Az alábbi lista tartalmazza a folyamatot, amelyet egy hibrid Runbook-feldolgozója Linuxra vonatkozó elindulnak. Ezek már az összes található a `/var/opt/microsoft/omsagent/state/automationworker/` könyvtár.

* **OMS.conf** – Ez a folyamat a feldolgozófolyamat manager, a DSC-ről Ez a folyamat elindul.

* **Worker.conf** – Ez a folyamat automatikusan regisztrálva hibrid feldolgozói során a rendszer, a feldolgozó kezelő elindul. Ez a folyamat használja az Update Management és transzparens módon történik, a felhasználó számára. Ez a folyamat nincs jelen, ha az Update Management megoldás nincs engedélyezve a gépen.

* **diy/Worker.conf** – Ez a folyamat rendszer tanácsokat önálló hibrid feldolgozó folyamaton. A házi KÉSZÍTÉSŰ hibrid feldolgozói folyamat segítségével hajtható végre felhasználói runbookok a hibrid Runbook-feldolgozón. Csak eltér az automatikus regisztrált hibrid feldolgozói folyamat a fő részletesen, amely használja egy másik konfigurációs. Ez a folyamat nincs jelen, ha az Azure Automation-megoldás nincs engedélyezve, és a házi KÉSZÍTÉSŰ Linux hibrid feldolgozó nincs regisztrálva.

Ha az OMS-ügynököt a Linux nem fut, futtassa a következő parancsot a szolgáltatás elindítása: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Forgatókönyv: A megadott osztály nem létezik.

Ha a következő hibát látja: **Nem létezik a megadott osztály...** az a `/var/opt/microsoft/omsconfig/omsconfig.log` majd frissíteni kell a Linuxhoz készült OMS-ügynök. Futtassa újra kell telepítenie az OMS-ügynök a következő parancsot:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

A Windows a hibrid Runbook-feldolgozó attól függ, hogy a Microsoft Monitoring Agent kommunikálni az Automation-fiók regisztrálása a feldolgozó, runbook-feladatok fogadására és állapotát. Ha a feldolgozó regisztrációja meghiúsul, az alábbiakban néhány a hiba lehetséges okai:

### <a name="mma-not-running"></a>Forgatókönyv: A Microsoft Monitoring Agent nem fut

#### <a name="issue"></a>Probléma

A `healthservice` szolgáltatás nem fut, a hibrid Runbook-feldolgozó gépen.

#### <a name="cause"></a>Ok

Ha a Microsoft Monitoring Agent Windows szolgáltatás nem fut, ez a forgatókönyv megakadályozza, hogy a hibrid Runbook-feldolgozó az Azure Automation szolgáltatással folytatott kommunikációját.

#### <a name="resolution"></a>Megoldás:

Ellenőrizze az ügynök fut-e a PowerShellben a következő parancs beírásával: `Get-Service healthservice`. Ha a szolgáltatás leáll, adja meg a következő parancsot a PowerShellben a szolgáltatás elindítása: `Start-Service healthservice`.

### <a name="event-4502"></a> Az esemény 4502 az Operations Manager

#### <a name="issue"></a>Probléma

Az a **alkalmazás- és Services Logs\Operations Manager** eseménynaplóban, látni esemény 4502 és tartalmazó EventMessage **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**a következő leírással: *A szolgáltatás által bemutatott tanúsítványt \<wsid\>. oms.opinsights.azure.com nem Microsoft-szolgáltatásokhoz használt hitelesítésszolgáltató állította ki. Lépjen kapcsolatba a hálózati rendszergazdától, ha még futnak a TLS/SSL-kommunikációt elfogó proxy. A kb3126513 jelű további információkat talál a kapcsolódási problémák.*

#### <a name="cause"></a>Ok

Ezt a problémát okozhatja a proxy vagy a hálózati tűzfal blokkolja a kommunikációt a Microsoft Azure-bA. Ellenőrizze, hogy a számítógép *.azure-automation.net kimenő hozzáféréssel rendelkezik a 443-as porton.

#### <a name="resolution"></a>Megoldás:

Naplók rendszer helyben tárolja, C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes minden hibrid feldolgozón. Ellenőrizheti, hogy vannak-e bármilyen figyelmeztetés vagy hibaüzenet események a **alkalmazások és szolgáltatások Logs\Microsoft-SMA\Operations** és **alkalmazás- és Services Logs\Operations Manager** , mintha Eseménynapló csatlakozási vagy más típusú problémával, amely hatással van a szerepkör az Azure Automationhöz bevezetése vagy a szokásos működés problémát jelez.

[A Runbook kimeneti és az üzenetek](../automation-runbook-output-and-messages.md) küldi az Azure Automation hibrid feldolgozók ugyanúgy, mint a runbook-feladatok, amelyek futtatását a felhőben. A részletes és az állapot adatfolyamok más forgatókönyvek esetében tenné ugyanúgy is engedélyezheti.

### <a name="corrupt-cache"></a> Hibrid Runbook-feldolgozó nem küld

#### <a name="issue"></a>Probléma

A hibrid Runbook-feldolgozó gép fut, de nem látja a szívverési adatok a munkaterületen a gépet.

A következő példalekérdezés bemutatja a gépek egy munkaterületet, és azok legutóbbi szívverés:

```loganalytics
// Last heartbeat of each computer
Heartbeat 
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Ok

A probléma okozhatja egy sérült gyorsítótár, a hibrid Runbook-feldolgozón.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához jelentkezzen be a hibrid Runbook-feldolgozó, és futtassa a következő szkriptet. Ez a szkript nem a Microsoft Monitoring Agent, eltávolítja a gyorsítótárban, és újraindítja a szolgáltatást. Ez a művelet újbóli letöltéséhez a konfigurációját az Azure Automation hibrid Runbook-feldolgozó kényszeríti.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.
