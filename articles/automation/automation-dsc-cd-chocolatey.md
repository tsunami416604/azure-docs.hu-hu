---
title: Azure Automation állapot konfiguráció folyamatos üzembe helyezés a Chocolatey
description: Fejlesztési és üzemeltetési folyamatos üzembe helyezés konfiguráló Azure Automation, DSC és a chocolatey-t a package manager használatával.  Példa a teljes JSON Resource Manager-sablon és a PowerShell-forrás.
services: automation
ms.service: automation
ms.component: dsc
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dc0e49f2857468338f6e1f16462ed814893ea6e7
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42055294"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Példa: Való Automation konfigurációs és a chocolatey-t használó virtuális gépek folyamatos üzembe helyezés

A DevOps-világ vannak, amelyek segítik a különféle pontokat, hogy a folyamatos integrációs folyamat számos eszközt. Azure Automation konfigurációs egy üdvözlő újdonága a beállításokat, amelyeket fejlesztési és üzemeltetési csapatok azonban. Ez a cikk azt ismerteti, hogy egy Windows-számítógép be a folyamatos üzembe helyezés (CD) beállítása. Könnyedén kiterjesztheti a technika tetszőleges számú Windows-számítógépek szükség szerint tartalmazza a szerepkör (egy webhely, a példában), és a további szerepkörök is innen.

