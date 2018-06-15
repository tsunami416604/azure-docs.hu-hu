---
title: Az Azure Site Recovery Azure ExpressRoute |} Microsoft Docs
description: Azure ExpressRoute használata az Azure Site Recovery vész-helyreállítási és áttelepítés
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 7cc4c84ebae7ade4169f8d85a2d5cc11f1df6f87
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071447"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Az Azure Site Recovery Azure ExpressRoute

A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute használatával kapcsolatokat létesíthet olyan Microsoft-felhőszolgáltatásokkal, mint például a Microsoft Azure, az Office 365 és a Dynamics 365.

Ez a cikk azt ismerteti, hogyan használható Azure ExpressRoute az Azure Site Recovery vész-helyreállítási és az áttelepítés.

## <a name="expressroute-circuits"></a>ExpressRoute-kapcsolatcsoportok

Egy ExpressRoute-kapcsolatcsoport között a helyszíni infrastruktúra és a kapcsolat-szolgáltató használatával a Microsoft más felhőszolgáltatásaival jelöli. Több ExpressRoute-Kapcsolatcsoportok sorba rendezheti. A kapcsolatok azonos vagy különböző régiókban lehet, és a helyi kapcsolat különböző szolgáltatók keresztül lehet csatlakoztatni. További tudnivalók az ExpressRoute-Kapcsolatcsoportok [Itt](../expressroute/expressroute-circuit-peerings.md).

## <a name="expressroute-routing-domains"></a>ExpressRoute útválasztási tartományok

