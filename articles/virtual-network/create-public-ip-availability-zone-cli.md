---
title: "Egy zoned nyilvános IP-cím létrehozása az Azure parancssori felülettel |} Microsoft Docs"
description: "Hozzon létre egy nyilvános IP-cím egy rendelkezésre állási zónát az Azure parancssori felület."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ef93d43bbd0c58950027810c8c335d9076574326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-cli"></a>Hozzon létre egy nyilvános IP-cím egy rendelkezésre állási zónát az Azure parancssori felület

A nyilvános IP-cím, egy Azure rendelkezésre állási zónában (előzetes verzió) telepítése. Egy rendelkezésre állási zóna a fizikailag különálló zónát Azure-régióban. Az alábbiak végrehajtásának módját ismerheti meg:

> * Hozzon létre egy nyilvános IP-cím egy rendelkezésre állási zónában
> * A rendelkezésre állási zónában létre kapcsolódó erőforrások azonosítása

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha telepítése és a parancssori felület helyileg használata mellett dönt, ez az oktatóanyag van szükség, hogy futtatja az Azure CLI-es verzió 2.0.17 nagyobb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Rendelkezésre állási zónák még csak előzetes verziójúak, és készen áll a fejlesztési és forgatókönyvek tesztelése. A select Azure-erőforrások és a régiók és a virtuális gép mérete családok támogatás érhető el. További információ az első lépések, és mely Azure-erőforrások, régiók és virtuális gép mérete családok megpróbálhatja a rendelkezésre állási zónákat, lásd: [rendelkezésre állási zónák áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md).

## <a name="create-a-zonal-public-ip-address"></a>Egy zonal nyilvános IP-cím létrehozása

Hozzon létre egy nyilvános IP-cím egy rendelkezésre állási zónában, a következő parancsot:


```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIp --zone 2 --location westeurope
```
> [!NOTE]
> Ha egy standard termékváltozatú nyilvános IP-címet hozzárendel egy virtuális gép hálózati adapteréhez, kifejezetten engedélyeznie kell a kívánt forgalmat egy [hálózati biztonsági csoporttal](security-overview.md#network-security-groups). Az erőforrással történő kommunikáció meghiúsul, amíg nem hoz létre és rendel hozzá egy hálózati biztonsági csoportot, és kifejezetten nem engedélyezi a kívánt forgalmat.

## <a name="get-zone-information-about-a-public-ip-address"></a>A nyilvános IP-cím zóna adatainak beolvasása

A zóna információ a nyilvános IP-cím a következő parancsot:

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIp
```

## <a name="next-steps"></a>Következő lépések

- További információ [rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview)
- További információ [nyilvános IP-címek](../virtual-network/virtual-network-public-ip-address.md) 
