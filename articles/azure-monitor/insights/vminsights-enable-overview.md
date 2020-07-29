---
title: Azure Monitor for VMs-áttekintés engedélyezése
description: Megtudhatja, hogyan telepítheti és konfigurálhatja a Azure Monitor for VMs. Ismerje meg a rendszerkövetelményeket.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 96783955eac6ade90a155236891307720616ed20
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323944"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Azure Monitor for VMs-áttekintés engedélyezése

Ez a cikk áttekintést nyújt azokról a lehetőségekről, amelyek lehetővé teszik a Azure Monitor for VMs számára a következők állapotának és teljesítményének figyelését:

- Azure-beli virtuális gépek 
- Azure-beli virtuálisgép-méretezési csoportok
- Az Azure arc-hoz csatlakozó hibrid virtuális gépek
- Helyszíni virtuális gépek
- Egy másik felhőalapú környezetben üzemeltetett virtuális gépek.  

Azure Monitor for VMs beállítása:

* Egyetlen Azure-beli virtuális gép, Azure-VMSS vagy Azure arc-gép engedélyezéséhez válassza ki az **áttekintéseket** közvetlenül a Azure Portal menüjéből.
* A Azure Policy használatával több Azure-beli virtuális gép, Azure VMSS vagy Azure arc-számítógép is engedélyezhető. Ez a módszer biztosítja, hogy a meglévő és az új virtuális gépeken és a méretezési csoportokon a szükséges függőségek telepítve és megfelelően legyenek konfigurálva. A rendszer nem megfelelő virtuális gépeket és méretezési csoportokat jelentett, így eldöntheti, hogy engedélyezi-e őket, és hogyan javíthatja őket.
* A PowerShell használatával több Azure-beli virtuális gép, Azure arc virtuális gép, Azure-VMSS vagy Azure-beli ív-számítógép is engedélyezhető egy adott előfizetésben vagy erőforráscsoporthoz.
* Engedélyezze Azure Monitor for VMs a vállalati hálózaton vagy más felhőalapú környezetben üzemeltetett virtuális gépek vagy fizikai számítógépek figyelését.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg róla, hogy tisztában van az alábbi részben található információkkal. 

>[!NOTE]
>Az ebben a szakaszban ismertetett információk a [Service Map megoldásra](service-map.md)is érvényesek.  

### <a name="log-analytics"></a>Log Analytics

A Azure Monitor for VMs a következő régiókban támogatja a Log Analytics munkaterületet:

- USA nyugati középső régiója
- USA nyugati régiója
- USA 2. nyugati régiója
- USA déli középső régiója
- USA keleti régiója
- USA 2. keleti régiója
- USA középső régiója
- USA északi középső régiója
- US Gov az
- US Gov VA
- Közép-Kanada
- Az Egyesült Királyság déli régiója
- Észak-Európa
- Nyugat-Európa
- Kelet-Ázsia
- Délkelet-Ázsia
- Közép-India
- Kelet-Japán
- Kelet-Ausztrália
- Délkelet-Ausztrália

>[!NOTE]
>Bármelyik régióban nyomon követheti az Azure-beli virtuális gépeket. Maguk a virtuális gépek nem korlátozódnak a Log Analytics munkaterület által támogatott régiókra.
>

Ha nem rendelkezik Log Analytics munkaterülettel, létrehozhat egyet az egyik erőforrás használatával:
* [Azure CLI](../learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Portal](../learn/quick-create-workspace.md)
* [Azure Resource Manager](../platform/template-workspace-configuration.md)

- Azure virtuális gép
- Azure virtuálisgép-méretezési csoport
- Az Azure arc-hoz csatlakoztatott hibrid virtuális gép

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A következő táblázat felsorolja a Azure Monitor for VMs által támogatott Windows-és Linux-operációs rendszereket. A szakasz későbbi részében egy teljes listát talál, amely részletesen ismerteti a Linux operációs rendszer kiadásának és a támogatott kernelnek a verziószámát.

|Operációs rendszer verziója |Teljesítmény |Maps |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18,04, 16,04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9,4, 8 | X<sup>1</sup> | |

