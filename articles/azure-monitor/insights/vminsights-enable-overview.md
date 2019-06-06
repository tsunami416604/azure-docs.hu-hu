---
title: Az Azure Monitor engedélyezése (előzetes verzió) virtuális gépek – áttekintés |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan üzembe helyezése és az Azure Monitor konfigurálása virtuális gépek és a rendszer szükséges követelményeknek.
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
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: c84c3eb74d214a5c98aabef7b2e2987dfdf67c0f
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472606"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Az Azure Monitor engedélyezése (előzetes verzió) virtuális gépek – áttekintés

Ez a cikk a virtuális gépek állapotát, teljesítményét, és az Azure virtual machines és a virtual machine scale sets futó alkalmazásfüggőségek felderítése az Azure Monitor beállítása, a helyszíni virtuális gépek rendelkezésre álló beállítások áttekintését, vagy az üzemeltetett virtuális gépek egy másik felhőalapú környezetben.  

Engedélyezi az Azure Monitor-beli virtuális gépek a következő módszerek egyikével:

* Egyetlen Azure virtuális gép vagy virtuálisgép-méretezési kiválasztásával engedélyezése **Insights (előzetes verzió)** közvetlenül a virtuális gép vagy virtuálisgép-méretezési csoport a beállítása.
* Engedélyezzen két vagy több Azure virtuális gépek és virtuálisgép-méretezési csoportok az Azure Policy használatával. Ezzel a módszerrel a meglévő és új virtuális gépek és a méretezési csoportok a szükséges függőségek, telepítve van és megfelelően beállította. Nem megfelelő virtuális gépek és a méretezési csoportok jelenti, hogy lehetősége engedélyezheti őket, és elháríthatja a módját.
* Engedélyezzen két vagy több Azure virtuális gépek vagy virtuálisgép-méretezési csoportok között egy adott előfizetésen vagy erőforráscsoporton PowerShell használatával.
* Engedélyezze a virtuális gépek vagy a vállalati hálózathoz vagy egyéb felhőalapú környezetben futó fizikai számítógépek.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt győződjön meg arról, hogy tisztában van-e az alábbi szakaszokban található információk.

### <a name="log-analytics"></a>Log Analytics

A virtuális gépek az Azure Monitor Log Analytics-munkaterület az alábbi régiókban támogatja:

- USA nyugati középső régiója
- USA 2. nyugati<sup>1</sup>
- USA keleti régiója
- Közép-Kanada<sup>1</sup>
- Egyesült Királyság déli régiója<sup>1</sup>
- Nyugat-Európa
- Délkelet-Ázsia<sup>1</sup>

<sup>1</sup> ebben a régióban jelenleg nem támogatja a Azure monitor az állapotfigyelő szolgáltatás virtuális gépek számára.

>[!NOTE]
>Az Azure virtual machines-régióból kezdeményezett telepíthetők, és nem csak a támogatott régiók, a Log Analytics-munkaterületen.
>

