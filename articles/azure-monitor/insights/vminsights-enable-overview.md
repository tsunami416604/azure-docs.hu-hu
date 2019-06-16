---
title: Az Azure Monitor engedélyezése (előzetes verzió) virtuális gépek – áttekintés |} A Microsoft Docs
description: Ismerje meg, hogyan helyezheti üzembe, és az Azure Monitor konfigurálása virtuális gépek számára. Ismerje meg a rendszerkövetelményeket.
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
ms.openlocfilehash: 3f93318dedb8a4667d32bcc97eb6a697ccebfcc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122574"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Az Azure Monitor engedélyezése (előzetes verzió) virtuális gépek – áttekintés

Ez a cikk áttekintést az Azure Monitor beállítása virtuális gépekhez elérhető lehetőségeket. A virtuális gépek az Azure Monitor használatával a figyelő állapotát és teljesítményét. Azure-beli virtuális gépek (VM) rendszeren futó alkalmazások függőségeinek észlelése és a virtuálisgép-méretezési csoportok, a helyszíni virtuális gépet, vagy egy másik felhőalapú környezetben futó virtuális gépek.  

Az Azure Monitor beállítása virtuális gépekhez:

* Engedélyezze egy egyetlen Azure virtuális gép vagy virtuálisgép-méretezési csoportot kiválasztásával **Insights (előzetes verzió)** közvetlenül a virtuális gép vagy virtuálisgép-méretezési csoport a beállítása.
* Engedélyezzen két vagy több Azure virtuális gépek és virtuálisgép-méretezési csoportok az Azure Policy használatával. Ez a módszer biztosítja, hogy a meglévő és új virtuális gép és a méretezési csoportok, a szükséges függőségek telepítése és megfelelően konfigurálva. Nem kompatibilis virtuális gépek és a méretezési csoportok jelenti, így eldöntheti, hogy engedélyezheti őket, és elháríthatja a.
* Engedélyezzen két vagy több Azure virtuális gépek vagy virtuálisgép-méretezési csoportok között egy adott előfizetésen vagy erőforráscsoporton PowerShell használatával.
* Engedélyezze a virtuális gépek, virtuális gépeket vagy a vállalati hálózathoz vagy egyéb felhőalapú környezetben futó fizikai számítógépek figyelése az Azure Monitor.

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
>Azure-beli virtuális gépek régiókba is telepítheti. Ezek a virtuális gépek nem korlátozódik a Log Analytics-munkaterület által támogatott régiók.
>

