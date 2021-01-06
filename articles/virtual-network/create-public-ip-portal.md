---
title: Nyilvános IP-Azure Portal létrehozása
description: Megtudhatja, hogyan hozhat létre nyilvános IP-címet a Azure Portalban
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: bd35d3288591a01c14e306960601eef6dea7e761
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954141"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Gyors útmutató: nyilvános IP-cím létrehozása a Azure Portal használatával

Ez a cikk bemutatja, hogyan hozhat létre egy nyilvános IP-cím-erőforrást a Azure Portal használatával. További információ arról, hogy mely erőforrásokhoz lehet társítva, az alapszintű és a standard SKU és az egyéb kapcsolódó információk között a [nyilvános IP-címek](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)című részben talál.  Ebben a példában csak IPv4-címekre fogunk összpontosítani; További információ az IPv6-címekről: [IPv6 for Azure VNet](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

# <a name="standard-sku---using-zones"></a>[**Szabványos SKU – zónák használatával**](#tab/option-create-public-ip-standard-zones)

A következő lépések végrehajtásával hozzon létre egy **myStandardZRPublicIP** nevű szabványos, redundáns nyilvános IP-címet.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza az **Erőforrás létrehozása** lehetőséget. 
3. A keresőmezőbe írja be a *nyilvános IP-cím* kifejezést.
4. A keresési eredmények között válassza a **nyilvános IP-cím** elemet. Ezután a **nyilvános IP-cím** lapon válassza a **Létrehozás** lehetőséget.
5. A **nyilvános IP-cím létrehozása** lapon adja meg vagy válassza ki a következő adatokat: 

    | Beállítás                 | Érték                       |
    | ---                     | ---                         |
    | IP-verzió              | IPv4 kiválasztása                 |    
    | Termékváltozat                     | **Standard** kiválasztása         |
    | Szintű (ha megjelenik *)                  | **Régió** kiválasztása         |
    | Name                    | *MyStandardZRPublicIP* megadása          |
    | IP-cím hozzárendelése   | Vegye figyelembe, hogy ez a "statikus" értékként lesz zárolva.                                        |
    | Üresjárati időkorlát (perc)  | Hagyja meg az értéket 4        |
    | DNS-névcímke          | Hagyja üresen az értéket    |
    | Előfizetés            | Válassza ki előfizetését.   |
    | Erőforráscsoport          | Válassza az **új létrehozása** elemet, írja be a myResourceGroup, majd kattintson **az OK gombra** . |
    | Hely                | Válassza az **USA 2. keleti** régióját      |
    | Rendelkezésre állási zóna       | Válasszon **zónát – redundáns** vagy válasszon ki egy adott zónát (lásd az alábbi megjegyzést) |

Vegye figyelembe, hogy ezek csak a [Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)-val rendelkező régiókban érvényesek.  (Kiválaszthat egy adott zónát ezekben a régiókban, bár nem lesz rugalmas a zónabeli meghibásodás miatt.)

\* = A réteg a [régiók közötti Load Balancer](https://docs.microsoft.com/azure/load-balancer/cross-region-overview) funkcióra vonatkozik, amely jelenleg előzetes verzióban érhető el.

# <a name="basic-sku"></a>[**Alapszintű termékváltozat**](#tab/option-create-public-ip-basic)

A következő lépésekkel hozzon létre egy **myBasicPublicIP** nevű alapszintű statikus nyilvános IP-címet.  Az alapszintű nyilvános IP-címek nem rendelkeznek a rendelkezésre állási zónák fogalmával.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza az **Erőforrás létrehozása** lehetőséget. 
3. A keresőmezőbe írja be a *nyilvános IP-cím* kifejezést.
4. A keresési eredmények között válassza a **nyilvános IP-cím** elemet. Ezután a **nyilvános IP-cím** lapon válassza a **Létrehozás** lehetőséget.
5. A **nyilvános IP-cím létrehozása** lapon adja meg vagy válassza ki a következő adatokat: 

    | Beállítás                 | Érték                       |
    | ---                     | ---                         |
    | IP-verzió              | IPv4 kiválasztása                 |    
    | Termékváltozat                     | **Standard** kiválasztása         |
    | Name                    | *MyBasicPublicIP* megadása          |
    | IP-cím hozzárendelése   | Válassza a **statikus** lehetőséget (lásd az alábbi megjegyzést)                                     |
    | Üresjárati időkorlát (perc)  | Hagyja meg az értéket 4        |
    | DNS-névcímke          | Hagyja üresen az értéket    |
    | Előfizetés            | Válassza ki előfizetését.   |
    | Erőforráscsoport          | Válassza az **új létrehozása** elemet, írja be a myResourceGroup, majd kattintson **az OK gombra** . |
    | Hely                | Válassza az **USA 2. keleti** régióját      |

Ha elfogadható az IP-cím időbeli változása, akkor kiválaszthatja a **dinamikus** IP-hozzárendelést.

---

## <a name="additional-information"></a>További információ 

A fent felsorolt egyes mezőkről további részleteket a [nyilvános IP-címek kezelése](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address)című témakörben talál.

## <a name="next-steps"></a>További lépések
- [Nyilvános IP-cím hozzárendelése virtuális géphez](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)
- További információ az Azure [-beli nyilvános IP-címekről](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) .
- További információ az összes [nyilvános IP-cím beállításról](virtual-network-public-ip-address.md#create-a-public-ip-address).