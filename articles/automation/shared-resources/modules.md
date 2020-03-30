---
title: Modulok kezelése az Azure Automationben
description: Ez a cikk bemutatja, hogyan kezelhetők a modulok az Azure Automationben
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e300bc0f29808215673407d21b65fe329e50ad45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278336"
---
# <a name="manage-modules-in-azure-automation"></a>Modulok kezelése az Azure Automationben

Az Azure Automation lehetővé teszi a PowerShell-modulok importálását az Automation-fiókba a PowerShell-alapú runbookok számára. Ezek a modulok lehetnek egyéni modulok, amelyeket a PowerShell-galériából, vagy az AzureRM és az Az-modulok az Azure-hoz létrehozott. Automatizálási fiók létrehozásakor a rendszer alapértelmezés szerint importál bizonyos modulokat.

## <a name="import-modules"></a>Modulok importálása

Többféleképpen is importálhat egy modult az Automation-fiókba. A következő szakaszok a modul importálásának különböző módjait mutatják be.

> [!NOTE]
> Az Azure Automationben használandó modulban lévő fájlok maximális elérési útja 140 karakter. A 140 karakternél hosszabb elérési utak nem importálhatók `Import-Module`a PowerShell-munkamenetbe.

### <a name="powershell"></a>PowerShell

