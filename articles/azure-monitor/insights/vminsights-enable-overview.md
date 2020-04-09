---
title: Az Azure Monitor engedélyezése virtuális gépekhez – áttekintés
description: Ismerje meg, hogyan telepítheti és konfigurálhatja az Azure Monitor virtuális gépekhez. Ismerje meg a rendszerkövetelményeket.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/08/2020
ms.openlocfilehash: 5bb5d5dd5110f176b59a99f6a3aa223184158da5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982310"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Az Azure Monitor engedélyezése virtuális gépekhez – áttekintés

Ez a cikk áttekintést nyújt az Azure Monitor virtuális gépek hez a virtuális gépek en az állapot és a teljesítmény figyeléséhez rendelkezésre álló lehetőségekről. Fedezze fel az Azure virtuális gépeken (VM-ek) és a virtuálisgép-méretezési csoportokon, a helyszíni virtuális gépeken vagy a más felhőalapú környezetben üzemeltetett virtuális gépeken futó alkalmazásfüggőségeket.  

Az Azure Monitor beállítása virtuális gépekhez:

* Engedélyezze egyetlen Azure virtuális gép vagy virtuálisgép-méretezési készlet kiválasztásával **Insights** közvetlenül a virtuális gép vagy a virtuális gép méretezési csoport.
* Engedélyezhet két vagy több Azure-beli virtuális gépet és virtuálisgép-méretezési csoportokat az Azure Policy használatával. Ez a módszer biztosítja, hogy a meglévő és az új virtuális gépek és méretezési készletek, a szükséges függőségek telepítése és megfelelőkonfigurálása. Nem megfelelő virtuális gépek és méretezési csoportok jelentik, így eldöntheti, hogy engedélyezi-e őket, és kijavíthatja őket.
* Engedélyezze két vagy több Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport egy adott előfizetés vagy erőforráscsoport a PowerShell használatával.
* Engedélyezze az Azure Monitor virtuális gépekhez a vállalati hálózatban vagy más felhőbeli környezetben üzemeltetett virtuális gépek vagy fizikai számítógépek figyelésére.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy megértette az alábbi szakaszokban található információkat. 

>[!NOTE]
>Az ebben a szakaszban ismertetett alábbi információk a [Szolgáltatástérkép-megoldásra](service-map.md)is vonatkoznak.  

### <a name="log-analytics"></a>Log Analytics

Az Azure Monitor virtuális gépek támogatja a Log Analytics munkaterületet a következő régiókban:

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
>Az Azure virtuális gépek figyelheti bármely régióban. Maguk a virtuális gépek nem korlátozódnak a Log Analytics munkaterület által támogatott régiókra.
>

Ha nem rendelkezik Log Analytics-munkaterülettel, létrehozhat egyet az egyik erőforrás használatával:
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Munkaterületet is létrehozhat, miközben egyetlen Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport figyelését engedélyezi az Azure Portalon.

Az Azure Policy, az Azure PowerShell vagy az Azure Resource Manager-sablonokat használó, scale-scale forgatókönyv beállítása a Log Analytics-munkaterületen:

* Telepítse a *ServiceMap* és *InfrastructureInsights* megoldásokat. Ezt a telepítést egy megadott Azure Resource Manager-sablon használatával végezheti el. Vagy az Azure Portal **első lépések** lapján válassza a **Munkaterület konfigurálása**lehetőséget.
* Konfigurálja a Log Analytics munkaterületet teljesítményszámlálók gyűjtésére.

A munkaterület méretarányos beállításához használja az alábbi módszerek egyikét:

* Használja [az Azure PowerShellt.](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)
* Az Azure Monitor szolgáltatás virtuális gépek [**házirend-lefedettség**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) e lapon válassza **a Munkaterület konfigurálása**lehetőséget. 

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Az alábbi táblázat felsorolja azokat a Windows és Linux operációs rendszereket, amelyeket az Azure Monitor virtuális gépekhez támogat. Ebben a szakaszban talál egy teljes listát, amely részletezi a linuxos operációs rendszer fő és kisebb kiadását és a támogatott kernelverziókat.

