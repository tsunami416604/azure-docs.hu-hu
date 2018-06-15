---
title: Azure CLI 2.0-minták – Virtuálisgép-méretezési csoport létrehozása | Microsoft Docs
description: Azure CLI 2.0-minták
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 8eda45a5e886f755f9f1850b146fd0b0521d1cba
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246457"
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Virtuálisgép-méretezési csoport létrehozása az Azure CLI 2.0 használatával
Ez a szkript egy Azure-beli virtuálisgép-méretezési csoportot hoz létre Ubuntu operációs rendszerrel és kapcsolódó hálózati erőforrásokkal, beleértve egy terheléselosztót is. A szkript futtatása után a virtuálisgép-példányokat SSH-kapcsolaton keresztül érheti el.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/simple-scale-set/simple-scale-set.sh "Create a simple virtual machine scale set")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
Az alábbi paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése
A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuálisgép-méretezési csoport és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Létrehozza a virtuálisgép-méretezési csoportot, és csatlakoztatja a virtuális hálózathoz, alhálózathoz és hálózati biztonsági csoporthoz. Továbbá egy terheléselosztót is létrehoz, amely elosztja a forgalmat a virtuálisgép-példányok között. A parancs megadja emellett a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az group delete](/cli/azure/ad/group#delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
Az Azure CLI 2.0-val kapcsolatos további információért lásd az [Azure CLI 2.0 parancssori felületdokumentációját](https://docs.microsoft.com/cli/azure/overview).

A virtuálisgép-méretezési csoportokhoz kapcsolódó további Azure CLI 2.0-példaszkripteket az [Azure virtuálisgép-méretezési csoportok dokumentációjában](../cli-samples.md) találhat.