---
title: Konfigurációk fordítása Azure Automation állapot konfigurációjában
description: Ez a cikk azt ismerteti, hogyan lehet lefordítani a Azure Automation kívánt állapot-konfiguráció (DSC) konfigurációit.
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: a4bdd28d2ad8f692b561d414af15b90b1609bac4
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462122"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>A DSC-konfigurációk fordítása Azure Automation állapot-konfigurációban

A kívánt állapot-konfiguráció (DSC) konfigurációk kétféleképpen fordíthatók le Azure Automation állapot-konfigurációval: az Azure-ban és a Windows PowerShellben. Az alábbi táblázat segítségével meghatározhatja, hogy mikor kell használni az egyes módszereket a következők jellemzői alapján:

- Azure State Configuration fordítási szolgáltatás
  - Kezdő módszer interaktív felhasználói felülettel
   - A feladatok állapotának egyszerű nyomon követése

- Windows PowerShell
  - Hívás a Windows PowerShellből helyi munkaállomáson vagy Build szolgáltatáson
  - Integrálás fejlesztési tesztelési folyamattal
  - Összetett paraméterek értékeinek megadása
  - Csomópont-és nem csomópontos adatmennyiség használata nagy méretekben
  - Jelentős teljesítmény-javulás

A fordítás részleteiért lásd: [a kívánt állapot konfigurációs bővítménye Azure Resource Manager-sablonokkal](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details).

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>DSC-konfiguráció fordítása az Azure állapot-konfigurációban

### <a name="portal"></a>Portál

1. Az Automation-fiókban kattintson az **állapot konfigurálása (DSC)** elemre.
1. Kattintson a **konfigurációk** fülre, majd a fordításhoz kattintson a konfiguráció nevére.
1. Kattintson a **fordítás**gombra.
1. Ha a konfiguráció nem rendelkezik paraméterekkel, a rendszer felszólítja, hogy erősítse meg, hogy szeretné-e lefordítani. Ha a konfiguráció paraméterekkel rendelkezik, megnyílik a **fordítási konfiguráció** panel, ahol megadhatja a paraméterek értékét.
1. Megnyílik a fordítási feladatok lap, hogy nyomon tudja követni a fordítási feladatok állapotát. Ezen a lapon nyomon követheti azokat a csomópont-konfigurációkat (MOF-konfigurációs dokumentumokat), amelyeket a feladat a Azure Automation állapot-konfiguráció lekérési kiszolgálóján helyez el.

### <a name="azure-powershell"></a>Azure PowerShell

A [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) segítségével megkezdheti a fordítást a Windows PowerShell használatával. Az alábbi mintakód megkezdi a SampleConfig nevű DSC-konfiguráció fordítását.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

A **Start-AzAutomationDscCompilationJob** egy fordítási feladattípust ad vissza, amelyet az állapotának nyomon követésére használhat. Ezt követően ezt a fordítási feladatot a [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) használatával határozhatja meg a fordítási feladatok állapotát, valamint a [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) használatával megtekintheti a streameket (kimenet). Az alábbi mintakód elindítja a SampleConfig-konfiguráció fordítását, megvárja, amíg befejeződik, majd megjeleníti a folyamait.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Alapszintű paraméterek deklarálása

A DSC-konfigurációk paramétereinek deklarációja, beleértve a paramétereket és a tulajdonságokat, ugyanúgy működik, mint a Azure Automation runbookok. A runbook paraméterekkel kapcsolatos további tudnivalókért tekintse meg a [runbook Megkezdése Azure Automationban](automation-starting-a-runbook.md) című témakört.

Az alábbi példa két, **FeatureName** és **IsPresent**nevű paramétert használ a tulajdonságok értékeinek meghatározásához a **ParametersExample.** a fordítás során generált minta csomópont-konfigurációban.

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

Az alapszintű paramétereket használó DSC-konfigurációk fordítása a Azure Automation állapot-konfigurációs portálon vagy a Azure PowerShell használatával:

#### <a name="portal"></a>Portál

A portálon a **fordítás**lehetőségre kattintva adhatja meg a paraméterek értékeit.

