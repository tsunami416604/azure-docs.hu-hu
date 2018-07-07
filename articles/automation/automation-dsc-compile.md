---
title: Az Azure Automation DSC konfigurációk fordítása
description: Ez a cikk ismerteti az Azure Automation Desired State Configuration (DSC) konfigurációk fordítása.
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 76b2b1983cc5a6cedfcff204871e0b0f985fef95
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900783"
---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>Az Azure Automation DSC konfigurációk fordítása

A két módon az Azure Automation Desired State Configuration (DSC) konfigurációk lefordíthatja: az Azure Portalon, és a Windows PowerShell-lel. Az alábbi táblázat segít meghatározni, hogy mikor érdemes használni az egyes jellemzők alapján módszer:

### <a name="azure-portal"></a>Azure Portal

* Az interaktív felhasználói kezelőfelület legegyszerűbb módja
* Adja meg a paraméterértékeket egyszerű űrlap
* Könnyedén nyomon követheti a feladat állapota
* Az Azure-beli bejelentkezési hitelesített hozzáférés

### <a name="windows-powershell"></a>Windows PowerShell

* Hívja a parancssorból a Windows PowerShell-parancsmagok
* Automatizált megoldás több lépést tartalmazhat
* Adja meg az egyszerű és összetett paraméterértékek
* Feladat állapotának nyomon követése
* PowerShell-parancsmagok támogatnia kell az ügyfél
* Pass ConfigurationData
* Fordítsa le a hitelesítő adatokat használó konfigurációk

Miután eldöntötte egy fordítási metódust, az alábbi eljárásokkal fordítása elindításához.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Az Azure Portalon a DSC-konfiguráció fordítása