<sup>1</sup> a Azure monitor for VMS teljesítménybeli funkciója csak Azure monitor érhető el. Nem érhető el közvetlenül az Azure-beli virtuális gép bal oldali paneljéről.

>[!NOTE]
>A Linux operációs rendszerben:
> - Csak az alapértelmezett és az SMP Linux kernelű kiadások támogatottak.
> - A nem szabványos kernel-kiadások (például a fizikai címkiterjesztés (PAE) és a Xen) nem támogatottak a Linux-disztribúciók esetében. Például a *2.6.16.21-0,8-Xen* kiadási karakterlánccal rendelkező rendszer nem támogatott.
> - Az egyéni kernelek, beleértve a standard kernelek újrafordítását, nem támogatottak.
> - A CentOSPlus kernel támogatott.
> - A Linux-kernelt a fantom biztonsági rések számára kell javítani. További részletekért tekintse meg a Linux-disztribúció gyártóját.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 7.6 | 3.10.0 – 957 |
| 7,5 | 3.10.0 – 862 |
| 7.4 | 3.10.0 – 693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6,10 | 2.6.32 – 754 |
| 6.9 | 2.6.32 – 696 |

#### <a name="centosplus"></a>CentOSPlus

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6,10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 18,04 | 5.3.0 – 1020<br>5,0 (tartalmazza az Azure által hangolt kernelt)<br>4,18* <br> 4,15* |
| 16.04.3 | 4,15. * |
| 16,04 | 4,13.\*<br>4,11.\*<br>4,10.\*<br>4,8.\*<br>4,4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
|12 SP4 CSOMAG | 4,12. * (tartalmazza az Azure által hangolt kernelt) |
|12 SP3 | 4,4. * |
|12 SP2 | 4,4. * |

#### <a name="debian"></a>Debian 

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 9 | 4,9 | 

## <a name="supported-azure-arc-machines"></a>Támogatott Azure arc-gépek
Azure Monitor for VMs elérhető az Azure arc-kompatibilis kiszolgálókhoz olyan régiókban, amelyeken elérhető az ív-bővítmény szolgáltatás. Az ív-ügynök 0,9-es vagy újabb verzióját kell futtatnia.

