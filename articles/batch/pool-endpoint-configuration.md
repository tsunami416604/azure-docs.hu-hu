---
title: Csomópont-végpontok konfigurálása az Azure Batch-készletben |} A Microsoft Docs
description: Hogyan konfigurálhatja vagy letilthatja a hozzáférést az SSH vagy RDP-portokhoz egy Azure Batch-készletben található számítási csomópontokon.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: lahugh
ms.openlocfilehash: a6c2c343b13b77048c772cb1e5c2ba06cf8add50
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457615"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Konfigurálhatja vagy letilthatja a távoli hozzáférést az Azure Batch-készlet számítási csomópontjain

Alapértelmezés szerint a Batch lehetővé teszi egy [csomópont felhasználói](/rest/api/batchservice/computenode/adduser) kívülről kapcsolódni egy számítási csomóponton a Batch-készlet hálózati kapcsolattal rendelkező. Például a felhasználók kapcsolódhatnak a távoli asztali (RDP) egy Windows-készletben lévő számítási csomópontok 3389-es porton. Alapértelmezés szerint ehhez hasonlóan a felhasználók kapcsolódhatnak a Secure Shell (SSH) 22-es port egy Linux-készlet számítási csomópontjaihoz. 

A környezetben szüksége lehet korlátozni, vagy tiltsa le a külső hozzáférés alapértelmezett beállítások. Állítsa be a Batch API-k használatával módosíthatja ezeket a beállításokat a [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) tulajdonság. 

## <a name="about-the-pool-endpoint-configuration"></a>Tudnivalók a készletvégpont-konfiguráció
A végpont-konfiguráció áll egy vagy több [hálózati címfordítás (NAT) címkészletek](/rest/api/batchservice/pool/add#inboundnatpool) előtérbeli portok. (Ne keverje össze a számítási csomópontok Batch-készlet egy NAT-készletet.) Felülírják az alapértelmezett kapcsolati beállításokat a készlet számítási csomópontokon való beállítása minden NAT-készletet. 

Minden egyes NAT a készletkonfiguráció tartalmaz egy vagy több [hálózati biztonsági csoport (NSG) szabályai](/rest/api/batchservice/pool/add#networksecuritygrouprule). Minden NSG-szabály engedélyezi, vagy megtagadja a bizonyos hálózati forgalmat a végponthoz. Dönthet úgy, hogy engedélyezi vagy megtagadja a minden forgalmat a forgalom által azonosított egy [szolgáltatáscímke](../virtual-network/security-overview.md#service-tags) (például az "Internet"), vagy adott IP-címekről vagy alhálózatokról érkező forgalmat.

### <a name="considerations"></a>Megfontolandó szempontok
* A készlet végpont-konfiguráció a készlet része [hálózati konfiguráció](/rest/api/batchservice/pool/add#NetworkConfiguration). A hálózati konfiguráció szükség esetén belefoglalhatja csatlakozni szeretne a készlet beállításait egy [az Azure virtual network](batch-virtual-network.md). Ha beállította a készlet az egy virtuális hálózatot, virtuális hálózatban lévő címekre vonatkozó beállításokat használó NSG-szabályok is létrehozhat.
* Beállíthatja több NSG-szabályok, NAT-készletet konfigurálásakor. A szabályokat a rendszer prioritás szerinti sorrendben ellenőrzi. Amint talál egy érvényes szabályt, nem vizsgálja, hogy a többi szabálynak megfelel-e a forgalom.


## <a name="example-deny-all-rdp-traffic"></a>Példa: RDP-forgalmat az összes elutasítása

A következő C# kódrészlet bemutatja, hogyan konfigurálhatja az RDP-végpontot összes hálózati forgalom megtagadásához Windows készlet számítási csomópontjain. A végpontot használja a tartomány portok előtérbeli címkészlet *60000-60099*. 

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

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Példa: Megtagadja az összes SSH-forgalom az internetről

A következő Python-kódrészlet bemutatja, hogyan konfigurálja az SSH-végpont minden internetes forgalom megtagadásához szükséges Linux-készlet számítási csomópontjain. A végpontot használja a tartomány portok előtérbeli címkészlet *4000-4100*. 

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

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Példa: Engedélyezze az RDP-forgalmat egy adott IP-címről

A következő C# kódrészlet bemutatja, hogyan konfigurálhatja az RDP-végpontot csak az IP-címről érkező RDP-hozzáférést biztosít egy Windows-készlet számítási csomópontjain *198.51.100.7*. A második Hálózatibiztonságicsoport-szabály megtagadja a forgalmat, amely nem egyezik meg az IP-címet.

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

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Példa: Egy adott alhálózatról SSH-forgalom engedélyezése

A következő Python-kódrészlet bemutatja, hogyan konfigurálja az SSH-végpont engedélyezi a hozzáférést az alhálózatról csak a Linux-készlet számítási csomópontjain *192.168.1.0/24*. A második Hálózatibiztonságicsoport-szabály megtagadja a forgalmat, amely nem egyezik meg az alhálózatot.

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

- Az Azure-ban az NSG-szabályok kapcsolatos további információkért lásd: [hálózati biztonsági csoportokkal a hálózati forgalom szűrése](../virtual-network/security-overview.md).

- A Batch részletesebb áttekintéséért lásd: [Develop nagy léptékű párhuzamos számítási megoldások Batch segítségével történő](batch-api-basics.md).

