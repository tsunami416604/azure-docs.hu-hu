---
title: Bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez
description: Gépek beállítása a Azure Automation állapot-konfigurációval való felügyelethez
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: d4e008a0bd43f10b01d78a1c388f1ca6fee983ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457739"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Miért érdemes a gépeket Azure Automation állapot-konfigurációval felügyelni?

Azure Automation állapot-konfiguráció a kívánt állapot-konfigurációs (DSC) csomópontok egyike a felhőben vagy a helyszíni adatközpontban. A Azure Portal az **állapot konfigurálása (DSC)** lehetőség kiválasztásával érhető el a **konfiguráció**felügyelete alatt. 

Ez a szolgáltatás lehetővé teszi, hogy a központi és biztonságos helyről gyorsan és egyszerűen lehessen méretezni a több ezer gépen. Könnyedén beépítheti a gépeket, hozzárendelheti a deklaratív konfigurációkat, és megtekintheti azokat a jelentéseket, amelyek az egyes gépek megfelelőségét a megadott kívánt állapotnak megfelelően mutatják.

A Azure Automation állapot-konfigurációs szolgáltatás azt a DSC-t adja meg, amit Azure Automation runbookok a PowerShell-parancsfájlok futtatásához. Más szóval ugyanúgy, ahogy a Azure Automation segíti a PowerShell-parancsfájlok kezelését, a DSC-konfigurációk kezelését is segíti. Ha többet szeretne megtudni a Azure Automation állapot konfigurációjának előnyeiről, tekintse meg [Azure Automation állapot-konfiguráció áttekintése](automation-dsc-overview.md)című témakört.

Azure Automation állapot-konfiguráció számos gép felügyeletére használható:

- Azure virtuális gépek
- Azure-beli virtuális gépek (klasszikus)
- Fizikai/virtuális Windowsos gépek a helyszínen vagy az Azure-tól eltérő felhőben (beleértve az AWS EC2-példányokat)
- Fizikai/virtuális Linux rendszerű számítógépek a helyszínen, az Azure-ban vagy az Azure-tól eltérő felhőben

Ha nem áll készen a számítógép-konfiguráció felhőből való kezelésére, akkor a Azure Automation állapot konfigurációját csak jelentési végpontként használhatja. Ezzel a funkcióval a DSC-n keresztül állíthatja be (leküldéses) konfigurációkat, és megtekintheti a jelentéskészítés részleteit Azure Automationban.

