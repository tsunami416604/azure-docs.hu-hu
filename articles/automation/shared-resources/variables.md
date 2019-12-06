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
ms.openlocfilehash: e56a1c9a158974266b810d31a0e9bb898262761a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849428"
---
# <a name="variable-assets-in-azure-automation"></a>Változó eszközök Azure Automation

A változó eszközök olyan értékek, amelyek az Automation-fiók összes runbookok és DSC-konfigurációjában elérhetők. Ezek kezelhetők a Azure Portal, a PowerShell, a runbook vagy a DSC-konfiguráció segítségével. Az Automation-változók a következő helyzetekben lehetnek hasznosak:

- Egy érték megosztása több runbookok vagy DSC-konfiguráció között.

- Megoszthat egy értéket több feladat között ugyanabból a runbook vagy DSC-konfigurációból.

- Kezelheti az értéket a portálról vagy a PowerShell-parancssorból, amelyet a runbookok-vagy DSC-konfigurációk használnak, például olyan gyakori konfigurációs elemek készletét, mint a virtuálisgép-nevek adott listája, egy adott erőforráscsoport, egy AD-tartománynév stb.  

Mivel a rendszer megőrzi az Automation-változókat, akkor is elérhetők, ha a runbook vagy a DSC-konfiguráció meghibásodik. Ez a viselkedés lehetővé teszi egy érték egy runbook általi beállítását, amelyet aztán egy másik használ, vagy ugyanazt a runbook vagy DSC-konfiguráció használja a következő futtatásakor.

Változó létrehozásakor megadhatja, hogy a rendszer titkosítva tárolja-e. A titkosított változók tárolása a Azure Automation biztonságosan történik, és az értéke nem kérhető le az Azure PowerShell modul részeként szállított [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) parancsmagból. A titkosított érték lekérésének egyetlen módja a **Get-AutomationVariable** tevékenység RUNBOOK vagy DSC-konfigurációban való használata. Ha egy titkosított változót nem titkosítottra szeretne módosítani, törölje, majd hozza létre újra a változót nem titkosítottként.

>[!NOTE]
>Azure Automation a biztonságos adategységek közé tartoznak a hitelesítő adatok, a tanúsítványok, a kapcsolatok és a titkosított változók. Ezeket az eszközöket a rendszer titkosítja és tárolja Azure Automation az egyes Automation-fiókokhoz generált egyedi kulcs használatával. Ezt a kulcsot a rendszer felügyelt Key Vault tárolja. A biztonságos eszköz tárolása előtt a kulcs betöltődik Key Vault, majd az eszköz titkosítására szolgál. Ezt a folyamatot a Azure Automation felügyeli.

## <a name="variable-types"></a>Változók típusai

Ha a Azure Portal változót hoz létre, meg kell adnia egy adattípust a legördülő listából, hogy a portál megjelenítse a változó értékének megadásához szükséges vezérlőt. A változó nem korlátozódik ehhez az adattípushoz. A változót a Windows PowerShell használatával kell beállítani, ha más típusú értéket szeretne megadni. Ha a **nem definiált**értéket adja meg, akkor a változó értéke **$Null**, és az értéket a [set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) parancsmaggal vagy a **set-AutomationVariable** tevékenységgel kell beállítania. A portálon nem lehet létrehozni vagy módosítani a komplex változó típus értékét, de bármilyen típusú értéket megadhat a Windows PowerShell használatával. Az összetett típusokat a rendszer [pscustomobject formájában kapja](/dotnet/api/system.management.automation.pscustomobject)adja vissza.

Tömb vagy szórótábla létrehozásával és a változóba való mentésével több értéket is tárolhat egyetlen változóban.

Az alábbi lista az Automationben elérhető változó típusok listáját tartalmazza:

* Sztring
* Egész szám
* Dátum és idő
* Logikai
* Null

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-parancsmagok

A AzureRM az alábbi táblázatban található parancsmagokkal automatizálható a hitelesítő adatok eszközei a Windows PowerShell használatával. A [AzureRM. Automation modul](/powershell/azure/overview)részét képezik, amely az Automation runbookok és a DSC-konfigurációkhoz is használható.

