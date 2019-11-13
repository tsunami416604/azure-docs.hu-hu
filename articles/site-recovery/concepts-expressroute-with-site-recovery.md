---
title: Tudnivalók a ExpressRoute és a Azure Site Recovery használatáról
description: Ismerteti, hogyan használható az Azure ExpressRoute a Azure Site Recovery szolgáltatással a vész-helyreállítási és-áttelepítéshez.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: dcc5105fcf2ad7b6a9f0695b3086dc2956a76a50
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954072"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure-ExpressRoute Azure Site Recovery

A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute használatával kapcsolatokat létesíthet olyan Microsoft-felhőszolgáltatásokkal, mint például a Microsoft Azure, az Office 365 és a Dynamics 365.

Ez a cikk azt ismerteti, hogyan használható az Azure ExpressRoute a Azure Site Recovery a vész-helyreállítási és-áttelepítéshez.

## <a name="expressroute-circuits"></a>ExpressRoute-áramkörök

ExpressRoute-kapcsolatcsoport a helyszíni infrastruktúra és a egy kapcsolatszolgáltatón keresztül a Microsoft-felhőszolgáltatások közötti logikai kapcsolatot jelöli. Több ExpressRoute-Kapcsolatcsoportok rendezheti. Minden kapcsolatnak az azonos vagy eltérő régiókban is lehetnek, és a helyszíni eredetű különböző kapcsolatszolgáltatók keresztül lehet csatlakoztatni. További információ az ExpressRoute- [áramkörökről](../expressroute/expressroute-circuit-peerings.md).