> [!NOTE]
> Az Azure-beli virtuális gépek Azure Automation állapotú konfigurációval való kezelése külön díj nélkül történik, ha a telepített Azure-beli virtuális gép kívánt állapot-konfigurációs bővítményének verziója nagyobb, mint 2,70. További információ: Automation- [**díjszabási oldal**](https://azure.microsoft.com/pricing/details/automation/).

A cikk következő fejezetei ismertetik, hogyan lehet bevezetni a fent felsorolt gépeket Azure Automation állapot-konfigurációhoz.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="onboarding-azure-vms"></a>Azure-beli virtuális gépek előkészítése

Azure Automation állapot-konfiguráció lehetővé teszi az Azure-beli virtuális gépek üzembe helyezését a konfiguráció kezeléséhez a Azure Portal, a Azure Resource Manager sablonok vagy a PowerShell használatával. A motorháztető alatt, és anélkül, hogy a rendszergazdának távoli GÉPEN kellene lennie, az Azure-beli virtuális gép kívánt állapota konfigurációs bővítmény regisztrálja Azure Automation állapot konfigurációjában a virtuális gépet. Mivel az Azure-bővítmény aszinkron módon fut, az előrehaladásának nyomon követéséhez és a hibaelhárításhoz szükséges lépések a jelen cikk hibaelhárítás az Azure-beli [virtuális gépek](#troubleshooting-azure-virtual-machine-onboarding) bevezetésével foglalkozó részében találhatók.

> [!NOTE]
>A DSC Linux-csomópontra történő telepítése a **/tmp** mappát használja. Azokat a modulokat, `nxautomation` amelyek a megfelelő helyekre való telepítése előtt ideiglenesen letöltődnek az ellenőrzéshez. A modulok megfelelő telepítésének biztosításához a linuxos Log Analytics-ügynöknek írási/olvasási jogosultságra van szüksége a **/tmp** mappához.<br><br>
>A Linux rendszerhez készült Log Analytics-ügynök `omsagent` felhasználóként fut. Ha >írási engedélyt szeretne adni a `omsagent` felhasználónak, futtassa a parancsot `setfacl -m u:omsagent:rwx /tmp`.

### <a name="onboard-a-vm-using-azure-portal"></a>Virtuális gép előkészítése Azure Portal használatával

Azure-beli virtuális gép beléptetése Azure Automation állapot konfigurálásához a [Azure Portal](https://portal.azure.com/)használatával:

1. Navigáljon ahhoz a Azure Automation-fiókhoz, amelyben a virtuális gépeket be szeretné készíteni. 

2. Az állapot konfigurációja lapon válassza a **csomópontok** lapot, majd kattintson a **Hozzáadás**gombra.

3. Válasszon ki egy virtuális gépet a bevezetéshez.

4. Ha a gépen nincs telepítve a PowerShell desired State bővítmény, és a tápellátási állapot fut, kattintson a **kapcsolat**gombra.

5. A **regisztráció**alatt adja meg a [PowerShell DSC helyi Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig) a használati esethez szükséges értékeket. Opcionálisan megadhat egy csomópont-konfigurációt, amelyet a virtuális géphez szeretne rendelni.

![bevezetési](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>Virtuális gép előkészítése Azure Resource Manager-sablonok használatával

A virtuális gépek üzembe helyezéséhez és bevezetéséhez Azure Resource Manager sablonok használatával Azure Automation állapot konfigurációját. Tekintse meg a [kiszolgáló által felügyelt, a kívánt állapot-konfigurációs szolgáltatást](https://azure.microsoft.com/resources/templates/101-automation-configuration/) egy olyan sablonhoz, amely egy meglévő virtuális gépet Azure Automation állapot konfigurációjának bevezetéséhez. Ha virtuálisgép-méretezési csoport kezelésére van szüksége, tekintse meg a [Azure Automation által felügyelt virtuálisgép-méretezési csoport konfigurációjának](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)példa sablonját.

### <a name="onboard-machines-using-powershell"></a>Gépek előkészítése a PowerShell használatával

A PowerShellben a [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) parancsmaggal telepíthet virtuális gépeket Azure Automation állapot-konfigurációra. 

> [!NOTE]
>A `Register-AzAutomationDscNode` parancsmag jelenleg csak Windows rendszerű gépeken van implementálva, mivel csak a Windows-bővítményt indítja el.

### <a name="register-vms-across-azure-subscriptions"></a>Virtuális gépek regisztrálása Azure-előfizetések között

A virtuális gépek más Azure-előfizetésből való regisztrálásának legjobb módja a DSC-bővítmény használata egy Azure Resource Manager telepítési sablonban. Példákat a [Azure Resource Manager sablonokkal rendelkező, a kívánt állapotú konfigurációs bővítményben](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)talál.

A sablonban paraméterként használandó regisztrációs kulcs és regisztrációs URL-cím megkereséséhez tekintse meg a jelen cikk a [regisztráció biztonságos használatával](#onboarding-securely-using-registration) című szakaszát.

## <a name="onboarding-physicalvirtual-windows-machines"></a>Fizikai/virtuális Windows-gépek bevezetése

A helyszíni vagy más felhőalapú környezetekben (például AWS EC2-példányokban) futó Windows-kiszolgálókat az állapot konfigurációjának Azure Automation. A kiszolgálóknak kimenő hozzáféréssel kell rendelkezniük az [Azure-hoz](automation-dsc-overview.md#network-planning).

1. Győződjön meg arról, hogy a [WMF 5](https://aka.ms/wmf5latest) legújabb verziója van telepítve a gépekre Azure Automation állapot konfigurációjának bevezetéséhez. Emellett a WMF 5-et a bevezetési művelethez használt számítógépre kell telepíteni.
1. Kövesse a [DSC-Metaconfigurations létrehozása](#generating-dsc-metaconfigurations) című szakasz utasításait, és hozzon létre egy mappát, amely tartalmazza a szükséges DSC-metaconfigurations. 
1. Az alábbi parancsmag használatával a PowerShell DSC-metaconfigurations távolról is alkalmazhatja a bevezetéshez használni kívánt gépekre. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Ha nem tudja távolról alkalmazni a PowerShell DSC-metaconfigurations, másolja a **metaconfigurations** mappát az előkészítés alatt álló gépekre. Ezután adjon hozzá kódot a [set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) helyi hívásához a gépeken.
1. A Azure Portal vagy a parancsmagok használatával ellenőrizze, hogy a bevezetésre kerülő gépek a Azure Automation-fiókjában regisztrált állapot-konfigurációs csomópontként jelennek-e meg.

## <a name="onboarding-physicalvirtual-linux-machines"></a>Fizikai/virtuális Linux rendszerű gépek bevezetése

A helyszíni vagy más felhőalapú környezetekben futó Linux-kiszolgálókat az állapot konfigurációjának Azure Automation. A kiszolgálóknak kimenő hozzáféréssel kell rendelkezniük az [Azure-hoz](automation-dsc-overview.md#network-planning).

1. Győződjön meg arról, hogy a Linux rendszerhez készült [PowerShell kívánt állapot-konfiguráció](https://github.com/Microsoft/PowerShell-DSC-for-Linux) legújabb verziója telepítve van a gépeken, hogy Azure Automation állapot konfigurációját.
2. Ha a [POWERSHELL DSC helyi Configuration Manager alapértelmezett](/powershell/scripting/dsc/managing-nodes/metaConfig4) beállításai megfelelnek a használati esetnek, és a gépeket kívánja bevezetni, hogy mind lekérik, és jelenteni tudják Azure Automation állapot konfigurációját:

   - Minden Linux rendszerű gépen Azure Automation állapot konfigurációjának bevezetéséhez `Register.py` használja a következőt: a PowerShell DSC helyi Configuration Manager alapértelmezett értékeit használja.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Az Automation-fiók regisztrációs kulcsának és regisztrációs URL-címének megkereséséhez tekintse meg a jelen cikk [regisztrációjának biztonságos használata](#onboarding-securely-using-registration) című szakaszát.

3. Ha a PowerShell DSC helyi Configuration Manager (LCD ChipOnGlas) alapértelmezett értéke nem felel meg a használati esetnek, vagy ha olyan gépeket kíván bevezetni, amelyek csak a Azure Automation állapot konfigurációját jelentik, kövesse az 4-7. lépést. Ellenkező esetben folytassa közvetlenül a 7. lépéssel.

4. Kövesse a [DSC Metaconfigurations létrehozása](#generating-dsc-metaconfigurations) szakasz utasításait, és hozzon létre egy mappát, amely tartalmazza a szükséges DSC-metaconfigurations.

5. Győződjön meg arról, hogy a [WMF 5](https://aka.ms/wmf5latest) legújabb verziója van telepítve a bevezetéshez használt gépen.

6. Adja hozzá a következő kódot a PowerShell DSC-metaconfigurations távolról a bevezetéshez használni kívánt gépekre való alkalmazásához.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Ha nem tudja távolról alkalmazni a PowerShell DSC-metaconfigurations, másolja a távoli gépeknek megfelelő metaconfigurations a 4. lépés a Linux rendszerű gépekhez című témakörben leírt mappából.

8. Az egyes Linux rendszerű gépeken helyileg hívható `Set-DscLocalConfigurationManager.py` kód hozzáadásával Azure Automation állapot konfigurációját.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. A Azure Portal vagy a parancsmagok használatával győződjön meg arról, hogy a bevezetésre kerülő gépek most a Azure Automation-fiókban regisztrált DSC-csomópontként jelennek meg.

## <a name="generating-dsc-metaconfigurations"></a>DSC-metaconfigurations generálása

Ha bármely gépet Azure Automation állapot-konfigurációba kíván bevezetni, létrehozhat egy [DSC-metaconfiguration](/powershell/scripting/dsc/managing-nodes/metaConfig). Ez a konfiguráció azt jelzi, hogy a DSC-ügynök lekéri és/vagy jelentést készít Azure Automation állapot-konfigurációról. A DSC-metaconfiguration a PowerShell DSC-konfiguráció vagy a Azure Automation PowerShell-parancsmagok használatával hozhatja Azure Automation állapot-konfigurációhoz.

> [!NOTE]
> A DSC-metaconfigurations azokat a titkokat tartalmazzák, amelyek szükségesek ahhoz, hogy a gép egy Automation-fiókba kerüljön a felügyelethez. Ügyeljen arra, hogy megfelelően megvédje a létrehozott DSC-metaconfigurations, vagy törölje azokat a használat után.

A metaconfigurations proxy-támogatását az LCD ChipOnGlas vezérli, amely a Windows PowerShell DSC motor. Az LCD ChipOnGlas az összes cél csomóponton fut, és felelős a DSC metaconfiguration-szkriptben található konfigurációs erőforrások meghívásához. A proxy támogatását a metaconfiguration is elvégezheti, ha a proxy URL-címét és a proxy hitelesítő adatait `ConfigurationRepositoryWeb`a `ResourceRepositoryWeb`, a `ReportServerWeb` , és a blokkokban szükség szerint tartalmazza. Lásd: [a helyi Configuration Manager konfigurálása](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>DSC-metaconfigurations létrehozása DSC-konfiguráció használatával

1. Nyissa meg a VSCode (vagy a kedvenc szerkesztőjét) rendszergazdaként a helyi környezetben található gépen. A gépnek telepítve kell lennie a [WMF 5](https://aka.ms/wmf5latest) legújabb verziójával.
1. Másolja a következő parancsfájlt helyileg. Ez a szkript egy PowerShell DSC-konfigurációt tartalmaz a metaconfigurations létrehozásához, valamint egy olyan parancsot, amely kirúgja a metaconfiguration létrehozását.

    > [!NOTE]
    > Az állapot-konfigurációs csomópontok konfigurációs nevei megkülönböztetik a kis-és nagybetűket a Azure Portal. Ha az eset nem egyezik, a csomópont nem jelenik meg a **csomópontok** lapon.

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

1. Adja meg az Automation-fiókjához tartozó regisztrációs kulcsot és URL-címet, valamint a bevezetéshez használt gépek nevét. Az összes többi paraméter megadása nem kötelező. Az Automation-fiók regisztrációs kulcsának és regisztrációs URL-címének megkereséséhez tekintse meg a [regisztráció biztonságos használatával](#onboarding-securely-using-registration) című szakaszt.

1. Ha azt szeretné, hogy a gépek jelentést készítsenek a DSC-állapotadatok Azure Automation állapot konfigurációjában, de ne legyenek lekéréses konfiguráció vagy PowerShell-modulok, állítsa igaz értékre a `ReportOnly` paramétert.

1. Ha `ReportOnly` nincs beállítva, a gépek a DSC-állapotinformációkat Azure Automation állapot-konfigurációra és lekéréses konfigurációra vagy PowerShell-modulokra jelentik. Adja meg a paramétereket ennek `ConfigurationRepositoryWeb`megfelelően `ResourceRepositoryWeb`a, `ReportServerWeb` és blokkokban.

1. Futtassa a szkriptet. Most már rendelkeznie kell egy **DscMetaConfigs**nevű munkakönyvtár-mappával, amely a PowerShell DSC-metaconfigurations tartalmazza a gépekhez a bevezetéshez (rendszergazdaként).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>DSC-metaconfigurations előállítása Azure Automation-parancsmagok használatával

Ha a PowerShell DSC LCD-alapértékei megfelelnek a használati esetnek, és azt szeretné, hogy a gépeket a beléptetéshez és a jelentéshez is Azure Automation az állapot konfigurációját, egyszerűen létrehozhatja a szükséges DSC-metaconfigurations az Azure Automation-parancsmagok használatával.

1. Nyissa meg a PowerShell-konzolt vagy a VSCode rendszergazdaként egy gépen a helyi környezetben.
2. Kapcsolódás Azure Resource Manager a [AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0)használatával.
3. Töltse le a PowerShell DSC-metaconfigurations azon gépekhez, amelyekhez be szeretné állítani az Automation-fiókot, amelyben csomópontokat állít be.

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

1. Most már rendelkeznie kell egy **DscMetaConfigs** -mappával, amely a PowerShell DSC-metaconfigurations tartalmazza a gépekhez a bevezetéshez (rendszergazdaként).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Biztonságos bevezetés a regisztrációval

A gépek a WMF 5 DSC regisztrációs protokollon keresztül biztonságosan bejelentkezhetnek egy Azure Automation fiókba. Ez a protokoll lehetővé teszi a DSC-csomópontok hitelesítését egy PowerShell DSC-lekéréses vagy jelentéskészítő kiszolgálón, beleértve a Azure Automation állapot konfigurációját. A csomópont regisztrálja a kiszolgálót a regisztrációs URL-címen, és egy regisztrációs kulccsal hitelesíti magát. A regisztráció során a DSC-csomópont és a DSC-lekérési/jelentéskészítő kiszolgáló egyeztet egy egyedi tanúsítvánnyal ahhoz, hogy a csomópont hitelesítve legyen a regisztráció után. Ez a folyamat megakadályozza, hogy a beépített csomópontok megszemélyesítsék egymást, például ha egy csomópontot feltörtek és rosszindulatúan viselkednek. A regisztrációt követően a regisztrációs kulcsot nem használja újra a hitelesítéshez, és a rendszer törli a csomópontról.

Az állapot-konfigurációs regisztrációs protokollhoz szükséges adatokat a Azure Portalban található **Fiókbeállítások** **területen érheti el.** 

![Azure Automation-kulcsok és URL-cím](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- A regisztrációs URL-cím a kulcsok lap URL-mezője.
- A regisztrációs kulcs az **elsődleges hozzáférési kulcs** mező vagy a kulcsok lapon található **másodlagos elérési kulcs** mező értéke. Bármelyik kulcsot használhatja.

A nagyobb biztonság érdekében a kulcsok oldalon bármikor újragenerálhatja egy Automation-fiók elsődleges és másodlagos hozzáférési kulcsait. A kulcs újragenerálása megakadályozza a későbbi csomópontok regisztrációját a korábbi kulcsok használatával.

## <a name="re-registering-a-node"></a>Csomópont ismételt regisztrálása

Miután Azure Automation állapot konfigurációjában egy gépet DSC-csomópontként regisztrált, több oka lehet annak, hogy a későbbiekben újra regisztrálnia kell a csomópontot.

- **Tanúsítvány megújítása.** A Windows Server 2019 előtti verziói esetében minden egyes csomópont automatikusan egyeztet egy egyedi tanúsítványt a hitelesítéshez, amely egy év után lejár. Ha egy tanúsítvány megújítása nélkül lejár, a csomópont nem tud kommunikálni Azure Automation és meg van `Unresponsive`jelölve. Jelenleg a PowerShell DSC regisztrációs protokoll nem tudja automatikusan megújítani a tanúsítványokat, amikor közelednek a lejárathoz, és egy év múlva újra regisztrálnia kell a csomópontokat. Az újbóli regisztrálás előtt győződjön meg arról, hogy minden csomóponton WMF 5 RTM fut. 

    Az újbóli regisztráció a tanúsítvány lejárati idejének 90 vagy kevesebb nap múlva, vagy a tanúsítvány lejárati idejének lejárta után bármikor új tanúsítványt hoz létre és használ. A probléma megoldása a Windows Server 2019-es és újabb verzióiban is megtalálható.

- **A DSC LCD-értékek változásai.** Előfordulhat, hogy módosítania kell a [POWERSHELL DSC LCD-értékeket](/powershell/scripting/dsc/managing-nodes/metaConfig4) a csomópont kezdeti regisztrálása során, például `ConfigurationMode`:. Jelenleg csak a DSC-ügynök értékeit módosíthatja újbóli regisztráció útján. Az egyetlen kivétel a csomóponthoz rendelt csomópont-konfigurációs érték. Ezt közvetlenül Azure Automation DSC-ben is megváltoztathatja.

A csomópontot úgy is újra regisztrálhatja, ahogy először regisztrálta a csomópontot, a jelen dokumentumban ismertetett bevezetési módszerek bármelyikével. Az újbóli regisztrálás előtt nem kell megszüntetnie a csomópont regisztrációját Azure Automation állapot-konfigurációból.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Azure-beli virtuális gépek előkészítésének hibaelhárítása

Azure Automation állapot-konfiguráció lehetővé teszi az Azure Windows rendszerű virtuális gépek egyszerű üzembe helyezését a konfiguráció felügyeletéhez. A motorháztető alatt az Azure VM desired State Configuration bővítmény a virtuális gép Azure Automation állapot-konfigurációval való regisztrálására szolgál. Mivel az Azure-beli virtuális gép kívánt állapotának konfigurációs bővítménye aszinkron módon fut, fontos lehet az előrehaladás nyomon követése és a végrehajtásuk hibaelhárítása.

> [!NOTE]
> Az Azure-beli Windows rendszerű virtuális gépek bevezetésének bármely módszere az Azure-beli virtuális gép kívánt állapot-konfigurációs bővítményét használó Azure Automation állapot-konfigurációhoz akár egy óráig is eltarthat, amíg a Azure Automation regisztráltként jelenik meg. Ezt a késleltetést a virtuális gépeken az Azure-beli kívánt állapot konfigurációs bővítményének a WMF 5 telepítése okozza, amely a virtuális gép bevezetéséhez szükséges a Azure Automation állapotának konfigurálásához.

Az Azure-beli virtuális gép kívánt állapotához tartozó konfiguráció-bővítmény hibáinak megoldása vagy megtekintése:

1. A Azure Portal navigáljon a bevezető virtuális géphez.
2. Kattintson a **bővítmények** elemre a **Beállítások**területen. 
3. Most válassza a **DSC** vagy a **DSCForLinux**lehetőséget az operációs rendszertől függően. 
4. További részletekért kattintson a **részletes állapot megtekintése**lehetőségre.

A hibaelhárítással kapcsolatos további információkért lásd: [Azure Automation kívánt állapot-konfigurációval (DSC) kapcsolatos hibák elhárítása](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>További lépések

- Első lépésként tekintse meg [az Azure Automation állapot konfigurációjának megismerése](automation-dsc-getting-started.md)című témakört.
- Ha szeretne többet megtudni a DSC-konfigurációk fordításáról, hogy hozzá lehessen rendelni őket a célcsoportokhoz, tekintse meg a [konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md)című témakört.
- A PowerShell-parancsmagok leírása: [az. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/).
- A folyamatos üzembe helyezési folyamat során Azure Automation állapot konfigurációjának használatára példát a következő témakörben talál [: a virtuális gépek folyamatos üzembe helyezése Azure Automation állapot-konfigurációval és a csokoládés használatával](automation-dsc-cd-chocolatey.md).