Ha nem rendelkezik egy munkaterületet, létrehozhat egyet ezek az erőforrások egyikének használatával:
* [Az Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Bár Ön engedélyezni a figyelést egy egyetlen Azure virtuális gép vagy virtuálisgép-méretezési csoportot az Azure Portalon is létrehozhat egy munkaterületet.

Állítsa be egy ipari méretekben forgatókönyvet használó Azure Policy, az Azure PowerShell vagy az Azure Resource Manager-sablonok, a Log Analytics-munkaterület:

* Telepítse a ServiceMap és InfrastructureInsights megoldásokat. A telepítést a megadott Azure Resource Manager-sablon használatával. Vagy a **Ismerkedés** lapon jelölje be **munkaterület konfigurálása**.
* Konfigurálja a teljesítményszámlálók adatainak összegyűjtése a Log Analytics-munkaterületet.

A munkaterület az ipari méretekben forgatókönyv konfigurálásához használja a következő módszerek egyikét:

* Használat [az Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* A virtuális gépek az Azure Monitor [ **szabályzati lefedettség** ](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) lapon jelölje be **munkaterület konfigurálása**. 

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Az alábbi táblázat felsorolja a Windows és Linux operációs rendszerek, amelyek az Azure-beli virtuális gépek használatát támogatja. Később ebben a szakaszban találja a teljes listát, amely részletesen, a fő- és alverzió Linux operációs rendszer kiadási és a kernel verziója támogatott.

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

<sup>1</sup> a teljesítmény funkció az Azure Monitor-beli virtuális gépek csak az Azure Monitor érhető el. Nem érhető el az Azure virtuális gépek a bal oldali panelen.

>[!NOTE]
>A Linux operációs rendszer:
> - Csak az alapértelmezett és az SMP Linux kernelű kiadások támogatottak.
> - Nestandardní kernel kiadások, mint például a Xen, és a fizikai cím bővítmény (fizikai) nem támogatottak az minden olyan Linux-disztribúció. A kiadási karakterláncot, például egy rendszer *2.6.16.21-0.8-xen* nem támogatott.
> - Egyéni kernelekkel, beleértve a szabványos kernelekkel, elérte az újrafordítások nem támogatottak.
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

#### <a name="centosplus"></a>CentOSPlus
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

A leképezés funkció a virtuális gépek az Azure Monitor az adatok lekérése a Microsoft Dependency agent. A függőségi ügynök a Log Analytics-ügynököket a Log Analytics-kapcsolat támaszkodik. Így a rendszer a Log Analytics-ügynököket telepíteni és konfigurálni a függőségi ügynök kell rendelkeznie.

Engedélyezi az Azure Monitor-beli virtuális gépek egy Azure virtuális Gépen, vagy az ipari méretekben üzembe helyezési módszert használja, az Azure virtuális gép függőségi ügynök bővítmény használatával telepítse az ügynököt a felhasználói élményt részeként.

Hibrid környezetben töltse le, és a függőségi ügynök manuális telepítése. Ha a virtuális gépek Azure-on kívülről, egy automatikus központi telepítési módszert használja.

A következő táblázat ismerteti a térkép funkció támogatja a hibrid környezetben összekapcsolt forrásokról.

| Csatlakoztatott forrás | Támogatott | Leírás |
|:--|:--|:--|
| Windows-ügynökök | Igen | Az a [Log Analytics-ügynököket for Windows](../../azure-monitor/platform/log-analytics-agent.md), Windows-ügynökök kell a függőségi ügynököt. További információkért lásd: [támogatott operációs rendszerek](#supported-operating-systems). |
| Linux-ügynökök | Igen | Az a [Linuxhoz készült Log Analytics-ügynök](../../azure-monitor/platform/log-analytics-agent.md), Linux-ügynökök kell a függőségi ügynököt. További információkért lásd: [támogatott operációs rendszerek](#supported-operating-systems). |
| System Center Operations Manage felügyeleti csoport | Nem | |

Letöltheti a függőségi ügynök ezekről a helyekről:

| Fájl | Operációs rendszer | Verzió | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Engedélyezze, és az Azure monitorban funkcióhoz férhet hozzá a virtuális gépek kell rendelkeznie a *Log Analytics-közreműködő* szerepkör. Teljesítmény, egészségügyi, megtekintése, és adatokat, rendelkeznie kell a *olvasó figyelése* az Azure virtuális gép szerepkör. A Log Analytics-munkaterület-beli virtuális gépek az Azure Monitor kell konfigurálni.

A Log Analytics-munkaterülethez való hozzáférésének kapcsolatos további információkért lásd: [munkaterületeinek kezeléséhez](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Az Azure Monitor engedélyezése a virtuális gépek (előzetes verzió)

Engedélyezze az Azure Monitor-beli virtuális gépek a táblázatban leírt módszerek egyikével:

| Üzembe helyezés állapota | Módszer | Leírás |
|------------------|--------|-------------|
| Egyetlen Azure virtuális gép vagy virtuálisgép-méretezési csoportot | [Engedélyezze a virtuális gépről](vminsights-enable-single-vm.md) | Egyetlen Azure virtuális gép kiválasztásával engedélyezheti **Insights (előzetes verzió)** közvetlenül a virtuális gép vagy virtuálisgép-méretezési beállítása. |
| Több Azure virtuális gépek vagy virtuálisgép-méretezési csoportok | [Az Azure Policy segítségével engedélyezése](vminsights-enable-at-scale-policy.md) | Több Azure virtuális gépeken elérhető szabályzatdefiníciók és az Azure Policy használatával engedélyezheti. |
| Több Azure virtuális gépek vagy virtuálisgép-méretezési csoportok | [Azure PowerShell vagy az Azure Resource Manager-sablonokkal engedélyezése](vminsights-enable-at-scale-powershell.md) | Azure PowerShell vagy az Azure Resource Manager-sablonok használatával engedélyezheti több Azure virtuális gépek vagy virtuálisgép-méretezési csoportok között egy adott előfizetésen vagy erőforráscsoporton. |
| Hibrid felhő | [A hibrid környezet engedélyezése](vminsights-enable-hybrid-cloud.md) | Az adatközpontban vagy egyéb felhőalapú környezetek virtuális gépek vagy fizikai számítógépeken futó telepítheti. |

## <a name="performance-counters-enabled"></a>A teljesítményszámlálók engedélyezve 

A virtuális gépek az Azure Monitor konfigurálása a Log Analytics-munkaterületet, amelyet használ a teljesítményszámlálók adatainak összegyűjtése. Az alábbi táblázatok tartalmazzák azokat az objektumokat és 60 másodpercenként gyűjtött teljesítményszámlálók.

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

A Microsoft automatikusan gyűjt keresztül az Azure Monitor szolgáltatás használatának és teljesítményének adatait. A Microsoft ezeket az adatokat a minőségének, biztonságának és integritásának a szolgáltatás javítására használja. 

Adja meg a pontos és hatékony hibaelhárítási képességeket kínál, a térkép funkció a szoftver konfigurációjára vonatkozó adatokat tartalmaz. Az adatok biztosít adatokat, például az operációs rendszer és verzió, IP-cím, DNS-nevet és munkaállomás nevét. A Microsoft nem gyűjt, neveket, címeket és egyéb kapcsolattartási adatait.

Az adatok gyűjtésével és használatával kapcsolatos további információkért tekintse meg a [Microsoft Online Services adatvédelmi nyilatkozata](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Most, hogy engedélyezte, hogy a virtuális gép figyelése, elemezhetők az Azure Monitor-beli virtuális gépek monitoringadatokat.

## <a name="next-steps"></a>További lépések

Az állapotfigyelő szolgáltatás használatával kapcsolatban lásd: [a virtuális gépek állapotának megtekintése az Azure Monitor](vminsights-health.md). Felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md).
