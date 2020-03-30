---
title: 'Azure ExpressRoute: Csatlakozás a Microsoft Cloud szolgáltatáshoz a Global Reach használatával'
description: Ez a cikk ismerteti az ExpressRoute globális elérést.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: cherylmc
ms.openlocfilehash: f574576044b7e4ddd34289b5cc45fe5ca353f180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538504"
---
# <a name="expressroute-global-reach"></a>Az ExpressRoute Global Reach
Az ExpressRoute egy privát és rugalmas módja annak, hogy a helyszíni hálózatokat a Microsoft Cloud-hoz csatlakoztassa. Számos Microsoft-felhőszolgáltatást, például az Azure-t és az Office 365-öt a saját adatközpontjából vagy a vállalati hálózatából érheti el. Előfordulhat például, hogy san franciscói fiókirodával rendelkezik egy ExpressRoute-áramkörrel a Szilícium-völgyben, és egy másik londoni fiókirodával, amelyugyanabban a városban rendelkezik ExpressRoute-áramkörrel. Mindkét fiókiroda nagy sebességű kapcsolattal rendelkezhet az Azure-erőforrásokkal az Egyesült Államok nyugati és déli részén. A fiókirodák azonban nem tudnak közvetlenül adatokat cserélni egymással. Más szóval a 10.0.1.0/24 adatokat küldhet a 10.0.3.0/24 és a 10.0.4.0/24, de nem 10.0.2.0/24.

![Nélkül][1]

Az **ExpressRoute Globális elérés segítségével**összekapcsolhatja az ExpressRoute-áramköröket, hogy magánhálózatot hozhasson létre a helyszíni hálózatok között. A fenti példában az ExpressRoute Global Reach hozzáadásával a San Francisco-i irodája (10.0.1.0/24) közvetlenül kicserélheti az adatokat a londoni irodával (10.0.2.0/24) a meglévő ExpressRoute-áramkörökön és a Microsoft globális hálózatán keresztül. 

![a][2]

## <a name="use-case"></a>Használati eset
Az ExpressRoute Global Reach célja, hogy kiegészítse a szolgáltató WAN implementációját, és világszerte összekapcsolja a fiókirodákat. Ha például a szolgáltató elsősorban az Egyesült Államokban működik, és az Egyesült Államokban lévő összes fiókját összekapcsolta, de a szolgáltató nem japánban és Hongkongban működik, az ExpressRoute Global Reach szolgáltatással együttműködhet egy helyi szolgáltatóval, és A Microsoft az ExpressRoute és globális hálózatunk segítségével csatlakoztatja az ottani fiókjait az Egyesült Államokban található fiókokhoz.

![használati eset][3]

## <a name="availability"></a>Rendelkezésre állás 
Az ExpressRoute globális elérése jelenleg a következő helyeken támogatott.

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

Az ExpressRoute-áramköröket a fenti országokban vagy régiókban lévő [ExpressRoute-társviszony-létesítési helyeken](expressroute-locations.md) kell létrehozni. Az ExpressRoute globális elérése [a különböző geopolitikai régiók](expressroute-locations.md)közötti engedélyezéséhez az áramköröknek prémium szintű termékváltozatnak kell lenniük.

## <a name="next-steps"></a>További lépések
1. [További információ az ExpressRoute globális eléréséről](expressroute-faqs.md)
2. [Az ExpressRoute globális elérésének engedélyezése](expressroute-howto-set-global-reach.md)
3. [Az ExpressRoute-kapcsolat az Azure virtuális hálózatához](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram globális elérés nélkül"
[2]: ./media/expressroute-global-reach/2.png "diagram globális eléréssel"
[3]: ./media/expressroute-global-reach/3.png "a globális elérés használata"
