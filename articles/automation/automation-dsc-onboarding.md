---
title: Bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez
description: Gépek beállítása felügyeletre Azure Automation állapot-konfigurációval
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: cf95a66cf68cf0b33444a17cf762bae79db4b50c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243434"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Miért érdemes a gépeket Azure Automation állapot-konfigurációval felügyelni?

Azure Automation állapot-konfiguráció a DSC-csomópontok bármely Felhőbeli vagy helyszíni adatközpontban való konfigurálása.
Lehetővé teszi, hogy gyorsan és könnyen, egy központi, biztonságos helyen lehessen méretezni a több ezer gépen.
Könnyedén beépítheti a gépeket, hozzárendelheti a deklaratív konfigurációkat, és megtekintheti azokat a jelentéseket, amelyek az egyes gépek megfelelőségét a megadott kívánt állapotnak megfelelően mutatják.
A Azure Automation állapot-konfigurációs szolgáltatás azt a DSC-t adja meg, amit Azure Automation runbookok a PowerShell-parancsfájlok futtatásához.
Más szóval ugyanúgy, ahogy a Azure Automation segíti a PowerShell-parancsfájlok kezelését, a DSC-konfigurációk kezelését is segíti.
Ha többet szeretne megtudni a Azure Automation állapot konfigurációjának előnyeiről, tekintse meg [Azure Automation állapot-konfiguráció áttekintése](automation-dsc-overview.md)című témakört.

Azure Automation állapot-konfiguráció számos gép felügyeletére használható:

- Azure-alapú virtuális gépek
- Azure-beli virtuális gépek (klasszikus)
- Amazon Web Services (AWS) EC2-példányok
- Fizikai/virtuális Windowsos gépek a helyszínen, vagy az Azure-tól/AWS-től eltérő felhőben
- Fizikai/virtuális Linux rendszerű számítógépek a helyszínen, az Azure-ban vagy az Azure-tól eltérő felhőben

Emellett, ha nem áll készen a számítógép-konfiguráció felhőből való kezelésére, Azure Automation az állapot-konfiguráció csak jelentési végpontként is használható.
Ezzel a beállítással beállíthatja (leküldéses) konfigurációkat DSC-n keresztül, és megtekintheti a jelentéskészítés részleteit Azure Automationban.

