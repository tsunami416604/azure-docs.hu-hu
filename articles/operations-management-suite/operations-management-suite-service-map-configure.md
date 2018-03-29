---
title: Szolgáltatástérkép konfigurálása az Azure-ban |} Microsoft Docs
description: A Service Map az Azure egyik megoldása, amely automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. Ez a cikk a Service Map telepítése a környezetben, és használja azt a különféle forgatókönyvekhez, amik részletesen.
services: operations-management-suite
documentationcenter: ''
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: daseidma;bwren;dairwin
ms.openlocfilehash: c01d18b17906a2b243a46241a6ec5c4b1d9ab8d9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="configure-service-map-in-azure"></a>Szolgáltatástérkép konfigurálása az Azure-ban
A Szolgáltatástérkép automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. Használhatja a kiszolgálók megtekintéséhez, módon úgy gondolja, hogy azok--összekapcsolt rendszerekhez, hogy a kritikus szolgáltatásokhoz. Szolgáltatástérkép kiszolgálók, folyamatok és portok közötti kapcsolatok között nincs konfigurációjával kapcsolatban egy ügynök telepítése nem szükséges bármely TCP-csatlakoztatott architektúra jeleníti meg.

Ez a cikk ismerteti a Service Map és bevezetése az ügynökök konfigurálása részleteit. Szolgáltatástérkép használatával kapcsolatos információkért lásd: [használhatja a Service Map megoldást az Azure-ban](operations-management-suite-service-map.md).

## <a name="dependency-agent-downloads"></a>A függőségi ügynök letöltése
| Fájl | Operációs rendszer | Verzió | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.5.0 | 8B8FE0F6B0A9F589C4B7B52945C2C25DF008058EB4D4866DC45EE2485062C9D7 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.5.0 | 4125A88E60650FF168D6254AB4FCD14CDD3CC1C7B4CF168F3F5F3C1AF30895DD  |


## <a name="connected-sources"></a>Összekapcsolt források
Szolgáltatástérkép az adatok lekérése a Microsoft függőségi ügynök. A függőségi ügynök attól függ, hogy az OMS-ügynököt a kapcsolatok szolgáltatáshoz. Ez azt jelenti, hogy a kiszolgálónak rendelkeznie kell az OMS-ügynököt telepítette és konfigurálta az első, majd a függőségi ügynök telepíthető. A következő táblázat ismerteti a Service Map megoldás támogatja a csatlakoztatott forrásból.

