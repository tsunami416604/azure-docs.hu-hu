---
title: Változó eszközök Azure Automation
description: A változó eszközök olyan értékek, amelyek a Azure Automation összes runbookok és DSC-konfigurációjában elérhetők.  Ez a cikk ismerteti a változók részleteit, valamint azt, hogy miként dolgozhat velük a szöveges és a grafikus létrehozásban.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 598be26024c22ba81c3f33510423605abc854b13
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79216830"
---
# <a name="variable-assets-in-azure-automation"></a>Változó eszközök Azure Automation

A változó eszközök olyan értékek, amelyek az Automation-fiók összes runbookok és DSC-konfigurációjában elérhetők. Felügyelheti őket a Azure Portal, a PowerShellből, egy runbook belül vagy egy DSC-konfigurációban.

Automation-változók hasznosak lehetnek a következő forgatókönyvekhez:

- Egy érték megosztása több runbookok vagy DSC-konfiguráció között.

- Egy érték megosztása több feladat között ugyanabból a runbook vagy DSC-konfigurációból.

- A runbookok vagy DSC-konfigurációk által a portálon vagy a PowerShell parancssorban használt érték kezelése. Ilyenek például a gyakori konfigurációs elemek, például a virtuálisgép-nevek, egy adott erőforráscsoport, egy AD-tartománynév és egyebek.  

Mivel a rendszer megőrzi az Automation-változókat, akkor is elérhetők, ha a runbook vagy a DSC-konfiguráció meghibásodik. Ez a viselkedés lehetővé teszi, hogy egy runbook vagy DSC-konfiguráció olyan értéket állítson be, amelyet egy másik runbook használ, vagy ha a következő futtatásakor ugyanaz a runbook vagy DSC-konfiguráció szerepel.

Változó létrehozásakor megadhatja a titkosítását és tárolását Azure Automation biztonságos eszközként. Az egyéb biztonságos eszközök közé tartoznak a hitelesítő adatok, a tanúsítványok és a kapcsolatok. Azure Automation titkosítja ezeket az eszközöket, és az egyes Automation-fiókokhoz generált egyedi kulccsal tárolja azokat. A kulcsot egy rendszer által felügyelt Key Vault tárolja. A biztonságos eszköz tárolása előtt Azure Automation betölti a kulcsot a Key Vault, majd a használatával titkosítja az eszközt. 

Azure Automation az egyes titkosított változókat biztonságosan tárolja. Az érték nem kérhető le a [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) parancsmaggal, amely a Azure PowerShell modul részét képezi. Egy titkosított érték beolvasásának egyetlen módja a runbook vagy DSC-konfiguráció `Get-AutomationVariable` tevékenységének használata.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Változók típusai

Ha a Azure Portal változót hoz létre, meg kell adnia egy adattípust a legördülő listából, hogy a portál megjelenítse a megfelelő vezérlőt a változó értékének megadásához. A következő változó típusok érhetők el Azure Automationban:

* Sztring
* Egész szám
* DateTime
* Logikai
* Null

A változó nem korlátozódik a kijelölt adattípusra. A változót a Windows PowerShell használatával kell beállítani, ha más típusú értéket szeretne megadni. Ha **nincs meghatározva**, a változó értéke null, és a [set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) parancsmaggal vagy a `Set-AutomationVariable` tevékenységgel kell megadnia az értéket.

A portál használatával nem lehet létrehozni vagy módosítani egy összetett változó típusának értékét. A Windows PowerShell használatával azonban bármilyen típusú értéket megadhat. Az összetett típusok beolvasása [pscustomobject formájában kapja](/dotnet/api/system.management.automation.pscustomobject)történik.

Tömb vagy szórótábla létrehozásával és egy változóba való mentésével több értéket is tárolhat egyetlen változóban.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Változó eszközöket létrehozó és kezelő PowerShell-parancsmagok

Az az modulhoz az alábbi táblázatban található parancsmagokkal automatizálható az Automation változó eszközeinek létrehozása és kezelése a Windows PowerShell használatával. Az az [. Automation modul](/powershell/azure/overview)részét képezik, amely az Automation runbookok és a DSC-konfigurációk esetében használható.

