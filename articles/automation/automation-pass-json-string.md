---
title: JSON-objektum továbbítása Azure Automation-runbookba
description: Hogyan adhatók át a paramétereket JSON-objektumként egy runbookhoz
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: PowerShell, a runbook, json, az azure automation
ms.openlocfilehash: 65f3bfcebdde50352d5e9e2748036d4522b2a991
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424896"
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>JSON-objektum továbbítása Azure Automation-runbookba

Egy runbook egy JSON-fájlt az átadni kívánt adatokat tárolhat hasznos lehet.
Például előfordulhat, hogy hozzon létre egy JSON-fájlt, amely tartalmazza az összes runbook az átadni kívánt paramétert.
Ehhez meg kell konvertálja a JSON-karakterlánc, és majd konvertálja a karakterláncot egy PowerShell-objektumot a tartalmát a runbook való továbbítása előtt.

Ebben a példában létrehozunk egy PowerShell-parancsprogram, amely meghívja ezt [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) egy PowerShell-forgatókönyvet, a tartalmát a JSON-fájllal való átadásához a runbook elindításához.
A PowerShell-runbook elindítja egy Azure virtuális Gépen, a paramétereket JSON-t lett átadva a virtuális gép lekérése.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha Ön még nem rendelkezik, akkor [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) vagy [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
* [Automation-fiók](automation-sec-configure-azure-runas-account.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez.  Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Ezt a gépet leállítjuk és elindítjuk, tehát ne olyan virtuális gépet használjon, amely élesben működik.
* Az Azure Powershell telepítve a helyi gépen. Lásd: [telepítse és konfigurálja az Azure Powershellt](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-4.1.0) beszerzése az Azure PowerShell-lel kapcsolatos információkat.

## <a name="create-the-json-file"></a>A JSON-fájl létrehozása

Írja be a következő vizsgálat egy szövegfájlba, és mentse a fájt `test.json` valahol a helyi számítógépen.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>A runbook létrehozása

Hozzon létre egy új PowerShell-runbook nevű, "Test-Json" az Azure Automationben.
Megtudhatja, hogyan hozhat létre egy új PowerShell-forgatókönyvet, tekintse meg a [az első PowerShell-forgatókönyvem](automation-first-runbook-textual-powershell.md).

Fogadja el a JSON-adatokat, a runbook kell tenniük az objektum bemeneti paraméterként.

A runbook indítására használhatja a JSON-megadott tulajdonságok.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 Mentse, és tegye közzé a runbook az Automation-fiókban.

## <a name="call-the-runbook-from-powershell"></a>A runbookot hívja meg a PowerShellben

Most már Ön is a runbookot hívja meg a helyi gépen Azure PowerShell-lel.
Futtassa a következő PowerShell-parancsokat:

1. Jelentkezzen be az Azure-bA:
   ```powershell
   Connect-AzureRmAccount
   ```
    Adja meg Azure hitelesítő adatait kéri.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** alias már **Connect-AzureRMAccount**. Ha a Keresés a szalagtár elemmel, ha nem látja, akkor **Connect-AzureRMAccount**, használhat **Add-AzureRmAccount**, vagy frissítheti az a modulokat az Automation-fiókban.

1. A JSON-fájl tartalmát, és alakíthatja át egy karakterlánc:
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath` az elérési utat, ahová mentette a JSON-fájl van.
1. Karakterlánc tartalmát `$json` PowerShell-objektummá:
   ```powershell
   $JsonParams = @{"json"=$json}
   ```
1. Hozzon létre egy kivonattáblát a paraméterek a `Start-AzureRmAutomationRunbook`:
   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```
   Figyelje meg, hogy értékét állítja `Parameters` a PowerShell-objektum, amely tartalmazza a JSON-fájlban szereplő értékek alapján. 
1. A runbook indítása
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

A runbook az értékeket fogja használni a JSON-fájlt a virtuális gép elindításához.

## <a name="next-steps"></a>További lépések

* Egy szöveges szerkesztő a PowerShell és a PowerShell-munkafolyamati runbookok szerkesztésével kapcsolatos további tudnivalókért lásd: [az Azure Automationben szöveges runbookok szerkesztése](automation-edit-textual-runbook.md) 
* Létrehozásával és a runbookok importálásával kapcsolatban lásd: [létrehozása vagy importálása az Azure Automation-runbook](automation-creating-importing-runbook.md)



