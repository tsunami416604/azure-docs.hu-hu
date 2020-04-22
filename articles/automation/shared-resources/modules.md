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
ms.openlocfilehash: 2a567b156c3a7e0c48e2c2f8db2d268e0eb3307c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770951"
---
# <a name="manage-modules-in-azure-automation"></a>Modulok kezelése az Azure Automationben

Az Azure Automation lehetővé teszi a PowerShell-modulok importálását a parancsmagok engedélyezéséhez a runbookokban és a DSC-erőforrásokban DSC-konfigurációkban. Az Azure Automationben használt modulok a következők:

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM.Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* A `Orchestrator.AssetManagement.Cmdlets` Windows Log Analytics ügynökének belső modulja
* [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)
* Egyéb PowerShell-modulok
* Létrehozott egyéni modulok 

Automation-fiók létrehozásakor az Azure Automation alapértelmezés szerint importál néhány modult. Lásd: [Alapértelmezett modulok](#default-modules).

Amikor az Azure Automation runbook és DSC fordítási feladatokat hajt végre, a modulokat homokozókba tölti be, ahol a runbookok futtathatók, és a DSC-konfigurációk lefordíthatók. Az automatizálás automatikusan elhelyezi a DSC-erőforrásokat a DSC lekéréses kiszolgálón lévő modulokban is. A gépek lekérhetik az erőforrásokat a DSC-konfigurációk alkalmazásakor.

>[!NOTE]
>Győződjön meg arról, hogy csak azokat a modulokat importálja, amelyeket a runbookok és a DSC-konfigurációk ténylegesen szüksége van. Ne importálja az összesítő modult, például az Az.Automation, minden esetben.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](../automation-update-azure-modules.md)

## <a name="default-modules"></a>Alapértelmezett modulok

Az alábbi táblázat azokat a modulokat sorolja fel, amelyeket az Azure Automation alapértelmezés szerint importál az Automation-fiók létrehozásakor. Az automatizálás a modulok újabb verzióit importálhatja. Az eredeti verziót azonban nem távolíthatja el az Automation-fiókból, még akkor sem, ha újabb verziót töröl. Vegye figyelembe, hogy ezek az alapértelmezett modulok több AzureRM-modult tartalmaznak. 

> [!NOTE]
> Nem javasoljuk a modulok és runbookok módosítását az Automation-fiókokban, amelyek bármilyen megoldást tartalmaznak. 

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