1. Kattintson az Automation-fiók **DSC-konfigurációk**.
2. Kattintson a konfigurációt a hozzá tartozó panel megnyitásához.
3. Kattintson a **összeállítása**.
4. Ha a konfiguráció nem paraméterekkel rendelkezik, győződjön meg arról, hogy azt szeretné, hogy a kéri. Ha a konfigurációs paraméterek, a **konfiguráció fordítása** panelt, így is adja meg a paraméterértékeket. A következő [ **alapvető paramétert** ](#basic-parameters) további tájékoztatást talál a Paraméterek szakaszban.
5. A **fordítási feladat** panel megnyílik, így nyomon követheti a fordítási feladat állapotát, és a csomópont-konfigurációk (MOF konfigurációs dokumentum), az okozza, az Azure Automation DSC lekéréses kiszolgálón kell helyezni.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Egy Windows PowerShell DSC-konfiguráció fordítása

Használhat [ `Start-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) összeállítása a Windows PowerShell-lel elindításához. Az alábbi példakód elindít egy úgynevezett DSC-konfiguráció fordítási **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob` a fordítási feladat objektumot, amely segítségével nyomon követheti az állapotát adja vissza. Ezután használhatja a fordítási feladatobjektum és a [ `Get-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) a fordítási feladat állapotának megállapításához és [ `Get-AzureRmAutomationDscCompilationJobOutput` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput) az adatfolyamok (kimenet) megtekintéséhez. Az alábbi példakód elindít összeállítása a **SampleConfig** konfigurációs, megvárja, amíg a befejeződött, és ezután megjeleníti az adatfolyamokat.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Alapszintű paraméterek
DSC-konfigurációk paramétertípusok és tulajdonságai, köztük a paraméterdeklarációhoz ugyanaz, mint az Azure Automation-runbookok működik. Lásd: [runbook elindítása az Azure Automationben](automation-starting-a-runbook.md) további információ a runbook-paramétereket.

Az alábbi példában két paramétert nevű **FeatureName** és **IsPresent**annak megállapításához, a tulajdonságokat a **ParametersExample.sample** csomópont konfiguráció, a fordítás során jönnek létre.

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

DSC-konfigurációk az Azure Automation DSC-portálon vagy az Azure PowerShell-lel alapvető paramétert használó állíthat össze:

### <a name="portal"></a>Portál

A portálon adhat meg paraméterértékek kattintás után **összeállítása**.

![helyettesítő szöveg](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

PowerShell a paraméterek szükségesek egy [kivonattábla](http://technet.microsoft.com/library/hh847780.aspx) ahol a kulcs megegyezik a paraméter nevével, és az érték megegyezik a paraméter értéke.

```powershell
$Parameters = @{
    "FeatureName" = "Web-Server"
    "IsPresent" = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters
```

További információ a PSCredentials átadott paraméterek: <a href="#credential-assets"> **hitelesítő eszközök** </a> alatt.

## <a name="composite-resources"></a>Összetett erőforrások

**Összetett erőforrások** lehetővé teszik, hogy egy konfigurációs belül beágyazott erőforrások, DSC-konfigurációk. Ez lehetővé teszi, hogy több konfiguráció alkalmazása a egy erőforrást. Lásd: [összetett erőforrások: a DSC-konfiguráció használata erőforrásként](https://docs.microsoft.com/powershell/dsc/authoringresourcecomposite) további részleteket ismerhet meg **összetett erőforrások**

> [!NOTE]
> Ahhoz, hogy **összetett erőforrások** fordítása helyes, akkor előbb ellenőrizze, hogy olyan DSC-erőforrások, az összetett támaszkodik telepítve legyenek az Azure Automation-fiók modulok adattárban, vagy nem megfelelően importálása.

A DSC hozzáadandó **összetett erőforrás**, hozzá kell adnia a resource modul archívumot (* .zip). A modulok tárházban nyissa meg az Azure Automation-fiók. Ezután kattintson a "Modul hozzáadása" gombra.

![Modul hozzáadása](./media/automation-dsc-compile/add_module.png)

Keresse meg azt a könyvtárat, ahol az archív található. Válassza ki az archívumfájlt, és kattintson az OK gombra.

![Modul kiválasztása](./media/automation-dsc-compile/select_dscresource.png)

Visszakerül a modulok könyvtárhoz, ahol figyelemmel kísérheti az állapotát a **összetett erőforrás** , kibontja, és regisztrálja az Azure Automationben.

![Összetett erőforrások importálása](./media/automation-dsc-compile/register_composite_resource.png)

A modul regisztrálása után, majd kattintson, és ellenőrizze, hogy a **összetett erőforrások** konfigurációban használandó mostantól elérhetők.

![Összetett erőforrások érvényesítése](./media/automation-dsc-compile/validate_composite_resource.png)

Akkor meghívhatja a **összetett erőforrás** azokat a konfigurációs lépések szerint:

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
**ConfigurationData** különítheti el szerkezeti konfigurációs PowerShell DSC használata során bármilyen környezetspecifikus konfigurációjából. Lásd: [elválasztja "Mi" a "Where", a PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) további részleteket ismerhet meg **ConfigurationData**.

> [!NOTE]
> Használhat **ConfigurationData** összeállítása az Azure PowerShell-lel az Azure Automation DSC, de nem az Azure Portalon során.

Használja az alábbi példa DSC-konfiguráció **ConfigurationData** keresztül a **$ConfigurationData** és **$AllNodes** kulcsszavakat. Emellett a [ **xWebAdministration** modul](https://www.powershellgallery.com/packages/xWebAdministration/) ebben a példában:

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

A fenti DSC-konfiguráció a PowerShell használatával állíthat össze. Az alábbi PowerShell-lel az Azure Automation DSC lekérési kiszolgálót ad hozzá két csomópont-konfigurációk: **ConfigurationDataSample.MyVM1** és **ConfigurationDataSample.MyVM3**:

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

Az eszközintelligencia-hivatkozások ugyanazok az Azure Automation DSC-konfigurációk és a runbookok. További információ a következő jelenik meg:

* [Tanúsítványok](automation-certificates.md)
* [Kapcsolatok](automation-connections.md)
* [Hitelesítő adatok](automation-credentials.md)
* [Változók](automation-variables.md)

### <a name="credential-assets"></a>Hitelesítő eszközök

Bár az Azure Automation DSC-konfigurációk használatával hitelesítő eszközök hivatkozhat **Get-AutomationPSCredential**, hitelesítő eszközök is átadható paraméterekkel, ha szükséges. Ha egy konfigurációs paramétert **PSCredential** teljesítenie kell a az Azure Automation szolgáltatásbeli hitelesítőadat-eszköz karakterlánc neve a paraméter értéke, nem pedig egy PSCredential objektumot, majd írja be. A színfalak mögött az Azure Automation szolgáltatásbeli hitelesítőadat-eszköz ilyen nevű lekért és a konfiguráció átadott.

Gondoskodik a hitelesítő adatok biztonságos, a csomópont-konfigurációk (MOF konfigurációs dokumentumok) van szükség a csomópont konfigurációs MOF-fájlban található a hitelesítő adatok titkosításához. Azonban jelenleg utasítsa PowerShell DSC nem probléma, a hitelesítő adatokat kell használt kimeneti adattípus szövegként csomópont konfigurációs MOF létrehozása során, mert a PowerShell DSC nem ismert, hogy Azure Automation fog kell titkosítása a teljes MOF-fájlt a létrehozás után keresztül egy fordítási feladat.

Beállíthatja, hogy a PowerShell DSC, hogy rendben, a használt kimeneti adattípus lehet, a létrehozott csomópont-konfiguráció MOF-fájlok az egyszerű szöveges hitelesítő adatokat használó [ **ConfigurationData**](#configurationdata). Kell átadnia `PSDscAllowPlainTextPassword = $true` keresztül **ConfigurationData** minden csomópont blokk neve, amely megjelenik a DSC-konfiguráció, és a hitelesítő adatokat használ.

Az alábbi példa egy Automation szolgáltatásbeli hitelesítőadat-eszköz használó DSC-konfiguráció látható.

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

A fenti DSC-konfiguráció a PowerShell használatával állíthat össze. Az alábbi PowerShell-lel az Azure Automation DSC lekérési kiszolgálót ad hozzá két csomópont-konfigurációk: **CredentialSample.MyVM1** és **CredentialSample.MyVM2**.

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
> Fordítás befejeződése is megjelenhet egy hibaüzenet szerint: **a "Microsoft.PowerShell.Management" modul nem lett importálva, mert a "Microsoft.PowerShell.Management" beépülő modul már importálták.** Ez az üzenet biztonságosan figyelmen kívül hagyhatja.

## <a name="importing-node-configurations"></a>Csomópont-konfigurációk importálása

Csomópont-konfigurációk (MOF-fájlok), amely fordított rendelkezik Azure-on kívül is importálhat. Ez egyik előnye az, hogy írhatók alá csomópont-konfigurációk.
Aláírt csomópont-konfiguráció ellenőrzése helyileg egy felügyelt csomóponton a DSC-ügynök, annak biztosítása, hogy a konfiguráció alkalmazásakor a csomópont egy hitelesített forrásból származik-e.

> [!NOTE]
> Használhatja az importálás aláírt konfigurációk az Azure Automation-fiókba, de az Azure Automation jelenleg nem támogatja az aláírt konfigurációk összeállítására.

> [!NOTE]
> A csomópont konfigurációs fájlnak kell lennie, hogy az Azure Automationbe importálni 1 MB-nál nagyobb.

Megismerheti a csomópont-konfigurációk aláírásához https://msdn.microsoft.com/powershell/wmf/5.1/dsc-improvements#how-to-sign-configuration-and-module.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Az Azure Portalon a csomópont-konfiguráció importálása

1. Kattintson az Automation-fiók **DSC-csomópontkonfigurációk** alatt **konfigurációkezelés**.

    ![DSC-csomópontkonfigurációk](./media/automation-dsc-compile/node-config.png)
2. Az a **DSC-csomópontkonfigurációk** panelen kattintson a **csomópont-konfiguráció hozzáadása**.
3. Az a **importálás** panelen kattintson a Tovább gombra a mappa ikont a **Csomópontkonfigurációs fájl** szövegmezőben, hogy a csomópont konfigurációs fájl (MOF) a helyi számítógépen.

    ![Helyi fájl keresése tallózással](./media/automation-dsc-compile/import-browse.png)
4. Írjon be egy nevet a **konfiguráció neve** szövegmezőbe. Ezt a nevet meg kell egyeznie a konfigurációt a csomópont-konfiguráció fordítása nevét.
5. Kattintson az **OK** gombra.

### <a name="importing-a-node-configuration-with-powershell"></a>A PowerShell-lel a csomópont-konfiguráció importálása

Használhatja a [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) parancsmag segítségével importálja a csomópont-konfiguráció az automation-fiókba.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName "MyAutomationAccount" -ResourceGroupName "MyResourceGroup" -ConfigurationName "MyNodeConfiguration" -Path "C:\MyConfigurations\TestVM1.mof"
```



