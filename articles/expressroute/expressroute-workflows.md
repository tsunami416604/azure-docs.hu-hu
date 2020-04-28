---
title: 'Azure ExpressRoute: áramkör-konfigurációs munkafolyamatok'
description: Ezen a lapon láthatók a ExpressRoute-áramkör és-társítások konfigurálásának munkafolyamatai
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75864366"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Az ExpressRoute kapcsolatcsoport-kiépítési munkafolyamatai és a kapcsolatcsoportok állapotai
Ez az oldal végigvezeti a szolgáltatás kiépítési és útválasztási konfigurációs munkafolyamatainak magas szinten való használatával.

![áramköri munkafolyamat](./media/expressroute-workflows/expressroute-circuit-workflow.png)

A következő ábra és a megfelelő lépések körvonalazzák azokat a feladatokat, amelyek a ExpressRoute áramkör teljes körű kiépítéséhez szükségesek. 

1. ExpressRoute-áramkör konfigurálása a PowerShell használatával. További részletekért kövesse a [ExpressRoute-áramkörök létrehozása](expressroute-howto-circuit-classic.md) című cikk utasításait.
2. Rendeljen kapcsolatot a szolgáltatótól. Ez a folyamat változó. A kapcsolat megrendelésével kapcsolatos további információkért forduljon a kapcsolati szolgáltatóhoz.
3. Győződjön meg arról, hogy az áramkör üzembe helyezése sikeresen megtörtént az ExpressRoute áramkör kiépítési állapotának PowerShell használatával történő ellenőrzésével. 
4. Útválasztási tartományok konfigurálása. Ha a kapcsolat szolgáltatója a 3. réteg konfigurációját kezeli, akkor a rendszer az áramkör útválasztási beállításait konfigurálja. Ha a kapcsolati szolgáltató csak a 2. rétegbeli szolgáltatásokat nyújtja, az útválasztási [követelmények](expressroute-routing.md) és az [útválasztási konfigurációs](expressroute-howto-routing-classic.md) lapok című cikkben ismertetett irányelvek szerint kell konfigurálnia az útválasztást.
   
   * Az Azure privát társának engedélyezése – engedélyezze ezt a társat a virtuális hálózatokon belül üzembe helyezett virtuális gépekhez/felhőalapú szolgáltatásokhoz való kapcsolódáshoz.

   * Microsoft-társak engedélyezése – engedélyezze ezt a Microsoft online szolgáltatások, például az Office 365 eléréséhez. Az összes Azure-beli Pásti-szolgáltatás a Microsoft-partneri hozzáférésen keresztül érhető el.
     
     > [!IMPORTANT]
     > Gondoskodnia kell arról, hogy a Microsofthoz való csatlakozáshoz külön proxyt vagy Edge-t használjon, mint amelyet az internethez használ. Ha a ExpressRoute és az internet között ugyanazt a szegélyt használja, az aszimmetrikus útválasztást okoz, és a hálózati kapcsolat kimaradást okoz.
     > 
     > 
     
     ![útválasztási munkafolyamatok](./media/expressroute-workflows/routing-workflow.png)
5. Virtuális hálózatok összekapcsolása a ExpressRoute-áramkörökkel – a virtuális hálózatokat összekapcsolhatja a ExpressRoute-áramkörrel. Kövesse az utasításokat a virtuális hálózatok az áramkörhöz való [kapcsolásához](expressroute-howto-linkvnet-arm.md) . Ezek a virtuális hálózatok lehetnek ugyanabban az Azure-előfizetésben, mint a ExpressRoute áramkör, vagy egy másik előfizetésben is lehetnek.

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute áramkör kiépítési állapota
Minden ExpressRoute-áramkör két állapottal rendelkezik:

* Szolgáltató kiépítési állapota
* status

Az állapot a Microsoft kiépítési állapotát jelöli. Ez a tulajdonság úgy van beállítva, hogy az Expressroute-kör létrehozásakor engedélyezve legyen

A kapcsolati szolgáltató kiépítési állapota a kapcsolati szolgáltató oldalán lévő állapotot jelöli. Ez lehet *NotProvisioned*, *kiépítés*vagy *üzembe*helyezés. A ExpressRoute áramkörnek kiépített állapotban kell lennie a társítás konfigurálásához.

### <a name="possible-states-of-an-expressroute-circuit"></a>Egy ExpressRoute áramkör lehetséges állapotai
Ez a szakasz a ExpressRoute áramkör lehetséges állapotait sorolja fel.

**Létrehozáskor**

Az ExpressRoute áramkör a következő állapotokat fogja jelenteni az erőforrások létrehozásakor.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Ha a kapcsolati szolgáltató az áramkör kiépítési folyamatában van**

A ExpressRoute áramkör a következő állapotokat fogja jelenteni, miközben a kapcsolati szolgáltató dolgozik az áramkör kiépítésekor.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Ha a kapcsolati szolgáltató befejezte a létesítési folyamatot**

A ExpressRoute áramkör a következő állapotokat fogja jelenteni, amint a kapcsolati szolgáltató sikeresen kiépítte az áramkört.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**Ha a kapcsolat szolgáltatója kiépíti az áramkört**

Ha a ExpressRoute áramkört el kell helyezni, az áramkör a következő állapotokat fogja jelenteni, amint a szolgáltató befejezte a megszüntetési folyamatot.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Igény szerint újra engedélyezheti, vagy PowerShell-parancsmagok futtatásával törölheti az áramkört.  

> [!IMPORTANT]
> Az áramkör nem törölhető, ha a ServiceProviderProvisioningState kiépítés vagy üzembe helyezés történik. A kapcsolat szolgáltatójának le kell vonnia az áramkört, mielőtt törölni lehetne. A Microsoft továbbra is számlázza az áramkört, amíg az Azure-ban nem törli az ExpressRoute Circuit-erőforrást.
> 

## <a name="routing-session-configuration-state"></a>Útválasztási munkamenet konfigurációjának állapota
A BGP-kiépítési állapot azt jelenti, hogy a BGP-munkamenet engedélyezve van-e a Microsoft Edge-ben. Az állapotot engedélyezni kell a privát vagy a Microsoft-partnerek használatához.

Fontos, hogy a BGP-munkamenet állapota különösen a Microsoft-társak számára legyen ellenőrizhető. A BGP kiépítési állapotán kívül egy másik, *meghirdetett nyilvános előtagok*nevű állam is van. A meghirdetett nyilvános előtagok állapotának *konfigurált* állapotban kell lennie, mind a BGP-munkamenethez, mind az Útválasztás teljes körű működéséhez. 

Ha a meghirdetett nyilvános előtag állapota *érvényesítés szükséges* állapotra van állítva, a BGP-munkamenet nincs engedélyezve, mivel a meghirdetett előtagok nem feleltek meg az as-számnak az útválasztási kibocsátásiegység-forgalmi jegyzékekben. 

> [!IMPORTANT]
> Ha a meghirdetett nyilvános előtagok *kézi ellenőrzési* állapotban vannak, meg kell nyitnia egy támogatási jegyet a [Microsoft támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) szolgálatával, és bizonyítania kell, hogy Ön rendelkezik a meghirdetett IP-címekkel a társított autonóm rendszerszámmal együtt.
> 
> 

## <a name="next-steps"></a>További lépések
* Az ExpressRoute-kapcsolat konfigurálása.
  
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-arm.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md)

