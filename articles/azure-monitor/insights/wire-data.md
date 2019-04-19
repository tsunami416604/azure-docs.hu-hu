---
title: Vezeték-Data-megoldás az Azure Monitor |} A Microsoft Docs
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
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: magoedte
ms.openlocfilehash: d295a5a7eae2bdc7983e7271aa11bce1840b92dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58882072"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Az Azure monitorban Wire Data 2.0 (előzetes verzió) megoldás

![Wire Data-szimbólum](media/wire-data/wire-data2-symbol.png)

Átviteli adatok összevont hálózati és a teljesítmény származó adatokat a Log Analytics-ügynököket, beleértve a környezetében az Operations Manager által figyelt csatlakozó Windows és Linux rendszerű csatlakoztatott számítógépek. A hálózati adatok és az egyéb naplóadatok összevonása segít az adatok összevetésében.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Mellett a Log Analytics-ügynököket a Wire Data megoldást használja a Microsoft függőségi ügynökök telepítése a számítógépeken az informatikai infrastruktúra. A függőségi ügynökök monitorozzák a számítógépek által fogadott és küldött adatokat az [OSI-modell](https://en.wikipedia.org/wiki/OSI_model) szerinti 2. és 3. szintű hálózatokon, beleértve a különböző alkalmazott protokollokat és portokat. Adatok majd küld az Azure Monitor használatával ügynökök.  

>[!NOTE]
>Ha a Service Map már üzembe helyezte, vagy a Service Map használatát fontolgatja, vagy [Azure Monitor-beli virtuális gépek](../../azure-monitor/insights/vminsights-overview.md), van egy új kapcsolat metrikák adatkészlet gyűjtse össze ő, majd az Azure monitorban, amely hasonló információt szolgáltat az átviteli adatok tárolására.

Alapértelmezés szerint az Azure Monitor naplózza a CPU, memória, lemez és hálózati teljesítménnyel adatai a beépített Windows és Linux rendszerű számlálók, valamint más teljesítményszámlálók, Ön által megadott adatokat. A hálózati és egyéb adatok gyűjtése valós időben történik az egyes ügynökökre vonatkozóan, beleértve a számítógép által használt alhálózatokat és alkalmazásszintű protokollokat.  A Wire Data a hálózati adatokat az alkalmazások szintjén kezeli, nem a TCP átviteli réteg szintjén.  A megoldás nem veszi figyelembe az önálló ACK-kat és SYN-eket.  Ha a kézfogás befejeződött, onnantól a kapcsolat élőnek számít és Csatlakoztatva jelölést kap. A kapcsolat addig marad élő, amíg mindkét oldal egyetért a szoftvercsatorna nyitva tartásában, és az adatok átvitele oda-vissza lehetséges.  Ha bármelyik oldal lezárja a kapcsolatot, leválasztott van megjelölve.  Ezért csak sikeresen elküldött csomagok által használt sávszélességet veszi számításba, az újraküldött vagy sikertelenül elküldött csomagok nem lesznek jelentve.

Ha már használta az [sFlow](http://www.sflow.org/)-t vagy valamilyen egyéb szoftvert a [Cisco NetFlow protokolljával](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), akkor a Wire Data statisztikái és adatai már ismerősek lesznek.

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

Mivel azonban metaadatokról van szó, ezek a részletes hibakereséshez nem feltétlenül lesznek hasznosak. Átviteli adatok az Azure monitorban nem egy teljes rögzítési hálózati adatforgalmat.  A szolgáltatást nem mélyre menő, csomagszintű hibakereséshez tervezték. Az ügynök képest más gyűjtési módszerek használatának előnye, hogy nem kell telepíteni a berendezések, konfigurálja újra a hálózati kapcsolókon vagy bonyolult konfigurációk végrehajtása. A Wire Data egy egyszerű ügynökalapú megoldás – Ön telepíti az ügynököt egy számítógépen, az ügynök pedig monitorozza a saját hálózati forgalmát. Egy másik előny, hogy a felhasználó monitorozhatja a felhőszolgáltatókon, tárhelyszolgáltatókon vagy a Microsoft Azure-ban futó számítási feladatokat is, vagyis olyan helyeken, ahol nem a hálóréteg tulajdonosa.

## <a name="connected-sources"></a>Összekapcsolt források

A Wire Data a Microsoft függőségi ügynöktől kapja az adatokat. A függőségi ügynök attól függ, hogy a kapcsolatok az Azure monitornak a Log Analytics-ügynököket. Ez azt jelenti, hogy egy kiszolgáló rendelkeznie kell a Log Analytics-ügynököket telepíteni és konfigurálni a függőségi ügynök. A következő táblázat ismerteti a Wire Data megoldás által támogatott csatlakoztatott forrásokat:

| **Csatlakoztatott forrás** | **Támogatott** | **Leírás** |
| --- | --- | --- |
| Windows-ügynökök | Igen | A Wire Data adatok elemez és gyűjt a Windows rendszerű ügynökszámítógépekről. <br><br> Mellett a [Log Analytics-ügynököket for Windows](../../azure-monitor/platform/agent-windows.md), Windows-ügynökök a Microsoft Dependency Agent szükséges. A támogatott operációsrendszer-verziók teljes listáját megtekintheti a [támogatott operációs rendszerek](../../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) szakaszban. |
| Linux-ügynökök | Igen | A Wire Data adatokat elemez és gyűjt a Linux rendszerű ügynökszámítógépekről.<br><br> Mellett a [Linuxhoz készült Log Analytics-ügynök](../../azure-monitor/learn/quick-collect-linux-computer.md), Linux-ügynökök a Microsoft Dependency Agent szükséges. A támogatott operációsrendszer-verziók teljes listáját megtekintheti a [támogatott operációs rendszerek](../../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) szakaszban. |
| System Center Operations Manage felügyeleti csoport | Igen | A Wire Data adatokat elemez és gyűjt az olyan Windows- és Linux-ügynököktől, amelyek egy csatlakoztatott [System Center Operations Manager felügyeleti csoporthoz](../../azure-monitor/platform/om-agents.md) tartoznak. <br><br> A System Center Operations Manager ügynök számítógépről közvetlenül kapcsolódik az Azure Monitor megadása kötelező. |
| Azure Storage-fiók | Nem | A Wire Data ügynökszámítógépekről gyűjt adatokat, így az Azure Storage-ből nem tud adatokat gyűjteni. |

A Windows a Microsoft Monitoring Agent (MMA) segítségével a System Center Operations Manager és az Azure Monitor gyűjtse össze és adatokat küldeni. A környezettől függően az ügynököt a System Center Operations Manager ügynök, a Log Analytics-ügynököket, az MMA vagy a közvetlen ügynök nevezzük. A System Center Operations Manager és az Azure Monitor adja meg az MMA némileg különböző verzióit. Ezek a fájlok minden egyes jelenthetik a System Center Operations Manager, az Azure Monitor vagy mindkettő értékre.

A Linuxhoz készült Log Analytics-ügynök Linux rendszeren gyűjt, és adatokat küld az Azure Monitor. Átviteli adatok közvetlenül csatlakozik az Azure Monitor-ügynökökkel kiszolgálókon, vagy olyan kiszolgálókra, amelyek a System Center Operations Manager felügyeleti csoportok keresztül csatlakozik az Azure Monitor használható.

Maga a függőségi ügynök nem közvetít adatokat, ezért nem igényli a tűzfalak vagy portok semmilyen módosítását. Az átviteli adatok mindig továbbított adatok az Azure Monitor, a Log Analytics-ügynök által közvetlenül vagy a Log Analytics-átjárón.

![ügynök diagram](./media/wire-data/agents.png)

Ha egy System Center Operations Manager felhasználói az Azure Monitor csatlakozik a felügyeleti csoport:

- További konfiguráció nélkül nem szükséges, ha a System Center Operations Manager-ügynökök hozzáférhet az interneten keresztül csatlakozni az Azure Monitor.
- A Log Analytics-átjáró használata a System Center Operations Manager, ha a System Center Operations Manager-ügynökök az interneten keresztül nem tud hozzáférni az Azure Monitor konfigurálása kell.

Ha a Windows vagy Linux rendszerű számítógépek közvetlenül nem tud csatlakozni a szolgáltatáshoz, konfigurálása a Log Analytics-ügynököket a Log Analytics-átjáró használata az Azure Monitor csatlakozni szeretne. A Log Analytics átjáróhoz letöltheti a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Előfeltételek

- Szükség van a [Betekintések és elemzés](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) megoldásra.
- Ha a Wire Data előző verzióját használja, először el kell távolítania azt. Az eredeti Wire Data megoldással rögzített adatok azonban továbbra is elérhetők lesznek a Wire Data 2.0-ban és a naplókeresésben.
- A függőségi ügynök telepítéséhez vagy eltávolításához rendszergazdai jogosultság szükséges.
- A függőségi ügynököt egy 64 bites operációs rendszert futtató számítógépre kell telepíteni.

### <a name="operating-systems"></a>Operációs rendszerek

Az alábbi táblázat a függőségi ügynök által támogatott operációs rendszereket tartalmazza: A Wire Data semmilyen operációs rendszer esetében nem támogatja a 32 bites architektúrákat.

#### <a name="windows-server"></a>Windows Server

- A Windows Server 2019
- A Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Asztali Windows

- A Windows 10 1803-as verzióban
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>Támogatott Linux operációs rendszerek
Az alábbi szakaszok a támogatott operációs rendszerek listázása a függőségi ügynök Linux rendszeren.  

- Csak az alapértelmezett és az SMP Linux kernelű kiadások támogatottak.
- A nem szabványos kernelű kiadások, például a PAE és a Xen nem támogatottak semmilyen Linux-disztribúció esetén. Ha például kiadási karakterlánc "2.6.16.21-0.8-xen", a rendszer nem támogatott.
- Az egyéni kernelek, beleértve a standard kernelek újrafordításait, nem támogatottak.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

##### <a name="centosplus"></a>CentOSPlus
| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 nagyvállalati Server

| Operációs rendszer verziója | Kernel verziója
|:--|:--|
| 11 SP4 | 3.0.* |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 vállalati kiszolgáló

| Operációs rendszer verziója | Kernel verziója
|:--|:--|
| 12 SP2 | 4.4. * |
| 12 SP3 | 4.4. * |

### <a name="dependency-agent-downloads"></a>Függőségi ügynök letöltése

| Fájl | Operációs rendszer | Verzió | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>Konfiguráció

A Wire Data megoldásnak a munkaterületekhez való konfigurálásához végezze el az alábbi lépéseket:

1. Engedélyezze a Activity Log Analytics megoldást, a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) vagy leírt folyamatot követve [hozzáadása a megoldástárból megoldások monitorozása](../../azure-monitor/insights/solutions.md).
2. Telepítse a függőségi ügynököt az összes olyan számítógépen, amelyről adatokat kíván gyűjteni. A függőségi ügynök képesek a közvetlen szomszédaikkal való kapcsolatok monitorozására, így lehetséges, hogy nem kell minden egyes számítógépre ügynököt telepíteni.

> [!NOTE]
> A Wire Data előző verzióját nem lehet hozzáadni új munkaterületekhez. Ha engedélyezve van az eredeti Wire Data megoldás, azt továbbra is használhatja. Azonban a Wire Data 2.0 használatához először el kell távolítani az eredeti verziót.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>A függőségi ügynök telepítése Windows rendszeren

Az ügynök telepítéséhez vagy eltávolításához rendszergazdai jogosultság szükséges.

A függőségi ügynök a Windows rendszerű számítógépekre az InstallDependencyAgent-Windows.exe fájllal telepíthető. Ha a végrehajtható fájlt bármilyen paraméter nélkül futtatja, akkor elindul egy varázsló, amelyet követve párbeszédes formában telepítheti az ügynököt.

A függőségi ügynököt az alábbi lépésekkel telepítheti minden Windows rendszerű számítógépre:

1. A Log Analytics-ügynököket leírt lépések végrehajtásával telepítse [adatok gyűjtése saját környezetben futtatott Windows-számítógépekről](../../azure-monitor/platform/agent-windows.md).
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

1. A Log Analytics-ügynököket leírt lépések végrehajtásával telepítse [adatok gyűjtése saját környezetben futtatott Linuxos számítógépekről](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key).
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

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

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

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

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

Ha az átviteli adatok aktiválva vannak a Log Analytics-munkaterületen, a rendszer egy 300 KB méretű felügyeleti csomagot küld a munkaterület Windows-kiszolgálóinak. Ha System Center Operations Manager-ügynököt használ egy [csatlakoztatott felügyeleti csoportban](../platform/om-agents.md), a Függőségfigyelő felügyeleti csomag a System Center Operations Managerből lesz telepítve. Ha az ügynököket közvetlenül csatlakoztatva van, az Azure Monitor biztosít a felügyeleti csomag.

A felügyeleti csomag neve Microsoft.IntelligencePacks.ApplicationDependencyMonitor. A következő helyre írja a rendszer: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. A felügyeleti csomag az alábbi adatforrást használja: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>A megoldás használata

A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

- A Wire Data megoldás a Windows Server 2012 R2, Windows 8.1 és újabb operációs rendszert futtató számítógépekről gyűjt adatokat.
- A Microsoft .NET-keretrendszer 4.0-s vagy újabb verziójával kell rendelkeznie azoknak a számítógépeknek, amelyekről átviteli adatokat szeretne gyűjteni.
- Adja hozzá a Wire Data megoldás a Log Analytics-munkaterülethez ismertetett folyamatot [hozzáadása a megoldástárból megoldások monitorozása](solutions.md). Nincs szükség további konfigurációra.
- Egy adott megoldás átviteli adatainak megtekintéséhez már rendelkeznie kell a megoldással a munkaterületen.

Miután telepítette az ügynököket és telepíti a megoldást, a munkaterületen megjelenik a Wire Data 2.0 csempéje.

![Wire Data csempe](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>A Wire Data 2.0 megoldás használata

Az Azure Portalon a Log Analytics-munkaterület **Áttekintés** területén kattintson a **Wire Data 2.0** csempére az Átviteli adatok irányítópult megnyitásához. Az irányítópulton az alábbi táblázatban felsorolt panelek találhatók. Minden panelen legfeljebb 10 olyan elem jelenik meg, amely megfelel a panel hatóköri és időtartományi kritériumainak. A panel alján található **Az összes megtekintése** elemre vagy a panel fejlécére kattintva az összes rekordot megjelenítő keresést végezhet a naplóban.

| **Panel** | **Leírás** |
| --- | --- |
| Hálózati forgalmat rögzítő ügynökök | A hálózati forgalmat rögzítő ügynökök számát és a forgalmat rögzítő első 10 számítógép listáját jeleníti meg. Kattintson a számra a következőre vonatkozó naplókeresés futtatásához: <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Kattintson a listában található egyik számítógépre egy olyan naplókeresés futtatásához, amely a rögzített bájtok számát adja vissza. |
| Helyi alhálózatok | Az ügynök által felderített helyi alhálózatok számát mutatja.  Kattintson a számra a következőre vonatkozó naplókeresés futtatásához: <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>. Ez az alhálózatokat és az egyes alhálózatokon átküldött bájtok számát jeleníti meg. Kattintson a listában található alhálózatra egy olyan naplókeresés futtatásához, amely az alhálózaton küldött bájtok teljes számát adja vissza. |
| Alkalmazásszintű protokollok | Az ügynökök által felderített, használatban lévő alkalmazásszintű protokollok számát jeleníti meg. Kattintson a számra a következőre vonatkozó naplókeresés futtatásához: <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Kattintson a protokollra egy olyan naplókeresés futtatásához, amely a protokoll használatával küldött bájtok számát adja vissza. |

![Átviteli adatok irányítópult](./media/wire-data/wire-data-dash.png)

A **Hálózati forgalmat rögzítő ügynökök** panellel megállapíthatja, mekkora hálózati sávszélességet használnak fel a számítógépek. A panel segítségével könnyen megtalálhatja a hálózat _legforgalmasabb_ számítógépét. Előfordulhat, hogy ezek a számítógépek túlterheltek, rendellenesen működnek vagy az átlagosnál több hálózati erőforrást használnak.

![naplóbeli keresés példája](./media/wire-data/log-search-example01.png)

Ehhez hasonlóan a **Helyi alhálózatok** panel segítségével megállapíthatja, mekkora hálózati forgalom halad át az alhálózatokon. A felhasználók gyakran az alkalmazásaikhoz tartozó kritikus területek körül adnak meg alhálózatokat. Ez a panel ezekbe a területekbe kínál betekintést.

![naplóbeli keresés példája](./media/wire-data/log-search-example02.png)

Az **Alkalmazásszintű protokollok** panel hasznos, mivel fontos lehet annak ismerete, hogy mely protokollok vannak használatban. Lehetséges például, hogy arra számít, hogy az SSH nincs használatban a hálózati környezetben. A panelen megjelenő információk ezt gyorsan megerősíthetik vagy megcáfolhatják.

![naplóbeli keresés példája](./media/wire-data/log-search-example03.png)

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
| Direction | Bemeneti vagy kimeneti |
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

- [Keresés a naplókban](../../azure-monitor/log-query/log-query-overview.md) az átviteli adatokhoz kapcsolódó részletes keresési rekordok megtekintéséhez.
