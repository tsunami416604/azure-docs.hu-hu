---
title: Konfigurálja a Service Map az Azure-ban |} A Microsoft Docs
description: A Service Map az Azure egyik megoldása, amely automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. Ez a cikk részletesen központi telepítése a Service Map a környezetben, és a számos célra használja.
services: monitoring
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/07/2018
ms.author: bwren
ms.openlocfilehash: 808fe41928a99ffc797c96a02305d81765318780
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54381655"
---
# <a name="configure-service-map-in-azure"></a>Konfigurálja a Service Map az Azure-ban
A Szolgáltatástérkép automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. Használhatja a kiszolgálók megtekintéséhez, ahogyan Ön gondol rájuk összekapcsolt rendszerekkel, amelyek kritikus fontosságú szolgáltatásokat. A Service Map megmutatja a kapcsolatokat kiszolgálók, folyamatok és portok között bármely TCP-kapcsolattal összekötött architektúrában semmilyen beállítást nem szükséges, eltérő ügynököt telepíteni.

Ez a cikk ismerteti a Service Map és bevezetési-ügynökök konfigurálása részleteit. A Service Map használatával kapcsolatos információkért lásd: [a Service Map megoldást használja az Azure-ban]( service-map.md).

## <a name="supported-azure-regions"></a>Támogatott Azure-régiók
Jelenleg a Service Map érhető el a következő Azure-régiókhoz:
- USA keleti régiója
- Nyugat-Európa
- USA nyugati középső régiója
- Délkelet-Ázsia

## <a name="supported-windows-operating-systems"></a>A támogatott Windows operációs rendszerek
A következő szakasz a függőségi ügynököt Windows támogatott operációs rendszerek listázása. 

>[!NOTE]
>A Service Map csak 64 bites platformokat támogatja.
>

### <a name="windows-server"></a>Windows Server
- A Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Asztali Windows
- A Windows 10 1803-as verzióban
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

## <a name="supported-linux-operating-systems"></a>Támogatott Linux operációs rendszerek
A következő szakasz a támogatott operációs rendszerek listázása a függőségi ügynök, a Red Hat Enterprise Linux, CentOS Linux és Oracle Linux (az RHEL-Kernel).  

- Csak az alapértelmezett és az SMP Linux kernelű kiadások támogatottak.
- A nem szabványos kernelű kiadások, például a PAE és a Xen nem támogatottak semmilyen Linux-disztribúció esetén. Ha például kiadási karakterlánc "2.6.16.21-0.8-xen", a rendszer nem támogatott.
- Az egyéni kernelek, beleértve a standard kernelek újrafordításait, nem támogatottak.
- A CentOSPlus kernel szintén nem támogatott.
- Az Oracle Unbreakable Enterprise Kernellel (UEK) a cikk későbbi szakasza foglalkozik.

### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

### <a name="ubuntu-server"></a>Ubuntu Server

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15. * |
| Ubuntu 16.04.3 | kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 6 szoros vállalati kernel
| Operációs rendszer verziója | Kernel verziója
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 5 szoros vállalati kernel

| Operációs rendszer verziója | Kernel verziója
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

## <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 vállalati kiszolgáló

| Operációs rendszer verziója | Kernel verziója
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |

## <a name="dependency-agent-downloads"></a>Függőségi ügynök letöltése

| Fájl | Operációs rendszer | Verzió | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="connected-sources"></a>Összekapcsolt források
A Service Map az adatok lekérése a Microsoft Dependency agent. A függőségi ügynök a Log Analytics-ügynököket a Log Analytics-kapcsolatok támaszkodik. Ez azt jelenti, hogy egy kiszolgáló rendelkeznie kell a Log Analytics-ügynököket telepíteni és konfigurálni a függőségi ügynök.  A következő táblázat ismerteti, amely a Service Map megoldás támogatja a csatlakoztatott forrásokat.

