---
title: Wire Data megoldás a Log Analyticsben | Microsoft Docs
description: Átviteli adatok az összevont hálózati és a teljesítmény adatok Log Analytics-ügynökök számítógépeken. A hálózati adatok és a naplóadatok összevonása segít az adatok összevetésében.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 7f2ced1d6e5a7368fbf136d31889a763b3306e37
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50964046"
---
# <a name="wire-data-20-preview-solution-in-log-analytics"></a>Wire Data 2.0 (előzetes verzió) megoldás a Log Analyticsben

![Wire Data-szimbólum](./media/log-analytics-wire-data/wire-data2-symbol.png)

Átviteli adatok összevont hálózati és a teljesítmény származó adatokat a Log Analytics-ügynököket, beleértve a környezetében az Operations Manager által figyelt csatlakozó Windows és Linux rendszerű csatlakoztatott számítógépek. A hálózati adatok és az egyéb naplóadatok összevonása segít az adatok összevetésében.

Mellett a Log Analytics-ügynököket a Wire Data megoldást használja a Microsoft függőségi ügynökök telepítése a számítógépeken az informatikai infrastruktúra. A függőségi ügynökök monitorozzák a számítógépek által fogadott és küldött adatokat az [OSI-modell](https://en.wikipedia.org/wiki/OSI_model) szerinti 2. és 3. szintű hálózatokon, beleértve a különböző alkalmazott protokollokat és portokat. Az adatok ezután ügynökök használatával lesznek továbbítva a Log Analyticsbe.  

>[!NOTE]
>Ha a Service Map már üzembe helyezte, vagy a Service Map használatát fontolgatja, vagy [-beli virtuális gépek az Azure Monitor](../monitoring/monitoring-vminsights-overview.md), van egy új kapcsolat metrikák adatkészlet összegyűjtése és a Log Analytics, amely hasonló információt szolgáltat az átviteli adatok tárolása.

Alapértelmezés szerint a Log Analytics a processzor, a memória, a lemezek és a hálózat teljesítményadatait naplózza a Windows és Linux beépített számlálóival, valamint további, szabadon megadható teljesítményszámlálók segítségével. A hálózati és egyéb adatok gyűjtése valós időben történik az egyes ügynökökre vonatkozóan, beleértve a számítógép által használt alhálózatokat és alkalmazásszintű protokollokat.  A Wire Data a hálózati adatokat az alkalmazások szintjén kezeli, nem a TCP átviteli réteg szintjén.  A megoldás nem veszi figyelembe az önálló ACK-kat és SYN-eket.  Ha a kézfogás befejeződött, onnantól a kapcsolat élőnek számít és Csatlakoztatva jelölést kap. A kapcsolat addig marad élő, amíg mindkét oldal egyetért a szoftvercsatorna nyitva tartásában, és az adatok átvitele oda-vissza lehetséges.  Ha bármelyik oldal bezárja a kapcsolatot, a kapcsolat Leválasztva jelölést kap.  Ezért csak sikeresen elküldött csomagok által használt sávszélességet veszi számításba, az újraküldött vagy sikertelenül elküldött csomagok nem lesznek jelentve.

Ha már használta az [sFlow](http://www.sflow.org/)-t vagy valamilyen egyéb szoftvert a [Cisco NetFlow protokolljával](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), akkor a Wire Data statisztikái és adatai már ismerősek lesznek.

A beépített naplókeresési lekérdezések néhány típusa:

- Átviteli adatokat szolgáltató ügynökök
- Az átviteli adatokat szolgáltató ügynökök IP-címe
- Kimenő kommunikáció IP-címek szerint
- Az alkalmazásprotokollok által elküldött bájtok száma
- Egy alkalmazásszolgáltatás által elküldött bájtok száma
- A különböző protokollok által fogadott bájtok
- Az elküldött és fogadott bájtok teljes száma IP-verzió szerint
- A megbízhatóan mért kapcsolatok átlagos késése
- Számítógép-folyamatok, amelyek hálózati forgalmat kezdeményeztek vagy fogadtak
- Egy folyamat hálózati forgalmának mennyisége

Az átviteli adatok használatával történő kereséskor az adatokat szűrheti és csoportosíthatja, így megtekintheti a leggyakoribb ügynökök és a leggyakoribb protokollok információit. Emellett megtekintheti, hogy az egyes számítógépek (IP-címek vagy MAC-címek) mikor kommunikáltak egymással, mennyi ideig, és mennyi adatot küldtek. Tulajdonképpen metaadatokat tekinthet meg a hálózati forgalomról, a keresések alapján.

Mivel azonban metaadatokról van szó, ezek a részletes hibakereséshez nem feltétlenül lesznek hasznosak. A Log Analytics átviteli adatai nem rögzítik teljes körűen a hálózati adatokat.  A szolgáltatást nem mélyre menő, csomagszintű hibakereséshez tervezték. Az ügynök használatának előnye más adatgyűjtési módszerekkel szemben az, hogy nem kell berendezéseket telepíteni, újrakonfigurálni a hálózati kapcsolókat vagy elvégezni bármilyen összetett konfigurálási műveletet. A Wire Data egy egyszerű ügynökalapú megoldás – Ön telepíti az ügynököt egy számítógépen, az ügynök pedig monitorozza a saját hálózati forgalmát. Egy másik előny, hogy a felhasználó monitorozhatja a felhőszolgáltatókon, tárhelyszolgáltatókon vagy a Microsoft Azure-ban futó számítási feladatokat is, vagyis olyan helyeken, ahol nem a hálóréteg tulajdonosa.

## <a name="connected-sources"></a>Összekapcsolt források

A Wire Data a Microsoft függőségi ügynöktől kapja az adatokat. A függőségi ügynök attól függ, hogy a Log Analytics-ügynököket a Log Analytics-kapcsolatok. Ez azt jelenti, hogy egy kiszolgáló rendelkeznie kell a Log Analytics-ügynököket telepíteni és konfigurálni a függőségi ügynök. A következő táblázat ismerteti a Wire Data megoldás által támogatott csatlakoztatott forrásokat:

| **Csatlakoztatott forrás** | **Támogatott** | **Leírás** |
| --- | --- | --- |
| Windows-ügynökök | Igen | A Wire Data adatok elemez és gyűjt a Windows rendszerű ügynökszámítógépekről. <br><br> Mellett a [Log Analytics-ügynököket for Windows](log-analytics-agent-windows.md), Windows-ügynökök a Microsoft Dependency Agent szükséges. A támogatott operációsrendszer-verziók teljes listáját megtekintheti a [támogatott operációs rendszerek](../monitoring/monitoring-service-map-configure.md#supported-windows-operating-systems) szakaszban. |
| Linux-ügynökök | Igen | A Wire Data adatokat elemez és gyűjt a Linux rendszerű ügynökszámítógépekről.<br><br> Mellett a [Linuxhoz készült Log Analytics-ügynök](log-analytics-quick-collect-linux-computer.md), Linux-ügynökök a Microsoft Dependency Agent szükséges. A támogatott operációsrendszer-verziók teljes listáját megtekintheti a [támogatott operációs rendszerek](../monitoring/monitoring-service-map-configure.md#supported-linux-operating-systems) szakaszban. |
| System Center Operations Manage felügyeleti csoport | Igen | A Wire Data adatokat elemez és gyűjt az olyan Windows- és Linux-ügynököktől, amelyek egy csatlakoztatott [System Center Operations Manager felügyeleti csoporthoz](log-analytics-om-agents.md) tartoznak. <br><br> Ehhez közvetlen kapcsolat szükséges a System Center Operations Manager-ügynökszámítógép és a Log Analytics között. |
| Azure Storage-fiók | Nem | A Wire Data ügynökszámítógépekről gyűjt adatokat, így az Azure Storage-ből nem tud adatokat gyűjteni. |

Windows rendszeren a System Center Operations Manager és a Log Analytics egyaránt a Microsoft Monitoring Agent (MMA) segítségével gyűjti össze és továbbítja az adatokat. A környezettől függően az ügynököt a System Center Operations Manager ügynök, a Log Analytics-ügynököket, az MMA vagy a közvetlen ügynök nevezzük. A System Center Operations Manager és Log Analytics által biztosított MMA-verziók kis mértékben különböznek. Ezek a verziók jelenthetnek a Log Analyticsnek, a System Center Operations Managernek vagy mindkettőnek.

A Linuxhoz készült Log Analytics-ügynök Linux rendszeren gyűjt, és adatokat küld a Log Analytics. Átviteli adatok közvetlenül csatlakozik a Log Analytics-ügynökökkel kiszolgálókon, vagy olyan kiszolgálókra, amelyek a System Center Operations Manager felügyeleti csoportok keresztül csatlakozik, a Log Analytics használható.

Maga a függőségi ügynök nem közvetít adatokat, ezért nem igényli a tűzfalak vagy portok semmilyen módosítását. Az átviteli adatok mindig továbbított adatok által a Log Analytics-ügynököket a Log Analyticsbe, vagy közvetlenül a Log Analytics-átjárón.

![ügynök diagram](./media/log-analytics-wire-data/agents.png)

Ha a System Center Operations Managert használja és a felügyeleti csoportja csatlakoztatva van a Log Analyticshez:

- Nincs szükség további konfigurációra, ha a System Center Operations Manager hozzáfér az internethez, hogy csatlakozni tudjon a Log Analyticshez.
- A Log Analytics-átjáró használata a System Center Operations Manager, ha a System Center Operations Manager-ügynökök az interneten keresztül nem tud hozzáférni a Log Analytics konfigurálása kell.

Ha a Windows vagy Linux rendszerű számítógépek közvetlenül nem tud csatlakozni a szolgáltatáshoz, konfigurálása a Log Analytics-ügynököket a Log Analytics használata a Log Analytics-átjáró csatlakozni szeretne. A Log Analytics átjáróhoz letöltheti a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Előfeltételek

- Szükség van a [Betekintések és elemzés](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) megoldásra.
- Ha a Wire Data előző verzióját használja, először el kell távolítania azt. Az eredeti Wire Data megoldással rögzített adatok azonban továbbra is elérhetők lesznek a Wire Data 2.0-ban és a naplókeresésben.
- A függőségi ügynök telepítéséhez vagy eltávolításához rendszergazdai jogosultság szükséges.
- A függőségi ügynököt egy 64 bites operációs rendszert futtató számítógépre kell telepíteni.

### <a name="operating-systems"></a>Operációs rendszerek

Az alábbi táblázat a függőségi ügynök által támogatott operációs rendszereket tartalmazza: A Wire Data semmilyen operációs rendszer esetében nem támogatja a 32 bites architektúrákat.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Asztali Windows

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux és Oracle Linux (RHEL Kernellel)

- Csak az alapértelmezett és az SMP Linux kernelű kiadások támogatottak.
- A nem szabványos kernelű kiadások, például a PAE és a Xen nem támogatottak semmilyen Linux-disztribúció esetén. Például ha egy rendszer kiadási sztringje _2.6.16.21-0.8-xen_, az a rendszer nem támogatott.
- Az egyéni kernelek, beleértve a standard kernelek újrafordításait, nem támogatottak.
- A CentOSPlus kernel szintén nem támogatott.
- Az Oracle Unbreakable Enterprise Kernellel (UEK) a cikk későbbi szakasza foglalkozik.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| **Operációs rendszer verziója** | **Kernel verziója** |
| --- | --- |
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| **Operációs rendszer verziója** | **Kernel verziója** |
| --- | --- |
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5

| **Operációs rendszer verziója** | **Kernel verziója** |
| --- | --- |
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398 <br> 2.6.18-400 <br>2.6.18-402 <br>2.6.18-404 <br>2.6.18-406 <br> 2.6.18-407 <br> 2.6.18-408 <br> 2.6.18-409 <br> 2.6.18-410 <br> 2.6.18-411 <br> 2.6.18-412 <br> 2.6.18-416 <br> 2.6.18-417 <br> 2.6.18-419 |

#### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux és Unbreakable Enterprise Kernel

#### <a name="oracle-linux-6"></a>Oracle Linux 6

| **Operációs rendszer verziója** | **Kernel verziója** |
| --- | --- |
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| **Operációs rendszer verziója** | **Kernel verziója** |
| --- | --- |
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11

| **Operációs rendszer verziója** | **Kernel verziója** |
| --- | --- |
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10

| **Operációs rendszer verziója** | **Kernel verziója** |
| --- | --- |
| 10 SP4 | 2.6.16.60 |

#### <a name="dependency-agent-downloads"></a>Függőségi ügynök letöltései

| **Fájl** | **OS** | **Verzió** | **SHA-256** |
| --- | --- | --- | --- |
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |



## <a name="configuration"></a>Konfiguráció

A Wire Data megoldásnak a munkaterületekhez való konfigurálásához végezze el az alábbi lépéseket:

1. Engedélyezze az Activity Log Analytics megoldást az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) felületéről vagy a [Log Analytics-megoldások hozzáadása a megoldástárból](../monitoring/monitoring-solutions.md) című témakörben leírt eljárást követve.
2. Telepítse a függőségi ügynököt az összes olyan számítógépen, amelyről adatokat kíván gyűjteni. A függőségi ügynök képesek a közvetlen szomszédaikkal való kapcsolatok monitorozására, így lehetséges, hogy nem kell minden egyes számítógépre ügynököt telepíteni.

> [!NOTE]
> A Wire Data előző verzióját nem lehet hozzáadni új munkaterületekhez. Ha engedélyezve van az eredeti Wire Data megoldás, azt továbbra is használhatja. Azonban a Wire Data 2.0 használatához először el kell távolítani az eredeti verziót.
> 
### <a name="install-the-dependency-agent-on-windows"></a>A függőségi ügynök telepítése Windows rendszeren

Az ügynök telepítéséhez vagy eltávolításához rendszergazdai jogosultság szükséges.

A függőségi ügynök a Windows rendszerű számítógépekre az InstallDependencyAgent-Windows.exe fájllal telepíthető. Ha a végrehajtható fájlt bármilyen paraméter nélkül futtatja, akkor elindul egy varázsló, amelyet követve párbeszédes formában telepítheti az ügynököt.

A függőségi ügynököt az alábbi lépésekkel telepítheti minden Windows rendszerű számítógépre:

1. A Log Analytics-ügynököket leírt lépések végrehajtásával telepítse [adatok gyűjtése saját környezetben futtatott Windows-számítógépekről](log-analytics-agent-windows.md).
2. Töltse le a Windows függőségi ügynököt az előző szakaszban található hivatkozás használatával, majd futtassa a következő paranccsal: `InstallDependencyAgent-Windows.exe`
3. Az ügynök telepítéséhez kövesse a varázslót.
4. Ha a függőségi ügynök nem indul el, tekintse meg a naplókat a hibával kapcsolatos részletes információért. Windows-ügynökök esetén a naplózási könyvtár a következő: %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Windows parancssor

A parancssorból való telepítéshez a következő táblában leírt paraméterek használhatók. A telepítésjelzők listájának megtekintéséhez futtassa a telepítőt a /? jelzővel, a következő módon.

InstallDependencyAgent-Windows.exe /?

| **Jelző** | **Leírás** |
| --- | --- |
| <code>/?</code> | A parancssori kapcsolók listájának lekérése. |
| <code>/S</code> | Beavatkozás nélküli telepítés a felhasználónak szóló üzenetek nélkül. |

A Windows függőségi ügynök fájljai alapértelmezés szerint a következő könyvtárba kerülnek: C:\Program Files\Microsoft Dependency Agent.

### <a name="install-the-dependency-agent-on-linux"></a>A függőségi ügynök telepítése Linux rendszeren

Az ügynök telepítéséhez vagy eltávolításához gyökérszintű hozzáférés szükséges.

A függőségi ügynök Linux rendszerű számítógépekre az InstallDependencyAgent-Linux64.bin fájllal telepíthető, amely egy önkicsomagoló bináris héjszkript. A fájlt futtathatja az _sh_ használatával, vagy hozzáadhat végrehajtási engedélyeket a fájlhoz.

A függőségi ügynököt az alábbi lépésekkel telepítheti minden Linux rendszerű számítógépre:

1. A Log Analytics-ügynököket leírt lépések végrehajtásával telepítse [adatok gyűjtése saját környezetben futtatott Linuxos számítógépekről](log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Töltse le a Linux függőségi ügynököt az előző szakaszban található hivatkozás használatával, majd telepítse gyökérként a következő paranccsal: sh InstallDependencyAgent-Linux64.bin
3. Ha a függőségi ügynök nem indul el, tekintse meg a naplókat a hibával kapcsolatos részletes információért. A Linux-ügynökökön a naplókönyvtár a következő: /var/opt/microsoft/dependency-agent/log.

A telepítésjelzők listájának megtekintéséhez futtassa a `-help` jelzővel rendelkező telepítőprogramot az alábbiak szerint.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Jelző** | **Leírás** |
| --- | --- |
| <code>-help</code> | A parancssori kapcsolók listájának lekérése. |
| <code>-s</code> | Beavatkozás nélküli telepítés a felhasználónak szóló üzenetek nélkül. |
| <code>--check</code> | Az engedélyek és az operációs rendszer ellenőrzése, az ügynök telepítése nélkül. |

A függőségi ügynök fájljai az alábbi könyvtárakba kerülnek:

| **Fájlok** | **Hely** |
| --- | --- |
| Alapvető fájlok | /opt/microsoft/dependency-agent |
| Naplófájlok | /var/opt/microsoft/dependency-agent/log |
| Konfigurációs fájlok | /etc/opt/microsoft/dependency-agent/config |
| Szolgáltatás végrehajtható fájljai | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Bináris tárolófájlok | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Telepítési példaszkriptek

Egy szkript használata segíthet, ha egyszerre több kiszolgálóra szeretné könnyen telepíteni a függőségi ügynököt. Az alábbi példaszkriptekkel letöltheti és telepítheti a függőségi ügynököt Windows vagy Linux rendszerű számítógépre.

#### <a name="powershell-script-for-windows"></a>PowerShell-szkript Windowshoz

```PowerShell

Invoke-WebRequest &quot;https://aka.ms/dependencyagentwindows&quot; -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Héjszkript Linuxhoz

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Célállapot-konfiguráló

A függőségi ügynök Desired State Configuration segítségével történő telepítéséhez használhatja az xPSDesiredStateConfiguration modult és egy, az alábbihoz hasonló kódrészletet:

```
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = &quot;C:\InstallDependencyAgent-Windows.exe&quot;



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = &quot;https://aka.ms/dependencyagentwindows&quot;

        DestinationPath = $DAPackageLocalPath

        DependsOn = &quot;[Package]OI&quot;

    }

    xPackage DA

    {

        Ensure=&quot;Present&quot;

        Name = &quot;Dependency Agent&quot;

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = &quot;&quot;

        InstalledCheckRegKey = &quot;HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent&quot;

        InstalledCheckRegValueName = &quot;DisplayName&quot;

        InstalledCheckRegValueData = &quot;Dependency Agent&quot;

    }

}

```
### <a name="uninstall-the-dependency-agent"></a>A függőségi ügynök eltávolítása

Az alábbi szakaszok segítséget nyújtanak a függőségi ügynök eltávolításában.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>A függőségi ügynök eltávolítása Windows rendszeren

A rendszergazda Windows esetén a Vezérlőpulton keresztül törölheti a függőségi ügynököt.

Egy rendszergazda a következő fájl futtatásával is eltávolíthatja a függőségi ügynököt: %Programfiles%\Microsoft Dependency Agent\Uninstall.exe.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>A függőségi ügynök eltávolítása Linux rendszeren

A függőségi ügynök Linux rendszerről történő teljes eltávolításához el kell távolítania az ügynököt és az ügynökkel együtt automatikusan telepített összekötőt. Az alábbi egyszerű parancs futtatásával mindkettőt eltávolíthatja:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Felügyeleti csomagok

Ha az átviteli adatok aktiválva vannak a Log Analytics-munkaterületen, a rendszer egy 300 KB méretű felügyeleti csomagot küld a munkaterület Windows-kiszolgálóinak. Ha System Center Operations Manager-ügynököt használ egy [csatlakoztatott felügyeleti csoportban](log-analytics-om-agents.md), a Függőségfigyelő felügyeleti csomag a System Center Operations Managerből lesz telepítve. Ha az ügynökök közvetlenül kapcsolódnak, a Log Analytics biztosítja a felügyeleti csomagot.

A felügyeleti csomag neve Microsoft.IntelligencePacks.ApplicationDependencyMonitor. A következő helyre írja a rendszer: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. A felügyeleti csomag az alábbi adatforrást használja: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>A megoldás használata

**A megoldás telepítése és konfigurálása**

A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

- A Wire Data megoldás a Windows Server 2012 R2, Windows 8.1 és újabb operációs rendszert futtató számítógépekről gyűjt adatokat.
- A Microsoft .NET-keretrendszer 4.0-s vagy újabb verziójával kell rendelkeznie azoknak a számítógépeknek, amelyekről átviteli adatokat szeretne gyűjteni.
- A Log Analytics-munkaterülethez adja hozzá a Wire Data megoldást. Ehhez kövesse a [Log Analytics-megoldások hozzáadása a megoldástárból](../monitoring/monitoring-solutions.md) című témakörben leírt eljárást. Nincs szükség további konfigurációra.
- Egy adott megoldás átviteli adatainak megtekintéséhez már rendelkeznie kell a megoldással a munkaterületen.

Miután telepítette az ügynököket és telepíti a megoldást, a munkaterületen megjelenik a Wire Data 2.0 csempéje.

![Wire Data csempe](./media/log-analytics-wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>A Wire Data 2.0 megoldás használata

Az Azure Portalon a Log Analytics-munkaterület **Áttekintés** területén kattintson a **Wire Data 2.0** csempére az Átviteli adatok irányítópult megnyitásához. Az irányítópulton az alábbi táblázatban felsorolt panelek találhatók. Minden panelen legfeljebb 10 olyan elem jelenik meg, amely megfelel a panel hatóköri és időtartományi kritériumainak. A panel alján található **Az összes megtekintése** elemre vagy a panel fejlécére kattintva az összes rekordot megjelenítő keresést végezhet a naplóban.

| **Panel** | **Leírás** |
| --- | --- |
| Hálózati forgalmat rögzítő ügynökök | A hálózati forgalmat rögzítő ügynökök számát és a forgalmat rögzítő első 10 számítógép listáját jeleníti meg. Kattintson a számra a következőre vonatkozó naplókeresés futtatásához: <code>Type:WireData &#124; measure Sum(TotalBytes) by Computer &#124; top 500000</code>. Kattintson a listában található egyik számítógépre egy olyan naplókeresés futtatásához, amely a rögzített bájtok számát adja vissza. |
| Helyi alhálózatok | Az ügynök által felderített helyi alhálózatok számát mutatja.  Kattintson a számra a következőre vonatkozó naplókeresés futtatásához: <code>Type:WireData &#124; Measure Sum(TotalBytes) by LocalSubnet</code>. Ez az alhálózatokat és az egyes alhálózatokon átküldött bájtok számát jeleníti meg. Kattintson a listában található alhálózatra egy olyan naplókeresés futtatásához, amely az alhálózaton küldött bájtok teljes számát adja vissza. |
| Alkalmazásszintű protokollok | Az ügynökök által felderített, használatban lévő alkalmazásszintű protokollok számát jeleníti meg. Kattintson a számra a következőre vonatkozó naplókeresés futtatásához: <code>Type:WireData &#124; Measure Sum(TotalBytes) by ApplicationProtocol</code>. Kattintson a protokollra egy olyan naplókeresés futtatásához, amely a protokoll használatával küldött bájtok számát adja vissza. |

![Átviteli adatok irányítópult](./media/log-analytics-wire-data/wire-data-dash.png)

A **Hálózati forgalmat rögzítő ügynökök** panellel megállapíthatja, mekkora hálózati sávszélességet használnak fel a számítógépek. A panel segítségével könnyen megtalálhatja a hálózat _legforgalmasabb_ számítógépét. Előfordulhat, hogy ezek a számítógépek túlterheltek, rendellenesen működnek vagy az átlagosnál több hálózati erőforrást használnak.

![naplóbeli keresés példája](./media/log-analytics-wire-data/log-search-example01.png)

Ehhez hasonlóan a **Helyi alhálózatok** panel segítségével megállapíthatja, mekkora hálózati forgalom halad át az alhálózatokon. A felhasználók gyakran az alkalmazásaikhoz tartozó kritikus területek körül adnak meg alhálózatokat. Ez a panel ezekbe a területekbe kínál betekintést.

![naplóbeli keresés példája](./media/log-analytics-wire-data/log-search-example02.png)

Az **Alkalmazásszintű protokollok** panel hasznos, mivel fontos lehet annak ismerete, hogy mely protokollok vannak használatban. Lehetséges például, hogy arra számít, hogy az SSH nincs használatban a hálózati környezetben. A panelen megjelenő információk ezt gyorsan megerősíthetik vagy megcáfolhatják.

![naplóbeli keresés példája](./media/log-analytics-wire-data/log-search-example03.png)

Ebben a példában elemezheti az SSH részleteit, és például megtekintheti, melyik számítógépek használják az SSH-t, valamint egyéb kommunikációs részleteket is megismerhet.

![sh keresési eredmények](./media/log-analytics-wire-data/ssh-details.png)

Ez akkor is hasznos, ha szeretné tudni, hogy a hálózati forgalom idővel növekszik vagy csökken. Például nem árt tudnia, vagy említésre méltónak találhatja, ha egy alkalmazás által továbbított adatok mennyisége növekszik.

## <a name="input-data"></a>Bemeneti adatok

A Wire Data az engedélyezett ügynökök használatával metaadatokat gyűjt a hálózati forgalomról. Minden ügynök körülbelül 15 másodpercenként küld adatokat.

## <a name="output-data"></a>Kimeneti adatok

A bemeneti adatok minden típusához létrejön egy _WireData_ típusú rekord. A WireData-rekordok tulajdonságai az alábbi táblázatban láthatók:

| Tulajdonság | Leírás |
|---|---|
| Computer | A számítógép neve, ahol az adatgyűjtés történt |
| TimeGenerated | A rekord létrehozásának időpontja |
| LocalIP | A helyi számítógép IP-címe |
| SessionState | Csatlakoztatva vagy leválasztva |
| ReceivedBytes | A fogadott bájtok mennyisége |
| ProtocolName | A használt hálózati protokoll neve |
| IPVersion | IP-cím verziója |
| Irány | Bemeneti vagy kimeneti |
| MaliciousIP | Ismert kártevő forrás IP-címe |
| Severity | Gyanús kártevő súlyossága |
| RemoteIPCountry | Az ország, ahol a távoli IP-cím található |
| ManagementGroupName | Az Operations Manager felügyeleti csoportjának neve |
| SourceSystem | A gyűjtött adatok forrása |
| SessionStartTime | A munkamenet kezdési időpontja |
| SessionEndTime | A munkamenet befejezési időpontja |
| LocalSubnet | Az alhálózat, ahol az adatgyűjtés történt |
| LocalPortNumber | Helyi portszám |
| RemoteIP | A távoli számítógép által használt távoli IP-cím |
| RemotePortNumber | A távoli IP-cím által használt portszám |
| SessionID | A két IP-cím közötti kommunikációs munkameneteket azonosító egyedi érték |
| SentBytes | Az elküldött bájtok száma |
| TotalBytes | A munkamenet során elküldött bájtok száma összesen |
| ApplicationProtocol | A használt hálózati protokoll típusa   |
| ProcessID | Windows-folyamat azonosítója |
| ProcessName | A folyamat elérési útja és neve |
| RemoteIPLongitude | IP-cím hosszúsági értéke |
| RemoteIPLatitude | IP-cím szélességi értéke |


## <a name="next-steps"></a>További lépések

- [Keresés a naplókban](log-analytics-log-search.md) az átviteli adatokhoz kapcsolódó részletes keresési rekordok megtekintéséhez.
