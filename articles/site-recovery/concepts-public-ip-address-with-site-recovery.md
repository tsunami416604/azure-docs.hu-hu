---
title: Az Azure Site Recovery a feladatátvételt követően nyilvános IP-címek használata |} A Microsoft Docs
description: Ismerteti, hogyan állíthat be vészhelyreállítást és az áttelepítés az Azure Site Recovery és az Azure Traffic Manager nyilvános IP-címek
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 1f20818f0b899eede9fff05d71e98c8bffb94b0a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280128"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>A feladatátvételt követően nyilvános IP-címek beállítása

A nyilvános IP-címek lehetővé teszik az internetes erőforrások bejövő kommunikációját az Azure-erőforrásokkal. A nyilvános IP-címek emellett lehetővé teszik az Azure-erőforrások kimenő kommunikációját az internettel és a nyilvános Azure-szolgáltatásokkal, az erőforráshoz rendelt IP-címet használva.
- Az erőforrás, például az Azure Virtual Machines (VM), az Azure Application Gateway átjárók, az Azure Load Balancer Terheléselosztók, Azure VPN Gateway és mások az internetről bejövő kommunikációt. Továbbra is kommunikálhat egyes erőforrásokat, például a virtuális gépek, az internetről, ha a virtuális gép nem rendelkezik egy nyilvános IP-cím rendelhető, mindaddig, amíg a virtuális gép része egy load balancer háttérkészlethez, és a terheléselosztó nyilvános IP-cím van hozzárendelve.
- Kimenő kapcsolódás az internethez, kiszámítható IP-címet használ. Például a virtuális gépek kommunikálhatnak az interneten egy nyilvános IP-cím nélkül kimenő rendelve, de a cím alapértelmezés szerint egy előre nem látható, nyilvános cím, az Azure által lefordított hálózati cím. Nyilvános IP-cím hozzárendelése egy erőforrás lehetővé teszi, hogy tudja, melyik IP-címet használja a kimenő kapcsolat. Előre jelezhető, bár a cím változhat, a választott hozzárendelési módszertől függően. További információkért lásd: [hozzon létre egy nyilvános IP-cím](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Az Azure-erőforrások kimenő kapcsolatok kapcsolatos további információkért lásd: [kimenő kapcsolatainak ismertetése](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Az Azure Resource Manager, egy nyilvános IP-cím egy erőforrás, amely saját tulajdonságokkal rendelkezik. A nyilvános IP-cím-erőforrást például a következő erőforrásokhoz rendelheti hozzá:

* Virtuális gépek hálózati adapterei
* Internetkapcsolattal rendelkező terheléselosztók
* VPN-átjárók
* Alkalmazásátjárók

Ez a cikk bemutatja, hogyan használható nyilvános IP-címek a Site Recoveryvel.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Nyilvános IP-cím hozzárendelése a helyreállítási terv használatával

Az éles alkalmazás nyilvános IP-cím **nem őrzi meg a feladatátvételi**. A számítási feladatok kerülnek sorra, a feladatátvétel során hozzá kell rendelni a célrégióban elérhető az Azure nyilvános IP-erőforrás. Ebben a lépésben történhet manuálisan vagy a helyreállítási tervek automatikusan végbemegy. A helyreállítási terv összegyűjti a gépek helyreállítási csoportokba. Segít, hogy meghatározza a rendszeres a helyreállítási folyamatot. A helyreállítási terv használatával írnak elő a sorrend, és automatizálhatja az egyes lépéseknél szükséges műveleteket a Azure-ban, vagy parancsfájlok feladatátvétel az Azure Automation-runbookok használatával.

A telepítő a következőképpen történik:
- Hozzon létre egy [helyreállítási terv](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) és csoportosíthatja a számítási feladatokat szükség szerint a tervbe.
- A terv testreszabása egy lépés egy nyilvános IP cím segítségével történő hozzáadásával [Azure Automation-runbookok](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) szkripteket a feladatátvételen átesett virtuális gép.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Nyilvános végpont-felcserélés a DNS-szintű Útválasztás

Az Azure Traffic Manager lehetővé teszi, hogy a DNS-szintű, végpontok közötti útválasztást és segítségére lehetnek [le az RTO-k vezetési](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) egy Vészhelyreállítási forgatókönyvhöz. 

További információ a Traffic Manager feladatátvételi funkciók:
1. [A helyszíni Azure feladatátvétel](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) a Traffic Managerrel 
2. [Azure-bA feladatátvételi](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) a Traffic Managerrel 

A telepítő a következőképpen történik:
- Hozzon létre egy [Traffic Manager-profil](../traffic-manager/traffic-manager-create-profile.md).
- Használatával a **prioritás** útválasztási módszer, hozzon létre két végpontot – **elsődleges** a forrás és a **feladatátvételi** az Azure-hoz. **Elsődleges** hozzá van rendelve a prioritása 1 és **feladatátvételi** prioritású 2 van hozzárendelve.
- A **elsődleges** végpont lehet [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) vagy [külső](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) attól függően, hogy a forrás-környezet Azure-on kívülről vagy belülről.
- A **feladatátvételi** végpont jön létre egy **Azure** végpont. Használja a **statikus nyilvános IP-cím** , ez lesz a vész-helyreállítási esemény a Traffic Manager esetében elérhető végponton külső.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Traffic Manager az Azure Site Recoveryvel](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- További tudnivalók a Traffic Manager [útválasztási metódusait](../traffic-manager/traffic-manager-routing-methods.md).
- Tudjon meg többet [helyreállítási tervek](site-recovery-create-recovery-plans.md) alkalmazás a feladatátvétel automatizálásához.
