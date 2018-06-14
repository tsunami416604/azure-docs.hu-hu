---
title: Azure-házirend segítségével korlátozhatja a Virtuálisgép-bővítmény telepítése |} Microsoft Docs
description: Azure-házirend segítségével korlátozhatja a Virtuálisgép-bővítmény telepítések.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: danis;cynthn
ms.openlocfilehash: 8e65b82730884947633688db9ed50080b96e0b8e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30248155"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Azure-házirend segítségével korlátozhatja a bővítmények telepítése Linux virtuális gépeken

Ha meg szeretné akadályozni, hogy használatát vagy az egyes bővítmények a Linux virtuális gépeken telepítési, létrehozhat egy Azure szabályzatot bővítmények korlátozása a virtuális gépek erőforráscsoporton belül a parancssori felület használatának. 

Ez az oktatóanyag a parancssori felület a rendszerhéjból Azure felhő, amelyek folyamatosan frissül a legfrissebb verziót használja. Ha szeretné helyileg történő futtatása az Azure parancssori felület, akkor verzióját kell telepíteni 2.0.26 vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Szabályok fájl létrehozása

Ahhoz, hogy korlátozza a bővítményekről telepíthető, rendelkeznie kell egy [szabály](/azure/azure-policy/policy-definition#policy-rule) arra, hogy azonosítsa a kiterjesztést a logikát.

Ez a példa bemutatja, hogyan megtagadási szabályokat fájlként Azure Cloud rendszerhéj "Microsoft.OSTCExtensions" által közzétett extensions telepítése, de ha dolgozik CLI helyileg, is hozzon létre egy helyi fájlt, és cserélje le az elérési út (~/clouddrive) elérési útja a helyi fájlt a számítógépre.

Az egy [felhő rendszerhéj bash](https://shell.azure.com/bash), típus:

```azurecli-interactive 
vim ~/clouddrive/azurepolicy.rules.json
```

Másolja és illessze be a következő .JSON kiterjesztésű fájlba.

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

Amikor elkészült, kattintson a **Esc** billentyűt és írja be **: wq** mentse és zárja be a fájlt.


## <a name="create-a-parameters-file"></a>Paraméterek fájl létrehozása

Emellett szükség van egy [paraméterek](/azure/azure-policy/policy-definition#parameters) fájlt, amely blokkolja a sikeres a bővítmények listájának használatára struktúrát hoz létre. 

Ez a példa bemutatja, hogyan hozzon létre egy paraméterfájl felhő rendszerhéj Linux virtuális gépekhez, de ha dolgozik CLI helyileg, is hozzon létre egy helyi fájlt és az elérési út (~/clouddrive) cserélje le a számítógépre a helyi fájl elérési útját.

Az a [felhő rendszerhéj bash](https://shell.azure.com/bash), típus:

```azurecli-interactive
vim ~/clouddrive/azurepolicy.parameters.json
```

Másolja és illessze be a következő .JSON kiterjesztésű fájlba.

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

Amikor elkészült, kattintson a **Esc** billentyűt és írja be **: wq** mentse és zárja be a fájlt.

## <a name="create-the-policy"></a>A házirend létrehozása

Házirend-definíció egy objektum tárolására használni kívánt konfigurációról. A házirend-definíció a házirend meghatározásához a szabályok és a paraméterek fájlokat használja. Hozzon létre, a házirendhez definíciója a [az házirend-definíció létrehozása](/cli/azure/role/assignment?view=azure-cli-latest#az_role_assignment_create).

Ebben a példában a szabályok és a paraméterek olyan a létrehozott és a felhő rendszerhéj .JSON kiterjesztésű fájlként tárolódik.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>A házirend kijelölése

Ebben a példában a házirendet hozzárendeli egy erőforrás csoport használatával [az házirend-hozzárendelés létrehozása](/cli/azure/policy/assignment#az_policy_assignment_create). A létrehozott bármely virtuális gép a **myResourceGroup** erőforráscsoport nem fogja tudni a Linux virtuális gép hozzáférés vagy az egyéni parancsfájl-kiterjesztés telepítése Linux. Az erőforráscsoport már léteznie kell a házirendet rendelhet.

Használjon [az fióklista](/cli/azure/account?view=azure-cli-latest#az_account_list) beolvasni az előfizetés-Azonosítóval, a példában egy helyett.


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

## <a name="test-the-policy"></a>A házirend tesztelése

Tesztelje a házirend egy új virtuális gép létrehozása, majd próbálkozzon egy új felhasználó hozzáadásához.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Hozzon létre egy új felhasználó nevű **myNewUser** a virtuális gép hozzáférési bővítmény használatával.

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
További információkért lásd: [Azure házirend](../../azure-policy/azure-policy-introduction.md).
