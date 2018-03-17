---
title: "Azure Automation DSC-konfigurációja fordítása"
description: "Ez a cikk ismerteti, hogyan Azure Automation kívánt állapot konfigurációs szolgáltatása (DSC) konfigurációi összeállításához."
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: 2df9512a70f8299ad9282894c0ae0805d05473bd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>Azure Automation DSC-konfigurációja fordítása

Az Azure Automation szolgáltatásban kétféle módon kívánt állapot konfigurációs szolgáltatása (DSC) konfigurációk állíthat össze: az Azure portálon, és a Windows PowerShell használatával. Az alábbi táblázat segít meghatározni, mikor érdemes használni, amelyen az egyes jellemzők alapján metódus:

### <a name="azure-portal"></a>Azure Portal

* Legegyszerűbb módja az interaktív felhasználói felülettel
* Egyszerű paraméter értékének megadására, képernyő
* Könnyen nyomon követheti a feladat állapota
* Az Azure bejelentkezési hitelesített hozzáférés

### <a name="windows-powershell"></a>Windows PowerShell

* A Windows PowerShell-parancsmagokkal a parancssorból hívható
* Automatizált megoldást több lépést tartalmazhat
* Adja meg az egyszerű és összetett paraméterértékek
* Feladat-állapotok nyomon követésére
* PowerShell-parancsmagok támogatásához szükséges ügyfél
* Pass ConfigurationData
* A hitelesítő adatokat használó konfigurációk összeállítása

Miután eldöntötte, hogy egy fordítási metódusra, az alábbi eljárásokkal fordítása elindításához.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Az Azure-portálon a DSC-konfiguráció fordítása

