---
title: Az Azure CLI Példaszkript – egy elosztott terhelésű virtuálisgép-méretezési csoportot a LAMP-verem üzembe helyezése |} A Microsoft Docs
description: Egy egyéni szkriptbővítmény használatával helyezheti üzembe a LAMP-vermet az Azure-on egy elosztott terhelésű virtuálisgép-méretezési csoportban.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: ff4c41ff6428ba6ef88473508830ec8545be8778
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731104"
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>A LAMP-verem üzembe helyezése egy elosztott terhelésű virtuálisgép-méretezési csoportban

Ez a példa létrehoz egy virtuálisgép-méretezési csoportot, és egy bővítményt alkalmaz, amely üzembe helyezi a LAMP-vermet a méretezési csoportba tartozó mindegyik virtuális gépen.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>Kapcsolódás

A kód használatával látható, hogyan lehet a virtuális gépekhez és a méretezési csoporthoz kapcsolódni.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és a virtuális gépek, valamint az összes kapcsolódó erőforrás.

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép, egy rendelkezésre állási csoport, egy terheléselosztó és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss) | Létrehoz egy virtuálisgép-méretezési csoportot. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Hozzáad egy elosztott terhelésű végpontot. |
| [az vmss extension set](https://docs.microsoft.com/cli/azure/vmss/extension) | Létrehozza a bővítményt, amely futtatja majd az egyéni szkriptet egy virtuális gép üzemelő példányán. |
| [az vmss update-instances](https://docs.microsoft.com/cli/azure/vmss) | Futtatja az egyéni szkriptet a bővítmény a méretezési csoporton való alkalmazását megelőzően üzembe helyezett virtuálisgép-példányokon. |
| [az vmss scale](https://docs.microsoft.com/cli/azure/vmss) | Vertikálisan felskálázza a méretezési csoportot további virtuálisgép-példányok hozzáadásával. Az egyéni szkript ezeken is lefut az üzembe helyezésük alkalmával. |
| [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip) | Lekéri a példaszkript által létrehozott virtuális gépek IP-címét. |
| [az network lb show](https://docs.microsoft.com/cli/azure/network/lb) | Lekéri a terheléselosztó által használt előtér- és háttérportokat. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

A virtuális gépekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
