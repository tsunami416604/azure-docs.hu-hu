---
title: A Azure Monitor for VMs (előzetes verzió) használatának áttekintése | Microsoft Docs
description: Megtudhatja, hogyan telepítheti és konfigurálhatja a Azure Monitor for VMs. Ismerje meg a rendszerkövetelményeket.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 44422f66f6fc995dcaf96947ea05b183c7131ea3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669573"
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
- USA nyugati régiója
- USA nyugati régiója, 2.
- USA déli középső régiója
- USA keleti régiója
- USA 2. keleti régiója
- USA középső régiója
- USA északi középső régiója
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

|Operációs rendszer verziója |Teljesítmény |Maps |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|A Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|A Windows 10 1803-as verzióban | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18,04, 16,04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> a Azure monitor for VMS teljesítménybeli funkciója csak Azure monitor érhető el. Nem érhető el közvetlenül az Azure-beli virtuális gép bal oldali paneljéről.

>[!NOTE]
>A Linux operációs rendszerben:
> - Csak az alapértelmezett és az SMP Linux kernelű kiadások támogatottak.
> - Nestandardní kernel kiadások, mint például a Xen, és a fizikai cím bővítmény (fizikai) nem támogatottak az minden olyan Linux-disztribúció. Például a *2.6.16.21-0,8-Xen* kiadási karakterlánccal rendelkező rendszer nem támogatott.
> - Az egyéni kernelek, beleértve a standard kernelek újrafordítását, nem támogatottak.
> - A CentOSPlus kernel támogatott.
> - A Linux-kernelt a fantom biztonsági rések számára kell javítani. További részletekért tekintse meg a Linux-disztribúció gyártóját.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 7,6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 18,04 | 5,0 (tartalmazza az Azure által hangolt kernelt)<br>4,18 *<br>4,15* |
| 16.04.3 | 4,15. * |
| 16.04 | 4,13.\*<br>4,11.\*<br>4,10.\*<br>4,8.\*<br>4,4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 vállalati kiszolgáló

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
|12 SP4 CSOMAG | 4,12. * (tartalmazza az Azure által hangolt kernelt) |
|12 SP3 | 4.4. * |
|12 SP2 | 4.4. * |

#### <a name="debian"></a>Debian 

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 9 | 4,9 | 

### <a name="the-microsoft-dependency-agent"></a>A Microsoft Dependency agent

A Azure Monitor for VMs Térkép funkciója a Microsoft függőségi ügynöktől kapja meg az adatait. A függőségi ügynök a Log Analytics-ügynököket a Log Analytics-kapcsolat támaszkodik. Ezért a rendszernek rendelkeznie kell a függőségi ügynökkel telepített és konfigurált Log Analytics-ügynökkel.

Függetlenül attól, hogy engedélyezi-e a Azure Monitor for VMst egyetlen Azure-beli virtuális gépen, vagy az igény szerinti üzembe helyezési módszert használja, a Windows vagy [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) [rendszerhez](../../virtual-machines/extensions/agent-dependency-windows.md) készült Azure-beli virtuálisgép-függőségi ügynök bővítmény használatával telepítse az ügynököt a felhasználói felület részeként.

>[!NOTE]
>Az ebben a szakaszban ismertetett információk a [Service Map megoldásra](service-map.md)is érvényesek.  

Hibrid környezetben a függőségi ügynököt manuálisan vagy automatikus módszerrel is letöltheti és telepítheti.

A következő táblázat ismerteti a térkép funkció támogatja a hibrid környezetben összekapcsolt forrásokról.

