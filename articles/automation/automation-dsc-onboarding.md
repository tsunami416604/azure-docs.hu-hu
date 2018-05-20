---
title: Azure Automation DSC általi kezelésre bevezetési gépek
description: Tudnivalók az Azure Automation DSC Szolgáltatásban felügyeleti gépek telepítéséről
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4493f9da0de12fbdfffdf0f4da0dd581ac3b589f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="onboarding-machines-for-management-by-azure-automation-dsc"></a>Azure Automation DSC általi kezelésre bevezetési gépek

## <a name="why-manage-machines-with-azure-automation-dsc"></a>Miért kezelése az Azure Automation DSC Szolgáltatásban gépek?

Például [PowerShell célállapot-konfiguráció](https://technet.microsoft.com/library/dn249912.aspx), Azure Automation célállapot-konfiguráció egy olyan egyszerű, mégis hatékony, konfigurációs felügyeleti szolgáltatás a DSC-csomópontok (fizikai és virtuális gépek) számára a felhőalapú vagy helyszíni adatközpontban. Lehetővé teszi méretezhetőség több ezer gép között gyors és egyszerű központi, biztonságos helyről. Könnyen előkészítésére gépek is előfordulhatnak, rendelje hozzá őket deklaratív konfigurációk és nézet jelentéseken mindegyik számítógép a kívánt állapot megadott való megfelelés. Az Azure Automation DSC alkalmazáskezelési réteg hoz DSC van az Azure Automation felügyeleti réteg PowerShell-parancsprogramok. Ez azt jelenti azonos módon, hogy az Azure Automation segítségével felügyelheti a PowerShell-parancsfájlok, azt is kezelheti a DSC-konfigurációk. Azure Automation DSC használatának előnyei kapcsolatos további információkért lásd: [Azure Automation DSC – áttekintés](automation-dsc-overview.md).

Az Azure Automation DSC gépek számos kezelésére használható:

* Az Azure virtuális gépek (klasszikus)
* Azure virtuális gépek
* Amazon Web Services (AWS) virtuális gépek
* A Windows fizikai vagy virtuális gépek a helyszíni vagy felhőben működő Azure/AWS eltérő
* Fizikai vagy virtuális Linux számítógépek, a helyszíni, az Azure-ban, vagy eltérő Azure felhőben

Emellett ha nem készen áll a számítógép-konfiguráció kezelése a felhőből, Azure Automation DSC is használható csak a jelentés-végpontként. Ez lehetővé teszi (leküldéses) szükségeskonfiguráció keresztül DSC a helyszíni és a kívánt állapot az Azure Automationben csomópont való megfelelés gazdag jelentéskészítési adatainak megtekintése.

> [!NOTE]
> Azure virtuális gépek DSC kezelése része külön díjfizetés nélkül Ha a virtuális gépek DSC-kiterjesztés telepítése nagyobb 2.70. bekezdés. Tekintse meg a [ **árképzést ismertető oldalra Automation** ](https://azure.microsoft.com/pricing/details/automation/) további részleteket.


Az alábbi szakaszok felsorolják, hogyan zajlik bevezetésében Azure Automation DSC gép különböző típusú.

## <a name="azure-virtual-machines-classic"></a>Az Azure virtuális gépek (klasszikus)

Az Azure Automation DSC Szolgáltatásban könnyen bevezetni az Azure virtuális gépek (klasszikus) konfigurációs Management az Azure-portálon, vagy a PowerShell használatával is. A technikai részletek alatt, és anélkül, hogy a rendszergazda nem kell a virtuális gép be távolról az Azure virtuális gép célállapot-konfiguráció bővítményt a virtuális gép Azure Automation DSC regisztrálja. Mivel az Azure virtuális gép célállapot-konfiguráció bővítmény aszinkron módon fut, nyomon követheti a telepítés előrehaladását, valamint a hibaelhárítás érdekében szerepelnek-e a következő [ **hibaelhárítási Azure virtuális gép bevezetési** ](#troubleshooting-azure-virtual-machine-onboarding) szakasz.

### <a name="azure-portal"></a>Azure Portal

Az a [Azure-portálon](http://portal.azure.com/), kattintson a **Tallózás** -> **virtuális gépek (klasszikus)**. Válassza ki a Windows virtuális szeretné előkészítésére. A virtuális gép irányítópult paneljén kattintson **összes beállítás** -> **bővítmények** -> **Hozzáadás** -> **Azure Automation DSC** -> **létrehozása**. Adja meg a [PowerShell DSC helyi Configuration Manager értékek](https://msdn.microsoft.com/powershell/dsc/metaconfig4) hozzárendelése a virtuális gép a használati eset, az Automation-fiók regisztrációs kulcsot és regisztrációs URL-címet, és nem kötelező a csomópont-konfiguráció szükséges.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

A regisztrációs URL-Címének megkeresése, és a gép, tekintse meg a következőt az Automation-fiókhoz való előkészítésére kulcs [ **regisztrációs biztonságos** ](#secure-registration) szakasz:

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
> A DSC-Csomópontkonfigurációnak nevek különbözőnek számítanak a kis a portálon. Ha a helyzet nem megfelelő a csomópont nem jelennek meg a DSC-csomópont alatt.

## <a name="azure-virtual-machines"></a>Azure virtuális gépek

Az Azure Automation DSC lehetővé teszi a konfigurációkezelésre, könnyen bevezetni az Azure virtuális gépek az Azure portál, Azure Resource Manager-sablonok, vagy a PowerShell használatával. A technikai részletek alatt, és anélkül, hogy a rendszergazda nem kell a virtuális gép be távolról az Azure virtuális gép célállapot-konfiguráció bővítményt a virtuális gép Azure Automation DSC regisztrálja. Mivel az Azure virtuális gép célállapot-konfiguráció bővítmény aszinkron módon fut, nyomon követheti a telepítés előrehaladását, valamint a hibaelhárítás érdekében szerepelnek-e a következő [ **hibaelhárítási Azure virtuális gép bevezetési** ](#troubleshooting-azure-virtual-machine-onboarding) szakasz.

### <a name="azure-portal"></a>Azure Portal

Az a [Azure-portálon](https://portal.azure.com/), keresse meg a helyét a bevezetni virtuális gépek Azure Automation-fiók. Az Automation-fiók irányítópultján kattintson **DSC-csomópontok** -> **+ Azure virtuális gép hozzáadása**.

Válassza ki a bevezetni az Azure virtuális gép.

Ha a számítógép nem rendelkezik a PowerShell kívánt állapot bővítmények vannak telepítve, és kikapcsolt állapotban fut-e, kattintson az **Connect**.

A **regisztrációs**, adja meg a [PowerShell DSC helyi Configuration Manager értékek](https://msdn.microsoft.com/powershell/dsc/metaconfig4) hozzárendelése a virtuális gép a használati eset, és szükség esetén a csomópont-konfiguráció szükséges.

![](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Azure virtuális gépeken is telepíthető és az Azure Resource Manager-sablonok segítségével az Azure Automation DSC előkészítve. Lásd: [konfigurálja a virtuális gépről a DSC-bővítményt és Azure Automation DSC](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) egy példa sablon, hogy egy meglévő virtuális gép Azure Automation DSC onboards. A regisztrációs kulcs és a regisztrációs URL-cím szükséges bemeneti sablonban szereplő, tekintse meg a következőt [ **regisztrációs biztonságos** ](#secure-registration) szakasz.

### <a name="powershell"></a>PowerShell

A [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) előkészítésére virtuális gépek Azure-portálon keresztül PowerShell parancsmag használható.

## <a name="amazon-web-services-aws-virtual-machines"></a>Amazon Web Services (AWS) virtuális gépek

Azure Automation DSC az AWS DSC eszközkészlet segítségével által konfigurációkezelés könnyen előkészítésére Amazon Web Services virtuális gépek is. További tudnivalók az eszközkészlet [Itt](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>A Windows fizikai vagy virtuális gépek a helyszíni vagy felhőben működő Azure/AWS eltérő

A helyi Windows-alapú gépek és a Windows-alapú gépek az-Azure felhők (például az Amazon Web Services) is lehet előkészítve az Azure Automation DSC, mindaddig, amíg hozzáférhetnek kimenő internet-néhány egyszerű lépésben:

1. Győződjön meg arról, hogy a legújabb [WMF 5](http://aka.ms/wmf5latest) az Azure Automation DSC a bevezetni kívánt számítógépeken telepítve van.
2. A következő szakasz útmutatásait [ **generálása DSC metaconfigurations** ](#generating-dsc-metaconfigurations) egy mappát, amely tartalmazza a szükséges DSC metaconfigurations létrehozásához.
3. A PowerShell DSC metakonfigurációját távolról vonatkoznak a bevezetni kívánt gépek. **Ez a parancs fut a gépen kell rendelkeznie a legújabb [WMF 5](http://aka.ms/wmf5latest) telepített**:

    ```powershell
    Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
    ```

4. Ha a PowerShell DSC metaconfigurations távolról nem alkalmaz, másolja a metaconfigurations mappát a 2. lépésben, minden gép előkészítésére. Majd meghívják a **Set-DscLocalConfigurationManager** helyileg az egyes gépek érheti.
5. Az Azure portál vagy parancsmagok használatával, ellenőrizze, hogy a gépek érheti megjelennek-e regisztrálva az Azure Automation-fiók a DSC-csomópontként.

## <a name="physicalvirtual-linux-machines-on-premises-in-azure-or-in-a-cloud-other-than-azure"></a>Fizikai vagy virtuális Linux számítógépek, a helyszíni, az Azure-ban, vagy eltérő Azure felhőben

A helyszíni Linux-gépek, a Linux-gépek Azure-ban és a Linux-gépek-Azure felhőben is lehet előkészítve az Azure Automation DSC, mindaddig, amíg hozzáférhetnek kimenő internet-néhány egyszerű lépésben:

1. Győződjön meg arról, hogy a legújabb [PowerShell célállapot-konfiguráció Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) az Azure Automation DSC a bevezetni kívánt számítógépeken telepítve van.
2. Ha a [PowerShell DSC helyi Configuration Manager alapértelmezett](https://msdn.microsoft.com/powershell/dsc/metaconfig4) felel meg a használati eset, és bevezetésében számítógépek, például szeretné, hogy azok **mindkét** való lekérésére és Azure Automation DSC jelentést:

   + Minden egyes Linux gépen érheti el az Azure Automation DSC segítségével Register.py bevezetni a PowerShell DSC helyi Configuration Manager alapértelmezett beállításaival:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   + A regisztrációs kulcs és a regisztrációs URL-cím keresése az Automation-fiók, tekintse meg a következő [ **regisztrációs biztonságos** ](#secure-registration) szakasz.

     Ha a PowerShell DSC helyi Configuration Manager alapértelmezett **tegye** **nem** egyezés a használati eset, vagy a bevezetni kívánt gépek úgy, hogy az Azure Automation DSC, csak jelentendő, de nincs lekéréses konfigurációja vagy a PowerShell-modulok tegye azt, hajtsa végre a 3-6. lépéseket. Egyéb esetben folytassa a 6.

3. Kövesse az utasításokat az alábbi [ **generálása DSC metaconfigurations** ](#generating-dsc-metaconfigurations) létrehozni a mappát, amely tartalmazza a szükséges DSC metaconfigurations szakasz.
4. A PowerShell DSC metakonfigurációját távolról vonatkoznak a bevezetni kívánt gépek:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard

    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Ez a parancs fut a gépen kell rendelkeznie a legújabb [WMF 5](http://aka.ms/wmf5latest) telepítve.

1. Ha a PowerShell DSC metaconfigurations távolról, nem alkalmazhatók minden Linux gép bevezetésében, másolja az 5. lépésben a Linux-gép alakzatot mappából, hogy a gép megfelelő metakonfigurációját. Majd meghívják a `SetDscLocalConfigurationManager.py` helyileg minden Linux rendszerű számítógépen a bevezetni kívánt Azure Automation DSC:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Az Azure portál vagy parancsmagok használatával, ellenőrizze, hogy a gépek érheti megjelennek-e regisztrálva az Azure Automation-fiók a DSC-csomópontként.

## <a name="generating-dsc-metaconfigurations"></a>A DSC metaconfigurations létrehozása

Általános érheti minden gép Azure Automation DSC szolgáltatásra, egy [DSC metakonfigurációját](https://msdn.microsoft.com/powershell/dsc/metaconfig) lehet létre, amelyek, alkalmazása esetén a DSC-ügynök tájékoztatja a lekéréses és/vagy az Azure Automation DSC jelentse a számítógépen. Az Azure Automation DSC DSC metaconfigurations a PowerShell DSC-konfiguráció, vagy az Azure Automation PowerShell-parancsmagok használatával hozhatók létre.

> [!NOTE]
> DSC metaconfigurations tartalmazza a titkos kulcsok szükséges és bevezetni a gép az Automation-fiók Management. Ügyeljen arra, hogy megfelelően védhetné a DSC-metaconfigurations hoz létre, vagy után törölje őket.

### <a name="using-a-dsc-configuration"></a>A DSC-konfiguráció használata

1. Nyissa meg rendszergazdaként a PowerShell ISE a helyi környezet valamelyik számítógépén. A gépnek rendelkeznie kell a legújabb [WMF 5](http://aka.ms/wmf5latest) telepítve.
2. Másolja a következő helyileg. Ezt a parancsfájlt a PowerShell DSC-konfiguráció létrehozásához metaconfigurations, és indítsa a metakonfigurációját létrehozása parancsot tartalmazza.

> [!NOTE]
> A DSC-Csomópontkonfigurációnak nevek különbözőnek számítanak a kis a portálon. Ha a helyzet nem megfelelő a csomópont nem jelennek meg a DSC-csomópont alatt.

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

3. Adja meg a regisztrációs kulcs és az URL-cím az Automation-fiók, valamint a gépeket előkészítésére nevét. Más paraméterek opcionálisak. A regisztrációs kulcs és a regisztrációs URL-cím keresése az Automation-fiók, tekintse meg a következő [ **regisztrációs biztonságos** ](#secure-registration) szakasz.
4. Ha azt szeretné, hogy a gépek DSC állapotinformáció jelentésének Azure Automation DSC, de a nem lekéréses konfigurációját vagy a PowerShell-modulok, állítsa be a **ReportOnly** paraméter igaz értékű.
5. Futtassa a szkriptet. Most már rendelkeznie kell egy nevű mappát **DscMetaConfigs** a munkakönyvtárba, a PowerShell DSC metaconfigurations gépek tartalmazó bevezetni (rendszergazdaként):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Az Azure Automation parancsmagokkal

Ha a PowerShell DSC helyi Configuration Manager alapértelmezett felel meg a használati eset, és szeretne bevezetni gépek úgy, hogy azok mind a lekéréses, és Azure Automation DSC jelentést, az Azure Automation parancsmagokkal jelentésmappái egyszerűsített generálására a DSC-metaconfigurations szükséges:

1. A PowerShell-konzolt vagy a PowerShell ISE rendszergazdaként nyissa meg a helyi környezet valamelyik számítógépén.
2. Csatlakozás az Azure Resource Manager használatával **Connect-AzureRmAccount**
3. Töltse le a PowerShell DSC metaconfigurations kívánt gépek érheti az Automation-fiók, amelyhez szeretné bevezetni csomópontok:

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
    
4. Most már rendelkeznie kell egy nevű mappát ***DscMetaConfigs***, a PowerShell DSC metaconfigurations gépek tartalmazó bevezetni (rendszergazdaként):
    
    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Biztonságos regisztrációs

Gépek discoveryt biztonságosan számára egy Azure Automation-fiók a WMF 5 DSC regisztrációs protokoll, amely lehetővé teszi, hogy a DSC-csomópont hitelesítése a PowerShell DSC lekéréses vagy a jelentéskészítő kiszolgálóra (beleértve az Azure Automation DSC) keresztül. A kiszolgáló regisztrálása a csomópont egy **regisztrációs URL-cím**, hitelesítő használatával egy **regisztrációs kulcs**. Alatt a regisztrációt a DSC-csomópont és DSC lekérési/jelentéskészítési kiszolgáló egyezteti erről a csomópontról használnak a hitelesítéshez a regisztrációt követő kiszolgáló egyedi tanúsítványt. Ez a folyamat megakadályozza, hogy a előkészítve csomópontok megszemélyesít egy másikra, például ha egy csomópont biztonsága sérül, és rosszindulatúan viselkedik. A regisztrációt követően a regisztrációs kulcs nem újra-hitelesítéshez használt, és a csomópont törlődik.

A DSC-regisztrációs protokoll a szükséges adatokat kaphat **kulcsok** alatt **Fiókbeállítások** az Azure portálon. Nyissa meg ezt a panelt a kulcs ikonra kattintva a **Essentials** az Automation-fiók panelen.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

* Regisztrációs URL-címe: az URL-cím mező a kulcsok kezelése paneljén.
* Regisztrációs kulcs az az elsődleges elérési kulcsot vagy a másodlagos elérési kulcsot a kulcsok kezelése paneljén. Vagy a kulcs használható.

A nagyobb biztonság érdekében az Automation-fiók elsődleges és másodlagos hívóbetűk bármikor helyreállíthatja (a a **kulcsok kezelése** panel) előző kulcsok későbbi csomópont regisztrációk megelőzése érdekében.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Hibaelhárítás az Azure virtuális gép bevezetése

Az Azure Automation DSC könnyen előkészítésére Azure Windows virtuális gépek a konfigurációkezelésre lehetővé teszi. A technikai részletek alatt a Azure VM célállapot-konfiguráció kiterjesztést használja a virtuális Gépet az Azure Automation DSC Szolgáltatásban regisztrálni. Mivel az Azure virtuális gép célállapot-konfiguráció bővítmény aszinkron módon futtatja, figyelemmel kíséri, a telepítés előrehaladását, és a végrehajtása hibaelhárítási fontos lehet.

> [!NOTE]
> A Bevezetés az Azure Windows virtuális gép Azure Automation DSC szolgáltatásra, az Azure virtuális gép célállapot-konfiguráció bővítmény használó bármely metódusát egy órát a csomópont regisztrálva az Azure Automation legfeljebb megjelenítendő eltarthat. Ez az miatt a virtuális gép által a Azure VM DSC-bővítményt, amely pedig szükséges érheti Windows Management Framework 5.0 telepítése a virtuális gép Azure Automation DSC szolgáltatásra.

Hibaelhárítását, és tekintse meg az Azure virtuális gép célállapot-konfiguráció bővítmény állapotát, az Azure portálon keresse meg a virtuális gép előkészítve alatt, majd kattintson **bővítmények** alatt **beállítások**. Kattintson a **DSC** vagy **DSCForLinux** operációs rendszertől függően. További részletekért kattintson **az állapot**.

## <a name="certificate-expiration-and-reregistration"></a>Tanúsítványok és ismételt

A gépek DSC-csomópontként az Azure Automation DSC a regisztrálás után a rendszer számos oka lehet, hogy miért regisztrálja újra a jövőben csomópontot kell:

* A regisztrálás után minden csomóponton automatikusan egyezteti egyedi tanúsítványt a hitelesítéshez, amely egy év után lejár. Jelenleg a PowerShell DSC regisztrációs protokoll nem tudja automatikusan megújítani a tanúsítványokat, amikor azok hamarosan lejáró, ezért a csomópontok egy év idő után ismét meg kell. Előtt újraregisztrálása, ellenőrizze, hogy mindegyik csomópontján fut a Windows Management Framework 5.0 RTM-re. Ha a csomópont-hitelesítési tanúsítvány lejár, és a csomópontot a rendszer nem újra regisztrálja, a csomópont nem lehet kommunikálni az Azure Automation-e, és jelölése "Unresponsive." Ismételt 90 naponta végrehajtott vagy kisebb a tanúsítvány lejárati ideje, vagy a tanúsítvány lejárati ideje után bármikor egy új tanúsítványt generált és a használt eredményez.
* Módosíthatja a [PowerShell DSC helyi Configuration Manager értékek](https://msdn.microsoft.com/powershell/dsc/metaconfig4) , hogy a csomópont, például a ConfigurationMode kezdeti regisztráció során lettek beállítva. Jelenleg a DSC-ügynök értékeiről csak módosítható újraregisztrálni keresztül. Az egyetlen kivétel a csomópont-konfigurációnak a csomópont rendelt – ez módosítható az Azure Automation DSC közvetlenül.

Ismételt regisztrálta a csomópont kezdetben a bevezetési módszereket a jelen dokumentumban ismertetett azonos módon hajtható végre. Nem kell egy Azure Automation DSC csomópont regisztrációját előtt újraregisztrálása azt.

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Azure Automation DSC – áttekintés](automation-dsc-overview.md)
* [Azure Automation DSC-parancsmagokkal](/powershell/module/azurerm.automation/#automation)
* [Azure Automation DSC – díjszabás](https://azure.microsoft.com/pricing/details/automation/)
