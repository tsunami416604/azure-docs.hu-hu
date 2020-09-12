---
title: Változók kezelése a Azure Automationban
description: Ez a cikk azt ismerteti, hogyan használhatók változók a runbookok és a DSC-konfigurációkban.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/10/2020
ms.topic: conceptual
ms.openlocfilehash: 300bfa2ed801b810bcaaeb5bc4d04775d590015b
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "90004563"
---
# <a name="manage-variables-in-azure-automation"></a>Változók kezelése a Azure Automationban

A változó eszközök olyan értékek, amelyek az Automation-fiók összes runbookok és DSC-konfigurációjában elérhetők. Felügyelheti őket a Azure Portal, a PowerShellből, egy runbook belül vagy egy DSC-konfigurációban.

Az Automation-változók a következő helyzetekben lehetnek hasznosak:

- Egy érték megosztása több runbookok vagy DSC-konfiguráció között.

- Egy érték megosztása több feladat között ugyanabból a runbook vagy DSC-konfigurációból.

- A runbookok vagy DSC-konfigurációk által a portálon vagy a PowerShell parancssorban használt érték kezelése. Ilyenek például a gyakori konfigurációs elemek, például a virtuálisgép-nevek, egy adott erőforráscsoport, egy AD-tartománynév és egyebek.  

Azure Automation megőrzi a változókat, és akkor is elérhetővé teszi őket, ha egy runbook vagy DSC-konfiguráció meghibásodik. Ez a viselkedés lehetővé teszi, hogy egy runbook vagy DSC-konfiguráció olyan értéket állítson be, amelyet egy másik runbook használ, vagy ha a következő futtatásakor ugyanaz a runbook vagy DSC-konfiguráció szerepel.

