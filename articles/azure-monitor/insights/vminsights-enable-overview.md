---
title: A Azure Monitor for VMs (előzetes verzió) használatának áttekintése | Microsoft Docs
description: Megtudhatja, hogyan telepítheti és konfigurálhatja a Azure Monitor for VMs. Ismerje meg a rendszerkövetelményeket.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2019
ms.author: magoedte
ms.openlocfilehash: d2aa25af4ebf721d04ada46ffab37107bf73113b
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861116"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Azure Monitor for VMs (előzetes verzió) engedélyezése – áttekintés

Ez a cikk áttekintést nyújt a Azure Monitor for VMs beállításához rendelkezésre álló lehetőségekről. A Azure Monitor for VMs használatával figyelheti az állapotot és a teljesítményt. Fedezze fel az Azure Virtual Machines (VM) és a virtuálisgép-méretezési csoportok, a helyszíni virtuális gépek vagy más felhőalapú környezetekben üzemeltetett virtuális gépek által futtatott alkalmazások függőségeit.  

Azure Monitor for VMs beállítása:

* Egyetlen Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport engedélyezéséhez válassza az **áttekintés (előzetes verzió)** lehetőséget közvetlenül a virtuális gépről vagy a virtuálisgép-méretezési csoportból.
* Azure Policy használatával engedélyezze a két vagy több Azure-beli virtuális gép és virtuálisgép-méretezési csoport használatát. Ez a módszer biztosítja, hogy a meglévő és az új virtuális gépeken és a méretezési csoportokon a szükséges függőségek telepítve és megfelelően legyenek konfigurálva. A rendszer nem megfelelő virtuális gépeket és méretezési csoportokat jelentett, így eldöntheti, hogy engedélyezi-e őket, és hogyan javíthatja őket.
* Engedélyezzen két vagy több Azure virtuális gépek vagy virtuálisgép-méretezési csoportok között egy adott előfizetésen vagy erőforráscsoporton PowerShell használatával.
* Engedélyezze Azure Monitor for VMs a vállalati hálózaton vagy más felhőalapú környezetben üzemeltetett virtuális gépek vagy fizikai számítógépek figyelését.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt győződjön meg arról, hogy tisztában van-e az alábbi szakaszokban található információk. 

>[!NOTE]
>Az ebben a szakaszban ismertetett információk a [Service Map megoldásra](service-map.md)is érvényesek.  

### <a name="log-analytics"></a>Log Analytics

A Azure Monitor for VMs a következő régiókban támogatja a Log Analytics munkaterületet:

- USA nyugati középső régiója
- USA<sup>2.</sup> nyugati régiója
- East US
- Közép-Kanada
- Az Egyesült Királyság déli régiója
- Nyugat-Európa
- Délkelet-Ázsia
- Kelet-Ausztrália<sup>1</sup>
- Délkelet-Ausztrália<sup>1</sup>

<sup>1</sup> ebben a régióban jelenleg nem támogatja a Azure monitor az állapotfigyelő szolgáltatás virtuális gépek számára.

>[!NOTE]
>Az Azure-beli virtuális gépek bármelyik régióból üzembe helyezhetők. Ezek a virtuális gépek nem korlátozódnak a Log Analytics munkaterület által támogatott régiókra.
>

