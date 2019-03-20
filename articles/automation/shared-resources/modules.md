---
title: Az Azure Automationben modulokkal kezelése
description: Ez a cikk bemutatja, hogyan kezelheti az Azure Automationben modulokkal
services: automation
ms.service: automation
ms.subservice: shared-resources
author: georgewallace
ms.author: gwallace
ms.date: 03/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fa7f5d3fb38eb1dbca51dec9b73dca3c998436aa
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2019
ms.locfileid: "57909135"
---
# <a name="manage-modules-in-azure-automation"></a>Az Azure Automationben modulokkal kezelése

Az Azure Automation lehetővé teszi a PowerShell-modulok importálásához az Automation-fiókját a PowerShell-alapú runbookok által használható. Ezek a modulok lehet egyéni modulok létrehozott, a PowerShell-galériából, vagy az AzureRM- és Az modulok az Azure-hoz.

## <a name="import-modules"></a>Modulok importálása

Többféle módon, hogy egy modul importálhatja az Automation-fiókba. A következő szakaszokban a modul importálása a különböző módszereket mutatnak.

> [!NOTE]
> A maximális használható az Azure Automation-modul egy fájl elérési útja legfeljebb 140. Legfeljebb 140 keresztül bármilyen útvonalat nem fogja tudni importálhatók, a PowerShell-munkamenetet `Import-Module`.

### <a name="powershell"></a>PowerShell

Használhatja a [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) modul importálása az Automation-fiókba. A parancsmag egy URL-címet a modul zip-csomagját vesz igénybe.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Azure Portal

Az Azure Portalon lépjen az Automation-fiókját, és válassza ki **modulok** alatt **megosztott erőforrások**. Kattintson a **+ modul hozzáadása**. Válassza ki a **.zip** fájlt, amely tartalmazza a modult, és kattintson **Ok** az importálási folyamat elindításához.

### <a name="powershell-gallery"></a>PowerShell-galéria

