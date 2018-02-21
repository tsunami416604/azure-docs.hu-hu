---
title: "Csomópont végpontok konfigurálása az Azure Batch-készlet |} Microsoft Docs"
description: "Hogyan konfigurálhatja vagy letilthatja a Azure Batch-készlet számítási csomópontjai SSH és az RDP-portját a hozzáférést."
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: danlep
ms.openlocfilehash: fdc68744406c3e995a2764f93d4474b807337ff5
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Konfigurálhatja vagy letilthatja a távoli hozzáférést a számítási csomópontok az Azure Batch-készlet

Alapértelmezés szerint kötegelt lehetővé teszi, hogy egy [csomópont felhasználói](/rest/api/batchservice/computenode/adduser) kívülről kapcsolódni a Batch-készlet számítási csomópont hálózati kapcsolattal rendelkező. Csatlakozzon például az a felhasználó által távoli asztal (RDP) a 3389-es porton keresztül egy Windows-készlet számítási csomópont. Alapértelmezés szerint hasonló módon, a felhasználók kapcsolódhatnak által Secure Shell (SSH) a 22-es porton keresztül egy számítási csomópont egy Linux-készletben. 

A környezetben szükség lehet korlátozhatja, vagy tiltsa le a alapértelmezett külső hozzáférési beállítások. Módosíthatja a kötegelt API-k használatával állítsa be ezeket a beállításokat a [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) tulajdonság. 

## <a name="about-the-pool-endpoint-configuration"></a>Tudnivalók a készlet végpont-konfiguráció
A végpont-konfiguráció áll egy vagy több [hálózati címfordítási (NAT) címkészletek](/rest/api/batchservice/pool/add#inboundnatpool) elülső rétegbeli portot. (Ne tévessze össze egy NAT-címkészlet, amely a Batch-készlet számítási csomóponton.) Minden NAT-készlet beállítása felülbírálhatja az alapértelmezett beállítások a készlet számítási csomóponton. 

Minden egyes NAT-készlet konfiguráció tartalmaz egy vagy több [hálózati biztonsági csoport (NSG) szabályok](/rest/api/batchservice/pool/add#networksecuritygrouprule). Minden NSG-szabály lehetővé teszi, vagy letiltja a végpontnak a hálózati forgalom meghatározott. Dönthet úgy, engedélyezése vagy megtagadása az összes forgalom, által azonosított forgalom egy [alapértelmezett címke](../virtual-network/virtual-networks-nsg.md#default-tags) (például az "Internet"), vagy adott IP-címek és alhálózatok érkező forgalmat.

### <a name="considerations"></a>Megfontolandó szempontok
* A készlet végpont-konfiguráció a készlet része [hálózati konfiguráció](/rest/api/batchservice/pool/add#NetworkConfiguration). A hálózati konfiguráció választhatóan is való csatlakozáshoz a készlet beállításait egy [Azure-beli virtuális hálózat](batch-virtual-network.md). A készlet része virtuális hálózatnak állít be, ha a virtuális hálózati cím beállításainak használó NSG-szabályok is létrehozhat.
* Több NSG-szabályok a NAT-készlete konfigurálásakor konfigurálhatja. A szabályok prioritás szerinti sorrendben ellenőrzi. Amint talál egy érvényes szabályt, nem vizsgálja, hogy a többi szabálynak megfelel-e a forgalom.


## <a name="example-deny-all-rdp-traffic"></a>Példa: Megtagadása az összes RDP-forgalmát

Az alábbi C# kódrészletben láthatja az RDP-végpontot konfigurálása a hálózati csomagok egy Windows-készlet számítási csomópontjai. A végpont használja a tartományba eső portok előtér készletét *60000-60099*. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Példa: Csomagok SSH az internetről

Az alábbi Python kódrészletben láthatja az SSH-végpont konfigurálása internet csomagok egy Linux-készlet számítási csomópontjai. A végpont használja a tartományba eső portok előtér készletét *4000-4100*. 

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                source_address_prefix='Internet'
                )
            ]
        )
        ]
    ) 
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Példa: Egy adott IP-címről érkező RDP-forgalmát engedélyezi

Az alábbi C# kódrészletben láthatja az RDP-végpontot konfigurálása Windows RDP-hozzáférést csak a következő IP-készlet számítási csomópontjai *198.51.100.7*. A második NSG-szabály megtagadja a forgalmat, amely nem egyezik meg az IP-címet.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Példa: Egy bizonyos alhálózat SSH-forgalom engedélyezése

Az alábbi Python kódrészletben láthatja az SSH-végpont konfigurálása engedélyezése csak az alhálózat Linux készlet számítási csomópontjai *192.168.1.0/24*. A második NSG-szabály megtagadja a forgalmat, amely nem egyezik meg az alhálózatot.

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access='allow',
                source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                priority=175,
                access='deny',
                source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>További lépések

- NSG-szabályok az Azure-ban kapcsolatos további információkért lásd: [hálózati forgalmat hálózati biztonsági csoportokkal](../virtual-network/virtual-networks-nsg.md).

- Kötegelt, áttekintéséért lásd: [Develop nagyméretű párhuzamos számítási solutions a kötegelt](batch-api-basics.md).

