---
title: Gépek előkészítése kezelésre, az Azure Automation állapot konfigurációja
description: Gépek kezelése az Azure Automation Állapotkonfiguráció-telepítés
services: automation
ms.service: automation
ms.component: dsc
author: bobbytreed
ms.author: robreed
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: 554c575f338ebaa415ed21be8dc8b27eb79c3c0c
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634404"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Gépek előkészítése kezelésre, az Azure Automation állapot konfigurációja

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Miért kezelése az Azure Automation Állapotkonfiguráció gépek?

Például [PowerShell Desired State Configuration](/powershell/dsc/overview), az Azure Automation konfigurációs egy egyszerű, mégis hatékony, felügyeleti konfigurációs szolgáltatáshoz tartozó DSC-csomópontok (fizikai és virtuális gépek) bármely felhőbeli vagy helyszíni adatközpontban . Lehetővé teszi méretezhetőség több ezer gépet gyorsan és egyszerűen üzembe helyezhető, biztonságos helyről. Akkor is, könnyen előkészítheti a gépeket, rendelje hozzá őket a deklaratív konfigurációkat, és a jelentések megjelenítése bemutató minden egyes számítógép-felhasználói állapothoz megadott megfelelőség. Az Azure Automation konfigurációs felügyeleti réteget, hogy a DSC, az Azure Automation felügyeleti réteg a PowerShell-parancsprogramok. Más szóval segítő Azure Automation PowerShell-parancsprogramok kezelése ugyanúgy, is segít DSC-konfigurációk kezelése. Azure Automation State Configuration használatával járó előnyöket kapcsolatos további információkért lásd: [Azure Automation konfiguráló áttekintése](automation-dsc-overview.md).

Azure Automation Állapotkonfiguráció különböző gépek kezeléséhez használhatók:

- Az Azure virtual machines (klasszikus és Azure Resource Manager üzemi modell is telepített)
- Az Amazon Web Services (AWS) EC2-példányok
- Fizikai/virtuális Windows-számítógépek a helyszínen, vagy nem az Azure vagy AWS felhőben
- Fizikai/virtuális Linux rendszerű számítógépek a helyszínen, az Azure-ban, vagy eltérő Azure-felhőben

Ezenkívül ha nem áll készen a gép konfigurációjának kezelése a felhőből, az Azure Automation Állapotkonfiguráció is használható csak a jelentés-végpontként. Ez lehetővé teszi helyszíni DSC keresztül (leküldés) szükségeskonfiguráció be, és gazdag jelentéskészítési részleteinek megtekintése a csomópont kívánt állapotát az Azure Automationben való megfelelést.