> [!NOTE]
> Az Azure-beli virtuális gépek állapot-konfigurációval való kezelése külön díj nélkül, ha a telepített virtuálisgép-bővítmény nagyobb, mint 2,70. További részletekért tekintse meg az [**Automation díjszabását ismertető oldalt**](https://azure.microsoft.com/pricing/details/automation/) .

A következő részekben felvázoljuk, hogyan végezheti el az egyes típusú gépek bevezetését Azure Automation állapot-konfigurációra.

## <a name="azure-virtual-machines"></a>Azure-alapú virtuális gépek

Azure Automation állapot-konfiguráció lehetővé teszi az Azure-beli virtuális gépek üzembe helyezését a konfiguráció kezeléséhez a Azure Portal, a Azure Resource Manager sablonok vagy a PowerShell használatával. A motorháztető alatt, és anélkül, hogy rendszergazda kelljen a virtuális géphez csatlakoznia, az Azure-beli virtuális gép kívánt állapota konfigurációs bővítmény regisztrálja Azure Automation állapot konfigurációjában a virtuális gépet.
Mivel az Azure-beli virtuális gép kívánt állapotának konfigurációs bővítménye aszinkron módon fut, a folyamat előrehaladásának nyomon követéséhez és a hibaelhárításhoz szükséges lépések az [**Azure Virtual Machine**](#troubleshooting-azure-virtual-machine-onboarding) bevezetési szakaszának alábbi hibaelhárítási szakaszában találhatók.

### <a name="azure-portal"></a>Azure Portal

A [Azure Portal](https://portal.azure.com/)Navigáljon arra a Azure Automation-fiókra, amelyben a virtuális gépeket szeretné bevezetni. Az állapot konfigurációja lapon és a **csomópontok** lapon kattintson a **+ Hozzáadás**gombra.

Válasszon ki egy Azure-beli virtuális gépet a bevezetéshez.

Ha a gépen nincs telepítve a PowerShell desired State bővítmény, és a tápellátási állapot fut, kattintson a **kapcsolat**gombra.

A **regisztráció**területen adja meg a használni kívánt [PowerShell DSC helyi Configuration Manager értékeket](/powershell/scripting/dsc/managing-nodes/metaConfig) , és opcionálisan a virtuális géphez hozzárendelni kívánt csomópont-konfigurációt.

![bevezetési](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Az Azure-beli virtuális gépek üzembe helyezhetők és behelyezhetők a Azure Automation állapot konfigurációjában Azure Resource Manager sablonok használatával. Tekintse meg a [kiszolgáló által felügyelt, a kívánt állapot-konfigurációs szolgáltatást](https://azure.microsoft.com/resources/templates/101-automation-configuration/) egy olyan sablonhoz, amely egy meglévő virtuális gépet Azure Automation állapot konfigurációjának bevezetéséhez.
Ha virtuálisgép-méretezési készletet kezel, tekintse meg a [Azure Automation által felügyelt sablon virtuálisgép-méretezési csoportjának konfigurációját](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

A [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) parancsmag segítségével virtuális gépeket helyezhet üzembe az Azure-ban a PowerShell használatával.
Ez azonban jelenleg csak Windows rendszerű gépeken implementálható (a parancsmag csak a Windows-bővítményt indítja el).

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Virtuális gépek regisztrálása Azure-előfizetések között

A virtuális gépek más Azure-előfizetésből való regisztrálásának legjobb módja a DSC-bővítmény használata egy Azure Resource Manager telepítési sablonban.
Példákat a [Azure Resource Manager sablonokkal rendelkező, a kívánt állapotú konfigurációs bővítményben](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)talál.
A sablonban paraméterként használandó regisztrációs kulcs és regisztrációs URL-cím megkereséséhez tekintse meg a következő [**biztonságos regisztráció**](#secure-registration) szakaszt.

## <a name="amazon-web-services-aws-virtual-machines"></a>Amazon Web Services (AWS) virtuális gépek

Az AWS DSC Toolkit használatával egyszerűen elkészítheti Amazon Web Services virtuális gépek konfigurálását Azure Automation állapot-konfigurációval. Az eszközkészletről [itt](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/)olvashat bővebben.

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Fizikai/virtuális Windowsos gépek a helyszínen, vagy az Azure-tól/AWS-től eltérő felhőben

A helyszíni vagy más Felhőbeli környezetekben futó Windows-kiszolgálók is bekészíthetők Azure Automation állapot-konfigurációba, feltéve, hogy az [Azure-ban kimenő hozzáférésük](automation-dsc-overview.md#network-planning)van:

1. Győződjön meg arról, hogy a [WMF 5](https://aka.ms/wmf5latest) legújabb verziója van telepítve a Azure Automation állapot-konfigurációba beépíteni kívánt gépekre.
1. Kövesse az alábbi, DSC- [**Metaconfigurations létrehozó**](#generating-dsc-metaconfigurations) szakasz utasításait a szükséges DSC-metaconfigurations tartalmazó mappa létrehozásához.
1. Távolról alkalmazza a PowerShell DSC-metaconfiguration a bevezetéshez használni kívánt gépekre. **A parancs futtatásához szükséges számítógépnek a [WMF 5](https://aka.ms/wmf5latest) legújabb verzióját kell telepítenie**:

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Ha nem tudja távolról alkalmazni a PowerShell DSC-metaconfigurations, másolja a 2. lépésben szereplő metaconfigurations mappát az egyes gépekre. Ezután a **set-DscLocalConfigurationManager** helyileg hívja meg az egyes gépeken a bevezetést.
1. A Azure Portal vagy a parancsmagok használatával győződjön meg arról, hogy a bevezetésre kerülő számítógépek megjelennek a Azure Automation-fiókban regisztrált állapot-konfigurációs csomópontként.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Fizikai/virtuális Linux rendszerű számítógépek a helyszínen, vagy az Azure-tól eltérő felhőben

A helyszíni vagy más felhőalapú környezetekben üzemelő Linux-kiszolgálók is bekészíthetők Azure Automation állapot-konfigurációba, feltéve, hogy az [Azure-ban kimenő hozzáférésük](automation-dsc-overview.md#network-planning)van:

1. Győződjön meg arról, hogy a Linux rendszerhez készült [PowerShell kívánt állapot-konfiguráció](https://github.com/Microsoft/PowerShell-DSC-for-Linux) legújabb verziója telepítve van azokon a gépeken, amelyeken be szeretné állítani Azure Automation állapot konfigurációját.
1. Ha a [POWERSHELL DSC helyi Configuration Manager alapértelmezett](/powershell/scripting/dsc/managing-nodes/metaConfig4) beállításai megfelelnek a használati esetnek, és olyan gépeket kíván **bevezetni** , amelyeknek a lekérése és jelentése a Azure Automation állapot konfigurációját jelenti:

   - Minden Linux rendszerű gépen Azure Automation állapot konfigurációjának bevezetéséhez használja a `Register.py` értéket a PowerShell DSC helyi Configuration Manager alapértelmezett értékeinek használatával:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Az Automation-fiók regisztrációs kulcsának és regisztrációs URL-címének megkereséséhez tekintse meg a következő [**biztonságos regisztráció**](#secure-registration) szakaszt.

     Ha a PowerShell DSC helyi Configuration Manager alapértelmezett értéke **nem** felel meg a használati esetnek, vagy ha olyan gépeket kíván bevezetni, amelyeket csak az állapot-konfiguráció Azure Automation jelentenek, kövesse az 3-6. lépést. Ellenkező esetben folytassa közvetlenül a 6. lépéssel.

1. A szükséges DSC-metaconfigurations tartalmazó mappa létrehozásához kövesse a [**DSC metaconfigurations következő generálása**](#generating-dsc-metaconfigurations) című szakasz utasításait.
1. Távolról alkalmazza a PowerShell DSC-metaconfiguration a bevezetésre használni kívánt gépekre:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

A parancs futtatásához szükséges számítógépnek a [WMF 5](https://aka.ms/wmf5latest) legújabb verzióját kell telepítenie.

1. Ha nem tudja távolról alkalmazni a PowerShell DSC-metaconfigurations, másolja a számítógépnek megfelelő metaconfiguration az 5. lépésben a Linux rendszerű számítógép mappájába. Ezután hívja meg `SetDscLocalConfigurationManager.py`-at helyileg minden olyan linuxos gépen, amelyet be szeretne állítani Azure Automation állapot-konfigurációba:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. A Azure Portal vagy a parancsmagok használatával győződjön meg arról, hogy a bevezetésre kerülő gépek most a Azure Automation-fiókban regisztrált DSC-csomópontként jelennek meg.

## <a name="generating-dsc-metaconfigurations"></a>DSC-metaconfigurations generálása

Ahhoz, hogy Azure Automation állapot konfigurációjában a gépeket általános bevezetéssel lehessen bevezetni, egy [DSC-metaconfiguration](/powershell/scripting/dsc/managing-nodes/metaConfig) hozható létre, amely közli, hogy a DSC-ügynök lekéri és/vagy jelentést készít az állapot-konfiguráció Azure Automationáról. A Azure Automation állapot-konfiguráció DSC-metaconfigurations a PowerShell DSC-konfiguráció vagy a Azure Automation PowerShell-parancsmagok használatával hozhatók létre.

> [!NOTE]
> A DSC-metaconfigurations azokat a titkokat tartalmazzák, amelyek szükségesek ahhoz, hogy a gép egy Automation-fiókba kerüljön a felügyelethez. Ügyeljen arra, hogy megfelelően megvédje a létrehozott DSC-metaconfigurations, vagy törölje azokat a használat után.

### <a name="using-a-dsc-configuration"></a>DSC-konfiguráció használata

1. Nyissa meg a VSCode (vagy a kedvenc szerkesztőjét) rendszergazdaként a helyi környezetben lévő számítógépeken. A gépnek telepítve kell lennie a [WMF 5](https://aka.ms/wmf5latest) legújabb verziójával.
1. Másolja a következő parancsfájlt helyileg. Ez a szkript egy PowerShell DSC-konfigurációt tartalmaz a metaconfigurations létrehozásához, valamint egy olyan parancsot, amely kirúgja a metaconfiguration létrehozását.

> [!NOTE]
> Az állapot-konfigurációs csomópontok konfigurációs nevei megkülönböztetik a kis-és nagybetűket a portálon. Ha az eset nem egyezik, a csomópont nem jelenik meg a **csomópontok** lapon.

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Adja meg az Automation-fiókjához tartozó regisztrációs kulcsot és URL-címet, valamint a bevezetéshez használt gépek nevét. Az összes többi paraméter megadása nem kötelező. Az Automation-fiók regisztrációs kulcsának és regisztrációs URL-címének megkereséséhez tekintse meg a következő [**biztonságos regisztráció**](#secure-registration) szakaszt.
1. Ha azt szeretné, hogy a gépek jelentést készítsenek a DSC-állapotadatok Azure Automation állapot konfigurációjában, de ne legyenek lekéréses konfiguráció vagy PowerShell-modulok, állítsa igaz értékre a **ReportOnly** paramétert.
1. Futtassa a szkriptet. Most már rendelkeznie kell egy **DscMetaConfigs** nevű mappával a munkakönyvtárában, amely a PowerShell DSC-metaconfigurations tartalmazza a gépekhez a bevezetéshez (rendszergazdaként):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Az Azure Automation-parancsmagok használata

Ha a PowerShell DSC helyi Configuration Manager alapértelmezett beállításai megfelelnek a használati esetnek, és olyan gépeket kíván bevezetni, amelyeknek a lekérése és jelentése is Azure Automation az állapot-konfigurációra, a Azure Automation-parancsmagok egyszerűsített módszert biztosítanak a létrehozáshoz a DSC-metaconfigurations szükséges:

1. Nyissa meg a PowerShell-konzolt vagy a VSCode rendszergazdaként a helyi környezetben lévő számítógépeken.
2. Kapcsolódás Azure Resource Manager `Connect-AzAccount` használatával
3. Töltse le a PowerShell DSC-metaconfigurations azon gépekhez, amelyeket az Automation-fiókból kíván bevezetni, amelybe be szeretné állítani a csomópontokat:

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Most már rendelkeznie kell egy ***DscMetaConfigs***nevű mappával, amely a PowerShell DSC-metaconfigurations tartalmazza a gépekhez a bevezetéshez (rendszergazdaként):

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Biztonságos regisztráció

A gépek a WMF 5 DSC regisztrációs protokollon keresztül biztonságosan bejelentkezhetnek egy Azure Automation fiókba, amely lehetővé teszi, hogy a DSC-csomópont hitelesíthető legyen egy PowerShell DSC lekéréses vagy jelentéskészítő kiszolgálón (beleértve Azure Automation állapot konfigurációját is). A csomópont regisztrálja a kiszolgálót egy **regisztrációs URL**-címen, és egy **regisztrációs kulccsal**hitelesíti. A regisztráció során a DSC-csomópont és a DSC-lekérési/jelentéskészítő kiszolgáló egyeztet egy egyedi tanúsítvánnyal ahhoz, hogy a csomópont hitelesítve legyen a regisztráció után. Ez a folyamat megakadályozza, hogy a beépített csomópontok megszemélyesítsék egymást, például ha egy csomópontot feltörtek és rosszindulatúan viselkednek. A regisztrációt követően a regisztrációs kulcsot nem használja újra a hitelesítéshez, és a rendszer törli a csomópontról.

Az állapot-konfigurációs regisztrációs protokollhoz szükséges adatokat a Azure Portalban található **Fiókbeállítások** **területen érheti el.** Nyissa meg ezt a panelt az Automation-fiók **Essentials** paneljének kulcs ikonjára kattintva.

![Azure Automation-kulcsok és URL-cím](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- A regisztrációs URL-cím a kulcsok kezelése panel URL-mezője.
- A regisztrációs kulcs az elsődleges hozzáférési kulcs vagy másodlagos elérési kulcs a kulcsok kezelése panelen. Bármelyik kulcsot használhatja.

A további biztonság érdekében az Automation-fiókok elsődleges és másodlagos hozzáférési kulcsai bármikor újra létrehozhatók (a **kulcsok kezelése** lapon), így megelőzhető, hogy a későbbi csomópont-regisztrációk a korábbi kulcsok használatával is elérhetők legyenek.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Azure-beli virtuális gépek előkészítésének hibaelhárítása

Azure Automation állapot-konfiguráció lehetővé teszi az Azure Windows rendszerű virtuális gépek egyszerű üzembe helyezését a konfiguráció felügyeletéhez. A motorháztető alatt az Azure VM desired State Configuration bővítmény a virtuális gép Azure Automation állapot-konfigurációval való regisztrálására szolgál. Mivel az Azure-beli virtuális gép kívánt állapotához tartozó konfigurációs bővítmény aszinkron módon fut, fontos lehet az előrehaladás nyomon követése és a végrehajtásuk hibaelhárítása.

> [!NOTE]
> Az Azure-beli Windows rendszerű virtuális gépek az Azure-beli virtuális gép kívánt állapot-konfigurációját használó Azure Automation állapot-konfigurációba való bevezetésének bármely módszere akár egy órát is igénybe vehet, hogy a csomópont a Azure Automationban regisztráltként jelenjen meg. Ennek az az oka, hogy a Windows Management Framework 5,0-es verziójának telepítése a virtuális gépen az Azure VM DSC bővítménnyel történik, amely a virtuális gép bevezetéséhez szükséges ahhoz, hogy Azure Automation állapot konfigurációját.

Az Azure-beli virtuális gép kívánt állapotához tartozó konfigurációs bővítmény állapotának hibakereséséhez vagy megtekintéséhez a Azure Portal navigáljon a bevezetést végző virtuális géphez, majd kattintson a **Beállítások**alatt található **bővítmények** elemre. Ezután kattintson a **DSC** vagy a **DSCForLinux** elemre az operációs rendszertől függően. További részletekért kattintson a **részletes állapot megtekintése**lehetőségre.

## <a name="certificate-expiration-and-reregistration"></a>Tanúsítvány lejárata és újraregisztrálása

Miután Azure Automation állapot konfigurációjában egy gépet DSC-csomópontként regisztrált, számos oka lehet annak, hogy újra regisztrálnia kell a csomópontot a jövőben:

- A Windows Server 2019 előtti verziói esetében minden egyes csomópont automatikusan egyeztet egy egyedi tanúsítványt a hitelesítéshez, amely egy év után lejár. Jelenleg a PowerShell DSC regisztrációs protokollja nem tudja automatikusan megújítani a tanúsítványokat, ha közelednek a lejárathoz, ezért a csomópontokat egy év múlva újra regisztrálni kell. Az újbóli regisztrálás előtt győződjön meg arról, hogy minden csomóponton fut a Windows Management Framework 5,0 RTM. Ha egy csomópont hitelesítési tanúsítványa lejár, és a csomópontot nem regisztrálja újra, a csomópont nem tud kommunikálni Azure Automation és a "nem válaszol" jelöléssel van megjelölve. Az Újraregisztrálás a tanúsítvány lejárati idejétől számítva 90 vagy kevesebb napig, vagy a tanúsítvány lejárati idejének lejárta után bármikor új tanúsítványt fog generálni és használni.  A probléma megoldása a Windows Server 2019-es és újabb verzióiban is megtalálható.
- Ha módosítani szeretné a [POWERSHELL DSC helyi Configuration Manager értékeit](/powershell/scripting/dsc/managing-nodes/metaConfig4) , amelyek a csomópont kezdeti regisztrálása során lettek beállítva, például ConfigurationMode. Jelenleg ezek a DSC-ügynök értékei csak az Újraregisztrálás útján módosíthatók. Az egyetlen kivétel a csomóponthoz rendelt csomópont-konfiguráció – ez a Azure Automation DSC-ben közvetlenül is módosítható.

Az Újraregisztrálás ugyanúgy végezhető el, ahogy először regisztrálta a csomópontot, a jelen dokumentumban ismertetett bevezetési módszerek bármelyikének használatával. Az Újraregisztrálás előtt nem kell megszüntetnie a csomópont regisztrációját Azure Automation állapot-konfigurációból.

## <a name="next-steps"></a>Következő lépések

- Első lépésként tekintse meg [az Azure Automation állapot konfigurációjának megismerése](automation-dsc-getting-started.md) című témakört.
- A DSC-konfigurációk fordításának megismeréséhez, hogy hozzá lehessen rendelni azokat a célcsoportokhoz, tekintse meg a [konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md) című témakört.
- A PowerShell-parancsmagok ismertetése: [Azure Automation állapot-konfigurációs parancsmagok](/powershell/module/az.automation#automation)
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/)
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse meg a [folyamatos üzembe helyezést a Azure Automation állapot-konfiguráció és a csokoládés használatával](automation-dsc-cd-chocolatey.md) .
