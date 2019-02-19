---
title: Az Azure Policy használatával korlátozhatja a VM-bővítmény telepítése |} A Microsoft Docs
description: Az Azure Policy használatával korlátozza a virtuális gépek bővítmény üzembe helyezése.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: 1f71276c25e3ec1e5791d9b35f89aa95190c6afd
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341959"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Az Azure Policy használatával korlátozhatja a bővítmények telepítése Linux rendszerű virtuális gépeken

Ha azt szeretné, hogy használatát vagy a Linux rendszerű virtuális gépek az egyes bővítmények telepítését, létrehozhat egy Azure szabályzat a parancssori felületről bővítmények korlátozni a virtuális gépek erőforráscsoporton belül. 

Ebben az oktatóanyagban a parancssori felület belül az Azure Cloud Shellt, amely folyamatosan frissül a legújabb verzióra. Ha azt szeretné, az Azure parancssori felület helyi futtatását, 2.0.26-os verzió telepítenie kell, vagy később. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Hozzon létre egy szabályok fájlt

Korlátozhatja a telepíthető bővítmények, rendelkeznie kell egy [szabály](../../governance/policy/concepts/definition-structure.md#policy-rule) azonosíthatja a bővítményt a logika biztosít.

Ez a példa bemutatja, hogyan megtagadási szabályokat fájl létrehozásával az Azure Cloud Shellben "Microsoft.OSTCExtensions" által közzétett bővítmények telepítése, de ha a parancssori felület helyileg dolgozik, is hozzon létre egy helyi fájlt és az elérési út (~/clouddrive) cserélje le a elérési útja a helyi fájlt a gépén.

Az egy [Cloud Shell bash](https://shell.azure.com/bash), írja be:

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

Amikor végzett, nyomja le az **Esc** kulcsra, és írja be **: wq** mentse és zárja be a fájlt.


## <a name="create-a-parameters-file"></a>A paraméterfájl létrehozása

Is szükség van egy [paraméterek](../../governance/policy/concepts/definition-structure.md#parameters) fájlt, amely számára, hogy az blokkolja a kiterjesztések listája megadásának struktúrát hoz létre. 

Ez a példa bemutatja, hogyan hozzon létre egy paraméterek fájlt a Cloud Shellben a Linux rendszerű virtuális gépekhez, de ha a parancssori felület helyileg dolgozik, is hozzon létre egy helyi fájlt és az elérési út (~/clouddrive) cserélje le a számítógépre a helyi fájl elérési útját.

Az a [Cloud Shell bash](https://shell.azure.com/bash), írja be:

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

Amikor végzett, nyomja le az **Esc** kulcsra, és írja be **: wq** mentse és zárja be a fájlt.

## <a name="create-the-policy"></a>A szabályzat létrehozása

Szabályzat-definíció egy olyan objektum, a konfigurációt, amely a használni kívánt tárolja. A szabályzatdefiníció a szabályzat meghatározására szabályok és a paraméterek-fájlokat használja. Létrehozhatja a szabályzat definíciója [az szabályzatdefiníció létrehozása](/cli/azure/role/assignment?view=azure-cli-latest).

Ebben a példában a szabályok és a paraméterek a következők létrehozott és a cloud shellben .JSON kiterjesztésű fájlként tárolja a fájlokat.

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

Ebben a példában a szabályzatot rendel egy erőforrás csoport használatával [az szabályzat-hozzárendelés létrehozására](/cli/azure/policy/assignment). A létrehozott virtuális Gépeket a **myResourceGroup** erőforráscsoport nem fogja tudni a Linux rendszerű virtuális gép hozzáférést vagy a Custom Script-bővítményeinek telepítése Linux rendszeren. Az erőforráscsoport léteznie kell, mielőtt is hozzárendeli a szabályzatot.

Használjon [az fióklista](/cli/azure/account?view=azure-cli-latest) beolvasni az előfizetés-Azonosítóját használja egy, a példában helyett.


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

A szabályzat új virtuális gép létrehozása és a egy új felhasználót a hozzáadni kívánt ellenőrzéséhez.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Hozzon létre egy új felhasználót nevű próbál **myNewUser** a Virtuálisgép-hozzáférési bővítmény használatával.

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
## <a name="remove-the-policy"></a>Távolítsa el a szabályzatot

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>További lépések

További információkért lásd: [Azure Policy](../../governance/policy/overview.md).