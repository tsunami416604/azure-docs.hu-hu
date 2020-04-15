---
title: Bevezetési gépek az Azure Automation állapotkonfigurációja általi kezeléshez
description: Gépek beállítása felügyeleti célokra az Azure Automation állapotkonfigurációjával
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: c718b9a66b378044618c8c52eec3a1a498ace83c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383208"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Bevezetési gépek az Azure Automation állapotkonfigurációja általi kezeléshez

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Miért érdemes gépeket kezelni az Azure Automation állapotkonfigurációval?

Az Azure Automation State Configuration egy konfigurációkezelő szolgáltatás a kívánt állapotkonfigurációs (DSC) csomópontokhoz bármely felhőbeli vagy helyszíni adatközpontban. Az Azure Portalon érhető el az **Állapotkonfiguráció (DSC)** **konfigurációs kezelés**csoportban. 

Ez a szolgáltatás lehetővé teszi a több ezer gép közötti méretezhetőséget gyorsan és egyszerűen egy központi, biztonságos helyről. Könnyedén bedeszkázhatja a gépeket, deklaratív konfigurációkat rendelhet hozzájuk, és megtekintheti azokat a jelentéseket, amelyek megmutatják, hogy az egyes gépek megfelelnek-e a megadott állapotnak.

Az Azure Automation állapotkonfigurációs szolgáltatás a DSC, amit az Azure Automation runbookok a PowerShell-parancsfájlok. Más szóval, ugyanúgy, ahogy az Azure Automation segít a PowerShell-parancsfájlok kezelésében, a DSC-konfigurációk kezelését is segíti. Ha többet szeretne megtudni az Azure Automation állapotkonfiguráció használatának előnyeiről, olvassa el az [Azure Automation állapotkonfiguráció – áttekintés című témakört.](automation-dsc-overview.md)

Az Azure Automation állapotkonfigurációja számos gép kezelésére használható:

- Azure virtuális gépek
- Azure virtuális gépek (klasszikus)
- Fizikai/virtuális Windows-gépek a helyszínen vagy az Azure-tól eltérő felhőben (beleértve az AWS EC2-példányokat is)
- Fizikai/virtuális Linux-gépek a helyszínen, az Azure-ban vagy az Azure-tól eltérő felhőben

Ha nem áll készen a gépkonfiguráció kontrájának kezelésére a felhőből, használhatja az Azure Automation State Configuration csak jelentésvégpontként. Ez a funkció lehetővé teszi, hogy a DSC-n keresztül állítsa be (leküldéses) konfigurációkat, és tekintse meg a jelentéskészítési részleteket az Azure Automationben.

