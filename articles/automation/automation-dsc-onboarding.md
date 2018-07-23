---
title: Gépek előkészítése kezelésre, amelyet az Azure Automation DSC
description: Gépek kezelése az Azure Automation DSC-telepítés
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.topic: conceptual
ms.date: 07/20/2018
manager: carmonm
ms.openlocfilehash: 907bb9b998012a5b907c041b3eba061036289cc1
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186985"
---
# <a name="onboarding-machines-for-management-by-azure-automation-dsc"></a>Gépek előkészítése kezelésre, amelyet az Azure Automation DSC

## <a name="why-manage-machines-with-azure-automation-dsc"></a>Miért kezelése az Azure Automation DSC gépek?

Például [PowerShell Desired State Configuration](https://technet.microsoft.com/library/dn249912.aspx), az Azure Automation Desired State Configuration egy olyan egyszerű, mégis hatékony, konfigurációs felügyeleti szolgáltatás DSC csomópontok (fizikai és virtuális gépek) bármilyen felhőben vagy helyszíni Adatközpont. Lehetővé teszi méretezhetőség több ezer gépet gyorsan és egyszerűen üzembe helyezhető, biztonságos helyről. Akkor is, könnyen előkészítheti a gépeket, rendelje hozzá őket a deklaratív konfigurációkat, és a jelentések megjelenítése bemutató minden egyes számítógép-felhasználói állapothoz megadott megfelelőség. Az Azure Automation DSC felügyeleti réteget, hogy a DSC, az Azure Automation felügyeleti réteg a PowerShell-parancsprogramok. Más szóval segítő Azure Automation PowerShell-parancsprogramok kezelése ugyanúgy, is segít DSC-konfigurációk kezelése. Azure Automation DSC használatával járó előnyöket kapcsolatos további információkért lásd: [áttekintése az Azure Automation DSC](automation-dsc-overview.md).

Az Azure Automation DSC különböző gépek kezeléséhez használhatók:

* Az Azure virtual machines (klasszikus és Azure Resource Manager üzemi modell is telepített)
* Az Amazon Web Services (AWS) EC2-példányok 
* Fizikai/virtuális Windows-számítógépek a helyszínen, vagy nem az Azure vagy AWS felhőben
* Fizikai/virtuális Linux rendszerű számítógépek a helyszínen, az Azure-ban, vagy eltérő Azure-felhőben

Ezenkívül ha nem áll készen a gép konfigurációjának kezelése a felhőben, az Azure Automation DSC is használható csak a jelentés-végpontként. Ez lehetővé teszi helyszíni DSC keresztül (leküldés) szükségeskonfiguráció be, és gazdag jelentéskészítési részleteinek megtekintése a csomópont kívánt állapotát az Azure Automationben való megfelelést.

> [!NOTE]
> Azure virtuális gépek DSC kezeléséhez része külön díjfizetés nélkül Ha a telepített virtuális gépek DSC bővítmény 2.70-nál nagyobb. Tekintse meg a [ **díjszabását ismertető lapon Automation** ](https://azure.microsoft.com/pricing/details/automation/) további részletekért.


A következő szakaszok szerkezeti hogyan segítségével készítheti elő egyes típusú gépek az Azure Automation DSC.

## <a name="azure-virtual-machines-classic"></a>Azure-beli virtuális gépek (klasszikus)

Az Azure Automation DSC könnyen előkészítheti az Azure virtual machines (klasszikus) konfiguráció kezelése az Azure Portalon, vagy a PowerShell használatával is. Technikai részletek, valamint anélkül, hogy a rendszergazda nem kell a virtuális gép távolról az Azure virtuális gépek Desired State Configuration bővítmény regisztrálja a virtuális gép az Azure Automation DSC. Mivel az Azure virtuális gépek Desired State Configuration bővítmény aszinkron módon fut, a következő lépéseket az előrehaladásának követéséhez vagy hibaelhárítás biztosított [ **hibáinak elhárítása Azure virtuális gép bevezetési** ](#troubleshooting-azure-virtual-machine-onboarding) szakaszban.

### <a name="azure-portal"></a>Azure Portal

Az a [az Azure portal](http://portal.azure.com/), kattintson a **Tallózás** -> **virtuális gépek (klasszikus)**. Válassza ki az előkészíteni kívánt Windows virtuális gép. A virtuális gép irányítópult paneljén kattintson **minden beállítás** -> **bővítmények** -> **Hozzáadás** -> **Azure Az Automation DSC** -> **létrehozása**. Adja meg a [PowerShell DSC helyi Configuration Manager értékek](https://msdn.microsoft.com/powershell/dsc/metaconfig4) hozzárendelése a virtuális gép a használati eset, az Automation-fiók regisztrációs kulcs és regisztrációs URL-címe és opcionálisan egy csomópont-konfiguráció szükséges.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

Keresse meg a regisztrációs URL-címe és az Automation-fiókot, hogy a gép, a következő kulcs [ **regisztrációs biztonságos** ](#secure-registration) szakaszban:

### <a name="powershell"></a>PowerShell

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Connect-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ""
$ServiceName = ""
$AutomationAccountName = ""
$AutomationAccountResourceGroup = ""

# fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
# NOTE: DSC Node Configuration names are case sensitive in the portal.
$NodeConfigName = ""

# get Azure Automation DSC registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation DSC
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ""
    ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
    ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
    RegistrationKey = @{
        UserName = 'notused'
        Password = 'PrivateSettingsRef:RegistrationKey'
    }
    RegistrationUrl = $RegistrationInfo.Endpoint
    NodeConfigurationName = $NodeConfigName
    ConfigurationMode = "ApplyAndMonitor"
    ConfigurationModeFrequencyMins = 15
    RefreshFrequencyMins = 30
    RebootNodeIfNeeded = $False
    ActionAfterReboot = "ContinueConfiguration"
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
    -Version 2.19 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

> [!NOTE]
> DSC csomópont-konfiguráció megkülönböztetik a kis-és nagybetűket a portálon. Ha az esetet nem egyezik a csomópont nem fog megjelenni a DSC-csomópontok.

## <a name="azure-virtual-machines"></a>Azure virtuális gépek

Az Azure Automation DSC lehetővé teszi a konfigurációkezelés, könnyen előkészítheti az Azure virtual machines az Azure Portalon, a az Azure Resource Manager-sablonok vagy a PowerShell használatával. Technikai részletek, valamint anélkül, hogy a rendszergazda nem kell a virtuális gép távolról az Azure virtuális gépek Desired State Configuration bővítmény regisztrálja a virtuális gép az Azure Automation DSC. Mivel az Azure virtuális gépek Desired State Configuration bővítmény aszinkron módon fut, a következő lépéseket az előrehaladásának követéséhez vagy hibaelhárítás biztosított [ **hibáinak elhárítása Azure virtuális gép bevezetési** ](#troubleshooting-azure-virtual-machine-onboarding) szakaszban.

### <a name="azure-portal"></a>Azure Portal

Az a [az Azure portal](https://portal.azure.com/), keresse meg az Azure Automation-fiók, amelyre a virtuális gépek előkészítése. Az automatizálási fiók irányítópultján kattintson **DSC-csomópontok** -> **+ Azure-beli virtuális gép hozzáadása**.

Válassza ki egy Azure virtuális gépet üzembe helyezni.

Ha a gép nem rendelkezik a PowerShell desired state bővítmény telepítve van, és a energiaállapota fut, kattintson az **Connect**.

A **regisztrációs**, adja meg a [PowerShell DSC helyi Configuration Manager értékek](https://msdn.microsoft.com/powershell/dsc/metaconfig4) hozzárendelése a virtuális gép a használati eset, és szükség esetén a csomópont-konfiguráció szükséges.

![](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Azure-beli virtuális gépek is üzembe helyezhetők és előkészítve az Azure Automation DSC-n keresztül az Azure Resource Manager-sablonok. Lásd: [konfigurálhatja egy virtuális géphez a DSC-bővítmény és Azure Automation DSC](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) egy példa sablon előkészítését végző egy meglévő virtuális gép az Azure Automation DSC. Keresse meg a regisztrációs kulcs és a regisztrációs URL-cím foglalt a bemeneti ebben a sablonban, tekintse meg a következőt [ **regisztrációs biztonságos** ](#secure-registration) szakaszban.

### <a name="powershell"></a>PowerShell

A [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) parancsmag segítségével előkészítheti a virtuális gépeket az Azure Portal, PowerShell-lel.

## <a name="amazon-web-services-aws-virtual-machines"></a>Az Amazon Web Services (AWS) virtuális gépek

Könnyen előkészítheti az Amazon Web Services a virtuális gépeket konfigurációkezelés által az Azure Automation DSC az AWS DSC-eszközkészlet használatával is. További információ az eszközkészlet [Itt](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Fizikai/virtuális Windows-számítógépek a helyszínen, vagy nem az Azure vagy AWS felhőben

A helyi Windows-számítógépek és Windows-gépek az-Azure felhőket (például az Amazon Web Services) is lehet előkészítve az Azure Automation DSC, mindaddig, amíg az interneten keresztül néhány egyszerű lépéssel kimenő hozzáféréssel rendelkeznek:

1. Ellenőrizze, hogy a legújabb [a WMF 5](http://aka.ms/wmf5latest) telepítve van az Azure Automation DSC, a bevezetni kívánt számítógépeken.
2. Kövesse az alábbi szakasz utasításait [ **létrehozása DSC metaconfigurations** ](#generating-dsc-metaconfigurations) egy mappát, amely tartalmazza a szükséges DSC metaconfigurations létrehozásához.
3. A PowerShell DSC metaconfiguration távolról vonatkoznak az előkészíteni kívánt számítógépeken. **Ez a parancs fut a gépen rendelkeznie kell a legújabb [a WMF 5](http://aka.ms/wmf5latest) telepített**:

    ```powershell
    Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
    ```

4. Ha a PowerShell DSC metaconfigurations távolról nem alkalmaz, másolja a metaconfigurations mappát arra minden gépre 2. lépés üzembe helyezni. Ezután hívja meg **Set-DscLocalConfigurationManager** helyileg az összes olyan számítógépen üzembe helyezni.
5. Az Azure Portalon vagy a parancsmagok használata esetén ellenőrizze, hogy a gépek felvétele most szabályzatként jelenik meg regisztrálni az Azure Automation-fiók DSC-csomópontok.

## <a name="physicalvirtual-linux-machines-on-premises-in-azure-or-in-a-cloud-other-than-azure"></a>Fizikai/virtuális Linux rendszerű számítógépek a helyszínen, az Azure-ban, vagy eltérő Azure-felhőben

A helyszíni Linuxos gépek, Linux rendszerű gépek az Azure-ban, és nem Azure-felhőben futó Linux rendszerű gépek is lehetnek előkészítve az Azure Automation DSC, mindaddig, amíg az interneten keresztül néhány egyszerű lépéssel kimenő hozzáféréssel rendelkeznek:

1. Ellenőrizze, hogy a legújabb [PowerShell a Linuxhoz készült meg Desired State Configuration a](https://github.com/Microsoft/PowerShell-DSC-for-Linux) telepítve van az Azure Automation DSC, a bevezetni kívánt számítógépeken.
2. Ha a [PowerShell DSC helyi Configuration Manager alapértelmezett](https://msdn.microsoft.com/powershell/dsc/metaconfig4) felel meg a használati eset, és készítse elő az ilyen gépek szeretné, hogy azok **mindkét** lekéréshez és Azure Automation DSC jelentést:

   + Az összes Linux üzembe helyezni az Azure Automation DSC használja a Register.py üzembe helyezni a PowerShell DSC helyi Configuration Manager alapértelmezett beállítások használatával:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   + Az Automation-fiók található a regisztrációs kulcs és a regisztrációs URL-címe, lásd a következő [ **regisztrációs biztonságos** ](#secure-registration) szakaszban.

     Ha a PowerShell DSC helyi Configuration Manager alapértelmezés szerint **tegye** **nem** egyezés az használati esetekhez, vagy a bevezetni kívánt gépek úgy, hogy azok csak az Azure Automation DSC jelentés, de nem kér a konfigurációs vagy PowerShell-modulok, akkor hajtsa végre a 3-6 lépést. Ellenkező esetben folytassa a 6.

3. Kövesse az alábbi [ **létrehozása DSC metaconfigurations** ](#generating-dsc-metaconfigurations) hozzon létre egy mappát, amely tartalmazza a szükséges DSC metaconfigurations szakaszt.
4. A PowerShell DSC metaconfiguration távolról vonatkoznak az előkészíteni kívánt számítógépeken:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard

    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Ez a parancs fut a gépen rendelkeznie kell a legújabb [a WMF 5](http://aka.ms/wmf5latest) telepítve.

1. Ha a PowerShell DSC metaconfigurations távolról, nem vonatkozik minden egyes Linux rendszerű gép felvétele, másolja az 5. lépésében a Linux-gép az alakzatot a mappából, hogy a gép megfelelő metaconfiguration. Ezután hívja meg `SetDscLocalConfigurationManager.py` helyileg az összes olyan Linux-számítógépen a bevezetni kívánt az Azure Automation DSC:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Az Azure Portalon vagy a parancsmagok használata esetén ellenőrizze, hogy a gépek felvétele most szabályzatként jelenik meg regisztrálni az Azure Automation-fiók DSC-csomópontok.

## <a name="generating-dsc-metaconfigurations"></a>DSC metaconfigurations generálása

Hogy általános védelemmel lát bármelyik a gép az Azure Automation DSC, egy [DSC metaconfiguration](https://msdn.microsoft.com/powershell/dsc/metaconfig) lehet jön létre, amely alkalmazásakor arra utasítja a DSC-ügynök lekéréshez és/vagy az Azure Automation DSC jelentheti a gépen. Az Azure Automation DSC DSC metaconfigurations vagy a PowerShell DSC-konfiguráció, vagy az Azure Automation PowerShell-parancsmagok használatával hozhatók létre.

> [!NOTE]
> DSC metaconfigurations tartalmaz a titkos kulcsok szükséges és felvétele egy gép egy Automation-fiók kezeléséhez. Ellenőrizze, hogy minden DSC metaconfigurations létrehozása megfelelő védelmét, vagy törölje őket használat után.

### <a name="using-a-dsc-configuration"></a>A DSC-konfiguráció használata

1. A helyi környezetben gépen nyissa meg rendszergazdaként egy PowerShell ISE-ben. A gép rendelkeznie kell a legújabb [a WMF 5](http://aka.ms/wmf5latest) telepítve.
2. Másolja a következő szkriptet helyileg. Ez a szkript tartalmazza a PowerShell DSC konfigurációs metaconfigurations és a egy parancsot a metaconfiguration létrehozása indíthat létrehozásakor.

> [!NOTE]
> DSC csomópont-konfiguráció megkülönböztetik a kis-és nagybetűket a portálon. Ha az esetet nem egyezik a csomópont nem fog megjelenni a DSC-csomópontok.

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

            [String]$ConfigurationMode = "ApplyAndMonitor",

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = "ContinueConfiguration",

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq "")
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
        $RefreshMode = "PUSH"
        }
        else
        {
        $RefreshMode = "PULL"
        }

        Node $ComputerName
        {

            Settings
            {
                RefreshFrequencyMins = $RefreshFrequencyMins
                RefreshMode = $RefreshMode
                ConfigurationMode = $ConfigurationMode
                AllowModuleOverwrite = $AllowModuleOverwrite
                RebootNodeIfNeeded = $RebootNodeIfNeeded
                ActionAfterReboot = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationDSC
                {
                    ServerUrl = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationDSC
                {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationDSC
            {
                ServerUrl = $RegistrationUrl
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

3. Töltse ki a regisztrációs kulcs és az URL-cím az Automation-fiók, valamint az üzembe helyezni a gépek nevei. Minden más paraméterek opcionálisak. Az Automation-fiók található a regisztrációs kulcs és a regisztrációs URL-címe, lásd a következő [ **regisztrációs biztonságos** ](#secure-registration) szakaszban.
4. Ha azt szeretné, hogy az Azure Automation DSC, de nem lekéréses konfigurációs vagy PowerShell-modulok DSC állapotadatait a gépek, állítsa be a **van** paraméter igaz.
5. Futtassa a szkriptet. Most már olyan nevű mappát **DscMetaConfigs** a munkakönyvtárban tartalmazó gépekhez a PowerShell DSC metaconfigurations (rendszergazdaként) előkészítéséhez:

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Az Azure Automation-parancsmagok használatával

Ha a PowerShell DSC helyi Configuration Manager alapértelmezett felel meg a használati eset, és előkészítheti a gépeket, hogy azok is kérje le, és az Azure Automation DSC jelenteni szeretné, az Azure Automation parancsmagok adjon meg egy egyszerűsített módszer a DSC generálása metaconfigurations szükséges:

1. Nyissa meg a helyi környezetben gépen a PowerShell-konzol vagy a PowerShell ISE-t rendszergazdaként.
2. Csatlakozás az Azure Resource Manager használatával **Connect-AzureRmAccount**
3. Töltse le a PowerShell DSC metaconfigurations a gépek szeretné üzembe helyezni az Automation-fiók, amelyhez szeretné előkészíteni csomópontok:

    ```powershell
    # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
    $Params = @{

        ResourceGroupName = 'ContosoResources'; # The name of the ARM Resource Group that contains your Azure Automation Account
        AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
        ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
        OutputFolder = "$env:UserProfile\Desktop\";
    }
    # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    Get-AzureRmAutomationDscOnboardingMetaconfig @Params
    ```
    
4. Most már olyan nevű mappát ***DscMetaConfigs***, a gépek a PowerShell DSC metaconfigurations tartalmazó vezethető (rendszergazdaként):
    
    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Biztonságos regisztráció

Gépek segítségével biztonságosan készítheti elő az Azure Automation-fiók, a WMF 5 DSC regisztrációs protokoll, amely lehetővé teszi, hogy a DSC-csomópont hitelesítése egy PowerShell DSC lekéréses vagy a jelentéskészítő kiszolgálóra (beleértve az Azure Automation DSC) keresztül. A kiszolgáló regisztrálása a csomópont egy **regisztrációs URL-cím**, és ehhez használatával egy **Szolgáltatásregisztrációs kulcs**. Regisztráció, a DSC-csomópont és a DSC lekéréses/Reporting server során egy egyedi tanúsítványt ehhez a csomóponthoz, használnak a hitelesítéshez a regisztrációt követő kiszolgáló az egyeztetés. Ez a folyamat megakadályozza, hogy előkészítve csomópontok megszemélyesítésekor egy másikra, például ha egy csomópont biztonsága sérült, és ártó szándékkal viselkedik. A regisztrációt követően a regisztrációs kulcs nem használatos újból a hitelesítést, és törlődik a csomópontból.

A szükséges a DSC-regisztrációs protokoll használatával információkat szerezhet a **kulcsok** alatt **fiókbeállításokat** az Azure Portalon. A kulcs ikonra kattintva nyissa meg a ezen a panelen a **Essentials** az Automation-fiókhoz tartozó panel.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

* Regisztrációs URL-címe, az URL-cím mezőben, a kulcsok kezelése panelen.
* Szolgáltatásregisztrációs kulcs az elsődleges hozzáférési kulcs vagy a másodlagos hozzáférési kulcs a kulcsok kezelése panelt. Egyiket sem használható.

A fokozott biztonság érdekében bármikor helyreállíthatja az Automation-fiók elsődleges és másodlagos hozzáférési kulcsait (a a **kulcsok kezelése** panel), hogy a jövőbeli csomópont regisztrációk korábbi kulcsok használatával.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Hibaelhárítás az Azure virtuális gép előkészítése

Az Azure Automation DSC segítségével egyszerűen az Azure Windows virtuális gépek előkészítése konfigurációkezelés. Technikai részletek az Azure virtuális gépek Desired State Configuration bővítmény segítségével regisztrálja a virtuális Gépet az Azure Automation DSC. Mivel az Azure virtuális gépek Desired State Configuration bővítmény aszinkron módon fut, a folyamat előrehaladását nyomon követése, és a végrehajtása hibaelhárítási fontos lehet.

> [!NOTE]
> Bármely metódusát meghívná, bevezetés az Azure Automation DSC az Azure virtuális gépek Desired State Configuration bővítmény használó Azure Windows virtuális gép is igénybe vehet egy órát a csomópont megjelenítése akár regisztrálva az Azure Automationben. Ez a Windows Management Framework 5.0 által az Azure VM DSC bővítmény esetében szükséges, hogy a virtuális gép telepítése miatt a virtuális gép az Azure Automation DSC.

Hibaelhárítás, vagy megtekintheti az állapotát az Azure virtuális gépek Desired State Configuration bővítmény, az Azure Portalon lépjen a virtuális gép típusától függenek, majd kattintson **bővítmények** alatt **beállítások**. Kattintson a **DSC** vagy **DSCForLinux** operációs rendszertől függően. További részletekért kattintson **részletes állapotadatok megtekintése**.

## <a name="certificate-expiration-and-reregistration"></a>Tanúsítvány lejárati és ismételt

Egy gépre a DSC-csomópontot az Azure Automation DSC regisztrálás után a rendszer számos oka lehet, hogy miért regisztrálja újra a csomóponton a jövőben kell:

* A regisztrálás után minden csomóponton automatikusan végrehajtja a hitelesítést, amely egy év után lejár egy egyedi tanúsítványt. Jelenleg a PowerShell DSC regisztrációs protokoll nem tudja automatikusan megújítja a tanúsítványokat, amikor azok lejárnak, így újból regisztrálnunk a csomópontok egy év időpont után. Mielőtt újraregisztrálása, ellenőrizze, hogy minden csomóponton fut Windows Management Framework 5.0 RTM-re. Ha egy csomópont-hitelesítési tanúsítvány lejár, és a csomópont a rendszer nem újra regisztrálja, a csomópont nem tud kommunikálni az Azure Automation, és meg van jelölve "Unresponsive." Ismételt végrehajtott 90 napig, vagy kisebb a tanúsítvány lejárati időt, vagy a tanúsítvány lejárati idő után bármikor alatt létrehozható és használható új tanúsítványt fog eredményezni.
* Módosíthatja [PowerShell DSC helyi Configuration Manager értékek](https://msdn.microsoft.com/powershell/dsc/metaconfig4) , hogy a csomópont, például a ConfigurationMode kezdeti regisztráció során beállított. Jelenleg ezen DSC-ügynök értékek csak módosítható ismételt keresztül. Az egyetlen kivétel rendelve a csomóponthoz csomópont-konfiguráció – ez módosítható az Azure Automation DSC közvetlenül.

Ismételt regisztrálta a csomópontot először, a jelen dokumentumban ismertetett regisztrációs módszerek bármelyikével megegyező módon hajtható végre. Nem kell az Azure Automation DSC-csomópont regisztrációjának újraregisztrálása azt megelőzően.

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Az Azure Automation DSC áttekintése](automation-dsc-overview.md)
* [Azure Automation DSC-parancsmagok](/powershell/module/azurerm.automation/#automation)
* [Az Azure Automation DSC díjszabása](https://azure.microsoft.com/pricing/details/automation/)