| Csatlakoztatott forrás | Támogatott | Leírás |
|:--|:--|:--|
| Windows-ügynökök | Igen | A Service Map elemzi, és gyűjti az adatokat Windows-számítógépek. <br><br>Mellett a [Log Analytics-ügynököket for Windows](../../azure-monitor/platform/log-analytics-agent.md), Windows-ügynökök a Microsoft Dependency agent szükséges. A támogatott operációsrendszer-verziók teljes listáját megtekintheti a [támogatott operációs rendszerek](#supported-operating-systems) szakaszban. |
| Linux-ügynökök | Igen | A Service Map elemzi, és a Linux rendszerű számítógépek gyűjti az adatokat. <br><br>Mellett a [Linuxhoz készült Log Analytics-ügynök](../../azure-monitor/platform/log-analytics-agent.md), Linux-ügynökök a Microsoft Dependency agent szükséges. A támogatott operációsrendszer-verziók teljes listáját megtekintheti a [támogatott operációs rendszerek](#supported-operating-systems) szakaszban. |
| System Center Operations Manage felügyeleti csoport | Igen | A Service Map elemzi, és a egy csatlakoztatott a Windows és Linux-ügynökök gyűjti az adatokat [System Center Operations Manager felügyeleti csoport](../../azure-monitor/platform/om-agents.md). <br><br>Ehhez közvetlen kapcsolat szükséges a System Center Operations Manager-ügynökszámítógép és a Log Analytics között. |
| Azure Storage-fiók | Nem | A Service Map gyűjti az adatokat ügynökszámítógépen, így nem szerepel megjeleníthető adat gyűjtését az Azure Storage-ból származó. |

A Windows, a Microsoft Monitoring Agent (MMA) segítségével a System Center Operations Manager és a Log Analytics összegyűjtése és küldése figyelési adatok. (Ez az ügynök nevezzük a System Center Operations Manager-ügynök, a Log Analytics-ügynököket, az MMA vagy a közvetlen ügynök, a környezettől függően.) A System Center Operations Manager és a Log Analytics biztosít különböző ki a box verziói az MMA. Ezek a verziók jelenthetnek a Log Analyticsnek, a System Center Operations Managernek vagy mindkettőnek.  

A Linux, a Linux-összegyűjti és figyelés a Log Analytics az adatokat küld a Log Analytics-ügynököket. A Log Analytics-ügynökök közvetlenül csatlakozik a szolgáltatáshoz a kiszolgálókon a Service Map is használhatja, vagy az Operations Manager felügyeleti csoport integrálva van a Log Analytics jelentenek, amelyek.  

Ebben a cikkben azt fog hivatkozni, az ügynökök, hogy Linux vagy Windows csatlakozó System Center Operations Manager felügyeleti csoporthoz, vagy közvetlenül a Log Analyticsbe, mint a *Log Analytics-ügynököket*. 

A Service Map-ügynök nem továbbít adatokat magát, és nem igényel tűzfalak és a portok módosítása. A Service Map az mindig továbbított adatok által a Log Analytics-ügynököket a Log Analytics szolgáltatásba, vagy közvetlenül a Log Analytics-átjárón.

![A Service Map ügynökök](media/service-map-configure/agents.png)

Ha egy System Center Operations Manager-ügyfél a Log Analytics csatlakozik a felügyeleti csoport:

- Ha a System Center Operations Manager-ügynökök el tudja érni az internetet a Log Analyticshez való csatlakozáshoz, további konfiguráció nélkül nem szükséges.  
- Ha a System Center Operations Manager-ügynökök az interneten keresztül nem érhető el a Log Analytics, a Log Analytics-átjáró használata a System Center Operations Manager konfigurálása szüksége.
  
Ha a Windows vagy Linux rendszerű számítógépek közvetlenül nem tud csatlakozni a szolgáltatáshoz, konfigurálása a Log Analytics-ügynököket a Log Analytics-munkaterület használata az átjáró csatlakozni szeretne. Hogyan telepítheti és konfigurálhatja a Log Analytics-átjáró további információkért lásd: [számítógépek csatlakoztatása a Log Analytics-átjáró Internet-hozzáférés nélküli](../../azure-monitor/platform/gateway.md).  

### <a name="management-packs"></a>Felügyeleti csomagok
A Service Map aktiválásakor a Log Analytics-munkaterületen, 300 KB-os felügyeleti csomag munkaterület Windows-kiszolgálók lesznek továbbítva. Ha a System Center Operations Manager ügynököt használ egy [csatlakoztatott felügyeleti csoport](../../azure-monitor/platform/om-agents.md), a Service Map felügyeleti csomag telepítése a System Center Operations Managerből. 

A felügyeleti csomag neve Microsoft.IntelligencePacks.ApplicationDependencyMonitor. %Programfiles%\Microsoft Monitoring Agent\Agent\Health szolgáltatás State\Management Packs\ írás. Az adatforrás, amely a felügyeleti csomagot használja a % Program files%\Microsoft figyelés Agent\Agent\Health szolgáltatás State\Resources\<AutoGeneratedID > \ Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="data-collection"></a>Adatgyűjtés
Minden ügynök körülbelül 25 MB / nap, attól függően, hogyan összetett a rendszert a függőségek olyan továbbítására várható. Minden ügynök 15 másodpercenként a Service Map függőségi adatokat küld.  

A függőségi ügynök általában felhasznál a rendszermemória 0,1 %-os és a rendszer CPU 0,1 %-os.

## <a name="diagnostic-and-usage-data"></a>Diagnosztika és használati adatok
A Microsoft automatikusan gyűjt használatának és teljesítményének adatokat a Szolgáltatástérkép szolgáltatás használata. A Microsoft ezeket az adatokat adja meg, és a minőségének, biztonságának és integritásának a Szolgáltatástérkép szolgáltatás javítására használja. Adatok operációs rendszerhez és verzióhoz például a szoftverek konfigurációjával kapcsolatos információkat tartalmaz. Azt is IP-cím, a DNS-nevet és a munkaállomás nevét annak érdekében, hogy pontos és hatékony hibaelhárítási képességeket kínál. Nem gyűjtünk neveket, címeket és egyéb kapcsolattartási adatait.

Az adatok gyűjtésének és felhasználásának további információkért lásd: a [Microsoft Online Services adatvédelmi nyilatkozata](https://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="installation"></a>Telepítés

### <a name="azure-vm-extension"></a>Az Azure Virtuálisgép-bővítmény
Egy bővítmény (DependencyAgentWindows) Windows és Linux (DependencyAgentLinux) is elérhető, és a függőségi ügynököt az Azure virtuális gépek használatával könnyedén telepítheti egy [Azure Virtuálisgép-bővítmény](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).  Az Azure Virtuálisgép-bővítménnyel a függőségi ügynököt telepítheti a Windows és Linux rendszerű virtuális gépekhez, vagy egy PowerShell-parancsfájl használatával, vagy közvetlenül a virtuális gép az Azure Resource Manager-sablon használatával.  Ha telepíti az ügynököt az Azure Virtuálisgép-bővítménnyel, az ügynökök automatikusan frissülnek a legújabb verzióra.

A PowerShell-lel az Azure Virtuálisgép-bővítmény telepítéséhez, használhatja az alábbi példában:

```PowerShell
#
# Deploy the Dependency agent to every VM in a Resource Group
#

$version = "9.4"
$ExtPublisher = "Microsoft.Azure.Monitoring.DependencyAgent"
$OsExtensionMap = @{ "Windows" = "DependencyAgentWindows"; "Linux" = "DependencyAgentLinux" }
$rmgroup = "<Your Resource Group Here>"

Get-AzureRmVM -ResourceGroupName $rmgroup |
ForEach-Object {
    ""
    $name = $_.Name
    $os = $_.StorageProfile.OsDisk.OsType
    $location = $_.Location
    $vmRmGroup = $_.ResourceGroupName
    "${name}: ${os} (${location})"
    Date -Format o
    $ext = $OsExtensionMap.($os.ToString())
    $result = Set-AzureRmVMExtension -ResourceGroupName $vmRmGroup -VMName $name -Location $location `
    -Publisher $ExtPublisher -ExtensionType $ext -Name "DependencyAgent" -TypeHandlerVersion $version
    $result.IsSuccessStatusCode
}
```

Egy még egyszerűbb a függőségi ügynök telepítve van a virtuális gépek biztosítása érdekében, hogy építse be az ügynököt az Azure Resource Manager-sablonban.  A következő példa a JSON-kódot is hozzáadhatók a *erőforrások* a sablon szakaszában.

```JSON
"type": "Microsoft.Compute/virtualMachines/extensions",
"name": "[concat(parameters('vmName'), '/DependencyAgent')]",
"apiVersion": "2017-03-30",
"location": "[resourceGroup().location]",
"dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
],
"properties": {
    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
    "type": "DependencyAgentWindows",
    "typeHandlerVersion": "9.4",
    "autoUpgradeMinorVersion": true
}

```

### <a name="install-the-dependency-agent-on-microsoft-windows"></a>A függőségi ügynök telepítése a Microsoft Windows
A függőségi ügynök telepítheti manuálisan a Windows-számítógépeken futó `InstallDependencyAgent-Windows.exe`. Ha a végrehajtható fájl kapcsolók nélkül futtatja, a telepítővarázsló, amelyek telepítéséhez az interaktív módon elindítja.  

>[!NOTE]
>Az ügynök telepítéséhez vagy eltávolításához rendszergazdai jogosultság szükséges.

A következő lépések segítségével telepítse a függőségi ügynököt minden olyan Windows-számítógépen:

1.  Az ismertetett módszerek valamelyikét a következő Windows készült Log Analytics-ügynök telepítése [Log Analytics-ügynök – áttekintés](../../azure-monitor/platform/log-analytics-agent.md).
2.  A Windows-ügynök letöltése, és futtassa a következő paranccsal: 
    
    `InstallDependencyAgent-Windows.exe`

3.  Kövesse a telepítővarázsló az ügynök telepítéséhez.
4.  Ha a függőségi ügynök nem indul el, ellenőrizze a hibával kapcsolatos részletes információk a naplókat. A naplózási könyvtár Windows-ügynökök az %Programfiles%\Microsoft függőségi Agent\logs. 

#### <a name="windows-command-line"></a>Windows parancssor
A parancssorból való telepítéshez a következő táblában leírt paraméterek használhatók. A telepítésjelzők listájának megtekintéséhez futtassa a telepítőt a /? jelzővel, a következő módon.

    InstallDependencyAgent-Windows.exe /?

| Jelző | Leírás |
|:--|:--|
| /? | A parancssori kapcsolók listájának lekérése. |
| /S | Beavatkozás nélküli telepítés a felhasználónak szóló üzenetek nélkül. |

A Windows a függőségi ügynök fájlok kerülnek a C:\Program Files\Microsoft függőségi ügynök alapértelmezés szerint.

### <a name="install-the-dependency-agent-on-linux"></a>A függőségi ügynök telepítése Linux rendszeren
A függőségi ügynök telepítve van a Linux-számítógépeken `InstallDependencyAgent-Linux64.bin`, azt a héjparancsfájlt, egy önkicsomagoló bináris. A fájl használatával futtathatja `sh` vagy adjon hozzá végrehajtási engedélyeket magát a fájlt.

>[!NOTE]
> Az ügynök telepítéséhez vagy eltávolításához gyökérszintű hozzáférés szükséges.

Az alábbi lépések segítségével a függőségi ügynök telepítése minden egyes Linux rendszerű számítógépen:

1.  Az ismertetett módszerek valamelyikét a következő Log Analytics-ügynök telepítése [Log Analytics-ügynök – áttekintés](../../azure-monitor/platform/log-analytics-agent.md).
2.  A Linux függőségi ügynök telepítése rendszergazdaként a következő parancs futtatásával:
    
    `sh InstallDependencyAgent-Linux64.bin`

3.  Ha a függőségi ügynök nem indul el, ellenőrizze a hibával kapcsolatos részletes információk a naplókat. Linux-ügynökök a naplózási könyvtár az /var/opt/microsoft/dependency-agent/log.

Megjeleníthetők a telepítés jelzők, futtassa a telepítési program – segítségével jelző a következő.

    InstallDependencyAgent-Linux64.bin -help

| Jelző | Leírás |
|:--|:--|
| – Súgó | A parancssori kapcsolók listájának lekérése. |
| -s | Beavatkozás nélküli telepítés a felhasználónak szóló üzenetek nélkül. |
| --ellenőrzése | Az engedélyek és az operációs rendszer ellenőrzése, az ügynök telepítése nélkül. |

A függőségi ügynök fájlok kerülnek a következő könyvtárak:

| Fájlok | Hely |
|:--|:--|
| Alapvető fájlok | /opt/microsoft/dependency-agent |
| Naplófájlok | /var/opt/microsoft/dependency-agent/log |
| Konfigurációs fájlok | /etc/opt/microsoft/dependency-agent/config |
| Szolgáltatás végrehajtható fájljai | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Bináris tárolófájlok | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Telepítési példaszkriptek
Könnyedén üzembe helyezéséhez egyszerre több kiszolgálón a függőségi ügynököt, a következő parancsfájl például töltse le és telepítse a függőségi ügynököt Windows vagy Linux rendszeren.

### <a name="powershell-script-for-windows"></a>PowerShell-szkript Windowshoz
```PowerShell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Héjszkript Linuxhoz
```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```
## <a name="desired-state-configuration"></a>Célállapot-konfiguráló
A Desired State Configuration (DSC) használatával a függőségi ügynököt telepíti, a következő példakód a xPSDesiredStateConfiguration modul használhatja:

```
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage 
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="remove-the-dependency-agent"></a>A függőségi ügynök eltávolítása
### <a name="uninstall-agent-on-windows"></a>A Windows-ügynök eltávolítása
A rendszergazda eltávolíthatja a függőségi ügynököt Windows Vezérlőpult segítségével.

A rendszergazda a függőségi ügynök eltávolítása a függőségi Agent\Uninstall.exe %Programfiles%\Microsoft is futtathatja.

### <a name="uninstall-agent-on-linux"></a>Linux-ügynök eltávolítása
A függőségi ügynök Linux rendszerben az alábbi paranccsal eltávolítható.

RHEL, CentOs vagy Oracle:

```
sudo rpm -e dependency-agent
```

Ubuntu rendszeren:

```
sudo apt -y purge dependency-agent
```

## <a name="troubleshooting"></a>Hibaelhárítás
Ha problémába ütközik telepítését és futtatását a Service Map, ez a szakasz segítségével. Ha még mindig nem tudja megoldani a problémát, forduljon a Microsoft Support.

### <a name="dependency-agent-installation-problems"></a>Függőségi ügynök telepítési problémák
#### <a name="installer-prompts-for-a-reboot"></a>Telepítő kérni fogja a számítógép újraindítása
A függőségi ügynök *általában* nem igényel újraindítást telepítése vagy eltávolítása után. Egyes ritka esetekben azonban a Windows Server-telepítés folytatásához újraindítás szükséges. Ez akkor történik, amikor a függőség, általában a Microsoft Visual C++ terjeszthető változata, a zárolt fájlok miatt újraindítás szükséges.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>Üzenet "függőségi ügynök telepítése nem sikerült: Nem sikerült telepíteni a Visual Studio modul kódtárak (kód = [code_number]) "jelenik meg

A Microsoft Dependency agent a Microsoft Visual Studio modul kódtárak épül. Egy üzenetet fog kapni, ha probléma van a kódtárak telepítése során. 

A futásidejű kódtár telepítőcsomagokat %LOCALAPPDATA%\temp mappában hozzon létre naplókat. A fájl dd_vcredist_arch_yyyymmddhhmmss.log, ahol *arch* "x86" vagy "amd64" és a *ééééhhnnóóppmm* dátuma és a naplófájl létrehozásának időpontja (24 órás formátum). A napló a probléma, amely blokkolja a telepítési részletesen ismerteti.

Érdemes lehet telepíteni a [legfrissebb futtatókörnyezet-kódtárak](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) saját kezűleg első.

A következő táblázat felsorolja a kód számokat és a javasolt megoldások.

| Kód | Leírás | Megoldás: |
|:--|:--|:--|
| 0x17 | A könyvtár telepítő szükséges a Windows update, amely nem lett telepítve. | Tekintse meg a legutóbbi könyvtár installer naplójának.<br><br>Ha egy sor követi "Windows8.1-KB2999226-x64.msu" hivatkozás "hiba 0x80240017: Nem sikerült végrehajtani az MSU-csomag,"KB2999226 telepítéséhez szükséges előfeltételeket nem rendelkezik. Kövesse az utasításokat az Előfeltételek szakaszban szereplő [a Windows Universal C futásidejű](https://support.microsoft.com/kb/2999226). Szükség lehet, futtassa a Windows Update, és indítsa újra a többször annak érdekében, hogy telepítse az előfeltételeket.<br><br>Futtassa újra a Microsoft Dependency agent telepítő. |

### <a name="post-installation-issues"></a>Telepítés utáni kapcsolatos problémák
#### <a name="server-doesnt-appear-in-service-map"></a>Kiszolgáló nem jelenik meg a Service Map
Ha a függőségi ügynök telepítése sikeres volt, de a kiszolgáló a Service Map megoldás nem jelenik meg:
* A függőségi ügynök sikeres telepítését? Ellenőrzi, hogy ha a szolgáltatás telepítve van és fut ellenőrizheti.<br><br>
**Windows**: Keresse meg a szolgáltatás nevű, "A Microsoft Dependency agent."<br>
**Linux**: Keresse meg a futó folyamat "a microsoft-függőség-ügynök."

* Az, hogy a [ingyenes tarifacsomag Operations Management Suite/Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)? Az ingyenes csomag legfeljebb öt egyedi Service Map kiszolgálók tesz lehetővé. További kiszolgálók nem jelennek meg a Service Map, még akkor is, ha az előzetes öt rendszer már nem küld adatokat.

* A kiszolgáló küldő napló- és teljesítményadatok van a Log Analytics? Lépjen a naplók keresése, és a számítógép a következő lekérdezés futtatásával: 

    Használati |} Ha számítógép == "admdemo-appsvr" |} Összegzés sum(Quantity), any(QuantityUnit) adattípus szerint

Jutott el a különféle eseményekre az eredmények között? Az adatok legutóbbi? Ha igen, a Log Analytics-ügynök megfelelően működik és a Log Analytics használatával való kommunikációhoz. Ha nem, ellenőrizze az ügynököt a kiszolgálón: [Log Analytics-ügynököket Windows hibaelhárítási](https://support.microsoft.com/help/3126513/how-to-troubleshoot-monitoring-onboarding-issues) vagy [Log Analytics-ügynök Linux hibaelhárítási](../../azure-monitor/platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Kiszolgáló megjelenik a Szolgáltatástérkép, de nincs folyamatokat
Ha a kiszolgáló a Service Map, de azt nem rendelkezik folyamat vagy kapcsolati adatokkal, azt jelzi, hogy a függőségi ügynök telepítve és fut, de a kernel-illesztőprogram nem töltődött be. 

Ellenőrizze a C:\Program Files\Microsoft függőségi Agent\logs\wrapper.log fájlt (Windows) vagy /var/opt/microsoft/dependency-agent/log/service.log fájlt (Linux). Az utolsó sort jeleznie kell, miért nem töltődtek be a kernelbe. Például a kernel előfordulhat, hogy nem támogatja a linuxon futó Ha frissítette a kernel.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [használata a Service Map]( service-map.md) üzembe helyezését és konfigurálását követően.