![Folyamatos üzembe helyezés IaaS virtuális gépekhez](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Magas szinten

Még egy kis itt történik, de Szerencsére azt is lehet osztani két fő folyamat:

- Kód írása és tesztelésére is, majd létrehozása és közzététele telepítőcsomagok fő- és alverzió verziója, a rendszer.
- Létrehozása és kezelése a virtuális gépeket, amelyek telepíti, és futtassa a kódot a csomagok.  

Miután mindkét alapvető eljárás érvényesítve van, egy rövid lépés automatikus frissítése az új verziók létrehozott és telepített bármely adott virtuális gépen futó csomag.

## <a name="component-overview"></a>Összetevő áttekintése

Például a kezelők csomag [apt-get paranccsal](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) Linux világszerte, de nem nagy részét a Windows világ viszonylag jól ismert.
[Chocolatey](https://chocolatey.org/) ilyen egy dolog, és házigazdája, Scott Hanselman [blog](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) témakörről van egy nagyszerű bemutatása. Legnépszerűbb helyezés a Chocolatey lehetővé teszi a csomagok egy központi tárházban a csomagok telepítése a parancssor használata Windows rendszerben. Hozhat létre és kezelheti a saját tárház, és a chocolatey-t, Ön által kijelölt tárházak tetszőleges számú csomagok lehet telepíteni.

Desired State Configuration (DSC) ([áttekintése](/powershell/dsc/overview)) egy PowerShell-eszköz, amely lehetővé teszi, hogy a konfigurációt, amelyet egy gép deklarálható. Ha például mondhatjuk, "a chocolatey-t telepíteni kívánt, telepített IIS-t szeretnék, szeretném a 80-as port megnyitása, szeretném 1.0.0-s verziójának webhelyem, telepítve van." A DSC helyi Configuration Manager (LCM) valósítja meg, hogy ez a konfiguráció. A DSC lekéréses kiszolgálón tárolja a gépek konfigurációi gyűjteményéből. Az LCM Konfigurálása az összes olyan számítógépen ellenőrzi rendszeres időközönként, ha annak konfigurációja megegyezik-e a tárolt konfiguráció. Állapotát, vagy próbálja meg a gép vissza beemelése a tárolt konfigurációs ciklustól. A lekérési kiszolgálón egy gép vagy gépek lépnek a módosított konfigurációs ciklustól okozhat a tárolt konfiguráció szerkesztheti.

Az Azure Automation egy olyan felügyelt szolgáltatás, amely lehetővé teszi a runbookok, csomópontok, hitelesítő adatok, erőforrások és -adategységeket, mint az ütemezés és a globális változók különféle feladatainak automatizálása a Microsoft Azure-ban.
Azure Automation állapot konfiguráció kibővíti a PowerShell DSC eszközök automation funkció. Itt van egy nagyszerű [áttekintése](automation-dsc-overview.md).

A DSC-erőforrás egy modul mutatnak, amelyek az adott képesség, például a hálózat, az Active Directory vagy az SQL Server kezelésére. A Chocolatey DSC-erőforrás képes egy (többek között) NuGet-kiszolgáló eléréséhez, -csomagok letöltése, csomagok telepítéséhez és így tovább. Sok más DSC-erőforrás a a [PowerShell-galériából](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).
Ezeket a modulokat az Azure Automation állapota konfigurációs lekéréses kiszolgálón telepíteni (Ön által), így azok a konfigurációk által használható.

Resource Manager-sablonok, az infrastruktúra - többek között a hálózatok, alhálózatok, hálózati biztonság létrehozása deklaratív módszert biztosítanak, és betölteni útválasztás, a terheléselosztók, hálózati adapterek, virtuális gépek és így tovább. Íme egy [cikk](../azure-resource-manager/resource-manager-deployment-model.md) , amely összehasonlítja a Resource Manager üzemi modell (deklaratív) az Azure szolgáltatásfelügyelet (ASM vagy klasszikus) üzemi modellel (imperatív), és ismerteti az alapvető erőforrás-szolgáltatók, a számítási, tárolási és a hálózat.

Resource Manager-sablon egyik fontos szolgáltatása a lehetőség ki van építve a virtuális Gépet, telepíti a VM-bővítmény. VM-bővítmény egy egyéni szkript futtatása, víruskereső szoftver telepítése vagy DSC-konfigurációs parancsprogram futtatása például adott képességekkel rendelkezik. Nincsenek sok más típusú Virtuálisgép-bővítmények.

## <a name="quick-trip-around-the-diagram"></a>A diagram körül gyors utazás

Indítása az oldal tetején, a kód írása hozhat létre és tesztelheti, majd telepítőcsomag létrehozása.
Chocolatey-t a különböző típusú telepítőcsomagok, például az MSI-vel az MSU, ZIP képes kezelni. És a tényleges telepítési teendő Chocolateys natív funkciók nem teljesen, akár a teljes power PowerShell rendelkezik. A csomag üzembe valahol érhető el – a csomagtárház. A használati példában egy nyilvános mappában egy Azure blob storage-fiókot, de bárhol lehet. Felügyeleti csomag metaadatok natív módon a NuGet-kiszolgálók és néhány másik chocolatey működik. [Ez a cikk](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) lehetőségeit ismerteti. A használati példa NuGet. Egy Nuspec metaadatait, a csomagok. A Nuspec "lefordítva" NuPkg a, és a egy NuGet-kiszolgálón tárolt. Ha a konfigurációs csomagot kérelmek név szerint, és a egy NuGet-kiszolgálóra hivatkozik, a Chocolatey DSC-erőforrás (mostantól megtalálható a virtuális gép) begyűjti a csomagot, és telepíti azt. A csomag egy adott verzióját is kérheti.

A bal alsó részén található a kép van egy Azure Resource Manager-sablon. Használat ebben a példában a Virtuálisgép-bővítmény regisztrálja a Azure Automation állapota konfigurációs lekérési kiszolgálóval (azaz egy lekéréses kiszolgálót) a virtuális gép egy csomópontnak tekinti. A konfiguráció a lekéréses kiszolgálón tárolja.
Valójában, tárolva kétszer: egyszer pedig egyszerű szövegként, és miután összeállított, MOF-fájlt (azok számára, többek között ismert.) A portálon az MOF "csomópont-konfiguráció" (nem egyszerűen csak "beállítások"). A lehívandó összetevő, amely egy csomópont van társítva, így a csomópont tudni fogja a konfigurációs. Az alábbi részletek bemutatják, hogyan a csomópont-konfiguráció hozzárendelése a csomópontra.

Elvileg már végzett a bit tetején, vagy a legtöbb kategóriát. A nuspec létrehozása, a kódja lefordításának és tárolása a NuGet-kiszolgáló egy kis dolog. És már kezelése virtuális gépek. (Ha) a lekérési kiszolgálójának beállítása, a regisztráló a csomópontok (egyszer), és a létrehozása és a (kezdeti) tárolja a konfigurációs van véve a következő lépés a folyamatos üzembe helyezés van szükség. Csomagok vannak frissítve, és telepítve van a tárházhoz, majd frissítse a konfigurációs és csomópont-konfiguráció a lekéréses kiszolgálón (ez szükség szerint ismételje meg).

Ha Ön még nem indul el egy Resource Manager-sablonnal, ez is OK. Számos PowerShell-parancsmagok, amelyek segítségével regisztrálja a virtuális gépek és az összes többi a lekéréses kiszolgálón. További részletekért lásd: Ez a cikk: [gépek előkészítése kezelésre által az Azure Automation Állapotkonfiguráció](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>1. lépés: A lekéréses kiszolgálón és az automation-fiók beállítása

Jelenleg egy hitelesített (`Connect-AzureRmAccount`) PowerShell-parancssorból: (is igénybe vehet néhány percet, amíg a lekéréses kiszolgálón be van állítva)

    New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT

Az automation-fiók elérhetősége (más néven helye) valamelyikébe helyezheti: USA keleti RÉGIÓJA 2, USA déli középső Régiója, USA beli államigazgatás – Virginia, Nyugat-Európa, Délkelet-Ázsia, kelet-japán, közép-India és Délkelet-Ausztrália, közép-Kanada, Észak-Európa.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>2. lépés: Virtuális gép bővítmény honlap a Resource Manager-sablon

Ebben a virtuális gép regisztrációhoz (PowerShell DSC bővítmény használatával) részletei [Azure gyorsindítási sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Ebben a lépésben regisztrálja az új virtuális gép állapota konfigurációs csomópontok listájában a lekéréses kiszolgálón. Ez a regisztráció részeként határozza meg a csomópont-konfigurációt a csomópont a alkalmazni. Ez a csomópont-konfiguráció nem kell a lekéréses kiszolgálón, hogy legyen, amely 4. lépés, ebben az esetben először OK még létezik. De itt a 2. lépésben kell a csomópont nevét, és a konfiguráció neve mellett döntött. Használat ebben a példában a csomópont "isvbox" és a konfigurációs "ISVBoxConfig". Így (a DeploymentTemplate.json adható meg), a csomópont-konfiguráció neve "ISVBoxConfig.isvbox".

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>3. lépés: A lekéréses kiszolgálón szükséges DSC-erőforrások hozzáadása

A PowerShell-galériából, az Azure Automation-fiók DSC-erőforrások telepítéséhez van kialakítva.
Keresse meg az erőforrást, kattintson a "Üzembe helyezése az Azure Automation" gombra.

![Példa a PowerShell-galéria](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Az Azure Portal nemrégiben hozzáadott egy másik módszer lehetővé teszi, hogy kérje le az új modulokat, vagy meglévő-modulok frissítése. Kattintson az Automation-fiók erőforrás, az eszközök csempét, és végül a modulok csempét. A Tallózás a katalógusban ikon lehetővé teszi a katalógusban található modulok listájának megtekintéséhez, a részletek feltárásához és a végső soron az Automation-fiókba importálásához. Ez remek módja a modulok naprakészen tartásához időről időre. És az importálás funkció más modulok számára, győződjön meg arról, semmi nem szinkronizált lekérdezi a függőségek ellenőrzi.

Másik lehetőségként a manuális módszer van. A mappastruktúra a számítógép Windows PowerShell integrációs modulok kissé eltér a mappastruktúra az Azure Automation által várt.
Ehhez egy kis finomhangolása az Ön részéről. De már nem rögzített, és csak azért alkalmazzuk csak egyszer erőforrásonként (hacsak nem szeretné frissíteni, a jövőben.) A PowerShell-integrációs modulok szerzői további információkért lásd: Ez a cikk: [integrációs modulok készítése az Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- Telepítse a modul, amely a következőképpen kell a munkaállomáson:
  - Telepítés [Windows Management Framework, 5-ös verziójának](http://aka.ms/wmf5latest) (Windows 10-es nem szükséges)
  - `Install-Module –Name MODULE-NAME`    < – begyűjti a modul a PowerShell-galériából
- Másolja a modul mappájában található `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` egy ideiglenes mappába
- Minták és a dokumentáció a fő mappa törlése
- Zip-mappáját, pontosan ugyanaz, mint a mappa a ZIP-fájl elnevezése 
- A ZIP-fájlban helyezzen egy érhető el a HTTP-helyen, például blob storage-ban egy Azure Storage-fiókot.
- Futtassa a Powershellt:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

A csomagban foglalt példa cChoco és xNetworking hajtja végre ezeket a lépéseket. Tekintse meg a [megjegyzések](#notes) a különleges kezelést cChoco számára.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>4. lépés: A csomópont-konfiguráció hozzáadása a lekérési kiszolgálón

Nincs mit speciális Ön így importálja a konfigurációt a pull-kiszolgáló és a fordítási először kapcsolatban. Az összes későbbi importálás/fordítható ugyanazt a konfigurációt, pontosan ugyanúgy néznek. Minden alkalommal, amikor a csomag frissítése és a kell leküldenie az éles környezetben teheti meg ezt a lépést – a csomag új verziójának a maradéktalanul a konfigurációs fájl helyességéről. Itt van a konfigurációs fájlt, és a PowerShell:

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
            DisplayGroup = 'IIS Incoming Traffic'
            State        = 'Enabled'
            Access       = 'Allow'
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

New-ConfigurationScript.ps1:

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

Ezen lépések eredménye az új csomópont-konfiguráció "ISVBoxConfig.isvbox" a lekérési kiszolgálón elhelyezni kívánt nevet. A csomópont-konfiguráció neve szerint "configurationName.nodeName" épül.

## <a name="step-5-creating-and-maintaining-package-metadata"></a>5. lépés: Létrehozása és karbantartása csomagok metaadatai

Minden csomag mindössze a csomagtárház szüksége lesz egy nuspec, amely azt ismerteti.
Adott nuspec kell összeállítani, és a NuGet-kiszolgálón tárolt. Ennek menetét [Itt](http://docs.nuget.org/create/creating-and-publishing-a-package). NuGet-kiszolgálóként MyGet.org is használhatja. Értékesíteni ezt a szolgáltatást, de rendelkezik alapszintű Termékváltozatát, amely ingyenes. NuGet.org címen találja a saját NuGet-kiszolgáló a saját csomagok telepítésével kapcsolatban.

## <a name="step-6-tying-it-all-together"></a>6. lépés: Minden egy helyen összeköthető

Minden alkalommal, amikor egy verziót adja át a QA és jóvá van hagyva központi telepítéshez, a csomag létrejött, nuspec és nupkg frissítve, és a NuGet-kiszolgálóra telepített. Emellett a konfigurációs (4. lépés a fenti) frissíteni kell, hogy fogadja el az új verziószámmal. Fel kell a pull-kiszolgálónak küldött, és fordítva.
Ezt követően a szolgáltatás a virtuális gépekhez, kérje le a frissítést, és telepítse az adott konfigurációtól függenek. Ezek a frissítések mindegyike egyszerű – mindössze egy vonal- vagy két PowerShell. A Visual Studio Team Services esetében némelyike vannak beágyazva, amely a build összefűzhetők úlohy buildu. Ez [cikk](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) további részleteket. Ez [GitHub-adattárat](https://github.com/Microsoft/vso-agent-tasks) a különböző elérhető összeállítási feladatok részleteit.

## <a name="notes"></a>Megjegyzések

A példa egy virtuális Gépet egy általános Windows Server 2012 R2 rendszerképet az Azure-katalógusból származó kezdődik. Indítsa el a tárolt képet, és majd végezze el a DSC-konfiguráció onnan.
Azonban módosítja a konfigurációt, amely a beépített rendszerképben nem sokkal nehezebb, mint a dinamikus frissítése a konfigurációját DSC segítségével.

Nem kell ezt a technikát használni a virtuális gépek a Resource Manager-sablon és a Virtuálisgép-bővítmény használatával. És a virtuális gépek nem kell az Azure-ra CD felügyelete alatt kell lenniük. Minden szükséges, hogy telepítve legyen-e a chocolatey-t és az LCM beállítva a virtuális gépen, így az tudni fogja a lekéréses kiszolgálót.

Természetesen Ha frissít egy csomagot a virtuális gép, éles környezetben, kell a virtuális gép rotációból igénybe vehet, amíg a frissítés telepítve van. Hogyan teheti ezt érték nagy eltéréseket mutat. Például egy Azure-terheléselosztó mögötti virtuális gépen, hozzáadhat egy egyéni mintavétel. A virtuális gép frissítése során rendelkezik az állapotminta-végpontot egy 400-as adja vissza. Szükségesek, hogy ez a módosítás hatására értékelésére lehet belül a konfigurációját, térjen vissza 200-as, a frissítés befejeződése után váltson a értékelésére is.

Teljes használat ebben a példában forrása a [a Visual Studio-projekt](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) a Githubon.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Automation DSC – áttekintés](automation-dsc-overview.md)
* [Azure Automation DSC-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Gépek előkészítése kezelésre, amelyet az Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [Azure Automation Állapotkonfiguráció](automation-dsc-overview.md)
- Első lépésként lásd [Ismerkedés az Azure Automation Állapotkonfiguráció](automation-dsc-getting-started.md)
- DSC-konfigurációk fordítása, így hozzárendelheti azokat a célcsomópontokat kapcsolatos további információkért lásd: [összeállítása az Azure Automation Állapotkonfiguráció konfigurációk](automation-dsc-compile.md)
- PowerShell-parancsmagok leírása, lásd: [konfiguráló Azure Automation-parancsmagok](/powershell/module/azurerm.automation/#automation)
- Díjszabási információkért tekintse meg a [Azure Automation State Configuration díjszabása](https://azure.microsoft.com/pricing/details/automation/)
- Folyamatos üzembe helyezés folyamatban lévő Azure Automation Állapotkonfiguráció használatának példájáért lásd: [folyamatos üzembe helyezés használatával az Azure Automation Állapotkonfiguráció és a chocolatey-t](automation-dsc-cd-chocolatey.md)