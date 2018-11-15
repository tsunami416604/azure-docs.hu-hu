---
title: Az ExpressRoute globális elérhetőséggel kapcsolatos |} A Microsoft Docs
description: Ez a cikk leírja, mi az az ExpressRoute globális elérhetőségű.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: cherylmc
ms.openlocfilehash: 23f3d2c4a53e05d962fdc7da72b336cc66f21306
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683066"
---
# <a name="expressroute-global-reach-preview"></a>Globális elérhetőség az ExpressRoute (előzetes verzió)
Az ExpressRoute egy magán- és rugalmas módja a helyszíni hálózatok csatlakoztatása a Microsoft Cloud. Számos Microsoft felhőszolgáltatások, például Azure, Office 365 és Dynamics 365 elérését a saját adatközpontban vagy a vállalati hálózathoz. Például előfordulhat, hogy rendelkezik egy fiókirodában, a San franciscóban az ExpressRoute-kapcsolatcsoport szilícium-völgy és a egy másik fiókiroda londoni az ExpressRoute-kapcsolatcsoport város. Mindkét fiókirodák lehet nagy sebességű kapcsolat az USA nyugati régiója, Egyesült Királyság déli régiója és az Azure-erőforrások. Azonban a fiókirodák közvetlenül egymással adatok nem váltanak. Más szóval 10.0.1.0/24 küldhet adatokat, 10.0.3.0/24 és 10.0.4.0/24, de nem 10.0.2.0/24.

![nélkül][1]

A **ExpressRoute globális elérhetőségű**, ExpressRoute-Kapcsolatcsoportok teszik a helyszíni hálózat között egy privát hálózati kapcsolat. A fenti példában, ExpressRoute globális elérhetőségű, azonban kiegészül a San Franciscóban office (10.0.1.0/24) is közvetlenül adatcserét (10.0.2.0/24) keresztül a meglévő ExpressRoute-Kapcsolatcsoportok és a Microsoft globális hálózaton keresztül londoni irodájában. 

![a][2]

## <a name="use-case"></a>Használati eset
Az ExpressRoute globális elérhetőségű célja, hogy a szolgáltató WAN végrehajtása kiegészíti, és a fiókirodák összekapcsolása a világ. Például, ha a szolgáltató elsősorban az Egyesült Államokban működik, és rendelkezik társított összes az ágak, az Egyesült Államokban, de a szolgáltató nem működik a japán és Hongkong (KKT), az ExpressRoute globális elérhetőségű együttműködve egy helyi szolgáltató és a A Microsoft fog csatlakozni az ágak van Egyesült államokbeli ExpressRoute-és globális hálózata azokat.

![Használati eset][3]

## <a name="availability"></a>Rendelkezésre állás 
Az ExpressRoute globális elérhetőségű jelenleg támogatott a következő helyeken.

* Ausztrália
* Franciaország
* Hongkong KKT
* Írország
* Japán
* Hollandia
* Egyesült
* Egyesült Államok

Az ExpressRoute-Kapcsolatcsoportok létre kell hozni a [ExpressRoute-társviszony-létesítési helyszínek](expressroute-locations.md) a fenti országban vagy régióban. Ahhoz, hogy az ExpressRoute globális elérhetőségű közötti [geopolitikai régiókon](expressroute-locations.md), a Kapcsolatcsoportok prémium Termékváltozatot kell lennie.

## <a name="next-steps"></a>További lépések
1. [További információ az ExpressRoute globális elérhetőségű](expressroute-faqs.md)
2. [Az ExpressRoute globális elérhetőségű engedélyezése](expressroute-howto-set-global-reach.md)
3. [Azure virtuális hálózati kapcsolat ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "globális jelenlét nélkül diagramja"
[2]: ./media/expressroute-global-reach/2.png "globális elérhetőségű diagramja"
[3]: ./media/expressroute-global-reach/3.png "használja a kis-és globális elérhetőséggel"
