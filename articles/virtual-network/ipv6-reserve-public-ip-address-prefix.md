---
title: Nyilvános IPv6-címek és címtartományok lefoglalása Azure-beli virtuális hálózatban
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan foglalhat le nyilvános IPv6-címeket és címtartományokat egy Azure virtuális hálózatban.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9a0dd56842174d89688c862397c373326ef50d1f
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420543"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Nyilvános IPv6-címelőtag lefoglalása
Az IPv6 for Azure Virtual Network (VNet) lehetővé teszi, hogy az Azure-ban iPv6- és IPv4-kapcsolattal rendelkező alkalmazásokat üzemeltetjen virtuális hálózaton belül, valamint az internetről és az internetről. Az egyes IPv6-címek lefoglalása mellett az Azure IPv6-címek (más néven IP-előtag) összefüggő tartományait is lefoglalhatja a használatra. Ez a cikk ismerteti, hogyan hozhat létre IPv6 nyilvános IP-címeket és címtartományokat az Azure PowerShell és a CLI használatával.


## <a name="create-a-single-reserved-ipv6-public-ip"></a>Egyetlen fenntartott IPv6-nyilvános IP-cím létrehozása

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Egyetlen fenntartott (statikus) IPv6 nyilvános IP-címet hozhat létre az Azure PowerShell és [a New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használatával az alábbiak szerint:

```azurepowershell  
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

 Egyetlen fenntartott (statikus) IPv6 nyilvános IP-címet hozhat létre az Azure CLI az [hálózati nyilvános ip-létrehozással](/cli/azure/network/public-ip) az alábbiak szerint:
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Fenntartott IPv6-előtag (tartomány) létrehozása

IPv6-előtag lefoglalásához adja hozzá az IPv6 IP-címcsaládját ugyanahhoz a parancshoz, amelyet az IPv4-előtagok létrehozásához használt. A következő parancsok /125 (8 IPv6-cím) méretű előtagot hoznak létre.  

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Nyilvános IPv6-címet hozhat létre az Azure CLI használatával [az az hálózati nyilvános ip-létrehozással](/powershell/module/az.network/new-azpublicipprefix) az alábbiak szerint:
```azurepowershell  
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Nyilvános IPv6-címet az Azure CLI használatával az alábbiak szerint hozhat létre:

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Nyilvános IP-cím lefoglalása fenntartott IPv6-előtagból

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

 Hozzon létre egy statikus IPv6 nyilvános IP-cím egy fenntartott előtag hozzáadásával az `-PublicIpPrefix` argumentumot, amikor létrehozza a nyilvános IP-t az Azure PowerShell használatával. A következő példa feltételezi, hogy egy előtagot hoztak létre és tároltak egy PowerShell-változóban, amelynek *neve: $myOwnIPv6Előtag*.

```azurepowershell:  
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
 
A következő példa feltételezi, hogy egy előtagot hoztak létre és tároltak egy *IPv6PrefixWestUS*nevű CLI változóban.

```azurecli 
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>További lépések
- További információ az [IPv6-címelőtagról.](ipv6-public-ip-address-prefix.md)
- További információ az [IPv6-címekről.](ipv6-overview.md)
