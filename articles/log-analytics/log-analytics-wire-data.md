---
title: "Naplóelemzési adatok megoldást vezetéknélküli |} Microsoft Docs"
description: "Átviteli adatokat az OMS-ügynököt, beleértve az Operations Manager és a Windows-csatlakoztatott ügynökök rendelkező számítógépekről összevont hálózati és a teljesítmény adatai. Hálózati adatok a naplóadatok segítséget adatainak együtt."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: magoedte;banders
ms.openlocfilehash: b7cb178a24b043fe2c884ef0e4b3ad14ca0d73e4
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="wire-data-20-preview-solution-in-log-analytics"></a>A Naplóelemzési átviteli adatok 2.0 (előzetes verzió) megoldás

![Átviteli adatokat szimbólum](./media/log-analytics-wire-data/wire-data2-symbol.png)

Átviteli adatokat az összevont hálózati és a teljesítmény származó adatokat az OMS-ügynököt, beleértve a környezetben az Operations Manager által figyelt Windows-csatlakozóval csatlakoztatott és a Linux-csatlakoztatott számítógépek. Hálózati adatok a más naplóadatokat segítséget adatainak együtt.

Mellett az OMS-ügynököt az átviteli adatokat megoldás használ a Microsoft függőségi ügynökök, amelyek az informatikai infrastruktúra telepítése számítógépeken. A függőségi ügynökei figyelik és a számítógépről küldött, a hálózati 2-3 szinttel hálózati adatok a [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), beleértve a különböző protokollok és portok. Majd küld adatokat a Naplóelemzési ügynököt használ.  

> [!NOTE]
> Új munkaterületek átviteli adatokat megoldás az előző verzió nem lehet hozzáadni. Ha az eredeti átviteli adatokat megoldás, továbbra is használhatja. Azonban hálózaton adatok 2.0 használatához el kell távolítani az eredeti verzió.

Alapértelmezés szerint a Naplóelemzési naplózza az adatokat a Processzor, memória, lemez és hálózati teljesítményadatait épített Windows és Linux számlálók, valamint megadhat más teljesítményszámlálókat. Hálózati és egyéb adatok gyűjtése történik valós idejű minden ügynökhöz, beleértve az alhálózatok és a számítógép által használt alkalmazásszintű protokollok.  Hálózati adatok az alkalmazás szintjén nem működik, az TCP átviteli réteg megvizsgálja átviteli adatokat.  A megoldás nem tekintse meg az egyes nyugtázás és SYNs.  Ha a handshake befejeződött, élő kapcsolattal minősül, és csatlakoztatva jelölésű. Hogy kapcsolat marad az élő mindaddig, amíg mindkét oldalon fogadja el a szoftvercsatorna meg nyitva, és adatok teljen oda-vissza.  Ha bármelyik oldal megszünteti a kapcsolatot, akkor Disconnected van megjelölve.  Ezért csak számolja a sikeresen befejezett csomagok sávszélességét, hogy nem jelenti a újraküldése vagy csomagok nem sikerült.

