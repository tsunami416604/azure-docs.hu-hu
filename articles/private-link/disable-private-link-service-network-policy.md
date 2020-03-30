---
title: 'Az Azure Private Link szolgáltatás forrásának IP-címéhez való hálózati házirendek letiltása '
description: Megtudhatja, hogy miként tilthatja le az Azure private Link hálózati házirendjeit
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 4c6bd64d141341e0b7fa5641e04320a95d7951bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452994"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>A Private Link szolgáltatás forrásának IP-címéhez való hálózati házirendek letiltása

A Privát kapcsolat szolgáltatás forrás IP-címének kiválasztásához `privateLinkServiceNetworkPolicies` explicit letiltási beállítás szükséges az alhálózaton. Ez a beállítás csak a privát kapcsolat szolgáltatás forrás IP-címeként kiválasztott privát IP-címre vonatkozik. Az alhálózat egyéb erőforrásai esetében a hozzáférés a hálózati biztonsági csoportok (NSG) biztonsági szabályok definíciója alapján történik. 
 
Bármely Azure-ügyfél (PowerShell, CLI vagy sablonok) használata esetén egy további lépés szükséges a tulajdonság módosításához. Letilthatja a szabályzatot a felhőbeli rendszerhéj használatával az Azure Portalon, vagy az Azure PowerShell, az Azure CLI helyi telepítései, vagy használhatja az Azure Resource Manager-sablonokat.  
 
Az alábbi lépéseket követve tiltsa le a *myVirtualNetwork* nevű virtuális hálózat magánhálózati hálózatának privát kapcsolati hálózati házirendjeit, amelynek *alapértelmezett* alhálózata a *myResourceGroup*nevű erőforráscsoportban található. 

## <a name="using-azure-powershell"></a>Az Azure PowerShell használata
Ez a szakasz ismerteti, hogyan tiltsa le az alhálózati privát végpont szabályzatok az Azure PowerShell használatával.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Ez a szakasz ismerteti, hogyan tiltsa le az alhálózati privát végpont szabályzatok az Azure CLI használatával.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Sablon használata
Ez a szakasz bemutatja, hogyan tilthatja le az alhálózati privát végpontszabályzatokat az Azure Resource Manager-sablon használatával.
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "WestUS", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>További lépések
- További információ az [Azure Private Endpointról](private-endpoint-overview.md)
 
