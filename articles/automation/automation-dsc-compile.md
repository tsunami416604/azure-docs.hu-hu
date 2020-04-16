---
title: DSC-konfigurációk fordítása az Azure Automation állapotkonfigurációjában
description: Ez a cikk ismerteti, hogyan kell lefordítani a kívánt állapotkonfigurációs (DSC) konfigurációk az Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: f7558745442ac26fc33a063ff66fe170d08487ac
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392091"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>DSC-konfigurációk fordítása az Azure Automation állapotkonfigurációjában

A kívánt állapotkonfigurációs (DSC) konfigurációkat az Azure Automation State Configuration alkalmazásban a következő módokon állíthatja le:

- Azure állapotkonfigurációs fordítási szolgáltatás
  - Kezdő módszer interaktív felhasználói felülettel
   - Egyszerűen nyomon követheti a feladat állapotát

- Windows PowerShell
  - Hívás a Windows PowerShellből helyi munkaállomáson vagy buildszolgáltatáson
  - Integrálás fejlesztési tesztfolyamattal
  - Összetett paraméterértékek megadása
  - Csomópont- és nem csomópontadatok nagy méretekben történő méretezése
  - Jelentős teljesítményjavulás

Az Azure Resource Manager-sablonok at Azure Desired State Configuration (DSC) bővítmény használatával is leküldéses konfigurációk az Azure virtuális gépek. Az Azure DSC-bővítmény az Azure VM Agent keretrendszer használatával biztosítja, életbe lépteti és jelentést készít az Azure virtuális gépeken futó DSC-konfigurációkról. Az Azure Resource Manager-sablonok használatával történő összeállítás részleteiről a [Kívánt állapotkonfiguráció bővítmény az Azure Resource Manager-sablonokkal.](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details) 

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>DSC-konfiguráció fordítása az Azure állapotkonfigurációjában

### <a name="portal"></a>Portál

1. Az Automation-fiókban kattintson **az Állapotkonfiguráció (DSC)** elemre.
1. Kattintson a **Konfigurációk** fülre, majd a lefordítani hozandó konfigurációnevére.
1. Kattintson **a Fordítás gombra.**
1. Ha a konfigurációnem rendelkezik paramétereket, a rendszer kéri, hogy erősítse meg, ha szeretné lefordítani. Ha a konfiguráció paramétereket tartalmaz, megnyílik a **Fordítási konfiguráció** panel, így paraméterértékeket adhat meg.
1. Megnyílik a Fordítási feladat lap, így nyomon követheti a fordítási feladat állapotát. Ezen a lapon is nyomon követheti az Azure Automation állapotkonfigurációs lekéréses kiszolgálón elhelyezett csomópontkonfigurációkat (MOF konfigurációs dokumentumokat).

### <a name="azure-powershell"></a>Azure PowerShell

