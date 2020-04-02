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
ms.openlocfilehash: 95cb29e871cce2ba600ab654d48c685b90ed027e
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80573238"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Hálózati korlátok – Azure Resource Manager
A következő korlátok csak az **Azure Resource Manager** en keresztül régiónként és előfizetésenként kezelt hálózati erőforrásokra vonatkoznak. Ismerje meg, hogyan tekintheti meg [az aktuális erőforrás-használatot az előfizetési korlátok között.](../articles/networking/check-usage-against-limits.md)

> [!NOTE]
> A közelmúltban az összes alapértelmezett korlátot a maximális limitekre emeltük. Ha nincs maximális korlát oszlop, az erőforrás nem rendelkezik állítható korlátokkal. Ha ezeket a korlátokat korábban a támogatás növelte, és az alábbi táblázatokban nem láthatók frissített korlátozások, [nyisson meg egy online ügyfélszolgálati kérelmet díjmentesen.](../articles/azure-resource-manager/templates/error-resource-quota.md)

| Erőforrás | Korlát | 
| --- | --- |
| Virtuális hálózatok |1,000 |
| Alhálózatok száma virtuális hálózatonként |3,000 |
| Virtuális hálózatonkénti virtuális társhálózat létesítések |500 |
| [Virtuális hálózati átjárók (VPN-átjárók) virtuális hálózatonként](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Virtuális hálózati átjárók (ExpressRoute-átjárók) virtuális hálózatonként](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| DNS-kiszolgálók virtuális hálózatonként |20 |
| Virtuális hálózatonkénti privát IP-címek |65 536 |
| Privát IP-címek hálózati adapterenként |256 |
| Privát IP-címek virtuális gépenként |256 |
| Nyilvános IP-címek hálózati adapterenként |256 |
| Nyilvános IP-címek virtuális gépenként |256 |
| [Egyidejű TCP- vagy UDP-folyamatok egy virtuális gép vagy szerepkörpéldány hálózati adaptere szerint](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500 000 |
| Hálózati csatolókártyák |65 536 |
| Network Security Groups (Hálózati biztonsági csoportok) |5000 |
| NSG-ben szereplő NSG-szabályok |1,000 |
| A biztonsági csoportban a forráshoz vagy célhoz megadott IP-címek és -tartományok |4,000 |
| Alkalmazásbiztonsági csoportok |3,000 |
| Alkalmazásbiztonsági csoportok IP-konfigurációnként, hálózati adapterenként |20 |
| IP-konfigurációk alkalmazásbiztonsági csoportonként |4,000 |
| A hálózati biztonsági csoport összes biztonsági szabályában megadható alkalmazásbiztonsági csoportok |100 |
| Felhasználó által definiált útvonaltáblák |200 |
| Felhasználó által definiált útvonalak útvonaltáblánként |400 |
| Pont-hely főtanúsítványok Azure VPN-átjárónként |20 |
| Virtuális hálózati TAPs |100 |
| Hálózati interfész TAP konfigurációk virtuális hálózatonként TAP |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Nyilvános IP-címkorlátok
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Nyilvános IP-címek<sup>1</sup> | 10 az alap. | Vegye fel a kapcsolatot az ügyfélszolgálattal. |
| Statikus nyilvános IP-címek<sup>1</sup> | 10 az alap. | Vegye fel a kapcsolatot az ügyfélszolgálattal. |
| Szabványos nyilvános IP-címek<sup>1</sup> | 10 | Vegye fel a kapcsolatot az ügyfélszolgálattal. |
| Nyilvános IP-előtagok | előfizetésben a szabványos nyilvános IP-k száma korlátozza | Vegye fel a kapcsolatot az ügyfélszolgálattal. |
| Nyilvános IP-előtag hossza | /28 | Vegye fel a kapcsolatot az ügyfélszolgálattal. |

<sup>1 1</sup> A nyilvános IP-címek alapértelmezett korlátozásai ajánlatkategória-típusonként változnak, például ingyenes próbaverzió, használatra fizetési alapú fizetés, kripta. A nagyvállalati szerződéssel kapcsolatos előfizetések alapértelmezett beállítása például 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Terheléselosztó határértékek
Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése régiónként és előfizetésenként, az Azure Resource Managerrel történik. Ismerje meg, hogyan tekintheti meg [az aktuális erőforrás-használatot az előfizetési korlátok között.](../articles/networking/check-usage-against-limits.md)

**Standard terheléselosztó**

| Erőforrás                                | Korlát         |
|-----------------------------------------|-------------------------------|
| Terheléselosztók                          | 1,000                         |
| Szabályok erőforrásonként                      | 1500                         |
| Hálózati adapterenkénti szabályok (a hálózati adapter összes IP-szolgáltatóján) | 300                           |
| Előtér IP-konfigurációi              | 600                           |
| Háttérkészlet mérete                       | 1000 IP-konfiguráció, egyetlen virtuális hálózat |
| Háttér-erőforrások <sup>terheléselosztónként 1<sup> | 150                   |
| Magas rendelkezésre állású portok                 | 1 belső előtérenként       |
| Kimenő szabályok terheléselosztónként        | 20                            |
| [TCP tétlen időmeghosszabbítása](https://docs.microsoft.com/azure/load-balancer/load-balancer-tcp-idle-timeout#tcp-idle-timeout) | 4 perc/30 perc          |

<sup>1 1</sup> A korlát legfeljebb 150 erőforrás, az önálló virtuálisgép-erőforrások, a rendelkezésre állási készlet erőforrásaiés a virtuálisgép-méretezési erőforrások kombinációja.

**Alapterhelés-elosztó**

| Erőforrás                                | Korlát        |
|-----------------------------------------|------------------------------|
| Terheléselosztók                          | 1,000                        |
| Szabályok erőforrásonként                      | 250                          |
| Hálózati adapterenkénti szabályok (a hálózati adapter összes IP-szolgáltatóján) | 300                          |
| Előtér IP-konfigurációi              | 200                          |
| Háttérkészlet mérete                       | 300 IP-konfiguráció, egyetlen rendelkezésre állási készlet |
| Rendelkezésre állási készletek terheléselosztónként     | 150                          |

<a name="virtual-networking-limits-classic"></a>A következő korlátok csak a **klasszikus** üzembe helyezési modell előfizetésenként kezelt hálózati erőforrásokra vonatkoznak. Ismerje meg, hogyan tekintheti meg [az aktuális erőforrás-használatot az előfizetési korlátok között.](../articles/networking/check-usage-against-limits.md)

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális hálózatok |100 |100 |
| Helyi hálózati helyek |20 |50 |
| DNS-kiszolgálók virtuális hálózatonként |20 |20 |
| Virtuális hálózatonkénti privát IP-címek |4,096 |4,096 |
| Egyidejű TCP- vagy UDP-folyamatok egy virtuális gép vagy szerepkörpéldány hálózati adaptere szerint |500 000, legfeljebb 1 000 000 két vagy több hálózati adapter esetén. |500 000, legfeljebb 1 000 000 két vagy több hálózati adapter esetén. |
| Hálózati biztonsági csoportok (NSG-k) |200 |200 |
| NSG-ben szereplő NSG-szabályok |1,000 |1,000 |
| Felhasználó által definiált útvonaltáblák |200 |200 |
| Felhasználó által definiált útvonalak útvonaltáblánként |400 |400 |
| Nyilvános IP-címek (dinamikus) |500 |500 |
| Fenntartott nyilvános IP-címek |500 |500 |
| Nyilvános virtuális IP-címek száma üzemelő példányonként |5 |Kapcsolatfelvétel a támogatási szolgáltatással |
| Privát VIP (belső terheléselosztás) telepítésenként |1 |1 |
| Végponthozzáférés-vezérlési listák (Hozzáférés-vezérlési listák) |50 |50 |
