---
title: Csomópontvégpontok konfigurálása az Azure Batch-készletben | Microsoft dokumentumok
description: Az SSH- vagy RDP-portokhoz való hozzáférés konfigurálása vagy letiltása az Azure Batch-készlet számítási csomópontjain.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: labrenne
ms.openlocfilehash: 098ccf999391412520989c4ec2433fd73bc0a72d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77017224"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Távoli hozzáférés konfigurálása vagy letiltása az Azure Batch-készlet ben lévő számítási csomópontokhoz

Alapértelmezés szerint a Batch lehetővé teszi, hogy egy hálózati kapcsolattal rendelkező [csomópontfelhasználó](/rest/api/batchservice/computenode/adduser) külsőleg csatlakozzon egy batch készlet ben lévő számítási csomóponthoz. A felhasználó például a 3389-es porton a Távoli asztal (RDP) segítségével csatlakozhat egy Windows-készlet számítási csomópontjához. Hasonlóképpen alapértelmezés szerint a felhasználó a 22-es porton a Secure Shell (SSH) segítségével csatlakozhat egy Linux-készlet számítási csomópontjához. 

Előfordulhat, hogy a környezetben korlátoznia vagy le tiltania kell ezeket az alapértelmezett külső hozzáférési beállításokat. Ezeket a beállításokat a Batch API-k segítségével módosíthatja a [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) tulajdonság beállításához. 

## <a name="about-the-pool-endpoint-configuration"></a>A készlet végpontkonfigurációja
A végpont konfigurációja az előtér-portok egy vagy több [hálózati címfordítási (NAT) készletéből](/rest/api/batchservice/pool/add#inboundnatpool) áll. (Ne keverje össze a NAT-készletet a számítási csomópontok batch készletével.) Minden NAT-készletet úgy állított be, hogy felülbírálja a készlet számítási csomópontjainak alapértelmezett kapcsolati beállításait. 

Minden NAT-készletkonfiguráció egy vagy több [hálózati biztonsági csoportra (NSG) vonatkozó szabályt](/rest/api/batchservice/pool/add#networksecuritygrouprule)tartalmaz. Minden NSG-szabály engedélyezi vagy letiltja a végpontra irányuló bizonyos hálózati forgalmat. Választhat, hogy engedélyezi vagy letagadja az összes forgalmat, a [szolgáltatáscímkével](../virtual-network/security-overview.md#service-tags) (például "Internet" által azonosított forgalmat) vagy adott IP-címekről vagy alhálózatokról érkező forgalmat.

### <a name="considerations"></a>Megfontolandó szempontok
* A készletvégpont-konfiguráció a készlet [hálózati konfigurációjának](/rest/api/batchservice/pool/add#networkconfiguration)része. A hálózati konfiguráció tetszés szerint tartalmazhat beállításokat, hogy csatlakozzon a készlet egy [Azure virtuális hálózathoz.](batch-virtual-network.md) Ha a készletet virtuális hálózatban állítja be, létrehozhat NSG-szabályokat, amelyek a virtuális hálózat címbeállításait használják.
* NAT-készlet konfigurálásakor több NSG-szabály is konfigurálható. A szabályok ellenőrzése prioritási sorrendben történik. Amint talál egy érvényes szabályt, nem vizsgálja, hogy a többi szabálynak megfelel-e a forgalom.


## <a name="example-deny-all-rdp-traffic"></a>Példa: Az összes RDP-forgalom megtagadása

A következő C# kódrészlet bemutatja, hogyan konfigurálhatja az RDP-végpontot a Windows-készlet számítási csomópontjain az összes hálózati forgalom megtagadásához. A végpont a *60000 és 60099*tartományban lévő portok előtérkészletét használja. 

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

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Példa: Az összes SSH-forgalom megtagadása az internetről

A következő Python-kódrészlet bemutatja, hogyan konfigurálhatja az SSH-végpontot egy Linux-készlet számítási csomópontjain az összes internetes forgalom megtagadásához. A végpont a *4000 és 4100*közötti portok előtérkészletét használja. 

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
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

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Példa: RDP-forgalom engedélyezése egy adott IP-címről

A következő C# kódrészlet bemutatja, hogyan konfigurálható az RDP-végpont a Windows-készlet számítási csomópontjain, hogy az RDP-hozzáférés csak *a 198.51.100.7*IP-címről történjen. A második NSG-szabály megtagadja az IP-címet nem egyező forgalmat.

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

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Példa: SSH-forgalom engedélyezése egy adott alhálózatról

A következő Python-kódrészlet bemutatja, hogyan konfigurálhatja az SSH-végpontot egy Linux-készlet számítási csomópontjain úgy, hogy csak a *192.168.1.0/24*alhálózatról engedélyezze a hozzáférést. A második NSG-szabály megtagadja az alhálózatnak nem megfelelő forgalmat.

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
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

- Az Azure-beli NSG-szabályokról a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/security-overview.md)című témakörben talál további információt.

- A Batch részletes áttekintését a [Nagyméretű párhuzamos számítási megoldások fejlesztése a Batch segítségével](batch-api-basics.md)című témakörben találja.

