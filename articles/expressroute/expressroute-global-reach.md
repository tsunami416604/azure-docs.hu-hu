---
title: 'Azure ExpressRoute: Kapcsolódás a Microsoft Cloudhoz Global Reach használatával'
description: Ez a cikk a ExpressRoute Global Reach ismerteti.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: cherylmc
ms.openlocfilehash: 5022efacf33a2c07f1388666b5de26ee86322739
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636733"
---
# <a name="expressroute-global-reach"></a>Az ExpressRoute Global Reach
A ExpressRoute egy privát és rugalmas módszer a helyi hálózatok Microsoft Cloudhoz való összekapcsolására. Számos Microsoft Cloud Services, például az Azure és az Office 365 is elérhető a privát adatközpontból vagy a vállalati hálózatból. Előfordulhat például, hogy egy San Francisco-beli fiókirodában egy ExpressRoute áramkör található a Szilícium-völgyben és egy másik fiókirodában Londonban, és egy ExpressRoute-áramkör található ugyanabban a városban. Mindkét fiókirodában nagy sebességű kapcsolat lehet az USA nyugati és Egyesült Királyság déli régiója Azure-erőforrásaival. A fiókirodák azonban nem tudnak közvetlenül átcserélni az adatcserét. Más szóval a 10.0.1.0/24 képes az 10.0.3.0/24 és a 10.0.4.0/24 adatküldésre, de nem a 10.0.2.0/24 értékre.

![nélkül][1]

A **ExpressRoute Global REACH**összekapcsolhatja a ExpressRoute-áramköröket, hogy a helyi hálózatok között privát hálózatot hozzon fel. A fenti példában a ExpressRoute-Global Reach hozzáadásával a San Francisco-iroda (10.0.1.0/24) közvetlenül az adatok cseréjét a londoni irodával (10.0.2.0/24) a meglévő ExpressRoute-áramkörökkel és a Microsoft globális hálózatán keresztül végezheti el. 

![a][2]

## <a name="use-case"></a>Használati eset
A ExpressRoute Global Reach úgy lett kialakítva, hogy kiegészítse a szolgáltató WAN-implementációját, és összekösse a fiókirodákat a világ különböző pontjain. Ha például a szolgáltató elsődlegesen a Egyesült Államok működik, és az Egyesült Államokban az összes ágát összekapcsolta, de a szolgáltató nem működik Japánban és Hongkongban, a ExpressRoute Global Reach a helyi szolgáltatóval dolgozhat, és a Microsoft a ExpressRoute és a globális hálózattal is összeköti az ágakat az Egyesült Államokban.

![használati eset][3]

## <a name="availability"></a>Rendelkezésre állás 
A ExpressRoute Global Reach jelenleg a következő helyeken támogatottak.

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
* Norvégia
* Szingapúr
* Svédország
* Svájc
* Egyesült Királyság
* Egyesült Államok

A ExpressRoute-áramköröket a fenti országokban vagy régióban található ExpressRoute-összevonási [helyeken](expressroute-locations.md) kell létrehozni. Ahhoz, hogy a ExpressRoute Global Reach a [különböző geopolitikai régiók](expressroute-locations.md)között, az áramkörnek prémium SKU-nak kell lennie.

## <a name="next-steps"></a>Következő lépések
1. [A Global Reach GYIK megtekintése](expressroute-faqs.md#globalreach)
2. [További információ a Global Reach engedélyezéséről](expressroute-howto-set-global-reach.md)
3. [Megtudhatja, hogyan kapcsolhat ExpressRoute-áramkört a virtuális hálózathoz](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "globális elérhetőség nélküli diagram"
[2]: ./media/expressroute-global-reach/2.png "globálisan elérhető diagram"
[3]: ./media/expressroute-global-reach/3.png "globális elérési eset használata"
