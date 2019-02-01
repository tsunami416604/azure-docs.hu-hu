---
title: Azure-beli virtuálisgép-méretezési csoportok végrehajtási használata |} A Microsoft Docs
description: Ismerje meg, hogyan feladatütemezési bővítmény kiépítése több bővítményt a virtuálisgép-méretezési csoportok üzembe helyezésekor.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: aa6ba07168e86b2163324c62f8bb32ca330551f3
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55501381"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Feladatütemezési bővítmény kiépítése a virtuálisgép-méretezési csoportok
Azure virtuálisgép-bővítmények például az üzembe helyezés utáni konfigurációs és felügyeleti, figyelés, biztonság és további funkciókat biztosítanak. Éles környezetekben üzemelő példányok több bővítményt a Virtuálisgép-példányokon konfigurált kombinációját általában használni kívánt eredmények elérése érdekében.

Ha több bővítményt használok egy virtuális gépen, fontos győződjön meg arról, hogy az ugyanazon operációs rendszer-erőforrásokat igénylő bővítmények nem megpróbálja beszerezni ezeket az erőforrásokat egyszerre. Néhány bővítmény is függenek más bővítmények adja meg a szükséges konfigurációk, például a környezeti beállítások és a titkos kulcsok. Függő bővítmény központi telepítések anélkül, hogy a megfelelő rendezése és alkalmazás-előkészítés helyen, sikertelen lehet.

Ez a cikk ismerteti, hogyan kell konfigurálni a Virtuálisgép-példányok a virtuálisgép-méretezési csoportok bővítményei is feladatütemezési.

## <a name="pre-requisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy ismeri a:
-   Az Azure virtuális gép [bővítmények](../virtual-machines/extensions/overview.md)
-   [Módosítása](virtual-machine-scale-sets-upgrade-scale-set.md) a virtual machine scale sets

## <a name="when-to-use-extension-sequencing"></a>Mikor érdemes használni a sorrendről
Műveleti sorrend a bővítmények a nem kötelező a méretezési csoportok és, hacsak nincs megadva, bővítmények bővítheti bármilyen sorrendben scale set-példány.

Például ha a méretezési csoport modelljéből két bővítmények – ExtensionA és ExtensionB – a modellben megadott majd vagy a következő üzembe helyezési feladatütemezések fordulhat elő:
-   ExtensionA ExtensionB ->
-   ExtensionB ExtensionA ->

Ha az alkalmazás mindig bővítmény B előtt kiosztandó bővítmény egy igényel, inkább sorrendről ebben a cikkben leírtak szerint. A végrehajtási, csak egy feladatütemezési most történik:
-   ExtensionA - > ExtensionB

Nincs megadva egy meghatározott kiépítési sorrendben kiterjesztések bármikor, beleértve a előtt, miután vagy során egy megadott feladatütemezési bővítheti. Végrehajtási csak adja meg, hogy egy adott kiterjesztéssel üzembe fogja helyezni egy másik megadott kiterjesztés után. Ne befolyásolja a modellben meghatározott minden más bővítmény kiépítése.

Például ha a bővítmény C van beállítva, A kiterjesztést követően ki kell építeni a méretezési csoport modelljéből rendelkezik három bővítmény – a bővítmény A, a bővítmény B és a bővítmény C – megadott a modellben, majd vagy a következő üzembe helyezési feladatütemezések fordulhat elő:
-   ExtensionA -> ExtensionC ExtensionB ->
-   ExtensionB -> ExtensionA ExtensionC ->
-   ExtensionA -> ExtensionB ExtensionC ->

Győződjön meg arról, hogy nincs más kiterjesztése ki van építve a meghatározott bővítmény feladatütemezés végrehajtása közben van szüksége, azt javasoljuk a méretezési csoport modelljéből összes kiterjesztések sorrendje. A fenti példában bővítmény B állítható bővítmény C után ki kell építeni, hogy csak egy feladatütemezési fordulhat elő:
-   ExtensionA -> ExtensionC ExtensionB ->


## <a name="how-to-use-extension-sequencing"></a>Végrehajtási használata
A feladatütemezési bővítmény kiépítése, frissítenie kell a méretezési csoport modelljét "provisionAfterExtensions", amely elfogadja a kiterjesztést nevei tömbjét tulajdonság a bővítmény definíciójának. A bővítmények a tömb tulajdonságérték említett teljes definiálni kell a a méretezési csoport modelljéből.

