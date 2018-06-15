---
title: Hozzon létre egy Load Balancer szabványos nyilvános zónaredundáns nyilvános IP-cím cím előtér Azure parancssori felületével |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy Load Balancer szabványos nyilvános zónaredundáns nyilvános IP-cím cím előtér Azure parancssori felület használatával
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
ms.openlocfilehash: f3f479de8bc3975f4da07a7761ffc99f976db20e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30320237"
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>Hozzon létre egy Load Balancer szabványos nyilvános zónaredundáns előtér Azure parancssori felület használatával

Ez a cikk lépésről-lépésre nyilvános létrehozása [Load Balancer szabványos](https://aka.ms/azureloadbalancerstandard) a zónaredundáns időtúllépést IP szabványos nyilvános cím segítségével.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha telepíti, és a parancssori felület helyileg választja, győződjön meg arról, hogy telepítette-e a legújabb [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) és az Azure-fiókkal bejelentkezett [az bejelentkezési](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

> [!NOTE]
 Rendelkezésre állási zónák támogatása select Azure-erőforrások és a régiók és a virtuális gép mérete családok érhető el. További információ az első lépések, és mely Azure-erőforrások, régiók és virtuális gép mérete családok megpróbálhatja a rendelkezésre állási zónákat, lásd: [rendelkezésre állási zónák áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot, a következő parancsot:

```azurecli-interactive
az group create --name myResourceGroupSLB --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Hozzon létre egy nyilvános IP Standard

Hozzon létre egy nyilvános IP szabványos a következő parancsot:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Load Balancer létrehozása

Hozzon létre egy Load Balancer szabványos nyilvános a szabványos nyilvános IP-cím a következő parancsot az előző lépésben létrehozott:

```azurecli-interactive
az network lb create --resource-group myResourceGroupSLB --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Hozzon létre egy LB mintavételi 80-as porton

A következő paranccsal terheléselosztói állapotfigyelő mintavétel létrehozása:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupSLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>A 80-as port LB szabály létrehozása

Hozzon létre olyan terheléselosztó szabályhoz a következő parancsot:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>További lépések
- További információ [szabványos terheléselosztó és a rendelkezésre állási zónák](load-balancer-standard-availability-zones.md).



