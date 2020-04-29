---
title: Virtuálisgép-méretezési csoportok üzembe helyezése IPv6-ban az Azure-ban
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja, hogyan helyezhet üzembe virtuálisgép-méretezési csoportokat IPv6-ban egy Azure-beli virtuális hálózaton.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80420462"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>Virtuálisgép-méretezési csoportok üzembe helyezése IPv6-ban az Azure-ban

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy kettős verem (IPv4 + IPv6) virtuálisgép-méretezési csoportját egy Azure-beli virtuális hálózatban lévő, kettős veremből álló külső terheléselosztó használatával. Az IPv6-kompatibilis virtuálisgép-méretezési csoport létrehozásának folyamata majdnem azonos az [itt](ipv6-configure-standard-load-balancer-template-json.md)ismertetett egyéni virtuális gépek létrehozási folyamatával. Az egyes virtuális gépekhez hasonló lépésekkel kezdheti meg a lépéseket:
1.    IPv4-és IPv6-alapú nyilvános IP-címek létrehozása.
2.    Hozzon létre egy Dual stack Load balancert.  
3.    Hozzon létre hálózati biztonsági csoport (NSG) szabályait.  

Az egyetlen virtuális gépről eltérő lépés a hálózati adapter (NIC) olyan konfigurációját hozza létre, amely a virtuálisgép-méretezési csoport erőforrását használja: networkProfile/Networkinterfaceconfigurations szakaszához. A JSON-struktúra hasonló az egyes virtuális gépekhez használt Microsoft. Network/networkInterfaces objektumhoz, a hálózati adapter és az IPv4-IpConfiguration elsődleges illesztőfelületként való beállításával, az **"elsődleges": true** attribútummal, az alábbi példában látható módon:

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


## <a name="sample-virtual-machine-scale-set-template-json"></a>Minta virtuálisgép-méretezési csoport sablonja JSON

A kettős verem (IPv4 + IPv6) virtuálisgép-méretezési csoportjának üzembe helyezéséhez hozzon létre egy kettős verem külső Load Balancer és a Virtual Network View minta sablont [itt](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/).
## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Virtual Networks IPv6-támogatásáról, tekintse meg a [Mi az az Azure-beli ipv6 Virtual Network?](ipv6-overview.md)című témakört.