Azure Automation az egyes titkosított változókat biztonságosan tárolja. Változó létrehozásakor megadhatja a titkosítását és tárolását Azure Automation biztonságos eszközként. A változó létrehozása után nem változtathatja meg a titkosítási állapotát a változó újbóli létrehozása nélkül. Azure Security Center javasoljuk, hogy titkosítsa az összes Azure Automation változót az [Automation-fiók változói](../../security-center/recommendations-reference.md#recs-computeapp)között leírt módon.

>[!NOTE]
>Azure Automation a biztonságos adategységek közé tartoznak a hitelesítő adatok, a tanúsítványok, a kapcsolatok és a titkosított változók. Ezeket az eszközöket a rendszer titkosítja és tárolja Azure Automation az egyes Automation-fiókokhoz generált egyedi kulcs használatával. Azure Automation a kulcsot a rendszerfelügyelt Key Vault tárolja. A biztonságos eszköz tárolása előtt az Automation betölti a kulcsot Key Vault, majd a használatával titkosítja az eszközt. 

## <a name="variable-types"></a>Változók típusai

Ha a Azure Portal változót hoz létre, meg kell adnia egy adattípust a legördülő listából, hogy a portál megjelenítse a megfelelő vezérlőt a változó értékének megadásához. A következő változó típusok érhetők el Azure Automationban:

* Sztring
* Egész szám
* Dátum/idő
* Logikai
* Null

A változó nem korlátozódik a megadott adattípusra. A változót a Windows PowerShell használatával kell beállítani, ha más típusú értéket szeretne megadni. Ha a jelzést adja meg `Not defined` , a változó értéke null. Az értéket a [set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable) parancsmaggal vagy a belső `Set-AutomationVariable` parancsmaggal kell beállítani.

Nem használhatja a Azure Portal egy összetett változó típusának értékének létrehozására vagy módosítására. A Windows PowerShell használatával azonban bármilyen típusú értéket megadhat. Az összetett típusok beolvasása [pscustomobject formájában kapja](/dotnet/api/system.management.automation.pscustomobject)történik.

Tömb vagy szórótábla létrehozásával és a változóba való mentésével több értéket is tárolhat egyetlen változóban.

>[!NOTE]
>A virtuális gépek nevének változói legfeljebb 80 karakterből állhatnak. Az erőforráscsoport-változók legfeljebb 90 karakterből állhatnak. Lásd: [Az Azure-erőforrások elnevezési szabályai és korlátozásai](../../azure-resource-manager/management/resource-name-rules.md).

## <a name="powershell-cmdlets-to-access-variables"></a>A változók eléréséhez szükséges PowerShell-parancsmagok

Az alábbi táblázatban található parancsmagok automatizálási változókat hoznak létre és kezelhetnek a PowerShell használatával. Az az [modulok](modules.md#az-modules)részét képezik.

| Parancsmag | Leírás |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable) | Egy létező változó értékét kérdezi le. Ha az érték egy egyszerű típus, akkor a rendszer ugyanazt a típust kéri le. Összetett típus esetén a `PSCustomObject` rendszer beolvassa a típust. <br>**Megjegyzés:**  Nem használhatja ezt a parancsmagot egy titkosított változó értékének lekéréséhez. Ennek egyetlen módja, ha a belső `Get-AutomationVariable` parancsmagot használja egy runbook vagy DSC-konfigurációban. [A változók eléréséhez tekintse meg a belső parancsmagokat](#internal-cmdlets-to-access-variables). |
|[Új – AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable) | Létrehoz egy új változót, és beállítja annak értékét.|
|[Remove-AzAutomationVariable](/powershell/module/az.automation/remove-azautomationvariable)| Eltávolít egy meglévő változót.|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable)| Beállítja egy létező változó értékét. |

## <a name="internal-cmdlets-to-access-variables"></a>A változók eléréséhez szükséges belső parancsmagok

A következő táblázatban található belső parancsmagok a runbookok és a DSC-konfigurációk változóinak elérésére szolgálnak. Ezek a parancsmagok a globális modulhoz tartoznak `Orchestrator.AssetManagement.Cmdlets` . További információ: [belső parancsmagok](modules.md#internal-cmdlets).

| Belső parancsmag | Description |
|:---|:---|
|`Get-AutomationVariable`|Egy létező változó értékét kérdezi le.|
|`Set-AutomationVariable`|Beállítja egy létező változó értékét.|

> [!NOTE]
> Ne használjon változókat a `Name` `Get-AutomationVariable` RUNBOOK vagy DSC-konfigurációban lévő paraméterben. A változók használata megnehezítheti a runbookok és az Automation változók közötti függőségek felderítését a tervezési időszakban.

`Get-AutomationVariable` a nem működik a PowerShellben, de csak runbook vagy DSC-konfigurációban. Ha például egy titkosított változó értékét szeretné megtekinteni, létrehozhat egy runbook a változó beszerzéséhez, majd a kimeneti adatfolyamba írja a következőt:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="python-2-functions-to-access-variables"></a>A Python 2 függvények a változók eléréséhez

A következő táblázatban szereplő függvények a Python 2 runbook változóinak elérésére szolgálnak.

|Python 2 függvények|Description|
|:---|:---|
|`automationassets.get_automation_variable`|Egy létező változó értékét kérdezi le. |
|`automationassets.set_automation_variable`|Beállítja egy létező változó értékét. |

> [!NOTE]
> Az `automationassets` Asset functions eléréséhez importálnia kell a modult a Python-runbook tetején.

## <a name="create-and-get-a-variable"></a>Változó létrehozása és lekérése

>[!NOTE]
>Ha el szeretné távolítani egy változó titkosítását, törölnie kell a változót, és újból létre kell hoznia a titkosítást.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Változó létrehozása és lekérése a Azure Portal használatával

1. Az Automation-fiókban a bal oldali ablaktáblán válassza a **változók** elemet a **megosztott erőforrások**területen.
2. A **változók** lapon válassza a **változó hozzáadása**elemet.
3. Fejezze be a beállításokat az **új változó** lapon, majd válassza a **Létrehozás** lehetőséget az új változó mentéséhez.

> [!NOTE]
> Ha mentett egy titkosított változót, azt a portálon nem lehet megtekinteni. Csak a frissítésre használható.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Változó létrehozása és lekérése a Windows PowerShellben

A runbook vagy DSC-konfiguráció a `New-AzAutomationVariable` parancsmaggal létrehoz egy új változót, és beállítja a kezdeti értékét. Ha a változó titkosítva van, a hívásnak a `Encrypted` paramétert kell használnia. A szkript a paranccsal kérheti le a változó értékét `Get-AzAutomationVariable` .

>[!NOTE]
>Egy PowerShell-parancsfájl nem tud beolvasni egy titkosított értéket. Ennek egyetlen módja a belső `Get-AutomationVariable` parancsmag használata.

Az alábbi példa bemutatja, hogyan hozhat létre egy karakterlánc-változót, majd visszaadja az értékét.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Az alábbi példa bemutatja, hogyan hozhat létre egy összetett típusú változót, majd hogyan kérheti le a tulajdonságait. Ebben az esetben a [Get-AzVM virtuálisgép-](/powershell/module/Az.Compute/Get-AzVM) objektumot használja a rendszer.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="textual-runbook-examples"></a>Szöveges runbook-példák

### <a name="retrieve-and-set-a-simple-value-from-a-variable"></a>Egyszerű érték beolvasása és beállítása egy változóból

Az alábbi példa bemutatja, hogyan állíthat be és kérhet le egy változót egy szöveges runbook. Ez a példa feltételezi, hogy egy nevű egész szám típusú változót és `NumberOfIterations` `NumberOfRunnings` egy nevű karakterlánc-változót hoz létre `SampleMessage` .

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

### <a name="retrieve-and-set-a-variable-in-a-python-2-runbook"></a>Változó beolvasása és beállítása Python 2 runbook

A következő minta bemutatja, hogyan lehet változót beolvasni, változót beállítani és kezelni egy nem létező változót egy Python 2 runbook.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

## <a name="graphical-runbook-examples"></a>Grafikus runbook-példák

Grafikus runbook felvehet tevékenységeket a belső parancsmagokhoz `Get-AutomationVariable` vagy `Set-AutomationVariable` . Csak kattintson a jobb gombbal az egyes változókra a grafikus szerkesztő könyvtár paneljén, és válassza ki a kívánt tevékenységet.

![Változó hozzáadása a vászonhoz](../media/variables/runbook-variable-add-canvas.png)

Az alábbi képen egy egyszerű értékkel rendelkező változó egy grafikus runbook való frissítését bemutató tevékenységek láthatók. Ebben a példában a tevékenység `Get-AzVM`  egy egyetlen Azure-beli virtuális gép lekérésére és a számítógép nevének egy meglévő Automation string változóba való mentésére irányul. Nem számít, hogy a [hivatkozás egy folyamat vagy egy folyamat](../automation-graphical-authoring-intro.md#use-links-for-workflow) , mert a kód csak egyetlen objektumot vár a kimenetben.

![Egyszerű változó beállítása](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni a változók eléréséhez használt parancsmagokról, tekintse meg a [modulok kezelése a Azure Automationban](modules.md)című témakört.
* A runbookok kapcsolatos általános információkért lásd: [a Runbook végrehajtása Azure Automation](../automation-runbook-execution.md).
* A DSC-konfigurációk részletes ismertetését lásd: [Azure Automation állapot konfigurációjának áttekintése](../automation-dsc-overview.md).
