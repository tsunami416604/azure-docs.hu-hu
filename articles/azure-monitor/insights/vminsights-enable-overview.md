---
title: Azure Monitor for VMs-áttekintés engedélyezése
description: Megtudhatja, hogyan telepítheti és konfigurálhatja a Azure Monitor for VMs. Ismerje meg a rendszerkövetelményeket.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 072f8fd44fa45648afd15cb40cba26bb427c7b56
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539618"
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
- Central US
- USA északi középső régiója
- US Gov az
- US Gov VA
- Közép-Kanada
- Az Egyesült Királyság déli régiója
- Észak-Európa
- West Europe
- Kelet-Ázsia
- Délkelet-Ázsia
- Central India
- Kelet-Japán
- Kelet-Ausztrália
- Délkelet-Ausztrália

>[!NOTE]
>Bármelyik régióban nyomon követheti az Azure-beli virtuális gépeket. Maguk a virtuális gépek nem korlátozódnak a Log Analytics munkaterület által támogatott régiókra.
>

Ha nem rendelkezik Log Analytics munkaterülettel, létrehozhat egyet az egyik erőforrás használatával:
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Létrehozhat egy munkaterületet is, miközben egy Azure-beli virtuális gép vagy egy virtuálisgép-méretezési csoport figyelését engedélyezi a Azure Portalban.

Azure Policy, Azure PowerShell vagy Azure Resource Manager sablonokat használó méretezési forgatókönyv beállításához telepítenie kell a *VMInsights* -megoldást. Ezt a következő módszerek egyikével teheti meg:

* [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)használata.
* A Azure Monitor for VMs [**házirend-lefedettség**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) lapon válassza a **munkaterület konfigurálása**lehetőséget. 

### <a name="azure-arc-machines"></a>Azure arc-gépek
Azure Monitor for VMs elérhető az Azure arc-kompatibilis kiszolgálókhoz olyan régiókban, amelyeken elérhető az ív-bővítmény szolgáltatás. A felhasználóknak az arc-ügynök 0,9-es vagy újabb verzióját kell futtatniuk ahhoz, hogy a Azure Monitor for VMst az ív használatára képes kiszolgálókon engedélyezzék.

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

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

### <a name="the-microsoft-dependency-agent"></a>A Microsoft függőségi ügynök

A Azure Monitor for VMs Térkép funkciója a Microsoft függőségi ügynöktől kapja meg az adatait. A függőségi ügynök a Log Analytics ügynökre támaszkodik Log Analyticshoz való kapcsolódáskor. Ezért a rendszernek rendelkeznie kell a függőségi ügynökkel telepített és konfigurált Log Analytics-ügynökkel.

Függetlenül attól, hogy engedélyezi-e a Azure Monitor for VMst egyetlen Azure-beli virtuális gépen, vagy az igény szerinti üzembe helyezési módszert használja, a Windows vagy [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) [rendszerhez](../../virtual-machines/extensions/agent-dependency-windows.md) készült Azure-beli virtuálisgép-függőségi ügynök bővítmény használatával telepítse az ügynököt a felhasználói felület részeként.

>[!NOTE]
>Az ebben a szakaszban ismertetett információk a [Service Map megoldásra](service-map.md)is érvényesek.  

Hibrid környezetben a függőségi ügynököt manuálisan vagy automatikus módszerrel is letöltheti és telepítheti.

A következő táblázat azokat a csatlakoztatott forrásokat ismerteti, amelyeket a Térkép funkció támogat egy hibrid környezetben.

