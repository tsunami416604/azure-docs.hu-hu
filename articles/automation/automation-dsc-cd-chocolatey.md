---
title: Az Azure Automation állapotkonfiguráció folyamatos üzembe helyezése a Chocolatey-val
description: A DevOps folyamatos üzembe helyezését az Azure Automation állapotkonfigurációjával a Chocolatey csomagkezelővel ismerteti. Tartalmaz egy példát a teljes JSON Resource Manager sablon és a PowerShell-forrás.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 0c61a431b985e494148500ed0a7aeb106534ed2c
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392120"
---
# <a name="provide-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Folyamatos üzembe helyezés a virtuális gépeken az Automation State Configuration és a Chocolatey használatával

A DevOps-világban számos olyan eszköz van, amely segítséget nyújt a folyamatos integrációs folyamat különböző pontjainak. Az Azure Automation [State Configuration](automation-dsc-overview.md) egy üdvözlendő új kiegészítése a lehetőségeket, amelyeket devops csapatok alkalmazhatnak. 

Az Azure Automation a Microsoft Azure felügyelt szolgáltatása, amely lehetővé teszi a különböző feladatok automatizálását runbookok, csomópontok és megosztott erőforrások, például hitelesítő adatok, ütemezések és globális változók használatával. Az Azure Automation State Configuration kiterjeszti ezt az automatizálási képességet a PowerShell kívánt állapotkonfigurációs (DSC) eszközeire. Itt egy nagy [áttekintést](automation-dsc-overview.md).

Ez a cikk bemutatja, hogyan állítható be a folyamatos telepítés (CD) Windows rendszerű számítógépekhez. Könnyedén kiterjesztheti a technikát, hogy annyi Windows-számítógépet vegyen fel a szerepkörbe, amennyi szükséges, például egy webhelyet, és onnan további szerepkörökre léphet.