Ha már használta [sFlow](http://www.sflow.org/) vagy más szoftvereket [Cisco tartozó NetFlow protokoll](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), akkor a statisztikák és adatok átvitel közbeni adatokból látja lesz ismerős lehet.

A beépített naplófájl-keresési lekérdezések típusú többek között:

- Átviteli adatokat szolgáltató ügynökök
- Átviteli adatokat szolgáltató ügynökök IP-címe
- Kimenő kommunikáció IP-cím
- Alkalmazás-protokollokra által küldött bájtok száma
- Egy alkalmazás szolgáltatás által küldött bájtok száma
- A különböző protokollok által fogadott bájtok
- IP-verziója által küldött és fogadott bájtok teljes száma
- Volt megbízhatóan mérhető kapcsolatok átlagos késése
- A számítógép folyamatainak kezdeményezett, illetve a hálózati forgalom érkezett
- A folyamatot a hálózati forgalom mennyisége

Átviteli adatokat használó keresésénél szűrheti és csoport adatai a felső ügynökök és a felső protokollokkal kapcsolatos információk megtekintéséhez. Szeretné megtekinteni, ha egyes számítógépek (IP-címet vagy MAC-címek) közölt egymással, hogyan hosszú, és mennyi adatot küldött – alapvetően, megtekintheti a hálózati forgalmat, amely keresési-alapú metaadatait.

Azonban mivel megtekintett metaadatok, célszerű nem feltétlenül részletes hibaelhárítási. Átvitel közbeni Naplóelemzési adatai nem a teljes rögzítési hálózati adatok.  Nem készült részletes csomagszintű hibaelhárítás. Az ügynök, más adatgyűjtési módszerek képest használatának előnye, hogy ne kelljen telepíteni készülékek, konfigurálja újra a hálózati kapcsolókon vagy összetett konfigurációk végrehajtásához. Átviteli adatokat egyszerűen ügynök-alapú – az ügynököt telepít egy számítógépre, és azt a saját hálózati forgalom figyeli. Egy másik előnye, ha meg szeretné figyelni a szolgáltatók vagy az üzemeltetési szolgáltató vagy a Microsoft Azure, ahol a felhasználó nem a tulajdonosa a háló réteg futó feladatok.

## <a name="connected-sources"></a>Összekapcsolt források

Átviteli adatokat az adatok lekérése a Microsoft függőségi ügynök. A függőségi ügynök attól függ, hogy az OMS-ügynököt a kapcsolatok szolgáltatáshoz. Ez azt jelenti, hogy a kiszolgálónak rendelkeznie kell az OMS-ügynököt telepítette és konfigurálta az első, majd a függőségi ügynök telepítése. A következő táblázat ismerteti, amely az átviteli adatokat megoldás támogatja a csatlakoztatott adatforrások.

| **Csatlakoztatott adatforrás** | **Támogatott** | **Leírás** |
| --- | --- | --- |
| Windows-ügynökök | Igen | Átviteli adatokat elemzi, és a Windows-ügynök számítógépekről gyűjt adatokat. <br><br> Kívül a [OMS-ügynököt](log-analytics-windows-agent.md), Windows-ügynökök szükséges a Microsoft függőségi ügynök. Tekintse meg a [támogatott operációs rendszerek](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) operációs rendszerek teljes listáját. |
| Linux-ügynökök | Igen | Átviteli adatokat elemzi, és a Linux-ügynök számítógépekről gyűjt adatokat.<br><br> Kívül a [OMS-ügynököt](log-analytics-quick-collect-linux-computer.md), Linux-ügynököt a Microsoft függőségi ügynök szükséges. Tekintse meg a [támogatott operációs rendszerek](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) operációs rendszerek teljes listáját. |
| System Center Operations Manage felügyeleti csoport | Igen | Átviteli adatokat elemzi, és összegyűjti az adatokat a Windows és Linux-ügynökök a csatlakoztatott [System Center Operations Manager felügyeleti csoport](log-analytics-om-agents.md). <br><br> Naplóelemzési a System Center Operations Manager ügynök számítógépről közvetlen kapcsolatra szükség. Adatok Naplóelemzési továbbítódik a felügyeleti csoportból. |
| Azure Storage-fiók | Nem | Átviteli adatokat adatokat gyűjt a ügynök számítógépekről, nincsenek adatok, az Azure Storage-ból gyűjtését. |

A Windows a Microsoft Monitoring Agent (MMA) segítségével a System Center Operations Manager és a Naplóelemzési gyűjtse össze és küldhetnek adatokat. Attól függően, hogy a környezetben az ügynököt a System Center Operations Manager ügynök, OMS-ügynököt, Log Analytics Agent, MMA vagy közvetlen ügynök neve. A System Center Operations Manager és a Naplóelemzési adja meg a MMA némileg különböző verziói. Ezen verziói egyes jelenthetik-e a System Center Operations Manager Naplóelemzési, vagy mindkettőt.

Linux az OMS-ügynököt Linux gyűjt, és adatokat küld a Naplóelemzési. Átviteli adatokat kiszolgálókon közvetlen OMS-ügynök, vagy a System Center Operations Manager felügyeleti csoportok keresztül Naplóelemzési csatolt kiszolgálók használható.

Ez a cikk hivatkozik a minden ügynököt, hogy Linux vagy a Windows, hogy egy System Center Operations Manager felügyeleti csoport csatlakoztatva, vagy közvetlenül a Naplóelemzési termékeket nevezzük a _OMS-ügynököt_. A megadott központi telepítés nevét, az ügynök csak akkor, ha szükség van a környezetben fogjuk használni.

A függőségi ügynök nem továbbítja az adatokat saját magát, és nem igényel módosításokat tűzfalak vagy portok. Az adatok átvitel közbeni mindig továbbított adatok az OMS-ügynök szolgáltatáshoz, vagy közvetlenül vagy az OMS-átjáró.

![ügynök diagramja](./media/log-analytics-wire-data/agents.png)

Ha a System Center Operations Manager felhasználói Naplóelemzési csatlakoztatott felügyeleti csoport:

- A System Center Operations Manager-ügynökök hozzáférjenek az internetes Log Analyticshez való csatlakozáshoz nincs szükség további konfigurációra.
- Konfigurálja az OMS-átjáró működik a System Center Operations Manager, amikor a System Center Operations Manager-ügynökök az interneten keresztül nem tud hozzáférni a Naplóelemzési kell.

Ha a közvetlen ügynök használ, akkor az OMS-ügynököt maga Naplóelemzési vagy az OMS-átjáró konfigurálása. Letöltheti az OMS-átjárót a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Előfeltételek

- Szükséges a [betekintést és az elemzések](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) megoldás ajánlat.
- Ha az előző verzió, az átviteli adatokat megoldás használata esetén távolítsa el azt. Azonban az összes rögzített az eredeti átviteli adatokat-megoldáson keresztül érhetők el az adatok továbbra is átviteli adatok 2.0 és a keresési napló.
- Telepíthet vagy távolíthat el a függőségi ügynök rendszergazdai jogosultság szükséges.
- A függőség ügynököt 64 bites operációs rendszerrel rendelkező számítógépen telepíteni kell.

### <a name="operating-systems"></a>Operációs rendszerek

Az alábbiakban a támogatott operációs rendszerek a függőségi ügynök listázása. Átviteli adatokat nem támogatja 32 bites operációs rendszert.

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

#### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, a CentOS Linux és az Oracle Linux (az RHEL Kernel)

- Csak az alapértelmezett és az SMP Linux kernel verziókban támogatott.
- Nem szabványos kernel kiadását, például a Xen, és a fizikai nem támogatottak a Linux-disztribúció. Például a rendszer, amely a kiadás karakterlánc _2.6.16.21-0.8-xen_ nem támogatott.
- Egyéni mag, beleértve a szabványos kernelek újrafordításainak maximális nem támogatottak.
- CentOSPlus kernel nem támogatott.
- Ez a cikk későbbi részében Oracle szoros vállalati Kernel (UEK) vonatkozik.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| **Operációs rendszer verziója** | **Kernel-verzió** |
| --- | --- |
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| **Operációs rendszer verziója** | **Kernel-verzió** |
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

| **Operációs rendszer verziója** | **Kernel-verzió** |
| --- | --- |
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398 <br> 2.6.18-400 <br>2.6.18-402 <br>2.6.18-404 <br>2.6.18-406 <br> 2.6.18-407 <br> 2.6.18-408 <br> 2.6.18-409 <br> 2.6.18-410 <br> 2.6.18-411 <br> 2.6.18-412 <br> 2.6.18-416 <br> 2.6.18-417 <br> 2.6.18-419 |

#### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Szoros vállalati Kernel az Oracle Enterprise Linux

#### <a name="oracle-linux-6"></a>Oracle Linux 6

| **Operációs rendszer verziója** | **Kernel-verzió** |
| --- | --- |
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| **Operációs rendszer verziója** | **Kernel-verzió** |
| --- | --- |
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11

| **Operációs rendszer verziója** | **Kernel-verzió** |
| --- | --- |
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10

| **Operációs rendszer verziója** | **Kernel-verzió** |
| --- | --- |
| 10 SP4 | 2.6.16.60 |

#### <a name="dependency-agent-downloads"></a>A függőségi ügynök letöltése

| **Fájl** | **OS** | **Verzió** | **SHA-256** |
| --- | --- | --- | --- |
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |



## <a name="configuration"></a>Konfiguráció

A következő lépésekkel állíthatja az átviteli adatokat megoldás a munkaterületek.

1. A tevékenység Naplóelemzési megoldást engedélyezése a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) vagy ismertetett folyamatot követve [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).
2. A függőségi ügynök telepíthető minden számítógépre, amelyre az adatok lekérése. A függőségi ügynök figyelheti azonnali szomszédok, kapcsolatok, így előfordulhat, hogy nem kell minden olyan számítógépen az ügynök.

