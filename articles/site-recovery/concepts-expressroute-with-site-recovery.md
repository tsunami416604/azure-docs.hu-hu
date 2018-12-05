---
title: Tudnivalók az Azure Site Recovery az Azure ExpressRoute használata vész-helyreállítási és áttelepítési |} A Microsoft Docs
description: Ismerteti, hogyan lehet a vészhelyreállításra és migrálás az Azure Site Recovery szolgáltatással az Azure ExpressRoute használata.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 60da5d4e80a7465d02926066298a5dc63afb1de7
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52875178"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Az Azure Site Recovery az Azure ExpressRoute

A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute használatával kapcsolatokat létesíthet olyan Microsoft-felhőszolgáltatásokkal, mint például a Microsoft Azure, az Office 365 és a Dynamics 365.

Ez a cikk bemutatja, hogyan használhatja az Azure ExpressRoute az Azure Site Recovery vész-helyreállítási és a migráláshoz.

## <a name="expressroute-circuits"></a>ExpressRoute-kapcsolatcsoportok

ExpressRoute-kapcsolatcsoport a helyszíni infrastruktúra és a egy kapcsolatszolgáltatón keresztül a Microsoft-felhőszolgáltatások közötti logikai kapcsolatot jelöli. Több ExpressRoute-Kapcsolatcsoportok rendezheti. Minden kapcsolatnak az azonos vagy eltérő régiókban is lehetnek, és a helyszíni eredetű különböző kapcsolatszolgáltatók keresztül lehet csatlakoztatni. További információ az ExpressRoute-Kapcsolatcsoportok [Itt](../expressroute/expressroute-circuit-peerings.md).

## <a name="expressroute-routing-domains"></a>ExpressRoute útválasztási tartományok

