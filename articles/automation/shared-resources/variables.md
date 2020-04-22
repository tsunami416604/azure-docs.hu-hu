---
title: Változók kezelése az Azure Automationben
description: A változó eszközök olyan értékek, amelyek az Azure Automation ben minden runbook és DSC-konfiguráció számára elérhetők.  Ez a cikk ismerteti a változók részleteit, és hogyan működik velük mind szöveges, mind grafikus szerzői nyelven.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4778e9b2c0d3b442b214966ab69810d2f42b70b8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732766"
---
# <a name="manage-variables-in-azure-automation"></a>Változók kezelése az Azure Automationben

A változó eszközök olyan értékek, amelyek az Automation-fiókban lévő összes runbook és DSC-konfiguráció számára elérhetők. Kezelheti őket az Azure Portalon, a PowerShellből, egy runbookon belül vagy egy DSC-konfigurációban.

Az automatizálási változók a következő esetekben hasznosak:

- Érték megosztása több runbookok vagy DSC konfigurációk között.

- Érték megosztása több feladat között ugyanabból a runbook vagy DSC konfigurációból.

- Runbookok vagy DSC-konfigurációk által használt érték kezelése a portálról vagy a PowerShell parancssorából. Egy példa a közös konfigurációs elemek, például egy adott listáját virtuális gép nevét, egy adott erőforráscsoport, egy AD tartomány nevét, és így tovább.  

Az Azure Automation továbbra is megmarad a változók, és elérhetővé teszi őket akkor is, ha egy runbook vagy DSC konfiguráció sikertelen. Ez a viselkedés lehetővé teszi, hogy egy runbook vagy DSC-konfiguráció olyan értéket állítson be, amelyet aztán egy másik runbook, vagy ugyanaz a runbook vagy DSC-konfiguráció használ a következő futtatáskor.

Az Azure Automation minden titkosított változót biztonságosan tárol. Változó létrehozásakor megadhatja a titkosítást és a tárolást az Azure Automation által biztonságos eszközként. Az egyéb biztonságos eszközök közé tartoznak a hitelesítő adatok, a tanúsítványok és a kapcsolatok. Az Azure Automation titkosítja ezeket az eszközöket, és tárolja őket egy egyedi kulcs, amely minden Automation-fiókhoz létrehozott. A kulcs egy rendszer által felügyelt Key Vault tárolja. Egy biztonságos eszköz tárolása előtt az Azure Automation betölti a kulcsot a Key Vaultból, és ezután az eszköz titkosításához használja azt. 

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](../automation-update-azure-modules.md)

## <a name="variable-types"></a>Változótípusok

Amikor létrehoz egy változót az Azure Portalon, meg kell adnia egy adattípust a legördülő listából, hogy a portál meg tudja jeleníteni a változó értékének megadásához szükséges megfelelő vezérlőt. Az Azure Automationben az alábbi változótípusok érhetők el:

* Sztring
* Egész szám
* DateTime
* Logikai
* Null

A változó nem korlátozódik a kijelölt adattípusra. Ha más típusú értéket szeretne megadni, a változót a Windows PowerShell használatával kell beállítania. Ha a `Not defined`jel a ( jel) értéket adja meg, a változó értéke Null. Az értéket a [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) parancsmaggal vagy `Set-AutomationVariable` a tevékenységgel kell beállítani.

Az Azure Portal használatával nem hozhat létre vagy módosíthat egy összetett változótípus értékét. Azonban bármilyen típusú értéket adhat meg a Windows PowerShell használatával. Az összetett típusok beolvasása [PSCustomObject objektumként lesz beolvasva.](/dotnet/api/system.management.automation.pscustomobject)

Tömb vagy kivonatoló létrehozásával több értéket is tárolhat egyetlen változóban, és mentheti azt a változóba.

>[!NOTE]
>A virtuális gép névváltozói legfeljebb 80 karakterből állhatnak. Az erőforráscsoport változói legfeljebb 90 karakter ből állhatnak. Lásd: [Az Azure-erőforrások elnevezési szabályai és korlátozásai.](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules)

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Változó eszközöket létrehozó és kezelő PowerShell-parancsmagok

Az Az modul esetében az alábbi táblázatban található parancsmagok az Automation-változóeszközök windowsos PowerShellhasználatával történő létrehozásához és kezeléséhez használatosak. Az [Az.Automation modul](/powershell/azure/overview)részeként szállítanak, amely az Automation runbookok és a DSC-konfigurációk ban használható.

| Parancsmag | Leírás |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Egy létező változó értékét kérdezi le. Ezzel a parancsmagval nem lehet lekérni egy titkosított változó értékét. Az egyetlen módja ennek a `Get-AutomationVariable` tevékenység használata egy runbook vagy DSC konfigurációban. |
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Új változót hoz létre, és beállítja annak értékét.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Egy meglévő változó eltávolítása.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Beállítja egy létező változó értékét. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>A runbookok és a DSC-konfigurációk változóinak elérésére irányuló tevékenységek

Az alábbi táblázatban szereplő tevékenységek a runbookok és a DSC-konfigurációk változóinak elérésére szolgálnak. Az ilyen tevékenységek hez készült parancsmagok `Orchestrator.AssetManagement.Cmdlets`a globális modullal vannak eljárva.

| Tevékenység | Leírás |
|:---|:---|
|`Get-AutomationVariable`|Egy létező változó értékét kérdezi le.|
|`Set-AutomationVariable`|Beállítja egy létező változó értékét.|

> [!NOTE]
> Ne használjon változókat a `Name` runbook vagy DSC-konfigurációban lévő `Get-AutomationVariable` paraméterben. Ennek a paraméternek a használata megnehezítheti a runbookok vagy dsc-konfigurációk és az automation-változók közötti függőségek felderítését a tervezés időpontjában.

