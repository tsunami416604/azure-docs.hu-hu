---
title: Azure Automation-modulok kezelése
description: Ez a cikk a Azure Automation moduljainak kezelését ismerteti
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 06/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cbc6932c3bbe11f0c4def17097c1791cbb1687bf
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515901"
---
# <a name="manage-modules-in-azure-automation"></a>Azure Automation-modulok kezelése

A Azure Automation lehetővé teszi PowerShell-modulok importálását az Automation-fiókjába a PowerShell-alapú runbookok használatával. Ezek a modulok lehetnek a létrehozott egyéni modulok, a PowerShell-galéria, illetve a AzureRM és az az Azure-hoz készült modulok. Amikor létrehoz egy Automation-fiókot, a rendszer alapértelmezés szerint egyes modulokat importál.

## <a name="import-modules"></a>Modulok importálása

Több módon is importálhat modulokat az Automation-fiókjába. A következő fejezetek a modulok importálásának különböző módjait mutatják be.

> [!NOTE]
> A modulban Azure Automationként használandó fájl maximális elérési útja 140 karakter. Az 140 karakternél hosszabb útvonalak nem importálhatók a PowerShell-munkamenetbe `Import-Module`a használatával.

### <a name="powershell"></a>PowerShell

A [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) használatával modult importálhat az Automation-fiókjába. A parancsmag egy modul ZIP-csomagjának URL-címét veszi át.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Azure Portal

A Azure Portal navigáljon az Automation-fiókjához, és válassza a **modulok** elemet a **megosztott erőforrások**területen. Kattintson **a + modul hozzáadása**lehetőségre. Válassza ki a modult tartalmazó **. zip** fájlt, és kattintson **az OK** gombra az importálási folyamat megkezdéséhez.

### <a name="powershell-gallery"></a>PowerShell-galéria

