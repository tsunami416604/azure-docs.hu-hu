---
title: Azure CLI-minták – Alkalmazások telepítése | Microsoft Docs
description: Azure CLI-minták
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9c1b0d4ea794e5b3ed4f899addef18da2465562a
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54882191"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli"></a>Alkalmazások telepítése virtuálisgép-méretezési csoportokban az Azure CLI használatával
Ez a szkript egy Ubuntu rendszert futtató virtuálisgép-méretezési csoportot hoz létre, és az egyéni szkriptbővítmény használatával telepít alapszintű webalkalmazásokat. A szkript futtatása után hozzáférhet a webalkalmazáshoz egy webböngészőn keresztül.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
Az alábbi paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése
A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuálisgép-méretezési csoport és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/ad/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Létrehozza a virtuálisgép-méretezési csoportot, és csatlakoztatja a virtuális hálózathoz, alhálózathoz és hálózati biztonsági csoporthoz. Továbbá egy terheléselosztót is létrehoz, amely elosztja a forgalmat a virtuálisgép-példányok között. A parancs megadja emellett a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) | Telepíti az Azure egyéni szkriptek futtatására szolgáló bővítményét azon szkript futtatásához, amely előkészíti az adatlemezeket az egyes virtuálisgép-példányokon. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Egy olyan terheléselosztási szabályt hoz létre, amely elosztja a 80-as TCP-port forgalmát a méretezési csoport virtuálisgép-példányai között. |
| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) | Lekéri a terheléselosztó által a felhasználóhoz hozzárendelt nyilvános IP-cím adatait. |
| [az group delete](/cli/azure/ad/group#delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure/overview).

A virtuálisgép-méretezési csoportokhoz kapcsolódó további Azure CLI-példaszkripteket az [Azure virtuálisgép-méretezési csoportok dokumentációjában](../cli-samples.md) találhat.
