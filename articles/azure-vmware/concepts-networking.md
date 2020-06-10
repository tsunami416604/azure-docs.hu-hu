---
title: Fogalmak – hálózati kapcsolat
description: Ismerje meg az Azure VMware-megoldás (AVS) legfontosabb szempontjait és a Hálózatkezelés és az összekapcsolhatóság alkalmazási eseteit
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 35d886fe0f6a68e522d7f2cf20b450b5d9afc199
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629207"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Azure VMware-megoldás (AVS) – előzetes hálózati és összekapcsolási fogalmak

Az Azure VMware-megoldás (AVS) privát felhők és a helyszíni környezetek, illetve az Azure-beli virtuális hálózatok közötti hálózati kapcsolat lehetővé teszi a privát felhő elérését és használatát. Ebben a cikkben néhány kulcsfontosságú hálózatkezelési és összekapcsolási fogalmat is ismertetünk, amelyek az összekapcsolások alapját alkotják.

Az összekapcsolhatóság hasznos perspektívája az, hogy figyelembe veszik az AVS Private Cloud implementációinak két típusát: a megvalósítások alapszintű Azure-alapú, a teljes helyszíni és a saját felhőalapú összekapcsolást biztosító implementációk.

Az AVS privát felhők használati esetei a következők:
- új VMware virtuális gép számítási feladatok a felhőben
- VIRTUÁLIS gépek számítási feladatainak a felhőbe való betörése
- VIRTUÁLIS gépek munkaterhelésének áttelepítése a felhőbe
- vész-helyreállítás
- Azure-szolgáltatások felhasználása

 Az AVS szolgáltatás összes használati esete engedélyezve van a helyszíni és a saját felhőalapú kapcsolat között. Az alapszintű összekapcsolási modell olyan AVS-értékelések vagy-implementációk esetében ajánlott, amelyek nem igényelnek hozzáférést a helyszíni környezetekhez.

Az alábbi szakaszokban az AVS Private Cloud-kapcsolat két típusát ismertetjük.  A legalapvetőbb kapcsolat az "Azure Virtual Network connectivity"; lehetővé teszi, hogy a saját felhőjét csak egyetlen virtuális hálózattal kezelhesse és használja az Azure-ban. A "helyszíni kapcsolat" című témakörben ismertetett kapcsolat kibővíti a virtuális hálózati kapcsolatot, hogy a helyszíni környezetek és az AVS privát felhők közötti összekapcsolást is magában foglalja.

## <a name="azure-virtual-network-interconnectivity"></a>Azure-beli virtuális hálózatok összekapcsolása

Az alábbi ábrán látható, a privát felhőalapú központi telepítés idején létrehozott alapszintű hálózati kapcsolat. Az Azure-beli virtuális hálózatok és a privát felhő közötti logikai, ExpressRoute-alapú hálózatkezelést jeleníti meg. Az összekapcsolási funkció három elsődleges használati esetet teljesít:
- Bejövő hozzáférés a felügyeleti hálózatokhoz, ahol a vCenter-kiszolgáló és a NSX-T kezelő található.
    - Az Azure-előfizetésében lévő virtuális gépekről nem a helyszíni rendszerekből érhető el.
- Kimenő hozzáférés a virtuális gépekről az Azure-szolgáltatásokba.
- A privát felhőt futtató munkaterhelések bejövő hozzáférése és felhasználása.

![Alapszintű virtuális hálózat – privát Felhőbeli kapcsolat](./media/concepts/adjacency-overview-drawing-single.png)

Az ebben a virtuális hálózatban lévő ExpressRoute áramkört a rendszer akkor hozza létre, amikor az előfizetésben lévő virtuális hálózatról hoz létre kapcsolatot a saját felhő ExpressRoute áramkörével. A társítás egy engedélyezési kulcsot és egy olyan áramkör-azonosítót használ, amelyet a Azure Portal kér. A ExpressRoute létrehozott kapcsolat egy privát, egy-az-egyhez kapcsolat a privát felhő és a virtuális hálózat között. Kezelheti saját felhőjét, felhasználhatja a saját Felhőbeli munkaterheléseket, és hozzáférhet az Azure-szolgáltatásokhoz az ExpressRoute-kapcsolaton keresztül.

Az AVS Private Cloud üzembe helyezése esetén egyetlen/22 magánhálózati címtartomány szükséges. Ez a Címterület nem fedi át az előfizetésben lévő más virtuális hálózatokban használt címeket. Ezen a címterületon belül a felügyelet, a kiépítés és a vMotion hálózat automatikusan kiépítve. Az Útválasztás BGP-alapú, és alapértelmezés szerint automatikusan kiépítve és engedélyezve van minden egyes privát Felhőbeli telepítéshez.

