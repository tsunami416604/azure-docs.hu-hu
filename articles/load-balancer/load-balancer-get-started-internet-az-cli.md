---
title: Terheléselosztó létrehozása az Azure parancssori felülettel
titleSuffix: Azure Load Balancer
description: Megtudhatja, hogyan hozhat létre nyilvános standard Load Balancer a Zone-redundáns nyilvános IP-cím felülettel az Azure CLI használatával
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: allensu
ms.openlocfilehash: b9e8874883b4155d38209da07590d5ce720d2335
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894528"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-cli"></a>Standard Load Balancer létrehozása az Azure parancssori felülettel redundáns előtér-szolgáltatással

Ez a cikk a nyilvános IP-címet használó, nyilvános IP-cím használatával ellátott nyilvános [Load Balancer standard](https://aka.ms/azureloadbalancerstandard) kialakításának lépéseit ismerteti.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor győződjön meg arról, hogy telepítette a legújabb [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -t, és bejelentkezett egy Azure-fiókba az [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)paranccsal.

> [!NOTE]
>  A Availability Zones támogatása elérhető az Azure-erőforrások és-régiók kiválasztásához, valamint a virtuális gépek méretének megadásához. További információ az első lépésekről, valamint arról, hogy az Azure-erőforrások,-régiók és a virtuális gépek méretének családja milyen módon próbálhatja ki a rendelkezésre állási zónákat: [Availability Zones áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a következő parancs használatával:

```azurecli-interactive
az group create --name myResourceGroupSLB --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Nyilvános IP-szabvány létrehozása

Hozzon létre egy nyilvános IP-címet a következő parancs használatával:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Load Balancer létrehozása

Hozzon létre egy nyilvános Load Balancer standardot a standard nyilvános IP-címmel, amelyet az előző lépésben hozott létre a következő parancs használatával:

```azurecli-interactive
az network lb create --resource-group myResourceGroupSLB --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>LB-mintavétel létrehozása a 80-as porton

Hozza létre a terheléselosztó állapotának mintavételét a következő paranccsal:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupSLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>LB-szabály létrehozása a 80-es porthoz

Hozzon létre egy terheléselosztó-szabályt a következő parancs használatával:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Következő lépések
- További információ a [standard Load Balancer és a rendelkezésre állási zónákról](load-balancer-standard-availability-zones.md).



