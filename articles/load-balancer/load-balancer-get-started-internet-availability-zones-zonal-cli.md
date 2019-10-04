---
title: Load Balancer létrehozása a zóna-előtérben – Azure parancssori felület
titlesuffix: Azure Load Balancer
description: Megtudhatja, hogyan hozhat létre standard Load Balancert az Azure CLI-vel a zónabeli előtér használatával
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 663567f6e3b078c1cb2afc60c3aaa9fcfb7af4dd
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275247"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-cli"></a>standard Load Balancer létrehozása az Azure parancssori felületének használatával

Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre egy nyilvános [standard Load Balancer](https://aka.ms/azureloadbalancerstandard) egy zónabeli előtérben. A zónákhoz tartozó előtér azt jelenti, hogy minden bejövő vagy kimenő folyamatot a régió egyetlen zónája szolgáltat. Létrehozhat egy olyan terheléselosztó-t, amely egy többhelyes standard nyilvános IP-címet használ a saját felületi konfigurációjában. Hogyan működnek a rendelkezésre állási zónák a Standard Load Balancer ismertetése: [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md). 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a parancssori felület választja, győződjön meg arról, hogy telepítette-e a legújabb [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) és az Azure-fiókkal jelentkezett be [az bejelentkezési](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest).

> [!NOTE]
> A rendelkezésre állási zónák támogatása az Azure-erőforrásokhoz, és a régiók és a virtuális gép méretcsaládjai érhető el. További információ az első lépésekről, és melyik Azure-erőforrások, régiók, és próbálja meg a rendelkezésre állási zónák a virtuális gép méretcsaládjai, lásd: [a rendelkezésre állási zónákat áttekintő](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot, a következő paranccsal:

```azurecli-interactive
az group create --name myResourceGroupZLB --location westeurope
```

## <a name="create-a-public-standard-ip-address"></a>Nyilvános standard IP-cím létrehozása

Hozzon létre egy szabványos nyilvános IP-címet a következő parancs használatával:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupZLB --name myPublicIPZonal --sku Standard --zone 1
```

## <a name="create-a-load-balancer"></a>Load Balancer létrehozása

Hozzon létre egy nyilvános standard Load Balancer a standard nyilvános IP-címmel, amelyet az előző lépésben hozott létre a következő parancs használatával:

```azurecli-interactive
az network lb create --resource-group myResourceGroupZLB --name myLoadBalancer --public-ip-address myPublicIPZonal --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Hozzon létre egy LB probe 80-as porton

Hozzon létre egy betöltési terheléselosztó állapotmintát a következő paranccsal:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupZLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>A 80-as porton LB-szabály létrehozása

Hozzon létre egy terheléselosztó-szabályt a következő paranccsal:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroupZLB --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md).



