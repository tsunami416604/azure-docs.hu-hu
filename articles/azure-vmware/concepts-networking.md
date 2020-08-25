---
title: Fogalmak – hálózati kapcsolat
description: Ismerkedjen meg az Azure VMware megoldás legfontosabb szempontjaival és hálózati és kapcsolódási eseteivel.
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3420f6aa61ced7632175f3e12edda9de72639517
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750569"
---
# <a name="azure-vmware-solution-preview-networking-and-interconnectivity-concepts"></a>Azure VMware-megoldás előzetes hálózati és összekapcsolási fogalmak

Az Azure VMware-megoldás (AVS) egy VMware Private Cloud Environment-környezetet kínál, amely a helyszíni és az Azure-alapú környezetekben vagy erőforrásokban elérhető felhasználók és alkalmazások számára érhető el. Az Azure ExpressRoute és a VPN-kapcsolatok szolgáltatásai biztosítják a kapcsolatot. Ezeknek a szolgáltatásoknak konkrét hálózati címtartományok és tűzfal-portok szükségesek a szolgáltatások engedélyezéséhez.  

Privát felhő üzembe helyezése esetén a rendszer létrehoz egy privát hálózatot a felügyelethez, a létesítéshez és a vMotion. Ezek a vCenter-és NSX-T kezelőhöz, illetve a virtuális gépek vMotion vagy üzembe helyezéséhez használhatók. Az összes magánhálózat elérhető egy Azure-beli VNet vagy helyszíni környezetből. A ExpressRoute Global Reach a privát felhőknek a helyszíni környezetekhez való csatlakoztatására szolgál, és ehhez a kapcsolathoz az előfizetésben ExpressRoute áramkört igénylő VNet van szükség.

Emellett a privát felhő telepítésekor az internetre és az Azure-szolgáltatásokra való hozzáférés is kiépítve és biztosítva, hogy az üzemi hálózatokon lévő virtuális gépek képesek legyenek felhasználni őket.  Alapértelmezés szerint az Internet-hozzáférés le van tiltva az új privát felhőknél, és a szolgáltatás bármikor engedélyezhető vagy letiltható.

Az összekapcsolhatóság hasznos perspektívája az, hogy figyelembe vegye az AVS Private Cloud implementációinak két típusát:

1. Alapszintű, kizárólag az Azure [**-beli összekapcsolással**](#azure-virtual-network-interconnectivity) az Azure-ban csak egyetlen virtuális hálózattal kezelheti és használhatja a saját felhőjét. Ez a megvalósítás olyan AVS-értékelések vagy-implementációk számára ajánlott, amelyek nem igénylik a helyszíni környezetek elérését.

1. A teljes helyszíni és a [**privát felhő közötti összekapcsolások**](#on-premises-interconnectivity) kiterjesztik az alapszintű Azure-alapú implementációt, amely magában foglalja a helyszíni és az AVS privát felhők közötti összekapcsolást.
 
Ebben a cikkben a Hálózatkezelés és az összekapcsolhatóság, valamint a követelmények és korlátozások kialakításával kapcsolatos főbb fogalmakat mutatjuk be. Emellett további információkhoz is tájékozódhat az AVS Private Cloud összekapcsolási implementációjának két típusáról. Ebből a cikkből megtudhatja, hogy milyen információkra van szüksége ahhoz, hogy a hálózatkezelés megfelelően működjön az AVS használatával.

## <a name="avs-private-cloud-use-cases"></a>Az AVS Private Cloud használati esetei

Az AVS privát felhők használati esetei a következők:
- Új VMware virtuális gép számítási feladatok a felhőben
- VIRTUÁLIS gépek számítási feladatainak kitakarítása a felhőbe (csak a helyszínen, csak AVS esetén)
- VIRTUÁLIS gépek számítási feladatainak áttelepítése a felhőbe (csak a helyszínen, csak az AVS-ben)
- Vész-helyreállítás (AVS-ből AVS-re vagy a helyszínen az AVS-be)
- Azure-szolgáltatások felhasználása

> [!TIP]
> Az AVS szolgáltatás összes használati esete engedélyezve van a helyszíni és a saját felhőalapú kapcsolat között.

## <a name="azure-virtual-network-interconnectivity"></a>Azure-beli virtuális hálózatok összekapcsolása

A virtuális hálózatban a saját felhő megvalósításához kezelheti az Azure VMware-megoldás saját felhőjét, felhasználhatja a saját Felhőbeli munkaterheléseket, és hozzáférhet az Azure-szolgáltatásokhoz a ExpressRoute-kapcsolaton keresztül. 

Az alábbi ábra a privát Felhőbeli üzembe helyezés időpontjában kialakított hálózati összekapcsolást mutatja be. Az Azure-beli virtuális hálózatok és a privát felhő közötti logikai, ExpressRoute-alapú hálózatkezelést jeleníti meg. Az összekapcsolási funkció három elsődleges használati esetet teljesít:
* Bejövő hozzáférés a vCenter-kiszolgálóhoz és a NSX-T kezelőhöz, amely elérhető az Azure-előfizetésében lévő virtuális gépekről, és nem a helyszíni rendszerekből. 
* Kimenő hozzáférés a virtuális gépekről az Azure-szolgáltatásokba. 
* A privát felhőt futtató munkaterhelések bejövő hozzáférése és felhasználása.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Alapszintű virtuális hálózat privát Felhőbeli kapcsolathoz" border="false":::

## <a name="on-premises-interconnectivity"></a>Helyszíni kapcsolat

A virtuális hálózatban és a helyszínen a teljes körű felhőalapú megvalósítás érdekében az Azure VMware-megoldás saját felhőit is elérheti a helyszíni környezetekben. Ez a megvalósítás az előző szakaszban leírt alapszintű implementáció kiterjesztése. Az alapszintű implementációhoz hasonlóan egy ExpressRoute áramkörre van szükség, de ezzel a megvalósítással a helyszíni környezetek és az Azure-beli privát felhő közötti kapcsolat is használható. 

Az alábbi ábra a helyszíni és a saját felhő közötti összekapcsolást mutatja be, amely a következő használati eseteket teszi lehetővé:
* VCenter vMotion gyors/hideg
* Helyszíni Azure VMware-megoldás saját felhőalapú felügyeleti hozzáférés

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Virtuális hálózat és helyszíni teljes Private Cloud connectivity" border="false":::

A privát felhővel való teljes összekapcsoláshoz engedélyezze a ExpressRoute Global Reach, majd kérjen egy engedélyezési kulcsot és egy privát társ-AZONOSÍTÓt a Azure Portal Global Reachhoz. Az engedélyezési kulcs és a társítási azonosító segítségével Global Reach lehet létrehozni az előfizetésben található ExpressRoute-áramkör és az új privát felhőhöz tartozó ExpressRoute áramkör között. A csatolást követően a két ExpressRoute áramkör a helyi környezetek közötti hálózati forgalmat átirányítja a saját felhőbe.  Az engedélyezési kulcs és a társ-azonosító kéréséhez és használatához szükséges eljárásokért tekintse meg azt az [oktatóanyagot, amely ExpressRoute-Global REACH-társítást hoz létre egy privát felhőben](tutorial-expressroute-global-reach-private-cloud.md) .

## <a name="next-steps"></a>További lépések 

- További információ a [hálózati kapcsolattal kapcsolatos megfontolásokról és követelményekről](tutorial-network-checklist.md). 
- További információ a [saját Felhőbeli tárolási fogalmakról](concepts-storage.md).


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