Vegye `Get-AutomationVariable` figyelembe, hogy nem működik a PowerShell, de csak egy runbook vagy DSC konfigurációban. Például egy titkosított változó értékének megtekintéséhez létrehozhat egy runbookot a változó lekérése érdekében, majd írhatja be a kimeneti adatfolyamba:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>A Python 2 runbookok változóinak elérésére irányuló függvények

Az alábbi táblázatban szereplő függvények a Python 2 runbook változóinak elérésére szolgálnak.

|Python 2 függvények|Leírás|
|:---|:---|
|`automationassets.get_automation_variable`|Egy létező változó értékét kérdezi le. |
|`automationassets.set_automation_variable`|Beállítja egy létező változó értékét. |

> [!NOTE]
> Az eszközfüggvények eléréséhez importálnia kell a `automationassets` modult a Python runbook tetején.

## <a name="working-with-automation-variables"></a>Automatizálási változók használata

>[!NOTE]
>Ha el szeretné távolítani egy változó titkosítását, törölnie kell a változót, és újra titkosítatlanként kell létrehoznia.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Új változó létrehozása az Azure Portal használatával

1. Az Automation-fiókban kattintson az **Eszközök** csempére, majd az **Eszközök** panelre, és válassza a **Változók**lehetőséget.
2. A **Változók** csempén válassza **a Változó hozzáadása**lehetőséget.
3. Töltse ki az **Új változó** panel beállításait, majd kattintson a **Létrehozás** gombra az új változó mentéséhez.

> [!NOTE]
> Miután mentett egy titkosított változót, az nem tekinthető meg a portálon. Csak frissíteni lehet.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Változó létrehozása és használata a Windows PowerShellben

A PowerShell-parancsfájl `New-AzAutomationVariable` a parancsmag vagy az AzureRM-modul megfelelője, egy új változó létrehozásához és a kezdeti érték beállításához. Ha a változó titkosítva van, `Encrypted` a hívásnak a paramétert kell használnia.

A parancsfájl a [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0)segítségével olvassa be a változó értékét. Ha az érték egyszerű típus, a parancsmag ugyanazt a típust olvassa be. Ha összetett típusról van `PSCustomObject` szó, a rendszer lekéri a típust.

>[!NOTE]
>Egy PowerShell-parancsfájl nem tud beolvasni egy titkosított értéket. Az egyetlen módja ennek az, `Get-AutomationVariable` hogy egy tevékenység et runbook vagy DSC konfigurációban.

A következő példa bemutatja, hogyan hozhat létre egy Karakterlánc típusú változót, majd hogyan adja vissza az értékét.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

A következő példa bemutatja, hogyan hozhat létre egy összetett típusú változót, majd hogyan kérheti le a tulajdonságait. Ebben az esetben a [Get-AzVM virtuálisgép-objektumot](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) használja a program.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Változó létrehozása és használata runbook- vagy DSC-konfigurációban

Egy runbook- vagy DSC-konfiguráción belül csak úgy hozhat `New-AzAutomationVariable` létre új változót, ha a parancsmamot vagy annak AzureRM-modulmegfelelőjét használja. A parancsfájl ezt a parancsmamot használja a változó kezdeti értékének beállításához. A parancsfájl ezután betudja olvasni az értéket a használatával. `Get-AzAutomationVariable` Ha az érték egyszerű típus, akkor a program ugyanezt a típust olvassa be. Ha összetett típusról van szó, akkor a rendszer lekéri a `PSCustomObject` típust.

>[!NOTE]
>A titkosított értékek lekérésének egyetlen `Get-AutomationVariable` módja a runbook vagy a DSC konfigurációjában lévő tevékenység használata. 

### <a name="textual-runbook-samples"></a>Szöveges runbook minták

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Egyszerű érték beállítása és beolvasása változóból

A következő mintaparancsok bemutatják, hogyan állítható be és kereshet be egy változót egy szöveges runbookban. Ez a minta a nevű `NumberOfIterations` egész változók és `NumberOfRunnings` egy `SampleMessage`karakterlánc-változó létrehozását feltételezi.

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

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>Változó beállítása és beolvasása Python 2 runbookban

A következő minta bemutatja, hogyan kell használni egy változót, beállítegy változót, és kezelni egy kivételt egy Nem létező változóhoz egy Python 2 runbookban.

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

### <a name="graphical-runbook-samples"></a>Grafikus runbook-minták

Egy grafikus runbook, hozzáadhatja `Get-AutomationVariable` `Set-AutomationVariable` a vagy tevékenység. Egyszerűen kattintson a jobb gombbal a változóra a grafikus szerkesztő Könyvtár ablaktáblájában, és válassza ki a kívánt tevékenységet.

![Változó hozzáadása a vászonhoz](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Értékek beállítása változóban

Az alábbi képen mintatevékenységek egy változó egy egyszerű érték egy grafikus runbook frissítéséhez. Ebben a `Get-AzVM` példában egyetlen Azure virtuális gépet kér le, és a számítógépnevet egy meglévő Automation-karakterlánc-változóba menti. Nem számít, hogy a [kapcsolat egy folyamat vagy sorozat,](../automation-graphical-authoring-intro.md#links-and-workflow) mivel a kód csak egy objektumot vár a kimenetben.

![Egyszerű változó beállítása](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>További lépések

- Ha többet szeretne tudni a grafikus szerzői tevékenységek összekapcsolásáról, olvassa el a Hivatkozások a grafikus szerzői alkalmazásban című [témakört.](../automation-graphical-authoring-intro.md#links-and-workflow)
- A grafikus runbookok első lépései [Az Első grafikus runbook című témakörben](../automation-first-runbook-graphical.md)látható.
