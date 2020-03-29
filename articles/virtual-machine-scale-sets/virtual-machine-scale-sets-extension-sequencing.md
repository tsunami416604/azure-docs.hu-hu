---
title: Bővítmény-szekvenálás használata az Azure virtuálisgép-méretezési készleteivel
description: Megtudhatja, hogyan szekvencia bővítmény kiépítése, ha több bővítmény üzembe helyezése a virtuális gép méretezési csoportok.
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: cde3fb8b56d8509a45bde00dde55e3c69d015b8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278052"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Szekvenciabővítmény kiépítése a virtuálisgép-méretezési készletekben
Az Azure virtuálisgép-bővítmények olyan képességeket biztosítanak, mint a telepítés utáni konfiguráció és felügyelet, a figyelés, a biztonság és egyebek. Éles környezetek általában a virtuális gép példányaihoz konfigurált több bővítmény kombinációját használják a kívánt eredmények elérése érdekében.

Ha több bővítményt használ egy virtuális gépen, fontos, hogy az azonos operációsrendszer-erőforrásokat igénylő bővítmények ne próbálják meg egyszerre beszerezni ezeket az erőforrásokat. Egyes bővítmények is függ más bővítmények, hogy a szükséges konfigurációk, például a környezeti beállítások és a titkos kulcsok. A megfelelő rendezés és szekvenálás nélkül a függő bővítmény-telepítések sikertelenek lehetnek.

Ez a cikk részletezi, hogyan veszenelheti a virtuálisgép-példányok virtuálisgép-példányok virtuálisgép-méretezési készletek ben konfigurálandó bővítmények et.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy ismeri a következőket:
-   Azure [virtuálisgép-bővítmények](../virtual-machines/extensions/overview.md)
-   [Virtuálisgép-méretezési](virtual-machine-scale-sets-upgrade-scale-set.md) készletek módosítása

## <a name="when-to-use-extension-sequencing"></a>Mikor kell használni a bővítményszekvenálást?
A méretezési csoportok hoz nem kötelező szekvenálási bővítmények, és ha nincs megadva, a bővítmények tetszőleges sorrendben kiépíthetők egy méretezési csoport példányában.

Ha például a méretezési modell két kiterjesztéssel – az ExtensionA és az ExtensionB – rendelkezik a modellben, akkor a következő kiépítési sorozatok bármelyike fordulhat elő:
-   ExtensionA -> ExtensionB
-   ExtensionB -> ExtensionA

Ha az alkalmazás megköveteli, hogy az A kiterjesztés mindig ki legyen építve a B kiterjesztés előtt, akkor a jelen cikkben leírtak szerint kell használnia a bővítmény-szekvenálást. A kiterjesztésszekvenálással csak egy sorozat következik be:
-   ExtensionA - > ExtensionB

A meghatározott kiépítési sorrendben nem megadott kiterjesztések bármikor kiépíthetők, beleértve a meghatározott sorrend előtt, után vagy alatt. A bővítmény-szekvenálás csak azt határozza meg, hogy egy adott bővítmény egy másik adott bővítmény után lesz kiépítve. Ez nem befolyásolja a modellben meghatározott bármely más bővítmény kiépítését.

Ha például a méretezési modell három kiterjesztéssel rendelkezik – A kiterjesztés, B kiterjesztés és C kiterjesztés – a modellben, és a C kiterjesztés az A kiterjesztés után van beállítva, akkor a következő kiépítési sorozatok bármelyike fordulhat elő:
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

Ha meg kell győződnie arról, hogy nincs más bővítmény kiépítése a megadott bővítménysorozat végrehajtása közben, javasoljuk, hogy a méretezési modell összes bővítményét egymás után szekvenálása. A fenti példában a B kiterjesztés beállítható úgy, hogy a C kiterjesztés után kiépíthető legyen úgy, hogy csak egy sorozat forduljon elő:
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>A kiterjesztés szekvenálásának használata
Szekvenciabővítmény kiépítése, frissítenie kell a bővítmény definícióját a méretezési készlet modell tartalmazza a tulajdonság "provisionAfterExtensions", amely elfogadja a bővítménynevek tömbje. A tulajdonságtömb értékében említett kiterjesztéseket teljes mértékben meg kell határozni a méretezési modellben.