| A  parancsmagjai | Leírás |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Egy létező változó értékét kérdezi le.|
|[Új – AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Létrehoz egy új változót, és beállítja annak értékét.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Eltávolít egy meglévő változót.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Beállítja egy létező változó értékét.|

## <a name="activities"></a>Activities (Tevékenységek)

Az alábbi táblázatban szereplő tevékenységek a runbook és a DSC-konfigurációk változóinak elérésére szolgálnak. A Get-AzureRmAutomationVariable és a Get-AutomationVariable parancsmagok közötti különbség a dokumentum elején kerül tisztázásra.

| Activities (Tevékenységek) | Leírás |
|:---|:---|
|Get-AutomationVariable|Egy létező változó értékét kérdezi le.|
|Set-AutomationVariable|Beállítja egy létező változó értékét.|

> [!NOTE]
> Ne használjon változókat a **Get-AutomationVariable** – Name paraméterében egy RUNBOOK vagy DSC konfigurációban, mivel ez megnehezítheti a runbookok-vagy DSC-konfiguráció és az Automation-változók közötti függőségek észlelését a tervezési időszakban.

Az alábbi táblázatban szereplő függvények a változók Python2-runbook való eléréséhez és lekéréséhez használatosak.

|Python2 függvények|Leírás|
|:---|:---|
|automationassets. get_automation_variable|Egy létező változó értékét kérdezi le. |
|automationassets. set_automation_variable|Beállítja egy létező változó értékét. |

> [!NOTE]
> Az Asset functions eléréséhez importálnia kell a "automationassets" modult a Python-runbook tetején.

## <a name="creating-a-new-automation-variable"></a>Új Automation-változó létrehozása

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Új változó létrehozása a Azure Portal

1. Az Automation-fiókban kattintson az **eszközök** csempére, majd az **eszközök** panelen válassza a **változók**lehetőséget.
2. A **változók** csempén válassza a **változó hozzáadása**elemet.
3. Fejezze be a beállításokat az **új változó** panelen, és **kattintson az** új változó mentése lehetőségre.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Új változó létrehozása a Windows PowerShell segítségével

A [New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) parancsmag egy új változót hoz létre, és beállítja a kezdeti értékét. Az értéket a [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)használatával kérheti le. Ha az érték egy egyszerű típus, akkor ugyanazt a típust adja vissza. Összetett típus esetén a rendszer egy **pscustomobject formájában kapja** ad vissza.

Az alábbi példák azt mutatják be, hogyan hozhat létre karakterlánc típusú változót, majd adja vissza az értékét.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Az alábbi példák azt mutatják be, hogyan hozhat létre egy összetett típusú változót, majd adja vissza a tulajdonságait. Ebben az esetben a **Get-AzureRmVm virtuálisgép-** objektumot használja a rendszer.

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Változó használata runbook vagy DSC-konfigurációban

A **set-AutomationVariable** tevékenységgel egy Automation-változó értékét állíthatja be egy PowerShell-RUNBOOK vagy DSC-konfigurációban, a **Get-AutomationVariable** pedig lekérdezheti. A **set-AzureRMAutomationVariable** és a **Get-AzureRMAutomationVariable** parancsmagot nem szabad a runbook-vagy DSC-konfigurációban használni, mivel azok kevésbé hatékonyak, mint a munkafolyamat-tevékenységek. A **Get-AzureRMAutomationVariable**nem tudja beolvasni a biztonságos változók értékét is. Az új változók runbook vagy DSC-konfigurációból való létrehozásának egyetlen módja a [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) parancsmag használata.

### <a name="textual-runbook-samples"></a>Szöveges runbook minták

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Egyszerű érték beállítása és lekérése egy változóból

Az alábbi példák azt mutatják be, hogyan lehet változót beállítani és beolvasni egy szöveges runbook. Ebben a példában feltételezzük, hogy a *NumberOfIterations* és a *NumberOfRunnings* típusú változók, valamint a *SampleMessage* nevű karakterlánc változó lett létrehozva.

```powershell
$NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Változó beállítása és beolvasása a Python2-ben

Az alábbi mintakód bemutatja, hogyan lehet változót használni, változót beállítani és egy nem létező változóhoz tartozó kivételt kezelni egy Python2-runbook.

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
    value = automationassets.get_automation_variable("non-existing variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Grafikus runbook minták

Grafikus runbook a **Get-AutomationVariable** vagy a **set-AutomationVariable** hozzáadásához kattintson a jobb gombbal a változóra a grafikus szerkesztő könyvtár paneljén, és válassza ki a kívánt tevékenységet.

![Változó hozzáadása a vászonhoz](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Értékek beállítása változóban

Az alábbi képen egy olyan minta tevékenységek láthatók, amelyek egy egyszerű értékkel rendelkező változót frissítenek egy grafikus runbook. Ebben a példában a **Get-AzureRmVM** egyetlen Azure-beli virtuális gépet kér le, és a számítógép nevét egy meglévő Automation-változóba menti, amely egy karakterlánc típusú. Nem számít, hogy a [hivatkozás egy folyamat vagy egy folyamat](../automation-graphical-authoring-intro.md#links-and-workflow) , mert csak egyetlen objektumot vár a kimenetben.

![Egyszerű változó beállítása](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni a tevékenységek grafikus létrehozással való összekapcsolásáról, tekintse meg a [hivatkozásokat a grafikus szerzői](../automation-graphical-authoring-intro.md#links-and-workflow) műveletekben
- A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](../automation-first-runbook-graphical.md).