![Konfigurációs fordítási paraméterek](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

A PowerShellhez paramétereket kell megadni egy olyan [szórótábla](/powershell/module/microsoft.powershell.core/about/about_hash_tables) , amelyben a kulcs megegyezik a paraméter nevével, és az érték megegyezik a paraméter értékével.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

További információ a PSCredentials paraméterként való átadásáról: az alábbi [hitelesítőadat-eszközök](#credential-assets) .

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Összetett erőforrásokat tartalmazó konfigurációk fordítása Azure Automation

Az **összetett erőforrások** funkció lehetővé teszi, hogy a DSC-konfigurációkat beágyazott erőforrásként használja egy konfiguráción belül. Ez lehetővé teszi, hogy több konfigurációt alkalmazzon egyetlen erőforrásra. Az összetett erőforrásokkal kapcsolatos további információkért tekintse meg az [összetett erőforrások: DSC-konfiguráció használata erőforrásként](/powershell/scripting/dsc/resources/authoringresourcecomposite) című témakört.

> [!NOTE]
> Ahhoz, hogy az összetett erőforrásokat tartalmazó konfigurációk megfelelően legyenek lefordítva, meg kell győződnie arról, hogy az összetett DSC-erőforrások importálása a Azure Automationba történik.

A DSC összetett erőforrások hozzáadása nem különbözik a PowerShell-modulnak a Azure Automationhoz való hozzáadásával. A folyamat a [Azure Automation moduljainak kezelése](/azure/automation/shared-resources/modules)című dokumentumban található.

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>ConfigurationData kezelése a konfigurációk fordításakor Azure Automation

A **ConfigurationData** funkció lehetővé teszi a strukturális konfiguráció elkülönítését bármely környezet-specifikus konfigurációból a PowerShell DSC használata közben. További információ a ConfigurationData-ről: ["mi" a "hol" a POWERSHELL DSC-ben](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) .

> [!NOTE]
> A ConfigurationData a Azure Automation állapot-konfiguráció összeállításakor Azure PowerShell használatával, de nem a Azure Portalban is használhatja.

A következő példában a DSC-konfiguráció a ConfigurationData-t használja a $ConfigurationData és a $AllNodes kulcsszavak használatával. Ehhez a példához a [xWebAdministration modulra](https://www.powershellgallery.com/packages/xWebAdministration/) is szüksége lesz:

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

A korábbi DSC-konfiguráció a Windows PowerShell használatával fordítható le. A következő parancsfájl két csomópont-konfigurációt hoz létre a Azure Automation állapot-konfiguráció lekérési szolgáltatáshoz: ConfigurationDataSample. MyVM1 és ConfigurationDataSample. MyVM3.

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

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Azure Automation-eszközök használata a fordítás során

Az eszközök hivatkozásai a Azure Automation állapot-konfigurációban és a runbookok egyaránt megegyeznek. További információt a következő témakörben talál:

- [Tanúsítványok](automation-certificates.md)
- [Kapcsolatok](automation-connections.md)
- [Hitelesítő adatok](automation-credentials.md)
- [Változók](automation-variables.md)

#### <a name="credential-assets"></a>Hitelesítőadat-eszközök

A Azure Automation DSC-konfigurációk a **Get-AutomationPSCredential** parancsmag használatával hivatkozhatnak az Automation hitelesítőadat-eszközeire. Ha egy konfiguráció egy PSCredential típusú paraméterrel rendelkezik, akkor a **Get-AutomationPSCredential** paranccsal a hitelesítő adatok lekéréséhez adja meg egy Azure Automation hitelesítőadat-eszköz sztring nevét. Ezt az objektumot használhatja a PSCredential objektumot igénylő paraméterhez. A színfalak mögött a rendszer beolvassa a Azure Automation hitelesítő adatokat, és átadja a konfigurációnak. Az alábbi példa a műveletet mutatja be működés közben.

A hitelesítő adatoknak a csomópont-konfigurációban való biztonságossá tételéhez titkosítani kell a hitelesítő adatokat a csomópont-konfiguráció MOF-fájljában. Tájékoztassa a PowerShell DSC-t kifejezetten arról, hogy jogosult a hitelesítő adatok egyszerű szövegként való kiírására a csomópont-konfiguráció MOF-generálásakor. A PowerShell DSC nem tudja, hogy Azure Automation egy fordítási feladatokon keresztül titkosítja a teljes MOF-fájlt.

Ahhoz, hogy a PowerShell DSC engedélyt nyújtson a hitelesítő adatok egyszerű szövegként való kiírására a generált csomópont-konfigurációs MOF a konfigurációs adatok használatával, pass `PSDscAllowPlainTextPassword = $true`. Ezeket az információkat a ConfigurationData-on keresztül adhatja át a DSC-konfigurációban megjelenő összes csomópont-blokk nevével, és hitelesítő adatokat használ.

Az alábbi példa egy olyan DSC-konfigurációt mutat be, amely egy Automation hitelesítőadat-eszközt használ.

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

Az előző DSC-konfigurációt a PowerShell használatával fordíthatja le. 

A következő PowerShell két csomópont-konfigurációt hoz létre a Azure Automation állapot-konfiguráció lekérési kiszolgálóhoz: CredentialSample. MyVM1 és CredentialSample. MyVM2.

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

>[!NOTE]
>A fordítás befejezésekor a következő hibaüzenet jelenhet meg: **a "Microsoft. PowerShell. Management" modul nem lett importálva, mert a "Microsoft. PowerShell. Management" beépülő modul már be lett importálva.** Biztonságosan figyelmen kívül hagyhatja ezt a figyelmeztetést.

## <a name="compiling-a-dsc-configuration-in-windows-powershell"></a>DSC-konfiguráció fordítása a Windows PowerShellben

A DSC-konfigurációk Windows PowerShellben való fordításának folyamata tartalmazza a PowerShell DSC dokumentációjának [írását, fordítását és konfigurációjának alkalmazását](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
A folyamatot egy fejlesztői munkaállomáson vagy egy Build szolgáltatáson belül, például az [Azure DevOps](https://dev.azure.com)lehet végrehajtani.
Ezután importálhatja az MOF az eredményül kapott csomópont-konfigurációkhoz közvetlenül az Azure állapot-konfigurációs szolgáltatásba. 

>[!NOTE]
>A csomópont-konfigurációs fájlok nem lehetnek nagyobbak 1 MB-nál, hogy azt Azure Automationba lehessen importálni.

Az Azure-on kívül lefordított csomópont-konfigurációk (MOF) is importálhatók. Ennek a megközelítésnek több előnye is van, beleértve a teljesítményt és a megbízhatóságot.

A Windows PowerShellben való fordítás lehetővé teszi a konfigurációs tartalom aláírását, a DSC-ügynök pedig helyileg ellenőrzi az aláírt csomópontok konfigurációját egy felügyelt csomóponton. Az ellenőrzéssel biztosítható, hogy a csomópontra alkalmazott konfiguráció egy erre vonatkozó forrásból származik. További információ az aláírási csomópontok konfigurációjáról: [a WMF 5,1-es verziójának fejlesztése – a konfiguráció és a modul aláírása](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Csomópont-konfiguráció importálása a Azure Portal

1. Az Automation-fiókban kattintson az **állapot konfigurációja (DSC)** elemre a **konfiguráció**felügyelete alatt.
1. Az állapot-konfiguráció (DSC) lapon kattintson a **konfigurációk** fülre, majd a **+ Hozzáadás**gombra.
1. Az importálás lapon kattintson a **csomópont-konfigurációs fájl** szövegmező melletti mappa ikonra, és keresse meg a csomópont-konfigurációs fájlt (MOF) a helyi számítógépen.

   ![Helyi fájl keresése tallózással](./media/automation-dsc-compile/import-browse.png)

1. Adjon meg egy nevet a **konfiguráció neve** szövegmezőben. Ennek a névnek meg kell egyeznie annak a konfigurációnak a nevével, amelyből a csomópont-konfigurációt leállították.
1. Kattintson az **OK** gombra.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Csomópont-konfiguráció importálása Azure PowerShell

Az [import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) parancsmag használatával importálhat egy csomópont-konfigurációt az Automation-fiókjába.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Következő lépések

- Első lépésként tekintse meg [az Azure Automation állapot konfigurációjának megismerése](automation-dsc-getting-started.md)című témakört.
- Ha szeretne többet megtudni a DSC-konfigurációk fordításáról, hogy hozzá lehessen rendelni őket a célcsoportokhoz, tekintse meg a [konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md)című témakört.
- A PowerShell-parancsmagok ismertetése: [Azure Automation állapot-konfigurációs parancsmagok](/powershell/module/az.automation).
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/).
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse meg a [folyamatos üzembe helyezést a Azure Automation állapot-konfigurációval és a csokoládéval](automation-dsc-cd-chocolatey.md).