ExpressRoute-kapcsolatcsoport több útválasztási tartomány társítva van:
-   [Az Azure privát társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#privatepeering) – Azure számítási szolgáltatások, azaz a virtuális gépek (IaaS), és a privát társviszony-létesítési tartományon keresztül csatlakozhat egy virtuális hálózaton belül üzembe helyezett cloud services (PaaS). A privát társviszony-létesítési tartomány egy megbízható kiterjesztése az alaphálózat számít a Microsoft Azure-bA.
-   [Az Azure nyilvános társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#publicpeering) – például az Azure Storage, SQL Database és Websites szolgáltatásokhoz a nyilvános IP-címek érhetők el. A nyilvános IP-címeket, beleértve a virtuális IP-címek a felhőalapú szolgáltatások keresztül a nyilvános társviszony-létesítési útválasztási tartomány-ban üzemeltetett szolgáltatások privát módon csatlakozhat. Új létrehozás a már nyilvános társviszony-létesítés elavult, és a Microsoft Peering kell használni helyette az Azure PaaS-szolgáltatások.
-   [Microsoft társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) -kapcsolatot a Microsoft online szolgáltatásaihoz (az Office 365, Dynamics 365 és Azure PaaS-szolgáltatások) van a Microsoft társviszony-létesítésen keresztül. Microsoft társviszony-létesítésre kapcsolódni az Azure PaaS-szolgáltatások az ajánlott útválasztási tartomány.

Tudjon meg többet, és hasonlítsa össze az ExpressRoute útválasztási tartományok [Itt](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>A helyszíni az Azure-bA az expressroute-tal

Az Azure Site Recovery lehetővé teszi a vészhelyreállítást és migrálás az Azure-bA a helyszíni [Hyper-V virtuális gépek](hyper-v-azure-architecture.md), [VMware virtuális gépek](vmware-azure-architecture.md), és [fizikai kiszolgálók](physical-azure-architecture.md). Az összes helyszíni – Azure forgatókönyvek esetén, a replikációs adatokat fogadja és egy Azure Storage-fiókban tárolt. A replikáció során a virtuális gép díjakat nem kell fizetnie. Ha feladatátvételt végez az Azure-ba, a Site Recovery automatikusan létrehozza az Azure IaaS virtuális gépeket.

A Site Recovery replikálja az adatokat az Azure Storage-fiók egy nyilvános végpontot keresztül. A Site Recovery replikációjára ExpressRoute használatához használhat [nyilvános társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#publicpeering) vagy [Microsoft társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Microsoft társviszony-létesítés a replikáció ajánlott útválasztási tartományhoz. Miután a virtuális gépek vagy kiszolgálók átadja a feladatokat az Azure virtuális hálózat, elérheti azokat használó [magánhálózati társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#privatepeering). Replikáció nem támogatott a privát társviszony-létesítésen keresztül.

A kombinált forgatókönyvhöz az alábbi ábrán jelölt: ![a – helyszíni – Azure ExpressRoute használatával](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Azure-bA replikációs az expressroute-tal

Az Azure Site Recovery lehetővé teszi, hogy a vész-helyreállítási [Azure-beli virtuális gépek](azure-to-azure-architecture.md). Attól függően, hogy az az Azure-beli virtuális gépek használata [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md), replikációs adatokat egy Azure Storage-fiók vagy a replika felügyelt lemez a cél Azure-régiókba küldött. Bár a replikációs végpontok nyilvános, az Azure virtuális gép replikációja, alapértelmezés szerint a replikációs forgalom sem halad át az interneten, függetlenül attól, amely az Azure-régióban a forrás virtuális hálózat szerepel. Az Azure alapértelmezett rendszer útvonalat a 0.0.0.0/0 címelőtag a felülbírálhatja egy [egyéni útválasztási](../virtual-network/virtual-networks-udr-overview.md#custom-routes) és átirányít a VM forgalmat egy a helyszíni hálózati virtuális berendezésre (NVA), de ez a konfiguráció nem ajánlott a Site Recovery a replikáció. Ha egyéni útvonalakat használ, akkor [hozzon létre egy virtuális hálózati szolgáltatásvégpont](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) a virtuális hálózatot "Tároló" úgy, hogy a replikálás forgalma nem hagyja el az Azure határain.

Az Azure-beli virtuális gépek vészhelyreállításához alapértelmezés szerint az ExpressRoute nem kötelező replikálásra. Miután a virtuális gépek átadja a feladatokat a cél Azure-régióban, elérheti azokat használó [magánhálózati társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#privatepeering).

Ha ExpressRoute kapcsolódni a helyi adatközpontból az Azure Virtual machines a forrásrégióban már használ, megtervezheti az újbóli bekapcsolását. a feladatátvételi célként megadott régióban az ExpressRoute-kapcsolat. Használhatja az ugyanahhoz az ExpressRoute-kapcsolatcsoporthoz a célrégióban, egy új virtuális hálózati kapcsolaton keresztül csatlakozni, vagy egy külön az ExpressRoute-kapcsolatcsoport és a vész-helyreállítási kapcsolat. A másik lehetséges forgatókönyv leírása olvasható [Itt](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute).

Az Azure virtual machines leírtaknak megfelelően földrajzi ugyanazon a fürtön belüli Azure-régiókba replikálhatja [Itt](../site-recovery/azure-to-azure-support-matrix.md#region-support). Ha a kiválasztott cél Azure-régió nem forrásaként ugyanazon geopolitikai régión belül, szüksége lehet ahhoz, hogy az ExpressRoute Premium. További részletekért ellenőrizze [ExpressRoute-helyek](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) és [ExpressRoute – díjszabás](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [ExpressRoute-Kapcsolatcsoportok](../expressroute/expressroute-circuit-peerings.md).
- Tudjon meg többet [ExpressRoute útválasztási tartományok](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Tudjon meg többet [ExpressRoute-helyek](../expressroute/expressroute-locations.md).
- További információ a vész-helyreállítási [Azure-beli virtuális gépek az expressroute-tal ](azure-vm-disaster-recovery-with-expressroute.md).