Ha nem rendelkezik munkaterülettel, létrehozhat egyet a következő erőforrások egyikének használatával:
* [Az Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Létrehozhat egy munkaterületet is, miközben egy Azure-beli virtuális gép vagy egy virtuálisgép-méretezési csoport figyelését engedélyezi a Azure Portalban.

Azure Policy, Azure PowerShell vagy Azure Resource Manager sablonokat használó, méretezési forgatókönyv beállításához a Log Analytics munkaterületen:

* Telepítse a ServiceMap és InfrastructureInsights megoldásokat. A telepítést egy megadott Azure Resource Manager sablonnal végezheti el. Vagy az **első lépések** lapon válassza a **munkaterület konfigurálása**lehetőséget.
* Konfigurálja a teljesítményszámlálók adatainak összegyűjtése a Log Analytics-munkaterületet.

Az alábbi módszerek egyikével konfigurálhatja a munkaterületet a méretezési forgatókönyvhöz:

* [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)használata.
* A Azure Monitor for VMs [**házirend-lefedettség**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) lapon válassza a **munkaterület konfigurálása**lehetőséget. 

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A következő táblázat felsorolja a Azure Monitor for VMs által támogatott Windows-és Linux-operációs rendszereket. A szakasz későbbi részében egy teljes listát talál, amely részletesen ismerteti a Linux operációs rendszer kiadásának és a támogatott kernelnek a verziószámát.

|Operációs rendszer verziója |Teljesítmény |Maps |Állapot |
|-----------|------------|-----|-------|
|A Windows Server 2019 | X | X | X |
|A Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X|  |
|A Windows 10 1803-as verzióban | X | X | |
|Windows 8.1 | X | X | |
|Windows 8 | X | X | |
|Windows 7 SP1 | X | X | |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14,04, 16,04, 18,04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Debian 8, 9,4 | X<sup>1</sup> | | X |

<sup>1</sup> a teljesítmény funkció az Azure Monitor-beli virtuális gépek csak az Azure Monitor érhető el. Nem érhető el közvetlenül az Azure-beli virtuális gép bal oldali paneljéről.

>[!NOTE]
>Azure Monitor for VMs állapota nem támogatja az Azure-beli virtuális gépek [beágyazott virtualizálása](../../virtual-machines/windows/nested-virtualization.md) funkciót.
>

>[!NOTE]
>A Linux operációs rendszerben:
> - Csak az alapértelmezett és az SMP Linux kernelű kiadások támogatottak.
> - Nestandardní kernel kiadások, mint például a Xen, és a fizikai cím bővítmény (fizikai) nem támogatottak az minden olyan Linux-disztribúció. A kiadási karakterláncot, például egy rendszer *2.6.16.21-0.8-xen* nem támogatott.
> - Az egyéni kernelek, beleértve a standard kernelek újrafordítását, nem támogatottak.
> - A CentOSPlus kernel támogatott.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7,6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

#### <a name="centosplus"></a>CentOSPlus
| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| Ubuntu 18.04 | kernel 4,15.\*<br>4,18 * |
| Ubuntu 16.04.3 | kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 vállalati kiszolgáló

| Operációs rendszer verziója | Kernel verziója
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |
|12 SP4 CSOMAG | 4.4. * |
|12 SP4 CSOMAG | Azure által hangolt kernel |

### <a name="the-microsoft-dependency-agent"></a>A Microsoft Dependency agent

A Azure Monitor for VMs Térkép funkciója a Microsoft függőségi ügynöktől kapja meg az adatait. A függőségi ügynök a Log Analytics-ügynököket a Log Analytics-kapcsolat támaszkodik. Ezért a rendszernek rendelkeznie kell a függőségi ügynökkel telepített és konfigurált Log Analytics-ügynökkel.

Függetlenül attól, hogy engedélyezi-e a Azure Monitor for VMst egyetlen Azure-beli virtuális gépen, vagy az igény szerinti üzembe helyezési módszert használja, az Azure-beli virtuálisgép-függőségi ügynök bővítménnyel telepítheti az ügynököt a felhasználói élmény részeként.

>[!NOTE]
>Az ebben a szakaszban ismertetett információk a [Service Map megoldásra](service-map.md)is érvényesek.  

Hibrid környezetben a függőségi ügynököt manuálisan is letöltheti és telepítheti. Ha a virtuális gépek az Azure-on kívül futnak, használjon automatikus telepítési módszert.

A következő táblázat ismerteti a térkép funkció támogatja a hibrid környezetben összekapcsolt forrásokról.

| Csatlakoztatott forrás | Támogatott | Leírás |
|:--|:--|:--|
| Windows-ügynökök | Igen | A Windows [log Analytics ügynökével](../../azure-monitor/platform/log-analytics-agent.md)együtt a Windows-ügynököknek szüksége van a függőségi ügynökre. További információ: [támogatott operációs rendszerek](#supported-operating-systems). |
| Linux-ügynökök | Igen | A [Linux rendszerhez készült log Analytics-ügynökkel](../../azure-monitor/platform/log-analytics-agent.md)együtt a Linux-ügynököknek szüksége van a függőségi ügynökre. További információ: [támogatott operációs rendszerek](#supported-operating-systems). |
| System Center Operations Manage felügyeleti csoport | Nem | |

A függőségi ügynököt a következő helyekről töltheti le:

| Fájl | Operációs rendszer | Verzió | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.9.1 | FCF9C1D9B20AD414051B49EE79144E595CCC411EB6D444D6D5B5A7B1874DCDEC |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.9.1 | 1CB447EF30FC042FE7499A686638F3F9B4F449692FB9D80096820F8024BE4D7C |

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A Azure Monitor for VMs szolgáltatásainak engedélyezéséhez és eléréséhez a *log Analytics közreműködő* szerepkörrel kell rendelkeznie. A teljesítmény-, állapot-és leképezési adat megtekintéséhez az Azure-beli virtuális gép *figyelési olvasójának* szerepkörrel kell rendelkeznie. A Log Analytics-munkaterület-beli virtuális gépek az Azure Monitor kell konfigurálni.

A Log Analytics-munkaterülethez való hozzáférésének kapcsolatos további információkért lásd: [munkaterületeinek kezeléséhez](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>A Azure Monitor for VMs engedélyezése (előzetes verzió)

Azure Monitor for VMs engedélyezése a táblázatban leírt módszerek egyikének használatával:

| Központi telepítés állapota | Módszer | Leírás |
|------------------|--------|-------------|
| Egyetlen Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport | [Engedélyezés a virtuális gépről](vminsights-enable-single-vm.md) | Egyetlen Azure-beli virtuális gép engedélyezéséhez válassza az **áttekintések (előzetes verzió)** lehetőséget közvetlenül a virtuális gépről vagy a virtuálisgép-méretezési csoportból. |
| Több Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport | [Engedélyezés Azure Policy](vminsights-enable-at-scale-policy.md) | A Azure Policy és a rendelkezésre álló házirend-definíciók használatával több Azure-beli virtuális gép is engedélyezhető. |
| Több Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport | [Engedélyezés Azure PowerShell vagy Azure Resource Manager sablonokkal](vminsights-enable-at-scale-powershell.md) | A megadott előfizetésben vagy erőforráscsoporthoz több Azure-beli virtuális gépet vagy virtuálisgép-méretezési csoportot is engedélyezhet Azure PowerShell vagy Azure Resource Manager sablonok használatával. |
| Hibrid felhő | [Hibrid környezet engedélyezése](vminsights-enable-hybrid-cloud.md) | Üzembe helyezheti az adatközpontban vagy más felhőalapú környezetekben üzemeltetett virtuális gépeken vagy fizikai számítógépeken. |

## <a name="performance-counters-enabled"></a>A teljesítményszámlálók engedélyezve 

Azure Monitor for VMs konfigurálja a Log Analytics munkaterületet az általa használt teljesítményszámlálók összegyűjtéséhez. Az alábbi táblázatok felsorolják az 60 másodpercenként összegyűjtött objektumokat és számlálókat.

### <a name="windows-performance-counters"></a>Windows-teljesítményszámlálók

|Objektumnév |Számláló neve |
|------------|-------------|
|Logikai lemez |% Szabad terület |
|Logikai lemez |Átl. Lemez mp/Olvasás |
|Logikai lemez |Átl. Lemez mp/átvitel |
|Logikai lemez |Átl. Lemez mp/írás |
|Logikai lemez |Bájt/mp |
|Logikai lemez |Lemezolvasási sebesség (bájt/s) |
|Logikai lemez |Lemezolvasások/mp |
|Logikai lemez |Átvitel/mp |
|Logikai lemez |Lemezírási sebesség (bájt/s) |
|Logikai lemez |Lemezírások/mp |
|Logikai lemez |Szabad hely MB-ban |
|Memory (Memória) |Rendelkezésre álló memória |
|Hálózati Adapter |Fogadott bájtok/mp |
|Hálózati Adapter |Küldött bájtok/s |
|Processzor |Processzoridő |

### <a name="linux-performance-counters"></a>Linux-teljesítményszámlálók

|Objektumnév |Számláló neve |
|------------|-------------|
|Logikai lemez |Foglalt hely % |
|Logikai lemez |Lemezolvasási sebesség (bájt/s) |
|Logikai lemez |Lemezolvasások/mp |
|Logikai lemez |Átvitel/mp |
|Logikai lemez |Lemezírási sebesség (bájt/s) |
|Logikai lemez |Lemezírások/mp |
|Logikai lemez |Szabad hely MB-ban |
|Logikai lemez |Logikai lemez bájt/mp |
|Memory (Memória) |Rendelkezésre álló memória |
|Network (Hálózat) |Fogadott bájtok teljes száma |
|Network (Hálózat) |Küldött bájtok száma összesen |
|Processzor |Processzoridő |

## <a name="management-packs"></a>Felügyeleti csomagok

Ha a Azure Monitor for VMs engedélyezve van, és egy Log Analytics munkaterületre van konfigurálva, a felügyeleti csomag a munkaterületnek jelentő összes Windows-számítógépre továbbítódik. Ha a [System Center Operations Manager felügyeleti csoportot](../../azure-monitor/platform/om-agents.md) a log Analytics munkaterülettel integrálta, a Service Map felügyeleti csomagot a felügyeleti csoportból kell telepíteni a felügyeleti csoportnak jelentő Windows rendszerű számítógépekre.  

A felügyeleti csomag neve *Microsoft. IntelligencePacks. ApplicationDependencyMonitor*. A `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` mappához írt mappa. Az adatforrás, amelyet a felügyeleti csomag használ `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`.

## <a name="diagnostic-and-usage-data"></a>Diagnosztika és használati adatok

A Microsoft automatikusan gyűjt keresztül az Azure Monitor szolgáltatás használatának és teljesítményének adatait. A Microsoft ezeket az adatmennyiségeket használja a szolgáltatás minőségének, biztonságának és integritásának javítására. 

A pontos és hatékony hibaelhárítási funkciók biztosításához a Map szolgáltatás a szoftver konfigurációjával kapcsolatos információkat tartalmaz. Az adatok olyan információkat biztosítanak, mint például az operációs rendszer és a verziószám, az IP-cím, a DNS-név és a munkaállomás neve. A Microsoft nem gyűjt, neveket, címeket és egyéb kapcsolattartási adatait.

Az adatok gyűjtésével és használatával kapcsolatos további információkért tekintse meg a [Microsoft Online Services adatvédelmi nyilatkozata](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Most, hogy engedélyezte a virtuális gép figyelését, a figyelési információk a Azure Monitor for VMs elemzéséhez érhetők el.

## <a name="next-steps"></a>További lépések

Az állapotfigyelő szolgáltatás használatával kapcsolatban lásd: [a virtuális gépek állapotának megtekintése az Azure Monitor](vminsights-health.md). Felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md).
