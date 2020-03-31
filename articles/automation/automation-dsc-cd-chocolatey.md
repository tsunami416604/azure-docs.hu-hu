---
title: Az Azure Automation állapotkonfigurációfolyamatos üzembe helyezése csokoládéval
description: DevOps folyamatos üzembe helyezés az Azure Automation State Configuration, DSC és Chocolatey csomagkezelő használatával.  Példa a teljes JSON Resource Manager sablon és a PowerShell-forrás.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 4445f6e9b72380b66f3282d50871b4283f7fc7fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966739"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Használati példa: Folyamatos üzembe helyezés a virtuális gépekre az Automation State Configuration és a Chocolatey használatával

A DevOps-világban számos olyan eszköz van, amely segítséget nyújt a folyamatos integrációs folyamat különböző pontjainak. Az Azure Automation State Configuration egy üdvözlendő új kiegészítése a lehetőségeket, amelyeket devops csapatok alkalmazhatnak. Ez a cikk bemutatja a folyamatos telepítés (CD) beállítását egy Windows-számítógéphez. Könnyedén kiterjesztheti a technikát, hogy minél több Windows-számítógépet vegyen fel a szerepkörbe (például egy webhelyet), és onnan további szerepkörökre is.

![Folyamatos üzembe helyezés iaaS virtuális gépekhez](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Magas szinten

Van egy kicsit folyik itt, de szerencsére lehet bontani két fő folyamatok:

- Kód írása és tesztelése, majd telepítési csomagok létrehozása és közzététele a rendszer fő- és alverzióihoz.
- A csomagokban a kódot telepítő és végrehajtó virtuális gépek létrehozása és kezelése.  

Miután mindkét alapvető folyamat a helyén van, ez egy rövid lépés, hogy automatikusan frissítse a csomagot futó adott virtuális gép, mint az új verziók jönnek létre és telepítve.

## <a name="component-overview"></a>Összetevő – áttekintés

Az olyan csomagkezelők, mint az [apt-get,](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) elég jól ismertek a Linux világban, de nem annyira a Windows világában.
[Chocolatey](https://chocolatey.org/) egy ilyen dolog, és Scott Hanselman [blogja](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) a témában egy nagy intro. Dióhéjban, Chocolatey lehetővé teszi, hogy telepíteni csomagokat egy központi adattár csomagok egy Windows rendszer segítségével a parancssorból. Létrehozhat és kezelhet saját tárházat, és a Chocolatey tetszőleges számú adattárakból telepíthet csomagokat.

A Kívánt állapotkonfiguráció (DSC) ([áttekintés](/powershell/scripting/dsc/overview/overview)) egy PowerShell-eszköz, amely lehetővé teszi a számítógép kívánt konfigurációjának deklarálását. Például azt mondhatja, hogy "Azt akarom, hogy a Chocolatey telepítve legyen, telepíteni akarom az IIS-t, meg akarom nyitni a 80-as portot, telepíteni szeretném a webhelyem 1.0.0-s verzióját." A DSC Local Configuration Manager (LCM) valósítja meg ezt a konfigurációt. A DSC pull server a gépek konfigurációinak tárházát tartalmazza. Az LCM minden gépen rendszeresen ellenőrzi, hogy a konfiguráció megfelel-e a tárolt konfigurációnak. Jelentheti az állapotot, vagy megpróbálhatja a gépet újra a tárolt konfigurációhoz igazodni. A lekéréses kiszolgálón tárolt konfiguráció tetszésszerint egy gép vagy gépkészlet igazodni fog a megváltozott konfigurációhoz.

Az Azure Automation a Microsoft Azure felügyelt szolgáltatása, amely lehetővé teszi a különböző feladatok automatizálását runbookok, csomópontok, hitelesítő adatok, erőforrások és eszközök, például ütemezések és globális változók használatával.
Az Azure Automation State Configuration kiterjeszti ezt az automatizálási képességet a PowerShell DSC-eszközökre. Itt egy nagy [áttekintést](automation-dsc-overview.md).

A DSC-erőforrás olyan kódmodul, amely speciális képességekkel rendelkezik, például a hálózatkezelés, az Active Directory vagy az SQL Server kezelésével. A Chocolatey DSC Erőforrás többek között tudja, hogyan érheti el a NuGet Kiszolgálót( csomagokat, telepítheti a csomagokat és így tovább. Sok más DSC-erőforrás van a [PowerShell-galériában.](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)
Ezek a modulok az Azure Automation állapotkonfigurációs lekéréses kiszolgálóra vannak telepítve (az Ön által), így a konfigurációk használhatják őket.

Az Erőforrás-kezelő sablonjai deklaratív módon biztosítják az infrastruktúra generálását – például hálózatokat, alhálózatokat, hálózati biztonságot és útválasztást, terheléselosztókat, hálózati adaptereket, virtuális gépeket és így tovább. Az alábbiakban egy [cikket,](../azure-resource-manager/management/deployment-models.md) amely összehasonlítja a Resource Manager telepítési modell (deklaratív) az Azure Service Management (ASM vagy klasszikus) telepítési modell (elengedhetetlen), és ismerteti az alapvető erőforrás-szolgáltatók, számítási, tárolási és hálózati.

Az Erőforrás-kezelő sablon egyik legfontosabb jellemzője, hogy képes telepíteni egy virtuális gép bővítményt a virtuális gép, ahogy ki van építve. A virtuálisgép-bővítmények olyan speciális képességekkel rendelkeznek, mint például egy egyéni parancsfájl futtatása, vírusirtó szoftverek telepítése vagy DSC-konfigurációs parancsfájl futtatása. Sok más típusú virtuálisgép-bővítmények.

## <a name="quick-trip-around-the-diagram"></a>Gyors utazás a diagram körül

A lap elejétől kezdve megírja a kódot, építést és tesztelést, majd létrehoz egy telepítőcsomagot.
Chocolatey képes kezelni a különböző típusú telepítő csomagok, mint például az MSI, MSU, ZIP. És a PowerShell teljes erejét, hogy a tényleges telepítés, ha Chocolateys natív képességek nem egészen fel rá. Helyezze a csomagot olyan helyre, amely elérhető – egy csomagtárba. Ez a használati példa egy nyilvános mappát használ egy Azure blob storage-fiókban, de bárhol lehet. A Chocolatey natívan működik a NuGet kiszolgálókkal és néhány másikkal a csomag metaadatainak kezeléséhez. [Ez a cikk](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) ismerteti a lehetőségeket. Ez a használati példa a NuGet-et használja. A Nuspec a csomagok metaadatai. A Nuspec's "levan fordítva" a NuPkg's-ba, és egy NuGet szerveren tárolja. Amikor a konfiguráció név szerint kér egy csomagot, és egy NuGet-kiszolgálóra hivatkozik, a Chocolatey DSC Erőforrás (most a virtuális gépen) megragadja a csomagot, és telepíti azt. A csomag egy adott verzióját is kérheti.

A kép bal alsó részén található egy Azure Resource Manager-sablon. Ebben a használati példában a virtuálisgép-bővítmény regisztrálja a virtuális gépet az Azure Automation állapotkonfigurációs lekéréses kiszolgálóval (azaz egy lekéréses kiszolgálóval) csomópontként. A konfiguráció a lekéréses kiszolgálón tárolódik.
Valójában kétszer tárolják: egyszer egyszerű szövegként, és egyszer mof fájlként állítják össze (azok számára, akik tudnak az ilyen dolgokról.) A portálon a MOF egy "csomópontkonfiguráció" (szemben az egyszerűen "konfigurációval"). Ez a összetevő, amely egy csomóponthoz kapcsolódik, így a csomópont tudni fogja a konfigurációját. Az alábbi részletek azt mutatják be, hogyan rendelheti hozzá a csomópont konfigurációját a csomóponthoz.

Feltehetőleg már a csúcson vagy a nagy részét csinálod. Létrehozása a nuspec, összeállítása és tárolása egy NuGet szerver egy kis dolog. És már kezeli a virtuális gépeket. A következő lépés a folyamatos üzembe helyezés hez a lekéréses kiszolgáló (egyszer) beállítására, a csomópontok regisztrálására (egyszer), és a konfiguráció létrehozásához és tárolásához (kezdetben). Ezután a csomagok frissítése és üzembe helyezése a tárházban, frissítse a konfiguráció s és csomópont konfiguráció a lekéréses kiszolgálón (ismételje meg szükség).

Ha nem erőforrás-kezelő sablonnal kezdi, az is rendben van. Vannak PowerShell-parancsmagok, amelyek segítségével regisztrálhatja a virtuális gépeket a lekéréses kiszolgálóval és az összes többi. További részletek: Ebben a cikkben: [Bevezetési gépek az Azure Automation állapotkonfiguráció ja) kezelésére.](automation-dsc-onboarding.md)

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>1. lépés: A lekéréses kiszolgáló és az automatizálási fiók beállítása

Hitelesített (`Connect-AzureRmAccount`) PowerShell parancssorból: (eltarthat néhány percig, amíg a lekéréses kiszolgáló be van állítva)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Az automatizálási fiókot a következő régiók bármelyikébe helyezheti (más néven helyszín): USA keleti régiója 2, USA déli középső régiója, USGov Virginia, Nyugat-Európa, Délkelet-Ázsia, Kelet-Japán, Közép-India és Ausztrália délkeleti része, Kanada Közép-, Észak-Európa.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>2. lépés: A Virtuálisgép-bővítmény módosítja az Erőforrás-kezelő sablont

Az [Azure gyorsútmutató-sablonban](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)biztosított virtuális gépregisztráció részletei (a PowerShell DSC virtuálisgép-bővítmény használatával).
Ez a lépés regisztrálja az új virtuális gépet a lekéréses kiszolgálóval az állapotkonfigurációs csomópontok listájában. A regisztráció egy része a csomópontra alkalmazandó csomópontkonfigurációt adja meg. Ez a csomópont konfiguráció nem kell még léteznie a lekéréses kiszolgálón, így rendben van, hogy a 4. De itt a 2. Ebben a használati példában a csomópont "isvbox", a konfiguráció pedig "ISVBoxConfig". Így a csomópont konfigurációs neve (a DeploymentTemplate.json fájlban adható meg) az "ISVBoxConfig.isvbox".

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>3. lépés: A szükséges DSC-erőforrások hozzáadása a lekéréses kiszolgálóhoz

A PowerShell-galériát úgy kell felszerelni, hogy DSC-erőforrásokat telepítsen az Azure Automation-fiókba.
Keresse meg a kívánt erőforrást, és kattintson a "Üzembe helyezés az Azure Automationbe" gombra.

![Példa a PowerShell-galériára](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Az Azure Portalhoz nemrég hozzáadott másik technika lehetővé teszi az új modulok lekérése vagy a meglévő modulok frissítése. Kattintson az Automation-fiók erőforrás, az Eszközök csempe, és végül a modulok csempe. A Galéria tallózása ikon nal megtekintheti a modulok listáját a galériában, részletesen leáshat, és végül importálhat az Automation-fiókba. Ez egy nagyszerű módja annak, hogy a modulok at up to date időről időre. És az importálási funkció ellenőrzi a függőségeket más modulokkal, hogy megbizonyosodjon arról, semmi sem kerül ki a szinkronból.

Vagy ott van a kézi megközelítés. A Windows-rendszerű számítógépekhez készült PowerShell-integrációs modul mappastruktúrája egy kicsit eltér az Azure Automation által elvárt mappastruktúrától.
Ez megköveteli egy kis csípés az Ön részéről. De ez nem nehéz, és ez történik csak egyszer egy erőforrás (kivéve, ha szeretné frissíteni a jövőben.) A PowerShell-integrációs modulok szerzőiről a következő cikkben olvashat: [Integrációs modulok létrehozása az Azure Automationhez](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- Telepítse a szükséges modult a munkaállomásra, az alábbiak szerint:
  - A [Windows Management Framework v5 telepítése](https://aka.ms/wmf5latest) (nem szükséges a Windows 10-hez)
  - `Install-Module –Name MODULE-NAME`< – megragadja a modult a PowerShell-galériából
- A modulmappa `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` másolása ideiglenes mappába
- Minták és dokumentáció törlése a főmappából
- Zip a fő mappát, elnevezése a ZIP fájl pontosan ugyanaz, mint a mappa 
- Helyezze a ZIP-fájlt egy elérhető HTTP-helyre, például egy Azure Storage-fiók blob storage-jába.
- Futtassa ezt a PowerShellt:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

A mellékelt példa ezeket a lépéseket hajtja végre a cChoco és xNetworking. A cChoco speciális kezelését a [megjegyzésekben](#notes) láthatja.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>4. lépés: A csomópontkonfiguráció hozzáadása a lekéréses kiszolgálóhoz

Nincs semmi különös abban, amikor először importálja a konfigurációt a lekéréses kiszolgálóra és lefordítja. Minden további importálás/fordítás ugyanazzal a konfigurációval pontosan ugyanúgy néz ki. Minden alkalommal, amikor frissíti a csomagot, és ki kell tolnia éles környezetben, akkor ezt a lépést a konfigurációs fájl helyességedje után teszi meg – beleértve a csomag új verzióját is. Íme a konfigurációs fájl és a PowerShell:

ISVBoxConfig.ps1:

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

Új-ConfigurationScript.ps1:

```powershell
Import-AzureRmAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Ezek a lépések azt eredményezik, hogy egy "ISVBoxConfig.isvbox" nevű új csomópontkonfiguráció kerül a lekéréses kiszolgálóra. A csomópont konfigurációs neve "configurationName.nodeName" néven van felépítve.

## <a name="step-5-creating-and-maintaining-package-metadata"></a>5. lépés: Csomag metaadatainak létrehozása és karbantartása

A csomagtárba helyezett minden egyes csomaghoz szüksége van egy olyan nuspecra, amely leírja azt.
Ezt a nuspec-t össze kell állítani és tárolni kell a NuGet szerveren. Ezt a folyamatot [itt](https://docs.nuget.org/create/creating-and-publishing-a-package)ismerteti . A MyGet.org nuget kiszolgálóként is használhatja. Eladják ezt a szolgáltatást, de van egy kezdő Termékváltozat, amely ingyenes. A NuGet.org utasításokat találsz a saját NuGet szervered privát csomagjaid hozadékához.

## <a name="step-6-tying-it-all-together"></a>6. lépés: Az egész összekapcsolása

Minden alkalommal, amikor egy verzió sikeres en megy, és jóváhagyásra van jóváhagyva a telepítéshez, a csomag létrejön, a nuspec és a nupkg frissül, és telepítve van a NuGet kiszolgálón. Ezenkívül a konfigurációt (a fenti 4. lépés) frissíteni kell, hogy egyetértsen az új verziószámmal. Meg kell küldeni a lekéréses szerverre, és lekell fordítani.
Ettől a ponttól kezdve a virtuális gépek, amelyek attól függ, hogy a konfiguráció lekéri a frissítést, és telepítse azt. Ezek a frissítések egyszerűek - csak egy-két sor a PowerShell. Az Azure DevOps esetében ezek közül néhány olyan buildfeladatokba van beágyazva, amelyek egy buildben összeláncolhatók. Ez [a cikk](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) további részleteket tartalmaz. Ez a [GitHub-tárház](https://github.com/Microsoft/vso-agent-tasks) részletezi a különböző rendelkezésre álló buildfeladatokat.

## <a name="notes"></a>Megjegyzések

Ez a használati példa egy általános Windows Server 2012 R2-lemezképből származó virtuális géppel kezdődik az Azure-galériából. Tudod elkezd-ból akármi raktározott kép aztán csípés onnan -val a DSC alakzat.
Azonban a lemezképbe sült konfiguráció módosítása sokkal nehezebb, mint a konfiguráció dinamikus frissítése a DSC használatával.

Nem kell használnia a Resource Manager-sablont és a virtuális gép bővítményt, hogy ezt a technikát használja a virtuális gépekhez. És a virtuális gépek nem kell az Azure-ban, hogy a CD-felügyelet alatt. Minden, ami szükséges, hogy Chocolatey kell telepíteni, és az LCM konfigurálva a virtuális gép, így tudja, hol van a lekéréses kiszolgáló.

Természetesen, ha éles környezetben lévő virtuális gépen frissít egy csomagot, ki kell vennie a virtuális gép rotációját a frissítés telepítése közben. Hogyan csinálod ezt széles körben változik. Például egy virtuális gép mögött egy Azure Load Balancer, egyéni mintavétel tadhat hozzá. A virtuális gép frissítése közben a mintavételi végpont 400-at ad vissza. A csípés szükséges okoz ez a változás lehet belül a konfiguráció, mint acsípés, hogy kapcsolja vissza a visszatérő 200, ha a frissítés befejeződött.

A használati példa teljes forrása ebben a GitHubon [található Visual Studio-projektben](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) található.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Automation dsc áttekintése](automation-dsc-overview.md)
* [Azure Automation DSC-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Bevezetési gépek az Azure Automation DSC általi kezeléshez](automation-dsc-onboarding.md)

## <a name="next-steps"></a>További lépések

- Áttekintést az [Azure Automation állapotkonfigurációja](automation-dsc-overview.md) című témakörben talál.
- Első lépések: [Az Azure Automation állapotkonfigurációjának első lépései](automation-dsc-getting-started.md)
- A DSC-konfigurációk összeállításáról, hogy hozzárendelhesse őket a célcsomópontokhoz, olvassa el [a Konfigurációk összeállítása az Azure Automation állapotkonfigurációjában](automation-dsc-compile.md)
- A PowerShell-parancsmagok hivatkozásáról az [Azure Automation állapotkonfigurációs parancsmagjai](/powershell/module/azurerm.automation/#automation)
- A díjszabással kapcsolatos információkért lásd: [Azure Automation State Configuration pricing](https://azure.microsoft.com/pricing/details/automation/)
- Az Azure Automation állapotkonfigurációjának folyamatos üzembe helyezési folyamatban való használatára példa: [Folyamatos üzembe helyezés az Azure Automation állapotkonfiguráció és csokoládés használatával](automation-dsc-cd-chocolatey.md)