> [!NOTE]
> Az Azure-beli virtuális gépek kezelése az Azure Automation állapotkonfigurációval díjmentesen biztosított, ha a telepített Azure VM kívánt állapotkonfigurációs bővítmény verziója nagyobb, mint 2,70. További információ: [**Automation pricing page**](https://azure.microsoft.com/pricing/details/automation/).

A cikk következő szakaszai ismertetik, hogyan lehet a fent felsorolt gépeket az Azure Automation-állapot konfigurációba.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="onboarding-azure-vms"></a>Bevezetés az Azure virtuális gépeibe

Az Azure Automation State Configuration segítségével egyszerűen beszervezheti az Azure-beli virtuális gépeket a konfigurációkezeléshez az Azure Portal, az Azure Resource Manager-sablonok vagy a PowerShell használatával. A motorháztető alatt, és anélkül, hogy a rendszergazda kelljen egy virtuális gép, az Azure vm kívánt állapot konfigurációs bővítmény regisztrálja a virtuális gépet az Azure Automation állapotkonfiguráció. Mivel az Azure-bővítmény aszinkron módon fut, a folyamat előrehaladásának nyomon követésére vagy a hibaelhárítási lépések a cikk [hibaelhárítása azure-beli virtuálisgép-bevezetési](#troubleshooting-azure-virtual-machine-onboarding) szakaszában találhatók.

> [!NOTE]
>A DSC linuxos csomópontra történő telepítése a **/tmp** mappát használja. A modulokat, például `nxautomation` a rendszer ideiglenesen letölti őket ellenőrzésre, mielőtt a megfelelő helyükre telepítené őket. Annak érdekében, hogy a modulok megfelelően települjenek, a Linuxos Log Analytics-ügynöknek olvasási/írási engedélyeket kell kérnie a **/tmp** mappához.<br><br>
>A Log Analytics ügynök Linux `omsagent` fut, mint a felhasználó. Ha írási engedélyt `omsagent` szeretne adni >`setfacl -m u:omsagent:rwx /tmp`a felhasználónak, futtassa a parancsot.

### <a name="onboard-a-vm-using-azure-portal"></a>Virtuális gép alaplapja az Azure Portal használatával

Az Azure-beli virtuális gép az Azure Automation-állapot konfigurációja az [Azure Portalon](https://portal.azure.com/)keresztül:

1. Keresse meg azt az Azure Automation-fiókot, amelyben a beépített virtuális gépeket. 

2. Az Állapotkonfiguráció lapon válassza a **Csomópontok** lapot, majd kattintson a **Hozzáadás**gombra.

3. Válasszon egy virtuális gép a fedélzeten.

4. Ha a számítógépen nincs telepítve a kívánt PowerShell-állapotbővítmény, és az energiaállapot fut, kattintson a **Csatlakozás**gombra.

5. A **Regisztráció csoportban**adja meg a [PowerShell DSC helyi konfigurációkezelő a](/powershell/scripting/dsc/managing-nodes/metaConfig) használati esethez szükséges értékeket. Szükség esetén megadhat egy csomópont-konfigurációt a virtuális géphez rendelve.

![bevezetés](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>Virtuális gép alaplapja az Azure Resource Manager-sablonokkal

Az Azure Resource Manager-sablonok használatával üzembe helyezheti és üzembe helyezheti az Azure Automation-állapot konfigurációját. Lásd: [A kívánt állapotkonfigurációs szolgáltatás által felügyelt kiszolgáló](https://azure.microsoft.com/resources/templates/101-automation-configuration/) egy példasablont, amely egy meglévő virtuális gép ről az Azure Automation-állapot konfigurációjára kerül. Ha virtuálisgép-méretezési készletet kezel, tekintse meg a példasablont az [Azure Automation által felügyelt VM méretezési csoport konfigurációjában.](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)

### <a name="onboard-machines-using-powershell"></a>PowerShellt használó beépített gépek

Használhatja a [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) parancsmag a PowerShell-ben az Azure Automation állapotkonfigurációja alaplapi virtuális gépek. 

> [!NOTE]
>A `Register-AzAutomationDscNode` parancsmag jelenleg csak a Windows rendszert futtató gépeken van megvalósítva, mivel csak a Windows bővítményt indítja el.

### <a name="register-vms-across-azure-subscriptions"></a>Virtuális gépek regisztrálása az Azure-előfizetések között

A más Azure-előfizetésekből származó virtuális gépek regisztrálásának legjobb módja a DSC-bővítmény használata egy Azure Resource Manager központi telepítési sablonban. Példák találhatók [a Kívánt állapot konfiguráció bővítmény az Azure Resource Manager sablonokat.](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)

A sablonban paraméterekként használandó regisztrációs kulcs és regisztrációs URL megkereséséhez tekintse meg a jelen cikk [Regisztráció biztonságos használatával](#onboarding-securely-using-registration) című szakaszában található Bevezetés című témakört.

## <a name="onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances"></a>Fizikai/virtuális Windows-gépek helyszíni bevezetése vagy az Azure-tól eltérő felhőben (beleértve az AWS EC2-példányokat is)

A helyszíni vagy más felhőalapú környezetekben futó Windows-kiszolgálókat az Azure Automation állapotkonfigurációja szolgáltatásba is belehet- vezetve. A kiszolgálóknak kimenő hozzáféréssel kell rendelkezniük [az Azure-hoz.](automation-dsc-overview.md#network-planning)

1. Győződjön meg arról, hogy a [WMF 5](https://aka.ms/wmf5latest) legújabb verziója telepítve van a gépeken az Azure Automation state configuration alaplapra. Ezenkívül a WMF 5-öt a bevezetési művelethez használt számítógépre kell telepíteni.
1. Kövesse a [DSC metakonfigurációk generálása](#generating-dsc-metaconfigurations) című szakasz utasításait a szükséges DSC-metakonfigurációkat tartalmazó mappa létrehozásához. 
1. A következő parancsmag segítségével alkalmazza a PowerShell DSC metakonfigurációk távolról a gépek, amelyek a fedélzeten alkalmazni szeretné. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Ha nem tudja távolról alkalmazni a PowerShell DSC metakonfigurációit, másolja a **metakonfigurációk** mappát a bevezetésre kerülő gépekre. Ezután adja `Set-DscLocalConfigurationManager` hozzá a helyileg hívandó kódot a gépeken.
1. Az Azure Portalon vagy a parancsmagok használatával ellenőrizze, hogy a gépek a fedélzeten jelennek meg az Azure Automation-fiókban regisztrált állapotkonfigurációs csomópontok.

## <a name="onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Fizikai/virtuális Linux-gépek helyszíni vagy az Azure-tól eltérő felhőben való bevezetés

A helyszíni vagy más felhőalapú környezetekben futó Linux-kiszolgálókat az Azure Automation state configuration szolgáltatásba is belehet, hogy beszálljon. A kiszolgálóknak kimenő hozzáféréssel kell rendelkezniük [az Azure-hoz.](automation-dsc-overview.md#network-planning)

1. Győződjön meg arról, hogy a [PowerShell kívánt állapotú linuxos konfigurációjának](https://github.com/Microsoft/PowerShell-DSC-for-Linux) legújabb verziója telepítve van a gépeken az Azure Automation állapotkonfigurációra való alaplaphoz.
2. Ha a [PowerShell DSC Helyi Configuration Manager alapértelmezett egyezik](/powershell/scripting/dsc/managing-nodes/metaConfig4) a használati eset, és azt szeretné, hogy a gépek et úgy, hogy azok mind lekérése s és jelentést az Azure Automation állapot konfigurációja:

   - Minden Linux-gépen az Azure Automation-állapot `Register.py` konfigurációja alaplapra, használja a beépített a PowerShell DSC helyi konfigurációkezelő alapértelmezett használatával.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Az Automation-fiók regisztrációs kulcsának és regisztrációs URL-címének megkereséséhez tekintse meg a cikk [regisztrációbiztonságos használatával](#onboarding-securely-using-registration) című bevezetés című részét.

3. Ha a PowerShell DSC Helyi Configuration Manager (LCM) alapértelmezés e nem egyezik meg a használati eset, vagy szeretné, hogy a beépített gépek, amelyek csak az Azure Automation állapotkonfiguráció, kövesse a 4-7. Ellenkező esetben folytassa közvetlenül a 7.

4. A [DSC-metakonfigurációk generálása](#generating-dsc-metaconfigurations) szakasz utasításainak követésével készítsen létre egy mappát, amely tartalmazza a szükséges DSC-metakonfigurációkat.

5. Győződjön meg arról, hogy a [WMF 5](https://aka.ms/wmf5latest) legújabb verziója telepítve van a bevezetésre használt gépen.

6. Adja hozzá a kódot az alábbiak szerint a PowerShell DSC metaconfigurations távoli alkalmazásához a kívánt gépekre.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Ha nem tudja távolról alkalmazni a PowerShell DSC metakonfigurációit, másolja a távoli gépeknek megfelelő metakonfigurációkat a 4.

8. Adjon hozzá `Set-DscLocalConfigurationManager.py` kódot, amelyet minden Linux-gépen helyileg hívhat az Azure Automation állapotkonfigurációjához.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Az Azure Portalon vagy a parancsmagok használatával győződjön meg arról, hogy a gépek az alaplapi most megjelennek az Azure Automation-fiókban regisztrált DSC-csomópontok.

## <a name="generating-dsc-metaconfigurations"></a>DSC-metakonfigurációk létrehozása

Bármely gép nek az Azure Automation állapotkonfigurációba való bedeszkázására [dsc-metakonfigurációt](/powershell/scripting/dsc/managing-nodes/metaConfig)hozhat létre. Ez a konfiguráció azt mondja a DSC-ügynök lekérése és/vagy jelentés az Azure Automation állapotkonfiguráció. DSC-metakonfigurálás az Azure Automation-állapot konfigurációja egy PowerShell DSC-konfiguráció vagy az Azure Automation PowerShell-parancsmagok használatával hozhat létre.

> [!NOTE]
> A DSC-metakonfigurációk tartalmazzák a számítógép egy Automation-fiókfelügyeleti rendszerbe való beirányításához szükséges titkokat. Győződjön meg arról, hogy megfelelően védi a létrehozott DSC-metakonfigurációkat, vagy használat után törli azokat.

A metakonfigurációk proxytámogatását az LCM, a Windows PowerShell DSC-motorja szabályozza. Az LCM az összes célcsomóponton fut, és felelős a DSC metakonfigurációs parancsfájlban szereplő konfigurációs erőforrások hívásáért. A proxytámogatást a metakonfigurációba is beillesztheti, ha szükség szerint belefoglalja `ResourceRepositoryWeb`a `ReportServerWeb` proxy URL-címének és a proxyhitelesítő adatoknak a szükséges definícióit a , és a `ConfigurationRepositoryWeb`blokkokba. Lásd: [A helyi konfigurációkezelő konfigurálása](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>DSC-metakonfigurációk létrehozása DSC-konfiguráció használatával

1. Nyissa meg a VSCode-ot (vagy kedvenc szerkesztőjét) rendszergazdaként a helyi környezetben lévő számítógépen. A készüléknek a [WMF 5](https://aka.ms/wmf5latest) legújabb verziójával kell rendelkeznie.
1. Másolja a következő parancsfájlt helyileg. Ez a parancsfájl egy PowerShell DSC-konfigurációt tartalmaz a metakonfigurációk létrehozásához, és egy parancsot a metakonfiguráció létrehozásának indításához.

    > [!NOTE]
    > Az állapotkonfigurációs csomópont konfigurációjának nevei az Azure Portalon a kis- és nagybetűket megkülönböztetők. Ha az eset nem egyezik, a csomópont nem jelenik meg a **Csomópontok** lapon.

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

1. Töltse ki az Automation-fiók regisztrációs kulcsát és URL-címét, valamint a fedélzetre hozandó gépek nevét. Minden más paraméter nem kötelező. A regisztrációs kulcs és az Automation-fiók regisztrációs URL-címének megkereséséhez tekintse meg a [Regisztráció biztonságos használatával](#onboarding-securely-using-registration) című bevezetés című szakaszt.

1. Ha azt szeretné, hogy a gépek jelentsenek DSC-állapotinformációkat az Azure Automation State `ReportOnly` Configuration szolgáltatásnak, de ne kérjék le a konfigurációt vagy a PowerShell-modulokat, állítsa a paramétert true értékre.

1. Ha `ReportOnly` nincs beállítva, a gépek jelentik a DSC állapotadatait az Azure Automation állapotkonfigurációjának és lekéréses konfigurációjának vagy A PowerShell-moduloknak. Ennek megfelelően állítsa `ConfigurationRepositoryWeb`be `ResourceRepositoryWeb`a `ReportServerWeb` paramétereket a és a blokkban.

1. Futtassa a szkriptet. Most már rendelkeznie kell egy **DscMetaConfigs**nevű működő könyvtármappával, amely tartalmazza a PowerShell DSC metakonfigurációit a gépek számára (rendszergazdaként).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>DSC-metakonfigurálások létrehozása az Azure Automation-parancsmagokkal

Ha a PowerShell DSC LCM alapértelmezett beállításai megfelelnek a használati esetnek, és azt szeretné, hogy a gépek et az Azure Automation-állapot konfigurációjából való lekérése és jelentése egyaránt bekapcsolja, és a szükséges DSC-metakonfigurációkat egyszerűbben hozhatja létre az Azure Automation-parancsmagok használatával.

1. Nyissa meg a PowerShell-konzolt vagy a VSCode-ot rendszergazdaként a helyi környezetben lévő számítógépen.
2. Csatlakozzon az Azure Resource Managerhez a [Connect-AzAccount segítségével.](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0)
3. Töltse le a PowerShell DSC metaconfigurations a gépek kívánt a fedélzeti az Automation-fiók, amelyben a csomópontok beállítása.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Most rendelkeznie kell egy **DscMetaConfigs** mappával, amely a PowerShell DSC metakonfigurációit tartalmazza a gépek számára (rendszergazdaként).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Bevezetés biztonságosan a regisztráció használatával

A gépek biztonságosan bevihetők egy Azure Automation-fiókba a WMF 5 DSC regisztrációs protokollon keresztül. Ez a protokoll lehetővé teszi, hogy egy DSC-csomópont hitelesítse magát egy PowerShell DSC-lekéréses vagy jelentéskészítő kiszolgálón, beleértve az Azure Automation állapotkonfigurációját is. A csomópont regisztrál a kiszolgálóval a regisztrációs URL-címen, és regisztrációs kulccsal hitelesíti magát. A regisztráció során a DSC-csomópont és a DSC lekéréses/jelentéskészítő kiszolgáló egyedi tanúsítványt egyeztet meg a kiszolgáló regisztráció utáni hitelesítéséhez. Ez a folyamat megakadályozza, hogy a fedélzeti csomópontok megszemélyesítsék egymást, például ha egy csomópont biztonsága sérül, és rosszindulatúan viselkedik. A regisztráció után a regisztrációs kulcs nem használható újra a hitelesítéshez, és törlődik a csomópontról.

Az Állapotkonfiguráció regisztrációs protokollhoz szükséges információkat az Azure Portal **Fiókbeállítások** csoportban szerezheti be a **Kulcsok.** 

![Azure automatizálási kulcsok és URL-cím](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- A regisztrációs URL a Kulcsok lap URL-mezője.
- A regisztrációs kulcs az **Elsődleges hozzáférési kulcs** mező vagy a Másodlagos hozzáférési **kulcs** mező értéke a Kulcsok lapon. Mindkét kulcs használható.

A nagyobb biztonság érdekében az Automation-fiók elsődleges és másodlagos hozzáférési kulcsait bármikor újragenerálhatja a Kulcsok lapon. A kulcs-regeneráció megakadályozza, hogy a jövőbeli csomópont-regisztrációk korábbi kulcsokat használjanak.

## <a name="re-registering-a-node"></a>Csomópont újbóli regisztrálása

Miután egy gépet DSC-csomópontként regisztrált az Azure Automation-állapotkonfigurációban, számos oka lehet annak, hogy a jövőben újra regisztrálnia kell a csomópontot.

- **Tanúsítvány megújítása.** A Windows Server 2019 előtti Windows Server-verziók esetében minden csomópont automatikusan egyeztet egy egyedi hitelesítési tanúsítványt, amely egy év elteltével lejár. Ha egy tanúsítvány megújítás nélkül lejár, a csomópont nem tud `Unresponsive`kommunikálni az Azure Automation szolgáltatással, és meg van jelölve. Jelenleg a PowerShell DSC regisztrációs protokoll nem tudja automatikusan megújítani a tanúsítványokat, amikor azok a lejárat idohez közelednek, és egy év elteltével újra regisztrálnia kell a csomópontokat. Az újbóli regisztrálás előtt győződjön meg arról, hogy minden csomóponton fut a WMF 5 RTM. 

    Az újraregisztráció a tanúsítvány lejárati idejétől vagy a tanúsítvány lejárati ideje után bármikor 90 napig vagy annál rövidebb ideig történt, és új tanúsítvány jön létre és használ. A Windows Server 2019-es és újabb verziói ban megoldást találni a probléma megoldására.

- **A DSC LCM értékek módosítása.** Előfordulhat, hogy módosítania kell a [PowerShell DSC LCM](/powershell/scripting/dsc/managing-nodes/metaConfig4) beállított értékeit a csomópont kezdeti regisztrációja során, `ConfigurationMode`például. Jelenleg ezek a DSC-ügynökértékek csak újraregisztrálás útján módosíthatók. Az egyetlen kivétel a csomóponthoz rendelt csomópontkonfigurációs érték. Ezt közvetlenül az Azure Automation DSC-ben módosíthatja.

A csomópontot ugyanúgy újra regisztrálhatja, ahogy először regisztrálta a csomópontot, a jelen dokumentumban leírt bevezetési módszerek bármelyikének használatával. Nem kell törölnie egy csomópontot az Azure Automation-állapotkonfigurációból, mielőtt újra regisztrálna.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Az Azure virtuális gépek bevezetésével kapcsolatos hibák elhárítása

Az Azure Automation állapotkonfiguráció lehetővé teszi, hogy könnyen alaplapi Azure Windows virtuális gépek konfigurációkezeléshez. A motorháztető alatt az Azure VM kívánt állapotkonfigurációs bővítménye a virtuális gép regisztrálására szolgál az Azure Automation state configuration szolgáltatással. Mivel az Azure VM kívánt állapotkonfigurációs bővítmény aszinkron módon fut, a folyamat nyomon követése és a végrehajtás hibaelhárítása fontos lehet.

> [!NOTE]
> Az Azure-beli Windows virtuális gép Azure Automation-állapotkonfigurációba történő bevezetés bármely olyan módszere, amely az Azure VM kívánt állapotkonfigurációs bővítményét használja, akár egy órát is igénybe vehet, amíg az Azure Automation regisztráltként jeleníti meg. Ez a késés annak köszönhető, hogy a WMF 5 telepítése a virtuális gépen az Azure VM kívánt állapotkonfigurációs bővítmény, amely szükséges a virtuális gép az Azure Automation állapotkonfiguráció.

Az Azure VM kívánt állapotkonfigurációs bővítményének hibaelhárítása vagy állapotának megtekintése:

1. Az Azure Portalon keresse meg a beépített virtuális gép.
2. Kattintson **a Bővítmények gombra a** Beállítások **csoportban.** 
3. Most válassza a **DSC** vagy **a DSCForLinux**lehetőséget, az operációs rendszertől függően. 
4. További részletekért kattintson **a Részletes állapot megtekintése gombra.**

A hibaelhárításról az [Azure Automation kívánt állapotkonfigurációjával (DSC) kapcsolatos problémák elhárítása című](./troubleshoot/desired-state-configuration.md)témakörben talál további információt.

## <a name="next-steps"></a>További lépések

- Első lépések: [Az Azure Automation állapotkonfigurációjának első lépései.](automation-dsc-getting-started.md)
- A DSC-konfigurációk összeállításáról a célcsomópontokhoz való hozzárendelésről a [Konfigurációk összeállítása az Azure Automation állapotkonfigurációjában](automation-dsc-compile.md)témakörben olvashat.
- A PowerShell-parancsmag referencia, lásd: [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Díjszabási információkért lásd: [Azure Automation state configuration fortaring.](https://azure.microsoft.com/pricing/details/automation/)
- Az Azure Automation State Configuration folyamatos üzembe helyezési folyamatban való használatának példáját a [Használati példa: Az Azure Automation állapotkonfigurációját és a Csokoládét használó virtuális gépeken történő folyamatos üzembe helyezés](automation-dsc-cd-chocolatey.md)című témakörben talál.
