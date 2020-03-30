---
title: Nyilvános IP-címek hozzárendelése feladatátvétel után az Azure Site Recovery szolgáltatással
description: Ez a témakör azt ismerteti, hogy miként állíthatók be nyilvános IP-címek az Azure Site Recovery és az Azure Traffic Manager segítségével a vészhelyreállításhoz és az áttelepítéshez
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: b1f3ffa6fc90fc0cab0217d1b71907342f2dbd0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281950"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Nyilvános IP-címek beállítása feladatátvétel után

A nyilvános IP-címek lehetővé teszik az internetes erőforrások bejövő kommunikációját az Azure-erőforrásokkal. A nyilvános IP-címek emellett lehetővé teszik az Azure-erőforrások kimenő kommunikációját az internettel és a nyilvános Azure-szolgáltatásokkal, az erőforráshoz rendelt IP-címet használva.
- Bejövő kommunikáció az internetről az erőforrás, például az Azure virtuális gépek (VM), az Azure Application Gateways, az Azure Load Balancers, az Azure VPN-átjárók és mások. Továbbra is kommunikálhat bizonyos erőforrásokkal, például a virtuális gépekkel az internetről, ha egy virtuális gép nem rendelkezik hozzá rendelt nyilvános IP-címmel, feltéve, hogy a virtuális gép egy terheléselosztó háttérkészlet része, és a terheléselosztó nyilvános IP-címet kap.
- Kimenő internetkapcsolat kiszámítható IP-címmel. Például egy virtuális gép kommunikálhat az internetre anélkül, hogy egy nyilvános IP-címet rendelt hozzá, de a cím hálózati cím az Azure által lefordított egy előre nem látható nyilvános címet, alapértelmezés szerint. Nyilvános IP-cím hozzárendelése egy erőforráshoz lehetővé teszi annak ismeretét, hogy melyik IP-címet használja a kimenő kapcsolathoz. Bár kiszámítható, a cím változhat, attól függően, hogy a hozzárendelési módszer választott. További információt a [Nyilvános IP-cím létrehozása](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)című témakörben talál. Ha többet szeretne tudni az Azure-erőforrásokból érkező kimenő kapcsolatokról, [olvassa el a Kimenő kapcsolatok ismertetése.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Az Azure Resource Managerben a nyilvános IP-cím olyan erőforrás, amely saját tulajdonságokkal rendelkezik. A nyilvános IP-cím-erőforrást például a következő erőforrásokhoz rendelheti hozzá:

* Virtuális gépek hálózati adapterei
* Internetkapcsolattal rendelkező terheléselosztók
* VPN-átjárók
* Alkalmazásátjárók

Ez a cikk azt ismerteti, hogyan használhatja a nyilvános IP-címeket a Site Recovery segítségével.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Nyilvános IP-cím hozzárendelése helyreállítási terv vel

Az éles alkalmazás nyilvános IP-címe **nem tartható meg feladatátvételkor**. A feladatátvételi folyamat részeként létrehozott számítási feladatokhoz hozzá kell rendelni egy Azure nyilvános IP-erőforrást, amely elérhető a célrégióban. Ez a lépés manuálisan vagy automatikusan elvégezhető a helyreállítási tervekkel. A helyreállítási terv helyreállítási csoportokba gyűjti a gépeket. Segít a rendszeres helyreállítási folyamat meghatározásában. A helyreállítási terv használatával rendelést szabhat ki, és automatizálhatja az egyes lépésekhez szükséges műveleteket az Azure Automation runbookok használatával az Azure-ba vagy parancsfájlok ba történő feladatátvételhez.

A beállítás a következő:
- Hozzon létre egy [helyreállítási tervet,](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) és szükség szerint csoportosítsa a munkaterheléseket a tervbe.
- A csomag testreszabása egy lépés sel csatolja a nyilvános IP-címet az [Azure Automation runbook-parancsfájlok](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) használatával a feladatátvételi virtuális gép.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Nyilvános végpontváltás DNS-szintű útválasztással

Az Azure Traffic Manager lehetővé teszi a DNS-szintű útválasztás végpontok között, és segíthet [a dr-forgatókönyv rso-k levezetésében.](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) 

További információ a Traffic Manager feladatátvételi forgatókönyveiről:
1. [Helyszíni azure-feladatátvétel a](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) Traffic Managerrel 
2. [Azure-ból Azure-ba feladatátvétel](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) a Traffic Managerrel 

A beállítás a következő:
- Traffic [Manager-profil](../traffic-manager/traffic-manager-create-profile.md)létrehozása .
- A **prioritási** útválasztási módszer használatával hozzon létre két végpontot – **elsődleges** a forráshoz és az Azure **feladatátvételéhez.** **Az elsődleges** prioritás 1-es, **a feladatátvétel** pedig a 2-es prioritás.
- Az elsődleges végpont lehet [Az Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) vagy a Külső attól függően, hogy a forráskörnyezet az Azure-on belül vagy kívül van.The **Primary** endpoint can be Azure or [External](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) depending on whether your source environment is inside or outside Azure.
- A **feladatátvételi** végpont **azure-végpontként** jön létre. Használjon **statikus nyilvános IP-címet,** mivel ez lesz a külső néző végpont traffic manager a katasztrófa esemény.

## <a name="next-steps"></a>További lépések
- További információ a Traffic Manager ről [az Azure Site Recovery segítségével](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- További információ a Traffic Manager [útválasztási módszereiről.](../traffic-manager/traffic-manager-routing-methods.md)
- További információ az alkalmazásfeladat-átvétel automatizálását célzó [helyreállítási tervekről.](site-recovery-create-recovery-plans.md)