| Csatlakoztatott forrás | Támogatott | Leírás |
|:--|:--|:--|
| Windows-ügynökök | Igen | A Windows [log Analytics ügynökével](../../azure-monitor/platform/log-analytics-agent.md)együtt a Windows-ügynököknek szüksége van a függőségi ügynökre. További információ: [támogatott operációs rendszerek](#supported-operating-systems). |
| Linux-ügynökök | Igen | A [Linux rendszerhez készült log Analytics-ügynökkel](../../azure-monitor/platform/log-analytics-agent.md)együtt a Linux-ügynököknek szüksége van a függőségi ügynökre. További információ: [támogatott operációs rendszerek](#supported-operating-systems). |
| System Center Operations Manage felügyeleti csoport | Nem | |

A függőségi ügynököt a következő helyekről töltheti le:

| Fájl | Operációs rendszer | Verzió | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.4.10090 | B4E1FF9C1E5CD254AA709AEF9723A81F04EC0763C327567C582CE99C0C5A0BAE  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.4.10090 | A56E310D297CE3B343AE8F4A6F72980F1C3173862D6169F1C713C2CA09660A9F |

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A Azure Monitor for VMs szolgáltatásainak engedélyezéséhez és eléréséhez a *log Analytics közreműködő* szerepkörrel kell rendelkeznie. A teljesítmény-, állapot-és leképezési adat megtekintéséhez az Azure-beli virtuális gép *figyelési olvasójának* szerepkörrel kell rendelkeznie. A Log Analytics munkaterületet konfigurálni kell a Azure Monitor for VMshoz.

A Log Analytics munkaterület elérésének szabályozásáról a [munkaterületek kezelése](../../azure-monitor/platform/manage-access.md)című témakörben olvashat bővebben.

## <a name="how-to-enable-azure-monitor-for-vms"></a>A Azure Monitor for VMs engedélyezése

Azure Monitor for VMs engedélyezése a táblázatban leírt módszerek egyikének használatával:

| Központi telepítés állapota | Metódus | Leírás |
|------------------|--------|-------------|
| Egyetlen Azure-beli virtuális gép, Azure-VMSS vagy Azure arc-gép | [Engedélyezés a portálról](vminsights-enable-single-vm.md) | Válassza **ki** az eredményeket közvetlenül a Azure Portal menüjéből. |
| Több Azure-beli virtuális gép, Azure-VMSS vagy Azure-beli ív | [Engedélyezés Azure Policy](vminsights-enable-at-scale-policy.md) | A Azure Policy használatával automatikusan engedélyezheti a virtuális gépek vagy VMSS létrehozását. |
| | [Engedélyezés Azure PowerShell vagy Azure Resource Manager sablonokkal](vminsights-enable-at-scale-powershell.md) | Azure PowerShell-vagy Azure Resource Manager-sablonok használatával engedélyezheti több Azure-beli virtuális gép, Azure arc virtuális gép vagy Azure-VMSS használatát egy adott előfizetésen vagy erőforráscsoporton keresztül. |
| Hibrid felhő | [Hibrid környezet engedélyezése](vminsights-enable-hybrid-cloud.md) | Üzembe helyezés az adatközpontban vagy más felhőalapú környezetekben üzemeltetett virtuális gépeken vagy fizikai számítógépeken. |

## <a name="management-packs"></a>Felügyeleti csomagok

Ha a Azure Monitor for VMs engedélyezve van, és egy Log Analytics munkaterületre van konfigurálva, a felügyeleti csomag a munkaterületnek jelentő összes Windows-számítógépre továbbítódik. Ha a [System Center Operations Manager felügyeleti csoportot](../../azure-monitor/platform/om-agents.md) a log Analytics munkaterülettel integrálta, a Service Map felügyeleti csomagot a felügyeleti csoportból kell telepíteni a felügyeleti csoportnak jelentő Windows rendszerű számítógépekre.  

A felügyeleti csomag neve *Microsoft. IntelligencePacks. ApplicationDependencyMonitor*. A `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` mappához írt mappa. Az adatforrás, amelyet a felügyeleti csomag használ `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll` .

## <a name="diagnostic-and-usage-data"></a>Diagnosztikai és használati adatok

A Microsoft a Azure Monitor szolgáltatás használatával automatikusan gyűjti a használati és teljesítményadatokat. A Microsoft ezeket az adatmennyiségeket használja a szolgáltatás minőségének, biztonságának és integritásának javítására. 

A pontos és hatékony hibaelhárítási funkciók biztosításához a Map szolgáltatás a szoftver konfigurációjával kapcsolatos információkat tartalmaz. Az adatok olyan információkat biztosítanak, mint például az operációs rendszer és a verziószám, az IP-cím, a DNS-név és a munkaállomás neve. A Microsoft nem gyűjt neveket, címeket és más kapcsolattartási adatokat.

További információ az adatok gyűjtéséről és használatáról: a [Microsoft Online Services adatvédelmi nyilatkozata](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Most, hogy engedélyezte a virtuális gép figyelését, a figyelési információk a Azure Monitor for VMs elemzéséhez érhetők el.

## <a name="next-steps"></a>Következő lépések

A Teljesítményfigyelő funkció használatának megismeréséhez tekintse meg a [Azure monitor for VMS teljesítményének megtekintése](vminsights-performance.md)című témakört. A felderített alkalmazások függőségeinek megtekintéséhez lásd: [Azure monitor for VMS Térkép megtekintése](vminsights-maps.md).