A PowerShell-galériából származó modulok közvetlenül a [PowerShell-Galéria](https://www.powershellgallery.com) importálhatók, vagy az Automation-fiókjából.

Ha egy modult szeretne importálni a PowerShell-galériaból, https://www.powershellgallery.com keresse meg az importálni kívánt modult, és keressen rá. Kattintson a telepítés gombra a **Azure Automation** lapon a **telepítési beállítások**alatt **Azure Automation** . Ez a művelet megnyitja a Azure Portal. Az **Importálás** lapon válassza ki az Automation-fiókját, és kattintson az **OK**gombra.

![PowerShell-galéria importálási modul](../media/modules/powershell-gallery.png)

A PowerShell-galéria modulokat közvetlenül az Automation-fiókjából is importálhatja. Az Automation-fiókban válassza a **megosztott erőforrások**alatt található **modulok** elemet. A modulok lapon kattintson a **Tallózás**katalógus lehetőségre, majd keresse meg a modul PowerShell-Galéria. Válassza ki az importálni kívánt modult, és kattintson az **Importálás**gombra. Az importálás lapon kattintson az **OK** gombra az importálási folyamat elindításához.

![PowerShell-galéria importálás Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Modulok törlése

Ha problémája van egy modullal, vagy vissza kell állítania egy modul egy korábbi verzióját, akkor törölheti az Automation-fiókjából. Az Automation-fiók létrehozásakor importált [alapértelmezett modulok](#default-modules) eredeti verzióját nem lehet törölni. Ha a törölni kívánt modul a telepített [alapértelmezett modulok](#default-modules) egyikének újabb verziója, az Automation-fiókkal telepített verzióra visszakerül. Ellenkező esetben az Automation-fiókból törölt modulok el lesznek távolítva.

### <a name="azure-portal"></a>Azure Portal

A Azure Portal navigáljon az Automation-fiókjához, és válassza a **modulok** elemet a **megosztott erőforrások**területen. Válassza ki az eltávolítani kívánt modult. A modul lapon clcick a **delete (Törlés**) **elemre** . Ha ez a modul az egyik [alapértelmezett modul](#default-modules), a rendszer visszaállítja az Automation-fiók létrehozásakor megjelenő verzióra.

### <a name="powershell"></a>PowerShell

Modul a PowerShell használatával történő eltávolításához futtassa a következő parancsot:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Belső parancsmagok

A következő lista felsorolja a belső `Orchestrator.AssetManagement.Cmdlets` modulban lévő parancsmagokat, amelyeket minden Automation-fiókba importálnak. Ezek a parancsmagok elérhetők a runbookok és a DSC-konfigurációkban, és lehetővé teszik, hogy az Automation-fiókján belül kommunikáljon az eszközeivel. Emellett a belső parancsmagok lehetővé teszik a titkos **változók** , a **hitelesítő adatok**és a titkosított kapcsolódási mezők titkainak beolvasását. A Azure PowerShell-parancsmagok nem tudják beolvasni ezeket a titkokat. Ezek a parancsmagok nem igénylik az Azure-hoz való implicit csatlakozást a használatakor, például futtató fiók használatával az Azure-ban való hitelesítéshez.

|Name (Név)|Leírás|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start – AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Kapcsolattípus hozzáadása a modulhoz

Az Automation-fiókban egyéni [kapcsolattípust](../automation-connections.md) is megadhat, ha egy opcionális fájlt ad hozzá a modulhoz. Ez a fájl egy metaadat-fájl, amely egy Azure Automation kapcsolattípus megadását adja meg az Automation-fiókjában található modul parancsmagokkal való használathoz. Ennek eléréséhez először tudnia kell, hogyan kell létrehozni egy PowerShell-modult. További információ a modulok létrehozásáról: [PowerShell parancsfájl-modul írása](/powershell/developer/module/how-to-write-a-powershell-script-module).

![Egyéni kapcsolatok használata a Azure Portal](../media/modules/connection-create-new.png)

Azure Automation kapcsolattípus hozzáadásához a modulnak tartalmaznia kell egy olyan fájlt, amely a kapcsolattípus `<ModuleName>-Automation.json` tulajdonságait megadó nevet adja meg. A rendszer a tömörített ZIP-fájl modul mappájába helyezi a JSON-fájlt. Ez a fájl tartalmazza a kapcsolat azon mezőit, amelyek szükségesek a modul által reprezentált rendszerhez vagy szolgáltatáshoz való kapcsolódáshoz. A konfiguráció a Azure Automationban létesített kapcsolattípus létrehozásával fejeződik be. Ezzel a fájllal beállíthatja a mezők nevét, típusát, valamint azt, hogy a mezőket titkosítva vagy opcionálisan kell-e használni a modul kapcsolattípus esetén. A következő példa egy JSON-fájlformátumban található sablon, amely a Felhasználónév és a jelszó tulajdonságot definiálja:

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

## <a name="module-best-practices"></a>Modul – ajánlott eljárások

A PowerShell-modulok a Azure Automationba importálhatók, hogy a parancsmagok elérhetők legyenek a runbookok-on belüli használatra, és a DSC-erőforrásokon belül elérhetők legyenek a DSC-konfigurációkon. A színfalak mögött Azure Automation tárolja ezeket a modulokat, valamint a runbook és a DSC-fordítási feladatok végrehajtási idejét, betölti azokat a Azure Automation-munkaterületekre, ahol a runbookok-végrehajtás és a DSC-konfigurációk fordítása megtörtént. A modulok DSC-erőforrásai is automatikusan a Automation DSC lekérési kiszolgálóra kerülnek. A gépek a DSC-konfigurációk alkalmazása esetén is lehívhatják őket.

Javasoljuk, hogy vegye figyelembe az alábbiakat, amikor létrehoz egy PowerShell-modult Azure Automationban való használatra:

* Ne tartalmazzon a. zip csomagban található Version mappát.  Ez a probléma kevésbé fontos a runbookok, de az állapot-konfigurációs szolgáltatással kapcsolatos problémát okoz.  Azure Automation automatikusan létrehozza a verzió mappát, ha a modult a DSC által felügyelt csomópontok számára terjesztik, és ha létezik, akkor két példánnyal kell végződnie.  Példa a DSC-modulhoz tartozó mappastruktúrát:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* A modul összes parancsmagjáról mellékeljen egy szinopszist, egy leírást és egy súgó URI-t. A PowerShellben meghatározhat bizonyos súgóinformációkat a parancsmagokhoz, hogy a felhasználó segítséget kapjon a **Get-Help** parancsmaggal való használatukkor. Az alábbi példa bemutatja, hogyan határozhat meg egy szinopszist és egy Súgó URI-t egy. psm1-modul fájljában:

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

  Ez az információ azt mutatja be, hogy ez a Súgó a **Get-Help** parancsmag használatával jelenik meg a PowerShell-konzolon. Ez a leírás a Azure Portalban is megjelenik.

  ![Integrációs modul súgó](../media/modules/module-activity-description.png)

* Ha a modul egy külső szolgáltatáshoz csatlakozik, a [kapcsolat típusát](#add-a-connection-type-to-your-module)is tartalmaznia kell. A modul minden egyes parancsmagjának képesnek kell lennie egy kapcsolat objektum (az adott kapcsolattípus egy példánya) befogadására paraméterként. A felhasználók a kapcsolódási eszköz paramétereit a parancsmaghoz tartozó paraméterekhez rendelik a parancsmag meghívásakor. A fenti runbook példa alapján egy ContosoConnection nevű contoso-kapcsolati eszközt használ a contoso-erőforrások eléréséhez és a külső szolgáltatás adatainak visszaküldéséhez.

  A következő példában a mezők egy `PSCredential` objektum Felhasználónév és jelszó tulajdonságaira vannak leképezve, majd átadásra kerülnek a parancsmagnak.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Ennek a viselkedésnek a könnyebb és hatékonyabb megközelítése közvetlenül a következő parancsmagra továbbítja a kapcsolódási objektumot:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Az előző példához hasonló viselkedést engedélyezheti a parancsmagok számára azáltal, hogy a paramétereket közvetlenül a kapcsolódási mezők helyett paraméterként fogadja el. Általában mindegyikhez meg kell határozni egy paramétert, így a Azure Automationt nem használó felhasználók a szórótábla létrehozása nélkül hívhatják meg a parancsmagokat. A set paraméter `UserAccount`a kapcsolódási mező tulajdonságainak átadására szolgál. `ConnectionObject`lehetővé teszi a kapcsolatok közvetlen továbbítását.

* Adja meg a modul összes parancsmagjának kimeneti típusát. Egy parancsmag kimenettípusának megadása lehetővé teszi a tervezés közben az IntelliSense használatát, amely segít meghatározni egy parancsmag kimenetének tulajdonságait a megírásuk támogatására. Ez különösen hasznos az Automation runbook grafikus készítése során, ahol a tervezési idő ismerete kulcsfontosságú a modul egyszerű felhasználói élménye érdekében.

Adja `[OutputType([<MyOutputType>])]` hozzá, hogy a MyOutputType egy érvényes típus legyen. További információ a OutputType: [About functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). A következő kód egy példa a parancsmag hozzáadására `OutputType` :

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

  Ez a viselkedés hasonló a parancsmag kimenetének "type Ahead" funkcióhoz a PowerShell ISE-ben anélkül, hogy futtatni kellene.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

* A modul egyik parancsmagjának se legyen állapota. Egyszerre több runbook-feladat is futtatható ugyanabban a alkalmazástartomány, és ugyanazzal a folyamattal és homokozóval. Ha az ilyen szinteken bármilyen állapot van megosztva, a feladatok hatással lehetnek egymásra. Ez a viselkedés időszakos és nehéz problémákat okozhat a problémák diagnosztizálásában.  Az alábbi példa bemutatja, mit ne tegyen.

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

* A modult teljes egészében egy xcopy-képes csomagban kell tartalmaznia. Azure Automation modulokat a rendszer az Automation-munkaterületekre terjeszti, amikor a runbookok végre kell hajtania. A moduloknak a rendszeren futó gazdagéptől függetlenül kell dolgozniuk. Képesnek kell lennie arra, hogy elhelyezze a modult, és áthelyezzen egy modul-csomagot, és a szokásos módon működjön, ha egy másik gazdagép PowerShell-környezetébe importálja. Ahhoz, hogy ez megtörténjen, a modul nem függ a modul mappáján kívüli fájlokkal. Ez a mappa az a mappa, amely a modulnak a Azure Automationba való importálásakor bekerül a tömörített mappába. A modulnak szintén nem kell a gazdagép egyedi beállításjegyzék-beállításaitól függ, például a termék telepítésekor beállított beállításoktól. A modulban lévő összes fájlnak 140 karakternél rövidebb elérési úttal kell rendelkeznie. Az 140 karakternél hosszabb útvonalak a runbook importálásával kapcsolatos problémákat okozhatnak. Ha ez az ajánlott eljárás nem következik be, a modul nem lesz használható Azure Automationban.  

* Ha a modulban az [Azure powershellre](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) hivatkozik, győződjön meg róla, hogy nem is hivatkozik `AzureRM`rá. A `Az` modul nem használható a `AzureRM` modulokkal együtt. `Az`a runbookok támogatott, de alapértelmezés szerint nem importálja őket. Ha szeretné megtudni, `Az` hogy milyen modulokat és szempontokat kell figyelembe vennie, tekintse meg az [az modul-támogatás Azure Automationban](../az-modules.md)című témakört.

## <a name="default-modules"></a>Alapértelmezett modulok

A következő táblázat felsorolja az Automation-fiók létrehozásakor alapértelmezés szerint importált modulokat. Az alább felsorolt modulok importálhatók újabb verziókkal, de az eredeti verzió nem távolítható el az Automation-fiókjából, még akkor is, ha töröl egy újabb verziót is.

|Modul neve|Version|
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
| GPRegistryPolicyParser | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft. PowerShell. Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator. AssetManagement. parancsmagok | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>További lépések

* További információk PowerShell-modulok létrehozásáról: [Windows PowerShell-modul írása](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx).