Modulokat a PowerShell-galériából való importálása lehet a [PowerShell-galériából](https://www.powershellgallery.com) közvetlenül vagy az Automation-fiók.

Modul importálása a PowerShell-galériából, lépjen a https://www.powershellgallery.com , és keresse meg az importálni kívánt modul. Kattintson a **üzembe helyezés az Azure Automation** a a **Azure Automation** lapjára **telepítési beállítások**. Ez a művelet megnyitja az Azure Portalt. Az a **importálás** lapon válassza ki az Automation-fiókját, és kattintson **OK**.

![PowerShell-galériából a modul importálása](../media/modules/powershell-gallery.png)

Közvetlenül az Automation-fiókját a PowerShell-galériából történő is importálhat modulok. Válassza ki az Automation-fiók **modulok** alatt **megosztott erőforrások**. A modulok oldalon kattintson a **Tallózás a katalógusban**. Megnyílik a **Tallózás a katalógusban** lapot. Keresés a PowerShell-galériából, a modul ezen a lapon. Válassza ki a modul importálása, és kattintson a kívánt **importálása**. Az a **importálása** kattintson **OK** az importálási folyamat elindításához.

![PowerShell-galériából importálás az Azure Portalon](../media/modules/gallery-azure-portal.png)

## <a name="internal-cmdlets"></a>Belső parancsmagok

Az alábbiakban a belső parancsmagok listája a `Orchestrator.AssetManagement.Cmdlets` modul, amely a rendszer importálja az összes Automation-fiók. Ezek a parancsmagok érhetők el a runbookok és a DSC-konfigurációk, és lehetővé teszik az eszközök az Automation-fiókon belül. Ezenkívül a belső parancsmagok lehetővé teszik titkos kódjainak beolvasására a titkosított **változó** értékek **hitelesítő adatok**, és a titkosított **kapcsolat** mezők. Az Azure PowerShell-parancsmagok kihasználására nem képes ezek titkos kódjainak beolvasására. Ezeket a parancsmagokat nem szükséges, hogy implicit módon csatlakoztatása az Azure-, amikor használja őket. Ez a előnyös a kapcsolatot, például a futtató fiók, amely hitelesítést az Azure-ban szüksége van.

|Name (Név)|Leírás|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>A modul ad hozzá egy kapcsolat típusa

Megadhat egy egyéni [kapcsolattípus](../automation-connections.md) egy nem kötelező fájlt ad hozzá a modul az Automation-fiókban használhatja. Ez a fájl az Automation-fiókban a modul parancsmagjaival használható egy Azure Automation kapcsolattípust megadó metaadatfájlt. Ennek érdekében először tudja kell, hogyan hozzon létre egy PowerShell-modul. A modul szerzői további információkért lásd: [szkriptet egy PowerShell-modul írása](/powershell/developer/module/how-to-write-a-powershell-script-module).

![Egyéni kapcsolat használata az Azure Portalon](../media/modules/connection-create-new.png)

Szeretne hozzáadni egy Azure Automation kapcsolattípust, a modul tartalmaznia kell egy fájlt névvel `<ModuleName>-Automation.json` , amely meghatározza a kapcsolattípus tulajdonságait. Ez az egy json-fájlt, amely a tömörített .zip fájl modulmappájában elhelyezett van. Ez a fájl tartalmazza a mezők a kapcsolatok, a rendszer vagy a modul által képviselt szolgáltatási való csatlakozáshoz szükséges. Ez a konfiguráció említi kapcsolattípus létrehozása az Azure Automationben. Ezzel a fájllal is megadhatja a mezőneveket, a típusait, és hogy kell lennie a mezőket, titkosított vagy nem kötelező, a kapcsolat típusa, egyben a modul. Az alábbi példában, amely meghatározza egy felhasználónév és jelszó tulajdonság json fájlformátumú sablon:

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

## <a name="module-best-practices"></a>A modul ajánlott eljárások

PowerShell-modul importálható az Azure Automationbe, hogy a runbookok és a DSC-erőforrásaik parancsmagjaik használhatók DSC-konfigurációk parancsmagjaik használhatók. A színfalak mögött az Azure Automation tárolja ezeket a modulokat, és a runbook-feladat és a DSC fordítási feladat végrehajtási ideje betölti őket az Azure Automation próbakörnyezetbe, ahol a runbookok végrehajtása és a DSC-konfigurációk fordítása. Olyan modulok DSC-erőforrások is automatikusan kikerülnek az Automation DSC lekéréses kiszolgálón. Lekérhetik őket gépek DSC-konfigurációk alkalmazható.

Javasoljuk, hogy amikor szerzőként összeállít egy PowerShell-modul használata az Azure Automationben vegye figyelembe a következőket:

* A modul összes parancsmagjáról mellékeljen egy szinopszist, egy leírást és egy súgó URI-t. A PowerShellben meghatározhat bizonyos súgóinformációkat a parancsmagokhoz, hogy a felhasználó segítséget kapjon a **Get-Help** parancsmaggal való használatukkor. Az alábbi példa bemutatja, hogyan meghatározhatja a szinopszist és a egy .psm1 modul fájlban URI-JÁNAK segítségével:

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

  Ezt az információt megadja a súgó használatát mutatja be a **Get-Help** parancsmag a PowerShell-konzolon. Ez a leírás az Azure Portalon is megjelenik.

  ![Integrációs modul súgó](../media/modules/module-activity-description.png)

* Ha a modul egy külső szolgáltatás kapcsolódik, akkor tartalmaznia kell egy [kapcsolattípus](#add-a-connection-type-to-your-module). A modul minden egyes parancsmagjának képesnek kell lennie egy kapcsolat objektum (az adott kapcsolattípus egy példánya) befogadására paraméterként. Ez lehetővé teszi a felhasználók leképezniük a kapcsolat adategység a parancsmag megfelelő paramétereihez minden alkalommal, amikor azok meghívnak egy parancsmagot. A fenti forgatókönyv-példa alapján, használ egy példa Contoso kapcsolatobjektum ContosoConnection nevű Contoso erőforrások eléréséhez, és a külső szolgáltatásból adatokat adja vissza.

  A következő példában a mezők felhasználónév és jelszó tulajdonságaira vannak leképezve egy `PSCredential` objektumra, és ezután a parancsmagnak átadott.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Az egyszerűbb és jobb megoldás ezt a viselkedést, ha közvetlenül továbbítja a kapcsolat objektumot a parancsmagnak:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Az előző példában viselkedést engedélyezhet a parancsmagjaihoz, a azáltal, hogy elfogadjon egy kapcsolat objektumot közvetlenül paraméterként ahelyett csak kapcsolat. Általában érdemes minden, egy paramétert, hogy a felhasználó nem használja az Azure Automation is meghívhatja a parancsmagjait anélkül hozhat létre, egy kapcsolat objektumként paraméterkészletre. A paraméterkészlet `UserAccount`, be a kapcsolat tulajdonságai. `ConnectionObject` egyenes keresztül továbbítja a kapcsolat teszi lehetővé.

* A modul adja meg az összes parancsmag kimenettípusát. Egy parancsmag kimenettípusának megadása lehetővé teszi a tervezés közben az IntelliSense használatát, amely segít meghatározni egy parancsmag kimenetének tulajdonságait a megírásuk támogatására. Ez különösen hasznos az Automation-forgatókönyvek grafikus létrehozásakor, ahol a tervezés az könnyedfelhasználói élmény biztosításához a modulban kulcs alatt.

  Ez a hozzáadásával érhető el `[OutputType([<MyOutputType>])]` ahol MyOutputType-e egy érvényes típusa. OutputType kapcsolatos további információkért lásd: [funkciók OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). A következő kódot a példázza hozzáadásának `OutputType` egy parancsmaghoz:

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

  Ez a viselkedés hasonlít a "type ahead" funkciójára, a parancsmag kimenete a PowerShell ISE-ben, anélkül, hogy nem kell futtatni.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

* A modul egyik parancsmagjának se legyen állapota. Több runbook-feladatok egyidejűleg futtatható ugyanabban az alkalmazástartományban és azonos folyamat és tesztkörnyezet. Ha van olyan ezeket a szinteket a megosztott állapotokat, feladatok hatással lehet az egymással. Ez a viselkedés vezethet időszakos, nehezen diagnosztizálhatja a problémákat.  Az alábbi példa bemutatja, mit ne tegyen.

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

* A modul kell lehet teljes egészében egy xcopy-képes csomag. Azure Automation-modulokat az Automation próbakörnyezetekbe oszlanak meg, amikor a forgatókönyveket végre kell hajtani. A modulok kell működniük a gazdagéptől futnak. Meg kell tudni Zip beállítása és a egy csomag és normális működtetésére, amikor egy másik gazdagép PowerShell környezetébe importálja azt. Ahhoz, hogy ez megtörténjen a modul a modul mappákon kívül semmilyen fájl nem függenek. Ez a mappa az a mappa, a modul importálása az Azure Automationbe modulmappán. A modul is nem függhet egy gazdagép bármilyen egyedi beállításjegyzék-beállításokat, mint például állítsa be ezeket a beállításokat, a termék telepítésekor. A modulban lévő összes fájlt egy elérési utat kell kevesebb mint 140 karakter. Legfeljebb 140 keresztül bármilyen elérési utak a runbook importálása problémák miatt. Ha ez az ajánlott eljárás nem követik, a modul nem használható az Azure Automationben.  

* Ha a hivatkozó [Az Azure Powershell-modulok](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) a modult, ellenőrizze, nem is hivatkozó `AzureRM`. A `Az` modul nem használható együtt a `AzureRM` modulok. `Az` a runbookok támogatott, de alapértelmezés szerint nincsenek importálva. További információ a `Az` modulok és szempontokat vegye figyelembe, hogy tekintse meg a [házirendmodul-támogatás Az Azure Automation](../az-modules.md).

## <a name="next-steps"></a>További lépések

* További információk PowerShell-modulok létrehozásáról: [Windows PowerShell-modul írása](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx).