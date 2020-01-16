---
title: fájl belefoglalása
description: fájl belefoglalása
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 17558b44c91425ce1a06625f8fd5c1806a762ba2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021114"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Hálózati korlátok – Azure Resource Manager a következő korlátozások érvényesek csak a **Azure Resource Manager** /régiónként felügyelt hálózati erőforrásokra. Megtudhatja, hogyan [tekintheti meg az aktuális erőforrás-használatot az előfizetési korlátok](../articles/networking/check-usage-against-limits.md)alapján.

> [!NOTE]
> A közelmúltban minden alapértelmezett korlátot megnövelt a maximális határértékek között. Ha nincs maximális korlát oszlop, az erőforrás nem rendelkezik állítható korlátokkal. Ha a múltban már a támogatásban is megnőtt a korlát, és a következő táblázatokban nem láthatók a frissített korlátok, [Nyisson meg egy online ügyfélszolgálati kérést díjmentesen](../articles/azure-resource-manager/templates/error-resource-quota.md) .

| Erőforrás | Alapértelmezett/maximális korlát | 
| --- | --- |
| Virtuális hálózatok |1,000 |
| Alhálózatok száma virtuális hálózatonként |3,000 |
| Virtuális hálózati alhálózatok száma virtuális hálózatonként |500 |
| [Virtuális hálózati átjárók (VPN-átjárók) virtuális hálózatonként](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Virtuális hálózati átjárók (ExpressRoute-átjárók) virtuális hálózatonként](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| DNS-kiszolgálók száma virtuális hálózatonként |20 |
| Magánhálózati IP-címek száma virtuális hálózatonként |65 536 |
| Magánhálózati IP-címek/hálózati adapterek |256 |
| Magánhálózati IP-címek száma virtuális gépenként |256 |
| Nyilvános IP-címek/hálózati adapter |256 |
| Nyilvános IP-címek virtuális gépenként |256 |
| [Virtuális gép vagy szerepkör-példány hálózati adapterén egyidejű TCP-vagy UDP-forgalom](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500,000 |
| Hálózati kártyák |65 536 |
| Hálózati biztonsági csoportok |5000 |
| NSG-ben szereplő NSG-szabályok |1,000 |
| A forráshoz vagy célhoz megadott IP-címek és tartományok egy biztonsági csoportban |4,000 |
| Alkalmazásbiztonsági csoportok |3,000 |
| Alkalmazás biztonsági csoportjai IP-konfiguráció alapján, NIC-ben |20 |
| IP-konfigurációk alkalmazás biztonsági csoportjában |4,000 |
| A hálózati biztonsági csoport összes biztonsági szabályában megadható alkalmazás-biztonsági csoportok |100 |
| Felhasználó által definiált útválasztási táblák |200 |
| Felhasználó által definiált útvonalak útválasztási táblázatban |400 |
| Pont – hely típusú legfelső szintű tanúsítványok Azure-VPN Gateway |20 |
| Virtuális hálózati csapok |100 |
| Hálózati adapter KOPPINTson a konfigurációk virtuális hálózatra KOPPINTva |100 |

#### <a name="publicip-address"></a>Nyilvános IP-címek korlátai
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Nyilvános IP-címek – dinamikus | 1 000 alapszintű. |Vegye fel a kapcsolatot az ügyfélszolgálattal. |
| Nyilvános IP-címek – statikus | 1 000 alapszintű. |Vegye fel a kapcsolatot az ügyfélszolgálattal. |
| Nyilvános IP-címek – statikus | 1 000 standard.|Vegye fel a kapcsolatot az ügyfélszolgálattal. |
| Nyilvános IP-előtag hossza | /28 | Vegye fel a kapcsolatot az ügyfélszolgálattal. |

#### <a name="load-balancer"></a>Terheléselosztó korlátai
Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése régiónként és előfizetésenként, az Azure Resource Managerrel történik. Megtudhatja, hogyan [tekintheti meg az aktuális erőforrás-használatot az előfizetési korlátok](../articles/networking/check-usage-against-limits.md)alapján.

**standard Load Balancer**

| Erőforrás                                | Alapértelmezett/maximális korlát         |
|-----------------------------------------|-------------------------------|
| Terheléselosztók                          | 1,000                         |
| Szabályok/erőforrás                      | 1500                         |
| Hálózati adapterek szabályai (a hálózati adapter összes IP-címe között) | 300                           |
| Előtér-IP-konfigurációk              | 600                           |
| Háttérbeli készlet mérete                       | 1 000 IP-konfiguráció, egyetlen virtuális hálózat |
| Magas rendelkezésre állású portok                 | 1/belső előtér       |
| Kimenő szabályok száma Load Balancer         | 20                            |


**Alapszintű Load Balancer**

| Erőforrás                                | Alapértelmezett/maximális korlát        |
|-----------------------------------------|------------------------------|
| Terheléselosztók                          | 1,000                        |
| Szabályok/erőforrás                      | 250                          |
| Hálózati adapterek szabályai (a hálózati adapter összes IP-címe között) | 300                          |
| Előtér-IP-konfigurációk              | 200                          |
| Háttérbeli készlet mérete                       | 300 IP-konfiguráció, egyetlen rendelkezésre állási csoport |
| Rendelkezésre állási készletek száma Load Balancer     | 150                          |

#### <a name="virtual-networking-limits-classic"></a>A következő korlátozások csak a **klasszikus** üzemi modellen keresztül felügyelt hálózati erőforrások esetében érvényesek. Megtudhatja, hogyan [tekintheti meg az aktuális erőforrás-használatot az előfizetési korlátok](../articles/networking/check-usage-against-limits.md)alapján.

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális hálózatok |100 |100 |
| Helyi hálózati helyek |20 |50 |
| DNS-kiszolgálók száma virtuális hálózatonként |20 |20 |
| Magánhálózati IP-címek száma virtuális hálózatonként |4 096 |4 096 |
| Virtuális gép vagy szerepkör-példány hálózati adapterén egyidejű TCP-vagy UDP-forgalom |500 000, legfeljebb 1 000 000 kettő vagy több hálózati adapter számára. |500 000, legfeljebb 1 000 000 kettő vagy több hálózati adapter számára. |
| Hálózati biztonsági csoportok (NSG) |200 |200 |
| NSG-ben szereplő NSG-szabályok |1,000 |1,000 |
| Felhasználó által definiált útválasztási táblák |200 |200 |
| Felhasználó által definiált útvonalak útválasztási táblázatban |400 |400 |
| Nyilvános IP-címek (dinamikus) |500 |500 |
| Fenntartott nyilvános IP-címek |500 |500 |
| Nyilvános virtuális IP-címek száma üzemelő példányonként |5 |Kapcsolatfelvétel az ügyfélszolgálattal |
| Privát VIP (belső terheléselosztás) üzembe helyezése |1 |1 |
| Végpontok hozzáférés-vezérlési listái (ACL-ek) |50 |50 |
