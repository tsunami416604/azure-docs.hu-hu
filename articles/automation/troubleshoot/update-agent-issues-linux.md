---
title: A Linux frissítési ügynökkel kapcsolatos problémák elhárítása Azure Automation Update Management
description: Megtudhatja, hogyan oldhatja fel a Linux Windows Update agenttel kapcsolatos problémákat a Update Management megoldás használatával.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: bba1c7e89a9c3bb1c9aa1567e36dd71a40f14636
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679061"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>A Linux frissítési ügynökkel kapcsolatos problémák elhárítása

Számos oka lehet annak, hogy a gép nem jelenik meg készenléti (kifogástalan) állapotban a Update Management. Update Management a hibrid Runbook Worker Agent állapotának ellenőrzését a probléma okának megállapításához. Ez a cikk azt ismerteti, hogyan futtathatja az Azure-gépekhez tartozó hibakeresőt a Azure Portal és a nem Azure-beli gépekről az [Offline forgatókönyvben](#troubleshoot-offline). 

A következő lista az alábbi három felkészültségi állapotot tartalmazza:

* Kész – a hibrid Runbook Worker üzembe helyezése megtörtént, és legalább 1 órával ezelőtt volt látható.
* Leválasztva – a hibrid Runbook Worker üzembe helyezése megtörtént, és a rendszer az elmúlt 1 órája óta utoljára látott el.
* Nincs konfigurálva – a hibrid Runbook Worker nem található vagy nem fejeződött be.

> [!NOTE]
> A Azure Portal megjelenítése és a gép aktuális állapota között enyhe késés adható meg.

## <a name="start-the-troubleshooter"></a>A hibakereső elindítása

Az Azure-gépek esetében a portál **frissítési ügynök készültsége** oszlopának **hibakeresés** hivatkozására kattintva elindítja az ügynök frissítése lapot. A nem Azure-beli gépek esetében a hivatkozás a jelen cikkre mutat. A nem Azure-beli gépek hibáinak megoldásához tekintse meg az offline utasításokat.

![Virtuálisgép-lista lapja](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Az ellenőrzésekhez szükség van a virtuális gép futtatására. Ha a virtuális gép nem fut, a **virtuális gép indítása** gombra kattintva jelenik meg.

Az ügynök frissítése lapon kattintson az **ellenőrzések futtatása**gombra a hibakereső elindításához. A hibakereső a [Futtatás parancs](../../virtual-machines/linux/run-command.md) használatával futtat egy parancsfájlt a gépen a függőségek ellenőrzéséhez. A hibakereső befejezése után visszaadja az ellenőrzések eredményét.

![Hibakeresési oldal](../media/update-agent-issues-linux/troubleshoot-page.png)

Ha elkészült, a rendszer az eredményeket az ablakban adja vissza. Az ellenőrzési szakaszokból megtudhatja, hogy mit keres az egyes ellenőrzések.

![Ügynök-ellenőrzési oldal frissítése](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Előfeltétel-ellenőrzések

### <a name="operating-system"></a>Operációs rendszer

Az operációs rendszer ellenőrzése ellenőrzi, hogy a hibrid Runbook-feldolgozó a következő operációs rendszerek egyikét futtatja-e:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|CentOS 6 (x86/x64) és 7 (x64)      | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz. A besoroláson alapuló javításhoz a "yum" utasítás szükséges ahhoz, hogy a CentOS ne legyen a dobozban.         |
|Red Hat Enterprise 6 (x86/x64) és 7 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|SUSE Linux Enterprise Server 11 (x86/x64) és 12 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|Ubuntu 14,04 LTS, 16,04 LTS és 18,04 LTS (x86/x64)      |A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.         |

## <a name="monitoring-agent-service-health-checks"></a>Figyelési ügynök szolgáltatás állapotának ellenőrzése

### <a name="log-analytics-agent"></a>Log Analytics-ügynök

Ez az ellenőrzési funkció biztosítja, hogy a Linux rendszerhez készült Log Analytics-ügynök telepítve legyen. A telepítésével kapcsolatos útmutatásért lásd: [a Linux-ügynök telepítése](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Log Analytics ügynök állapota

Ez az ellenőrzési funkció biztosítja, hogy a Linux Log Analytics ügynöke fusson. Ha az ügynök nem fut, akkor a következő parancs futtatásával próbálja meg újraindítani. Az ügynök hibaelhárításával kapcsolatos további információkért lásd: [Linux Hybrid Runbook Worker – hibaelhárítás](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Többhelyű

Ez az érték határozza meg, hogy az ügynök több munkaterületnek jelent-e jelentést. Update Management nem támogatja a többszörös vezérlést.

### <a name="hybrid-runbook-worker"></a>hibrid runbook-feldolgozó

Ez az ellenőrzés ellenőrzi, hogy a Linux Log Analytics-ügynöke a hibrid Runbook Worker csomaggal rendelkezik-e. Ez a csomag a Update Management működéséhez szükséges.

### <a name="hybrid-runbook-worker-status"></a>Hibrid Runbook Worker állapota

Ez az ellenőrzés ellenőrzi, hogy a hibrid Runbook Worker fut-e a gépen. Ha a hibrid Runbook Worker megfelelően fut, a következő folyamatoknak kell szerepelniük. További információ: [a Linux rendszerhez készült log Analytics-ügynök hibaelhárítása](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Kapcsolatok ellenőrzése

### <a name="general-internet-connectivity"></a>Általános internetkapcsolat

Ez az ellenőrzés biztosítja, hogy a számítógép hozzáférjen az internethez.

### <a name="registration-endpoint"></a>Regisztrációs végpont

Ez az érték határozza meg, hogy a hibrid Runbook-feldolgozó megfelelően tud-e kommunikálni Azure Automation a Log Analytics munkaterülettel.

A proxy és a tűzfal konfigurációjának lehetővé kell tennie, hogy a hibrid Runbook Worker ügynök kommunikáljon a regisztrációs végponttal. A megnyitni kívánt címek és portok listáját lásd: a [hibrid feldolgozók hálózati tervezése](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Műveleti végpont

Ez az érték határozza meg, hogy az ügynök megfelelően tud-e kommunikálni a feladatütemezés adatszolgáltatásával.

A proxy és a tűzfal konfigurációjának lehetővé kell tennie, hogy a hibrid Runbook-feldolgozó ügynök kommunikáljon a feladatütemezés adatszolgáltatásával. A megnyitni kívánt címek és portok listáját lásd: a [hibrid feldolgozók hálózati tervezése](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Log Analytics 1. végpont

Ez az ellenőrzés ellenőrzi, hogy a számítógép rendelkezik-e hozzáféréssel a Log Analytics ügynök által igényelt végpontokhoz.

### <a name="log-analytics-endpoint-2"></a>Log Analytics 2. végpont

Ez az ellenőrzés ellenőrzi, hogy a számítógép rendelkezik-e hozzáféréssel a Log Analytics ügynök által igényelt végpontokhoz.

### <a name="log-analytics-endpoint-3"></a>Log Analytics 3. végpont

Ez az ellenőrzés ellenőrzi, hogy a számítógép rendelkezik-e hozzáféréssel a Log Analytics ügynök által igényelt végpontokhoz.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Offline hibák

A hibakeresőt offline módon használhatja hibrid Runbook-feldolgozón a parancsfájl helyi futtatásával. A [update_mgmt_health_check.](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) a Python-szkript a script Centerben található. A szkript kimenetének példája az alábbi példában látható:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>További lépések

A hibrid Runbook-feldolgozókkal kapcsolatos további problémák elhárításához lásd: a [hibrid Runbook-feldolgozók hibaelhárítása](hybrid-runbook-worker.md).