| Csatlakoztatott forrás | Támogatott | Leírás |
|:--|:--|:--|
| Windows-ügynökök | Igen | A Windows [log Analytics ügynökével](../platform/log-analytics-agent.md)együtt a Windows-ügynököknek szüksége van a függőségi ügynökre. További információ: [támogatott operációs rendszerek](#supported-operating-systems). |
| Linux-ügynökök | Igen | A [Linux rendszerhez készült log Analytics-ügynökkel](../platform/log-analytics-agent.md)együtt a Linux-ügynököknek szüksége van a függőségi ügynökre. További információ: [támogatott operációs rendszerek](#supported-operating-systems). |
| System Center Operations Manage felügyeleti csoport | Nem | |

## <a name="agents"></a>Ügynökök
Azure Monitor for VMs a következő két ügynököt kell telepíteni az egyes virtuális gépekre vagy virtuálisgép-méretezési csoportokra. Ezeknek az ügynököknek a telepítése és a munkaterülethez való csatlakoztatása az egyetlen követelmény az erőforrás bevezetéséhez.

- [Log Analytics ügynök](../platform/log-analytics-agent.md). Eseményeket és teljesítményadatokat gyűjt a virtuális gépről vagy virtuálisgép-méretezési csoportból, és továbbítja azt a Log Analytics munkaterületre. Az Azure-erőforrások Log Analytics ügynökének üzembe helyezési módszerei a Windows és [Linux](../../virtual-machines/extensions/oms-linux.md) [rendszerhez](../../virtual-machines/extensions/oms-windows.md) készült virtuálisgép-bővítményt használják.
- Függőségi ügynök. Gyűjti a felderített adatokat a virtuális gépen és a külső folyamatok függőségein futó folyamatokról, amelyeket az [Azure monitor for VMS Térkép funkciója](vminsights-maps.md)használ. A függőségi ügynök a Log Analytics ügynökre támaszkodik, hogy az adatAzure Monitorba kézbesítse az adatforrást. Az Azure-erőforrások függőségi ügynökének üzembe helyezési módszerei a Windows és [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) [rendszerhez](../../virtual-machines/extensions/agent-dependency-windows.md) készült virtuálisgép-bővítményt használják.

> [!NOTE]
> A Log Analytics ügynök ugyanaz az ügynök, amelyet a System Center Operations Manager használ. A Azure Monitor for VMs felügyelheti a Operations Manager által is figyelt ügynököket, ha azokat közvetlenül csatlakoztatják, és telepítik a függőségi ügynököt. A [felügyeleti csoport kapcsolatain](../tform/../platform/om-agents.md) keresztül Azure monitorhoz csatlakozó ügynököket Azure monitor for VMS nem lehet figyelni.

Az alábbi módszerek több módszert is biztosítanak ezeknek az ügynököknek a üzembe helyezésére. 

| Metódus | Leírás |
|:---|:---|
| [Azure Portal](vminsights-enable-single-vm.md) | Telepítsen mindkét ügynököt egyetlen virtuális gépre, virtuálisgép-méretezési csoportra vagy az Azure arc-hoz csatlakoztatott hibrid virtuális gépekre. |
| [Resource Manager-sablonok](vminsights-enable-powershell.md) | Telepítse mindkét ügynököt a támogatott módszerek bármelyikével egy Resource Manager-sablon üzembe helyezéséhez, beleértve a CLI-t és a PowerShellt is. |
| [Azure Policy](vminsights-enable-at-scale-policy.md) | Azure Policy-kezdeményezés hozzárendelésével automatikusan telepítheti az ügynököket virtuális gép vagy virtuálisgép-méretezési csoport létrehozásakor. |
| [Manuális telepítés](vminsights-enable-hybrid-cloud.md) | Telepítse az ügynököket a vendég operációs rendszerbe az Azure-on kívül üzemeltetett számítógépeken, például az adatközpontban vagy más felhőalapú környezetekben. |




## <a name="management-packs"></a>Felügyeleti csomagok
Ha egy Log Analytics munkaterületen Azure Monitor for VMs van konfigurálva, a rendszer két felügyeleti csomagot továbbít az adott munkaterülethez csatlakozó összes Windows-számítógépre. A felügyeleti csomagokat a *Microsoft. IntelligencePacks. ApplicationDependencyMonitor* és a *Microsoft. IntelligencePacks. VMInsights* nevű csomag tartalmazza, és a rendszer a *%ProgramFiles%\Microsoft monitoring Agent\Agent\Health szolgáltatás State\Management csomagjaira ír \* . 

A *ApplicationDependencyMonitor* felügyeleti csomag által használt adatforrás **% Program Files%\Microsoft monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. A *VMInsights* felügyeleti csomag által használt adatforrás a *(z)% program files%\Microsoft monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Diagnosztikai és használati adatok

A Microsoft a Azure Monitor szolgáltatás használatával automatikusan gyűjti a használati és teljesítményadatokat. A Microsoft ezeket az adatmennyiségeket használja a szolgáltatás minőségének, biztonságának és integritásának javítására. 

A pontos és hatékony hibaelhárítási funkciók biztosításához a Map szolgáltatás a szoftver konfigurációjával kapcsolatos információkat tartalmaz. Az adatok olyan információkat biztosítanak, mint például az operációs rendszer és a verziószám, az IP-cím, a DNS-név és a munkaállomás neve. A Microsoft nem gyűjt neveket, címeket és más kapcsolattartási adatokat.

További információ az adatok gyűjtéséről és használatáról: a [Microsoft Online Services adatvédelmi nyilatkozata](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>További lépések

A Teljesítményfigyelő funkció használatának megismeréséhez tekintse meg a [Azure monitor for VMS teljesítményének megtekintése](vminsights-performance.md)című témakört. A felderített alkalmazások függőségeinek megtekintéséhez lásd: [Azure monitor for VMS Térkép megtekintése](vminsights-maps.md).

