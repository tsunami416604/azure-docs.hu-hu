---
title: Beépített gépek az Azure Automation állapotkonfigurációja általi kezeléshez
description: Ez a cikk ismerteti, hogyan állíthat be gépeket az Azure Automation állapotkonfigurációval a felügyelethez.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 04/15/2020
manager: carmonm
ms.openlocfilehash: 4fc9f701f4f832deb1fed34d7ac9f888521d3830
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406202"
---
# <a name="onboard-machines-for-management-by-azure-automation-state-configuration"></a>Beépített gépek az Azure Automation állapotkonfigurációja általi kezeléshez

Az Azure Automation State Configuration egy konfigurációkezelő szolgáltatás a kívánt állapotkonfigurációs (DSC) csomópontokhoz a felhőben vagy a helyszíni adatközpontokban. A szolgáltatás az Azure Portalon az **Állapotkonfiguráció (DSC)** **konfigurációs csoportban**kiválasztásával érhető el. 

Az Azure Automation állapotkonfigurációjával gyorsan és egyszerűen érheti el a gépek ezreiközötti méretezhetőséget egy központi, biztonságos helyről. Könnyedén bedeszkázhatja a gépeket, deklaratív konfigurációkat rendelhet hozzájuk, és megtekintheti azokat a jelentéseket, amelyek azt mutatják, hogy az egyes gépek megfelelnek a megadott állapotnak.

Az Azure Automation állapotkonfigurációs szolgáltatás a DSC, amit az Azure Automation runbookok a PowerShell-parancsfájlok. Más szóval, ugyanúgy, ahogy az Azure Automation segít a PowerShell-parancsfájlok kezelésében, a DSC-konfigurációk kezelését is segíti. Ha többet szeretne megtudni az Azure Automation állapotkonfiguráció használatának előnyeiről, olvassa el az [Azure Automation állapotkonfiguráció – áttekintés című témakört.](automation-dsc-overview.md)

Az Azure Automation State Configuration használatával kezelheti a különböző gépek:

- Azure-beli virtuális gépek (VM-ek)
- Azure virtuális gépek (klasszikus)
- Fizikai/virtuális Windows-gépek a helyszínen vagy az Azure-tól eltérő felhőben (beleértve az Amazon Web Services [AWS] Elastic Compute Cloud [EC2] példányait)
- Fizikai/virtuális Linux-gépek a helyszínen, az Azure-ban vagy az Azure-tól eltérő felhőben

Ha nem áll készen a gépkonfiguráció kontrájának kezelésére a felhőből, használhatja az Azure Automation State Configuration csak jelentésvégpontként. Ez a funkció lehetővé teszi a konfigurációk beállítását vagy leküldéses, DSC-n keresztül, és az Azure Automation jelentéskészítési részleteinek megtekintéséhez.

> [!NOTE]
> Az Azure-beli virtuális gépek kezelése az Azure Automation állapotkonfigurációval díjmentesen biztosított, ha a telepített Azure VM kívánt állapotkonfigurációs bővítmény e-két verzió 2.70-es vagy újabb verzió. További információ: [Automation pricing](https://azure.microsoft.com/pricing/details/automation/).

A cikk következő szakaszai ismertetik, hogyan lehet a korábban felsorolt gépeket az Azure Automation-állapot konfigurációba beszállni.

## <a name="onboard-azure-vms"></a>Beépített Azure-beli virtuális gépek

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)című témakörben. Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure Automation-modulfrissítési folyamattal.](automation-update-azure-modules.md)

