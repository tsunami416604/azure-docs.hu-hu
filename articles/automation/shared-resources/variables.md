---
title: Az Azure Automation változó adategységek
description: Változó adategységek értékek, az összes runbookok és Azure Automation DSC-konfigurációk érhetők el.  Ez a cikk ismerteti a változók és hogyan dolgozhatnak velük a szöveges és a grafikus szerzői műveletek részleteit.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/01/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fc26c0357dcb071c4c75e8684fe47144a04177e4
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58806884"
---
# <a name="variable-assets-in-azure-automation"></a>Az Azure Automation változó adategységek

Változó adategységek olyan értékek, amelyek elérhetők a runbookok és a DSC-konfigurációk az automation-fiókban. Az Azure Portalról, PowerShell, egy runbook vagy DSC-konfiguráció kezelhetők. Automation-változók hasznosak lehetnek a következő forgatókönyvekhez:

- Ossza meg egy érték elérhetővé tétele több runbook vagy DSC-konfigurációk.

- Ossza meg egy érték elérhetővé tétele több feladat a ugyanaz a runbook vagy DSC-konfigurációból.

- Érték kezelése a portálon vagy a runbook vagy DSC-konfigurációk, például egy virtuális gép neve, egy adott erőforráscsoportban, egy AD-tartomány neve és egyéb, például adott lista közös konfigurációelemek készletét által használt PowerShell-parancssorból.  

Automation-változók megmaradnak, mivel azok érhetők el még akkor is, ha a runbook vagy DSC-konfiguráció nem sikerül. Ez a viselkedés lehetővé teszi, hogy egy runbookot, amely egy másik alapján, vagy használja a ugyanaz a runbook vagy DSC-konfiguráció fut a következő futtatáskor kell beállítani az értéket.

Egy változó létrehozásakor a megadhatja, hogy tárolja a titkosított. A titkosított változók biztonságos tárolása az Azure Automationben, és annak értéke nem lehet lekérdezni a [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) parancsmagot, amely az Azure PowerShell-modul részeként. Titkosított érték beolvasásának egyetlen módja a rendszer a **Get-AutomationVariable** tevékenység a runbookból vagy DSC-konfigurációból.

>[!NOTE]
>Az Azure Automationben biztonságos eszközök tartalmazzák, hitelesítő adatok, tanúsítványok, kapcsolatok és a titkosított változókat. Ezek az eszközök titkosítottak és a létrehozott egyedi kulcs segítségével minden automation-fiókhoz tartozó Azure Automation tárolja. Ezt a kulcsot tárolja a rendszer által felügyelt Key Vault. A kulcs tárolása egy biztonságos objektumot, előtt betöltése a Key Vaultból és majd az eszköz titkosításához használt. Ez a folyamat az Azure Automation felügyeli.

## <a name="variable-types"></a>Változótípusok

