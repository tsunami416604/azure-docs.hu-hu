---
title: "Az Azure CLI Script Sample – a LÁMPA verem A(z) terheléselosztást alkalmazó szolgáltatásszintű Machin méretezési csoportban lévő telepítése |} Microsoft Docs"
description: "Egy egyéni parancsprogramok futtatására szolgáló bővítmény használatával telepítheti a LÁMPA verem egy elosztott terhelésű virtuális gép méretezési készletben Azure =."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: 23170923d7c05c9b7230cf331725250b2a3c0f09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>A LÁMPA tartozó, egy elosztott terhelésű virtuálisgép-méretezési csoport központi telepítése

Ez a példa hoz létre egy virtuálisgép-méretezési csoport, és egyéni parancsfájl központi telepítése a LÁMPA verem a méretezési csoportban lévő összes virtuális gépén futó bővítmény vonatkozik.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>Kapcsolódás

Ezt a kódot használja, hogyan csatlakozhat a virtuális gépek és a skála beállítása.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az erőforráscsoport, a méretezési és a virtuális gépek eltávolítása a következő parancsot, és az összes kapcsolódó erőforrásokat.

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, virtuális gép, a rendelkezésre állási csoporthoz, terheléselosztó és minden kapcsolódó erőforrások létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az vmss létrehozása](https://docs.microsoft.com/cli/azure/vmss#az_vmss_create) | A virtuálisgép-méretezési csoport létrehozása |
| [az hálózati terheléselosztó szabály létrehozása](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Elosztott terhelésű végpont hozzáadása |
| [az vmss bővítmény beállítása](https://docs.microsoft.com/cli/azure/vmss/extension#az_vmss_extension_set) | A bővítmény, az egyéni parancsprogramok futtatására egy virtuális gépet futtató létrehozása |
| [az vmss frissítés-példányok](https://docs.microsoft.com/cli/azure/vmss#az_vmss_update_instances) | Az egyéni parancsprogrammal üzembe helyezése előtt a bővítmény alkalmazta a méretezési VM-példányokon. |
| [az vmss méretezési](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) | Vertikális felskálázás a méretezési készletben több Virtuálisgép-példányok hozzáadásával. Az egyéni parancsfájl futtatása ezekkel amikor központilag telepítették azokat. |
| [az nyilvános ip-lista](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_list) | A minta által létrehozott virtuális gépek IP-címek lekérése. |
| [az hálózati lb megjelenítése](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_show) | Az előtér- és háttérszolgáltatások a terheléselosztó által használt portok beolvasása. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép CLI parancsfájl minták megtalálhatók a [Azure Linux virtuális dokumentációját](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