|Operációs rendszer verziója |Teljesítmény |Maps |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18.04, 16.04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> Az Azure Monitor szolgáltatás a virtuális gépek csak az Azure Monitor érhető el. Nem érhető el közvetlenül az Azure virtuális gép bal oldali ablaktáblájából.

>[!NOTE]
>A Linux operációs rendszerben:
> - Csak az alapértelmezett és az SMP Linux kernelű kiadások támogatottak.
> - A nem szabványos kernelkiadások, például a fizikai címkiterjesztés (PAE) és az Xen nem támogatottak linuxos disztribúcióesetén. Például egy *2.6.16.21-0.8-xen* kiadási karakterláncú rendszer nem támogatott.
> - Az egyéni kernelek, beleértve a szabványos kernelek újrafordítását, nem támogatottak.
> - A CentOSPlus kernel támogatott.
> - A Spectre biztonsági réséhez a Linux kernelt ki kell javítani. További részletekért forduljon a Linux disztribúciós forgalmazójához.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7,5 | 3.10.0-862 |
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
| 18.04 | 5.0 (beleértve az Azure-ra hangolt kernelt is)<br>4.18*<br>4.15* |
| 16.04.3 | 4.15.* |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 vállalati kiszolgáló

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
|12 SP4 | 4.12.* (beleértve az Azure-ra hangolt kernelt) |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 9 | 4.9 | 

### <a name="the-microsoft-dependency-agent"></a>A Microsoft függőségi ügynöke

Az Azure Monitor szolgáltatás virtuális gépek leképezése szolgáltatás a Microsoft függőségi ügynök. A függőségi ügynök támaszkodik a Log Analytics-ügynök a Log Analytics-kapcsolat. Így a rendszernek telepítenie kell a Log Analytics-ügynököt, és konfigurálnia kell a függőségi ügynökkel.

Akár engedélyezi az Azure Monitor virtuális gépek egyetlen Azure virtuális gép, vagy használja a nagy léptékű telepítési módszer, használja az Azure VM-függőségügynök bővítmény [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) vagy [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) az ügynök telepítéséhez a felhasználói élmény részeként.

>[!NOTE]
>Az ebben a szakaszban ismertetett alábbi információk a [Szolgáltatástérkép-megoldásra](service-map.md)is vonatkoznak.  

Hibrid környezetben manuálisan vagy automatikus módszerrel töltheti le és telepítheti a függőségi ügynököt.

Az alábbi táblázat azokat a csatlakoztatott forrásokat ismerteti, amelyeket a Map szolgáltatás hibrid környezetben támogat.

