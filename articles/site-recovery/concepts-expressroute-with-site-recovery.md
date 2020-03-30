---
title: Az ExpressRoute és az Azure Site Recovery szolgáltatással való használata
description: Az Azure ExpressRoute használata az Azure Site Recovery szolgáltatással a vészhelyreállítás hoz és az áttelepítéshez.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: e4525bdc6165e8e736db5f539c764d25250cb248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258004"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute az Azure Site Recovery szolgáltatással

A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute használatával kapcsolatokat létesíthet olyan Microsoft-felhőszolgáltatásokkal, mint például a Microsoft Azure, az Office 365 és a Dynamics 365.

Ez a cikk bemutatja, hogyan használhatja az Azure ExpressRoute-ot az Azure Site Recovery szolgáltatással a vészhelyreállításhoz és az áttelepítéshez.

## <a name="expressroute-circuits"></a>ExpressRoute-kapcsolatcsoportok

Az ExpressRoute-áramkör a helyszíni infrastruktúra és a Microsoft felhőszolgáltatásai közötti logikai kapcsolatot jelöli egy kapcsolatszolgáltatón keresztül. Több ExpressRoute-áramkört is rendelhet. Minden kapcsolatcsoport lehet ugyanabban a régióban, és különböző kapcsolatszolgáltatókon keresztül csatlakoztatható a helyiséghez. Az ExpressRoute-áramkörökről itt olvashat [bővebben.](../expressroute/expressroute-circuit-peerings.md)

