---
title: A virtuális gépbővítmény telepítésének korlátozása az Azure-szabályzathasználatával
description: Az Azure Policy használatával korlátozhatja a virtuális gépbővítmény-telepítéseket.
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
ms.openlocfilehash: 3c660f7e05af43c2aad6f7283e32cfc1d85571ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066833"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Az Azure-szabályzat használata a bővítmények linuxos virtuális gépeken történő telepítésének korlátozásához

Ha meg szeretné akadályozni bizonyos bővítmények használatát vagy telepítését a Linux-virtuális gépeken, létrehozhat egy Azure-szabályzatot a CLI használatával, hogy korlátozza a virtuális gépek egy erőforráscsoporton belüli bővítmények et. 

Ez az oktatóanyag a CLI-t használja az Azure Cloud Shellen belül, amely folyamatosan frissül a legújabb verzióra. Ha helyileg szeretné futtatni az Azure CLI-t, telepítenie kell a 2.0.26-os vagy újabb verzióját. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Szabályfájl létrehozása

Annak érdekében, hogy korlátozza, hogy milyen bővítmények telepíthetők, rendelkeznie kell egy [szabály,](../../governance/policy/concepts/definition-structure.md#policy-rule) amely a bővítmény azonosításához a logika.

Ez a példa bemutatja, hogyan tagadhatja meg a "Microsoft.OSTCExtensions" által közzétett bővítmények telepítését egy szabályfájl létrehozásával az Azure Cloud Shellben, de ha helyileg dolgozik a CLI-ben, létrehozhat egy helyi fájlt is, és lecserélheti az elérési utat (~/clouddrive) a gépen lévő helyi fájl elérési útjára.

Bash [Cloud Shell](https://shell.azure.com/bash)mezőbe írja be a következőt:

```bash
vim ~/clouddrive/azurepolicy.rules.json
```

Másolja a fájlt a következő .json fájlba.

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

Ha elkészült, nyomja meg az **Esc** gombot, majd írja be **a :wq** billentyűt a fájl mentéséhez és bezárásához.


## <a name="create-a-parameters-file"></a>Paraméterfájl létrehozása

Szüksége van egy [paraméterfájlra](../../governance/policy/concepts/definition-structure.md#parameters) is, amely létrehoz egy struktúrát, amelyet a letiltandó bővítmények listájának átadásához használhat. 

Ez a példa bemutatja, hogyan hozhat létre paraméterfájlt a Linux-virtuális gépekhez a Cloud Shellben, de ha helyileg dolgozik a CLI-ben, létrehozhat egy helyi fájlt is, és lecserélheti az elérési utat (~/clouddrive) a gépen lévő helyi fájl elérési útjára.

A [bash Cloud Shell](https://shell.azure.com/bash)mezőbe írja be a következőt:

```bash
vim ~/clouddrive/azurepolicy.parameters.json
```

Másolja a fájlt a következő .json fájlba.

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

Ha elkészült, nyomja meg az **Esc** gombot, majd írja be **a :wq** billentyűt a fájl mentéséhez és bezárásához.

## <a name="create-the-policy"></a>A házirend létrehozása

A házirend-definíció olyan objektum, amely a használni kívánt konfiguráció tárolására szolgál. A házirend-definíció a szabályok és a paraméterek fájljait használja a házirend meghatározásához. Hozza létre a házirend-definíciót az [az házirend-definíció létrehozása segítségével.](/cli/azure/role/assignment?view=azure-cli-latest)

Ebben a példában a szabályok és paraméterek a létrehozott és a felhőbeli rendszerhéjban .json fájlként tárolt fájlok.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>A szabályzat hozzárendelése

Ez a példa a házirendet egy erőforráscsoporthoz rendeli az [az házirend-hozzárendelés létrehozása használatával.](/cli/azure/policy/assignment) A **myResourceGroup** erőforráscsoportban létrehozott virtuális gépek nem tudják telepíteni a Linux Virtuálisgép-hozzáférést vagy a Linuxegyéni parancsfájl-bővítményeket. Az erőforráscsoportnak léteznie kell a házirend hozzárendelése előtt.

Az [fióklista](/cli/azure/account?view=azure-cli-latest) használatával lekaphatja az előfizetés-azonosítóját a példában szereplő helyett.


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

Tesztelje a szabályzatot egy új virtuális gép létrehozásával, és próbáljon meg új felhasználót hozzáadni.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Próbáljon meg létrehozni egy új felhasználó nevű **myNewUser** a VM Access bővítmény használatával.

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