### <a name="template-deployment"></a>Sablon telepítése
Az alábbi példa egy sablont, ahol a méretezési csoportban van-e három bővítmény – ExtensionA ExtensionB és ExtensionC – úgy, hogy a bővítmények a sorrendben vannak kiépítve határozza meg:
-   ExtensionA -> ExtensionB ExtensionC ->

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

Mivel a tulajdonság "provisionAfterExtensions" elfogadja a kiterjesztést nevei tömbje, a fenti példában módosítható úgy, hogy ExtensionC ExtensionA és ExtensionB után van kiépítve, de nincs rendezése nem szükséges ExtensionA és ExtensionB között. A következő sablon segítségével érhető el ebben a forgatókönyvben:

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
Az alábbi példa hozzáad egy méretezési csoport modelljéből ExtensionC nevű új bővítmény. ExtensionC ExtensionA és ExtensionB, amely már megadták a méretezési csoport modelljéből függőségekkel rendelkezik.

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

Ha ExtensionC korábban definiálva lett a méretezési csoport modell, és most szeretne adja a függőségeket, akkor futtathat egy `PATCH` szerkesztheti a már üzembe helyezett bővítmény tulajdonságai.

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
Meglévő méretezési csoport példányaihoz módosítások a következő [frissítése](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Használja a [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) parancsmag használatával adja hozzá az alkalmazás állapotának bővítményt a méretezési csoport állítsa be a modell definícióját. Végrehajtási Az PowerShell 1.2.0-s vagy annál használatát igényli.

Az alábbi példa hozzáadja a [Application Health bővítmény](virtual-machine-scale-sets-health-extension.md) , a `extensionProfile` egy méretezésicsoport-modellben Windows-alapú méretezési. Az alkalmazás állapotának bővítmény kiépítése után lesznek üzembe helyezve a [egyéni szkriptek futtatására szolgáló bővítmény](../virtual-machines/extensions/custom-script-windows.md)már definiált, a méretezési csoportban.

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
Használat [az vmss-bővítmény beállítása](/cli/azure/vmss/extension#az-vmss-extension-set) hozzáadása az alkalmazásállapot bővítményt a méretezési csoport modell definícióját. Végrehajtási szükséges Azure CLI 2.0.55 vagy újabb.

Az alábbi példa hozzáadja a [Application Health bővítmény](virtual-machine-scale-sets-health-extension.md) a méretezési csoport méretezésicsoport-modellben, egy Windows-alapú méretezési csoportot. Az alkalmazás állapotának bővítmény kiépítése után lesznek üzembe helyezve a [egyéni szkriptek futtatására szolgáló bővítmény](../virtual-machines/extensions/custom-script-windows.md)már definiált, a méretezési csoportban.

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

### <a name="not-able-to-add-extension-with-dependencies"></a>A függőségek bővítmény hozzáadása nem sikerül?
1. Győződjön meg arról, hogy a megadott provisionAfterExtensions bővítmények határozzák meg a méretezési csoport modelljéből.
2. Ellenőrizze, hogy nincsenek körkörös függőségek a bevezetésre. Ha például a következő lépéseket nem engedélyezett: ExtensionA -> ExtensionB -> ExtensionC ExtensionA ->
3. Biztosíthatja a függőségeket is, a kiterjesztések bővítmény "Tulajdonságok" a "beállítások" tulajdonság. Például ha ExtentionB kell ExtensionA után ki kell építeni, majd ExtensionA kell ExtensionA "Tulajdonságok" a "beállítások" mezőre. Egy üres "beállítások" tulajdonság is megadhat, ha a bővítmény nem írja elő a kívánt beállításokat.

### <a name="not-able-to-remove-extensions"></a>Nem sikerült eltávolítani a bővítmények?
Győződjön meg arról, hogy az eltávolítani kívánt bővítmények nem szereplő egyéb kiterjesztések provisionAfterExtensions.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [az alkalmazás üzembe helyezéséhez](virtual-machine-scale-sets-deploy-app.md) a virtuálisgép-méretezési csoportok.