| Csatlakoztatott forrás | Támogatott | Leírás |
|:--|:--|:--|
| Windows-ügynökök | Igen | Szolgáltatástérkép elemzi és Windows-ügynök számítógépekről gyűjt adatokat. <br><br>Kívül a [OMS-ügynököt](../log-analytics/log-analytics-windows-agent.md), Windows-ügynökök szükséges a Microsoft függőségi ügynök. Tekintse meg a [támogatott operációs rendszerek](#supported-operating-systems) operációs rendszerek teljes listáját. |
| Linux-ügynökök | Igen | Szolgáltatástérkép elemzi, és a Linux-ügynök számítógépekről gyűjt adatokat. <br><br>Kívül a [OMS-ügynököt](../log-analytics/log-analytics-linux-agents.md), Linux-ügynököt a Microsoft függőségi ügynök szükséges. Tekintse meg a [támogatott operációs rendszerek](#supported-operating-systems) operációs rendszerek teljes listáját. |
| System Center Operations Manage felügyeleti csoport | Igen | Szolgáltatástérkép elemzi, és összegyűjti az adatokat a Windows és Linux-ügynökök a csatlakoztatott [System Center Operations Manager felügyeleti csoport](../log-analytics/log-analytics-om-agents.md). <br><br>Naplóelemzési a System Center Operations Manager ügynök számítógépről közvetlen kapcsolatra szükség. A Naplóelemzési munkaterületet adat továbbítódik a felügyeleti csoportból.|
| Azure Storage-fiók | Nem | Szolgáltatástérkép adatokat gyűjt a ügynök számítógépekről, nincsenek adatok, az Azure Storage-ból gyűjtését. |

Szolgáltatástérkép csak 64 bites platformokat támogatja.

A Windows, a Microsoft Monitoring Agent (MMA) segítségével a System Center Operations Manager és a Naplóelemzési gyűjtse össze és küldése figyelési adatok. (Ez az ügynök neve a System Center Operations Manager ügynök, OMS-ügynököt, Log Analytics Agent, MMA vagy közvetlen ügynök, attól függően, hogy a környezetben.) A System Center Operations Manager és a Naplóelemzési adja meg a MMA különböző out-kiszállítási verzióit. Ezen verziói egyes jelenthetik-e a System Center Operations Manager Naplóelemzési, vagy mindkettőt.  

A Linux-, Linux összegyűjti és figyelési adatok szolgáltatáshoz küld az OMS-ügynököt. Szolgáltatástérkép kiszolgálókon közvetlen OMS-ügynök, vagy a System Center Operations Manager felügyeleti csoportok keresztül Naplóelemzési csatolt kiszolgálók használható.  

Ebben a cikkben kifejezés összes ügynököt – hogy Linux vagy a Windows, hogy egy System Center Operations Manager felügyeleti csoport vagy közvetlenül Naplóelemzési –, a "OMS-ügynököt." A megadott központi telepítés nevét, az ügynök csak akkor, ha szükség van a környezetben fogjuk használni.

A Service Map ügynök nem továbbít adatokat saját magát, és nem igényel módosításokat tűzfalak vagy portok. A Service Map adatai mindig átkerülnek az OMS-ügynök szolgáltatáshoz, vagy közvetlenül az OMS-átjárón keresztül.

![Szolgáltatástérkép ügynökök](media/oms-service-map/agents.png)

Ha egy System Center Operations Manager-ügyfél Naplóelemzési csatlakoztatott felügyeleti csoport:

- Ha a System Center Operations Manager-ügynökök hozzáférjenek az internetes Log Analyticshez való csatlakozáshoz, nincs szükség semmilyen további konfigurációra.  
- Ha a System Center Operations Manager-ügynökök az interneten keresztül nem tud hozzáférni a Naplóelemzési, kell együttműködni a System Center Operations Manager az OMS-átjáró konfigurálása.
  
Ha közvetlen OMS-ügynököt használ, akkor az OMS-ügynököt maga Naplóelemzési vagy az OMS-átjáró konfigurálása. Az OMS-átjáró tölthető le: a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

### <a name="management-packs"></a>Felügyeleti csomagok
Amikor a Szolgáltatástérkép aktiválva van a Naplóelemzési munkaterület, 300 KB-os felügyeleti csomag futó Windows-kiszolgálók elküldi. Ha a System Center Operations Manager ügynököt használ egy [csatlakoztatott felügyeleti csoport](../log-analytics/log-analytics-om-agents.md), a Service Map felügyeleti csomag a System Center Operations Manager telepítése. Ha az ügynököt közvetlenül csatlakoztatott, Log Analyticshez nyújt a felügyeleti csomag.

A felügyeleti csomag neve Microsoft.IntelligencePacks.ApplicationDependencyMonitor. %ProgramFiles%\Microsoft figyelés Agent\Agent\Health szolgáltatás State\Management Packs\ írás. A felügyeleti csomagot használó adatforrás-e % Program files%\Microsoft figyelés Agent\Agent\Health szolgáltatás State\Resources\<AutoGeneratedID > \Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="installation"></a>Telepítés
### <a name="install-the-dependency-agent-on-microsoft-windows"></a>A függőségi ügynök telepíthető a Microsoft Windows
Telepítéséhez vagy az ügynök eltávolításához rendszergazdai jogosultság szükséges.

A függőségi ügynök telepítve van a Windows rendszerű számítógépeken InstallDependencyAgent-Windows.exe keresztül. Ha a végrehajtható fájl kapcsolók nélkül futtatja, akkor elindít egy varázslót, amelyeket követve párbeszédes formában történő telepítéséhez.  

Az alábbi lépések segítségével a függőségi ügynök telepítése a Windows számítógépekre:

1.  Telepítse az OMS-ügynököt található utasítások segítségével: [csatlakozás Windows-számítógépek számára az Azure Naplóelemzés szolgáltatás](../log-analytics/log-analytics-windows-agent.md).
2.  A Windows-ügynök letöltése, és futtassa a következő paranccsal: <br>`InstallDependencyAgent-Windows.exe`
3.  Kövesse a varázsló az ügynök telepítéséhez.
4.  Ha a függőségi ügynök nem indul el, tekintse meg a hibával kapcsolatos részletes információk a naplókat. A Windows-ügynökök a naplózási könyvtár %Programfiles%\Microsoft függőségi Agent\logs. 

#### <a name="windows-command-line"></a>A Windows parancssor
Beállítások a következő táblázat segítségével telepítése a parancssorból. A telepítési jelző listájának megtekintéséhez futtassa a telepítő használatával a /? Ez a jelző az alábbiak szerint.

    InstallDependencyAgent-Windows.exe /?

| Jelzője | Leírás |
|:--|:--|
| /? | A parancssori kapcsolók listájának lekérése. |
| /S | Felhasználói beavatkozás nélküli telepítés végrehajtásához. |

A Windows függőségi ügynök fájlok alapértelmezés szerint a C:\Program Files\Microsoft függőségi ügynök kerülnek.

### <a name="install-the-dependency-agent-on-linux"></a>A függőségi ügynök telepíthető Linux
Az ügynök telepítéséhez és konfigurálásához rendszergazdai hozzáférés szükséges.

A függőségi ügynök telepítve van a Linux rendszerű számítógépeken InstallDependencyAgent Linux64.bin, egy önkicsomagoló bináris héjparancsfájlt keresztül. Futtassa a fájlt megosztása, vagy adja hozzá a végrehajtási engedélyeket magában a fájlban.
 
A függőségi ügynök telepítése minden egyes Linux-számítógép tegye a következőket:

1.  Telepítse az OMS-ügynököt található utasítások segítségével: [adatok gyűjtésére és kezelésére a Linux rendszerű számítógépek](https://technet.microsoft.com/library/mt622052.aspx).
2.  Legfelső szintű a Linux függőségi ügynök telepítése a következő paranccsal:<br>`sh InstallDependencyAgent-Linux64.bin`
3.  Ha a függőségi ügynök nem indul el, tekintse meg a hibával kapcsolatos részletes információk a naplókat. A Linux-ügynökök a naplózási könyvtár /var/opt/microsoft/dependency-agent/log.

Listáját a telepítési jelző, futtassa a telepítési program - segítségével jelzőt az alábbiak szerint.

    InstallDependencyAgent-Linux64.bin -help

| Jelzője | Leírás |
|:--|:--|
| -Súgó | A parancssori kapcsolók listájának lekérése. |
| -s | Felhasználói beavatkozás nélküli telepítés végrehajtásához. |
| --ellenőrzése | Ellenőrizze az engedélyeit és az operációs rendszer, de nem telepíti az ügynököt. |

A függőségi ügynök fájlok kerülnek, a következő könyvtárban:

| Fájlok | Hely |
|:--|:--|
| Alapvető fájljait | /opt/microsoft/dependency-agent |
| Naplófájlok | /var/opt/microsoft/dependency-agent/log |
| Olyan konfigurációs fájlt | /etc/opt/microsoft/dependency-agent/config |
| Végrehajtható fájlok | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| A tároló bináris fájljai | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Telepítési parancsfájl példák
Könnyen telepíthető egyszerre több kiszolgálón a függőségi ügynök, áttekinteni parancsfájl használata. Az alábbi parancsfájl-példák segítségével töltse le és a függőségi ügynök telepíthető Windows vagy Linux.

### <a name="powershell-script-for-windows"></a>A Windows PowerShell-parancsfájl
```PowerShell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>A Linux rendszerhez parancsfájl
```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="azure-vm-extension"></a>Az Azure Virtuálisgép-bővítmény
Az Azure virtuális gépek használata a függőségi ügynök könnyedén telepítheti egy [Azure Virtuálisgép-bővítmény](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).  Az Azure Virtuálisgép-bővítmény használatával telepíthet a függőségi ügynök a virtuális gépek közvetlenül az Azure Resource Manager-sablon a virtuális gép vagy egy PowerShell-parancsfájlt keresztül.  Nincs elérhető (DependencyAgentWindows) Windows és Linux (DependencyAgentLinux) bővítménye.  Ha az Azure Virtuálisgép-bővítmény használatával telepít, az ügynökök automatikusan frissíthető a legújabb verzióra.

Az Azure PowerShell Virtuálisgép-bővítmény telepítéséhez használhatja a következő példa:
```PowerShell
#
# Deploy the Dependency Agent to every VM in a Resource Group
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

Még egyszerűbb legyen a függőségi ügynök minden egyes a virtuális gépen ahhoz, hogy az ügynök szerepeljen az Azure Resource Manager-sablon.  Vegye figyelembe, hogy a függőségi ügynök továbbra is függ az OMS-ügynököt, ezért a [OMS-ügynök Virtuálisgép-bővítmény](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-vm-extension) először telepítenie kell.  A következő szövegrészletet JSON adhatók hozzá a *erőforrások* a sablon szakasza.
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


## <a name="desired-state-configuration"></a>Célállapot-konfiguráló
A célállapot-konfiguráció keresztül függőségi ügynök telepítéséhez használhatja a xPSDesiredStateConfiguration modul és a kód a következőhöz hasonló:
```
configuration ServiceMap {

Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

Node localhost
{ 
    # Download and install the Dependency Agent
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

## <a name="uninstallation"></a>Eltávolítás
### <a name="uninstall-the-dependency-agent-on-windows"></a>Távolítsa el a függőségi ügynököt Windows rendszeren
A rendszergazda eltávolíthatja a függőségi ügynök a Windows Vezérlőpult segítségével.

A rendszergazda %Programfiles%\Microsoft függőségi Agent\Uninstall.exe távolítsa el a függőségi ügynököt is futtathatja.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Távolítsa el a függőségi ügynököt Linux rendszeren
A függőség ügynököt eltávolíthatja a Linux az alábbi paranccsal.
<br>RHEL, CentOs vagy Oracle:
```
sudo rpm -e dependency-agent
```
Ubuntu:
```
sudo apt -y purge dependency-agent
```
## <a name="troubleshooting"></a>Hibaelhárítás
Ha bármely telepítését és futtatását a Service Map, akkor ez a szakasz segítséget. Ha még mindig nem tudja megoldani a problémát, forduljon a Microsoft Support.

### <a name="dependency-agent-installation-problems"></a>A függőségi ügynök telepítési problémák
#### <a name="installer-asks-for-a-reboot"></a>Telepítő kéri az újraindítást
A függőségi ügynök *általában* nem kell indítani a telepítés vagy eltávolítás. Ritka esetekben azonban a Windows Server való telepítés folytatásához újraindítás szükséges. Ez akkor fordul elő, amikor a függőség, általában a Microsoft Visual C++ terjeszthető változata, újra kell indítani a zárolt fájlok miatt.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>Üzenet "függőségi ügynök telepítése nem sikerült: nem sikerült telepíteni a Visual Studio futtató könyvtárak (kód = [code_number])" jelenik meg

A Microsoft függőségi ügynök a Microsoft Visual Studio futtató könyvtárak épül. Üzenetet fog kapni, ha probléma van a kódtárak telepítése során. 

A futásidejű könyvtár telepítők naplók a %LOCALAPPDATA%\temp mappában hozzon létre. A fájl dd_vcredist_arch_yyyymmddhhmmss.log, ahol *architektúrája* "x86" vagy "amd64" és *ééééhhnnóóppmm* dátuma és a naplófájl létrehozásának időpontja (24 órás formátumban). A napló tartalmazza a problémát, amely blokkolja a telepítési adatait.

A telepítendő hasznos lehet a [legújabb futtató könyvtárak](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) saját kezűleg első.

A következő táblázat felsorolja a kód számok és javasolt megoldások.

| Kód | Leírás | Megoldás: |
|:--|:--|:--|
| 0x17 | A könyvtár telepítő szükséges a Windows update, amely nem lett telepítve. | Keresse meg a legutóbbi könyvtár telepítő naplóban.<br><br>Ha egy sor követi "Windows8.1-KB2999226-x64.msu" mutató hivatkozás "hiba 0x80240017: MSU csomag végrehajtása sikertelen," nincs KB2999226 telepítéséhez szükséges előfeltételeket. Az Előfeltételek című szakaszában található útmutatást [Windows Universal C futásidejű](https://support.microsoft.com/kb/2999226). Előfordulhat, hogy szeretné futtatni a Windows Update szolgáltatást, és indítsa újra a többször az előfeltételek telepítéséhez.<br><br>Futtassa újra a Microsoft függőségi ügynök telepítőjét. |

### <a name="post-installation-issues"></a>Telepítés utáni problémák
#### <a name="server-doesnt-appear-in-service-map"></a>Kiszolgáló nem jelenik meg a Service Map
Ha a függőségi ügynök telepítése sikeres volt, de nem jelenik meg a kiszolgáló a Service Map megoldásban:
* A függőségi ügynök sikeres telepítését? Ellenőrzi, hogy ha a szolgáltatás telepítve van és fut ellenőrizheti ezt.<br><br>
**Windows**: keresse meg a "Microsoft Dependency ügynök." nevű szolgáltatás<br>
**Linux**: Ellenőrizze, hogy a futó feldolgozni a "microsoft-függőség-ügynök."

* A rendszer a [ingyenes tarifacsomag Operations Management Suite/Log Analyticshez](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)? Az ingyenes csomagban lehetővé teszi, hogy legfeljebb öt egyedi Szolgáltatástérkép kiszolgálók. További kiszolgálók nem jelenik meg a Service Map, még akkor is, ha az előző öt már nem küld adatokat.

* A küldő kiszolgálónapló és teljesítményadatok van a Naplóelemzési? Nyissa meg a keresési napló, és futtassa a következő lekérdezést a számítógép: 

        * Computer="<your computer name here>" | measure count() by Type
        
  Kapott események számos az eredmények között? Az elmúlt adatai? Ha igen, az OMS-ügynököt megfelelően működik-e és Naplóelemzési kommunikál. Ha nem, nézze meg az OMS-ügynököt a kiszolgálón: [OMS ügynök a Windows hibaelhárítási](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) vagy [OMS-ügynököt a Linux hibaelhárítási](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Kiszolgáló megjelenik a Szolgáltatástérkép, de nincs folyamat
Ha a kiszolgáló a Service Map látja, de nincs adatainak feldolgozása vagy kapcsolat van, ez azt jelzi, hogy a függőségi ügynök telepítve és fut, de a kernel-illesztőprogram nem töltődött be. 

Ellenőrizze a C:\Program Files\Microsoft függőségi Agent\logs\wrapper.log fájlt (Windows) vagy /var/opt/microsoft/dependency-agent/log/service.log fájl (Linux). Az utolsó sort kell jeleznie, miért nem töltődött be a rendszermag. Például a kernel lehetséges, hogy nem támogatja a Linux Ha frissítette a kernel.

## <a name="data-collection"></a>Adatgyűjtés
Minden ügynök körülbelül 25 MB naponkénti, attól függően, hogy hogyan összetett a rendszer függőségek használhatók átviteléhez számíthat. Minden ügynök 15 másodpercenként Szolgáltatástérkép függőségi adatokat küld.  

A függőségi ügynök általában akkor 0,1 rendszermemória és 0,1 rendszer CPU.

## <a name="supported-azure-regions"></a>Támogatott Azure-régiók
Jelenleg Szolgáltatástérkép érhető el a következő Azure-régiók:
- USA keleti régiója
- Nyugat-Európa
- USA nyugati középső régiója
- Délkelet-Ázsia


## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
Az alábbiakban a támogatott operációs rendszerek a függőségi ügynök listázása. Szolgáltatástérkép nem támogatja 32 bites operációs rendszert.

### <a name="windows-server"></a>Windows Server
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Asztali Windows
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, a CentOS Linux és az Oracle Linux (az RHEL Kernel)
- Csak az alapértelmezett és az SMP Linux kernel verziókban támogatott.
- Nem szabványos kernel kiadását, például a Xen, és a fizikai nem támogatottak a Linux-disztribúció. A rendszer, amely a kiadás "2.6.16.21-0.8-xen" karakterlánc például nem támogatott.
- Egyéni mag, beleértve a szabványos kernelek újrafordításainak maximális nem támogatottak.
- CentOSPlus kernel nem támogatott.
- Ez a cikk későbbi részében Oracle szoros vállalati Kernel (UEK) vonatkozik.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| Operációs rendszer verziója | Kernel-verzió |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| Operációs rendszer verziója | Kernel-verzió |
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

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| Operációs rendszer verziója | Kernel-verzió |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416<br>2.6.18-417<br>2.6.18-419<br>2.6.18-420 |

### <a name="ubuntu-server"></a>Ubuntu Server
- Egyéni mag, beleértve a szabványos kernelek újrafordításainak maximális nem támogatottak.

| Operációs rendszer verziója | Kernel-verzió |
|:--|:--|
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Szoros vállalati Kernel az Oracle Enterprise Linux
#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Operációs rendszer verziója | Kernel-verzió
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Operációs rendszer verziója | Kernel-verzió
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Operációs rendszer verziója | Kernel-verzió
|:--|:--|
| 11 SP2 | 3.0.101-0.7 |
| 11 SP3 | 3.0.101-0.47 |
| 11 SP4 | 3.0.101-65 |


## <a name="diagnostic-and-usage-data"></a>diagnosztikai és használati adatok
A Microsoft automatikusan használati és teljesítményadatokat gyűjt a Szolgáltatástérkép szolgáltatás használata. A Microsoft ezeket az adatokat ellátására és fejlesztésére minőségének, biztonsági és integritását, a Service Map szolgáltatást használja. A szoftverek, például az operációs rendszer és verzió konfigurációs információinak szerepel. Is IP-cím, a DNS-nevét és a munkaállomás nevét ahhoz, hogy pontos és hatékony hibaelhárítási képességeket biztosítanak. Nem gyűjtünk neveket, címeket és egyéb kapcsolattartási adatait.

Az adatok gyűjtésével és felhasználásával további információkért lásd: a [Microsoft Online Services adatvédelmi nyilatkozatát](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>További lépések
- Megtudhatja, hogyan [használja a Service Map](operations-management-suite-service-map.md) telepítése és konfigurálása után.