Mivel az Azure-bővítmény aszinkron módon fut, a jelen cikk Azure [virtuálisgép-bevezetési hibaelhárítási](#troubleshoot-azure-virtual-machine-onboarding) szakaszában lépéseket biztosítunk a folyamat nyomon követéséhez vagy hibaelhárításához.

> [!NOTE]
> A DSC linuxos csomópontra történő telepítése a */tmp* mappát használja. A modulokat, például `nxautomation` a rendszer telepítésük előtt ideiglenesen letölti őket ellenőrzésre. A modulok megfelelő telepítésének biztosításához a Linuxhoz készült Log Analytics-ügynöknek olvasási/írási engedélyeket kell kérnie a */tmp* mappához.<br><br>
> A Log Analytics ügynök Linux fut az *omsagent* felhasználóként. Ha írási engedélyt szeretne adni az *omsagent felhasználónak, futtassa* a parancsot. `setfacl -m u:omsagent:rwx /tmp`

### <a name="onboard-vms-by-using-the-azure-portal"></a>Beépített virtuális gépek az Azure Portal használatával

Az Azure-beli virtuális gépek az Azure Automation-állapotkonfigurációhoz az [Azure Portalon](https://portal.azure.com/)keresztül:

1. Nyissa meg azt az Azure Automation-fiókot, amelyben a virtuális gépeket szeretné bevonni. 

1. Az **Állapotkonfiguráció** lapon válassza a **Csomópontok** lapot, majd a **Hozzáadás gombot.**

1. Válasszon egy virtuális gép a fedélzeten.

1. Ha a számítógépen nincs telepítve a Kívánt PowerShell-állapot bővítmény, és az energiaállapot fut, válassza a **Csatlakozás lehetőséget.**

1. A **Regisztráció csoportban**adja meg a [PowerShell DSC helyi konfigurációkezelő (LCM) használati](/powershell/scripting/dsc/managing-nodes/metaConfig) esethez szükséges értékeit. Szükség esetén megadhat egy csomópont-konfigurációt a virtuális géphez rendelve.

![A virtuális gép regisztrációs ablaktáblája](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-vms-by-using-azure-resource-manager-templates"></a>Beépített virtuális gépek az Azure Resource Manager-sablonok használatával

Üzembe helyezhet és üzembe helyezhet egy virtuális gép az Azure Automation-állapot konfigurációaz Azure Resource Manager-sablonok használatával. Egy példasablon, amely egy meglévő virtuális gép ről az Azure Automation-állapot konfigurációjára kerül, olvassa el a [Kívánt állapotkonfiguráció szolgáltatás által felügyelt kiszolgáló című témakört.](https://azure.microsoft.com/resources/templates/101-automation-configuration/) Ha egy virtuálisgép-méretezési készletet kezel, tekintse meg a példasablont az [Azure Automation által felügyelt virtuálisgép-méretezési csoport konfigurációjában.](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)

### <a name="onboard-vms-by-using-powershell"></a>Beépített virtuális gépek a PowerShell használatával

Használhatja a [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) parancsmag a PowerShell-ben az Azure Automation állapotkonfigurációja alaplapi virtuális gépek. 

> [!NOTE]
>A `Register-AzAutomationDscNode` parancsmag jelenleg csak a Windows rendszert futtató gépeken van megvalósítva, mivel a parancsmag csak a Windows-bővítményt váltja ki.

### <a name="register-vms-across-azure-subscriptions"></a>Virtuális gépek regisztrálása az Azure-előfizetések között

A más Azure-előfizetésekből származó virtuális gépek regisztrálásának legjobb módja a DSC-bővítmény használata egy Azure Resource Manager központi telepítési sablonban. Példák találhatók [a Kívánt állapot konfiguráció bővítmény az Azure Resource Manager sablonokat.](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)

A sablonban paraméterekként használandó regisztrációs kulcs és regisztrációs URL megkereséséhez tekintse meg [a beépített rögzítési](#onboard-securely-by-using-registration) szakaszt a cikk regisztrációs szakaszának használatával.

## <a name="onboard-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Beépített fizikai/virtuális Windows-gépek a helyszínen vagy az Azure-tól eltérő felhőben

A helyszíni vagy más felhőalapú környezetekben , beleértve az AWS EC2-példányokat futtató Windows-kiszolgálókat az Azure Automation állapotkonfigurációja rendszerbe is belehet- és bedeszkázhatja. A kiszolgálóknak kimenő hozzáféréssel kell rendelkezniük [az Azure-hoz.](automation-dsc-overview.md#network-planning)

1. Győződjön meg arról, hogy a [Windows Management Framework 5](https://aka.ms/wmf5latest) legújabb verziója telepítve van az Azure Automation állapotkonfigurációba beépítendő gépekre. Ezenkívül a Windows Management Framework 5 rendszert telepíteni kell a bevezetési művelethez használt számítógépre.
1. A szükséges DSC-metakonfigurációkat tartalmazó mappa létrehozásához kövesse a [DSC metakonfigurációk generálása szakasz utasításait.](#generate-dsc-metaconfigurations) 
1. A PowerShell DSC metakonfigurációinak távoli alkalmazásához használja a következő parancsmast a következő gépekre: 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Ha nem tudja távolról alkalmazni a PowerShell DSC metakonfigurációit, másolja a **metakonfigurációk** mappát a bevezetésre kerülő gépekre. Ezután adja hozzá a [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) helyi hívásához szükséges kódot a gépeken.

1. Az Azure Portalon vagy parancsmagok használatával ellenőrizze, hogy a beépített gépek az Azure Automation-fiókban regisztrált állapotkonfigurációs csomópontokként jelennek-e meg.

## <a name="onboard-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Beépített fizikai/virtuális Linux-gépek a helyszínen, vagy az Azure-tól eltérő felhőben

A helyszíni vagy más felhőalapú környezetekben futó Linux-kiszolgálókat az Azure Automation state configuration szolgáltatásba is belehet, hogy beszálljon. A kiszolgálóknak kimenő hozzáféréssel kell rendelkezniük [az Azure-hoz.](automation-dsc-overview.md#network-planning)

1. Győződjön meg arról, hogy a [PowerShell kívánt állapotú linuxos konfigurációjának](https://github.com/Microsoft/PowerShell-DSC-for-Linux) legújabb verziója telepítve van az Azure Automation-állapot konfigurációjához beépítendő gépeken.
1. Ha a [PowerShell DSC LCM alapértelmezés szerint](/powershell/scripting/dsc/managing-nodes/metaConfig4) megfelel a használati eset, és azt szeretné, hogy a gépek beépítése, hogy azok is lekérése s és jelentést azure Automation állapotkonfiguráció, tegye a következőket:

   a. Minden Linux-gépen az Azure Automation-állapot `Register.py` konfigurációja alaplapi, használja a fedélzeti őket a PowerShell DSC helyi configuration manager alapértelmezett használatával.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   b. Az Automation-fiók regisztrációs kulcsának és regisztrációs URL-címének megkereséséhez tekintse meg [a beépített biztonságos an-](#onboard-securely-by-using-registration) és regisztrációs szakasza a cikk.  
   c. Ugrás a 4.
   
1. Ha a PowerShell DSC LCM alapértelmezett alapértelmezései nem egyeznek meg a használati eset, vagy ha azt szeretné, hogy a gépek, amelyek csak az Azure Automation-állapot konfigurációja, kövesse az a-d lépéseket. Ellenkező esetben folytassa közvetlenül a d.

    a. A szükséges DSC-metakonfigurációkat tartalmazó mappa létrehozásához kövesse a [DSC metakonfigurációk generálása szakasz utasításait.](#generate-dsc-metaconfigurations)

    b. Győződjön meg arról, hogy a [Windows Management Framework 5](https://aka.ms/wmf5latest) legújabb verziója telepítve van a bevezetéshez használt számítógépen.

    c. Ha a PowerShell DSC metakonfigurációit távolról szeretné alkalmazni a bejelentkezni kívánt gépekre, adja hozzá a következő kódot:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

    d. Ha nem tudja távolról alkalmazni a PowerShell DSC metakonfigurációit, másolja a távoli gépeknek megfelelő metakonfigurációkat a 3a lépésben leírt mappából a Linux-gépekre.

1. Adjon hozzá *kódot,* amely Set-DscLocalConfigurationManager.py helyileg hívja az Azure Automation-állapotkonfigurációba való bedolgozandó minden Linux-gépen.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Az Azure Portalon vagy a parancsmagok használatával győződjön meg arról, hogy a gépek az alaplapi most megjelennek az Azure Automation-fiókban regisztrált DSC-csomópontok.

## <a name="generate-dsc-metaconfigurations"></a>DSC-metakonfigurációk létrehozása

Bármely gép nek az Azure Automation állapotkonfigurációba való bedeszkázására [dsc-metakonfigurációt](/powershell/scripting/dsc/managing-nodes/metaConfig)hozhat létre. Ez a konfiguráció azt mondja a DSC-ügynök lekérése az Azure Automation állapotkonfiguráció. DSC-metakonfigurálás az Azure Automation-állapot konfigurációja egy PowerShell DSC-konfiguráció vagy az Azure Automation PowerShell-parancsmagok használatával hozhat létre.

> [!NOTE]
> A DSC-metakonfigurációk tartalmazzák azokat a titkos kulcsokat, amelyek egy számítógép felügyeleti automatizálási fiókba való bevezetéséhez szükséges. Ügyeljen arra, hogy megfelelően védje a létrehozott DSC-metakonfigurálásokat, vagy használat után törölje azokat.

A metakonfigurációk proxytámogatását az LCM, a Windows PowerShell DSC-motorja szabályozza. Az LCM az összes célcsomóponton fut, és felelős a DSC metakonfigurációs parancsfájlban szereplő konfigurációs erőforrások hívásáért. 

A proxytámogatást a metakonfigurációba is beillesztheti, ha szükség szerint belefoglalja `ResourceRepositoryWeb`a `ReportServerWeb` proxy URL-címének és a proxyhitelesítő adatoknak a szükséges definícióit a , és a `ConfigurationRepositoryWeb`blokkokba. Lásd: [A helyi konfigurációkezelő konfigurálása](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-by-using-a-dsc-configuration"></a>DSC-metakonfigurációk létrehozása DSC-konfiguráció használatával

1. Nyissa meg a Visual Studio-kódot (vagy kedvenc szerkesztőjét) rendszergazdaként a helyi környezetben lévő számítógépen. A számítógépnek telepítve kell lennie a [Windows Management Framework 5](https://aka.ms/wmf5latest) legújabb verziójával.
1. Másolja a következő parancsfájlt helyileg. Ez a parancsfájl egy PowerShell DSC-konfigurációt tartalmaz a metakonfigurációk létrehozásához, és egy parancsot a metakonfiguráció létrehozásának indításához.

    > [!NOTE]
    > Az állapotkonfigurációs csomópont konfigurációjának nevei az Azure Portalon a kis- és nagybetűket megkülönböztetők. Ha az eset nem egyezik, a csomópont nem jelenik meg a **Csomópontok** lapon.

   ```powershell
   # The DSC configuration that will generate metaconfigurations.
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

    # Create the metaconfigurations.
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: Edit the following as needed for your use case.
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

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   DscMetaConfigs @Params
   ```

1. Töltse ki az Automation-fiók regisztrációs kulcsát és URL-címét, valamint a fedélzetre hozandó gépek nevét. Minden más paraméter nem kötelező. Az Automation-fiók regisztrációs kulcsának és regisztrációs URL-címének megkereséséhez tekintse meg [az Alaplap biztonságos regisztráció a regisztráció használatával](#onboard-securely-by-using-registration) című szakaszban.

1. Ha azt szeretné, hogy a gépek jelentsék a DSC állapotadatait az Azure `ReportOnly` Automation state configuration, de nem lekéréses konfiguráció vagy PowerShell-modulok, állítsa a paraméter *igaz*értékre.

1. Ha `ReportOnly` nincs beállítva, a gépek jelentik a DSC állapotadatait az Azure Automation állapotkonfigurációjának és lekéréses konfigurációjának vagy A PowerShell-moduloknak. Ennek megfelelően állítsa `ConfigurationRepositoryWeb`be `ResourceRepositoryWeb`a `ReportServerWeb` paramétereket a és a blokkban.

1. Futtassa a szkriptet. Most rendelkeznie kell egy *DscMetaConfigs*nevű működő könyvtármappával, amely a powershelldsc-metakonfigurációkat tartalmazza a gépek (rendszergazdaként) számára.

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-by-using-azure-automation-cmdlets"></a>DSC-metakonfigurációk létrehozása az Azure Automation-parancsmagok használatával

Ha a PowerShell DSC LCM alapértelmezett egyezik a használati eset, és azt szeretné, hogy a gépek be, és az Azure Automation-állapot konfigurációja, a szükséges DSC-metakonfigurációk egyszerűbben az Azure Automation-parancsmagok használatával.

1. Nyissa meg a PowerShell-konzolt vagy a Visual Studio-kódot rendszergazdaként a helyi környezetben lévő számítógépen.
1. Csatlakozzon az Azure Resource Managerhez a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0)segítségével.
1. Az Automation-fiókból, amelyben csomópontokat állít be, töltse le a PowerShell DSC-metakonfigurációkat a bedolgozni kívánt gépekhez.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting.
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Most rendelkeznie kell egy *DscMetaConfigs* mappával, amely a PowerShell DSC metakonfigurációit tartalmazza a bedolgozni kívánt gépekhez (rendszergazdaként).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboard-securely-by-using-registration"></a>Biztonságosan belegabalyulaszkozás a regisztráció segítségével

A gépek biztonságosan beszervezhetők egy Azure Automation-fiókba a Windows Management Framework 5 DSC regisztrációs protokollon keresztül. Ez a protokoll lehetővé teszi, hogy egy DSC-csomópont hitelesítse magát egy PowerShell DSC-lekéréses vagy jelentéskészítő kiszolgálón, beleértve az Azure Automation állapotkonfigurációját is. A csomópont regisztrál a kiszolgálóval a regisztrációs URL-címen, és regisztrációs kulccsal hitelesíti magát. 

A regisztráció során a DSC-csomópont és a DSC lekéréses/jelentéskészítő kiszolgáló egyedi tanúsítványt egyeztet meg a kiszolgáló nak a regisztráció t követően történő hitelesítéshez. Ez a folyamat megakadályozza, hogy a fedélzeti csomópontok megszemélyesítsék egymást (például ha egy csomópont biztonsága sérül, és rosszindulatúan viselkedik). 

A regisztráció után a regisztrációs kulcs nem lesz újra felhasználva a hitelesítéshez, és törlődik a csomópontról.

Az Állapotkonfiguráció regisztrációs protokollhoz szükséges információk bekéréséhez nyissa meg az Azure Portalt, és a **Fiókbeállítások**csoportban válassza a **Kulcsok lehetőséget.** 

![Az Azure automatizálási kulcsai és URL-címe a Kulcsok ablaktáblában](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- **Regisztrációs URL**: Az **URL-mezőben** lévő érték.
- **Regisztrációs kulcs**: Az **Elsődleges hozzáférési kulcs** vagy a Másodlagos hozzáférési **kulcs** mező értéke. Mindkét gombot használhatja.

A nagyobb biztonság érdekében az Automation-fiók elsődleges és másodlagos hozzáférési kulcsait bármikor újragenerálhatja a **Kulcsok** ablaktáblán. A kulcs-regeneráció megakadályozza, hogy a jövőbeli csomópont-regisztrációk korábbi kulcsokat használjanak.

## <a name="re-register-a-node"></a>Csomópont újbóli regisztrálása

Miután egy számítógépet DSC-csomópontként regisztrál az Azure Automation-állapotkonfigurációban, előfordulhat, hogy a jövőben újra regisztrálnia kell a csomópontot a következő okok valamelyike miatt:

- **Tanúsítványmegújítás**: A Windows Server 2019-nél korábbi Windows Server-verziók esetén minden csomópont automatikusan egyeztet egy egyedi hitelesítési tanúsítványt, amely egy év elteltével lejár. Ha egy tanúsítvány megújítás nélkül lejár, a csomópont nem tud kommunikálni az Azure Automation szolgáltatással, és *nem válaszol.* 

  Jelenleg a PowerShell DSC regisztrációs protokoll nem tudja automatikusan megújítani a tanúsítványokat, amikor azok a lejárat idohez közelednek, és egy év elteltével újra regisztrálnia kell a csomópontokat. Az újbóli regisztráció előtt győződjön meg arról, hogy minden csomóponton fut a Windows Management Framework 5 RTM. 

    Az újraregisztráció a tanúsítvány lejárati idejétől vagy a tanúsítvány lejárati ideje után bármikor 90 vagy kevesebb napon belül történt, és új tanúsítvány jön létre és használ. A Windows Server 2019-es és újabb verziói ban megoldást találni a probléma megoldására.

- **A DSC LCM-értékek módosítása**: Előfordulhat, `ConfigurationMode`hogy módosítania kell a [PowerShell DSC LCM értékeit,](/powershell/scripting/dsc/managing-nodes/metaConfig4) amelyeket a csomópont kezdeti regisztrációja során állítottak be (például ). Jelenleg ezeket a DSC-ügynökértékeket csak újraregisztrálással módosíthatja. Az egyetlen kivétel a csomóponthoz rendelt csomópontkonfigurációs érték. Ezt az értéket közvetlenül az Azure Automation DSC-ben módosíthatja.

A csomópontot ugyanúgy újra regisztrálhatja, ahogy eredetileg regisztrálta, a jelen dokumentumban ismertetett bevezetési módszerek bármelyikének használatával. Nem kell törölnie egy csomópontot az Azure Automation-állapotkonfigurációból, mielőtt újra regisztrálna.

## <a name="troubleshoot-azure-virtual-machine-onboarding"></a>Az Azure virtuális gépek bevezetésének hibái

Az Azure Automation állapotkonfiguráció lehetővé teszi, hogy könnyen alaplapi Azure Windows virtuális gépek konfigurációkezeléshez. A motorháztető alatt az Azure VM kívánt állapotkonfigurációs bővítménye a virtuális gép regisztrálására szolgál az Azure Automation state configuration szolgáltatással. Mivel az Azure VM kívánt állapotkonfigurációs bővítmény aszinkron módon fut, a folyamat nyomon követése és a végrehajtás hibaelhárítása fontos lehet.

> [!NOTE]
> Az Azure-beli Windows virtuális gép Azure Automation-állapotkonfigurációba történő bevezetés bármely olyan módszere, amely az Azure VM kívánt állapotkonfigurációs bővítményét használja, akár egy órát is igénybe vehet, amíg az Azure Automation regisztráltként jeleníti meg. Ez a késés a Windows Management Framework 5 telepítésének eredménye a virtuális gépen az Azure VM kívánt állapotkonfigurációs bővítménye által, amely a virtuális gép azure Automation-állapotkonfigurációba való üzembe helyezéséhez szükséges.

Az Azure VM kívánt állapotkonfigurációs bővítményének hibaelhárítása vagy állapotának megtekintése:

1. Az Azure Portalon nyissa meg a beépülő virtuális gép.
2. A **BEÁLLÍTÁSOK** területen válassza a **Bővítmények** elemet. 
3. Válassza a **DSC** vagy **a DSCForLinux**lehetőséget az operációs rendszertől függően. 
4. A kiterjesztéssel kapcsolatos további tudnivalókért válassza a **Részletes állapot megtekintése**lehetőséget.

A hibaelhárításról további információt az [Azure Automation kívánt állapotkonfigurációjával (DSC) kapcsolatos problémák elhárítása című témakörben](./troubleshoot/desired-state-configuration.md)talál.

## <a name="next-steps"></a>További lépések

- Első lépések: [Az Azure Automation állapotkonfigurációjának első lépései.](automation-dsc-getting-started.md)
- A DSC-konfigurációk összeállításáról a célcsomópontokhoz való hozzárendelésről az [Azure Automation állapotkonfigurációja konfigurációk fordítása témakörben](automation-dsc-compile.md)olvashat.
- A PowerShell-parancsmag referencia, lásd: [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Díjszabási információkért lásd: [Azure Automation state configuration fortaring.](https://azure.microsoft.com/pricing/details/automation/)
- Az Azure Automation State Configuration folyamatos üzembe helyezési folyamatban való használatának például az [Azure Automation állapotkonfigurációját és a Chocolatey szolgáltatást használó virtuális gépekfolyamatos üzembe helyezése című témakört.](automation-dsc-cd-chocolatey.md)
