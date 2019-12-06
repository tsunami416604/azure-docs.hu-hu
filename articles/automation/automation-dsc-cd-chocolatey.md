---
title: Azure Automation állapot-konfiguráció folyamatos üzembe helyezése csokoládéval
description: DevOps a folyamatos üzembe helyezést a Azure Automation állapot-konfiguráció, a DSC és a chocolatey Package Manager használatával.  Példa teljes JSON Resource Manager-sablonnal és PowerShell-forrással.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ddbf652c35c4f1504e3253838a983fd0f6039401
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850363"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Használati példa: folyamatos üzembe helyezés a Virtual Machines Automation State Configuration és chocolatey használatával

A DevOps világában számos eszköz segíti a folyamatos integrációs folyamat különböző pontjainak támogatását. Azure Automation az állapot-konfiguráció a DevOps csapatok által alkalmazható lehetőségek örvendetes új kiegészítése. Ez a cikk a folyamatos üzembe helyezés (CD) beállítását mutatja be egy Windows rendszerű számítógépen. Egyszerűen kiterjesztheti a technikát úgy, hogy a szerepkör (például a webhely) számos Windows-számítógépét tartalmazza, és onnan további szerepköröket is tartalmazzon.

![IaaS virtuális gépek folyamatos üzembe helyezése](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Magas szinten

Van egy kicsit, de szerencsére két fő folyamatra bontható:

- Kód írása és tesztelése, majd telepítési csomagok létrehozása és közzététele a rendszer fő és másodlagos verzióihoz.
- Virtuális gépek létrehozása és kezelése, amelyek telepítik és végrehajtják a kódokat a csomagokban.  

Ha mindkét alapvető folyamat teljesült, ez egy rövid lépés, amellyel automatikusan frissítheti az adott virtuális gépen futó csomagot, mivel új verziók jönnek létre és telepíthetők.

## <a name="component-overview"></a>Az összetevők áttekintése

A Package managerek, például az [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) nagyon jól ismertek a Linux világában, de nem annyira a Windows világában.
A [chocolatey](https://chocolatey.org/) egy ilyen dolog, és Scott Jancsi [blogja](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) a témakörben egy nagyszerű bevezető. Dióhéjban a chocolatey lehetővé teszi csomagok központi tárházból történő telepítését a Windows rendszerbe a parancssor használatával. Saját tárházat hozhat létre és kezelhet, a chocolatey pedig tetszőleges számú tárházból telepíthet csomagokat.

A kívánt állapot-konfiguráció (DSC) ([Áttekintés](/powershell/scripting/dsc/overview/overview)) egy PowerShell-eszköz, amely lehetővé teszi, hogy deklarálja a gép kívánt konfigurációját. Például azt is megteheti, hogy "szeretnék telepíteni a csokis alkalmazást, szeretném telepíteni az IIS-t, a 80-es portot szeretném megnyitni, szeretném telepíteni a webhelyem 1.0.0 verzióját." A DSC helyi Configuration Manager (LCD ChipOnGlas) implementálja ezt a konfigurációt. A DSC lekérési kiszolgáló a gépek konfigurációinak tárházát tárolja. Az egyes gépeken lévő LCD ChipOnGlas rendszeresen ellenőrzi, hogy a konfigurációja megfelel-e a tárolt konfigurációnak. Vagy jelenthet állapotot, vagy megkísérli a gép visszahelyezését a tárolt konfigurációval való összehangolásra. A lekéréses kiszolgáló tárolt konfigurációjának szerkesztésével megadhatja, hogy a gép vagy a gépek készlete igazodjon a módosított konfigurációhoz.

A Azure Automation Microsoft Azure felügyelt szolgáltatása, amely lehetővé teszi a különböző feladatok automatizálását a runbookok, a csomópontok, a hitelesítő adatok, az erőforrások és az eszközök, például az ütemtervek és a globális változók használatával.
Azure Automation állapot-konfiguráció kiterjeszti ezt az automatizálási képességet, hogy tartalmazza a PowerShell DSC-eszközöket. Íme egy nagyszerű [Áttekintés](automation-dsc-overview.md).

A DSC-erőforrás olyan programkód modulja, amely adott képességekkel rendelkezik, például hálózatkezelés, Active Directory vagy SQL Server kezelésére. A chocolatey DSC-erőforrás tudja, hogyan férhet hozzá egy NuGet-kiszolgálóhoz (többek között), csomagokat tölthet le, csomagokat telepíthet stb. A [PowerShell-Galéria](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)számos más DSC-erőforrással rendelkezik.
Ezeket a modulokat a rendszer a Azure Automation állapot-konfiguráció lekérési kiszolgálójára telepíti (Ön is), így a konfigurációk használhatják őket.

A Resource Manager-sablonok deklaratív módszert biztosítanak az infrastruktúra (például hálózatok, alhálózatok, hálózati biztonság és útválasztás, terheléselosztó, hálózati adapterek, virtuális gépek stb.) létrehozásához. Íme egy [cikk](../azure-resource-manager/resource-manager-deployment-model.md) , amely összehasonlítja a Resource Manager-alapú üzemi modellt (deklaratív) az Azure Service Management (ASM vagy klasszikus) üzembe helyezési modellel, és ismerteti az alapvető erőforrás-szolgáltatókat, a számításokat, a tárolást és a hálózatot.

A Resource Manager-sablonok egyik kulcsfontosságú funkciója, hogy a virtuálisgép-bővítményt a virtuális gépre telepítse, mivel az üzembe van építve. A virtuálisgép-bővítmény olyan speciális képességekkel rendelkezik, mint például az egyéni parancsfájlok futtatása, a víruskereső szoftver telepítése vagy a DSC konfigurációs parancsfájl futtatása. Számos más típusú virtuálisgép-bővítmény van.

## <a name="quick-trip-around-the-diagram"></a>Gyors utazás a diagram körül

A lap elejétől kezdve megírhatja a kódot, kiépítheti és tesztelheti, majd létrehozhat egy telepítőcsomagot.
A chocolatey különféle típusú telepítési csomagokat képes kezelni, például MSI, MSU vagy ZIP. A PowerShell a tényleges telepítést is lehetővé teszi, ha a Chocolateys natív képességei nem egészen a leghatékonyabban. Helyezze a csomagot egy olyan helyre, amely elérhető – egy csomag tárháza. Ez a használati példa egy nyilvános mappát használ egy Azure Blob Storage-fiókban, de bárhol lehet. A chocolatey natív módon működik a NuGet-kiszolgálókkal, és néhány más, a csomagok metaadatainak kezeléséhez. [Ez a cikk](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) a beállításokat ismerteti. Ez a használati példa a NuGet-t használja. A Nuspec a csomagjaival kapcsolatos metaadatok. A Nuspec "lefordítva" a NuPkg, és egy NuGet-kiszolgálón tárolódnak. Ha a konfiguráció név alapján kéri a csomagokat, és egy NuGet-kiszolgálóra hivatkozik, a chocolatey DSC-erőforrás (most a virtuális gépen) megragadja a csomagot, és telepíti azt. A csomagok egy adott verzióját is kérheti.

A kép bal alsó részén van egy Azure Resource Manager sablon. Ebben a használati példában a virtuálisgép-bővítmény regisztrálja a virtuális gépet a Azure Automation állapot-konfiguráció lekérési kiszolgálóval (azaz egy lekérési kiszolgálóval) csomópontként. A konfigurációt a lekérési kiszolgáló tárolja.
Valójában a rendszer kétszer tárolja a következőt: egyszer egyszerű szövegként és egyszer lefordítva egy MOF-fájlként (ilyen dolgokkal kapcsolatban). A portálon a MOF egy "csomópont-konfiguráció" (a "konfiguráció" helyett). Ez a csomóponthoz társított összetevő, így a csomópont fogja tudni a konfigurációját. Az alábbi részletek azt mutatják be, hogyan rendelhető hozzá a csomópont-konfiguráció a csomóponthoz.

Valószínűleg már a legfelül, vagy a legtöbbet futtatja. A nuspec létrehozása, a NuGet-kiszolgálón való fordítása és tárolása kis dolog. A virtuális gépek már felügyelet alatt állnak. A folyamatos üzembe helyezés következő lépéseként be kell állítania a lekérési kiszolgálót (egyszer), regisztrálnia kell a csomópontjait (egyszer), majd létrehoznia és tárolnia kell a konfigurációt (kezdetben). Ezután a csomagok frissítése és üzembe helyezése a tárházban folyamatban van, frissítse a konfigurációt és a csomópont konfigurációját a lekérési kiszolgálón (szükség esetén ismételje meg a műveletet).

Ha nem a Resource Manager-sablonnal kezdődik, az is OK. A PowerShell-parancsmagok úgy vannak kialakítva, hogy segítsenek regisztrálni a virtuális gépeket a lekérési kiszolgálóval és az összes többivel. További részletekért tekintse meg ezt a cikket: [bevezetési gépek felügyelethez Azure Automation állapot-konfiguráció alapján](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>1\. lépés: a lekérési kiszolgáló és az Automation-fiók beállítása

Hitelesített (`Connect-AzureRmAccount`) PowerShell parancssorban: (eltarthat néhány percig, amíg a lekérési kiszolgáló be van állítva)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Az Automation-fiókját a következő régiókba helyezheti el (más néven hely): USA 2. keleti régiója, az USA déli középső régiója, US Gov Virginia, Nyugat-Európa, Délkelet-Ázsia, Kelet-Japán, Közép-India és Délkelet-Ausztrália, Közép-Kanada, Észak-Európa.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>2\. lépés: a virtuálisgép-bővítmény Tweaks a Resource Manager-sablonhoz

A virtuális gépek regisztrálásának részletei (a PowerShell DSC virtuálisgép-bővítmény használatával), amely ebben az [Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)-útmutatóban van megadva.
Ez a lépés regisztrálja az új virtuális gépet a lekérési kiszolgálóval az állapot konfigurációs csomópontjainak listájában. A regisztráció részeként meg kell adni a csomópontra alkalmazni kívánt csomópont-konfigurációt. Ennek a csomópont-konfigurációnak még nem kell létezni a lekérési kiszolgálón, ezért a 4. lépés az első alkalommal történik. A 2. lépésben azonban meg kell határoznia a csomópont nevét és a konfiguráció nevét. Ebben a használati példában a csomópont "isvbox", a konfiguráció pedig "ISVBoxConfig". Így a csomópont-konfiguráció neve (a DeploymentTemplate. JSON fájlban adható meg) a következő: "ISVBoxConfig. isvbox".

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>3\. lépés: a szükséges DSC-erőforrások hozzáadása a lekérési kiszolgálóhoz

A PowerShell-galéria a DSC-erőforrások Azure Automation-fiókba való telepítéséhez lett kialakítva.
Navigáljon a kívánt erőforráshoz, és kattintson a "központi telepítés Azure Automation" gombra.

![PowerShell-galéria példa](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Az Azure Portalra nemrég hozzáadott másik módszer lehetővé teszi új modulok lekérését vagy meglévő modulok frissítését. Kattintson az Automation-fiók erőforrására, az eszközök csempére, végül pedig a modulok csempére. A tallózási katalógus ikon segítségével megtekintheti a katalógusban található modulok listáját, részletezheti a részleteket, és végül importálhatja az Automation-fiókjába. Ez nagyszerű módja annak, hogy a modulok naprakészek legyenek időről időre. Az importálási funkció pedig más modulokkal is ellenőrzi a függőségeket, így biztosítva, hogy semmi sincs szinkronban.

Vagy van a manuális megközelítése. A Windows rendszerű számítógépek PowerShell-integrációs moduljának mappastruktúrát kissé eltér a Azure Automation által várt mappastruktúrát.
Ehhez kis csípésre van szükség az Ön részéről. Ez azonban nem nehéz, és az erőforrás csak egyszer van végrehajtva (kivéve, ha később szeretné frissíteni.) A PowerShell-integrációs modulok létrehozásával kapcsolatos további információkért tekintse meg a következő cikket: [integrációs modulok készítése a Azure Automationhoz](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- Telepítse a szükséges modult a munkaállomáson, a következőképpen:
  - [Windows Management Framework, V5](https://aka.ms/wmf5latest) telepítése (Windows 10 rendszerhez nem szükséges)
  - `Install-Module –Name MODULE-NAME` < – a modult a PowerShell-galéria
- A modul mappájának másolása `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME`ból egy ideiglenes mappába
- Minták és dokumentáció törlése a fő mappából
- Zip a fő mappa, a ZIP-fájl elnevezése pontosan ugyanaz, mint a mappa 
- Helyezze a ZIP-fájlt egy elérhető HTTP-helyre, például egy Azure Storage-fiók blob Storage-fiókjába.
- Futtassa ezt a PowerShellt:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

A példában szereplő példa a cChoco és a xNetworking vonatkozó lépéseket hajtja végre. Tekintse meg a cChoco speciális kezelésével kapcsolatos [megjegyzéseket](#notes) .

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>4\. lépés: a csomópont-konfiguráció hozzáadása a lekérési kiszolgálóhoz

A konfigurációnak a lekéréses kiszolgálóra való első importálásakor és a fordításakor semmi nem különleges. Ugyanaz a konfiguráció minden további importálási/fordítási beállítása pontosan ugyanaz. Minden alkalommal, amikor frissíti a csomagot, és le kell küldenie az éles környezetbe, ezt a lépést a konfigurációs fájl helyességének biztosítása után hajtja végre – beleértve a csomag új verzióját is. A konfigurációs fájl és a PowerShell:

ISVBoxConfig. ps1:

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

New-ConfigurationScript. ps1:

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

Ezek a lépések egy új, "ISVBoxConfig. isvbox" nevű csomópont-konfigurációt eredményeznek, amely a lekérési kiszolgálón van elhelyezve. A csomópont-konfiguráció neve "configurationName. csomópontnév" néven van felépítve.

## <a name="step-5-creating-and-maintaining-package-metadata"></a>5\. lépés: a csomag metaadatainak létrehozása és fenntartása

A Package repositoryba helyezett minden csomaghoz szüksége van egy nuspec, amely leírja azt.
Ezt a nuspec a NuGet-kiszolgálón kell összeállítani és tárolni. Ez a folyamat [itt](https://docs.nuget.org/create/creating-and-publishing-a-package)olvasható. A MyGet.org NuGet-kiszolgálóként is használható. Eladják ezt a szolgáltatást, de ingyenes, alapszintű SKU-val rendelkeznek. A NuGet.org-on a saját NuGet-kiszolgáló telepítésére vonatkozó utasításokat talál a privát csomagokhoz.

## <a name="step-6-tying-it-all-together"></a>6\. lépés: az együttes összekapcsolása

Minden alkalommal, amikor egy verzió megfelel a QA-nek, és jóváhagyja az üzembe helyezést, a rendszer létrehozza, nuspec és nupkg frissíti és telepíti a NuGet-kiszolgálóra. Emellett a konfigurációt (4. lépés) frissíteni kell, hogy egyezzen az új verziószámmal. Ezt el kell juttatni a lekérési kiszolgálóhoz, és le kell fordítani.
Ettől kezdve a konfigurációtól függ, hogy a virtuális gépek a frissítés lekéréséhez és telepítéséhez szükségesek-e. Ezek a frissítések egyszerűek – csak egy sor vagy két PowerShell. Az Azure-DevOps esetében ezek némelyike olyan felépítési feladatokbe van ágyazva, amelyek összekapcsolhatók egy összeállításban. Ez a [cikk](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) további részleteket tartalmaz. Ez a [GitHub](https://github.com/Microsoft/vso-agent-tasks) -tárház a különböző elérhető Build-feladatokat részletezi.

## <a name="notes"></a>Megjegyzések

Ez a használati példa egy általános Windows Server 2012 R2 rendszerképpel rendelkező virtuális géppel kezdődik az Azure-katalógusból. Bármely tárolt rendszerképből elindíthatja, majd a DSC-konfigurációval is megkezdheti a csípését.
Azonban a rendszerképbe besütött konfiguráció módosítása sokkal nehezebb, mint a DSC-t használó konfiguráció dinamikus frissítése.

A virtuális gépekhez nem szükséges Resource Manager-sablont és a virtuálisgép-bővítményt használni. A virtuális gépeknek nem kell az Azure-ban lenniük a CD-felügyelet alatt. Mindez szükséges ahhoz, hogy a chocolatey telepítve legyen, és a virtuális merevlemezen legyen konfigurálva, hogy a lekéréses kiszolgáló hol található.

Természetesen, ha a csomagot egy éles virtuális gépen frissíti, akkor a frissítés telepítése közben el kell végeznie a virtuális gép elforgatását. Ez a művelet igen változó. Ha például egy virtuális gép egy Azure Load Balancer mögött található, egyéni mintavételt is hozzáadhat. A virtuális gép frissítésekor a mintavételi végpont 400-as értéket ad vissza. A módosítást okozó csípés lehet a konfiguráción belül, ahogy a csípés visszaválthat a 200-re, ha a frissítés befejeződött.

A használati példa teljes forrása ebben a [Visual Studio-projektben](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) a githubon.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Azure Automation DSC – áttekintés](automation-dsc-overview.md)
* [Azure Automation DSC-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Bevezetési gépek a Azure Automation DSC általi felügyelethez](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Következő lépések

- Az áttekintést lásd: [Azure Automation állapot konfigurálása](automation-dsc-overview.md)
- Első lépésként tekintse meg [az Azure Automation állapot konfigurációjának megismerése](automation-dsc-getting-started.md) című témakört.
- A DSC-konfigurációk fordításának megismeréséhez, hogy hozzá lehessen rendelni azokat a célcsoportokhoz, tekintse meg a [konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md) című témakört.
- A PowerShell-parancsmagok ismertetése: [Azure Automation állapot-konfigurációs parancsmagok](/powershell/module/azurerm.automation/#automation)
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/)
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse meg a [folyamatos üzembe helyezést a Azure Automation állapot-konfiguráció és a csokoládés használatával](automation-dsc-cd-chocolatey.md) .