Az Azure portal használatával hozzon létre egy változót, ha meg kell adnia a legördülő listából válassza ki az adattípust, így a portálon írja be a változó értékének megfelelő vezérlőket tudja megjeleníteni. A változó nem ez az adattípus korlátozva. A Windows PowerShell használatával, ha azt szeretné, adjon meg egy értéket egy eltérő típusú változót kell beállítania. Ha megad **nincs definiálva**, majd a változó értékét állítja **$null**, és be kell az értéket a [Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) parancsmag vagy **Set-AutomationVariable** tevékenység. Nem hozható létre vagy módosítsa az értéket egy változó komplex típus a portálon, de megadhatja a Windows PowerShell segítségével bármilyen típusú érték. Komplexní typy mezeje egy [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Egy tömb vagy kivonattábla létrehozásával és mentésével, a változó változóhoz több érték is tárolhatja.

Elérhető az Automation változó típusainak listáját a következők:

* String
* Egész szám
* DateTime
* Logikai
* Null

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-parancsmagok

Az alábbi táblázatban a parancsmagok AzureRM, létrehozása és kezelése automation hitelesítő eszközök a Windows PowerShell használatával történik. Részét képezi a [AzureRM.Automation modul](/powershell/azure/overview), amely is elérhető az Automation-runbookok és a DSC-konfigurációk használatát.

| Parancsmagok | Leírás |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Egy létező változó értékét kérdezi le.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Új változót hoz létre, és beállítja annak értékét.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Eltávolítja a meglévő változókat.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Beállítja egy létező változó értékét.|

## <a name="activities"></a>Tevékenységek

Az alábbi táblázatban felsorolt tevékenységek a runbookok és a DSC-konfigurációk hitelesítő adatok elérésére használhatók.

| Tevékenységek | Leírás |
|:---|:---|
|Get-AutomationVariable|Egy létező változó értékét kérdezi le.|
|Set-AutomationVariable|Beállítja egy létező változó értékét.|

> [!NOTE]
> Kerülendő a változók használata a – Name paraméterében **Get-AutomationVariable** egy runbook vagy DSC-konfigurációja, mivel ez megnehezítheti a runbook vagy DSC-konfiguráció és automatizálás közti függőségek változók a tervezés során.

Az alábbi táblázat a functions elérni és beolvasni a Python2 runbookban található változók használhatók.

|Python2-funkciók|Leírás|
|:---|:---|
|automationassets.get_automation_variable|Egy létező változó értékét kérdezi le. |
|automationassets.set_automation_variable|Beállítja egy létező változó értékét. |

> [!NOTE]
> Importálnia kell a "automationassets" modul a Python-alapú runbook tetején az eszközintelligencia-funkciók eléréséhez.

## <a name="creating-a-new-automation-variable"></a>Új automatizálási változó létrehozása

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Új változó létrehozása az Azure portal használatával

1. Az Automation-fiókjából, kattintson a **eszközök** csempére, majd a a **eszközök** panelen válassza ki **változók**.
2. Az a **változók** csempéről válassza **változó hozzáadása**.
3. Végezze el a beállításokat a a **új változó** panelre, és kattintson **létrehozás** az új változó mentéséhez.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Új változó létrehozása a Windows PowerShell-lel

A [New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) parancsmag új változót hoz létre, és a kezdeti értéket. Az érték használatával lekérheti [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Ha az egyszerű típusú érték, majd, hogy ugyanolyan típusú adja vissza. Ha egy összetett típus akkor **PSCustomObject** adja vissza.

Az alábbi mintaparancsok bemutatják, hogyan hozzon létre egy karakterlánc típusú változót, és visszatér az értékét.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Az alábbi mintaparancsok bemutatják, hogyan hozzon létre egy változót egy összetett típusú, és visszatér a tulajdonságait. Ebben az esetben a virtuális gépek objektum **Get-AzureRmVm** szolgál.

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Egy változó egy runbook vagy DSC-konfiguráció használata

Használja a **Set-AutomationVariable** tevékenység és a egy automatizálási változó értékét állíthatja be egy PowerShell-runbook vagy DSC-konfiguráció, és a **Get-AutomationVariable** is lekérheti azt. Ne használja a **Set-AzureRMAutomationVariable** vagy **Get-AzureRMAutomationVariable** parancsmagok a runbookból vagy DSC-konfigurációból óta kevésbé hatékonyak, mint a munkafolyamat-tevékenységek. Emellett nem lehet beolvasni a biztonságos változók értékét **Get-AzureRMAutomationVariable**. Hozzon létre egy új változót a runbookból vagy DSC-konfigurációból belül csak úgy, hogy használja a [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) parancsmagot.

### <a name="textual-runbook-samples"></a>Szöveges runbook-minták

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Beállítási és lekérési egyszerű érték a változóból

Az alábbi mintaparancsok bemutatják, hogyan beállítása és lekérése egy változót, a szöveges runbookok. Ebben a példában azt feltételezzük, hogy az egész szám típusú nevű *NumberOfIterations* és *NumberOfRunnings* és nevű, karakterlánc típusú változót *példában feltételezzük* rendelkezik létrejött.

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

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>Beállítás és a egy összetett objektumot egy változóban beolvasása

Az alábbi mintakód bemutatja, hogyan frissítheti egy változót egy összetett értéket képviselő szöveges runbookok. Ebben a példában az Azure virtuális gép kérhető le **Get-AzureVM** és a egy meglévő Automation változó mentve.  A [változótípusok](#variable-types), ez egy PSCustomObject van tárolva.

```powershell
$vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
Set-AutomationVariable -Name "MyComplexVariable" -Value $vm
```

Az alábbi kódban értékét olvassa be a változót, és a virtuális gép indításához használt.

```powershell
$vmObject = Get-AutomationVariable -Name "MyComplexVariable"
if ($vmObject.PowerState -eq 'Stopped') {
    Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
}
```

#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>Beállítás és a egy változóban gyűjtemény beolvasása

Az alábbi mintakód bemutatja, hogyan használhat egy változót az összetett értékek a szöveges runbookok. Ebben a példában a több Azure-beli virtuális gépek a blobnevet **Get-AzureVM** és a egy meglévő Automation változó mentve. A [változótípusok](#variable-types), a tárolás PSCustomObjects gyűjteménye.

```powershell
$vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}
Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms
```

Az alábbi kódot a gyűjtemény olvassa be a változót, és minden virtuális gép indításához használt.

```powershell
$vmValues = Get-AutomationVariable -Name "MyComplexVariable"
ForEach ($vmValue in $vmValues)
{
    if ($vmValue.PowerState -eq 'Stopped') {
        Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
    }
}
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>És a Python2-változó beolvasása

Az alábbi mintakód bemutatja, hogyan változók használata, állítson be egy változót és a egy nem létező változó a Python2-forgatókönyvem kivétel kezeléséhez.

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

### <a name="graphical-runbook-samples"></a>Grafikus runbook-minták

A grafikus runbookokban, adja hozzá a **Get-AutomationVariable** vagy **Set-AutomationVariable** kattintson a jobb gombbal a változó a könyvtár ablaktáblán, a grafikus szerkesztő, és kiválasztja a tevékenységet, szeretné.

![Adja hozzá a vászonhoz változót](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Egy változó értékeinek beállítása

Az alábbi képen látható a minta tevékenységeket változó frissíteni egy grafikus runbook egyszerű értékkel. Ebben a példában **Get-AzureRmVM** beolvassa egy meglévő Automation változó karakterlánc típusú ment egy Azure virtuális gépen, és a számítógép nevét. Nem számít-e a [hivatkozás egy folyamat vagy egy feladatütemezési](../automation-graphical-authoring-intro.md#links-and-workflow) mivel csak egyetlen objektumot várt a kimenetben.

![Egyszerű változó beállítása](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>További lépések

- Tevékenységek összekapcsolása a grafikus létrehozásról kapcsolatos további információkért lásd: [hivatkozások a grafikus létrehozásról](../automation-graphical-authoring-intro.md#links-and-workflow)
- A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](../automation-first-runbook-graphical.md).