Egy ExpressRoute-kapcsolathoz több útválasztási tartomány van társítva. Az ExpressRoute útválasztási tartományokról itt olvashat bővebben, és hogyan hasonlíthatja össze [azokat.](../expressroute/expressroute-circuit-peerings.md#peeringcompare)

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Helyszíni azure-replikáció az ExpressRoute-on

Az Azure Site Recovery lehetővé teszi a vészhelyreállítást és az Azure-ba való áttelepítést a helyszíni [Hyper-V virtuális gépek](hyper-v-azure-architecture.md), [VMware virtuális gépek](vmware-azure-architecture.md)és fizikai [kiszolgálók](physical-azure-architecture.md)számára. Az Azure-forgatókönyvek minden helyszíni azure-forgatókönyvek replikációs adatok at küldi el, és egy Azure Storage-fiókban tárolja. A replikáció során nem kell fizetnie a virtuális gépek nek. Amikor feladatátvételt futtat az Azure-ba, a Site Recovery automatikusan létrehozza az Azure IaaS virtuális gépeket.

A Site Recovery replikálja az adatokat egy Azure Storage-fiókba vagy replikájának felügyelt lemezére a cél Azure-régióban egy nyilvános végponton keresztül. Az ExpressRoute webhely-helyreállítási replikációs forgalomhoz való használatához használhatja a [Microsoft társviszony-létesítést](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) vagy egy meglévő [nyilvános társviszony-létesítést](../expressroute/about-public-peering.md) (új létrehozások esetén elavult). A Microsoft társviszony-létesítésa a replikáció ajánlott útválasztási tartománya. Vegye figyelembe, hogy a privát társviszony-létesítés nem támogatja a replikációt.

Győződjön meg arról, hogy a [konfigurációs](vmware-azure-configuration-server-requirements.md#network-requirements) kiszolgáló hálózati követelményei is teljesülnek. A Configuration Server nek szüksége van az adott URL-címekhez való kapcsolódásra a Site Recovery replikációvoniójának vezényléséhez. Az ExpressRoute nem használható ehhez a kapcsolathoz. 

Abban az esetben, ha a helyszíni proxyt használja, és az ExpressRoute-ot szeretné használni a replikációs forgalomhoz, konfigurálnia kell a proxymegkerülési listát a konfigurációs kiszolgálón és a folyamatkiszolgálókon. Kövesse az alábbi lépéseket:

- Töltse le psexec eszköz [innen](https://aka.ms/PsExec) eléréséhez Rendszer felhasználói környezetben.
- Az Internet Explorer megnyitása rendszerfelhasználói környezetben a következő psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe" parancssor futtatásával
- Proxybeállítások hozzáadása az IE-ben
- A megkerülési listában adja hozzá az Azure storage URL-címét *.blob.core.windows.net

Ez biztosítja, hogy csak a replikációs forgalom áramlik az ExpressRoute-on keresztül, míg a kommunikáció proxyn keresztül.

Miután a virtuális gépek vagy kiszolgálók átjutnak egy Azure virtuális hálózatra, [privát társviszony-létesítéssel érheti](../expressroute/expressroute-circuit-peerings.md#privatepeering)el őket. 

A kombinált forgatókönyv a következő ![ábrán jelenik meg: Helyszíni Azure-beli expressroute-tal](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Azure-replikáció az ExpressRoute-on

Az Azure Site Recovery lehetővé teszi az [Azure virtuális gépek](azure-to-azure-architecture.md)vészhelyreállítását. Attól függően, hogy az Azure virtuális [gépei használják-e az Azure felügyelt lemezeket,](../virtual-machines/windows/managed-disks-overview.md)a replikációs adatokat egy Azure Storage-fiókba vagy a cél Azure-régióban kezelt lemezreplikába küldi a rendszer. Bár a replikációs végpontok nyilvánosak, az Azure VM replikációjának replikációs forgalma alapértelmezés szerint nem halad át az interneten, függetlenül attól, hogy a forrás virtuális hálózat melyik Azure-régióban létezik. Felülbírálhatja az Azure alapértelmezett rendszerútvonalát a 0.0.0.0/0-s címelőtaghoz [egyéni útvonallal,](../virtual-network/virtual-networks-udr-overview.md#custom-routes) és átirányíthatja a virtuális gép forgalmát egy helyszíni hálózati virtuális berendezésre (NVA), de ez a konfiguráció nem ajánlott a Site Recovery replikációjához. Ha egyéni útvonalakat használ, [hozzon létre egy virtuális hálózati szolgáltatás végpontot](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) a virtuális hálózatban a "Storage" számára, hogy a replikációs forgalom ne hagyja el az Azure-határt.

Az Azure VM vész-helyreállítási, alapértelmezés szerint ExpressRoute nem szükséges a replikációhoz. Miután a virtuális gépek feladatátvételt a cél Azure-régióban, elérheti őket [a privát társviszony-létesítés.](../expressroute/expressroute-circuit-peerings.md#privatepeering) Vegye figyelembe, hogy az adatátviteli díjak az Azure-régiók közötti adatreplikáció módjától függetlenül érvényesek.

Ha már használja az ExpressRoute-ot a helyszíni adatközpontból a forrásrégióban lévő Azure virtuális gépekhez való csatlakozáshoz, megtervezheti az ExpressRoute-kapcsolat újbóli létrehozását a feladatátvételi célrégióban. Használhatja ugyanazt az ExpressRoute-áramkört a célrégióhoz való csatlakozáshoz egy új virtuális hálózati kapcsolaton keresztül, vagy használhat egy külön ExpressRoute-áramkört és kapcsolatot a vészhelyreállításhoz. A különböző lehetséges forgatókönyveket [itt](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute)ismerteti .

Az Azure virtuális gépekreplikálhatók bármely Azure-régióban ugyanazon a földrajzi fürtön belül, mint [itt](../site-recovery/azure-to-azure-support-matrix.md#region-support)részletezett. Ha a kiválasztott cél Azure-régió nem ugyanabban a geopolitikai régióban, mint a forrás, szükség lehet az ExpressRoute Premium engedélyezéséhez. További részletekért ellenőrizze [az ExpressRoute-helyeket](../expressroute/expressroute-locations.md) és [az ExpressRoute-díjszabást.](https://azure.microsoft.com/pricing/details/expressroute/)

## <a name="next-steps"></a>További lépések
- További információ az [ExpressRoute-áramkörökről.](../expressroute/expressroute-circuit-peerings.md)
- További információ az [ExpressRoute útválasztási tartományokról.](../expressroute/expressroute-circuit-peerings.md#peeringcompare)
- További információ az [ExpressRoute-helyekről.](../expressroute/expressroute-locations.md)
- További információ az [Azure virtuális gépek vészutáni](azure-vm-disaster-recovery-with-expressroute.md)helyreállításáról az ExpressRoute segítségével.
