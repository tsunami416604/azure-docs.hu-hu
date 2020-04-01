---
title: Virtuálisgép-méretezési készletek üzembe helyezése az IPv6-tal az Azure-ban
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja, hogyan telepítheti a virtuális gép méretezési készleteit az IPv6-tal egy Azure virtuális hálózatban.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 6a751fa193c8dd530707f790af0292d536a6f47d
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420462"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>Virtuálisgép-méretezési készletek üzembe helyezése az IPv6-tal az Azure-ban

Ez a cikk bemutatja, hogyan telepíthet egy kétverű (IPv4 + IPv6) virtuális gép méretezési készlet két halom külső terheléselosztó egy Azure virtuális hálózatban. Az IPv6-kompatibilis virtuálisgép-méretezési csoport létrehozásának folyamata közel azonos az [itt](ipv6-configure-standard-load-balancer-template-json.md)leírt egyedi virtuális gépek létrehozásának folyamatához. Az egyes virtuális gépekhez leírtakhoz hasonló lépésekkel kell kezdenie:
1.    IPv4 és IPv6 nyilvános IP-címek létrehozása.
2.    Hozzon létre egy kettős stack terheléselosztó.  
3.    Hozzon létre hálózati biztonsági csoport (NSG) szabályokat.  

Az egyetlen lépés, amely eltér az egyes virtuális gépek létrehozása a hálózati adapter konfiguráció, amely a virtuális gép méretezési csoport erőforrás: networkProfile/networkInterfaceConfigurations. A JSON-struktúra hasonló az egyes virtuális gépekekhez használt Microsoft.Network/networkInterfaces objektuméhoz, azzal a kiegészítéssel, hogy a hálózati adaptert és az IPv4 IpConfiguration-t elsődleges illesztőként állítja be az **"elsődleges" használatával: true** attribútum, ahogy az a következő példában látható:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>Minta virtuálisgép-méretezési csoport sablonjának JSON-ja

Két verem (IPv4 + IPv6) virtuálisgép-méretezési készlet üzembe helyezése két veremkülső terheléselosztóval és virtuális hálózati nézet mintasablonnal [itt.](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)
## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni az IPv6-támogatásról az Azure virtuális hálózatokban, olvassa el [a Mi az IPv6 az Azure virtuális hálózathoz című témakörben.](ipv6-overview.md)