Egy ExpressRoute-áramkörhöz több útválasztási tartomány van társítva. További információ az ExpressRoute [útválasztási tartományokról](../expressroute/expressroute-circuit-peerings.md#peeringcompare)és azok összehasonlításáról.

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Helyszíniről Azure-ba történő replikáció a ExpressRoute

Azure Site Recovery lehetővé teszi a vész-helyreállítást és az Azure-ba való áttelepítést helyszíni [Hyper-V virtuális gépek](hyper-v-azure-architecture.md), [VMWare virtuális gépek](vmware-azure-architecture.md)és [fizikai kiszolgálók](physical-azure-architecture.md)számára. A helyszíni és az Azure-beli forgatókönyvek esetében a replikációs adatok küldése és tárolása egy Azure Storage-fiókban történik. A replikáció során nem számítunk fel virtuális gépek díját. Amikor feladatátvételt futtat az Azure-ba, Site Recovery automatikusan létrehozza az Azure IaaS virtuális gépeket.

A Site Recovery egy nyilvános végponton keresztül replikálja az Azure Storage-fiókba vagy a replika felügyelt lemezére a cél Azure-régióban. Ha a ExpressRoute-t Site Recovery replikációs forgalomhoz kívánja használni, használhatja a [Microsoft-társat](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) vagy egy meglévő [nyilvános](../expressroute/expressroute-circuit-peerings.md#publicpeering) (új létrehozási) szolgáltatást. A replikációhoz a Microsoft-társ a javasolt útválasztási tartomány. Vegye figyelembe, hogy a replikálás nem támogatott privát társak esetén.

Győződjön meg arról, hogy a konfigurációs kiszolgáló [hálózati követelményei](vmware-azure-configuration-server-requirements.md#network-requirements) is teljesülnek. A konfigurációs kiszolgáló a Site Recovery replikáció összehangolása érdekében meghatározott URL-címekhez való kapcsolódást igényel. A ExpressRoute nem használható ehhez a kapcsolathoz. 

Ha a helyszíni proxyt használja, és a ExpressRoute-t szeretné használni a replikálási forgalomhoz, konfigurálnia kell a proxy-megkerülési listát a konfigurációs kiszolgálón és a kiszolgálókon. Kövesse az alábbi lépéseket:

- Töltse [le a PsExec eszközt innen a](https://aka.ms/PsExec) rendszer felhasználói környezetének eléréséhez.
- A következő parancssori PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe" futtatásával nyissa meg az Internet Explorert a rendszerfelhasználó környezetben.
- Proxybeállítások megadása az IE-ben
- A Mellőzés listából adja hozzá az Azure Storage URL *. blob.core.windows.net

Ez biztosítja, hogy csak a replikálási forgalom haladjon át a ExpressRoute, miközben a kommunikáció a proxyn keresztül történik.

Ha a virtuális gépek vagy a kiszolgálók feladatátvételét egy Azure-beli virtuális hálózatra hajtja végre, a [privát](../expressroute/expressroute-circuit-peerings.md#privatepeering)kapcsolaton keresztül érheti el azokat. 

A kombinált forgatókönyv a következő ábrán látható: ![helyszíni – Azure ExpressRoute-](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Azure-ról Azure-ba történő replikáció a ExpressRoute

Azure Site Recovery lehetővé teszi az Azure-beli [virtuális gépek](azure-to-azure-architecture.md)vész-helyreállítását. Attól függően, hogy az Azure-beli virtuális gépek használják-e az [azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md)-t, a rendszer a replikációs adatait egy Azure Storage-fiókba vagy egy, a cél Azure-régióban található replika felügyelt lemezre Bár a replikációs végpontok nyilvánosak, az Azure-beli virtuális gépek replikálásának replikációs forgalma alapértelmezés szerint nem halad át az interneten, függetlenül attól, hogy melyik Azure-régió létezik a forrás virtuális hálózatban. Az Azure alapértelmezett rendszerútvonalát felülbírálhatja a 0.0.0.0/0 címek előtagja számára egy [Egyéni útvonallal](../virtual-network/virtual-networks-udr-overview.md#custom-routes) , és átirányíthatja a virtuális gépek forgalmát egy helyszíni hálózati virtuális készülékre (NVA), de ez a konfiguráció nem ajánlott site Recovery replikáláshoz. Ha egyéni útvonalakat használ, [hozzon létre egy virtuális hálózati szolgáltatási végpontot](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) a "Storage" virtuális hálózatában, hogy a replikálási forgalom ne hagyja el az Azure-határt.

Az Azure-beli virtuális gépek vész-helyreállításához a ExpressRoute alapértelmezés szerint nem szükséges a replikáláshoz. Miután a virtuális gépek átvettek a feladatokat a cél Azure-régióba, hozzáférhetnek a [privát társak](../expressroute/expressroute-circuit-peerings.md#privatepeering)használatával. Vegye figyelembe, hogy az adatátviteli díjak az Azure-régiók közötti adatreplikálás módjától függetlenül érvényesek.

Ha már használja a ExpressRoute-t a helyszíni adatközpontból az Azure-beli virtuális gépekhez való csatlakozáshoz a forrásoldali régióban, megtervezheti a ExpressRoute-kapcsolat újbóli létrehozását a feladatátvételi célként megadott régióban. Ugyanezzel a ExpressRoute áramkörrel csatlakozhat a célként megadott régióhoz egy új virtuális hálózati kapcsolaton keresztül, vagy egy külön ExpressRoute áramkört és a vész-helyreállítási kapcsolatot is használhatja. A különböző lehetséges forgatókönyvek leírását [itt](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute)találja.

Az Azure-beli virtuális gépeket az [itt](../site-recovery/azure-to-azure-support-matrix.md#region-support)leírtak szerint replikálhatja az adott földrajzi fürtön belüli bármely Azure-régióba. Ha a kiválasztott cél Azure-régió nem a forráshoz tartozó geopolitikai régióban található, akkor előfordulhat, hogy engedélyeznie kell a ExpressRoute Premiumot. További részletekért lásd a [ExpressRoute helyét](../expressroute/expressroute-locations.md) és a [ExpressRoute díjszabását](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Következő lépések
- További információ az [ExpressRoute-áramkörökről](../expressroute/expressroute-circuit-peerings.md).
- További információ az [ExpressRoute-útválasztási tartományokról](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- További információ a [ExpressRoute helyeiről](../expressroute/expressroute-locations.md).
- További információ az Azure-beli [virtuális gépek ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md)-mel való vész-helyreállításáról.
