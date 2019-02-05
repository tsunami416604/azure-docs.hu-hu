---
title: A zónaszintű előtérbeli – Azure CLI-vel rendelkező Load Balancer létrehozása
titlesuffix: Azure Load Balancer
description: Ismerje meg, hogyan hozhat létre egy Standard Load Balancer az Azure CLI-vel zónaszintű frontend
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 3b89c11c11276781ec63367247601fccfd2fa858
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699652"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-cli"></a>Standard Load Balancer létrehozása az Azure CLI-vel zónaszintű frontend

Ez a cikk végigvezeti egy nyilvános létrehozása [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) a zónaszintű időtúllépést. A zónaszintű előtérbeli azt jelenti, hogy az egy régióban egy zóna által kiszolgált bármelyik kimenő vagy bejövő folyamathoz kellene. Létrehozhat egy terheléselosztót zónaszintű előtérbeli az előtér-konfigurációban zónaszintű Standard nyilvános IP-cím használatával. Hogyan működnek a rendelkezésre állási zónák a Standard Load Balancer ismertetése: [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md). 

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

Zónaszintű Standard nyilvános IP-cím létrehozása a következő paranccsal:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupZLB --name myPublicIPZonal --sku Standard --zone 1
```

## <a name="create-a-load-balancer"></a>Load Balancer létrehozása

Hozzon létre egy nyilvános Standard Load Balancer a Standard nyilvános IP-cím a következő parancsot az előző lépésben létrehozott:

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



