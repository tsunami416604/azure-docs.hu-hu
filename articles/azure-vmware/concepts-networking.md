---
title: Fogalmak – hálózati kapcsolat
description: Ismerje meg az Azure VMware-megoldás (AVS) legfontosabb szempontjait és a Hálózatkezelés és az összekapcsolhatóság alkalmazási eseteit
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 6f1f1f5a089781f1f7e882c9c8692f0c845ae485
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214094"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Az Azure VMware Solution (AVS) előzetes verziója, hálózatkezelési és összekapcsolási fogalmak

Az Azure VMware-megoldás (AVS) privát felhők és a helyszíni környezetek, illetve az Azure-beli virtuális hálózatok közötti hálózati kapcsolat lehetővé teszi a privát felhő elérését és használatát. Ebben a cikkben a Hálózatkezelés és az összekapcsolhatóság alapját képező főbb fogalmakat mutatjuk be.

Az összekapcsolhatóság hasznos perspektívája az, hogy figyelembe vegye az AVS Private Cloud implementációinak két típusát:

1. Alapszintű, kizárólag az Azure [**-beli összekapcsolással**](#azure-virtual-network-interconnectivity) az Azure-ban csak egyetlen virtuális hálózattal kezelheti és használhatja a saját felhőjét. Ez a megvalósítás olyan AVS-értékelések vagy-implementációk számára ajánlott, amelyek nem igénylik a helyszíni környezetek elérését.

1. A teljes helyszíni és a [**privát felhő közötti összekapcsolások**](#on-premises-interconnectivity) kiterjesztik az alapszintű Azure-alapú implementációt, amely magában foglalja a helyszíni és az AVS privát felhők közötti összekapcsolást.
 
További információt a követelményekről és az alábbi szakaszokban ismertetett, az AVS Private Cloud összekapcsolási implementációjának két típusáról.

## <a name="avs-private-cloud-use-cases"></a>Az AVS Private Cloud használati esetei

Az AVS privát felhők használati esetei a következők:
- új VMware virtuális gép számítási feladatok a felhőben
- VIRTUÁLIS gépek számítási feladatainak kitakarítása a felhőbe (csak a helyszínen, csak AVS esetén)
- VIRTUÁLIS gépek számítási feladatainak áttelepítése a felhőbe (csak a helyszínen, csak az AVS-ben)
- vész-helyreállítás (AVS-ből AVS-re vagy a helyszínen az AVS-be)
- Azure-szolgáltatások felhasználása

 Az AVS szolgáltatás összes használati esete engedélyezve van a helyszíni és a saját felhőalapú kapcsolat között. 

## <a name="virtual-network-and-expressroute-circuit-requirements"></a>A virtuális hálózat és a ExpressRoute áramköri követelményei
 
Amikor létrehoz egy kapcsolatot az előfizetésben található virtuális hálózatból, a ExpressRoute áramkör a társításon keresztül jön létre, és egy engedélyezési kulcsot és egy, a Azure Portal által kért társ-azonosítót használ. A társítás egy privát, egy-az-egyhez kapcsolat a privát felhő és a virtuális hálózat között.

> [!NOTE] 
> A ExpressRoute áramkör nem része a felhőalapú telepítésnek. A helyszíni ExpressRoute áramkör a jelen dokumentum hatókörén kívül esik. Ha helyszíni kapcsolatra van szüksége a saját felhőhöz, használhat egy meglévő ExpressRoute-áramkört, vagy vásárolhat egyet a Azure Portal.

Privát felhő telepítésekor a vCenter és a NSX-T kezelő IP-címeit kapja meg. Ezen felügyeleti felületek eléréséhez további erőforrásokat kell létrehoznia az előfizetésében található virtuális hálózatban. Ezen erőforrások létrehozásához és ExpressRoute létrehozásához szükséges eljárásokat az oktatóanyagokban találja.

A privát felhőalapú logikai hálózat előre kiépített NSX-T tartalmaz. Az Ön számára előre kiépített 0. rétegbeli átjáró és 1. rétegbeli átjáró. Létrehozhat egy szegmenst, és csatolhatja azt a meglévő 1. rétegbeli átjáróhoz, vagy csatolhatja azt egy Ön által definiált új 1. rétegbeli átjáróhoz. A NSX-T logikai hálózati összetevők a munkaterhelések közötti kelet-nyugati kapcsolatot biztosítanak, és az internettel és az Azure-szolgáltatásokkal való észak-déli kapcsolatot is biztosítanak. 

## <a name="routing-and-subnet-requirements"></a>Útválasztási és alhálózati követelmények

Az Útválasztás Border Gateway Protocol (BGP) alapú, amely automatikusan kiépítve és alapértelmezés szerint engedélyezve van minden egyes privát Felhőbeli telepítésnél. Az AVS Private-felhők esetében meg kell terveznie a saját Felhőbeli hálózati címtartomány minimális/22 előtag-hosszát az alhálózatok CIDR, az alábbi táblázatban látható módon. A Címterület nem fedi át az előfizetésben és a helyszíni hálózatokban lévő más virtuális hálózatokban használt címeket. Ezen a címen belül a rendszer automatikusan kiépíti a felügyeletet, az üzembe helyezést és a vMotion-hálózatokat.

Példa a `/22` CIDR hálózati címterület:  `10.10.0.0/22`

Az alhálózatok:

| Hálózati forgalom             | Alhálózat | Példa        |
| ------------------------- | ------ | -------------- |
| Magánfelhő-felügyelet  | `/24`  | `10.10.0.0/24` |
| vMotion hálózat           | `/24`  | `10.10.1.0/24` |
| Virtuális gépek számítási feladatai              | `/24`  | `10.10.2.0/24` |
| ExpressRoute-társítás      | `/24`  | `10.10.3.8/30` |


## <a name="azure-virtual-network-interconnectivity"></a>Azure-beli virtuális hálózatok összekapcsolása

A virtuális hálózatban a saját felhő megvalósításához kezelheti az AVS privát felhőjét, felhasználhatja a saját Felhőbeli munkaterheléseket, és hozzáférhet az Azure-szolgáltatásokhoz a ExpressRoute-kapcsolaton keresztül. 

Az alábbi ábra a privát Felhőbeli üzembe helyezés időpontjában kialakított hálózati összekapcsolást mutatja be. Az Azure-beli virtuális hálózatok és a privát felhő közötti logikai, ExpressRoute-alapú hálózatkezelést jeleníti meg. Az összekapcsolási funkció három elsődleges használati esetet teljesít:
* Bejövő hozzáférés a vCenter-kiszolgálóhoz és a NSX-T kezelőhöz, amely elérhető az Azure-előfizetésében lévő virtuális gépekről, és nem a helyszíni rendszerekből. 
* Kimenő hozzáférés a virtuális gépekről az Azure-szolgáltatásokba. 
* A privát felhőt futtató munkaterhelések bejövő hozzáférése és felhasználása.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Alapszintű virtuális hálózat privát Felhőbeli kapcsolathoz" border="false":::

## <a name="on-premises-interconnectivity"></a>Helyszíni kapcsolat

A virtuális hálózatban és a helyszínen a teljes körű saját felhő megvalósításához a helyi környezetekről férhet hozzá az AVS privát felhőkhöz. Ez a megvalósítás az előző szakaszban leírt alapszintű implementáció kiterjesztése. Az alapszintű implementációhoz hasonlóan egy ExpressRoute áramkörre van szükség, de ezzel a megvalósítással a helyszíni környezetek és az Azure-beli privát felhő közötti kapcsolat is használható. 

Az alábbi ábra a helyszíni és a saját felhő közötti összekapcsolást mutatja be, amely a következő használati eseteket teszi lehetővé:
* VCenter vMotion gyors/hideg
* Helyszíni és AVS Private Cloud Management-hozzáférés

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Virtuális hálózat és helyszíni teljes Private Cloud connectivity" border="false":::

A privát felhővel való teljes összekapcsoláshoz engedélyezze a ExpressRoute Global Reach, majd kérjen egy engedélyezési kulcsot és egy privát társ-AZONOSÍTÓt a Azure Portal Global Reachhoz. Az engedélyezési kulcs és a társítási azonosító segítségével Global Reach lehet létrehozni az előfizetésben található ExpressRoute-áramkör és az új privát felhőhöz tartozó ExpressRoute áramkör között. A csatolást követően a két ExpressRoute áramkör a helyi környezetek közötti hálózati forgalmat átirányítja a saját felhőbe.  Az engedélyezési kulcs és a társ-azonosító kéréséhez és használatához szükséges eljárásokért tekintse meg azt az [oktatóanyagot, amely ExpressRoute-Global REACH-társítást hoz létre egy privát felhőben](tutorial-expressroute-global-reach-private-cloud.md) .


## <a name="next-steps"></a>Következő lépések 

A következő lépés a [saját Felhőbeli tárolási fogalmak](concepts-storage.md)megismerése.

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
