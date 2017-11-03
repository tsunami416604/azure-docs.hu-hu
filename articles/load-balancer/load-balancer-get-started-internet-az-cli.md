---
title: "Hozzon létre egy Load Balancer szabványos nyilvános zónaredundáns nyilvános IP-cím cím előtér Azure parancssori felületével |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy Load Balancer szabványos nyilvános zónaredundáns nyilvános IP-cím cím előtér Azure parancssori felület használatával"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: 769eb86af3e0506ddf03d1ec616d5a17b7e5f714
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>Hozzon létre egy Load Balancer szabványos nyilvános zónaredundáns előtér Azure parancssori felület használatával

Ez a cikk lépésről-lépésre nyilvános létrehozása [Load Balancer szabványos](https://aka.ms/azureloadbalancerstandard) a zónaredundáns időtúllépést IP szabványos nyilvános cím segítségével.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Standard Preview rendelkezésre állási zónák, a Load Balancer Standard és a nyilvános IP-cím regisztrálása

Rendszererőforrásokra telepíti, és a parancssori felület helyileg, ez az oktatóanyag megköveteli, hogy futnak-e az Azure parancssori felület 2.0.17 verzió vagy újabb verzióját.  A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

>[!NOTE]
[Terheléselosztó Standard Termékváltozat betöltése](https://aka.ms/azureloadbalancerstandard) jelenleg előzetes verzió. Az előzetes kiadás során a szolgáltatás rendelkezésre állása és megbízhatósága eltérő lehet az általánosan elérhető kiadásétól. További részletekért lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Használja a nyilvánosan elérhető [Load Balancer alapszintű Termékváltozat](load-balancer-overview.md) a termelés számára. 

> [!NOTE]
> Rendelkezésre állási zónák még csak előzetes verziójúak, és készen áll a fejlesztési és forgatókönyvek tesztelése. A select Azure-erőforrások és a régiók és a virtuális gép mérete családok támogatás érhető el. További információ az első lépések, és mely Azure-erőforrások, régiók és virtuális gép mérete családok megpróbálhatja a rendelkezésre állási zónákat, lásd: [rendelkezésre állási zónák áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Mielőtt kiválasztja, a zóna vagy zónaredundáns. lehetőséget az az előtérbeli nyilvános IP-címet a terheléselosztóhoz, akkor először el kell végeznie a lépéseket [regisztrálja a rendelkezésre állási zónák Preview](https://docs.microsoft.com/azure/availability-zones/az-overview).

Győződjön meg arról, hogy telepítette-e a legújabb [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)és az Azure-fiókkal bejelentkezett [az bejelentkezési](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest#login).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot, a következő parancsot:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Hozzon létre egy nyilvános IP Standard

Hozzon létre egy nyilvános IP szabványos a következő parancsot:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Terheléselosztó létrehozása

Hozzon létre egy Load Balancer szabványos nyilvános a szabványos nyilvános IP-cím a következő parancsot az előző lépésben létrehozott:

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Hozzon létre egy LB mintavételi 80-as porton

A következő paranccsal terheléselosztói állapotfigyelő mintavétel létrehozása:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>A 80-as port LB szabály létrehozása

Hozzon létre olyan terheléselosztó szabályhoz a következő parancsot:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Következő lépések
- Megtudhatja, hogyan [hozzon létre egy nyilvános IP-cím egy rendelkezésre állási zónában](../virtual-network/create-public-ip-availability-zone-cli.md)



