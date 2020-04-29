---
title: 'CLI-minta: hozzon létre két virtuális gépet belső és külső NSG'
description: Hozzon létre két virtuális gépet belső és külső NSG az Azure CLI használatával a hálózati forgalom biztonságossá tételéhez.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b4339ab2c512a96614158f673cb07b60184f5f71
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81459950"
---
# <a name="secure-network-traffic-between-virtual-machines-using-an-nsg"></a>Biztonságos hálózati forgalom a virtuális gépek között NSG használatával

Ez a szkript két virtuális gépet hoz létre, és biztonságossá teszi a rájuk irányuló forgalmat. Az egyik virtuális gép az interneten érhető el, és egy olyan hálózati biztonsági csoporttal (NSG) rendelkezik, amelynek konfigurációja a 22-es és a 80-as porton engedélyezi a forgalmat. A második virtuális gép nem érhető el az interneten, és egy olyan NSG-vel rendelkezik, amelynek konfigurációja csak az első virtuális gépről érkező forgalmat engedélyezi.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Létrehoz egy Azure-beli virtuális hálózatot és alhálózatot. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet) | Létrehoz egy alhálózatot. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, a virtuális hálózathoz, az alhálózathoz és az NSG-hez. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Egy hálózati biztonsági csoport szabályával kapcsolatos információkat ad vissza. Ebben a példában a szabály nevét eltároljuk egy változóban, hogy a szkript későbbi részében is fel lehessen használni. |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Frissít egy NSG-szabályt. Ebben a példában a háttérrendszerre vonatkozó szabályt frissítjük, hogy csak az előtéri alhálózatról érkező forgalmat engedje át. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

A virtuális gépekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
