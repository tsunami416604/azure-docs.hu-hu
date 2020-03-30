---
title: 'Azure ExpressRoute: Áramköri konfigurációs munkafolyamatok'
description: Ez a lap az ExpressRoute-kapcsolatés társviszony-létesítések konfigurálásának munkafolyamatait jeleníti meg
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864366"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Az ExpressRoute kapcsolatcsoport-kiépítési munkafolyamatai és a kapcsolatcsoportok állapotai
Ez a lap végigvezeti a szolgáltatáskiépítési és útválasztási konfigurációs munkafolyamatok magas szinten.

![áramköri munkafolyamat](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Az alábbi ábra és a megfelelő lépések ismertetik az ExpressRoute-kapcsolat végpontok között történő kiépítésének feladatait. 

1. ExpressRoute-kapcsolat konfigurálásával a PowerShell használatával konfigurálhat egy ExpressRoute-áramkört. További részletekért kövesse az [ExpressRoute-áramkörök létrehozása](expressroute-howto-circuit-classic.md) című cikk utasításait.
2. Rendeljen kapcsolatot a szolgáltatótól. Ez a folyamat változó. A kapcsolat rendelésével kapcsolatos további részletekért forduljon a kapcsolatszolgáltatóhoz.
3. Győződjön meg arról, hogy az áramkör kiépítése sikeresen megtörtént az ExpressRoute-kapcsolat létesítési állapotának a PowerShellen keresztül imasálásával. 
4. Útválasztási tartományok konfigurálása. Ha a kapcsolatszolgáltató kezeli a Layer 3 konfigurációját, akkor konfigurálják az útválasztást a kapcsolatcsoporthoz. Ha a kapcsolatszolgáltató csak Layer 2 szolgáltatásokat kínál, akkor az útválasztást az [útválasztási követelményekben](expressroute-routing.md) és az [útválasztási konfigurációs](expressroute-howto-routing-classic.md) lapokban leírt irányelvek szerint kell konfigurálnia.
   
   * Engedélyezze az Azure privát társviszony-létesítés – Ezt a társviszony-létesítést a virtuális hálózatokon üzembe helyezett virtuális gépekhez / felhőszolgáltatásokhoz való csatlakozásengedélyezéséhez.

   * A Microsoft-társviszony-létesítés engedélyezése – A Microsoft online szolgáltatásainak, például az Office 365-nek a eléréséhez ily ben engedélyezheti. Minden Azure PaaS-szolgáltatás elérhető a Microsoft társviszony-létesítés.
     
     > [!IMPORTANT]
     > Meg kell győződnie arról, hogy külön proxy / edge használatával csatlakozik a Microsofthoz, mint az internethez használt. Ha ugyanazt a periódust használja az ExpressRoute és az internet számára, az aszimmetrikus útválasztást és a hálózat kapcsolatkimaradását okozza.
     > 
     > 
     
     ![útválasztási munkafolyamatok](./media/expressroute-workflows/routing-workflow.png)
5. Virtuális hálózatok összekapcsolása ExpressRoute-áramkörökhöz – Virtuális hálózatokat kapcsolhat az ExpressRoute-kapcsolathoz. Kövesse az utasításokat [a virtuális hálózatok összekapcsolására](expressroute-howto-linkvnet-arm.md) az áramkörhöz. Ezek a virtuális hálózatok lehetnek ugyanabban az Azure-előfizetésben, mint az ExpressRoute-kapcsolat, vagy lehet egy másik előfizetésben.

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute-kör létesítési állapotai
Minden ExpressRoute-kapcsolat két állapotú:

* Szolgáltató létesítési állapota
* status

Az állapot a Microsoft kiépítési állapotát jelöli. Ez a tulajdonság engedélyezve van Expressroute-kapcsolat létrehozásakor

A kapcsolatszolgáltató létesítési állapota a kapcsolatszolgáltató oldalán lévő állapotot jelöli. Ez lehet *NotProvisioned*, *Provisioning*vagy *Provisioned*. Az ExpressRoute-kapcsolatcsoportnak kiépített állapotban kell lennie a társviszony-létesítéshez.

### <a name="possible-states-of-an-expressroute-circuit"></a>ExpressRoute-kapcsolat lehetséges állapotai
Ez a szakasz egy ExpressRoute-kapcsolat lehetséges állapotait sorolja fel.

**Létrehozáskor**

Az ExpressRoute-kapcsolatcsoport a következő állapotokat jelenti az erőforrás-létrehozáskor.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Ha a kapcsolatszolgáltató a kapcsolat létesítésének folyamatában van**

Az ExpressRoute-kapcsolatcsoport a következő állapotokat jelenti, miközben a kapcsolatszolgáltató a kapcsolatcsoport kiépítésén dolgozik.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Ha a kapcsolatszolgáltató befejezte a kiépítési folyamatot**

Az ExpressRoute-kapcsolatcsoport a következő állapotokat jelenti, miután a kapcsolatszolgáltató sikeresen kiépítette a kapcsolatot.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**Ha a kapcsolatszolgáltató megszünteti a kapcsolati kapcsolatot**

Ha az ExpressRoute-áramkört meg kell tenni, a kapcsolatcsoport a következő állapotokat jelenti, miután a szolgáltató befejezte a megszüntetési folyamatot.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Szükség esetén újra engedélyezheti, vagy futtathatja a PowerShell-parancsmagokat az áramkör törléséhez.  

> [!IMPORTANT]
> Egy kapcsolatcsoport nem törölhető, ha a ServiceProviderProvisioningState kiépítés vagy kiépítés. A kapcsolatszolgáltatónak törölnie kell a kapcsolatot a törlés előtt. A Microsoft továbbra is kiszámlázja a körkapcsolatot, amíg az ExpressRoute-kapcsolatáramkör-erőforrás törlődik az Azure-ban.
> 

## <a name="routing-session-configuration-state"></a>Útválasztási munkamenet konfigurációs állapota
A BGP-kiépítési állapot jelenti, ha a BGP-munkamenet engedélyezve van a Microsoft peremén. Az állapotot engedélyezni kell a privát vagy a Microsoft társviszony-létesítéséhez.

Fontos, hogy ellenőrizze a BGP munkamenet állapotát, különösen a Microsoft társviszony-létesítés. A BGP-kiépítési állapot mellett van egy másik állapot is, amelyet *hirdetett nyilvános előtagoknak hívnak.* A hirdetett nyilvános előtagok állapotának *konfigurált* állapotban kell lennie, mind a BGP-munkamenet működéséhez, mind az útválasztás hoz létre a végpontok közötti munkához. 

Ha a hirdetett nyilvános előtag állapota *érvényesítési szükséges* állapotra van állítva, a BGP-munkamenet nincs engedélyezve, mivel a hirdetett előtagok egyik útválasztási beállításjegyzékben sem feleltek meg az AS-számnak. 

> [!IMPORTANT]
> Ha a hirdetett nyilvános előtagok állapota *manuális érvényesítési* állapotban van, meg kell nyitnia egy támogatási jegyet a [Microsoft támogatásával,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) és igazolnia kell, hogy ön a meghirdetett IP-címek és a kapcsolódó autonóm rendszerszámmal együtt ön a tulajdonában.
> 
> 

## <a name="next-steps"></a>További lépések
* Az ExpressRoute-kapcsolat konfigurálása.
  
  * [ExpressRoute-kapcsolat létrehozása](expressroute-howto-circuit-arm.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-arm.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md)