1. Az Automation-fiók kattintson **a DSC-konfigurációk**.
2. Kattintson a konfigurációját, és nyissa meg a panelt.
3. Kattintson a **fordítási**.
4. Ha a konfiguráció nincs paraméterekkel rendelkezik, ellenőrizze, hogy kívánja-e, hogy kéri. Ha a konfigurációs paraméterek, a **fordítási konfigurációs** panel nyílik meg, megadhatja a paraméterértékek. Tekintse meg a következőt [ **alapvető paraméterek** ](#basic-parameters) további tájékoztatást talál a Paraméterek szakaszban.
5. A **fordítási feladat** panel meg van nyitva, így nyomon követheti a fordítási feladat állapotát, és a csomópont-konfigurációt (MOF konfigurációs dokumentumok) az Azure Automation DSC-lekéréses kiszolgáló elhelyezését okozta.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>A Windows PowerShell DSC-konfiguráció fordítása

Használhat [ `Start-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) fordítása a Windows PowerShell indításához. Az alábbi példakód elindít egy nevű DSC-konfiguráció összeállításának **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob` egy fordítási feladat objektumot állapotának nyomon követésére használható adja vissza. Ezután használhatja a fordítási feladat objektum [ `Get-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) fordítási feladat állapotának megállapításához és [ `Get-AzureRmAutomationDscCompilationJobOutput` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput) az adatfolyamok (kimenet) megtekintéséhez. Az alábbi példakód elindít összeállítása a **SampleConfig** konfigurációs, megvárja, amíg befejeződött, és megjeleníti az adatfolyamokat.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Alapvető paraméterek
A paraméterdeklarációhoz a DSC-konfigurációkkal, például a különböző és tulajdonságait, azonos Azure Automation-runbook működik. Lásd: [runbook elindítása az Azure Automationben](automation-starting-a-runbook.md) további információt a runbook-paraméterek.

A következő példában két paramétereket **FeatureName** és **IsPresent**annak megállapításához, a tulajdonságok értékeit az **ParametersExample.sample** csomópont konfiguráció, a fordítás során jönnek létre.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]

        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = "Present"
    if($IsPresent -eq $false)
    {
        $EnsureString = "Absent"
    }

    Node "sample"
    {
        WindowsFeature ($FeatureName + "Feature")
        {
            Ensure = $EnsureString
            Name = $FeatureName
        }
    }
}
```

Az Azure Automation DSC-portálon, vagy az Azure PowerShell alapvető paramétert használó DSC-konfigurációk állíthat össze:

### <a name="portal"></a>Portál

A portálon írhatja paraméterértékek kattintás után **fordítási**.

![helyettesítő szöveg](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

PowerShell-paraméterek a szükséges egy [hashtable](http://technet.microsoft.com/library/hh847780.aspx) ahol a kulcs megegyezik a paraméter nevével, és értéke egyenlő a paraméter értéke.

```powershell
$Parameters = @{
    "FeatureName" = "Web-Server"
    "IsPresent" = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters
```

PSCredentials átadása paraméterként kapcsolatos információkért lásd: <a href="#credential-assets"> **hitelesítő eszközök** </a> alatt.

## <a name="composite-resources"></a>Összetett erőforrások

**Összetett erőforrások** teszik lehetővé a DSC-konfigurációk használata a konfigurációs belül a beágyazott erőforrások. Ez lehetővé teszi, hogy több konfiguráció alkalmazása egy erőforrást. Lásd: [összetett erőforrások: a DSC-konfiguráció használata erőforrásként](https://docs.microsoft.com/powershell/dsc/authoringresourcecomposite) kapcsolatos további **összetett erőforrások**

> [!NOTE]
> Ahhoz, hogy **összetett erőforrások** megfelelően fordítása, akkor előbb ellenőrizze, hogy az összetett támaszkodik DSC erőforrásokat telepítve legyenek az Azure Automation-fiók modulok tárházban, vagy nem megfelelően importálni.

A DSC hozzáadandó **összetett erőforrás**, hozzá kell adnia a erőforrásmodul archiválhatja (* .zip). A modulok tárház nyissa meg az Azure Automation-fiók. Kattintson a "Modul hozzá lesz adva egy" gombra.

![A modul hozzáadása](./media/automation-dsc-compile/add_module.png)

Nyissa meg a könyvtárat, amelyben az archívumban található. Válassza ki az archív fájlt, és kattintson az OK gombra.

![Válassza ki a modul](./media/automation-dsc-compile/select_dscresource.png)

Visszakerül a modulok könyvtárba, ahol állapotának figyelése a **összetett erőforrás** kicsomagolja, és regisztrálja az Azure Automation szolgáltatásban.

![Összetett erőforrás importálása](./media/automation-dsc-compile/register_composite_resource.png)

Ha a modul regisztrálva van, majd rákattinthat a úgy, hogy ellenőrizze, hogy a **összetett erőforrások** érhető el egy konfigurációs használhatók.

![Összetett erőforrás ellenőrzése](./media/automation-dsc-compile/validate_composite_resource.png)

Akkor meghívhatja a **összetett erőforrás** azokat a konfigurációs, például így:

```powershell

    Node ($AllNodes.Where{$_.Role -eq "WebServer"}).NodeName
    {
            
            JoinDomain DomainJoin
            {
                DomainName = $DomainName
                Admincreds = $Admincreds
            }

            PSWAWebServer InstallPSWAWebServer
            {
                DependsOn = "[JoinDomain]DomainJoin"
            }        
    }

```

## <a name="configurationdata"></a>ConfigurationData
**ConfigurationData** lehetővé teszi bármely környezet konfigurációs PowerShell DSC használata során a strukturális konfigurációja külön. Lásd: [mappától "Mi" a "Where" a PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) kapcsolatos további **ConfigurationData**.

> [!NOTE]
> Használhat **ConfigurationData** Azure Automation DSC Azure PowerShell használatával, de nem az Azure-portálon fordítása során.

Használja az alábbi példa a DSC-konfiguráció **ConfigurationData** keresztül a **$ConfigurationData** és **$AllNodes** kulcsszavakat. Emellett szükség van a [ **xWebAdministration** modul](https://www.powershellgallery.com/packages/xWebAdministration/) ehhez a példához:

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    {
        xWebsite Site
        {
            Name = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure   = "Present"
        }
    }
}
```

Az előző DSC-konfiguráció PowerShell állíthat össze. A következő PowerShell két csomópont-konfigurációt ad az Azure Automation DSC-lekéréses kiszolgáló: **ConfigurationDataSample.MyVM1** és **ConfigurationDataSample.MyVM3**:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "MyVM1"
            Role = "WebServer"
        },
        @{
            NodeName = "MyVM2"
            Role = "SQLServer"
        },
        @{
            NodeName = "MyVM3"
            Role = "WebServer"
        }
    )

    NonNodeData = @{
        SomeMessage = "I love Azure Automation DSC!"
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData
```

## <a name="assets"></a>Objektumok

Eszköz hivatkozások megegyeznek az Azure Automation DSC-konfiguráció és a runbookok. További információt a következő témakörben talál:

* [Tanúsítványok](automation-certificates.md)
* [Kapcsolatok](automation-connections.md)
* [Hitelesítő adatok](automation-credentials.md)
* [Változók](automation-variables.md)

### <a name="credential-assets"></a>Hitelesítő eszközök

Azure Automation DSC-konfigurációja is hivatkozni lehessen hitelesítő eszközök használata során **Get-AutomationPSCredential**, hitelesítő eszközök is adhatók át a keresztül paraméterek, ha szükséges. Ha egy konfigurációs paramétert **PSCredential** kell egy Azure Automation szolgáltatásbeli hitelesítőadat-eszköz karakterlánc nevét átadni adott paraméter értéke, nem pedig egy PSCredential objektumot, majd írja be. A háttérben az Azure Automation szolgáltatásbeli hitelesítőadat-eszköz ezen a néven beolvasni, és a konfigurációs átadott.

Hitelesítő adatok megőrzésével csomópont-konfigurációt (MOF konfigurációs dokumentumok) biztonságos kell titkosítani a hitelesítő adatokat, a csomópont konfigurációs MOF-fájlban. Azure Automation szolgáltatásbeli ebben a lépésben egy tovább tart, és titkosítja a teljes MOF-fájlt. Azonban jelenleg pedig kell utasítani fogja a PowerShell DSC nem probléma, a csomópont konfigurációs MOF létrehozása során gyártandó egyszerű szöveges hitelesítő adatokat, mert a PowerShell DSC nem ismert, hogy Azure Automation fog kell titkosítása a teljes MOF-fájlt a generációját után keresztül egy fordítási feladat.

Megadható, hogy a PowerShell DSC, amelyek a hitelesítő adatokat egyszerű szöveges a létrehozott csomópont-konfiguráció MOF-ot a gyártandó kapcsolatát használatával [ **ConfigurationData**](#configurationdata). Át kell `PSDscAllowPlainTextPassword = $true` keresztül **ConfigurationData** minden csomópont blokk neve, amely megjelenik a DSC-konfiguráció és a hitelesítő adatokat használ.

A következő példa bemutatja a DSC-konfiguráció által használt Automation szolgáltatásbeli hitelesítőadat-eszköz.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential "SomeCredentialAsset"

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath = "\\Server\share\path\file.ext"
            DestinationPath = "C:\destinationPath"
            Credential = $Cred
        }
    }
}
```

Az előző DSC-konfiguráció PowerShell állíthat össze. A következő PowerShell két csomópont-konfigurációt ad az Azure Automation DSC-lekéréses kiszolgáló: **CredentialSample.MyVM1** és **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "*"
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = "MyVM1"
        },
        @{
            NodeName = "MyVM2"
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData
```

> [!NOTE]
> Művelet befejeződése jelenhet egy hiba szerint: **a "Microsoft.PowerShell.Management" modul nem lett importálva, mert a "Microsoft.PowerShell.Management" beépülő modul már importálva van.** Ez az üzenet biztonságosan figyelmen kívül hagyhatja.

## <a name="importing-node-configurations"></a>Csomópont-konfigurációk importálása

Csomópont-konfigurációt (MOF-ot), amely rendelkezik Azure-on kívüli fordított is importálhat. Egy ennek előnye, hogy csomópont-konfigurációt írhatók alá.
Egy aláírt csomópont-konfiguráció ellenőrzése helyileg felügyelt csomóponton a DSC-ügynök, győződjön meg arról, hogy a konfiguráció alkalmazták a csomópont egy hitelesített forrásból származik-e.

> [!NOTE]
> Használja az importálást aláírt beállításokat az Azure Automation-fiók rendszerbe, de Azure Automation jelenleg nem támogatja a fordítása aláírt konfigurációkat.

> [!NOTE]
> A csomópont konfigurációs fájl nem engedélyezi az Azure Automation importáláshoz 1 MB-nál nagyobb lehet.

Megismerheti a csomópont-konfigurációt, aláírásához https://msdn.microsoft.com/en-us/powershell/wmf/5.1/dsc-improvements#how-to-sign-configuration-and-module.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Az Azure-portálon a csomópont-konfiguráció importálása

1. Az Automation-fiók kattintson **DSC-csomópontkonfigurációk** alatt **konfigurációkezelés**.

    ![A DSC-csomópontkonfigurációk](./media/automation-dsc-compile/node-config.png)
2. Az a **DSC-csomópontkonfigurációk** panelen kattintson a **hozzáadása egy NodeConfiguration**.
3. Az a **importálási** panelen kattintson a Tovább gombra a mappa ikonra a **csomópont konfigurációs fájl** szövegmező a csomópont konfigurációs fájl (MOF) a helyi számítógépen.

    ![Helyi fájl kiválasztása](./media/automation-dsc-compile/import-browse.png)
4. Írjon be egy nevet a **Konfigurációnévvel** szövegmező. Ezt a nevet meg kell egyeznie a nevét, amelyből a csomópont-konfiguráció lett lefordítva, a konfiguráció.
5. Kattintson az **OK** gombra.

### <a name="importing-a-node-configuration-with-powershell"></a>A PowerShell-lel egy csomópont-konfiguráció importálása

Használhatja a [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) parancsmagot, hogy a csomópont-konfiguráció importálása az automation-fiók.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName "MyAutomationAccount" -ResourceGroupName "MyResourceGroup" -ConfigurationName "MyNodeConfiguration" -Path "C:\MyConfigurations\TestVM1.mof"
```