Egy ExpressRoute-kapcsolatcsoportot több útválasztási tartomány társítva van:
-   [Az Azure magánhálózati társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) - Azure számítási szolgáltatások, azaz a virtuális gépek (IaaS), és a magánhálózati társviszony-létesítési tartomány keresztül csatlakozhatnak felhőszolgáltatásokat (PaaS), amely a virtuális hálózaton belül vannak telepítve. A magánhálózati társviszony-létesítési tartomány az a Microsoft Azure tekinthető az alaphálózat megbízható kiterjesztését.
-   [Az Azure nyilvános társviszony](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) -szolgáltatásokat, például az Azure tárolás, az SQL-adatbázisok és a webhelyek érhető el a nyilvános IP-címeket. Nyilvános IP-címek, beleértve a virtuális IP-címek a felhőalapú szolgáltatások, a nyilvános társviszony-létesítési útválasztási tartomány segítségével üzemeltetett szolgáltatások közvetlenül kapcsolódni. Az új és elavult nyilvános társviszony, és a Microsoft Peering Azure PaaS szolgáltatások helyette használható.
-   [A Microsoft társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#microsoft-peering) -kapcsolatot a Microsoft online services (Office 365, Dynamics 365 és Azure PaaS szolgáltatások) a Microsoft társviszony-létesítés keresztül történik. A Microsoft társviszony-létesítés Azure PaaS-szolgáltatásokhoz ajánlott útválasztási tartomány.

További információ, és hasonlítsa össze az útválasztási tartományok ExpressRoute [Itt](../expressroute/expressroute-circuit-peerings.md#routing-domain-comparison).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>A helyszíni Azure ExpressRoute-replikációt

Az Azure Site Recovery lehetővé teszi, hogy katasztrófa utáni helyreállítás és a helyszíni Azure-ba történő áttelepítés [Hyper-V virtuális gépek](hyper-v-azure-architecture.md), [VMware virtuális gépek](vmware-azure-architecture.md), és [fizikai kiszolgálók](physical-azure-architecture.md). Az összes helyszíni Azure forgatókönyvekre replikációs adatokat küldött, és egy Azure Storage-fiókban tárolt. Replikálás során a virtuális gép díjakat nem kell fizetnie. Ha a feladatátvételt az Azure-ba, a Site Recovery automatikusan létrehozza a Azure infrastruktúra-szolgáltatási virtuális gépeket.

A Site Recovery replikálja az adatokat egy Azure Storage-fiók egy nyilvános végpontot keresztül. A Site Recovery replikáció ExpressRoute használatához használhatja [nyilvános társviszony](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) vagy [Microsoft társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#microsoft-peering). A Microsoft társviszony-létesítés a replikáció az ajánlott útválasztási tartomány. Miután a virtuális gépek vagy kiszolgálók feladatok átadása egy Azure virtuális hálózatra, akkor is elérhetőek használatával [magánhálózati társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#azure-private-peering). Replikációs magánhálózati társviszony-létesítés keresztül nem támogatott.

Az alábbi ábrán a jelzi a kombinált forgatókönyv: ![a-helyi-az-Azure és az ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Az ExpressRoute-replikációt Azure az Azure-bA

Az Azure Site Recovery lehetővé teszi, hogy a vész-helyreállítási [Azure virtuális gépek](azure-to-azure-architecture.md). Attól függően, hogy az Azure virtuális gépek használata [Azure felügyelt lemezek](../virtual-machines/windows/managed-disks-overview.md), egy Azure Storage-fiók vagy a felügyelt lemezt a cél Azure-régió, a replika küldött replikációs adatokat. Annak ellenére, hogy a replikációs végpontok nyilvános, az Azure virtuális gép replikációs alapértelmezés szerint a replikációs forgalom nem haladnak át az interneten, függetlenül attól, amely az Azure-régió a forrás virtuális hálózat szerepel. Ha szeretné felülbírálni az Azure alapértelmezett rendszer útvonalat a 0.0.0.0/0 címelőtag egy [egyéni útvonal](../virtual-network/virtual-networks-udr-overview.md#custom-routes) és a helyszíni hálózat virtuális készülék (NVA) a virtuális gépek forgalma átirányít, de ez a konfiguráció nem ajánlott a Site Recovery a replikáció. Ha egyéni útvonalak használ, akkor [hozzon létre egy virtuális hálózati szolgáltatási végpont](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) a virtuális hálózatot "Tároló" úgy, hogy a replikálás forgalma nem hagyja meg az Azure-határ.

A vészhelyreállítás Azure virtuális gép esetében alapértelmezés szerint ExpressRoute nincs szükség a replikáció. Miután a virtuális gép nem keresztül a cél Azure-régió, akkor is elérhetőek használatával [magánhálózati társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

Ha ExpressRoute csatlakozni a helyszíni adatközpontját a forrás régió, az Azure virtuális gépeken már használ, megtervezheti, ExpressRoute-kapcsolatot, a feladatátvételi cél régió újbóli létrehozásához. Az azonos ExpressRoute-kapcsolatcsoport segítségével a cél-régió, egy új virtuális hálózati kapcsolaton keresztül csatlakozni, vagy egy külön ExpressRoute-kapcsolatcsoportot és vész-helyreállítási kapcsolat használatára. A másik lehetséges forgatókönyv leírása olvasható [Itt](azure-vm-disaster-recovery-with-expressroute.md#failover-models-with-expressroute).

Azure virtuális gépeken belül az azonos földrajzi fürt részletes bármely Azure régióban replikálhatja [Itt](../site-recovery/azure-to-azure-support-matrix.md#region-support). Ha a választott cél Azure-régió, mint a forrás geopolitikai ugyanabban a régióban kívül esik, előfordulhat, hogy szeretné ExpressRoute-támogatás engedélyezése. További részletekért ellenőrizze [helyek ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) és [ExpressRoute árképzési](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>További lépések
- További információ [ExpressRoute-Kapcsolatcsoportok](../expressroute/expressroute-circuit-peerings.md).
- További információ [ExpressRoute útválasztási tartományok](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- További információ [helyek ExpressRoute](../expressroute/expressroute-locations.md).
- További információ a vész-helyreállítási [ExpressRoute Azure virtuális gépek ](azure-vm-disaster-recovery-with-expressroute.md).
