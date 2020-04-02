---
title: Modulok kezelése az Azure Automationben
description: Ez a cikk ismerteti, hogyan kezelheti a modulokat az Azure Automationben.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 859eea66d10e07a3503e33166bc77c8a97577acd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548937"
---
# <a name="manage-modules-in-azure-automation"></a>Modulok kezelése az Azure Automationben

Az Azure Automation lehetővé teszi, hogy a PowerShell-modulok importálása az Automation-fiókba a PowerShell-alapú runbookok által használható. Ezek a modulok lehetnek egyéni modulok, létrehozott, modulok a PowerShell-galériában, vagy az AzureRM és az Az modulok az Azure-hoz. Automation-fiók létrehozásakor a rendszer alapértelmezés szerint importál bizonyos modulokat.

## <a name="import-modules"></a>Modulok importálása

Többmódon is importálhat egy modult az Automation-fiókba. A következő szakaszok a modul importálásának különböző módjait mutatják be.

> [!NOTE]
> Az Azure Automationben használt modulban lévő fájlok maximális elérési útja 140 karakter. Az Automation nem tud 140 karakternél hosszabb elérési útméretű `Import-Module`fájlt importálni a PowerShell-munkamenetbe.

### <a name="powershell"></a>PowerShell

A [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) parancsmag használatával importálhat egy modult az Automation-fiókba. A parancsmag egy modul .zip csomag URL-címét veszi fel.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Ugyanazzal a parancsmagmal is importálhat egy modult közvetlenül a PowerShell-galériából. Győződjön meg `ModuleName` `ModuleVersion` róla, hogy megragad, és a [PowerShell Galéria](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Azure Portal

Modul importálása az Azure Portalon:

1. Nyissa meg az Automation-fiókot.
2. Válassza a **Modulok lehetőséget a** Megosztott erőforrások **csoportban.**
3. Kattintson **a Modul hozzáadása**gombra. 
4. Jelölje ki a modult tartalmazó **.zip** fájlt.
5. A folyamat importálásának megkezdéséhez kattintson az **OK** gombra.

### <a name="powershell-gallery"></a>PowerShell-galéria

Importálhatja a [PowerShell-galéria](https://www.powershellgallery.com) moduljait közvetlenül a katalógusból vagy az Automation-fiókból.

Modul importálása közvetlenül a PowerShell-galériából:

1. Lépjen https://www.powershellgallery.com az importálni a modulra, és keresse meg a modult.
2. Kattintson a **Telepítés az Azure Automation szolgáltatásba** elemre az Azure **Automation** lapon a Telepítési **beállítások csoportban.** Ez a művelet megnyitja az Azure Portalon. 
3. Az Importálás lapon jelölje ki az Automation-fiókot, és kattintson **az OK**gombra.

![A PowerShell-galéria importálási modulja](../media/modules/powershell-gallery.png)

PowerShell-katalógusmodul importálása közvetlenül az Automation-fiókból:

1. Válassza a **Modulok lehetőséget a** Megosztott erőforrások **csoportban.** 
2. A Modulok lapon kattintson a **Galéria tallózása**gombra, majd keressen egy modult a gyűjteményben. 
3. Jelölje ki az importálni kívánt modult, és kattintson **az Importálás gombra.** 
4. Az Importálás lapon kattintson az **OK** gombra az importálási folyamat elindításához.

![PowerShell-galéria importálása az Azure Portalról](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Modulok törlése

Ha problémái vannak egy modullal, vagy vissza kell visszavonnia egy modul egy korábbi verziójára, törölheti azt az Automation-fiókból. Az automation-fiók létrehozásakor importált alapértelmezett modulok eredeti [verziói](#default-modules) nem törölhetők. Ha a törlött modul az egyik [alapértelmezett modul](#default-modules)újabb verziója, akkor visszaáll az Automation-fiókkal telepített verzióra. Ellenkező esetben az Automation-fiókból törölt modulok törlődnek.

### <a name="azure-portal"></a>Azure Portal

Modul eltávolítása az Azure Portalon:

1. Nyissa meg az Automation-fiókot, és válassza **a Modulok lehetőséget a** Megosztott erőforrások **csoportban.** 
2. Jelölje ki az eltávolítani kívánt modult. 
3. A **Modul** lapon válassza a **Törlés**lehetőséget. Ha ez a modul az [egyik alapértelmezett modul,](#default-modules)akkor visszaáll arra a verzióra, amely az Automation-fiók létrehozásakor létezett.

### <a name="powershell"></a>PowerShell

Ha el szeretne távolítani egy modult a PowerShellen keresztül, futtassa a következő parancsot:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Belső parancsmagok

Az alábbi táblázat felsorolja a `Orchestrator.AssetManagement.Cmdlets` parancsmagokat a belső modulban, amely minden Automation-fiókba importálva van. Ezek a parancsmagok a runbookok és a DSC-konfigurációkban érhetők el, és lehetővé teszik az Automation-fiókon belüli eszközökkel való interakciót. Emellett a belső parancsmagok lehetővé teszik a titkos kulcsok lekérését a titkosított változókból, hitelesítő adatokból és titkosított kapcsolatokból. Az Azure PowerShell-parancsmagok nem tudják beolvasni ezeket a titkos kulcsokat. Ezek a parancsmagok nem követelik meg, hogy implicit módon csatlakozzon az Azure-hoz, amikor használja őket, mint amikor egy Futtatás más fiók használatával hitelesíti az Azure-ban.

>[!NOTE]
>Ezek a belső parancsmagok a Windows hibrid Runbook-feldolgozó, de nem a Linux hibrid Runbook-feldolgozó. Használja a megfelelő [AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) vagy [Az modul](../az-modules.md) parancsmagjait a közvetlenül a számítógépen vagy a környezetben lévő erőforrásokon futó runbookokhoz. 

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

Az Automation-fiókban használható egyéni [kapcsolattípust](../automation-connections.md) úgy adhat meg, hogy egy opcionális metaadatfájlt ad a modulhoz. Ez a fájl egy Azure Automation-kapcsolattípust ad meg, amelyet a modul parancsmagjaihoz kell használni az Automation-fiókban. Ennek eléréséhez először tudnia kell, hogyan kell egy PowerShell-modult. Lásd: [PowerShell-parancsfájlmodul írása.](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)

![Egyéni kapcsolat használata az Azure Portalon](../media/modules/connection-create-new.png)

A kapcsolattípus-tulajdonságokat megadó fájl neve ** &lt;&gt;ModuleName -Automation.json,** és a tömörített **.zip** fájl modulmappájában található. Ez a fájl a modul által képviselt rendszerhez vagy szolgáltatáshoz való csatlakozáshoz szükséges kapcsolat mezőit tartalmazza. A konfiguráció lehetővé teszi egy kapcsolattípus létrehozását az Azure Automationben. Ezzel a fájllal beállíthatja a mezőneveket, típusokat, valamint azt, hogy a mezők titkosítottak vagy választhatók-e a modul kapcsolattípusához. A következő példa egy **.json** fájlformátumú sablon, amely meghatározza a felhasználónév és a jelszó tulajdonságait:

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

PowerShell-modulok importálása az Azure Automationbe, hogy a parancsmagok elérhetők legyenek a runbookokon és a DSC-erőforrásokon belül a DSC-konfigurációkban. A színfalak mögött az Azure Automation tárolja ezeket a modulokat. A runbook-feladat és a DSC fordítási feladat végrehajtási ideje alatt az Automation betölti őket az Azure Automation sandboxokba, ahol runbookok futnak, és a DSC-konfigurációk lefordítják. A modulokban lévő DSC-erőforrások is automatikusan az Automation DSC lekéréses kiszolgálóra kerülnek. A gépek lekérhetik őket, amikor DSC-konfigurációkat alkalmaznak.

Javasoljuk, hogy vegye figyelembe a következőket, amikor egy PowerShell-modult hoz az Azure Automationben való használatra:

* NE vegyen fel verziómappát a **.zip** csomagba.  Ez a probléma kevésbé érinti a runbookokat, de problémát okoz az Állapotkonfiguráció szolgáltatással. Az Azure Automation automatikusan létrehozza a verziómappát, amikor a modult a DSC által kezelt csomópontokra terjesztik. Ha létezik verziómappa, akkor két példány jelenik meg. Íme egy példa egy DSC-modul mappastruktúrájára:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* A modul összes parancsmagjáról mellékeljen egy szinopszist, egy leírást és egy súgó URI-t. A PowerShellben a `Get-Help` parancsmagok segítségével adhat meg súgóinformációkat. A következő példa bemutatja, hogyan definiálható szinopszis és súgó URI egy **.psm1** modulfájlban:

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

  Ennek az információnak a `Get-Help` megadása a PowerShell-konzol parancsmagján keresztül imitáló súgószöveget jelenít meg. Ez a szöveg is megjelenik az Azure Portalon.

  ![Integrációs modul súgó](../media/modules/module-activity-description.png)

* Ha a modul külső szolgáltatáshoz csatlakozik, adjon meg [egy kapcsolattípust](#add-a-connection-type-to-your-module). A modul minden parancsmacájának paraméterként el kell fogadnia egy kapcsolatobjektumot (a kapcsolattípus egy példányát). A felhasználók a csatlakozási eszköz paramétereit a parancsmag megfelelő paramétereihez képezik le minden alkalommal, amikor egy parancsmag hívását meghívják. A fenti runbook-példa alapján egy példa Contoso-kapcsolati eszközt használ a Contoso-erőforrások `ContosoConnection` eléréséhez és a külső szolgáltatásból származó adatok visszaadására.

  A következő példában a mezők le `UserName` vannak `Password` képezve `PSCredential` egy objektum és tulajdonságaihoz, majd átkerülnek a parancsmagba.

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

  A parancsmagok hasonló viselkedését engedélyezheti, ha lehetővé teszi számukra, hogy közvetlenül egy kapcsolatobjektumot fogadjanak el paraméterként, és ne csak a paraméterek csatlakozási mezőit. Általában szeretne egy paraméterkészletet minden, hogy az Azure Automation-t nem használó felhasználó meghívhatja a parancsmagokat anélkül, hogy egy kivonatot hozna létre, hogy kapcsolatobjektumként működjön. A paraméterkészlet `UserAccount`, a kapcsolatmező tulajdonságainak átadására szolgál. `ConnectionObject`lehetővé teszi, hogy a kapcsolatot egyenesen átadja.

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

* A modult teljes mértékben xcopy-képes csomagban kell tartani. Az Azure Automation-modulok az Automation sandboxok között vannak elosztva, amikor a runbookok végrehajtásra szorulnak. A moduloknak a ttól a gazdagéptől függetlenül kell működniük, amelyen futnak. Képesnek kell lennie arra, hogy zip-fel, és helyezze át a modulcsomagot, és azt, hogy a szokásos módon működik, ha egy másik gazdagép PowerShell-környezetbe importált. Ehhez a modul nem függhet a modulmappán kívüli fájloktól, amelyek a modul Azure Automationbe importálásakor vannak tömörítve. A modul nem függhet az állomás egyedi beállításjegyzék-beállításaitól sem, például a termék telepítésekor beállított beállításoktól. A modulban lévő összes fájl elérési útja 140 karakternél rövidebb lehet. A 140 karakternél hosszabb elérési utak problémákat okoznak a runbook importálásával kapcsolatban. Ha nem követi ezt az ajánlott eljárás, a modul nem használható az Azure Automationben.  

* Ha az [Azure PowerShell Az modulra](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) hivatkozik a modulban, győződjön meg arról, hogy nem hivatkozik. `AzureRM` A modul nem `Az` használható a `AzureRM` modullal együtt. `Az`runbookok támogatják, de alapértelmezés szerint nem importálják. A modulról `Az` és a figyelembe vemvanandó szempontokról az [Az modul támogatása az Azure Automationben](../az-modules.md)című témakörben olvashat.

## <a name="default-modules"></a>Alapértelmezett modulok

Az alábbi táblázat az automation-fiók létrehozásakor alapértelmezés szerint importált modulokat sorolja fel. Az automatizálás a modulok újabb verzióit importálhatja. Az eredeti verziót azonban nem távolíthatja el az Automation-fiókból, még akkor sem, ha újabb verziót töröl.

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

* A PowerShell-modulok létrehozásáról a [Windows PowerShell-modul írása](/powershell/scripting/developer/windows-powershell)című témakörben olvashat bővebben.