> [!NOTE]
> Kezelése az Azure virtuális gépek konfigurációs külön díjak nélkül tartalmazza, ha a telepített virtuális gépek DSC bővítmény 2.70-nál nagyobb. Tekintse meg a [ **díjszabását ismertető lapon Automation** ](https://azure.microsoft.com/pricing/details/automation/) további részletekért.

A következő szakaszok szerkezeti hogyan segítségével készítheti elő az Azure Automation Állapotkonfiguráció gép különböző típusú.

## <a name="azure-virtual-machines-classic"></a>Azure-beli virtuális gépek (klasszikus)

Az Azure Automation-konfiguráló szolgáltatása könnyen előkészítheti az Azure virtual machines (klasszikus) konfiguráció kezelése az Azure Portalon, vagy a PowerShell használatával is. Technikai részletek, valamint anélkül, hogy a rendszergazda nem kell a virtuális gép távolról az Azure virtuális gépek Desired State Configuration bővítmény regisztrálja a virtuális gép az Azure Automation Állapotkonfiguráció. Mivel az Azure virtuális gépek Desired State Configuration bővítmény aszinkron módon fut, a következő lépéseket az előrehaladásának követéséhez vagy hibaelhárítás biztosított [ **hibáinak elhárítása Azure virtuális gép bevezetési** ](#troubleshooting-azure-virtual-machine-onboarding) szakaszban.

### <a name="azure-portal"></a>Azure Portal

Az a [az Azure portal](http://portal.azure.com/), kattintson a **Tallózás** -> **virtuális gépek (klasszikus)**. Válassza ki az előkészíteni kívánt Windows virtuális gép. A virtuális gép irányítópult paneljén kattintson **minden beállítás** -> **bővítmények** -> **Hozzáadás** -> **Azure Az Automation DSC** -> **létrehozása**.
Adja meg a [PowerShell DSC helyi Configuration Manager értékek](/powershell/dsc/metaconfig4) hozzárendelése a virtuális gép a használati eset, az Automation-fiók regisztrációs kulcs és regisztrációs URL-címe és opcionálisan egy csomópont-konfiguráció szükséges.

![A DSC Azure Virtuálisgép-bővítmények](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

Keresse meg a regisztrációs URL-címe és az Automation-fiókot, hogy a gép, a következő kulcs [ **regisztrációs biztonságos** ](#secure-registration) szakaszban:

### <a name="powershell"></a>PowerShell

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Connect-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ''
$ServiceName = ''
$AutomationAccountName = ''
$AutomationAccountResourceGroup = ''

# fill in the name of a Node Configuration in Azure Automation State Configuration, for this VM to conform to
# NOTE: DSC Node Configuration names are case sensitive in the portal.
$NodeConfigName = ''

# get Azure Automation State Configuration registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation State Configuration
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ''
    ModulesUrl = 'https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip'
    ConfigurationFunction = 'RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2'

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://docs.microsoft.com/powershell/dsc/metaConfig for more details
    Properties = @{
        RegistrationKey = @{
            UserName = 'notused'
            Password = 'PrivateSettingsRef:RegistrationKey'
        }
        RegistrationUrl = $RegistrationInfo.Endpoint
        NodeConfigurationName = $NodeConfigName
        ConfigurationMode = 'ApplyAndMonitor'
        ConfigurationModeFrequencyMins = 15
        RefreshFrequencyMins = 30
        RebootNodeIfNeeded = $False
        ActionAfterReboot = 'ContinueConfiguration'
        AllowModuleOverwrite = $False
    }
}

$PrivateConfiguration = ConvertTo-Json -Depth 8 @{
    Items = @{
        RegistrationKey = $RegistrationInfo.PrimaryKey
    }
}

$VM = Set-AzureVMExtension `
    -VM $vm `
    -Publisher Microsoft.Powershell `
    -ExtensionName DSC `
    -Version 2.76 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

> [!NOTE]
> Állami konfigurációs csomópont-nevek a következők a portálon a nagybetűk. Ha az esetet nem egyezik a csomópont nem alatt fog megjelenni a **csomópontok** fülre.

## <a name="azure-virtual-machines"></a>Azure virtuális gépek

Azure Automation Állapotkonfiguráció teszi lehetővé egyszerűen előkészítése az Azure virtual machines, a konfigurációkezelés, az Azure Portalon, a az Azure Resource Manager-sablonok vagy a PowerShell használatával. Technikai részletek, valamint anélkül, hogy a rendszergazda nem kell a virtuális gép távolról az Azure virtuális gépek Desired State Configuration bővítmény regisztrálja a virtuális gép az Azure Automation Állapotkonfiguráció.
Mivel az Azure virtuális gépek Desired State Configuration bővítmény aszinkron módon fut, a következő lépéseket az előrehaladásának követéséhez vagy hibaelhárítás biztosított [ **hibáinak elhárítása Azure virtuális gép bevezetési** ](#troubleshooting-azure-virtual-machine-onboarding) szakaszban.

### <a name="azure-portal"></a>Azure Portal

Az a [az Azure portal](https://portal.azure.com/), keresse meg az Azure Automation-fiók, amelyre a virtuális gépek előkészítése. A konfigurációs lapon és a **csomópontok** lapra, majd **+ Hozzáadás**.

Válassza ki egy Azure virtuális gépet üzembe helyezni.

Ha a gép nem rendelkezik a PowerShell desired state bővítmény telepítve van, és a energiaállapota fut, kattintson az **Connect**.

A **regisztrációs**, adja meg a [PowerShell DSC helyi Configuration Manager értékek](/powershell/dsc/metaconfig4) hozzárendelése a virtuális gép a használati eset, és szükség esetén a csomópont-konfiguráció szükséges.

![](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Azure-beli virtuális gépek is üzembe helyezhetők és előkészítve az Azure Automation konfiguráló Azure Resource Manager-sablonok használatával. Lásd: [konfigurálhatja egy virtuális géphez a DSC-bővítmény és Azure Automation DSC](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) egy példa sablon előkészítését végző egy meglévő virtuális gép az Azure Automation Állapotkonfiguráció. Keresse meg a regisztrációs kulcs és a regisztrációs URL-cím foglalt a bemeneti ebben a sablonban, tekintse meg a következőt [ **regisztrációs biztonságos** ](#secure-registration) szakaszban.

### <a name="powershell"></a>PowerShell

A [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) parancsmag segítségével előkészítheti a virtuális gépeket az Azure Portal, PowerShell-lel.

## <a name="amazon-web-services-aws-virtual-machines"></a>Az Amazon Web Services (AWS) virtuális gépek

Könnyen előkészítheti az Amazon Web Services a virtuális gépeket konfigurációkezelés a AWS DSC eszközkészletével konfiguráló Azure Automation által is. További információ az eszközkészlet [Itt](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Fizikai/virtuális Windows-számítógépek a helyszínen, vagy nem az Azure vagy AWS felhőben

A helyi Windows-számítógépek és Windows-gépek az-Azure felhőket (például az Amazon Web Services) is lehet előkészítve az Azure Automation-konfiguráló szolgáltatása, mindaddig, amíg az interneten keresztül néhány egyszerű lépéssel kimenő hozzáféréssel rendelkeznek:

1. Ellenőrizze, hogy a legújabb [a WMF 5](http://aka.ms/wmf5latest) telepítve van az Azure Automation Állapotkonfiguráció a bevezetni kívánt számítógépeken.
1. Kövesse az alábbi szakasz utasításait [ **létrehozása DSC metaconfigurations** ](#generating-dsc-metaconfigurations) egy mappát, amely tartalmazza a szükséges DSC metaconfigurations létrehozásához.
1. A PowerShell DSC metaconfiguration távolról vonatkoznak az előkészíteni kívánt számítógépeken. **Ez a parancs fut a gépen rendelkeznie kell a legújabb [a WMF 5](http://aka.ms/wmf5latest) telepített**:

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Ha a PowerShell DSC metaconfigurations távolról nem alkalmaz, másolja a metaconfigurations mappát arra minden gépre 2. lépés üzembe helyezni. Ezután hívja meg **Set-DscLocalConfigurationManager** helyileg az összes olyan számítógépen üzembe helyezni.
1. Az Azure Portalon vagy a parancsmagok használata esetén ellenőrizze, hogy üzembe helyezni a gépek most már szabályzatként jelenik meg az Azure Automation-fiókjába regisztrált Állapotkonfiguráció csomópontok.

## <a name="physicalvirtual-linux-machines-on-premises-in-azure-or-in-a-cloud-other-than-azure"></a>Fizikai/virtuális Linux rendszerű számítógépek a helyszínen, az Azure-ban, vagy eltérő Azure-felhőben

Helyszíni Linuxos gépek, Linux rendszerű gépek az Azure-ban, és nem Azure-felhőben futó Linux rendszerű gépek is lehetnek előkészítve az Azure Automation-konfiguráló szolgáltatása, mindaddig, amíg az interneten keresztül néhány egyszerű lépéssel kimenő hozzáféréssel rendelkeznek:

1. Ellenőrizze, hogy a legújabb [PowerShell a Linuxhoz készült meg Desired State Configuration a](https://github.com/Microsoft/PowerShell-DSC-for-Linux) telepítve van az Azure Automation Állapotkonfiguráció a bevezetni kívánt számítógépeken.
1. Ha a [PowerShell DSC helyi Configuration Manager alapértelmezett](/powershell/dsc/metaconfig4) felel meg a használati eset, és készítse elő az ilyen gépek szeretné, hogy azok **mindkét** lekéréshez és Azure Automation Állapotkonfiguráció jelentést:

   - Az összes Linux az Azure Automation állapot konfiguráció előkészítése, használjon `Register.py` bevezetni a PowerShell DSC helyi Configuration Manager alapértelmezett beállítások használatával:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Az Automation-fiók található a regisztrációs kulcs és a regisztrációs URL-címe, lásd a következő [ **regisztrációs biztonságos** ](#secure-registration) szakaszban.

     Ha a PowerShell DSC helyi Configuration Manager alapértelmezés szerint **nem** egyezés az használati esetekhez, vagy a bevezetni kívánt gépek úgy, hogy azok csak az Azure Automation Állapotkonfiguráció jelentés, de nem kér, konfigurációs vagy a PowerShell modulok, akkor hajtsa végre a 3-6 lépést. Ellenkező esetben folytassa a 6.

1. Kövesse az alábbi [ **létrehozása DSC metaconfigurations** ](#generating-dsc-metaconfigurations) hozzon létre egy mappát, amely tartalmazza a szükséges DSC metaconfigurations szakaszt.
1. A PowerShell DSC metaconfiguration távolról vonatkoznak az előkészíteni kívánt számítógépeken:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Ez a parancs fut a gépen rendelkeznie kell a legújabb [a WMF 5](http://aka.ms/wmf5latest) telepítve.

1. Ha a PowerShell DSC metaconfigurations távolról, nem vonatkozik minden egyes Linux rendszerű gép felvétele, másolja az 5. lépésében a Linux-gép az alakzatot a mappából, hogy a gép megfelelő metaconfiguration. Ezután hívja meg `SetDscLocalConfigurationManager.py` helyileg az összes olyan Linux-számítógépen a bevezetni kívánt az Azure Automation Állapotkonfiguráció:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Az Azure Portalon vagy a parancsmagok használata esetén ellenőrizze, hogy a gépek felvétele most szabályzatként jelenik meg regisztrálni az Azure Automation-fiók DSC-csomópontok.

## <a name="generating-dsc-metaconfigurations"></a>DSC metaconfigurations generálása

Hogy általános védelemmel lát bármelyik a gép az Azure Automation-konfiguráló szolgáltatása, egy [DSC metaconfiguration](/powershell/dsc/metaconfig) lehet létre, amelyek, amikor a alkalmazni, arra utasítja a lekéréshez és/vagy az Azure Automation állapot jelentése a gépen a DSC-ügynök Konfiguráció. Az Azure Automation konfiguráló DSC metaconfigurations vagy a PowerShell DSC-konfiguráció, vagy az Azure Automation PowerShell-parancsmagok használatával hozhatók létre.

> [!NOTE]
> DSC metaconfigurations tartalmaz a titkos kulcsok szükséges és felvétele egy gép egy Automation-fiók kezeléséhez. Ellenőrizze, hogy minden DSC metaconfigurations létrehozása megfelelő védelmét, vagy törölje őket használat után.

### <a name="using-a-dsc-configuration"></a>A DSC-konfiguráció használata

1. Nyissa meg a VSCode (vagy a kedvenc szerkesztőjében) a helyi környezetben gépen rendszergazdaként. A gép rendelkeznie kell a legújabb [a WMF 5](http://aka.ms/wmf5latest) telepítve.
1. Másolja a következő szkriptet helyileg. Ez a szkript tartalmazza a PowerShell DSC konfigurációs metaconfigurations és a egy parancsot a metaconfiguration létrehozása indíthat létrehozásakor.

> [!NOTE]
> Állami konfigurációs csomópont-nevek a következők a portálon a nagybetűk. Ha az esetet nem egyezik a csomópont nem alatt fog megjelenni a **csomópontok** fülre.

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

1. Töltse ki a regisztrációs kulcs és az URL-cím az Automation-fiók, valamint az üzembe helyezni a gépek nevei. Minden más paraméterek opcionálisak. Az Automation-fiók található a regisztrációs kulcs és a regisztrációs URL-címe, lásd a következő [ **regisztrációs biztonságos** ](#secure-registration) szakaszban.
1. Ha azt szeretné, hogy a gépek Azure Automation-konfiguráló szolgáltatása, de a nem lekéréses konfigurációját vagy a PowerShell-modulok DSC állapotinformációk jelentésére, állítsa be a **van** paraméter igaz.
1. Futtassa a szkriptet. Most már olyan nevű mappát **DscMetaConfigs** a munkakönyvtárban tartalmazó gépekhez a PowerShell DSC metaconfigurations (rendszergazdaként) előkészítéséhez:

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Az Azure Automation-parancsmagok használatával

Ha a PowerShell DSC helyi Configuration Manager alapértelmezett felel meg a használati eset, és előkészítheti a gépeket, hogy azok is kérje le, és Azure Automation Állapotkonfiguráció jelenteni szeretné, az Azure Automation parancsmagok adjon meg egy egyszerűsített módszer létrehozása a DSC metaconfigurations szükséges:

1. Nyissa meg a PowerShell-konzol vagy a VSCode a helyi környezetben gépen rendszergazdaként.
2. Csatlakozhat az Azure Resource Manager használatával `Connect-AzureRmAccount`
3. Töltse le a PowerShell DSC metaconfigurations a gépek szeretné üzembe helyezni az Automation-fiók, amelyhez szeretné előkészíteni csomópontok:

   ```powershell
   # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzureRmAutomationDscOnboardingMetaconfig @Params
   ```

1. Most már olyan nevű mappát ***DscMetaConfigs***, a gépek a PowerShell DSC metaconfigurations tartalmazó vezethető (rendszergazdaként):

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Biztonságos regisztráció

Gépek segítségével biztonságosan készítheti elő az Azure Automation-fiók, a WMF 5 DSC regisztrációs protokoll, amely lehetővé teszi, hogy a DSC-csomópont hitelesítése egy PowerShell DSC lekéréses vagy a jelentéskészítő kiszolgálóra (beleértve az Azure Automation Állapotkonfiguráció) keresztül. A kiszolgáló regisztrálása a csomópont egy **regisztrációs URL-cím**, és ehhez használatával egy **Szolgáltatásregisztrációs kulcs**. Regisztráció, a DSC-csomópont és a DSC lekéréses/Reporting server során egy egyedi tanúsítványt ehhez a csomóponthoz, használnak a hitelesítéshez a regisztrációt követő kiszolgáló az egyeztetés. Ez a folyamat megakadályozza, hogy előkészítve csomópontok megszemélyesítésekor egy másikra, például ha egy csomópont biztonsága sérült, és ártó szándékkal viselkedik. A regisztrációt követően a regisztrációs kulcs nem használatos újból a hitelesítést, és törlődik a csomópontból.

A szükséges az Állapotkonfiguráció regisztrációs protokoll használatával információkat szerezhet a **kulcsok** alatt **fiókbeállításokat** az Azure Portalon. A kulcs ikonra kattintva nyissa meg a ezen a panelen a **Essentials** az Automation-fiókhoz tartozó panel.

![Az Azure automation-kulcsok és URL-címe](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Regisztrációs URL-címe, az URL-cím mezőben, a kulcsok kezelése panelen.
- Szolgáltatásregisztrációs kulcs az elsődleges hozzáférési kulcs vagy a másodlagos hozzáférési kulcs a kulcsok kezelése panelt. Egyiket sem használható.

A fokozott biztonság érdekében bármikor helyreállíthatja az Automation-fiók elsődleges és másodlagos hozzáférési kulcsait (a a **kulcsok kezelése** oldal), hogy a jövőbeli csomópont regisztrációk korábbi kulcsok használatával.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Hibaelhárítás az Azure virtuális gép előkészítése

Azure Automation Állapotkonfiguráció teszi lehetővé egyszerűen az Azure Windows virtuális gépek előkészítése konfigurációkezelés. Technikai részletek az Azure virtuális gépek Desired State Configuration bővítmény segítségével regisztrálja a virtuális Gépet a az Azure Automation-konfiguráló szolgáltatásával. Mivel az Azure virtuális gépek Desired State Configuration bővítmény aszinkron módon fut, a folyamat előrehaladását nyomon követése, és a végrehajtása hibaelhárítási fontos lehet.

> [!NOTE]
> Bármely metódusát meghívná, bevezetés az Azure Windows virtuális Gépekhez az Azure Automation Állapotkonfiguráció, amely használja az Azure virtuális gépek Desired State Configuration bővítmény is igénybe vehet egy órát a csomópont megjelenítése akár regisztrálva az Azure Automationben. Ez a miatt a virtuális gép által az Azure VM DSC bővítmény esetében szükséges, hogy a Windows Management Framework 5.0 telepítését a virtuális gép az Azure Automation Állapotkonfiguráció.

Hibaelhárítás, vagy megtekintheti az állapotát az Azure virtuális gépek Desired State Configuration bővítmény, az Azure Portalon lépjen a virtuális gép típusától függenek, majd kattintson **bővítmények** alatt **beállítások**. Kattintson a **DSC** vagy **DSCForLinux** operációs rendszertől függően. További részletekért kattintson **részletes állapotadatok megtekintése**.

## <a name="certificate-expiration-and-reregistration"></a>Tanúsítvány lejárati és ismételt

A gép az Azure Automation konfiguráló DSC-csomópontként a regisztrálás után van számos oka lehet, hogy miért regisztrálja újra a csomóponton a jövőben kell:

- A regisztrálás után minden csomóponton automatikusan végrehajtja a hitelesítést, amely egy év után lejár egy egyedi tanúsítványt. Jelenleg a PowerShell DSC regisztrációs protokoll nem tudja automatikusan megújítja a tanúsítványokat, amikor azok lejárnak, így újból regisztrálnunk a csomópontok egy év időpont után. Mielőtt újraregisztrálása, ellenőrizze, hogy minden csomóponton fut Windows Management Framework 5.0 RTM-re. Ha egy csomópont-hitelesítési tanúsítvány lejár, és a csomópont a rendszer nem újra regisztrálja, a csomópont nem tud kommunikálni az Azure Automation, és meg van jelölve "Unresponsive." Ismételt végrehajtott 90 napig, vagy kisebb a tanúsítvány lejárati időt, vagy a tanúsítvány lejárati idő után bármikor alatt létrehozható és használható új tanúsítványt fog eredményezni.
- Módosíthatja [PowerShell DSC helyi Configuration Manager értékek](/powershell/dsc/metaconfig4) , hogy a csomópont, például a ConfigurationMode kezdeti regisztráció során beállított. Jelenleg ezen DSC-ügynök értékek csak módosítható ismételt keresztül. Az egyetlen kivétel rendelve a csomóponthoz csomópont-konfiguráció – ez módosítható az Azure Automation DSC közvetlenül.

Ismételt regisztrálta a csomópontot először, a jelen dokumentumban ismertetett regisztrációs módszerek bármelyikével megegyező módon hajtható végre. Nem kell újból regisztrálni, mielőtt egy Azure Automation konfigurációs csomópont regisztrációját.

## <a name="next-steps"></a>További lépések

- Első lépésként lásd [Ismerkedés az Azure Automation Állapotkonfiguráció](automation-dsc-getting-started.md)
- DSC-konfigurációk fordítása, így hozzárendelheti azokat a célcsomópontokat kapcsolatos további információkért lásd: [összeállítása az Azure Automation Állapotkonfiguráció konfigurációk](automation-dsc-compile.md)
- PowerShell-parancsmagok leírása, lásd: [konfiguráló Azure Automation-parancsmagok](/powershell/module/azurerm.automation/#automation)
- Díjszabási információkért tekintse meg a [Azure Automation State Configuration díjszabása](https://azure.microsoft.com/pricing/details/automation/)
- Folyamatos üzembe helyezés folyamatban lévő Azure Automation Állapotkonfiguráció használatának példájáért lásd: [folyamatos üzembe helyezés használatával az Azure Automation Állapotkonfiguráció és a chocolatey-t](automation-dsc-cd-chocolatey.md)