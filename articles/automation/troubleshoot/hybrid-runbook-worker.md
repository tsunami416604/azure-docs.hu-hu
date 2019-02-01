---
title: Hibaelhárítás – az Azure Automation hibrid Runbook-feldolgozók
description: Ez a cikk ismerteti az Azure Automation hibrid Runbook-feldolgozók hibaelhárítási információ
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 12/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a88e1fc5395a1b935371ae9d1eb24528a87b853
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512339"
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

Háromszor végrehajtásához megpróbál röviddel a runbook fel van függesztve. Nincsenek feltételek, amelyek megszakíthatja a runbook befejezését. Ha ez történik, a kapcsolódó hibaüzenet nem feltétlenül tartalmazzák a tájékoztatja, hogy miért további adatokat.

#### <a name="cause"></a>Ok

A lehetséges lehetséges okok a következők:

* A runbookok nem tudja hitelesíteni a helyi erőforrásokkal

* A hibrid feldolgozó proxy vagy tűzfal mögé van

* A runbookok nem tudja hitelesíteni a helyi erőforrásokkal

* A hibrid Runbook-feldolgozó szolgáltatás futtatásához konfigurált számítógép megfelel a minimális hardverkövetelményeknek.

#### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy a számítógép *.azure-automation.net kimenő hozzáféréssel rendelkezik a 443-as porton.

A hibrid Runbook-feldolgozó futtató számítógépek meg kell felelnie a minimális hardverkövetelményeknek előtt, ez a szolgáltatás gazdagépeként van konfigurálva. A Runbookok és a háttérben futó folyamatok, használják okozhat a rendszer túlterhelt legyen, és runbook-feladat késedelem és időtúllépéseket okoz.

Erősítse meg, amely a hibrid Runbook-feldolgozó szolgáltatás fogja futtatni a számítógép megfelel-e a minimális hardverkövetelményeknek. Ha igen, monitor CPU és memória segítségével meghatározhatja a hibrid Runbook-feldolgozó folyamat teljesítményét és a Windows bármely korrelációját. Ha nincs, memória és CPU nyomás, ez jelezheti erőforrások frissítése. Válassza ki a különböző számítási erőforrás által támogatható a minimális követelményeknek és a méretezési csoport, amikor terheléshez azt jelzik, hogy szükség a növekedést is.

Ellenőrizze a **Microsoft-SMA** eseménynaplót, amelyben egy megfelelő eseményt, leírás *Win32 folyamat kilépési kódja [4294967295]*. Ez a hiba oka még nincs konfigurálva a hitelesítést a runbookokban, vagy a hibrid feldolgozócsoport a futtató hitelesítő adatokat adott. Felülvizsgálat [Runbook-engedélyek](../automation-hrw-run-runbooks.md#runbook-permissions) , győződjön meg arról, hogy megfelelően konfigurálta hitelesítést a runbookokban.

### <a name="no-cert-found"></a>Forgatókönyv: Nem található tanúsítvány a tanúsítványtárolóban, a hibrid Runbook-feldolgozón

#### <a name="issue"></a>Probléma

Hibrid Runbook-feldolgozók futó runbook a következő hibaüzenettel meghiúsul:

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, próbálja meg használni, amikor egy [futtató fiók](../manage-runas-account.md) , amely egy hibrid Runbook-feldolgozó, ahol a futtató fiók tanúsítványa nem a jelenlegi futtat egy runbookban. Hibrid Runbook-feldolgozók nincs tanúsítványobjektum helyi alapértelmezetten, amelyek a futtató fiók megfelelő működéséhez szükségesek.

#### <a name="resolution"></a>Megoldás:

Ha egy Azure virtuális Gépen, a hibrid Runbook-feldolgozó használhatja [felügyelt identitások az Azure-erőforrások](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) helyette. Ebben a forgatókönyvben lehetővé teszi az Azure virtuális gép felügyelt identitás használata helyett a futtató fiók, leegyszerűsíti a hitelesítést az Azure erőforrásokban való hitelesítéshez. Ha egy helyszíni gépre, a hibrid Runbook-feldolgozó kell a futtató fiók tanúsítványának telepítése a gépre. A tanúsítvány telepítése című témakörben talál a lépések végrehajtásához a [Export-RunAsCertificateToHybridWorker](../automation-hrw-run-runbooks.md#runas-script) runbook.

## <a name="linux"></a>Linux

A hibrid Runbook-feldolgozója Linuxra attól függ, hogy az OMS-ügynök Linux kommunikálni az Automation-fiók regisztrálása a feldolgozó, runbook-feladatok fogadására és állapotát. Ha a feldolgozó regisztrációja meghiúsul, az alábbiakban néhány a hiba lehetséges okai:

### <a name="oms-agent-not-running"></a>Forgatókönyv: A Linuxhoz készült OMS-ügynök nem fut

#### <a name="issue"></a>Probléma

A Linuxhoz készült OMS-ügynök nem fut

#### <a name="cause"></a>Ok

Ha a Linuxhoz készült OMS-ügynök nem fut, megakadályozza, hogy a hibrid Runbook-feldolgozója Linuxra az Azure Automation szolgáltatással folytatott kommunikációját. Esetleg nem fut az ügynök különböző okok miatt.

#### <a name="resolution"></a>Megoldás:

 Ellenőrizze az ügynök fut-e a következő parancs beírásával: `ps -ef | grep python`. A python-folyamatok az alábbihoz hasonló kimenetnek kell megjelennie **nxautomation** felhasználói fiókot. Az Update Management vagy az Azure Automation-megoldások nem engedélyezett, ha nincs, az alábbi eljárások futnak.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Az alábbi lista tartalmazza a folyamatot, amelyet egy hibrid Runbook-feldolgozója Linuxra vonatkozó elindulnak. Ezek már az összes található a `/var/opt/microsoft/omsagent/state/automationworker/` könyvtár.


* **OMS.conf** -értéke a feldolgozófolyamat manager. Közvetlenül a DSC elindul.

* **Worker.conf** – Ez a folyamat automatikusan regisztrálva hibrid feldolgozói során a rendszer, a feldolgozó kezelő elindul. Ez a folyamat használja az Update Management és transzparens módon történik, a felhasználó számára. Ez a folyamat nincs jelen, ha az Update Management megoldás nincs engedélyezve a gépen.

* **diy/Worker.conf** – Ez a folyamat rendszer tanácsokat önálló hibrid feldolgozó folyamaton. A házi KÉSZÍTÉSŰ hibrid feldolgozói folyamat segítségével hajtható végre felhasználói runbookok a hibrid Runbook-feldolgozón. Csak eltér az automatikus regisztrált hibrid feldolgozói folyamat a fő részletesen, amely használja egy másik konfigurációs. Ez a folyamat nincs jelen, ha az Azure Automation-megoldás le van tiltva, és a házi KÉSZÍTÉSŰ Linux hibrid feldolgozó nincs regisztrálva.

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

Ha a Microsoft Monitoring Agent Windows szolgáltatás nem fut, ez az állapot megakadályozza, hogy a hibrid Runbook-feldolgozó az Azure Automation szolgáltatással folytatott kommunikációját.

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

