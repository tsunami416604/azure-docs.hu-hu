---
title: Modulok kezelése az Azure Automationben
description: Ez a cikk a Azure Automation moduljainak kezelését ismerteti.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c8d22e63be880c0cef0c4072e99ab85bf3250a1c
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114274"
---
# <a name="manage-modules-in-azure-automation"></a>Modulok kezelése az Azure Automationben

Azure Automation lehetővé teszi a PowerShell-modulok importálását a runbookok és DSC-erőforrásokban lévő parancsmagok engedélyezéséhez a DSC-konfigurációkban. A Azure Automation használt modulok a következők:

* [Azure PowerShell az. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM. Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Belső `Orchestrator.AssetManagement.Cmdlets` modul a Windows log Analytics ügynökéhez
* Egyéb PowerShell-modulok
* Létrehozott egyéni modulok 

Automation-fiók létrehozásakor a Azure Automation alapértelmezés szerint importál egyes modulokat. Lásd: [alapértelmezett modulok](#default-modules).

Amikor Azure Automation végrehajtja a runbook és a DSC fordítási feladatait, betölti a modulokat a runbookok futtatható és a DSC-konfigurációk fordítására alkalmas munkaterületekre. Az Automation emellett automatikusan elhelyez minden DSC-erőforrást a modulokban a DSC lekérési kiszolgálón. A gépek lehívhatják az erőforrásokat a DSC-konfigurációk alkalmazása során.

>[!NOTE]
>Ügyeljen arra, hogy csak azokat a modulokat importálja, amelyeket a runbookok és a DSC-konfigurációkhoz ténylegesen szükség van. Nem javasoljuk, hogy a root importálása az modult, mert számos más modult tartalmaz, amelyek esetleg nem szükségesek, ami teljesítménnyel kapcsolatos problémákat okozhat. Importálja az egyes modulokat, például az az. számítási helyet.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](../automation-update-azure-modules.md).

## <a name="default-modules"></a>Alapértelmezett modulok

A következő táblázat felsorolja az Automation-fiók létrehozásakor alapértelmezés szerint Azure Automation importálási modulokat. Az Automation képes a modulok újabb verzióinak importálására. Az eredeti verziót azonban nem távolíthatja el Automation-fiókjából, még akkor is, ha újabb verziót töröl. Vegye figyelembe, hogy ezek az alapértelmezett modulok több AzureRM-modult is tartalmaznak. 

Azure Automation nem importálja automatikusan az új vagy meglévő Automation-fiókba a gyökeret az modult. A modulok használatával kapcsolatos további információkért lásd: [áttelepítés az modulokra](#migrating-to-az-modules).

> [!NOTE]
> Nem javasoljuk, hogy módosítsa a modulokat és a runbookok olyan Automation-fiókokban, amelyek a [Azure Automation Start/Stop VMS During off-hours megoldást](../automation-solution-vm-management.md)tartalmazzák.

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
| Microsoft. PowerShell. Core | 0 |
| Microsoft. PowerShell. Diagnostics |  |
| Microsoft. PowerShell. Management |  |
| Microsoft. PowerShell. Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft. WSMan. Management |  |
| Orchestrator. AssetManagement. parancsmagok | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-module-cmdlets"></a>Az Module parancsmagok

Az az. Automation esetében a parancsmagok többsége ugyanazokkal a névvel rendelkezik, mint a AzureRM-modulok esetében, azzal a különbséggel, hogy a AzureRm előtag az az értékre módosult. Az azon modulok listáját, amelyek nem követik ezt az elnevezési konvenciót, tekintse meg [a kivételek listáját](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Belső parancsmagok

A következő táblázat a `Orchestrator.AssetManagement.Cmdlets` modul által támogatott belső parancsmagokat határozza meg. Ezeket a parancsmagokat a runbookok és a DSC-konfigurációkban használhatja az Automation-fiókon belüli Azure-eszközök használatához. A parancsmagokat úgy tervezték, hogy Azure PowerShell parancsmagok helyett a titkos változók, a hitelesítő adatok és a titkosított kapcsolatok kulcsainak beolvasására szolgálnak. 

>[!NOTE]
>A belső parancsmagok csak akkor érhetők el, ha a runbookok-t az Azure-beli homokozó környezetében vagy egy Windows Hybrid Runbook Worker szolgáltatásban futtatja. 

|Name (Név)|Leírás|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start – AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Várakozás – AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Vegye figyelembe, hogy a belső parancsmagok eltérnek az az és a AzureRM parancsmagok elnevezésével. A belső parancsmagok nevei nem tartalmaznak olyan szavakat, mint az "Azure" vagy az "az" a főnév esetében, de az "Automation" szót használják. Azt javasoljuk, hogy használja az az vagy a AzureRM parancsmag használatát az Azure-beli homokozóban vagy egy Windowsos hibrid feldolgozón a runbook végrehajtása során. Kevesebb paramétert igényelnek, és a már végrehajtás alatt álló feladatok kontextusában futnak.

Azt javasoljuk, hogy az az vagy az AzureRM parancsmagot használja a runbook környezetén kívüli Azure Automation-erőforrások kezeléséhez. 

## <a name="module-supporting-get-automationpscredential"></a>A Get-AutomationPSCredential támogató modul

A `Get-AutomationPSCredential` parancsmag a modul `Orchestrator.AssetManagement.Cmdlets`részét képezi. Ez a parancsmag egy `PSCredential` objektumot ad vissza, amelyet a legtöbb, a hitelesítő adatokkal működő PowerShell-parancsmag várt. Ha többet szeretne megtudni a hitelesítő adatok használatáról a Azure Automationban, tekintse meg a [hitelesítő adatok a Azure Automationban](credentials.md)című témakört.

## <a name="migrating-to-az-modules"></a>Áttelepítés az az modulokba

### <a name="migration-considerations"></a>A migrálás szempontjai

Ez a szakasz azokat a szempontokat ismerteti, amelyeket figyelembe kell venni az az Azure Automation-ban lévő moduljaiba való Migrálás során. Lásd még: [Azure PowerShell migrálása a AzureRM-ről az-](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0)ra. 

#### <a name="use-of-azurerm-modules-and-az-modules-in-the-same-automation-account"></a>AzureRM-modulok és az az modulok használata ugyanabban az Automation-fiókban

 Nem javasoljuk, hogy futtassa az AzureRM modulokat és az az modulokat ugyanabban az Automation-fiókban. Ha biztos abban, hogy át szeretné telepíteni a AzureRM-ről az az-ra, a legjobb megoldás, ha teljes mértékben véglegesíti a teljes áttelepítést. Ennek a legfontosabb oka az, hogy Azure Automation gyakran újrahasználja az Automation-fiókban lévő munkaterületeket az indítási időpontokban való mentéshez. Ha nem hajtja végre a modul teljes áttelepítését, elindíthat egy feladatot csak AzureRM-modulok használatával, majd egy másik feladatot is indíthat, csak az az modulok használatával. A homokozó hamarosan összeomlik, és végzetes hibaüzenetet kap arról, hogy a modulok nem kompatibilisek. Ez a helyzet véletlenszerűen előforduló összeomlásokat eredményez bármely adott runbook vagy konfigurációnál. 

#### <a name="import-of-az-modules-into-the-powershell-session"></a>Az az modulok importálása a PowerShell-munkamenetbe

Az az modul az Automation-fiókba való importálása nem importálja automatikusan a modult a runbookok által használt PowerShell-munkamenetbe. A modulok a következő helyzetekben importálhatók a PowerShell-munkamenetbe:

* Amikor egy runbook meghívja a parancsmagot egy modulból
* Ha egy runbook explicit módon importálja a modult az [import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7) parancsmaggal
* Ha egy runbook egy másik függő modult importál
    
#### <a name="testing-for-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>A runbookok és a DSC-konfigurációk tesztelése a modul áttelepítése előtt

Ügyeljen arra, hogy alaposan tesztelje az összes runbookok és DSC-konfigurációt egy külön Automation-fiókban, mielőtt áttelepíti az az modulba. 

#### <a name="updates-for-tutorial-runbooks"></a>Az oktatóanyag runbookok frissítései 

Amikor új Automation-fiókot hoz létre, akkor is, ha az az modulba való áttelepítést követően Azure Automation telepíti a AzureRM modulokat. Az oktatóanyag runbookok továbbra is frissítheti az AzureRM-parancsmagokkal. Ezeket a runbookok azonban nem kell futtatnia.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Az összes AzureRM-modult használó runbookok leállítása és visszaírása

Annak biztosítása érdekében, hogy ne futtasson olyan meglévő runbookok-vagy DSC-konfigurációkat, amelyek AzureRM-modulokat használnak, le kell állítania és el kell indítania az összes érintett runbookok és konfigurációt. Először is győződjön meg arról, hogy az egyes runbook-vagy DSC-konfigurációkat és azok ütemezett beállításait külön tekinti át, hogy a későbbiekben szükség esetén újraütemezze az elemeket. 

Ha készen áll az ütemtervek eltávolítására, használhatja a Azure Portal vagy a [Remove-AzureRmAutomationSchedule](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0) parancsmagot. Lásd: [az ütemterv eltávolítása](schedules.md#removing-a-schedule).

### <a name="remove-the-azurerm-modules"></a>A AzureRM modulok eltávolítása

Az az modulok importálása előtt el lehet távolítani a AzureRM modulokat. A AzureRM modulok törlése azonban megszakíthatja a verziókövetés szinkronizálását, és a még sikertelenül ütemezett parancsfájlokat is okozhatja. Ha úgy dönt, hogy eltávolítja a modulokat, tekintse meg a [AzureRM eltávolítása](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.8.0#uninstall-azurerm)című témakört.

### <a name="import-the-az-modules"></a>Az az modulok importálása

Ez a szakasz azt ismerteti, hogyan importálhatja az az modulokat a Azure Portalban. Ne feledje, hogy csak a szükséges modulokat importálja, nem a teljes az. Automation-modult. Mivel az [az. accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) a többi az modultól való függőség, ügyeljen arra, hogy a modult minden más előtt importálja.

1. Az Automation-fiókból válassza a **modulok** elemet a **megosztott erőforrások**területen. 
2. Kattintson a **Tallózás** a katalógusban elemre a tallózási katalógus lap megnyitásához.  
3. A keresősáv mezőben adja meg a modul nevét, például: `Az.Accounts`. 
4. A PowerShell-modul lapon kattintson az **Importálás** elemre a modul Automation-fiókba való importálásához.

    ![Modulok importálása az Automation-fiókba](../media/modules/import-module.png)

Ez az importálási folyamat a [PowerShell-galériaon](https://www.powershellgallery.com) is elvégezhető, ha az importálni kívánt modulra keres rá. Miután megtalálta a modult, válassza ki azt, válassza a **Azure Automation** lapot, majd kattintson a telepítés elemre **Azure Automation**.

![Modulok importálása közvetlenül a katalógusból](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>A runbookok tesztelése

Miután importálta az az modulokat az Automation-fiókba, megkezdheti a runbookok és a DSC-konfigurációk szerkesztését az új modulok használatához. A runbook módosításának az új parancsmagok használatára való tesztelésének egyik módja a runbook elején használatos `Enable-AzureRmAlias -Scope Process` . Ha hozzáadja ezt a parancsot a runbook, a parancsfájl módosítása nélkül is futtatható. 

## <a name="authoring-modules"></a>Modulok készítése

Javasoljuk, hogy kövesse az ebben a szakaszban ismertetett szempontokat, amikor egy PowerShell-modult hoz létre Azure Automationban való használatra.

### <a name="version-folder"></a>Verzió mappája

A modulhoz tartozó **. zip** csomagban ne adjon meg egy Version mappát.  Ez a probléma kevésbé fontos a runbookok, de az állapot-konfiguráció (DSC) szolgáltatással kapcsolatos problémát okoz. A Azure Automation automatikusan létrehozza a verzió mappát, ha a modult a DSC által felügyelt csomópontok számára terjeszti. Ha létezik egy verziószám, két példánnyal kell végződnie. Íme egy példa a DSC-modulhoz tartozó mappastruktúrát:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Súgó információi

Adja meg a modul összes parancsmagjának szinopszisát, leírását és súgóját. A PowerShellben a `Get-Help` parancsmag használatával megadhatja a parancsmagok súgójának adatait. Az alábbi példa bemutatja, hogyan határozhat meg egy szinopszist és egy Súgó URI-t egy **. psm1** modul-fájlban.

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

  Az információ megadása a PowerShell-konzolon `Get-Help` található parancsmagon keresztül jeleníti meg a Súgó szövegét. Ez a szöveg a Azure Portal is megjelenik.

  ![Integrációs modul súgó](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Kapcsolat típusa

Ha a modul külső szolgáltatáshoz csatlakozik, adjon meg egy [kapcsolattípust](#adding-a-connection-type-to-your-module). A modul minden egyes parancsmagjának paraméterként el kell fogadnia egy, a kapcsolattípus egy példányát. A felhasználók a kapcsolódási eszköz paramétereit a parancsmaghoz tartozó paraméterekhez rendelik a parancsmag meghívásakor. Az alábbi runbook példa az előző szakasz példája alapján egy nevű `ContosoConnection` contoso-kapcsolódási eszközt használ a contoso-erőforrások eléréséhez, és a külső szolgáltatástól származó adatok visszaküldéséhez. Ebben a példában a mezők egy `UserName` `Password` `PSCredential` objektum és tulajdonságaira vannak leképezve, majd át lesznek adva a parancsmagnak.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Ennek a viselkedésnek a könnyebb és hatékonyabb megközelítése az, ha közvetlenül átadja a kapcsolódási objektumot a parancsmagnak:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  A parancsmagok hasonló viselkedését engedélyezheti, ha lehetővé teszi, hogy közvetlenül paraméterként fogadják el a kapcsolódási objektumokat, ahelyett, hogy a paraméterekhez csak a kapcsolódási mezőket. Általában mindegyikhez meg kell határozni egy paramétert, így a Azure Automationt nem használó felhasználók a szórótábla létrehozása nélkül hívhatják meg a parancsmagokat. A set `UserAccount` paraméter a kapcsolódási mező tulajdonságainak továbbítására szolgál. `ConnectionObject`lehetővé teszi a kapcsolatok közvetlen továbbítását.

### <a name="output-type"></a>Kimenet típusa

Adja meg a modul összes parancsmagjának kimeneti típusát. A parancsmagok kimeneti típusának meghatározása lehetővé teszi a tervezési idejű IntelliSense használatát a parancsmag kimeneti tulajdonságainak meghatározásához a szerzői műveletek során. Ez a gyakorlat különösen hasznos a grafikus runbook készítése során, amelynek a tervezési idejű ismerete kulcsfontosságú a modul egyszerű felhasználói élményéhez.

`MyOutputType` Adjon hozzá `[OutputType([<MyOutputType>])]` egy érvényes típust. További információ `OutputType`: [About functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). A következő kód egy példa a parancsmag hozzáadására `OutputType` :

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

### <a name="cmdlet-state"></a>Parancsmag állapota

Az összes parancsmagot állapotba kell tenni a modulban. Egyszerre több runbook-feladat is futtatható ugyanabban `AppDomain` a folyamatban és a homokozóban. Ha az ilyen szinteken bármilyen állapot van megosztva, a feladatok hatással lehetnek egymásra. Ez a viselkedés időszakos és nehezen diagnosztizálható problémákhoz vezethet. Íme egy példa arra, hogy mi a teendő:

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

### <a name="module-dependency"></a>Modul függősége

Győződjön meg arról, hogy a modul teljes mértékben megtalálható az xcopy használatával másolható csomagban. Azure Automation modulokat a rendszer a runbookok végrehajtásakor terjeszti az automatizálási munkaterületekre. A moduloknak az azokat futtató gazdagéptől függetlenül kell működniük. 

Képesnek kell lennie arra, hogy elhelyezze a modult, és áthelyezzen egy modul-csomagot, és a szokásos módon működjön, ha egy másik gazdagép PowerShell-környezetébe importálja. Ehhez győződjön meg arról, hogy a modul nem függ a modul mappáján kívüli fájlokkal, amelyek a modul Azure Automationba való importálásakor vannak kitömörítve. 

A modul nem függhet a gazdagép egyedi beállításjegyzék-beállításaitól. Ilyenek például a termék telepítésekor végrehajtott beállítások. 

### <a name="module-file-paths"></a>Modul fájlelérési útjai

Győződjön meg arról, hogy a modulban lévő összes fájl 140 karakternél rövidebb elérési úttal rendelkezik. Az 140 karakternél hosszabb útvonalak a runbookok importálásával kapcsolatos problémákat okozhatnak. A Azure Automation nem tud olyan fájlt importálni, amelynek elérési útja 140 karakternél hosszabb `Import-Module`a PowerShell-munkamenetben.

## <a name="importing-modules"></a>Modulok importálása

Ez a szakasz számos módszert határoz meg, amelyekkel modulokat importálhat az Automation-fiókjába. 

### <a name="import-modules-in-azure-portal"></a>Modulok importálása Azure Portal

Modul importálása a Azure Portalban:

1. Navigáljon az Automation-fiókjához.
2. Válassza a **modulok** elemet a **megosztott erőforrások**területen.
3. Kattintson **a modul hozzáadása**lehetőségre. 
4. Válassza ki a modult tartalmazó **. zip** fájlt.
5. Kattintson **az OK** gombra az importálási folyamat megkezdéséhez.

### <a name="import-modules-using-powershell"></a>Modulok importálása a PowerShell használatával

A [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) parancsmag használatával egy modult importálhat az Automation-fiókjába. A parancsmag egy Module. zip-csomag URL-címét veszi át.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Ugyanezt a parancsmagot használhatja arra is, hogy PowerShell-galéria közvetlenül importáljon egy modult. Győződjön meg arról, `ModuleName` hogy `ModuleVersion` megragad és [PowerShell-Galéria](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>Modulok importálása PowerShell-galériaból

[PowerShell-Galéria](https://www.powershellgallery.com) modulokat közvetlenül a katalógusból vagy Automation-fiókjából is importálhatja.

Modul importálása közvetlenül a PowerShell-galériaról:

1. Keresse meg https://www.powershellgallery.com az importálni kívánt modult, és keresse meg.
2. Kattintson a telepítés gombra a **Azure Automation** lapon a **telepítési beállítások**alatt **Azure Automation** . Ez a művelet megnyitja a Azure Portal. 
3. Az importálás lapon válassza ki az Automation-fiókját, és kattintson az **OK**gombra.

![PowerShell-galéria importálási modul](../media/modules/powershell-gallery.png)

PowerShell-galéria modul importálása közvetlenül az Automation-fiókból:

1. Válassza a **modulok** elemet a **megosztott erőforrások**területen. 
2. A modulok lapon kattintson a **Tallózás**katalógus lehetőségre, majd keresse meg a katalógusban a modult. 
3. Válassza ki az importálni kívánt modult, majd kattintson az **Importálás**gombra. 
4. Az importálás lapon kattintson az **OK** gombra az importálási folyamat elindításához.

![PowerShell-galéria importálás Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Modulok törlése

Ha problémája van egy modullal, vagy vissza kell állítania egy modul egy korábbi verzióját, akkor törölheti az Automation-fiókjából. Az Automation-fiók létrehozásakor importált [alapértelmezett modulok](#default-modules) eredeti verziói nem törölhetők. Ha a törlendő modul az egyik [alapértelmezett modul](#default-modules)újabb verziója, a rendszer Visszagörgeti az Automation-fiókjával telepített verzióra. Ellenkező esetben a rendszer eltávolítja az Automation-fiókból törölt modulokat.

### <a name="delete-modules-in-azure-portal"></a>Modulok törlése Azure Portal

Modul eltávolítása a Azure Portalban:

1. Navigáljon az Automation-fiókjához, és válassza a **modulok** elemet a **megosztott erőforrások**területen. 
2. Válassza ki az eltávolítani kívánt modult. 
3. A **modul** lapon válassza a **Törlés**lehetőséget. Ha ez a modul az [alapértelmezett modulok](#default-modules)egyike, az visszagörget az Automation-fiók létrehozásakor létezett verzióra.

### <a name="delete-modules-using-powershell"></a>Modulok törlése a PowerShell használatával

Modul a PowerShell használatával történő eltávolításához futtassa a következő parancsot:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Kapcsolattípus hozzáadása a modulhoz

Az Automation-fiókban használható egyéni [kapcsolattípust](../automation-connections.md) egy opcionális metaadat-fájl hozzáadásával is megadhatja a modulhoz. Ez a fájl egy Azure Automation kapcsolattípus használatát határozza meg, amelyet az Automation-fiókjában található modul-parancsmagokkal szeretne használni. Ha többet szeretne megtudni a PowerShell-modulok létrehozásáról, tekintse meg [a PowerShell parancsfájl-modul írása](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7)című témakört.

![Egyéni kapcsolatok használata a Azure Portal](../media/modules/connection-create-new.png)

A kapcsolattípus-tulajdonságokat megadó fájl neve ** &lt;ModuleName&gt;-Automation. JSON** , és a tömörített **. zip** fájl modul mappájában található. Ez a fájl tartalmazza a kapcsolat azon mezőit, amelyek szükségesek a modul által reprezentált rendszerhez vagy szolgáltatáshoz való kapcsolódáshoz. A konfiguráció lehetővé teszi a kapcsolattípus létrehozását a Azure Automationban. A fájl használatával megadhatja a mezők nevét, típusát, valamint azt, hogy a mezők titkosítva vannak-e, vagy választható-e a modul kapcsolattípus. A következő példa egy **. JSON** fájlformátumú sablon, amely meghatározza a Felhasználónév és a jelszó tulajdonságait:

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

## <a name="next-steps"></a>További lépések

* Azure PowerShell modulok használatával kapcsolatos további információkért lásd: [az Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0)használatának első lépései.
* További információ a PowerShell-modulok létrehozásáról: [Windows PowerShell-modul írása](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