### <a name="install-the-dependency-agent-on-windows"></a>A függőségi ügynök telepíthető Windows

Telepítéséhez vagy az ügynök eltávolításához rendszergazdai jogosultság szükséges.

A függőségi ügynök InstallDependencyAgent-Windows.exe Windows rendszert futtató számítógépekre telepíthető. Ha a végrehajtható fájl kapcsolók nélkül futtatja, akkor elindít egy varázslót, amelyeket követve párbeszédes formában történő telepítéséhez.

A függőségi ügynök telepítése minden Windows rendszerű számítógépen tegye a következőket:

1. Az OMS-ügynököt lépéseit követve telepítse [adatok gyűjtése a Windows rendszerű számítógépek a környezetében üzemeltetett](log-analytics-windows-agent.md).
2. Töltse le a Windows függőségi ügynök az előző szakaszban hivatkozás segítségével, és futtassa a következő paranccsal:`InstallDependencyAgent-Windows.exe`
3. Kövesse a varázsló az ügynök telepítéséhez.
4. Ha a függőségi ügynök nem indul el, tekintse meg a hibával kapcsolatos részletes információk a naplókat. Windows-ügynökök a naplózási könyvtár %Programfiles%\Microsoft függőségi Agent\logs esetén.

#### <a name="windows-command-line"></a>A Windows parancssor