![Folyamatos üzembe helyezés iaaS virtuális gépekhez](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="at-a-high-level"></a>Magas szinten

Van egy kicsit folyik itt, de szerencsére lehet bontani két fő folyamatok:

- Kód írása és tesztelése, majd telepítési csomagok létrehozása és közzététele a rendszer fő- és alverzióihoz.
- A csomagokban a kódot telepítő és végrehajtó virtuális gépek létrehozása és kezelése.  

Miután mindkét alapvető folyamat a helyén van, könnyen frissítheti a csomagot a virtuális gépeken, mivel az új verziók jönnek létre és települnek.

## <a name="component-overview"></a>Összetevő – áttekintés

Az olyan csomagkezelők, mint az [apt-get,](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) jól ismertek a Linux világában, de nem annyira a Windows világában.
[Chocolatey](https://chocolatey.org/) egy ilyen dolog, és Scott Hanselman [blogja](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) a témában egy nagy bevezetés. Dióhéjban, Chocolatey lehetővé teszi, hogy használja a parancssort telepíteni csomagokat egy központi adattár-ra egy Windows operációs rendszer. Létrehozhat és kezelhet saját tárházat, és a Chocolatey tetszőleges számú adattárakból telepíthet csomagokat.

[A PowerShell DSC](/powershell/scripting/dsc/overview/overview) egy PowerShell-eszköz, amely lehetővé teszi a kívánt konfiguráció deklarálását a géphez. Ha például azt szeretné, hogy a Chocolatey telepítve legyen, az IIS telepítve legyen, a 80-as port meglegyen nyitva, és a webhely 1.0.0-s verziója legyen telepítve, a DSC Local Configuration Manager (LCM) megvalósítja ezt a konfigurációt. A DSC lekéréses kiszolgáló a gépek konfigurációinak tárházát tartalmazza. Az LCM minden gépen rendszeresen ellenőrzi, hogy a konfiguráció megfelel-e a tárolt konfigurációnak. Jelentheti az állapotot, vagy megpróbálhatja a gépet újra a tárolt konfigurációhoz igazodni. A lekéréses kiszolgálón tárolt konfiguráció tetszésszerint egy gép vagy gépkészlet igazodni fog a megváltozott konfigurációhoz.

A DSC-erőforrás olyan kódmodul, amely speciális képességekkel rendelkezik, például a hálózatkezelés, az Active Directory vagy az SQL Server kezelésével. A Chocolatey DSC Erőforrás többek között tudja, hogyan érheti el a NuGet Kiszolgálót( csomagokat, telepítheti a csomagokat és így tovább. Sok más DSC-erőforrás van a [PowerShell-galériában.](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title) Ezeket a modulokat az Azure Automation-állapotkonfigurációs lekéréses kiszolgálóra telepíti a konfigurációk számára.

Az Erőforrás-kezelő sablonjai deklaratív módon biztosítják az infrastruktúra generálását, például hálózatokat, alhálózatokat, hálózati biztonságot és útválasztást, terheléselosztókat, hálózati adaptereket, virtuális gépeket és így tovább. Az alábbiakban egy [cikket,](../azure-resource-manager/management/deployment-models.md) amely összehasonlítja a Resource Manager telepítési modell (deklaratív) az Azure Service Management (ASM vagy klasszikus) telepítési modell (elengedhetetlen). Ez a cikk ismerteti az alapvető erőforrás-szolgáltatók: számítási, tárolási és hálózati.

Az Erőforrás-kezelő sablon egyik legfontosabb jellemzője, hogy képes telepíteni egy virtuális gép bővítményt a virtuális gép, ahogy ki van építve. A virtuálisgép-bővítmények speciális képességekkel rendelkeznek, például egyéni parancsfájl futtatásával, víruskereső szoftverek telepítésével és DSC-konfigurációs parancsfájl futtatásával. Sok más típusú virtuálisgép-bővítmények.

## <a name="quick-trip-around-the-diagram"></a>Gyors utazás a diagram körül

A lap tetejétől kezdve megírja a kódot, létrehozza, teszteli, majd létrehoz egy telepítőcsomagot. Chocolatey képes kezelni a különböző típusú telepítő csomagok, mint például az MSI, MSU, ZIP. És a PowerShell teljes erejét, hogy a tényleges telepítés, ha Chocolatey natív képességek nem fel rajta. Helyezze a csomagot olyan helyre, amely elérhető – egy csomagtárba. Ez a használati példa egy nyilvános mappát használ egy Azure blob storage-fiókban, de bárhol lehet. A Chocolatey natívan működik a NuGet kiszolgálókkal és néhány másikkal a csomag metaadatainak kezeléséhez. [Ez a cikk](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) ismerteti a lehetőségeket. A használati példa a NuGet-et használja. A Nuspec a csomagok metaadatai. A Nuspec adatokat nupkg-ba állítják össze, és nuget szerveren tárolják. Amikor a konfiguráció név szerint kér egy csomagot, és egy NuGet-kiszolgálóra hivatkozik, a virtuális gép Csokoládés DSC-erőforrása megragadja a csomagot, és telepíti azt. A csomag egy adott verzióját is kérheti.

A kép bal alsó sarkában van egy Azure Resource Manager sablon. Ebben a használati példában a virtuálisgép-bővítmény regisztrálja a virtuális gépet az Azure Automation-állapotkonfigurációs lekéréses kiszolgálóval csomópontként. A konfiguráció tárolják a lekéréses szerver kétszer: egyszer egyszerű szövegként, és egyszer lefordítani, mint egy MOF fájlt. Az Azure Portalon a MOF egy csomópont konfigurációját jelöli, szemben egy egyszerű konfigurációval. Ez a összetevő, amely egy csomóponthoz kapcsolódik, így a csomópont tudni fogja a konfigurációját. Az alábbi részletek azt mutatják be, hogyan rendelheti hozzá a csomópont konfigurációját a csomóponthoz.

A Nuspec létrehozása, összeállítása és tárolása egy NuGet szerveren egy kis dolog. És már kezeli a virtuális gépeket. 

A következő lépés a folyamatos üzembe helyezés szükséges üzembe helyezése a lekéréses kiszolgáló egyszer, regisztrálja a csomópontok at vele egyszer, és létrehozza és tárolja a kezdeti konfiguráció a kiszolgálón. A csomagok frissítése és üzembe helyezése a tárházban, csak frissítenie kell a konfigurációs és csomópont-konfiguráció a lekéréses kiszolgálón, ha szükséges.

Ha nem egy Erőforrás-kezelő sablonnal kezdi, az rendben van. Vannak PowerShell-parancsok, amelyek segítségével regisztrálhatja a virtuális gépeket a lekéréses kiszolgálón. További információ: [Bevezetési gépek az Azure Automation állapotkonfigurációja általi kezeléshez.](automation-dsc-onboarding.md)

## <a name="about-the-usage-example"></a>A használati példa

A használati példa ebben a cikkben kezdődik egy virtuális gép egy általános Windows Server 2012 R2 lemezkép az Azure-katalógusból. Tudod elkezd-ból akármi raktározott kép aztán csípés onnan -val a DSC alakzat.
Azonban a lemezképbe sült konfiguráció módosítása sokkal nehezebb, mint a konfiguráció dinamikus frissítése a DSC használatával.

Nem kell használnia a Resource Manager-sablont és a virtuális gép bővítményt, hogy ezt a technikát használja a virtuális gépekhez. És a virtuális gépek nem kell az Azure-ban, hogy a CD-felügyelet alatt. Minden, ami szükséges, hogy Chocolatey kell telepíteni, és az LCM konfigurálva a virtuális gép, így tudja, hol van a lekéréses kiszolgáló.

Amikor éles környezetben lévő virtuális gépen frissít egy csomagot, a frissítés telepítése közben ki kell vennie a virtuális gép rotációját. Hogyan csinálod ezt széles körben változik. Például egy virtuális gép mögött egy Azure Load Balancer, egyéni mintavétel tadhat hozzá. A virtuális gép frissítése közben a mintavételi végpont 400-at ad vissza. A csípés szükséges okoz ez a változás lehet belül a konfiguráció, mint acsípés, hogy kapcsolja vissza a visszatérő 200, ha a frissítés befejeződött.

A használati példa teljes forrása ebben a GitHubon [található Visual Studio-projektben](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) található.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>1. lépés: A lekéréses kiszolgáló és az Automation-fiók beállítása

Hitelesített (`Connect-AzAccount`) PowerShell parancssorból: (eltarthat néhány percig, amíg a lekéréses kiszolgáló be van állítva)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Automation-fiókját a következő régiók (más néven helyszínek) valamelyikébe helyezheti: USA keleti régiója 2, USA déli középső régiója, USGov Virginia, Nyugat-Európa, Délkelet-Ázsia, Kelet-Japán, Közép-India és Ausztrália délkeleti része, Kanada Közép-, Észak-Európa.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>2. lépés: A Virtuálisgép-bővítmény tweaks módosítása az Erőforrás-kezelő sablonhoz

Az [Azure gyorsútmutató-sablonban](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)biztosított virtuális gépregisztráció részletei (a PowerShell DSC virtuálisgép-bővítmény használatával).
Ez a lépés regisztrálja az új virtuális gépet a lekéréses kiszolgálóval az állapotkonfigurációs csomópontok listájában. A regisztráció egy része a csomópontra alkalmazandó csomópontkonfigurációt adja meg. Ez a csomópont konfiguráció nem kell még léteznie a lekéréses kiszolgálón, így rendben van, hogy a 4 lépés, ahol ez történik az első alkalommal. De itt a 2. Ebben a használati példában a csomópont "isvbox", a konfiguráció pedig "ISVBoxConfig". Így a csomópont konfigurációs neve (a DeploymentTemplate.json fájlban adható meg) az "ISVBoxConfig.isvbox".

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>3. lépés: A szükséges DSC-erőforrások hozzáadása a lekéréses kiszolgálóhoz

A PowerShell-galériát úgy kell felszerelni, hogy DSC-erőforrásokat telepítsen az Azure Automation-fiókba.
Keresse meg a kívánt erőforrást, és kattintson a "Üzembe helyezés az Azure Automationbe" gombra.

![Példa a PowerShell-galériára](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Egy másik, az Azure Portalhoz nemrég hozzáadott módszer lehetővé teszi, hogy új modulokat kér, vagy frissítse a meglévő modulokat. Kattintson az Automation-fiók erőforrás, az Eszközök csempe, és végül a modulok csempe. A Galéria tallózása ikon lehetővé teszi a modulok listájának megtekintését a katalógusban, a részletek részletezését és végső soron az Automation-fiókba való importálást. Ez egy nagyszerű módja annak, hogy a modulok at up to date időről időre. És az importálási funkció ellenőrzi a függőségeket más modulokkal, hogy megbizonyosodjon arról, semmi sem kerül ki a szinkronból.

Van egy manuális megközelítés is, amelyet csak egyszer használnak erőforrásonként, hacsak nem szeretné később frissíteni. A PowerShell-integrációs modulok szerzőiről az [Azure Automation integrációs modulok készítése](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)című témakörben talál további információt.

>[!NOTE]
>A Windows-rendszerű számítógépekHez készült PowerShell-integrációs modul mappastruktúrája egy kicsit eltér az Azure Automation által elvárt mappastruktúrától. 

1. Telepítse a [Windows Management Framework v5-öt](https://aka.ms/wmf5latest) (nem szükséges a Windows 10-hez).

2. Telepítse az integrációs modult.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Másolja a modulmappát **a c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME** mappából egy ideiglenes mappába.

4. Minták és dokumentáció törlése a főmappából.

5. Zip a fő mappát, elnevezése a ZIP fájlt a nevét a mappát.

6. Helyezze a ZIP-fájlt egy elérhető HTTP-helyre, például egy Azure Storage-fiók blob storage-jába.

7. Futtassa a következő parancsot.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

A mellékelt példa megvalósítja ezeket a lépéseket a cChoco és xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>4. lépés: A csomópontkonfiguráció hozzáadása a lekéréses kiszolgálóhoz

Nincs semmi különös abban, amikor először importálja a konfigurációt a lekéréses kiszolgálóra és lefordítja. Az összes későbbi importálás vagy összeállítás ugyanannak a konfigurációnak pontosan ugyanúgy néz ki. Minden alkalommal, amikor frissíti a csomagot, és ki kell tolnia éles környezetben, akkor ezt a lépést a konfigurációs fájl helyességedje után teszi meg – beleértve a csomag új verzióját is. Itt a konfigurációs fájl **ISVBoxConfig.ps1:**

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

Itt van a **New-ConfigurationScript.ps1** parancsfájl (az Az modul használatára módosítva):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Ezek a lépések azt eredményezik, hogy egy **isvboxconfig.isvbox** nevű új csomópontkonfiguráció kerül a lekéréses kiszolgálóra. A csomópont konfigurációjának `configurationName.nodeName`neve a .

## <a name="step-5-create-and-maintain-package-metadata"></a>5. lépés: Csomag metaadatainak létrehozása és karbantartása

A csomagtárba helyezett minden egyes csomaghoz szüksége van egy Nuspec-re, amely leírja azt. Le kell fordítani és tárolni kell a NuGet szerveren. Ezt a folyamatot [itt](https://docs.nuget.org/create/creating-and-publishing-a-package)ismerteti . 

A **MyGet.org** nuget kiszolgálóként is használhatja. Lehet kapni ezt a szolgáltatást, de te egy ingyenes kezdő Termékváltozat. A [NuGet-nél](https://www.nuget.org/)útmutatást találsz a saját NuGet szervered privát csomagjaidhoz való telepítéséhez.

## <a name="step-6-tie-it-all-together"></a>6. lépés: Tie az egészet együtt

Minden alkalommal, amikor egy verzió sikeres en megy, és jóváhagyásra van jóváhagyva a telepítéshez, a csomag létrejön, és a nuspec és a nupkg frissül, és a NuGet kiszolgálóra kerül. A konfigurációt (4. lépés) is frissíteni kell, hogy egyetértsen az új verziószámmal. Ezután el kell küldeni a lekéréses kiszolgálóra, és le kell fordítani.

Ettől a ponttól kezdve a virtuális gépek, amelyek attól függ, hogy a konfiguráció lekéri a frissítést, és telepítse azt. Ezek a frissítések egyszerűek - csak egy-két sor a PowerShell. Az Azure DevOps esetében ezek közül néhány olyan buildfeladatokba van beágyazva, amelyek egy buildben összeláncolhatók. Ez [a cikk](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) további részleteket tartalmaz. Ez a [GitHub-tár-tárlaton](https://github.com/Microsoft/vso-agent-tasks) a rendelkezésre álló buildfeladatok részleteit.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Automation DSC áttekintése](automation-dsc-overview.md)
* [Bevezetési gépek az Azure Automation DSC általi kezeléshez](automation-dsc-onboarding.md)

## <a name="next-steps"></a>További lépések

- Áttekintést az [Azure Automation állapotkonfigurációja című témakörben talál.](automation-dsc-overview.md)
- Első lépések: [Az Azure Automation állapotkonfigurációjának első lépései.](automation-dsc-getting-started.md)
- A DSC-konfigurációk összeállításáról a célcsomópontokhoz való hozzárendelésről a [Konfigurációk összeállítása az Azure Automation állapotkonfigurációjában](automation-dsc-compile.md)témakörben olvashat.
- A PowerShell-parancsmag referencia, lásd: [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Díjszabási információkért lásd: [Azure Automation state configuration fortaring.](https://azure.microsoft.com/pricing/details/automation/)
- Az Azure Automation state configuration használatával kapcsolatos példa folyamatos üzembe helyezési folyamatban: [Folyamatos üzembe helyezés az Azure Automation állapotkonfiguráció és a Csokoládé használata című témakörben.](automation-dsc-cd-chocolatey.md)