| Csatlakoztatott forrás | Támogatott | Leírás |
|:--|:--|:--|
| Windows-ügynökök | Igen | A [Windows-ügynökök loganalytics-ügynökével](../../azure-monitor/platform/log-analytics-agent.md)együtt a Windows-ügynököknek szükségük van a függőségi ügynökre. További információt a támogatott operációs rendszerek című témakörben [talál.](#supported-operating-systems) |
| Linux-ügynökök | Igen | A [Linux-ügynök i Log Analytics-ügynökkel](../../azure-monitor/platform/log-analytics-agent.md)együtt a Linux-ügynököknek szükségük van a függőségi ügynökre. További információt a támogatott operációs rendszerek című témakörben [talál.](#supported-operating-systems) |
| System Center Operations Manage felügyeleti csoport | Nem | |

A függőségi ügynök az alábbi helyekről tölthető le:

| Fájl | Operációs rendszer | Verzió | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.3.9380 | 40763BD0A5B60707DF3F9E7BCC17D917F5CE995F2F5A4633D8B733F3BE143921  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.3.9380 | BB41BB59BDD293968F02A9EF821F9639406AA1BDF1F67925DB9EE00D54A7F0B |

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A virtuális gépekhez készült Azure Monitor funkcióinak engedélyezéséhez és eléréséhez rendelkeznie kell a *Log Analytics közreműködői* szerepkörsel. A teljesítmény, az állapot és a térképadatok megtekintéséhez rendelkeznie kell az Azure virtuális gép *figyelési olvasó* szerepkörével. A Log Analytics munkaterületet konfigurálni kell az Azure Monitor virtuális gépekhez.

A Log Analytics-munkaterülethez való hozzáférés szabályozásáról a [Munkaterületek kezelése című témakörben talál](../../azure-monitor/platform/manage-access.md)további információt.

## <a name="how-to-enable-azure-monitor-for-vms"></a>Az Azure Monitor virtuális gépekhez szolgáltatásának engedélyezése

Engedélyezze az Azure Monitor virtuális gépekhez a táblázatban leírt módszerek egyikének használatával:

| Telepítési állapot | Módszer | Leírás |
|------------------|--------|-------------|
| Egyetlen Azure virtuális gép vagy virtuális gép méretezési készlete | [Engedélyezés a virtuális gépről](vminsights-enable-single-vm.md) | Egyetlen Azure-virtuális gépet engedélyezhet, ha közvetlenül a virtuális gépből vagy a virtuális gép méretezési csoportból választja ki az **Insights-ot.** |
| Több Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport | [Engedélyezés az Azure-szabályzaton keresztül](vminsights-enable-at-scale-policy.md) | Több Azure-beli virtuális gépet is engedélyezhet az Azure Policy és a rendelkezésre álló szabályzat-definíciók használatával. |
| Több Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport | [Engedélyezés az Azure PowerShell vagy az Azure Resource Manager-sablonok on keresztül](vminsights-enable-at-scale-powershell.md) | Több Azure-beli virtuális gépet vagy virtuálisgép-méretezési csoportot is engedélyezhet egy adott előfizetésben vagy erőforráscsoportban az Azure PowerShell vagy az Azure Resource Manager-sablonok használatával. |
| Hibrid felhő | [Engedélyezés hibrid környezethez](vminsights-enable-hybrid-cloud.md) | Telepítheti a virtuális gépek vagy fizikai számítógépek, amelyek az adatközpontban vagy más felhőalapú környezetekben üzemeltetett. |

## <a name="management-packs"></a>Felügyeleti csomagok

Ha az Azure Monitor virtuális gépekhez engedélyezve van, és konfigurálva van egy Log Analytics-munkaterülettel, a felügyeleti csomag továbbításra kerül az adott munkaterületre jelentést tevő összes Windows-számítógépnek. Ha [integrálta a System Center Operations Manager felügyeleti csoportot](../../azure-monitor/platform/om-agents.md) a Log Analytics-munkaterülettel, a szolgáltatástérkép-felügyeleti csomag a felügyeleti csoportból a felügyeleti csoportnak a felügyeleti csoportnak jelentést tevő Windows-számítógépekre kerül.  

A felügyeleti csomag neve *Microsoft.IntelligencePacks.ApplicationDependencyMonitor*. A írt `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` mappába. A felügyeleti csomag által használt `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`adatforrás a.

## <a name="diagnostic-and-usage-data"></a>Diagnosztikai és használati adatok

A Microsoft automatikusan gyűjti a használati és teljesítményadatokat az Azure Monitor szolgáltatás használatával. A Microsoft ezeket az adatokat a szolgáltatás minőségének, biztonságának és integritásának javítására használja. 

A pontos és hatékony hibaelhárítási lehetőségek biztosítása érdekében a Térkép szolgáltatás adatokat tartalmaz a szoftver konfigurációjáról. Az adatok olyan információkat tartalmaznak, mint az operációs rendszer és verzió, az IP-cím, a DNS-név és a munkaállomás neve. A Microsoft nem gyűjt neveket, címeket vagy egyéb kapcsolattartási adatokat.

Az adatgyűjtésről és -használatról a [Microsoft Online Services adatvédelmi nyilatkozatában olvashat bővebben.](https://go.microsoft.com/fwlink/?LinkId=512132)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Most, hogy engedélyezte a virtuális gép figyelését, a figyelési információk elemzésre érhetők el az Azure Monitor virtuális gépekhez.

## <a name="next-steps"></a>További lépések

A Teljesítményfigyelés funkció használatáról az [Azure-figyelő a virtuális gépek teljesítményének megtekintése című témakörben](vminsights-performance.md)olvashat. A felderített alkalmazásfüggőségek megtekintéséhez olvassa el [az Azure-figyelő megtekintése virtuális gépekleképezéséhez.](vminsights-maps.md)
