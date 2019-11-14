---
title: A virtuálisgép-bővítmények telepítésének korlátozása Azure Policy használatával
description: A virtuálisgép-bővítmények üzembe helyezésének korlátozásához használja a Azure Policy.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 113736198f40510981c80909c862282fa07ac68d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073779"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>A bővítmények Linux rendszerű virtuális gépeken való telepítésének korlátozása a Azure Policy használatával

Ha meg szeretné akadályozni bizonyos bővítmények használatát vagy telepítését a Linux rendszerű virtuális gépeken, létrehozhat egy Azure-szabályzatot a parancssori felület használatával a virtuális gépek bővítményeinek korlátozásához az erőforráscsoporthoz. 

Ez az oktatóanyag a CLI-t használja a Azure Cloud Shellon belül, amely folyamatosan frissül a legújabb verzióra. Ha helyileg szeretné futtatni az Azure CLI-t, telepítenie kell a 2.0.26 vagy újabb verziót. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Szabály létrehozása fájl

A bővítmények telepítésének korlátozásához rendelkeznie kell egy olyan [szabállyal](../../governance/policy/concepts/definition-structure.md#policy-rule) , amely megadja a logikát a bővítmény azonosításához.

Ebből a példából megtudhatja, hogyan tagadhatja meg a "Microsoft. OSTCExtensions" által közzétett bővítmények telepítését egy Azure Cloud Shell, de ha a parancssori felület helyileg dolgozik, létrehozhat egy helyi fájlt is, és lecserélheti az elérési utat (~/clouddrive) a helyi fájl elérési útjára a gépen.

Egy [bash-Cloud Shell](https://shell.azure.com/bash)írja be a következőt:

```azurecli-interactive 
vim ~/clouddrive/azurepolicy.rules.json
```

Másolja és illessze be a következő. JSON fájlt a fájlba.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Ha elkészült, nyomja le az **ESC** billentyűt, majd írja be a következőt **: wq** a fájl mentéséhez és bezárásához.


## <a name="create-a-parameters-file"></a>Parameters-fájl létrehozása

Szükség van egy [paraméter](../../governance/policy/concepts/definition-structure.md#parameters) -fájlra is, amely létrehoz egy struktúrát, amellyel elvégezhető a letiltani kívánt bővítmények listájának átadása. 

Ebből a példából megtudhatja, hogyan hozhat létre a Linux rendszerű virtuális gépekhez tartozó Parameters-fájlt a Cloud Shellban, de ha helyileg dolgozik a CLI-ben, létrehozhat egy helyi fájlt is, és lecserélheti az elérési utat (~/clouddrive) a helyi fájl elérési útjára a gépen.

A [bash Cloud Shell](https://shell.azure.com/bash)írja be a következőt:

```azurecli-interactive
vim ~/clouddrive/azurepolicy.parameters.json
```

Másolja és illessze be a következő. JSON fájlt a fájlba.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Ha elkészült, nyomja le az **ESC** billentyűt, majd írja be a következőt **: wq** a fájl mentéséhez és bezárásához.

## <a name="create-the-policy"></a>A szabályzat létrehozása

A házirend-definíció a használni kívánt konfiguráció tárolására szolgáló objektum. A házirend-definíció a szabályok és paraméterek fájlok használatával határozza meg a szabályzatot. Hozza létre a házirend-definíciót az [az Policy definition Create](/cli/azure/role/assignment?view=azure-cli-latest)paranccsal.

Ebben a példában a szabályok és paraméterek a létrehozott és. JSON-fájlként tárolt fájlok a Cloud shellben.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>A szabályzat kiosztása

Ez a példa hozzárendeli a szabályzatot egy erőforráscsoporthoz az [az Policy hozzárendelés létrehozása](/cli/azure/policy/assignment)paranccsal. A **myResourceGroup** ERŐFORRÁSCSOPORTHOZ létrehozott virtuális gépek nem telepíthetik a linuxos virtuális gép elérését vagy a Linuxhoz készült egyéni parancsfájl-bővítményeket. Ahhoz, hogy hozzá lehessen rendelni a szabályzatot, az erőforráscsoport léteznie kell.

Az az [Account List](/cli/azure/account?view=azure-cli-latest) paranccsal kérheti le az előfizetés azonosítóját, hogy az a példában szereplő helyett használja.


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>A szabályzat tesztelése

A szabályzat teszteléséhez hozzon létre egy új virtuális gépet, és próbálkozzon új felhasználó hozzáadásával.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Hozzon létre egy **myNewUser** nevű új felhasználót a virtuálisgép-hozzáférési bővítmény használatával.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>A hozzárendelés eltávolítása

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>A házirend eltávolítása

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>További lépések

További információ: [Azure Policy](../../governance/policy/overview.md).