Beállítások a következő táblázat segítségével telepítése a parancssorból. A telepítési jelző listájának megtekintéséhez futtassa a telepítő használatával a /? Ez a jelző az alábbiak szerint.

InstallDependencyAgent-Windows.exe /?

| **Flag** | **Leírás** |
| --- | --- |
| <code>/?</code> | A parancssori kapcsolók listájának lekérése. |
| <code>/S</code> | Felhasználói beavatkozás nélküli telepítés végrehajtásához. |

A Windows függőségi ügynök fájlok alapértelmezés szerint a C:\Program Files\Microsoft függőségi ügynök kerülnek.

### <a name="install-the-dependency-agent-on-linux"></a>A függőségi ügynök telepíthető Linux

Az ügynök telepítéséhez és konfigurálásához rendszergazdai hozzáférés szükséges.

A függőségi ügynök telepítve van a Linux rendszerű számítógépeken InstallDependencyAgent Linux64.bin, egy önkicsomagoló bináris héjparancsfájlt keresztül. A fájl segítségével is futtathatja _megosztása_ , vagy adja hozzá végrehajtási engedélyeket magát a fájlt.

A függőségi ügynök telepítése minden egyes Linux-számítógép tegye a következőket:

1. Az OMS-ügynököt lépéseit követve telepítse [adatokat gyűjteni a környezetében üzemeltetett Linux rendszerű számítógépek](log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. A Linux-függőségi ügynök az előző szakaszban hivatkozás segítségével töltse le és telepítse legfelső szintű a következő paranccsal: sh InstallDependencyAgent-Linux64.bin
3. Ha a függőségi ügynök nem indul el, tekintse meg a hibával kapcsolatos részletes információk a naplókat. A Linux-ügynökök, a naplózási könyvtár van: /var/opt/microsoft/dependency-agent/log.

A telepítési jelző listájának megtekintéséhez futtassa a telepítőprogramot a `-help` jelzőt az alábbiak szerint.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Flag** | **Leírás** |
| --- | --- |
| <code>-help</code> | A parancssori kapcsolók listájának lekérése. |
| <code>-s</code> | Felhasználói beavatkozás nélküli telepítés végrehajtásához. |
| <code>--check</code> | Ellenőrizze az engedélyeit és az operációs rendszer, de nem telepíti az ügynököt. |

A függőségi ügynök fájlok kerülnek, a következő könyvtárban:

| **Fájlok** | **Hely** |
| --- | --- |
| Alapvető fájljait | /opt/microsoft/dependency-agent |
| Naplófájlok | /var/opt/microsoft/dependency-agent/log |
| Olyan konfigurációs fájlt | /etc/opt/microsoft/dependency-agent/config |
| Végrehajtható fájlok | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| A tároló bináris fájljai | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Telepítési parancsfájl példák

Könnyen telepíthető egyszerre több kiszolgálón a függőségi ügynök, áttekinteni parancsfájl használata. Az alábbi parancsfájl-példák segítségével töltse le és a függőségi ügynök telepíthető Windows vagy Linux.

#### <a name="powershell-script-for-windows"></a>A Windows PowerShell-parancsfájl

```PowerShell

Invoke-WebRequest &quot;https://aka.ms/dependencyagentwindows&quot; -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>A Linux rendszerhez parancsfájl

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Célállapot-konfiguráló

A célállapot-konfiguráció keresztül függőségi ügynök telepítéséhez használhatja a xPSDesiredStateConfiguration modul és a kód a következőhöz hasonló:

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

A következő szakaszok segítségével távolítsa el a függőségi ügynök.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Távolítsa el a függőségi ügynököt Windows rendszeren

A rendszergazda eltávolíthatja a függőségi ügynök a Windows Vezérlőpult segítségével.

A rendszergazda %Programfiles%\Microsoft függőségi Agent\Uninstall.exe távolítsa el a függőségi ügynököt is futtathatja.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Távolítsa el a függőségi ügynököt Linux rendszeren

A függőségi ügynök teljesen eltávolítása Linux, el kell távolítani az ügynök önmagát és az összekötő telepítésekor automatikusan települ az ügynök. Eltávolíthatja, mindkettő használatával a következő parancsot:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Felügyeleti csomagok

Átviteli adatokat aktiválva van a Naplóelemzési munkaterület, 300 KB-os felügyeleti csomag van küld futó Windows-kiszolgálók. Ha a System Center Operations Manager ügynököt használ egy [csatlakoztatott felügyeleti csoport](log-analytics-om-agents.md), a függőségi figyelő felügyeleti csomag a System Center Operations Manager telepítése. Ha az ügynököt közvetlenül csatlakoztatott, Log Analyticshez nyújt a felügyeleti csomag.

A felügyeleti csomag neve Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Az írás: %Programfiles%\Microsoft figyelési Agent\Agent\Health State\Management szervizcsomagok. Az adatforrás által használt felügyeleti csomag: % Program files%\Microsoft figyelés Agent\Agent\Health szolgáltatás State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>A megoldás használata

**Telepítése és a megoldás konfigurálása**

Az alábbi információk segítségével telepítse és konfigurálja a megoldást.

- Az átviteli adatokat megoldás szerez be a Windows Server 2012 R2, Windows 8.1 és újabb operációs rendszereket futtató számítógépek adatait.
- Microsoft .NET-keretrendszer 4.0-s vagy újabb szükséges a számítógépeken, ahol szeretné-e az átvitel közbeni adatainak megszerzése.
- Az átviteli adatokat megoldás hozzáadni a Naplóelemzési munkaterület ismertetett eljárással [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md). Nincs szükség további konfigurációra.
- Meg szeretné tekinteni a átviteli adatokat egy adott megoldás, ha szüksége van a megoldás már hozzá van adva a munkaterületen.

Után az ügynök telepítve van, és a megoldás telepítése, az átvitel közbeni adatok 2.0 csempe jelenik meg a munkaterületen.

![Átviteli adatokat csempe](./media/log-analytics-wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>A vezetékes adatok 2.0 segítségével

Az OMS-portálon kattintson a **átviteli adatok 2.0** csempére kattintva nyissa meg az átviteli adatokat irányítópulton. Az irányítópult a paneleket az alábbi táblázat tartalmazza. Minden egyes panel adott panelhez feltételeknek, a megadott hatókör és időtartomány legfeljebb 10 elemeket sorolja fel. A napló keresési, amely visszaadja az összes rekord kattintva futtathatja **láthatja az összes** alján a panelről, vagy kattintson a panel fejléc.

| **Blade** | **Leírás** |
| --- | --- |
| Hálózati forgalmat rögzítő ügynökök | Az ügynököket, amelyeket a hálózati forgalom rögzítése számát mutatja, és felsorolja a felső 10 számítógépet forgalom rögzítése. Kattintson a napló keresése futtatásához <code>Type:WireData &#124; measure Sum(TotalBytes) by Computer &#124; top 500000</code>. Kattintson egy számítógépre, a listában egy rögzített bájtok teljes száma adatszolgáltató napló keresés futtatásához. |
| Helyi alhálózatok | Az ügynökök felderített helyi alhálózatok számát mutatja.  Kattintson a napló keresése futtatásához <code>Type:WireData &#124; Measure Sum(TotalBytes) by LocalSubnet</code> összes alhálózatot, amely felsorolja a minden egyes küldött bájtok száma. Kattintson a napló-keresés vissza az alhálózat küldött bájtok teljes száma futtatásához a listában lévő alhálózatot. |
| Alkalmazásszintű protokollok | Alkalmazásszintű protokollok számának ügynököket használja, megjelenítése Kattintson a napló keresése futtatásához <code>Type:WireData &#124; Measure Sum(TotalBytes) by ApplicationProtocol</code>. Kattintson a napló-keresés vissza a protokoll használatával küldött bájtok teljes száma futtatásához protokoll. |

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Átviteli adatokat irányítópult](./media/log-analytics-wire-data/wire-data-dash.png)

Használhatja a **hálózati forgalmat rögzítő ügynökök** panelt, és határozza meg, mekkora hálózati sávszélesség felhasználás alatt a számítógépek által. Ez a panel segítségével könnyedén megtalálhatja a _chattiest_ számítógép a környezetben. Ezek a számítógépek sikerült túlterheltté vált, rendellenesen működő, vagy további hálózati erőforrások, mint a normál használatával.

![naplófájl-keresési példa](./media/log-analytics-wire-data/log-search-example01.png)

Hasonlóképpen, használhatja a **helyi alhálózatok** panelt, és annak megállapítása, hogy mekkora hálózati forgalom áthelyezése az alhálózatokon keresztül. Felhasználók gyakran határozza meg azt az alkalmazáshoz kritikus területeket körül alhálózatokat. Ezen a panelen a fenti területekre megtekintésében kínál.

![naplófájl-keresési példa](./media/log-analytics-wire-data/log-search-example02.png)

A **alkalmazásszintű protokollok** panel akkor hasznos, előnyös, mert tudják, mit protokollt használja. Például előfordulhat, hogy várt SSH használata a hálózati környezetben nem találhatók. A panelen elérhető adatok megtekintéséhez használatos időkategóriát gyorsan erősítse meg, és a várt eredmény disprove.

![naplófájl-keresési példa](./media/log-analytics-wire-data/log-search-example03.png)

Ebben a példában lehetett-feltárás SSH részletek mely számítógépeket használó SSH és sok más kommunikációs részleteinek megtekintése.

![SH keresési eredmények](./media/log-analytics-wire-data/ssh-details.png)

Akkor célszerű is tudja, hogy a protokoll forgalom növekvő vagy csökkenő adott idő alatt. Például ha egy alkalmazás által továbbított adatok mennyisége növekszik, előfordulhat, hogy valami kell ügyelnie, vagy hogy előfordulhat, hogy a fontos.

## <a name="input-data"></a>A bemeneti adatok

Átviteli adatokat gyűjt a metaadatok használatával az ügynököket, amelyeket engedélyezte a hálózati forgalom. Minden ügynök 15 másodpercenként adatokat küld.

## <a name="output-data"></a>kimeneti adatok

A típusú rekord _WireData_ jön létre az egyes bemeneti adatokat. WireData rögzíti az alábbi táblázatban szereplő jellemzőkkel rendelkezik:

| Tulajdonság | Leírás |
|---|---|
| Computer | Számítógép neve, hol történt adatgyűjtés |
| TimeGenerated | A rekord idő |
| LocalIP | A helyi számítógép IP-címe |
| SessionState | Csatlakoztatva, vagy nincs csatlakoztatva |
| ReceivedBytes | Fogadott bájtok mennyiségét |
| ProtocolName | A használt hálózati protokoll neve |
| IPVersion | IP-verziója |
| Irány | Bejövő vagy kimenő |
| MaliciousIP | Egy ismert rosszindulatú forrás IP-címe |
| Súlyosság | Gyanús kártevőt súlyossága |
| RemoteIPCountry | A távoli IP-cím ország |
| ManagementGroupName | Az Operations Manager felügyeleti csoport neve |
| SourceSystem | Ha adatokat gyűjtött a program forrás |
| SessionStartTime | A munkamenet kezdési idejét |
| SessionEndTime | Munkamenet befejezési időpontja |
| LocalSubnet | Alhálózati hol történt adatgyűjtés |
| LocalPortNumber | Helyi port száma |
| RemoteIP | A távoli számítógép által használt távoli IP-cím |
| RemotePortNumber | A távoli IP-cím által használt portszám |
| SessionID | Két IP-címek közötti kommunikáció munkamenetet azonosító egyedi érték |
| SentBytes | Küldött bájtok száma |
| TotalBytes | A munkamenetben küldött bájtok teljes száma |
| ApplicationProtocol | A használt hálózati protokoll típusa   |
| ProcessID | Windows-folyamat azonosítója |
| Folyamatnév | A folyamat elérési útját és nevét |
| RemoteIPLongitude | IP-hosszúság érték |
| RemoteIPLatitude | IP-szélesség értéke |


## <a name="next-steps"></a>További lépések

- [Naplók keresése](log-analytics-log-searches.md) részletes vezetékes keresési rekordok megtekintéséhez.
