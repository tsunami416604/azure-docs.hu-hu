---
title: Linux-hibrid runbook-feldolgozó diagnosztizálása – Azure Update Management
description: Ismerje meg, hogyan háríthatja el és oldhatja meg a linuxos Azure Automation hibrid runbook-feldolgozóval kapcsolatos problémákat, amelyek támogatják az Update Management szolgáltatást.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: e60ba71607b99f0ea97e0725ffdd0740f3e9c579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278297"
---
# <a name="understand-and-resolve-linux-hybrid-runbook-worker-health-for-update-management"></a>A Linux-hibrid runbook-feldolgozó állapotának ismertetése és megoldása a frissítéskezeléshez

Számos oka lehet annak, hogy a számítógép nem jelenik **meg a Ready** in Update Management szolgáltatásban. Az Update Management, ellenőrizheti a hibrid Runbook-feldolgozó ügynök állapotát az alapul szolgáló probléma meghatározásához. Ez a cikk bemutatja, hogyan futtathatja a hibaelhárító az Azure-gépek az Azure Portalon és a nem Azure-gépek az [offline forgatókönyvben.](#troubleshoot-offline)

Az alábbi lista a három készenléti állapotok egy gép lehet:

* **Ready** – A hibrid Runbook-feldolgozó telepítve van, és kevesebb mint 1 órával ezelőtt látták utoljára.
* **Leválasztva** – A hibrid Runbook-feldolgozó telepítve van, és utoljára 1 órával ezelőtt látták.
* **Nincs konfigurálva** – a hibrid Runbook-feldolgozó nem található, vagy nem fejeződött be a bevezetés.

> [!NOTE]
> Előfordulhat, hogy az Azure Portal on-t és a gép aktuális állapotát az Azure Portal megjelenítése között kis késés.

## <a name="start-the-troubleshooter"></a>A hibaelhárító indítása

Azure-gépek esetén a portál **frissítési ügynök-készenléte** oszlopában található **Hibaelhárítás** hivatkozásra kattintva elindul a **Frissítési ügynök hibaelhárítási ügynök e-hibaelhárítása** lap. Nem Azure-alapú gépek esetén a hivatkozás ebben a cikkben található. Tekintse meg az offline utasításokat egy nem Azure-gép hibaelhárításához.

![vm listalap](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Az ellenőrzések megkövetelik, hogy a virtuális gép fut. Ha a virtuális gép nem fut, megjelenik egy gomb **a virtuális gép elindításához.**

A **Hibaelhárítási ügynök** lapon kattintson az **Ellenőrzések futtatása**gombra a hibaelhárító elindításához. A hibaelhárító a [Futtatás paranccsal](../../virtual-machines/linux/run-command.md) futtat egy parancsfájlt a számítógépen a függőségek ellenőrzéséhez. Amikor a hibaelhárító befejeződött, az ellenőrzések eredményét adja vissza.

![Lap – hibaelhárítás](../media/update-agent-issues-linux/troubleshoot-page.png)

Ha elkészült, az eredmények az ablakban jelennek meg. Az ellenőrzési szakaszok tájékoztatást nyújtanak arról, hogy mit keresnek az egyes csekkek.

![Ügyintéző-ellenőrzések frissítése lap](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Előfeltétel-ellenőrzések

### <a name="operating-system"></a>Operációs rendszer

Az operációs rendszer ellenőrzése ellenőrzi, hogy a hibrid Runbook-feldolgozó az alábbi operációs rendszerek valamelyikét futtatja-e:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|CentOS 6 (x86/x64) és 7 (x64)      | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz. A besorolás-alapú javításhoz "yum"-ra van szükség a biztonsági adatok visszaadásához, amelyeket a CentOS nem rendelkezik a dobozból.         |
|Red Hat Enterprise 6 (x86/x64) és 7 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|SUSE Linux Enterprise Server 11 (x86/x64) és 12 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|Ubuntu 14.04 LTS, 16.04 LTS és 18.04 LTS (x86/x64)      |A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.         |

## <a name="monitoring-agent-service-health-checks"></a>Figyelőügynök-szolgáltatás állapotellenőrzése

### <a name="log-analytics-agent"></a>Log Analytics-ügynök

Ez az ellenőrzés biztosítja, hogy a Log Analytics-ügynök Linux telepítve van. A telepítésmódjáról az [Ügynök telepítése Linuxra](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
)című témakörben talál.

### <a name="log-analytics-agent-status"></a>Log Analytics-ügynök állapota

Ez az ellenőrzés biztosítja, hogy a Log Analytics-ügynök Linux fut. Ha az ügynök nem fut, a következő paranccsal megpróbálhatja újraindítani. Az ügynök hibaelhárításával kapcsolatos további információkért lásd: [Linux Hybrid Runbook worker troubleshooting](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Többhoming

Ez a ellenőrzés azt határozza meg, hogy az ügynök több munkaterületnek jelent-e. Az Update Management nem támogatja a több-homing szolgáltatást.

### <a name="hybrid-runbook-worker"></a>hibrid runbook-feldolgozó

Ez az ellenőrzés ellenőrzi, hogy a Linux-alapú Log Analytics-ügynök rendelkezik-e a hibrid Runbook-feldolgozó csomaggal. Ez a csomag szükséges az Update Management működéséhez.

### <a name="hybrid-runbook-worker-status"></a>Hibrid Runbook-feldolgozó állapota

Ez az ellenőrzés biztosítja, hogy a hibrid Runbook-feldolgozó fut a számítógépen. A következő folyamatoknak kell lenniük, ha a hibrid Runbook-feldolgozó megfelelően fut. További információ: [A Linuxos Log Analytics-ügynök hibaelhárítása.](hybrid-runbook-worker.md#oms-agent-not-running)

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Kapcsolódási ellenőrzések

### <a name="general-internet-connectivity"></a>Általános internetkapcsolat

Ez az ellenőrzés biztosítja, hogy a készülék hozzáférhessen az internethez.

### <a name="registration-endpoint"></a>Regisztrációs végpont

Ez az ellenőrzés határozza meg, hogy a hibrid runbook-feldolgozó megfelelően kommunikálhat-e az Azure Automation a Log Analytics-munkaterülettel.

A proxy- és tűzfal-konfigurációknak lehetővé kell tenniük, hogy a hibrid Runbook-feldolgozó ügynök kommunikáljon a regisztrációs végponttal. A megnyitandó címek és portok listáját a [Hibrid dolgozók hálózattervezése](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Műveleti végpont

Ez az ellenőrzés határozza meg, hogy az ügynök képes-e megfelelően kommunikálni a feladatfutásidejű adatszolgáltatással.

A proxy- és tűzfal-konfigurációknak lehetővé kell tenniük, hogy a hibrid Runbook-feldolgozó ügynök kommunikáljon a feladatfutásidejű adatszolgáltatással. A megnyitandó címek és portok listáját a [Hibrid dolgozók hálózattervezése](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Log Analytics-végpont 1

Ez az ellenőrzés ellenőrzi, hogy a számítógép rendelkezik-e a Log Analytics-ügynök által szükséges végpontokhoz.

### <a name="log-analytics-endpoint-2"></a>2. loganalytics-végpont

Ez az ellenőrzés ellenőrzi, hogy a számítógép rendelkezik-e a Log Analytics-ügynök által szükséges végpontokhoz.

### <a name="log-analytics-endpoint-3"></a>Log Analytics-végpont 3

Ez az ellenőrzés ellenőrzi, hogy a számítógép rendelkezik-e a Log Analytics-ügynök által szükséges végpontokhoz.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Kapcsolat nélküli kapcsolat nélküli – problémamegoldás

A hibaelhárító offline használhatja a hibrid Runbook-feldolgozón a parancsfájl helyi futtatásával. A python script, [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) megtalálható a Script Center. A parancsfájl kimenetének egy példája a következő példában látható:

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

A hibrid runbook-feldolgozókkal kapcsolatos további problémák elhárításához olvassa el a [Hibrid Runbook-dolgozók hibaelhárítása című témakört.](hybrid-runbook-worker.md)