| Csatlakoztatott forrás | Támogatott | Leírás |
|:--|:--|:--|
| Windows-ügynökök | Igen | A Windows [log Analytics ügynökével](../../azure-monitor/platform/log-analytics-agent.md)együtt a Windows-ügynököknek szüksége van a függőségi ügynökre. További információ: [támogatott operációs rendszerek](#supported-operating-systems). |
| Linux-ügynökök | Igen | A [Linux rendszerhez készült log Analytics-ügynökkel](../../azure-monitor/platform/log-analytics-agent.md)együtt a Linux-ügynököknek szüksége van a függőségi ügynökre. További információ: [támogatott operációs rendszerek](#supported-operating-systems). |
| System Center Operations Manage felügyeleti csoport | Nem | |

A függőségi ügynököt a következő helyekről töltheti le:

| Fájl | Operációs rendszer | Verzió | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.9.2 | 6DFF19B9690E42CA190E3B69137C77904B657FA02895033EAA4C3A6A41DA5C6A |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.9.1 | 1CB447EF30FC042FE7499A686638F3F9B4F449692FB9D80096820F8024BE4D7C |

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A Azure Monitor for VMs szolgáltatásainak engedélyezéséhez és eléréséhez a *log Analytics közreműködő* szerepkörrel kell rendelkeznie. A teljesítmény-, állapot-és leképezési adat megtekintéséhez az Azure-beli virtuális gép *figyelési olvasójának* szerepkörrel kell rendelkeznie. A Log Analytics-munkaterület-beli virtuális gépek az Azure Monitor kell konfigurálni.

A Log Analytics munkaterület elérésének szabályozásáról a [munkaterületek kezelése](../../azure-monitor/platform/manage-access.md)című témakörben olvashat bővebben.

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

>[!NOTE]
>A Azure Monitor for VMs által engedélyezett teljesítményszámlálók következő listája nem korlátozza a munkaterületre jelentett virtuális gépekről összegyűjtött további számlálók engedélyezését. Emellett, ha letiltja ezeket a számlálókat, a teljesítmény-diagramok készletét is megakadályozza, hogy az erőforrás-kihasználtságot a virtuális gépekről jelenítse meg.

### <a name="windows-performance-counters"></a>Windows-teljesítményszámlálók

|Objektumnév |Számláló neve |
|------------|-------------|
|Logikai lemez |Szabad terület százalékos aránya |
|Logikai lemez |Átlagos írási idő (mp/olvasás) |
|Logikai lemez |Átlagos műveleti idő (mp/átvitel) |
|Logikai lemez |Átlagos írási idő (mp/írás) |
|Logikai lemez |Lemez sebessége bájt/mp-ben |
|Logikai lemez |Lemezolvasás sebessége bájt/mp-ben |
|Logikai lemez |Lemezolvasások/mp |
|Logikai lemez |Átvitel/mp |
|Logikai lemez |Lemezírás sebessége bájt/mp-ben |
|Logikai lemez |Lemezírások/mp |
|Logikai lemez |Szabad hely MB-ban |
|Memory (Memória) |Rendelkezésre álló memória |
|Hálózati adapter |Fogadott bájtok/mp |
|Hálózati adapter |Küldött bájtok/s |
|Processzor |A processzor kihasználtsága (%) |

### <a name="linux-performance-counters"></a>Linux-teljesítményszámlálók

|Objektumnév |Számláló neve |
|------------|-------------|
|Logikai lemez |Foglalt hely % |
|Logikai lemez |Lemezolvasás sebessége bájt/mp-ben |
|Logikai lemez |Lemezolvasások/mp |
|Logikai lemez |Átvitel/mp |
|Logikai lemez |Lemezírás sebessége bájt/mp-ben |
|Logikai lemez |Lemezírások/mp |
|Logikai lemez |Szabad hely MB-ban |
|Logikai lemez |Logikai lemez bájt/mp |
|Memory (Memória) |Rendelkezésre álló memória |
|Hálózat |Fogadott bájtok teljes száma |
|Hálózat |Küldött bájtok száma összesen |
|Processzor |A processzor kihasználtsága (%) |

## <a name="management-packs"></a>Felügyeleti csomagok

Ha a Azure Monitor for VMs engedélyezve van, és egy Log Analytics munkaterületre van konfigurálva, a felügyeleti csomag a munkaterületnek jelentő összes Windows-számítógépre továbbítódik. Ha a [System Center Operations Manager felügyeleti csoportot](../../azure-monitor/platform/om-agents.md) a log Analytics munkaterülettel integrálta, a Service Map felügyeleti csomagot a felügyeleti csoportból kell telepíteni a felügyeleti csoportnak jelentő Windows rendszerű számítógépekre.  

A felügyeleti csomag neve *Microsoft. IntelligencePacks. ApplicationDependencyMonitor*. A `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` mappájába írt. A felügyeleti csomag által használt adatforrás `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`.

## <a name="diagnostic-and-usage-data"></a>Diagnosztika és használati adatok

A Microsoft automatikusan gyűjt keresztül az Azure Monitor szolgáltatás használatának és teljesítményének adatait. A Microsoft ezeket az adatmennyiségeket használja a szolgáltatás minőségének, biztonságának és integritásának javítására. 

A pontos és hatékony hibaelhárítási funkciók biztosításához a Map szolgáltatás a szoftver konfigurációjával kapcsolatos információkat tartalmaz. Az adatok olyan információkat biztosítanak, mint például az operációs rendszer és a verziószám, az IP-cím, a DNS-név és a munkaállomás neve. A Microsoft nem gyűjt, neveket, címeket és egyéb kapcsolattartási adatait.

További információ az adatok gyűjtéséről és használatáról: a [Microsoft Online Services adatvédelmi nyilatkozata](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Most, hogy engedélyezte a virtuális gép figyelését, a figyelési információk a Azure Monitor for VMs elemzéséhez érhetők el.

## <a name="next-steps"></a>Következő lépések

A Teljesítményfigyelő funkció használatának megismeréséhez tekintse meg a [Azure monitor for VMS teljesítményének megtekintése](vminsights-performance.md)című témakört. A felderített alkalmazások függőségeinek megtekintéséhez lásd: [Azure monitor for VMS Térkép megtekintése](vminsights-maps.md).
