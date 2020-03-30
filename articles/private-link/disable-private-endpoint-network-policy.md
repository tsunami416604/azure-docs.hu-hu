---
title: Hálózati házirendek letiltása az Azure-beli magánvégpontokhoz
description: További információ a magánhálózati házirendek letiltásáról.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: b5ab62e7ab57d32a11a45713519633034deb6a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453021"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Hálózati házirendek letiltása magánvégpontokhoz

A hálózati házirendek, például a hálózati biztonsági csoportok (NSG) nem támogatottak a magánvégpontok esetében. Egy privát végpont egy adott alhálózaton való központi telepítéséhez explicit letiltási beállítás szükséges az adott alhálózaton. Ez a beállítás csak a privát végpontra vonatkozik. Az alhálózat egyéb erőforrásai esetében a hozzáférés a hálózati biztonsági csoportok (NSG) biztonsági szabályok definíciója alapján történik. 
 
Ha a portál használatával hozzon létre egy privát végpontot, ez a beállítás automatikusan le van tiltva a létrehozási folyamat részeként. A más ügyfelekkel történő telepítés további lépést igényel a beállítás módosításához. Letilthatja a beállítást az Azure Portalon, vagy az Azure PowerShell, az Azure CLI helyi telepítései használatával, vagy használhatja az Azure Resource Manager-sablonokat.  
 
Az alábbi példák `PrivateEndpointNetworkPolicies` bemutatják, hogyan lehet letiltani egy *myVirtualNetwork* nevű virtuális hálózatot, amelynek *alapértelmezett* alhálózata *egy myResourceGroup*nevű erőforráscsoportban található.

## <a name="using-azure-powershell"></a>Az Azure PowerShell használata
Ez a szakasz ismerteti, hogyan tiltsa le az alhálózati privát végpont szabályzatok az Azure PowerShell használatával.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Ez a szakasz ismerteti, hogyan tiltsa le az alhálózati privát végpont szabályzatok az Azure CLI használatával.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
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
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>További lépések
- További információ az [Azure privát végpontjáról](private-endpoint-overview.md)
 