A [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) segítségével importálhat egy modult az Automation-fiókba. A parancsmag egy modul zip-csomag url-jét veszi fel.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Ugyanazzal a parancsmagmal is importálhat egy modult közvetlenül a PowerShell-galériából. Győződjön meg róla, hogy megragadja **a ModuleName** és **a ModuleVersion** -t a [PowerShell-galériából.](https://www.powershellgallery.com)

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Azure portál

Az Azure Portalon keresse meg az Automation-fiókot, és válassza a **Modulok lehetőséget** a Megosztott **erőforrások csoportban.** Kattintson **a + Modul hozzáadása gombra.** Jelöljön ki egy **.zip** fájlt, amely tartalmazza a **modult,** majd a folyamat importálásának megkezdéséhez kattintson az Ok gombra.

### <a name="powershell-gallery"></a>PowerShell-galéria

A PowerShell-galériából származó modulok közvetlenül a [PowerShell-galériából](https://www.powershellgallery.com) vagy az Automation-fiókból importálhatók.

Ha egy modult szeretne importálni https://www.powershellgallery.com a PowerShell-galériából, nyissa meg az importálni kívánt modult, és keresse meg azt a modult, amelyet importálni szeretne. Kattintson a **Telepítés az Azure Automation szolgáltatásba** elemre az Azure **Automation** lapon a Telepítési **beállítások csoportban.** Ez a művelet megnyitja az Azure Portalon. Az **Importálás** lapon jelölje ki az Automatizálási fiókot, és kattintson **az OK gombra.**

![A PowerShell-galéria importálási modulja](../media/modules/powershell-gallery.png)

A PowerShell-galériából is importálhat modulokat közvetlenül az Automation-fiókból. Az Automation-fiókban válassza a **Modulok lehetőséget a** Megosztott erőforrások **csoportban.** A modulok lapon kattintson a **Galéria tallózása**elemre, majd keressen egy modult a PowerShell-galériában. Jelölje ki az importálni kívánt modult, és kattintson az **Importálás gombra.** Az **Importálás** lapon kattintson az **OK** gombra az importálási folyamat elindításához.

![PowerShell-galéria importálása az Azure Portalról](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Modulok törlése

Ha problémái vannak egy modullal, vagy vissza kell visszaállna egy modul egy korábbi verziójára, törölheti azt az Automation-fiókból. Az automatizálási fiók létrehozásakor importált [alapértelmezett modulok](#default-modules) eredeti verziója nem törölhető. Ha a törölni kívánt modul az egyik telepített [alapértelmezett modul](#default-modules) újabb verziója, akkor visszaáll az Automation-fiókkal telepített verzióra. Ellenkező esetben az Automation-fiókból törölt modulok törlődnek.

### <a name="azure-portal"></a>Azure portál

Az Azure Portalon keresse meg az Automation-fiókot, és válassza a **Modulok lehetőséget** a Megosztott **erőforrások csoportban.** Jelölje ki az eltávolítani kívánt modult. A **Modul** lapon válassza a **Törlés**lehetőséget. Ha ez a modul az [egyik alapértelmezett modul,](#default-modules)akkor visszalesz állítva az Automation-fiók létrehozásakor jelen lévő verzióra.

### <a name="powershell"></a>PowerShell

Ha el szeretne távolítani egy modult a PowerShellen keresztül, futtassa a következő parancsot:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Belső parancsmagok

Az alábbiakban a belső `Orchestrator.AssetManagement.Cmdlets` modulban lévő parancsmagok felsorolása látható, amelyek minden Automation-fiókba importálásra kerülnek. Ezek a parancsmagok a runbookok és a DSC-konfigurációkban érhetők el, és lehetővé teszik az eszközök automatizálási fiókon belüli interakcióját. Emellett a belső parancsmagok lehetővé teszik **Variable** a titkos kulcsok lekérését a titkosított változóértékekből, **hitelesítő adatokból**és titkosított **kapcsolatmezőkből.** Az Azure PowerShell-parancsmagok nem tudják beolvasni ezeket a titkos kulcsokat. Ezek a parancsmagok nem követelik meg, hogy implicit módon csatlakozzon az Azure-hoz, amikor használja őket, például egy Futtatás fiók használatával az Azure-ba való hitelesítéshez.

>[!NOTE]
>Ezek a belső parancsmagok érhetők el a Windows hibrid Runbook-feldolgozó, nem érhetők el a Linux hibrid Runbook-feldolgozó. Használja a megfelelő [AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) vagy [Az modulok runbookok](../az-modules.md) futó közvetlenül a számítógépen, vagy a környezetben lévő erőforrások ellen. 
>

|Név|Leírás|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob (Várakozás-automatizálási feladat)|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Kapcsolattípus hozzáadása a modulhoz

Az Automatizálási fiókban használható egyéni [kapcsolattípust](../automation-connections.md) úgy biztosíthatja, hogy egy választható fájlt ad hozzá a modulhoz. Ez a fájl egy metaadatfájl, amely egy Azure Automation-kapcsolattípust határoz meg, amelyet a modul parancsmagjaihoz kell használni az Automation-fiókban. Ennek eléréséhez először tudnia kell, hogyan kell egy PowerShell-modult. A modulkészítésről további információt a [PowerShell-parancsfájlmodul írása című témakörben talál.](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)

![Egyéni kapcsolat használata az Azure Portalon](../media/modules/connection-create-new.png)

Azure Automation-kapcsolattípus hozzáadásához a modulnak tartalmaznia `<ModuleName>-Automation.json` kell egy fájlt a kapcsolat típusának tulajdonságait. A jsonfájl a tömörített .zip fájl modulmappájába kerül. Ez a fájl a modul által képviselt rendszerhez vagy szolgáltatáshoz való csatlakozáshoz szükséges kapcsolat mezőit tartalmazza. A konfiguráció végül létrehoz egy kapcsolattípust az Azure Automationben. Ezzel a fájllal beállíthatja a mezőneveket, típusokat, valamint azt, hogy a mezőket titkosítsa-e vagy nem szabad-e megkérni a modul kapcsolattípusához. A következő példa egy json fájlformátumú sablon, amely egy felhasználónév- és jelszótulajdonságot határoz meg:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="module-best-practices"></a>A modul okkal kapcsolatos gyakorlati tanácsok

A PowerShell-modulok importálhatók az Azure Automationbe, hogy a parancsmagjaik elérhetők legyenek a runbookokon és a DSC-erőforrásokban való használatra dsc-konfigurációkban. A színfalak mögött az Azure Automation tárolja ezeket a modulokat, és a runbook-feladat és a DSC fordítási feladat végrehajtási ideje alatt betölti őket az Azure Automation sandboxokba, ahol a runbookok végrehajtása és a DSC-konfigurációk fordítása. A modulokban lévő DSC-erőforrások is automatikusan az Automation DSC lekéréses kiszolgálóra kerülnek. A DSC-konfigurációk alkalmazásakor a gépek lehúzhatják őket.

Javasoljuk, hogy vegye figyelembe a következőket, amikor egy PowerShell-modult hoz az Azure Automationben való használatra:

* NE vegyen fel verziómappát a .zip csomagba.  Ez a probléma kevésbé érinti a runbookokat, de problémát okoz az Állapotkonfiguráció szolgáltatással.  Az Azure Automation automatikusan létrehozza a verziómappát, amikor a modult a DSC által kezelt csomópontokra terjesztik, és ha létezik verziómappa, akkor két példányt fog létrehozni.  Példa egy DSC-modul mappaszerkezetére:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* A modul összes parancsmagjáról mellékeljen egy szinopszist, egy leírást és egy súgó URI-t. A PowerShellben meghatározhat bizonyos súgóinformációkat a parancsmagokhoz, hogy a felhasználó segítséget kapjon a **Get-Help** parancsmaggal való használatukkor. A következő példa bemutatja, hogyan definiálható szinopszis és súgó URI egy .psm1 modulfájlban:

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Ez az információ azt mutatja, ez a súgó segítségével a **Get-Help** parancsmag a PowerShell konzolon. Ez a leírás az Azure Portalon is megjelenik.

  ![Integrációs modul súgó](../media/modules/module-activity-description.png)

* Ha a modul külső szolgáltatáshoz csatlakozik, annak tartalmaznia kell egy [kapcsolattípust.](#add-a-connection-type-to-your-module) A modul minden egyes parancsmagjának képesnek kell lennie egy kapcsolat objektum (az adott kapcsolattípus egy példánya) befogadására paraméterként. A felhasználók a csatlakozási eszköz paramétereit a parancsmag megfelelő paramétereihez képezik le minden alkalommal, amikor egy parancsmag hívását meghívják. A fenti runbook-példa alapján egy Contoso-kapcsolati eszköz nevű ContosoConnection nevű példa segítségével fér hozzá a Contoso-erőforrásokhoz, és adatokat ad vissza a külső szolgáltatásból.

  A következő példában a mezők egy `PSCredential` objektum Felhasználónév és Jelszó tulajdonságaira vannak leképezve, majd átkerülnek a parancsmagba.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  A viselkedés megközelítésének egyszerűbb és jobb módja, ha közvetlenül átadja a kapcsolatobjektumot a parancsmagnak:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Engedélyezheti az előző példához hasonló viselkedést a parancsmagok esetében, ha lehetővé teszi számukra, hogy közvetlenül egy kapcsolatobjektumot fogadjanak el paraméterként, és ne csak a paraméterek csatlakozási mezőit. Általában szeretne egy paraméterkészletet minden, hogy az Azure Automation-t nem használó felhasználó meghívhatja a parancsmagokat anélkül, hogy egy kivonatot hozna létre, hogy kapcsolatobjektumként működjön. A paraméterkészlet `UserAccount`, a kapcsolatmező tulajdonságainak átadására szolgál. `ConnectionObject`lehetővé teszi, hogy a kapcsolatot egyenesen átadja.

* Adja meg a modul összes parancsmagjának kimeneti típusát. Egy parancsmag kimenettípusának megadása lehetővé teszi a tervezés közben az IntelliSense használatát, amely segít meghatározni egy parancsmag kimenetének tulajdonságait a megírásuk támogatására. Ez különösen hasznos az Automation runbook grafikus készítése során, ahol a tervezési idő ismerete kulcsfontosságú a modul lal való egyszerű felhasználói élményhez.

Adja `[OutputType([<MyOutputType>])]` hozzá, ahol a MyOutputType érvényes típus. Ha többet szeretne megtudni az OutputType típusról, olvassa el [a Functions OutputTypeAttribute – ismertette.](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute) A következő kód egy `OutputType` parancsmag hozzáadásának példája:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Grafikus forgatókönyv kimenettípusa](../media/modules/runbook-graphical-module-output-type.png)

  Ez a viselkedés hasonló a "type ahead" funkció egy parancsmag kimenete a PowerShell ISE futtatása nélkül.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

* A modul egyik parancsmagjának se legyen állapota. Egyszerre több runbook-feladat is futtatható ugyanabban az AppDomain ben, és ugyanabban a folyamatban és homokozóban. Ha ezen a szinten van megosztva olyan állapot, amely hatással lehet egymásra. Ez a viselkedés időszakos és nehezen diagnosztizálható problémákhoz vezethet.  Az alábbi példa bemutatja, mit ne tegyen.

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

* A modult teljes mértékben xcopy-képes csomagban kell tartani. Az Azure Automation-modulok az Automation sandboxok között vannak elosztva, amikor a runbookok végrehajtásra szorulnak. A moduloknak a ttól a gazdagéptől függetlenül kell működniük, amelyen futnak. Képesnek kell lennie a zip, és helyezze át a modulcsomagot, és azt, hogy a szokásos módon működik, ha egy másik gazdagép PowerShell-környezetbe importált. Annak érdekében, hogy ez megtörténjen, a modul nem függhet a modul mappán kívüli fájloktól. Ez a mappa az a mappa, amely a modul Azure Automationbe való importálásakor bevan csomagolva. A modul nem függhet az állomás egyedi beállításjegyzék-beállításaitól sem, például a termék telepítésekor beállított beállításoktól. A modulban lévő összes fájl elérési útja 140 karakternél rövidebb lehet. A 140 karakternél hosszabb elérési utak problémákat okoznak a runbook importálásával. Ha ez az ajánlott eljárás nem követi, a modul nem lesz használható az Azure Automationben.  

* Ha az [Azure Powershell Az-modulokat](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) a modulban hivatkozza, `AzureRM`győződjön meg arról, hogy nem hivatkozik. A `Az` modul nem használható a `AzureRM` modulokkal együtt. `Az`a runbookok támogatják, de alapértelmezés szerint nem importálják. A modulok `Az` és a figyelembe vemvanandó szempontok megismeréséhez lásd: [Az modul támogatása az Azure Automationben.](../az-modules.md)

## <a name="default-modules"></a>Alapértelmezett modulok

Az alábbi táblázat felsorolja azokat a modulokat, amelyeket az automatizálási fiók létrehozásakor alapértelmezés szerint importálnak. Az alább felsorolt modulok újabb verzióit importálhatják, de az eredeti verzió nem távolítható el az Automation-fiókból, még akkor sem, ha egy újabb verziót töröl.

|Modul neve|Verzió|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0,2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnosztika |  |
| Microsoft.PowerShell.Kezelés |  |
| Microsoft.PowerShell.Biztonság |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscForrások | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>További lépések

* További információk PowerShell-modulok létrehozásáról: [Windows PowerShell-modul írása](/powershell/scripting/developer/windows-powershell).