Az.Automation-modulok a runbookok és a DSC-konfigurációk előnyben részesítése. Az Azure Automation azonban nem importálja automatikusan az Összesítő Az modult egyetlen új vagy meglévő Automation-fiókba sem. A modulok ról további tudnivalókért [lásd: Áttelepítés az Az modulokba.](#migrating-to-az-modules)

## <a name="internal-cmdlets"></a>Belső parancsmagok

Az alábbi táblázat a `Orchestrator.AssetManagement.Cmdlets` modul által támogatott belső parancsmagokat határozza meg. Ezeket a runbookok és a DSC-konfigurációk segítségével kezelheti az Automation-eszközök az Automation-fiókon belül. A parancsmagok célja, hogy lekérje a titkos kulcsokat a titkosított változók, hitelesítő adatok és titkosított kapcsolatok. 

> [!NOTE]
> Az Azure PowerShell-parancsmagok nem szerezhetők be a belső parancsmagok által lekérhető eszköztitkokat. 

|Name (Név)|Leírás|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob (Várakozás-automatizálási feladat)|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Ezek a belső parancsmagok a Windows hibrid Runbook-feldolgozó, de nem a Linux hibrid Runbook-feldolgozó. Egy Orchestrator sandboxból vannak kivégezve, amelyet a hibrid feldolgozó használ.  Használatuk nem igényel implicit kapcsolatot az Azure-ral, mint a Futtatás másként fiók használata a hitelesítéshez.

A belső parancsmagok használata helyett használja az Az vagy Az RM parancsmagokat a közvetlenül a számítógépen vagy a környezetben lévő erőforrásokon futó runbookokhoz és konfigurációkhoz. Ezekben az esetekben implicit módon csatlakoznia kell az Azure-hoz a parancsmagok használatakor, mint amikor egy Futtatás másként fiókot használ az Azure-ba való hitelesítéshez. 

## <a name="modules-supporting-get-automationpscredential"></a>Get-AutomationPSCredential-t támogató modulok

Az Azure Automation authoring toolkit használatával `Get-AutomationPSCredential` történő helyi fejlesztéshez a parancsmag az [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)szerelvény része. Az Automation környezettel dolgozó Azure számára a `Orchestrator.AssetManagement.Cmdlets`parancsmag a. Ha többet szeretne megtudni a hitelesítő adatok Azure Automation-ben való használatáról, olvassa el a Hitelesítő adatok az Azure Automationben című [témakört.](credentials.md)

## <a name="migrating-to-az-modules"></a>Áttelepítés Az modulokra

Az Azure Automation Ben az Az modulok használatakor számos szempontot figyelembe kell venni:

* Az Automation-fiók magasabb szintű megoldásai runbookokat és modulokat használhatnak. Ezért a runbookok szerkesztése vagy a modulok frissítése problémákat okozhat a megoldásokkal. Az Az modulok importálása előtt gondosan tesztelje az összes runbookot és megoldást egy külön Automation-fiókban. 

* A modulok bármilyen módosítása negatívan befolyásolhatja a [virtuális gépek indítási/leállítási/leállítási idejét .](../automation-solution-vm-management.md) 

* Az modul importálása az Automation-fiókba nem importálja automatikusan a modult a Runbookok által használt PowerShell-munkamenetben. A modulok a következő esetekben lesznek importálva a PowerShell-munkamenetbe:

    * Amikor egy runbook meghívja a modul parancsmagját
    * Amikor egy runbook kifejezetten importálja a modult a `Import-Module` parancsmaggal
    * Amikor egy runbook importál egy másik függő modult

Miután befejezte a modulok áttelepítését, ne próbálja meg elindítani a runbookok azureRM-modulok használatával az Automation-fiók. Azt is javasoljuk, hogy ne importálja vagy frissítse az AzureRM-modulokat a fiókban. Vegye figyelembe a fiókot áttelepített Az.Automation, és csak az Az modulok használata. 

>[!IMPORTANT]
>Új Automation-fiók létrehozásakor az Azure Automation alapértelmezés szerint telepíti az AzureRM-modulokat. Továbbra is frissítheti az oktatóanyag runbookok AzureRM-parancsmagokkal. Azonban nem kell futtatni ezeket a runbookokat.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Az AzureRM-modulokat használó összes runbook leállítása és ütemezésének leállítása

Annak érdekében, hogy ne futtasson azureRM-modulokat használó meglévő runbookokat, állítsa le és ne ütemezze az összes érintett runbookot. A példához hasonló kód futtatásával megtekintheti, hogy milyen ütemezések léteznek, és mely ütemezéseket kell eltávolítani:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Fontos, hogy az egyes ütemezéseket külön-külön tekintse át, hogy szükség esetén a runbookok hoz a jövőben átütemezheti.

### <a name="import-the-az-modules"></a>Az Az modulok importálása

Ez a szakasz bemutatja, hogyan importálhatja az Az modulokat az Azure Portalon. Ne feledje, hogy csak a szükséges Az modulokat importálja, a teljes Az.Automation modult nem. Mivel az [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) függőség a többi Az modul, győződjön meg róla, hogy importálja ezt a modult, mielőtt bármely más.

1. Az Automation-fiókban válassza a **Modulok lehetőséget a** Megosztott erőforrások **csoportban.** 
2. Kattintson a **Galéria tallózása** gombra a Galéria tallózása lap megnyitásához.  
3. A keresősávban adja meg a modul `Az.Accounts`nevét, például . 
4. A PowerShell-modul lapon kattintson az **Importálás gombra** a modul automatizálási fiókba importálásához.

    ![Modulok importálása automation-fiókba](../media/modules/import-module.png)

Ez az importálási folyamat a [PowerShell-galérián](https://www.powershellgallery.com) keresztül is elvégezhető az importálandó modul keresésével. Miután megtalálta a modult, válassza ki, válassza az **Azure Automation** fület, és kattintson az Üzembe helyezés az **Azure Automationben parancsra.**

![Modulok importálása közvetlenül a galériából](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>A runbookok tesztelése

Miután importálta az Az modulokat az Automation-fiókba, elkezdheti szerkeszteni a runbookokat az új modulok használatához. A parancsmagok többsége ugyanazokkal a nevekkel rendelkezik, mint az AzureRM-modulok, azzal a különbséggel, hogy az AzureRM (vagy AzureRm) előtag az Az.The majority of the cmdlets have the same names as for the AzureRM modules, except that the AzureRM (or AzureRm) prefix has been changed to A. Az elnevezési konvenciót nem követő modulok listáját [a kivételek listája](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)tartalmazza.

A runbook új parancsmagok használatára történő módosításának tesztelésének egyik módja a runbook elején történő használat. `Enable-AzureRmAlias -Scope Process` Ha hozzáadja ezt a parancsot a runbookhoz, a parancsfájl módosítások nélkül futtatható.

## <a name="authoring-modules"></a>Szerzői modulok

Azt javasoljuk, hogy kövesse az ebben a szakaszban található szempontokat, amikor egy PowerShell-modult hoz az Azure Automationben való használatra.

### <a name="references-to-azurerm-and-az"></a>Az AzureRM-re és az Az-ra mutató hivatkozások

Ha az Az modulok a modulban, győződjön meg arról, hogy nem is hivatkozik az AzureRM-modulok. Nem használhatja egyszerre mindkét modulkészletet. 

### <a name="version-folder"></a>Verziómappa

NE vegyen fel verziómappát a modul **.zip** csomagjába.  Ez a probléma kevésbé érinti a runbookokat, de problémát okoz az Állapotkonfiguráció (DSC) szolgáltatással. Az Azure Automation automatikusan létrehozza a verziómappát, amikor a modult a DSC által kezelt csomópontokra terjesztik. Ha létezik verziómappa, akkor két példány jelenik meg. Íme egy példa egy DSC-modul mappastruktúrájára:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Súgó információk

Adjon meg egy szinopszist, leírást és súgó URI-t a modul minden parancsmagjához. A PowerShellben a `Get-Help` parancsmagok segítségével adhat meg súgóinformációkat. A következő példa bemutatja, hogyan definiálható szinopszis, és help URI egy **.psm1** modulfájlban.

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

### <a name="connection-type"></a>Kapcsolat típusa

Ha a modul külső szolgáltatáshoz csatlakozik, adjon meg [egy kapcsolattípust](#adding-a-connection-type-to-your-module). A modul minden parancsmacájának paraméterként el kell fogadnia egy kapcsolatobjektumot (a kapcsolattípus egy példányát). A felhasználók a csatlakozási eszköz paramétereit a parancsmag megfelelő paramétereihez képezik le minden alkalommal, amikor egy parancsmag hívását meghívják. A következő runbook-példa az előző szakaszban példa alapján egy Contoso-kapcsolati eszközt használ a Contoso-erőforrások `ContosoConnection` eléréséhez és a külső szolgáltatásból származó adatok visszaadására. Ebben a példában a mezők levannak `Password` képezve `PSCredential` egy `UserName` objektum és tulajdonságaihoz, majd átkerülnek a parancsmagba.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  A viselkedés megközelítésének egyszerűbb és jobb módja a kapcsolatobjektum közvetlen átadása a parancsmagnak:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  A parancsmagok hasonló viselkedését engedélyezheti, ha lehetővé teszi számukra, hogy közvetlenül egy kapcsolatobjektumot fogadjanak el paraméterként, és ne csak a paraméterek csatlakozási mezőit. Általában szeretne egy paraméterkészletet minden, hogy az Azure Automation-t nem használó felhasználó meghívhatja a parancsmagokat anélkül, hogy egy kivonatot hozna létre, hogy kapcsolatobjektumként működjön. A paraméterkészlet `UserAccount` a kapcsolatmező tulajdonságainak átadására szolgál. `ConnectionObject`lehetővé teszi, hogy a kapcsolatot egyenesen átadja.

### <a name="output-type"></a>Kimenet típusa

Adja meg a kimeneti típusát a modul összes parancsmagjához. Egy parancsmag kimeneti típusának meghatározása lehetővé teszi, hogy az IntelliSense tervezési idejű módon határozza meg a parancsmag kimeneti tulajdonságait a szerzői jog során. Ez a gyakorlat különösen hasznos a grafikus runbook-készítés során, amelyhez a tervezési idejű ismeretek kulcsfontosságúak a modul egyszerű felhasználói élményéhez.

Adja `[OutputType([<MyOutputType>])]` `MyOutputType` meg, hogy hol érvényes típus. További információ: `OutputType`A [Functions OutputTypeAttribute –](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute) A következő kód egy `OutputType` parancsmag hozzáadásának példája:

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

### <a name="cmdlet-state"></a>A parancsmag állapota

Az összes parancsmag a modulban állapot nélküli. Egyszerre több runbook-feladat is `AppDomain` futtatható ugyanabban a folyamatban és a sandboxban. Ha ezen a szinten van megosztva olyan állapot, amely hatással lehet egymásra. Ez a viselkedés időszakos és nehezen diagnosztizálható problémákhoz vezethet. Íme egy példa arra, hogy mit ne tegyünk:

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

### <a name="module-dependency"></a>Modulfüggőség

Győződjön meg arról, hogy a modul teljes mértékben tartalmazza a csomagot, amely lehet másolni xcopy. Az Azure Automation-modulok az Automation sandboxok között vannak elosztva a runbookok végrehajtásakor. A moduloknak az azokat üzemeltető állomástól függetlenül kell működniük. 

Képesnek kell lennie arra, hogy zip-fel, és helyezze át a modulcsomagot, és azt, hogy a szokásos módon működik, ha egy másik gazdagép PowerShell-környezetbe importált. Ehhez győződjön meg arról, hogy a modul nem függ a modulmappán kívüli fájloktól, amelyek a modul Azure Automation-be importálásakor fel vannak csomagolva. 

A modul nem függhet az állomás egyedi beállításjegyzék-beállításaitól. Ilyenek például a termék telepítésekor megadott beállítások. 

### <a name="module-file-paths"></a>Modulfájl elérési útjai

Győződjön meg arról, hogy a modul ban lévő összes fájl elérési útja 140 karakternél rövidebb. A 140 karakternél hosszabb elérési utak problémákat okoznak a runbookok importálásával kapcsolatban. Az Azure Automation nem tud 140 karakternél nagyobb elérési útméretű fájlt importálni a PowerShell-munkamenetbe. `Import-Module`

## <a name="importing-modules"></a>Modulok importálása

Ez a szakasz számos módot határoz meg amodul automation-fiókba történő importálásához. 

### <a name="import-modules-in-azure-portal"></a>Modulok importálása az Azure Portalon

Modul importálása az Azure Portalon:

1. Nyissa meg az Automation-fiókot.
2. Válassza a **Modulok lehetőséget a** Megosztott erőforrások **csoportban.**
3. Kattintson **a Modul hozzáadása**gombra. 
4. Jelölje ki a modult tartalmazó **.zip** fájlt.
5. A folyamat importálásának megkezdéséhez kattintson az **OK** gombra.

### <a name="import-modules-using-powershell"></a>Modulok importálása a PowerShell használatával

A [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) parancsmag segítségével importálhat egy modult az Automation-fiókba. A parancsmag egy modul .zip csomag URL-címét veszi fel.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Ugyanazzal a parancsmagmal is importálhat egy modult közvetlenül a PowerShell-galériából. Győződjön meg `ModuleName` `ModuleVersion` róla, hogy megragad, és a [PowerShell Galéria](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>Modulok importálása a PowerShell-galériából

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

## <a name="deleting-modules"></a>Modulok törlése

Ha problémái vannak egy modullal, vagy vissza kell visszavonnia egy modul egy korábbi verziójára, törölheti azt az Automation-fiókból. Az automation-fiók létrehozásakor importált alapértelmezett modulok eredeti [verziói](#default-modules) nem törölhetők. Ha a törlött modul az egyik [alapértelmezett modul](#default-modules)újabb verziója, akkor visszaáll az Automation-fiókkal telepített verzióra. Ellenkező esetben az Automation-fiókból törölt modulok törlődnek.

### <a name="delete-modules-in-azure-portal"></a>Modulok törlése az Azure Portalon

Modul eltávolítása az Azure Portalon:

1. Nyissa meg az Automation-fiókot, és válassza **a Modulok lehetőséget a** Megosztott erőforrások **csoportban.** 
2. Jelölje ki az eltávolítani kívánt modult. 
3. A **Modul** lapon válassza a **Törlés**lehetőséget. Ha ez a modul az [egyik alapértelmezett modul,](#default-modules)akkor visszaáll arra a verzióra, amely az Automation-fiók létrehozásakor létezett.

### <a name="delete-modules-using-powershell"></a>Modulok törlése a PowerShell használatával

Ha el szeretne távolítani egy modult a PowerShellen keresztül, futtassa a következő parancsot:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Kapcsolattípus hozzáadása a modulhoz

Az Automation-fiókban használható egyéni [kapcsolattípust](../automation-connections.md) úgy adhat meg, hogy egy opcionális metaadatfájlt ad a modulhoz. Ez a fájl egy Azure Automation-kapcsolattípust ad meg, amelyet a modul parancsmagjaihoz kell használni az Automation-fiókban. A PowerShell-modulok szerzőiről a [PowerShell-parancsfájlmodul írása](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7)című témakörben olvashat bővebben.

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

## <a name="next-steps"></a>További lépések

* Az Azure PowerShell-modulok használatáról az [Azure PowerShell használatának első lépései](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0)című témakörben talál további információt.
* A PowerShell-modulok létrehozásáról a [Windows PowerShell-modul írása](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7)című témakörben olvashat bővebben.
