---
title: A Linuxos ügynök ellenőrzésének az eredménye az Azure az Update Management ismertetése
description: Ismerje meg, az Update Management ügynökkel kapcsolatos problémák elhárítása.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/14/2018
ms.topic: conceptual
ms.service: automation
ms.component: update-management
manager: carmonm
ms.openlocfilehash: 491f60b55843957bf9ec904f7310ef67219ba3c5
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438642"
---
# <a name="understand-the-linux-agent-check-results-in-update-management"></a>A Linuxos ügynök ellenőrzésének az eredménye az Update Management ismertetése

Előfordulhat, hogy a gép nem látható, számos oka lehet **készen** az Update Management. Az Update Management ellenőrizheti az határozza meg a hibát kiváltó problémát hibrid feldolgozó ügynök állapotát. Ez a cikk ismerteti az Azure-beli gépek hibaelhárító futtatása az Azure Portalon és a nem Azure-gépek a [offline forgatókönyv](#troubleshoot-offline).

## <a name="start-the-troubleshooter"></a>Indítsa el a hibaelhárító

Azure-beli gépek, kattintson a **hibaelhárítás** hivatkozásra a **frissítési ügynök készültsége** oszlopa a portál elindítja a **hibaelhárítása Windows Update Agent** lap. Nem Azure-gépek a hivatkozás teszi elérhetővé, az ebben a cikkben. Tekintse meg a [offline utasításokat](#offline) egy nem Azure-beli gép hibaelhárítása.

![virtuális gép listáját tartalmazó lapon](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Az ellenőrzések futtatását van szüksége. Ha a virtuális gép nem fut egy gombra kattintva megnyílik **indítsa el a virtuális Gépet**.

Az a **hibaelhárítása Windows Update Agent** kattintson **ellenőrzések futtatása**, a hibaelhárító elindításához. Használja a hibaelhárító [futtatása paranccsal](../../virtual-machines/linux/run-command.md) parancsfájl futtatása a függőségeket, amelyen az ügynök ellenőrzése a számítógépen. A hibaelhárító befejeződése után az ellenőrzések eredményét adja vissza.

![Hibaelhárítás lap](../media/update-agent-issues-linux/troubleshoot-page.png)

Amikor végzett, a rendszer visszairányítja az eredményeket az ablakban. A [ellenőrizze szakaszokat](#pre-requisistes-checks) információkat tartalmaz minden ellenőrzés keresi.

![Az ügynök ellenőrzése lap frissítése](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Előfeltétel-ellenőrzések

### <a name="operating-system"></a>Operációs rendszer

Az operációs rendszer ellenőrzés ellenőrzi, hogy ha a hibrid Runbook-feldolgozót a következő operációs rendszerek egyikét futtatja:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|CentOS 6 (x86/x64) és 7 (x64)      | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz. "Yum" besorolásalapú javítás szükséges, amely CentOS nem rendelkezik beépített biztonsági adatokat adja vissza.         |
|Red Hat Enterprise 6 (x86/x64) és 7 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|SUSE Linux Enterprise Server 11 (x86/x64) és 12 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|Ubuntu 14.04 LTS, 16.04 LTS és 18.04 LTS (x86/x64)      |A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.         |

## <a name="monitoring-agent-service-health-checks"></a>Figyelési ügynök szolgáltatás állapot-ellenőrzések

### <a name="oms-agent"></a>Az OMS-ügynök

Ez az ellenőrzés biztosítja, hogy a Linuxhoz készült OMS-ügynök telepítve van-e. Hogyan kell telepíteni, útmutatásért lásd: [a Linuxhoz készült ügynök telepítése](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="oms-agent-status"></a>Az OMS-ügynök állapota

Ez az ellenőrzés biztosítja, hogy a Linuxhoz készült OMS-ügynök fut-e. Ha az ügynök nem fut, próbálja meg újraindítani a következő parancsot futtathatja. Az ügynök hibaelhárítással kapcsolatos további információkért lásd: [Linux hibrid Runbook-feldolgozó hibaelhárítása](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Frissítéskor

Ez az ellenőrzés határozza meg, ha a ügynök több munkaterülethez is jelent. Többkiszolgálós nem támogatja az Update Management.

### <a name="hybrid-runbook-worker"></a>hibrid runbook-feldolgozó

Ez az ellenőrzés ellenőrzi, hogy rendelkezik-e a Linuxhoz készült OMS-ügynök a hibrid Runbook-feldolgozó csomag. Ez a csomag az Update Management működéséhez szükség.

### <a name="hybrid-runbook-worker-status"></a>A hibrid Runbook-feldolgozó állapota

Ez az ellenőrzés biztosítja, hogy a hibrid Runbook-feldolgozó fut a gépen. A következő folyamatok végrehajtásának kell van jelen, ha a hibrid Runbook-feldolgozó megfelelően működnek-e. További tudnivalókért lásd: [hibáinak elhárítása a Log Analytics-ügynök Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Kapcsolat ellenőrzése

### <a name="general-internet-connectivity"></a>Általános internetkapcsolat

Ez az ellenőrzés gondoskodik arról, hogy a gép rendelkezik internetkapcsolattal.

### <a name="registration-endpoint"></a>Regisztrációs végpont

Ez az ellenőrzés határozza meg, ha az ügynök megfelelően kommunikálhat az ügynök szolgáltatással.

Proxy és tűzfal-konfiguráció lehetővé teszi a hibrid Runbook-feldolgozó ügynök a regisztrációs végponttal való kommunikációhoz. Címek és portok megnyitásához listáját lásd: [hálózati hibrid feldolgozók tervezése](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Műveletek végpont

Ez az ellenőrzés határozza meg, ha az ügynök megfelelően kommunikál a feladat futásidejű adatok szolgáltatással.

Proxy és a tűzfal beállításait a hibrid Runbook-feldolgozó ügynök kommunikáljon a feladat modul Data Service lehetővé teszi. Címek és portok megnyitásához listáját lásd: [hálózati hibrid feldolgozók tervezése](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Log Analytics-végpont 1

Ellenőrzi, hogy a gép rendelkezik-e hozzáféréssel a végpontokra, szükség szerint a Log Analytics-ügynököket.

### <a name="log-analytics-endpoint-2"></a>Log Analytics-végpont 2

Ellenőrzi, hogy a gép rendelkezik-e hozzáféréssel a végpontokra, szükség szerint a Log Analytics-ügynököket.

### <a name="log-analytics-endpoint-3"></a>Log Analytics-végpont 3

Ellenőrzi, hogy a gép rendelkezik-e hozzáféréssel a végpontokra, szükség szerint a Log Analytics-ügynököket.

## <a name="troubleshoot-offline"></a>Kapcsolat nélküli hibaelhárítása

Használhatja a hibaelhárító offline egy hibrid Runbook-feldolgozón a szkript helyi futtatásával. A python-szkriptet [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) Script Centerben tekintheti meg. Ez a szkript a kimenetét egy példát a következő példában látható:

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

A hibrid Runbook-feldolgozók további problémák hibaelhárítása: [hibaelhárítás – a hibrid Runbook-feldolgozók](hybrid-runbook-worker.md)
