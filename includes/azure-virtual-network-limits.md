---
title: fájl belefoglalása
description: fájl belefoglalása
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 12/09/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 2596351a0db921276ad74c27e30a8e2af93f2f36
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74981132"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Hálózati korlátok – Azure Resource Manager a következő korlátozások érvényesek csak a **Azure Resource Manager** /régiónként felügyelt hálózati erőforrásokra. Megtudhatja, hogyan [tekintheti meg az aktuális erőforrás-használatot az előfizetési korlátok](../articles/networking/check-usage-against-limits.md)alapján.

> [!NOTE]
> A közelmúltban minden alapértelmezett korlátot megnövelt a maximális határértékek között. Ha nincs maximális korlát oszlop, az erőforrás nem rendelkezik állítható korlátokkal. Ha a múltban már a támogatásban is megnőtt a korlát, és a következő táblázatokban nem láthatók a frissített korlátok, [Nyisson meg egy online ügyfélszolgálati kérést díjmentesen](../articles/azure-resource-manager/resource-manager-quota-errors.md) .

| Erőforrás | Alapértelmezett/maximális korlát | 
| --- | --- |
| Virtuális hálózatok |1,000 |
| Alhálózatok száma virtuális hálózatonként |3,000 |
| Virtuális hálózati alhálózatok száma virtuális hálózatonként |500 |
| [Virtuális hálózati átjárók (VPN-átjárók) virtuális hálózatonként](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |30 |
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

| Erőforrás | Alapértelmezett/maximális korlát |
| --- | --- |
| Terheléselosztók | 1,000 | 
| Erőforrásokra vonatkozó szabályok, alapszintű | 250 |
| Szabályok/erőforrás, standard | 1500 | 
| Szabályok/IP-konfiguráció | 299 |
| Szabályok/NIC | 300 |
| Előtér-IP-konfigurációk, alapszintű | 200 |
| Előtér-végponti IP-konfigurációk, standard | 600 |
| Háttérbeli készlet, alapszintű | 100, egyetlen rendelkezésre állási csoport |
| Háttérbeli készlet, standard | 1 000, egyetlen virtuális hálózat |
| Háttérbeli erőforrások, terheléselosztó, standard<sup>1</sup> | 150 |
| Magas rendelkezésre állású portok, standard | 1 belső előtér |

<sup>1</sup> A korlát akár 150 erőforrás, az önálló virtuálisgép-erőforrások, a rendelkezésre állási csoport erőforrásai és a virtuálisgép-méretezési készlet erőforrásainak bármilyen kombinációja.

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
