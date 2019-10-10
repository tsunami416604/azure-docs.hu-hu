---
title: Konfigurációk fordítása Azure Automation állapot konfigurációjában
description: Ez a cikk azt ismerteti, hogyan lehet lefordítani a Azure Automation kívánt állapot-konfiguráció (DSC) konfigurációit.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3d9c6c9b73f8887d4fdb85da277b2e27d8f5221c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243569"
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

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>DSC-konfiguráció fordítása az Azure állapot-konfigurációban

### <a name="portal"></a>Portál

1. Az Automation-fiókban kattintson az **állapot konfigurálása (DSC)** elemre.
1. Kattintson a **konfigurációk** fülre, majd a fordításhoz kattintson a konfiguráció nevére.
1. Kattintson a **fordítás**gombra.
1. Ha a konfiguráció nem rendelkezik paraméterekkel, a rendszer felszólítja, hogy erősítse meg, hogy szeretné-e lefordítani. Ha a konfiguráció paraméterekkel rendelkezik, megnyílik a **fordítási konfiguráció** panel, ahol megadhatja a paraméterek értékét. A paraméterekkel kapcsolatos további részletekért tekintse meg a következő [**alapvető paraméterek**](#basic-parameters) szakaszt.
1. Megnyílik a **fordítási feladat** lap, amelyen nyomon követheti a fordítási feladat állapotát, valamint a csomópont-konfigurációkat (MOF konfigurációs dokumentumok), amelyeket a rendszer a Azure Automation állapot-konfiguráció lekérési kiszolgálójára helyez.

### <a name="azure-powershell"></a>Azure PowerShell

A [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) segítségével megkezdheti a fordítást a Windows PowerShell használatával. Az alábbi mintakód elindítja a **SampleConfig**nevű DSC-konfiguráció fordítását.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

@no__t – 0 – a fordítási feladatok azon objektumát adja vissza, amelyet az állapotának nyomon követésére használhat. Ezt követően ezt a fordítási feladatot a [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) paranccsal használhatja
a fordítási feladatok állapotának megállapítása és [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput)
a streamek (kimenet) megtekintéséhez. Az alábbi mintakód elindítja a **SampleConfig** -konfiguráció fordítását, megvárja, amíg befejeződik, majd megjeleníti a folyamait.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

###  <a name="basic-parameters"></a>Alapszintű paraméterek

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

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

További információ a PSCredentials paraméterként való átadásáról: az alábbi [hitelesítőadat-eszközök](#credential-assets) .

### <a name="compiling-configurations-in-azure-automation-that-contain-composite-resources"></a>Összetett erőforrásokat tartalmazó Azure Automation konfigurációk fordítása

Az **összetett erőforrások** lehetővé teszik a DSC-konfigurációk használatát beágyazott erőforrásként egy konfiguráción belül. Ez lehetővé teszi, hogy több konfigurációt alkalmazzon egyetlen erőforrásra. Az **összetett erőforrásokkal**kapcsolatos további információkért tekintse meg az [összetett erőforrások: DSC-konfiguráció használata erőforrásként](/powershell/scripting/dsc/resources/authoringresourcecomposite) című témakört.

> [!NOTE]
> Ahhoz, hogy az **összetett erőforrásokat** tartalmazó konfigurációk helyesen legyenek lefordítva, először be kell állítani a Azure Automationba, hogy a rendszer először importálja azokat a DSC-erőforrásokat, amelyekre az összetett támaszkodik.

A DSC **összetett erőforrások** hozzáadása nem különbözik a PowerShell-modulok Azure Automationhoz való hozzáadásával.
A folyamat lépésenkénti utasításait a [Azure Automation moduljainak kezelése](/azure/automation/shared-resources/modules)című cikk ismerteti.

### <a name="managing-configurationdata-when-compiling-configuration-in-azure-automation"></a>A ConfigurationData kezelése a Azure Automation konfigurációjának összeállítása során

A **ConfigurationData** lehetővé teszi a strukturális konfiguráció elkülönítését bármely környezet-specifikus konfigurációból a PowerShell DSC használata közben. További információ a **ConfigurationData**-ről: ["mi" a "hol" a PowerShell DSC-ben](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) .

> [!NOTE]
> A **ConfigurationData** a Azure Automation állapot-konfiguráció összeállításakor Azure PowerShell, de nem a Azure Portalban is használhatja.

A következő példában a DSC-konfiguráció a **ConfigurationData** -t használja a **$ConfigurationData** és a **$AllNodes** kulcsszavak használatával. Ehhez a példához a [ **xWebAdministration** modulra](https://www.powershellgallery.com/packages/xWebAdministration/) is szüksége lesz:

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

A korábbi DSC-konfiguráció a Windows PowerShell használatával fordítható le. A következő parancsfájl két csomópont-konfigurációt hoz létre a Azure Automation állapot-konfiguráció lekérési szolgáltatáshoz: **ConfigurationDataSample. MyVM1** és **ConfigurationDataSample. MyVM3**:

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

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="working-with-assets-in-azure-automation-during-compilation"></a>Adategységek használata a fordítás során Azure Automation

Az eszközök hivatkozásai megegyeznek a Azure Automation állapot-konfigurációban és a runbookok. További információt a következő témakörben talál:

- [Tanúsítványok](automation-certificates.md)
- [Kapcsolatok](automation-connections.md)
- [Hitelesítő adatok](automation-credentials.md)
- [Változók](automation-variables.md)

#### <a name="credential-assets"></a>Hitelesítőadat-eszközök

A Azure Automation DSC-konfigurációk hivatkozhatnak az Automation hitelesítőadat-eszközökre a `Get-AutomationPSCredential` parancsmag használatával. Ha egy konfiguráció egy **PSCredential** típusú paraméterrel rendelkezik, akkor az `Get-AutomationPSCredential` parancsmagot használhatja, ha egy Azure Automation hitelesítőadat-eszköz sztring nevét átadja a parancsmagnak a hitelesítő adat lekéréséhez. Ezt az objektumot használhatja a **PSCredential** objektumot igénylő paraméterhez. A színfalak mögött a rendszer beolvassa a Azure Automation hitelesítő adatokat, és átadja a konfigurációnak. Az alábbi példa ezt mutatja be működés közben.

A hitelesítő adatok biztonságos tárolásához a csomópont-konfigurációkban (MOF konfigurációs dokumentumok) a hitelesítő adatok titkosítását igényli a csomópont-konfiguráció MOF-fájljában. Jelenleg azonban meg kell adnia a PowerShell DSC-t, hogy a hitelesítő adatok egyszerű szövegként legyenek kiválasztva a csomópont-konfiguráció MOF-generációja során, mert a PowerShell DSC nem tudja, hogy Azure Automation a teljes MOF-fájlt a létrehozás után titkosítja. fordítási feladatokon keresztül.

Megadhatja a PowerShell DSC-t, hogy a hitelesítő adatok egyszerű szövegként legyenek leképezve a generált csomópont-konfigurációs MOF a konfigurációs adatok használatával. A **ConfigurationData** a DSC-konfigurációban megjelenő és a hitelesítő adatokat használó minden csomópont-blokk neveként át kell adnia `PSDscAllowPlainTextPassword = $true` értéket.

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

Az előző DSC-konfigurációt a PowerShell használatával fordíthatja le. A következő PowerShell két csomópont-konfigurációt hoz létre a Azure Automation állapot-konfiguráció lekérési kiszolgálóhoz: **CredentialSample. MyVM1** és **CredentialSample. MyVM2**.

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

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> A fordítás befejezésekor a következő hibaüzenet jelenhet meg: **a "Microsoft. PowerShell. Management" modul nem lett importálva, mert a "Microsoft. PowerShell. Management" beépülő modul már be lett importálva.** Ez a figyelmeztetés nyugodtan figyelmen kívül hagyható.

## <a name="compiling-configurations-in-windows-powershell-and-publishing-to-azure-automation"></a>Konfigurációk fordítása a Windows PowerShellben és közzététel a Azure Automation

Az Azure-on kívül lefordított csomópont-konfigurációk (MOF) is importálhatók.
Ebbe beletartozik egy fejlesztői munkaállomás vagy egy olyan szolgáltatás fordítása, mint például az [Azure DevOps](https://dev.azure.com).
Ennek a megközelítésnek több előnye is van, beleértve a teljesítményt és a megbízhatóságot.
A Windows PowerShellben való fordítás lehetőséget biztosít a konfigurációs tartalom aláírására is.
Az aláírt csomópontok konfigurációját helyileg ellenőrzik a DSC-ügynök által felügyelt csomóponton, így biztosítva, hogy a csomópontra alkalmazott konfiguráció egy hitelesített forrásból származik.

> [!NOTE]
> A csomópont-konfigurációs fájlok nem lehetnek nagyobbak 1 MB-nál, hogy azt Azure Automationba lehessen importálni.

A csomópont-konfigurációk aláírásával kapcsolatos további információkért lásd: [a WMF 5,1-es verziójának fejlesztése – a konfiguráció és a modul aláírása](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compiling-a-configuration-in-windows-powershell"></a>Konfiguráció fordítása a Windows PowerShellben

A DSC-konfigurációk Windows PowerShellben való fordításának folyamata tartalmazza a PowerShell DSC dokumentációjának [írását, fordítását és konfigurációjának alkalmazását](/powershell/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Ezt egy fejlesztői munkaállomáson vagy egy olyan Build szolgáltatáson belül lehet végrehajtani, mint például az [Azure DevOps](https://dev.azure.com).

A konfiguráció fordításával létrehozott MOF-fájl vagy-fájlok ezután közvetlenül importálhatók az Azure állapot-konfigurációs szolgáltatásba.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Csomópont-konfiguráció importálása a Azure Portalban

1. Az Automation-fiókban kattintson az **állapot konfigurációja (DSC)** elemre a **konfiguráció**felügyelete alatt.
1. Az **állapot-konfiguráció (DSC)** lapon kattintson a **konfigurációk** fülre, majd a **+ Hozzáadás**gombra.
1. Az **Importálás** lapon kattintson a **csomópont-konfigurációs fájl** szövegmező melletti mappa ikonra, és keresse meg a csomópont-konfigurációs fájlt (MOF) a helyi számítógépen.

   ![Helyi fájl keresése tallózással](./media/automation-dsc-compile/import-browse.png)

1. Adjon meg egy nevet a **konfiguráció neve** szövegmezőben. Ennek a névnek meg kell egyeznie annak a konfigurációnak a nevével, amelyből a csomópont-konfigurációt leállították.
1. Kattintson az **OK** gombra.

### <a name="importing-a-node-configuration-with-azure-powershell"></a>Csomópont-konfiguráció importálása Azure PowerShell

Az [import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) parancsmag használatával importálhat egy csomópont-konfigurációt az Automation-fiókjába.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Következő lépések

- Első lépésként tekintse meg [az Azure Automation állapot konfigurációjának megismerése](automation-dsc-getting-started.md) című témakört.
- A DSC-konfigurációk fordításának megismeréséhez, hogy hozzá lehessen rendelni azokat a célcsoportokhoz, tekintse meg a [konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md) című témakört.
- A PowerShell-parancsmagok ismertetése: [Azure Automation állapot-konfigurációs parancsmagok](/powershell/module/azurerm.automation/#automation)
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/)
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse meg a [folyamatos üzembe helyezést a Azure Automation állapot-konfiguráció és a csokoládés használatával](automation-dsc-cd-chocolatey.md) .