| Parancsmag | Leírás |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Egy létező változó értékét kérdezi le.|
|[Új – AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Létrehoz egy új változót, és beállítja annak értékét.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Eltávolít egy meglévő változót.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Beállítja egy létező változó értékét.|

## <a name="activities-to-access-variables"></a>Változókhoz hozzáférő tevékenységek

Az alábbi táblázatban szereplő tevékenységek a runbookok-és DSC-konfigurációk változóinak elérésére szolgálnak. A `Get-AzAutomationVariable` és `Get-AutomationVariable` közötti különbséget a cikk elején található titkosított változóknál találja.

| Tevékenység | Leírás |
|:---|:---|
|`Get-AutomationVariable`|Egy létező változó értékét kérdezi le.|
|`Set-AutomationVariable`|Beállítja egy létező változó értékét.|

> [!NOTE]
> Kerülje a változók használatát a `Get-AutomationVariable` `Name` paraméterében runbook vagy DSC-konfigurációban. Ennek a paraméternek a használata megnehezítheti a runbookok-vagy DSC-konfigurációk és az Automation-változók közötti függőségek felderítését a tervezési időszakban.

Az alábbi táblázatban szereplő függvények a változók Python2-runbook való eléréséhez és lekéréséhez használatosak.

|Python2 függvények|Leírás|
|:---|:---|
|`automationassets.get_automation_variable`|Egy létező változó értékét kérdezi le. |
|`automationassets.set_automation_variable`|Beállítja egy létező változó értékét. |

> [!NOTE]
> Az Asset functions eléréséhez importálnia kell a `automationassets` modult a Python-runbook tetején.

## <a name="creating-a-new-automation-variable"></a>Új Automation-változó létrehozása

### <a name="create-a-new-variable-using-the-azure-portal"></a>Új változó létrehozása a Azure Portal használatával

1. Az Automation-fiókban kattintson az **eszközök** csempére, majd az **eszközök** panelre, és válassza a **változók**lehetőséget.
2. A **változók** csempén válassza a **változó hozzáadása**elemet.
3. Fejezze be a beállításokat az **új változó** panelen, majd kattintson a **Létrehozás** gombra az új változó mentéséhez.

>[!NOTE]
>Ha el szeretné távolítani egy változó titkosítását, törölnie kell a változót, és újból létre kell hoznia a titkosítást.

### <a name="create-a-new-variable-with-windows-powershell"></a>Új változó létrehozása a Windows PowerShell használatával

A szkript a `New-AzAutomationVariable` parancsmag használatával létrehoz egy új változót, és beállítja a kezdeti értékét. Ezt követően a `Get-AzAutomationVariable`használatával kérheti le az értéket. Ha az érték egy egyszerű típus, akkor a rendszer ugyanazt a típust kéri le. Összetett típus esetén a rendszer lekéri a `PSCustomObject` típusát.

Az alábbi példa bemutatja, hogyan hozhat létre karakterlánc típusú változót, majd adja vissza az értékét.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Az alábbi példa bemutatja, hogyan hozhat létre egy összetett típusú változót, majd lekéri a tulajdonságait. Ebben az esetben a [Get-AzVM virtuálisgép-](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) objektumot használja a rendszer.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Változó használata runbook vagy DSC-konfigurációban

A `Set-AutomationVariable` tevékenységgel egy Automation-változó értékét állíthatja be egy PowerShell-runbook vagy DSC-konfigurációban, és a `Get-AutomationVariable` a lekéréséhez. A runbook-vagy DSC-konfigurációban ne használja a `Set-AzAutomationVariable` és `Get-AzAutomationVariable` parancsmagokat vagy azok AzureRM moduljának megfelelőjét, mivel azok kevésbé hatékonyak, mint a munkafolyamat-tevékenységek. 

Vegye figyelembe, hogy `Get-AzAutomationVariable` vagy annak AzureRM moduljának megfelelő biztonsági változó értékét nem lehet lekérni. 

Az új változók runbook vagy DSC-konfigurációból való létrehozásának egyetlen módja a `New-AzAutomationVariable` parancsmag használata.

### <a name="textual-runbook-samples"></a>Szöveges runbook minták

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Egyszerű érték beállítása és lekérése egy változóból

Az alábbi példák azt mutatják be, hogyan lehet változót beállítani és beolvasni egy szöveges runbook. Ez a példa feltételezi a `NumberOfIterations` és `NumberOfRunnings` nevű egész számú változók létrehozását, valamint egy `SampleMessage`nevű karakterlánc-változót.

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

#### <a name="set-and-retrieve-a-variable-in-a-python2-runbook"></a>Változó beállítása és lekérése Python2-runbook

Az alábbi példa bemutatja, hogyan lehet változót használni, változót beállítani és egy nem létező változóhoz tartozó kivételt kezelni egy Python2-runbook.

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

### <a name="graphical-runbook-samples"></a>Grafikus runbook minták

Grafikus runbook felveheti a `Get-AutomationVariable` vagy `Set-AutomationVariable` tevékenységet. Egyszerűen kattintson a jobb gombbal a változóra a grafikus szerkesztő könyvtár paneljén, és válassza ki a kívánt tevékenységet.

![Változó hozzáadása a vászonhoz](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Értékek beállítása változóban

Az alábbi képen egy olyan minta tevékenységek láthatók, amelyek egy egyszerű értékkel rendelkező változót frissítenek egy grafikus runbook. Ebben a példában a `Get-AzVM` egyetlen Azure-beli virtuális gépet kér le, és a számítógép nevét egy meglévő Automation string változóba menti. Nem számít, hogy a [hivatkozás egy folyamat vagy egy folyamat](../automation-graphical-authoring-intro.md#links-and-workflow) , mert a kód csak egyetlen objektumot vár a kimenetben.

![Egyszerű változó beállítása](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni a tevékenységek grafikus létrehozással való csatlakoztatásáról, tekintse meg a [hivatkozások grafikus szerzői](../automation-graphical-authoring-intro.md#links-and-workflow)műveletekben című részt.
- A grafikus runbookok megkezdéséhez tekintse meg [az első grafikus runbook](../automation-first-runbook-graphical.md).