### <a name="template-deployment"></a>Sablon központi telepítése
A következő példa egy olyan sablont határoz meg, amelyben a méretezési csoport három bővítményt – ExtensionA, ExtensionB és ExtensionC – rendelkezik, így a bővítmények sorrendben vannak kiépítve:
-   ExtensionA -> ExtensionB -> ExtensionC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

Mivel a "provisionAfterExtensions" tulajdonság egy bővítménynév-tömböt fogad el, a fenti példa módosítható úgy, hogy az ExtensionC az ExtensionA és az ExtensionB után legyen kiépítve, de nincs szükség rendelésre az ExtensionA és az ExtensionB között. A forgatókönyv eléréséhez a következő sablon használható:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>REST API
A következő példa egy új ExtensionC nevű bővítményt ad hozzá egy méretezési modellhez. Az ExtensionC függőséget jelent az ExtensionA és az ExtensionB kiterjesztéstől, amelyek már definiálva vannak a méretezési modellben.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

Ha az ExtensionC korábban lett definiálva a méretezési csoportosító modellben, `PATCH` és most hozzá szeretné adni a függőségeit, végrehajthat egy-t a már üzembe helyezett bővítmény tulajdonságainak szerkesztéséhez.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
A meglévő méretezési csoport példányainak módosításai a következő [frissítéskor](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)lesznek alkalmazva.

### <a name="azure-powershell"></a>Azure PowerShell
Az [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) parancsmag segítségével adja hozzá az Alkalmazásállapot-bővítményt a méretezési készlet modelldefinícióhoz. A bővítmény-szekvenálás az Az PowerShell 1.2.0-s vagy újabb használatával igényel.

A következő példa hozzáadja az `extensionProfile` [Alkalmazásállapot-bővítményt](virtual-machine-scale-sets-health-extension.md) egy Windows-alapú méretezési készlet méretezési modelljéhez. Az alkalmazásállapot-bővítmény az [egyéni parancsfájl-bővítmény](../virtual-machines/extensions/custom-script-windows.md)kiépítése után lesz kiépítve, amely már definiálva van a méretezési készletben.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Használja [az vmss bővítménykészletet](/cli/azure/vmss/extension#az-vmss-extension-set) az Alkalmazásállapot-bővítmény hozzáadása a méretezési készlet modell definíciójához. A bővítmény-szekvenálás az Azure CLI 2.0.55 vagy újabb használatát igényli.

A következő példa hozzáadja az [Alkalmazásállapot bővítményt](virtual-machine-scale-sets-health-extension.md) egy Windows-alapú méretezési készlet méretezési modelljéhez. Az alkalmazásállapot-bővítmény az [egyéni parancsfájl-bővítmény](../virtual-machines/extensions/custom-script-windows.md)kiépítése után lesz kiépítve, amely már definiálva van a méretezési készletben.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="not-able-to-add-extension-with-dependencies"></a>Nem tudja hozzáadni a függőségekkel rendelkező bővítményt?
1. Győződjön meg arról, hogy a provisionAfterExtensions bővítményben megadott bővítmények a méretezési készlet modellben vannak definiálva.
2. Győződjön meg arról, hogy nincsenek körkörös függőségek bevezetése. A következő sorrend például nem engedélyezett: ExtensionA -> ExtensionB -> ExtensionC -> ExtensionA
3. Győződjön meg arról, hogy minden olyan bővítmény, amelyen függőségeket vesz igénybe, rendelkezik egy "settings" tulajdonsággal a "Properties" bővítmény alatt. Ha például az ExtentionB-t az ExtensionA után kell kiépíteni, akkor az ExtensionA-nak rendelkeznie kell a "settings" mezővel az ExtensionA "properties" területen. Megadhat egy üres "settings" tulajdonságot, ha a bővítmény nem írja elő a szükséges beállításokat.

### <a name="not-able-to-remove-extensions"></a>Nem tudja eltávolítani a bővítményeket?
Győződjön meg arról, hogy az eltávolított bővítmények nem szerepelnek a provisionAfterExtensions bármely más bővítményhez.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [telepítheti az alkalmazást](virtual-machine-scale-sets-deploy-app.md) a virtuális gép méretezési csoportok.
