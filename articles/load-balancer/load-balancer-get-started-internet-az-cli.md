---
title: Hozzon létre egy nyilvános Load Balancer Standard zónaredundáns nyilvános előtérbeli IP-Címmel cím Azure parancssori felületével |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy nyilvános Load Balancer Standard zónaredundáns nyilvános előtérbeli IP-Címmel cím az Azure CLI használatával
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 23c9d1b56c9a7c49b2cabe434693797fc2d23dfa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967676"
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>Az Azure CLI-vel zónaredundáns előtér egy nyilvános Load Balancer Standard létrehozása

Ez a cikk végigvezeti egy nyilvános létrehozása [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) és a egy zónaredundáns frontend-IP szabványos nyilvános cím segítségével.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a parancssori felület választja, győződjön meg arról, hogy telepítette-e a legújabb [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) és az Azure-fiókkal jelentkezett be [az bejelentkezési](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

> [!NOTE]
 A rendelkezésre állási zónák támogatása az Azure-erőforrásokhoz, és a régiók és a virtuális gép méretcsaládjai érhető el. További információ az első lépésekről, és melyik Azure-erőforrások, régiók, és próbálja meg a rendelkezésre állási zónák a virtuális gép méretcsaládjai, lásd: [a rendelkezésre állási zónákat áttekintő](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot, a következő paranccsal:

```azurecli-interactive
az group create --name myResourceGroupSLB --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Hozzon létre egy nyilvános IP Standard

Hozzon létre egy nyilvános IP Standard a következő paranccsal:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Load Balancer létrehozása

A Standard nyilvános IP-címmel, amely a következő parancsot az előző lépésben létrehozott egy nyilvános Load Balancer Standard létrehozása:

```azurecli-interactive
az network lb create --resource-group myResourceGroupSLB --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Hozzon létre egy LB probe 80-as porton

Hozzon létre egy betöltési terheléselosztó állapotmintát a következő paranccsal:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupSLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>A 80-as porton LB-szabály létrehozása

Hozzon létre egy terheléselosztó-szabályt a következő paranccsal:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md).