Ha nem rendelkezik egy munkaterületet, létrehozhat egyet az alábbi módszerek egyikével:
* [Az Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Ha engedélyezi a figyelést egy egyetlen Azure-beli virtuális gép vagy virtuálisgép-méretezési csoportba az Azure Portalon, létrehozhat egy munkaterületet, a folyamat során.

A Log Analytics-munkaterületet az Azure Policy, az Azure PowerShell vagy Azure Resource Manager-sablonok használatával ipari méretekben eset, először a következő beállításokkal kell rendelkeznie:

* Telepítse a ServiceMap és InfrastructureInsights megoldásokat. Is elvégezheti a telepítést, biztosított egy Azure Resource Manager-sablon használatával vagy segítségével a **munkaterület konfigurálása** beállítás található a **első lépései** fülre.
* Konfigurálja a teljesítményszámlálók adatainak összegyűjtése a Log Analytics-munkaterületet.

A munkaterület az ipari méretekben forgatókönyv konfigurálásához segítségével konfigurálhatja az alábbi módszerek egyikével:

* [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)
* Az a **munkaterület konfigurálása** lehetőséget a virtuális gépek az Azure Monitor [szabályzati lefedettség](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) lap

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Az alábbi táblázat a virtuális gépek az Azure monitorban támogatott Windows- és Linux operációs rendszerek listája. Ebben a szakaszban később nyújtott teljes listáját, amelyek a fő- és alverzió Linux operációsrendszer-kiadás részleteit, és a kernel verziója támogatott.

|Operációs rendszer verziója |Teljesítmény |Maps |Állapot |
|-----------|------------|-----|-------|
|A Windows Server 2019 | X | X | X |
|A Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14.04, 16.04, 18.04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 11, 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> a teljesítmény funkció az Azure Monitor-beli virtuális gépek csak az Azure Monitor érhető el. Nem érhető el, közvetlenül a bal oldali ablaktáblán az Azure virtuális gépek használatakor.

>[!NOTE]
>Az alábbi adatokat a következőkre vonatkozik a Linux operációs rendszer támogatja:
> - Csak az alapértelmezett és az SMP Linux kernelű kiadások támogatottak.
> - Nestandardní kernel kiadások, mint például a Xen, és a fizikai cím bővítmény (fizikai) nem támogatottak az minden olyan Linux-disztribúció. A kiadási karakterláncot, például egy rendszer *2.6.16.21-0.8-xen* nem támogatott.
> - Egyéni kernelekkel, többek között a standard szintű kernelekkel, újrafordítások nem támogatottak.
> - CentOSPlus kernel használata támogatott.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus
| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15. * |
| Ubuntu 16.04.3 | kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 nagyvállalati Server

| Operációs rendszer verziója | Kernel verziója
|:--|:--|
|11 SP4 | 3.0.* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 vállalati kiszolgáló

| Operációs rendszer verziója | Kernel verziója
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |

### <a name="the-microsoft-dependency-agent"></a>A Microsoft Dependency agent

Virtuális gépek térkép funkció az Azure Monitor az adatok lekérése a Microsoft Dependency agent. A függőségi ügynök a Log Analytics-ügynököket a Log Analytics-kapcsolat támaszkodik. Ezért a rendszer a Log Analytics-ügynököket telepíteni és konfigurálni a függőségi ügynök kell rendelkeznie.

Engedélyezi az Azure Monitor-beli virtuális gépek egy Azure virtuális Gépen, vagy az ipari méretekben üzembe helyezési módszert használja, a felhasználói élményt részeként az ügynök telepítése az Azure virtuális gép függőségi ügynök bővítmény használatára van szükség.

Hibrid környezetben, töltse le és telepítse a függőségi ügynök két módszer egyikével: manuális, illetve egy automatikus telepítési módszer használatával, virtuális gépek, amelyek üzemeltetett Azure-on kívülről.

A következő táblázat ismerteti a térkép funkció támogatja a hibrid környezetben összekapcsolt forrásokról.

| Csatlakoztatott forrás | Támogatott | Leírás |
|:--|:--|:--|
| Windows-ügynökök | Igen | Mellett a [Log Analytics-ügynököket for Windows](../../azure-monitor/platform/log-analytics-agent.md), Windows-ügynökök a Microsoft Dependency agent szükséges. Operációs rendszerek teljes listáját lásd: [támogatott operációs rendszerek](#supported-operating-systems). |
| Linux-ügynökök | Igen | Mellett a [Linuxhoz készült Log Analytics-ügynök](../../azure-monitor/platform/log-analytics-agent.md), Linux-ügynökök a Microsoft Dependency agent szükséges. Operációs rendszerek teljes listáját lásd: [támogatott operációs rendszerek](#supported-operating-systems). |
| System Center Operations Manage felügyeleti csoport | Nem | |

A függőségi ügynök letölthető a következő helyekről:

| Fájl | Operációs rendszer | Verzió | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Engedélyezze, és az Azure monitorban funkcióhoz férhet hozzá a virtuális gépek kell a következő hozzáférési szerepköröket hozzárendelni:

- Az engedélyezéshez, rendelkeznie kell a *Log Analytics-közreműködő* szerepkör.

- Teljesítmény, egészségügyi, megtekintése, és adatokat, rendelkeznie kell a *Monitoring Reader* az Azure virtuális gép szerepkör. A Log Analytics-munkaterület-beli virtuális gépek az Azure Monitor kell konfigurálni.

A Log Analytics-munkaterülethez való hozzáférésének kapcsolatos további információkért lásd: [munkaterületeinek kezeléséhez](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Az Azure Monitor engedélyezése a virtuális gépek (előzetes verzió)

Az Azure Monitor-beli virtuális gépek, a következő táblázat ismerteti a következő módszerek egyikével engedélyeznie.

| Üzembe helyezés állapota | Módszer | Leírás |
|------------------|--------|-------------|
| Egyetlen Azure virtuális gép vagy virtuálisgép-méretezési csoportot | [Közvetlenül a virtuális gépről](vminsights-enable-single-vm.md) | Egyetlen Azure virtuális gép kiválasztásával engedélyezheti **Insights (előzetes verzió)** közvetlenül a virtuális gép vagy virtuálisgép-méretezési beállítása. |
| Több Azure virtuális gépek vagy virtuálisgép-méretezési csoportok | [Azure Policy](vminsights-enable-at-scale-policy.md) | Több Azure virtuális gépek az Azure Policy és elérhető szabályzatdefiníciók használatával engedélyezheti. |
| Több Azure virtuális gépek vagy virtuálisgép-méretezési csoportok | [Az Azure PowerShell vagy az Azure Resource Manager-sablonok](vminsights-enable-at-scale-powershell.md) | Több Azure virtuális gépek vagy virtuálisgép-méretezési csoportok között a megadott előfizetés vagy az erőforrás-csoport Azure PowerShell vagy az Azure Resource Manager-sablonok használatával engedélyezheti. |
| Hibrid felhő | [A hibrid környezet engedélyezése](vminsights-enable-hybrid-cloud.md) | Az adatközpontban vagy egyéb felhőalapú környezetek virtuális gépek vagy fizikai számítógépeken futó telepítheti. |

## <a name="performance-counters-enabled"></a>A teljesítményszámlálók engedélyezve

A virtuális gépek az Azure Monitor konfigurálása a Log Analytics-munkaterületet, amelyet használ a teljesítményszámlálók adatainak összegyűjtése. A következő táblázat felsorolja azokat az objektumokat és 60 másodpercenként gyűjtött teljesítményszámlálók.

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

## <a name="diagnostic-and-usage-data"></a>Diagnosztika és használati adatok

A Microsoft automatikusan gyűjt keresztül az Azure Monitor szolgáltatás használatának és teljesítményének adatait. A Microsoft ezeket az adatokat adja meg, és a minőségének, biztonságának és integritásának a szolgáltatás javítására használja. Pontos és hatékony hibaelhárítás képességeket biztosít, a térkép funkció adatait tartalmazza a szoftverek, például az operációs rendszer és verzió, IP-cím, DNS-nevet és munkaállomás neve konfigurációjával kapcsolatos információkat. A Microsoft nem gyűjt, neveket, címeket és egyéb kapcsolattartási adatait.

Az adatok gyűjtésével és használatával kapcsolatos további információkért tekintse meg a [Microsoft Online Services adatvédelmi nyilatkozata](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>További lépések

Most, hogy a figyelés engedélyezve van a virtuális gépet, ezeket az adatokat és az Azure Monitor-beli virtuális gépek elemzési érhető el. Az állapotfigyelő szolgáltatás használatával kapcsolatban lásd: [a virtuális gépek állapotának megtekintése az Azure Monitor](vminsights-health.md). Felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md).