A [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) segítségével elkezdheti a windowspowershell nelkul történő összeállítást. A következő mintakód megkezdi a SampleConfig nevű DSC-konfiguráció **összeállítását.**

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob`egy fordítási feladat objektumot ad vissza, amely a feladat állapotának nyomon követésére használható. Ezután használhatja ezt a fordítási feladat objektum [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) állapotának meghatározásához a fordítási feladat, és a [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) az adatfolyamok (kimenet) megtekintéséhez. A következő minta elindítja a SampleConfig konfiguráció összeállítását, megvárja, amíg befejeződik, majd megjeleníti az adatfolyamokat.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Alapvető paraméterek deklarációja

Paraméterdeklaráció A DSC-konfigurációk, beleértve a paramétertípusok és -tulajdonságok, ugyanúgy működik, mint az Azure Automation runbookok. A Runbook-paraméterekről a Runbook-paraméterekről című [témakörben](automation-starting-a-runbook.md) olvashat bővebben.

A következő `FeatureName` példa `IsPresent` a **ParametersExample.sample** csomópont konfigurációjában a tulajdonságok értékeinek meghatározására és paraméterek meghatározására használja a fordítás során létrehozott értékeket.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Lefordíthatja azokat a DSC-konfigurációkat, amelyek alapvető paramétereket használnak az Azure Automation állapotkonfigurációs portálon vagy az Azure PowerShell használatával.

#### <a name="portal"></a>Portál

A portálon a Fordítás gombra kattintva **megadhatja**a paraméterértékeket.

![Konfigurációfordítási paraméterek](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

A PowerShell paramétereket igényel egy [kivonattáblában,](/powershell/module/microsoft.powershell.core/about/about_hash_tables)ahol a kulcs megegyezik a paraméter nevével, és az érték megegyezik a paraméter értékértékkel.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Az objektumok `PSCredential` paraméterként való átadásáról a [Hitelesítő adatok című](#credential-assets)témakörben talál további információt.

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Összetett erőforrásokat tartalmazó konfigurációk fordítása az Azure Automationben

**Az Összetett erőforrások** szolgáltatás lehetővé teszi, hogy a DSC-konfigurációkat beágyazott erőforrásként használja egy konfiguráción belül. Ez a szolgáltatás lehetővé teszi több konfiguráció egyetlen erőforrásra történő alkalmazását. Lásd: [Összetett erőforrások: A DSC-konfiguráció használata erőforrásként,](/powershell/scripting/dsc/resources/authoringresourcecomposite) hogy többet tudjon meg az összetett erőforrásokról.

> [!NOTE]
> Annak érdekében, hogy az összetett erőforrásokat tartalmazó konfigurációk megfelelően fordítsanak fordítást, először importálnia kell az Azure Automationbe azokat a DSC-erőforrásokat, amelyekre a kompozitok támaszkodnak. DSC-összetett erőforrás hozzáadása nem különbözik a PowerShell-modulok Azure Automationhez való hozzáadásától. Ez a folyamat dokumentálva van az [Azure Automation moduljainak kezelése mezőben.](/azure/automation/shared-resources/modules)

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>ConfigurationData kezelése konfigurációk összeállításakor az Azure Automationben

`ConfigurationData`egy beépített DSC-paraméter, amely lehetővé teszi a szerkezeti konfiguráció elkülönítése bármely környezetspecifikus konfigurációtól a PowerShell DSC használata közben. További információ: ["Mi" és "Hol" a PowerShell DSC-ben.](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/)

> [!NOTE]
> Az Azure Automation-állapot konfigurációja összeállításakor használhatja `ConfigurationData` az Azure PowerShellben, de az Azure Portalon nem.

A következő példa DSC `$ConfigurationData` konfiguráció `$AllNodes` segítségével `ConfigurationData` a és a kulcsszavakat. Ehhez a példához az [xWebAdministration modulra](https://www.powershellgallery.com/packages/xWebAdministration/) is szüksége van.

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

Az előző DSC-konfiguráció t a Windows PowerShell segítségével fordíthatja le. A következő parancsfájl két csomópontkonfigurációt ad hozzá az Azure Automation state configuration lekéréses szolgáltatáshoz: **ConfigurationDataSample.MyVM1** és **ConfigurationDataSample.MyVM3.**

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Eszközök használata az Azure Automationben összeállítás közben

Az eszközhivatkozások megegyeznek az Azure Automation állapotkonfigurációjában és a runbookokban. További információkat a következő cikkekben talál:

- [Tanúsítványok](automation-certificates.md)
- [Kapcsolatok](automation-connections.md)
- [Hitelesítő adatok](automation-credentials.md)
- [Változók](automation-variables.md)

#### <a name="credential-assets"></a>Hitelesítő adatok eszközei

Az Azure Automation DSC-konfigurációi a `Get-AutomationPSCredential` parancsmag használatával hivatkozhatnak az Automation hitelesítő adatokra. Ha egy konfiguráció rendelkezik egy `PSCredential` olyan `Get-AutomationPSCredential` paraméterrel, amely megad egy objektumot, használja egy Azure Automation-hitelesítő adateszköz karakterláncnevének a parancsmagba történő megadásával a hitelesítő adatok lekéréséhez. Ezután használja ezt az objektumot `PSCredential` az objektumot igénylő paraméterhez. A színfalak mögött az Azure Automation hitelesítő adatok at, hogy a név lekérésre kerül, és átadta a konfigurációnak. Az alábbi példa bemutatja ezt a forgatókönyvet működés közben.

A hitelesítő adatok biztonságának megőrzése csomópontkonfigurációkban (MOF konfigurációs dokumentumok) a hitelesítő adatok titkosítását igényli a csomópont konfigurációs MOF-fájljában. Jelenleg meg kell adnia a PowerShell DSC engedélyt kimeneti hitelesítő adatok egyszerű szövegben csomópont konfigurációja mof generálása során. A PowerShell DSC nincs tudatában annak, hogy az Azure Automation egy fordítási feladaton keresztül titkosítja a teljes MOF-fájlt a létrehozás után.

Megmondhatja a PowerShell DSC-nek, hogy a hitelesítő adatok egyszerű szövegben történő kimenetele a konfigurációs adatok használatával a létrehozott csomópontkonfigurációs MIF-ek ben történik. Át kell `PSDscAllowPlainTextPassword = $true` `ConfigurationData` adnia a DSC-konfigurációban megjelenő és hitelesítő adatokat használó csomópontblokkneveket.

A következő példa egy DSC-konfigurációt mutat be, amely egy Automation hitelesítő adatokat használ.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

Az előző DSC-konfiguráció t a PowerShell segítségével lefordíthatja. A következő PowerShell-kód két csomópontkonfigurációt ad hozzá az Azure Automation állapotkonfigurációs lekéréses kiszolgálóhoz: **CredentialSample.MyVM1** és **CredentialSample.MyVM2.**

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Amikor a fordítás befejeződött, a hibaüzenet `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` et nyugodtan figyelmen kívül hagyhatja.

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>A DSC-konfiguráció fordítása a Windows PowerShellben

Az Azure-on kívül lefordított csomópontkonfigurációkat (MOF-fájlokat) is importálhat. Az importálás magában foglalja a fejlesztői munkaállomásról vagy egy szolgáltatásból, például az [Azure DevOps-ból](https://dev.azure.com)történő összeállítást. Ennek a megközelítésnek több előnye van, beleértve a teljesítményt és a megbízhatóságot.

A Windows PowerShell ben való fordítás lehetővé teszi a konfigurációs tartalom aláírását is. A DSC-ügynök helyileg ellenőrzi az aláírt csomópontkonfigurációt egy felügyelt csomóponton. Az ellenőrzés biztosítja, hogy a csomópontra alkalmazott konfiguráció egy engedélyezett forrásból származik.

> [!NOTE]
> A csomópont konfigurációs fájl nem lehet nagyobb, mint 1 MB, hogy az Azure Automation importálni.

A csomópontkonfigurációk aláírásáról a [WMF 5.1 fejlesztései – A konfiguráció és](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)a modul aláírása című témakörben talál további információt.

### <a name="compile-the-dsc-configuration"></a>A DSC-konfiguráció fordítása

A DSC-konfigurációk Windows PowerShellben való fordításának folyamata megtalálható a PowerShell DSC [dokumentációjában, a Konfiguráció írása, fordítása és alkalmazása című dokumentációban.](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)
Ezt a folyamatot fejlesztői munkaállomásról vagy egy buildszolgáltatáson, például az [Azure DevOps-on](https://dev.azure.com)belül is végrehajthatja. Ezután importálhatja a mof-fájlokat a konfiguráció az Azure State Configuration szolgáltatás fordításával.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Csomópont-konfiguráció importálása az Azure Portalon

1. Az Automation-fiókban kattintson **az Állapotkonfiguráció (DSC)** elemre a **Konfigurációkezelés csoportban.**
1. Az Állapotkonfiguráció (DSC) lapon kattintson a **Konfigurációk** fülre, majd a **Hozzáadás**gombra.
1. Az Importálás lapon kattintson a **Csomópontkonfigurációs fájl** mező melletti mappaikonra, ha a helyi számítógépen meg szeretne keresni egy csomópontkonfigurációs MOF-fájlt.

   ![Helyi fájl tallózása](./media/automation-dsc-compile/import-browse.png)

1. Írjon be egy nevet a **Konfiguráció neve** mezőbe. Ennek a névnek meg kell egyeznie annak a konfigurációnak a nevével, amelyből a csomópont-konfigurációt lefordították.
1. Kattintson az **OK** gombra.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Csomópont-konfiguráció importálása az Azure PowerShell használatával

[Az Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) parancsmag használatával csomópontkonfigurációt importálhat az Automation-fiókba.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>További lépések

- Első lépések: [Az Azure Automation állapotkonfigurációjának első lépései.](automation-dsc-getting-started.md)
- A DSC-konfigurációk összeállításáról a célcsomópontokhoz való hozzárendelésről a [Konfigurációk összeállítása az Azure Automation állapotkonfigurációjában](automation-dsc-compile.md)témakörben olvashat.
- A PowerShell-parancsmag referencia, lásd: [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Díjszabási információkért lásd: [Azure Automation state configuration fortaring.](https://azure.microsoft.com/pricing/details/automation/)
- Az Azure Automation State Configuration folyamatos üzembe helyezési folyamatban való használatára példa: [Folyamatos üzembe helyezés az Azure Automation állapotkonfigurációés csokoládés használatával működő virtuális gépeken.](automation-dsc-cd-chocolatey.md)
