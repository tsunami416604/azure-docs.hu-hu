---
title: Azure Automation DSC Chocolatey a folyamatos üzembe helyezés
description: DevOps folyamatos üzembe helyezés Azure Automation DSC és Chocolatey Csomagkezelő használatával.  Példa a teljes JSON ARM-sablon és a PowerShell-forrás.
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f695eaadc0aa2d01473262c478a3b184d89d882c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-dsc-and-chocolatey"></a>Példa: A virtuális gépek Automation DSC és Chocolatey folyamatos üzembe helyezés
DevOps világában sok eszközöket különböző pontok folyamatos integrációs folyamatban van.  Azure Automation kívánt állapot konfigurációs szolgáltatása (DSC) egy üdvözlő új hozzáadása arra a DevOps csoportokat is alkalmaz.  Ez a cikk bemutatja a beállítás mentése folyamatos Deployment (CD) a Windows-számítógépen.  Könnyen kiterjesztheti a technika (egy webhely, például) szerepkör, és onnan, valamint a további szerepkörök közé tartoznak a tetszőleges számú, Windows rendszerű számítógépeken szükség szerint.

![Infrastruktúra-szolgáltatási virtuális gépek folyamatos üzembe helyezés](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Magas szinten
Igen, felhasználó ide látogatott bit, de Szerencsére az is lehet osztani két fő folyamatok: 

* Kód írása tesztelés, létrehozása és közzététele telepítési csomagokat a rendszer fő- és alverzió verzióit. 
* Létrehozása, és a virtuális gépek, amelyek telepítése és a kód végrehajtása a-csomagok kezelése.  

Ha mindkét alapvető eljárás helyen, akkor automatikusan frissíti a csomagot, bármely adott virtuális gép futó, új verziók létrehozott és telepített egy rövid lépés.

## <a name="component-overview"></a>Az összetevő áttekintése
Például a csomag kezelők [apt get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) a Linux világ, de nem nagy részét a Windows világ közérthető jól ismert.  [Chocolatey](https://chocolatey.org/) egy ilyen dolog, és Scott Hanselman [blog](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) a következő témakörben található nagy bevezetés.  A ez már a vég Chocolatey lehetővé teszi egy központi tárházban csomagok csomagok telepítése a parancssor használatával Windows rendszerben.  Hozhat létre és kezelheti a saját tárház, és Chocolatey csomagok kijelölt adattárak tetszőleges számú telepíthetnek.

Szükségeskonfiguráció-konfiguráló (DSC) ([áttekintése](https://technet.microsoft.com/library/dn249912.aspx)), amely lehetővé teszi a konfigurációt, amelyet a gép deklarálható PowerShell eszköz.  Feltételezzük például, hogy tudja, "Chocolatey telepíteni kívánt, kívánt telepített IIS-t, szeretném a 80-as port nyitva, kívánt 1.0.0 a webhely telepített verzió."  A DSC helyi Configuration Manager (LCM) valósítja meg, hogy a konfigurálás. A DSC lekérni a kiszolgáló rendelkezik egy konfigurációinak a gépek-tárházat. Minden számítógépen LCM ellenőrzi rendszeres időközönként konfigurációja megegyezik-e a tárolt konfiguráció. Az állapotjelentés, vagy annak érdekében, a gép ismét igazodik a tárolt konfiguráció kísérlet. Egy számítógép vagy a módosított konfigurációjával igazítása során gépek csoportja tartalmazza a lekérési kiszolgálón tárolt konfigurációs szerkesztheti.

Azure Automation szolgáltatásbeli egy olyan felügyelt szolgáltatás, amely lehetővé teszi a runbookok, csomópontok, hitelesítő adatokat, erőforrások és ütemezési és globális változókat eszközök használatával különböző feladatok automatizálására Microsoft Azure-ban. Az Azure Automation DSC kibővíti az automatizálási funkció PowerShell DSC eszközöket is tartalmazza.  Ez egy nagy [áttekintése](automation-dsc-overview.md).

A DSC-erőforrás egy modul kód, amely az adott funkciót, például a hálózat, az Active Directory és az SQL Servert kezelésére.  A DSC Chocolatey erőforrás tudja, hol (többek között) a NuGet-kiszolgáló elérhető, csomagokat letölteni, csomagok és így tovább.  Sok más DSC-erőforrás a a [PowerShell-galériában](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).  Ezek a modulok telepíti azokat az Azure Automation DSC-lekérni a kiszolgáló (,), ezek a konfigurációk által használható.

Resource Manager-sablonok deklaratív teszik lehetővé a létrehozást, az infrastruktúra - többek között a hálózatok, az alhálózatok, a hálózati biztonsági és útválasztási, betölteni, terheléselosztók, hálózati adapterek, virtuális gépek és így tovább.  Íme egy [cikk](../azure-resource-manager/resource-manager-deployment-model.md) , amely összehasonlítja a Resource Manager üzembe helyezési modellben (deklaratív) az Azure szolgáltatásfelügyelet (ASM vagy klasszikus) a központi telepítési modell (imperatív), és ismerteti az alapvető erőforrás-szolgáltató, számítási, tárolási és hálózati.

Egy kulcs egy Resource Manager sablon szolgáltatása képesek-e ki van építve, telepítse a virtuális Gépet egy Virtuálisgép-bővítmény.  A Virtuálisgép-bővítmény például egy egyéni parancsfájl futtatása, a víruskereső szoftver telepítése vagy a DSC-konfigurációs parancsprogram futtatása adott képességekkel rendelkezik.  Nincsenek számos más típusú Virtuálisgép-bővítmények.

## <a name="quick-trip-around-the-diagram"></a>A diagram körül gyors út
Indítása az oldal tetején, a kódot ír összeállítása és tesztelése, majd hozzon létre egy csomag.  Chocolatey kezelni tud a különféle telepítési csomagokat, például az MSI-fájl, MSU, ZIP.  És a teendő a tényleges telepítési Chocolatey tartozó natív képességek nem elég legfeljebb PowerShell teljes hatványa rendelkezik.  A csomag üzembe valahol érhető el – a csomag tárházba.  A használati példában az egyik nyilvános mappájába egy Azure blob storage-fiók, de bárhol lehet.  Felügyeleti csomag metaadatok natív módon a NuGet-kiszolgálók és néhány más chocolatey működik.  [Ez a cikk](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) azokat a lehetőségeket ismerteti.  A használati példa NuGet.  Egy Nuspec a csomagok metaadatait.  A Nuspec "lefordítva" NuPkg meg, és a NuGet-kiszolgáló tárolja.  Amikor a konfigurációs csomag kér név szerint, és hivatkozik a NuGet-kiszolgáló, a Chocolatey DSC-erőforrás (most a virtuális Gépre) a csomag grabs, és telepíti azt.  A csomag egy adott verziójához is kérheti.

A bal alsó részén a kép az Azure Resource Managerrel (ARM) sablon esetében.  A használati példában a Virtuálisgép-bővítmény regisztrálja a virtuális Gépet az Azure Automation DSC lekérési kiszolgálójával (Ez azt jelenti, hogy a lekérési kiszolgálójával) rendelkező csomópont.  A konfiguráció a lekérési kiszolgálón tárolja.  Ténylegesen, tárolt kétszer: egyszer egyszerű szövegként, és miután összeállítani MOF-fájlként (az alábbiakhoz ilyen dolgot tudnia.)  A portálon a MOF egy "csomópont-konfiguráció" (figyelésekor egyszerűen "konfiguráció").  A csomópont van társítva, ezért a csomópont tudni fogják konfigurációjában hitelesítendő entitások azonosítása.  Az alábbi részletek megjelenítése a csomópont-konfiguráció hozzárendelése a csomópont.

Valószínűleg már végzett tetején, vagy azt a legtöbb bit.  A nuspec létrehozása, fordítása, és tárolja őket a NuGet-kiszolgáló egy kis is.  És már Ön által felügyelt virtuális gépek.  A lekérési kiszolgálójával (egyszer) beállítása, a regisztráló a csomópontok (egyszer), és a létrehozása és a (kezdetben) tárolja a konfigurációs hiba véve a következő lépés a folyamatos üzembe helyezés igényel.  Mind a csomópont konfigurációs csomagok frissítése és központi telepítése a tárházban, majd frissítse a lekérési kiszolgálón (ismétlődő igény szerint).

Ha még nem kezdve egy ARM-sablon, ez is OK gombra.  PowerShell-parancsmagok célja, hogy a virtuális gépek regisztrálása a lekérési kiszolgálójával, és az összes többi van. További részletekért lásd: Ez a cikk: [bevezetési gépeket Azure Automation DSC általi kezelésre](automation-dsc-onboarding.md)

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>1. lépés: A lekérési kiszolgálón és az automatizálás fiók beállítása
Egy hitelesített (Csatlakozás-AzureRmAccount) PowerShell parancssorból: (percet is igénybe vehet néhány közben a lekérési kiszolgálón be van állítva)

    New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT 

Az automation-fiók (más néven helye) következő régiókban minden helyezhet el: USA keleti régiója 2, déli középső Régiójában, Velünk – (kormányzati) Virginia, Nyugat-Európában, Délkelet-Ázsiában, kelet-japán, közép-Indiában és Ausztrália délkeleti, Kanada központi Észak-Európa.

## <a name="step-2-vm-extension-tweaks-to-the-arm-template"></a>2. lépés: Virtuális gép bővítmény beállításokból állnak az ARM-sablon
VM-regisztrációk (a PowerShell DSC Virtuálisgép-bővítmény) részletei jelen [Azure gyors üzembe helyezés sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).  Ez a lépés az új virtuális gép regisztrálja a DSC-csomópontok közül a lekérési kiszolgálójával.  Ez a regisztráció része van a csomópont-konfigurációnak a csomópont alkalmazandó megadása.  Ez a csomópont-konfiguráció nem kell a lekérési kiszolgálón, ezért ezt a OK, amely a 4. lépés, ahol ehhez először még létezik.  De itt 2. lépésben kell a csomópont neve és a konfiguráció neve mellett döntött.  A használati példában a csomópont "isvbox" pedig a konfiguráció "ISVBoxConfig".  Így a csomópont-konfiguráció neve (meg kell határozni a DeploymentTemplate.json) "ISVBoxConfig.isvbox".  

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>3. lépés: A lekérési kiszolgálójával DSC szükséges erőforrások hozzáadása
A PowerShell-galériában van tagolva DSC erőforrások telepítse az Azure Automation-fiók.  Nyissa meg az erőforrás, kattintson a "Központi telepítése az Azure Automation" gombra.

![PowerShell-galériában – példa](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Nemrég adta hozzá az Azure-portálra egy másik módszer lehetővé teszi, hogy új modulok bekérésére vagy frissítse a meglévő modulok. Kattintson az Automation-fiók erőforrás, az eszközök csempe, és végül a modulok csempén.  A tallózási gyűjteményelem ikon lehetővé teszi a katalógusban szereplő modulok listáját, részletekbe menően tárhatják fel részleteit, és végül importálja az Automation-fiók. Ez remek módja a modulok naprakészen tartásához időről időre. És az importálás funkció ellenőrzi annak érdekében, semmi sem lekérdezi szinkronban más modulok függőségek.

Vagy nincs az manuális módszerrel.  A PowerShell integrációs modul a Windows-számítógép mappaszerkezetét kissé eltér a mappastruktúra az Azure Automation által várt.  Ehhez egy kis tökéletesítse beavatkozást.  Azonban ez nem rögzített, és elkészült csak egyszer erőforrásonként (hacsak nem szeretné frissíteni a jövőben.)  A PowerShell integrációs modulok készítése további információkért lásd: Ez a cikk: [integrációs modulok készítése az Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

* A modul telepítése, amelyekre szüksége van a munkaállomáson, az alábbiak szerint:
  * Telepítés [Windows Management Framework v5](http://aka.ms/wmf5latest) (a Windows 10-hez nem szükséges)
  * `Install-Module –Name MODULE-NAME`    < – a PowerShell-galériából modul grabs 
* A modul mappát másolja `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` egy ideiglenes mappába 
* A fő mappa minták és dokumentáció törlése 
* A ZIP-mappáját, pontosan ugyanaz, mint a mappa a ZIP-fájl elnevezése 
* A ZIP-fájl helyezze egy elérhető-e HTTP-helyre, például az Azure-Tárfiók a blob-tároló.
* Futtassa ezt a PowerShell:
  
      New-AzureRmAutomationModule `
          -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
          -Name MODULE-NAME –ContentLink "https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip"

A befoglalt példa cChoco és xNetworking hajtja végre ezeket a lépéseket. Tekintse meg a [megjegyzések](#notes) a cChoco a különleges kezelést.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>4. lépés: A csomópont-konfiguráció hozzáadása a lekérési kiszolgálón
Nincs szükség különleges, a konfiguráció importál a lekérési kiszolgálójával, és a fordítási először kapcsolatos.  Az összes későbbi importálási/lefordítható ugyanazt a konfigurációt a pontosan is ugyanúgy néznek ki.  Minden alkalommal, amikor frissíti a csomagot, és a leküldése éles kell meg ehhez a lépéshez győződjön meg arról, a konfigurációs fájl nem megfelelő – beleértve a csomag új verziója.  Ez a konfigurációs fájl és a PowerShell használatával:

ISVBoxConfig.ps1:

    Configuration ISVBoxConfig 
    { 
        Import-DscResource -ModuleName cChoco 
        Import-DscResource -ModuleName xNetworking

        Node "isvbox" {   

            cChocoInstaller installChoco 
            { 
                InstallDir = "C:\choco" 
            }

            WindowsFeature installIIS 
            { 
                Ensure="Present" 
                Name="Web-Server" 
            }

            xFirewall WebFirewallRule 
            { 
                Direction = "Inbound" 
                Name = "Web-Server-TCP-In" 
                DisplayName = "Web Server (TCP-In)" 
                Description = "IIS allow incoming web site traffic." 
                DisplayGroup = "IIS Incoming Traffic" 
                State = "Enabled" 
                Access = "Allow" 
                Protocol = "TCP" 
                LocalPort = "80" 
                Ensure = "Present" 
            }

            cChocoPackageInstaller trivialWeb 
            {            
                Name = "trivialweb" 
                Version = "1.0.0" 
                Source = “MY-NUGET-V2-SERVER-ADDRESS” 
                DependsOn = "[cChocoInstaller]installChoco", 
                "[WindowsFeature]installIIS" 
            } 
        }    
    }

New-ConfigurationScript.ps1:

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

Ezek új csomópont-konfiguráció lépéseket eredményez a lekérési kiszolgálón helyezés "ISVBoxConfig.isvbox" nevű.  A csomópont-konfiguráció neve "configurationName.nodeName" mint épül.

## <a name="step-5-creating-and-maintaining-package-metadata"></a>5. lépés: Létrehozása és karbantartása csomag metaadatai
Minden csomagot, hogy vannak-e a csomag tárházba van szüksége, amely leírja, hogy egy nuspec.  Adott nuspec kell összeállítani, és a NuGet-kiszolgáló által tárolt. A műveletet [Itt](http://docs.nuget.org/create/creating-and-publishing-a-package).  MyGet.org NuGet-kiszolgálóként is használhatja.  Ez a szolgáltatás el, hanem alapszintű Termékváltozat, amely nem rendelkezik.  A NuGet.org saját NuGet-kiszolgáló a saját csomagok telepítésével kapcsolatos találhat.

## <a name="step-6-tying-it-all-together"></a>6. lépés: Az összes együtt Ön
Minden alkalommal, amikor egy verzió QA továbbítja, jóvá van hagyva központi telepítéséhez a csomagot a létrehozása nuspec és frissítése, és a NuGet-kiszolgálóra telepített nupkg.  Ezenkívül a konfigurációs (4. lépés a fenti) frissíteni kell, hogy elfogadja az új verziószámmal.  Kell küldeni a lekérési kiszolgálójával, és fordítva.  Ettől kezdve akkor a virtuális gépek, amelyek függenek, hogy a frissítés lekéréses és telepítéséhez.  E frissítések egyszerű - csak egy vonal, vagy két PowerShell.  Visual Studio Team Services, esetén némelyikük vannak ágyazva elérése érdekében együtt a build összeállítási feladatokat.  Ez [cikk](https://www.visualstudio.com/en-us/docs/alm-devops-feature-index#continuous-delivery) további részleteket tartalmaz.  Ez [GitHub-tárház](https://github.com/Microsoft/vso-agent-tasks) adatokat a különböző elérhető összeállítási feladatokat.

## <a name="notes"></a>Megjegyzések
Ez a példa egy virtuális Gépet az Azure katalógusában az általános Windows Server 2012 R2 lemezképéről kezdődik.  Indítsa el a tárolt képet, és majd végeznünk a DSC-konfiguráció onnan.  Sütik-konfiguráció módosítása lemezképekbe azonban sokkal nehezebb mint dinamikusan a a DSC használata konfigurációjának frissítése.

Ez a módszer használata a virtuális gépek egy ARM-sablon és a Virtuálisgép-bővítmény használatára nincs.  És a virtuális gépek nem kell Azure CD felügyelet alatt kell lennie.  Minden szükséges Chocolatey telepített, és a LCM konfigurálva a virtuális Gépen, így az tudni fogja, hogy a lekéréses kiszolgálót.  

Természetesen Amikor frissít egy csomagot a virtuális gép, amely a termelésben, kell Elforgatás kívüli virtuális igénybe vehet, amíg a frissítés telepítve van.  Ennek módja széles körben változik.  Például egy virtuális Gépet egy Azure terheléselosztó mögött, hozzáadhat egy egyéni mintavételi.  A virtuális gép frissítésekor a mintavételi végpont, térjen vissza a 400 rendelkezik.  A végeznünk szükségesek, hogy ez a módosítás következtében lehet belül a konfigurációs végeznünk való visszatéréshez azt vissza a 200-as, a frissítés befejezése után is.

Ez a példa a teljes forrás van [a Visual Studio-projekt](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) a Githubon.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Azure Automation DSC – áttekintés](automation-dsc-overview.md)
* [Azure Automation DSC-parancsmagokkal](https://msdn.microsoft.com/library/mt244122.aspx)
* [Azure Automation DSC általi kezelésre bevezetési gépek](automation-dsc-onboarding.md)

