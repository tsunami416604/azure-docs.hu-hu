---
title: 'Azure ExpressRoute: Kapcsolódás a Microsoft Cloudhoz Global Reach használatával'
description: Ez a cikk bemutatja az ExpressRoute globális elérhetőségű.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: cherylmc
ms.openlocfilehash: f574576044b7e4ddd34289b5cc45fe5ca353f180
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538504"
---
# <a name="expressroute-global-reach"></a>Az ExpressRoute Global Reach
Az ExpressRoute egy magán- és rugalmas módja a helyszíni hálózatok csatlakoztatása a Microsoft Cloud. Számos Microsoft Cloud Services, például az Azure és az Office 365 is elérhető a privát adatközpontból vagy a vállalati hálózatból. Például előfordulhat, hogy rendelkezik egy fiókirodában, a San franciscóban az ExpressRoute-kapcsolatcsoport szilícium-völgy és a egy másik fiókiroda londoni az ExpressRoute-kapcsolatcsoport város. Mindkét fiókirodák lehet nagy sebességű kapcsolat az USA nyugati régiója, Egyesült Királyság déli régiója és az Azure-erőforrások. Azonban a fiókirodák közvetlenül egymással adatok nem váltanak. Más szóval 10.0.1.0/24 küldhet adatokat, 10.0.3.0/24 és 10.0.4.0/24, de nem 10.0.2.0/24.

![nélkül][1]

A **ExpressRoute Global REACH**összekapcsolhatja a ExpressRoute-áramköröket, hogy a helyi hálózatok között privát hálózatot hozzon fel. A fenti példában, ExpressRoute globális elérhetőségű, azonban kiegészül a San Franciscóban office (10.0.1.0/24) is közvetlenül adatcserét (10.0.2.0/24) keresztül a meglévő ExpressRoute-Kapcsolatcsoportok és a Microsoft globális hálózaton keresztül londoni irodájában. 

![a][2]

## <a name="use-case"></a>Használati eset
Az ExpressRoute globális elérhetőségű célja, hogy a szolgáltató WAN végrehajtása kiegészíti, és a fiókirodák összekapcsolása a világ. Például, ha a szolgáltató elsősorban az Egyesült Államokban működik, és rendelkezik társított összes az ágak, az Egyesült Államokban, de a szolgáltató nem működik a japán és Hongkong (KKT), az ExpressRoute globális elérhetőségű együttműködve egy helyi szolgáltató és a A Microsoft fog csatlakozni az ágak van Egyesült államokbeli ExpressRoute-és globális hálózata azokat.

![használati eset][3]

## <a name="availability"></a>Rendelkezésre állás 
Az ExpressRoute globális elérhetőségű jelenleg támogatott a következő helyeken.

* Ausztrália
* Kanada
* Franciaország
* Németország
* Hongkong (KKT)
* Írország
* Japán
* Dél-Korea
* Hollandia
* Új-Zéland
* Szingapúr
* Svájc
* Egyesült Királyság
* Egyesült Államok

A ExpressRoute-áramköröket a fenti országokban vagy régióban található ExpressRoute-összevonási [helyeken](expressroute-locations.md) kell létrehozni. Ahhoz, hogy a ExpressRoute Global Reach a [különböző geopolitikai régiók](expressroute-locations.md)között, az áramkörnek prémium SKU-nak kell lennie.

## <a name="next-steps"></a>Következő lépések
1. [További információ a ExpressRoute Global Reach](expressroute-faqs.md)
2. [A ExpressRoute engedélyezése Global Reach](expressroute-howto-set-global-reach.md)
3. [ExpressRoute-áramkör összekapcsolása az Azure Virtual networkvel](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "globális elérhetőség nélküli diagram"
[2]: ./media/expressroute-global-reach/2.png "globálisan elérhető diagram"
[3]: ./media/expressroute-global-reach/3.png "globális elérési eset használata"