Privát felhő telepítésekor a vCenter és a NSX-T Manager IP-címei is elérhetők. Ezen felügyeleti felületek eléréséhez további erőforrásokat fog létrehozni az előfizetésben található virtuális hálózatban. Ezeknek az erőforrásoknak a létrehozásához és a privát ExpressRoute létrehozásához szükséges eljárások az oktatóanyagokban érhetők el.

Tervezze meg a saját felhőalapú logikai hálózatot, és implementálja a NSX-T-vel. A privát felhő előre kiosztott NSX-T tartalmaz. A 0. rétegbeli átjáró & 1. rétegbeli átjáró előre ki van építve az Ön számára. Létrehozhat egy szegmenst, és csatolhatja azt a meglévő 1. rétegbeli átjáróhoz, vagy csatolhat egy új, 1. rétegbeli átjáróhoz, amelyet meghatározhat. A NSX-T logikai hálózati összetevők a munkaterhelések közötti kelet-nyugati kapcsolatot biztosítanak, és az internettel és az Azure-szolgáltatásokkal való észak-déli kapcsolatot is biztosítanak. 

## <a name="on-premises-interconnectivity"></a>Helyszíni kapcsolat

A helyszíni környezetek is csatlakoztathatók az AVS privát felhőkhöz. Ez az összekapcsolási típus az előző szakaszban leírt alapszintű kapcsolat kiterjesztése.

![virtuális hálózat és helyszíni teljes Private Cloud connectivity](./media/concepts/adjacency-overview-drawing-double.png)

A privát felhőhöz való teljes összekapcsoláshoz a Azure Portal segítségével engedélyezheti a ExpressRoute Global Reach a saját felhőalapú ExpressRoute áramkör és egy helyszíni ExpressRoute-áramkör között. Ez a konfiguráció kiterjeszti az alapszintű kapcsolatot, hogy a helyszíni környezetből is hozzáférjen a privát felhőkhöz.

A helyszíni és az Azure-beli virtuális hálózati ExpressRoute áramkört a helyszíni környezetek és az Azure-beli privát felhő közötti kapcsolathoz kell kötni. Ez a ExpressRoute-áramkör az előfizetésében van, és nem része a felhőalapú telepítésnek. A helyszíni ExpressRoute áramkör a jelen dokumentum hatókörén kívül esik. Ha helyszíni kapcsolatra van szüksége a saját felhőhöz, használhat egy meglévő ExpressRoute-áramkört, vagy vásárolhat egyet a Azure Portal.

A Global Reachtel való összekapcsolása után a két ExpressRoute-áramkör irányítja a hálózati forgalmat a helyszíni környezetek és a saját felhő között. A helyszíni és a privát felhő közötti kapcsolat az előző ábrán látható. A diagramon ábrázolt kapcsolat a következő használati eseteket teszi lehetővé:

- VCenter vMotion gyors/hideg
- Helyszíni és AVS Private Cloud Management-hozzáférés

A teljes kapcsolat engedélyezéséhez meg kell adni egy engedélyezési kulcsot és a Global Reach privát társ-AZONOSÍTÓját a Azure Portal. A kulcs és az azonosító használatával Global Reach hozhat létre az előfizetésben található ExpressRoute-áramkör és az új privát felhőhöz tartozó ExpressRoute áramkör között. A [privát felhő létrehozásával kapcsolatos oktatóanyag](tutorial-create-private-cloud.md) a kulcs és az azonosító kérésére és használatára vonatkozó eljárásokat tartalmazza.

A megoldás útválasztási követelményei megkövetelik a saját felhőalapú hálózati címtartomány megtervezését, hogy elkerülje az átfedést más virtuális hálózatokkal és helyszíni hálózatokkal. Az AVS Private-felhőkhöz legalább egy `/22` CIDR hálózati címfordító szükséges az alhálózatok számára, alább látható. Ez a hálózat kiegészíti a helyszíni hálózatokat. A helyszíni környezetekhez és a virtuális hálózatokhoz való kapcsolódáshoz nem átfedésben lévő hálózati címterület szükséges.

Példa a `/22` CIDR hálózati címterület:`10.10.0.0/22`

Az alhálózatok:

| Hálózati forgalom             | Alhálózat | Példa        |
| ------------------------- | ------ | -------------- |
| Magánfelhő-felügyelet            | `/24`    | `10.10.0.0/24`   |
| vMotion hálózat       | `/24`    | `10.10.1.0/24`   |
| Virtuális gépek számítási feladatai | `/24`   | `10.10.2.0/24`   |
| ExpressRoute-társítás | `/24`    | `10.10.3.8/30`   |

## <a name="next-steps"></a>Következő lépések 

A következő lépés a [saját Felhőbeli tárolási fogalmak](concepts-storage.md)megismerése.

<!-- LINKS - external -->
[enable Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach

<!-- LINKS - internal -->

