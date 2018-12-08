---
title: 'Konfiguráció-munkafolyamatok – az ExpressRoute kapcsolatcsoport-: Azure |} A Microsoft Docs'
description: Ezen a lapon látható a munkafolyamatok a konfigurálása ExpressRoute-kapcsolatcsoport és a társviszony
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 3ffcc5ac2193e607573ceb93717258f5349d1f15
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101687"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Az ExpressRoute kapcsolatcsoport-kiépítési munkafolyamatai és a kapcsolatcsoportok állapotai
Ezen a lapon végigvezeti a szolgáltatás kiépítése, és magas szintű konfigurációs munkafolyamatok útválasztást.

![kapcsolatcsoport munkafolyamat](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Az alábbi ábra és a megfelelő lépéseket kell követnie ahhoz, hogy rendelkezik ExpressRoute-kapcsolatcsoport kiosztott feladatokat jeleníti teljes körű. 

1. ExpressRoute-kapcsolatcsoport konfigurálása a PowerShell használatával. Kövesse az utasításokat a [létrehozása az ExpressRoute-Kapcsolatcsoportok](expressroute-howto-circuit-classic.md) további részleteivel.
2. A szolgáltató rendelés kapcsolat. Ez a folyamat függ. Kapcsolat order kapcsolatos további részletekért forduljon a kapcsolatszolgáltató.
3. Győződjön meg arról, hogy a kapcsolatcsoport lett kiépítése sikeresen megtörtént az ExpressRoute-kapcsolatcsoport kiépítési állapota a PowerShell-lel ellenőrzésével. 
4. Útválasztási tartományok konfigurálása. Ha a kapcsolatszolgáltató felügyeli a 3. rétegbeli az Ön számára, a vállalat konfigurálja a kapcsolatcsoport útválasztásának. Ha a kapcsolatszolgáltató csak a 2. rétegbeli szolgáltatásokat kínál, konfigurálnia kell útválasztás / ismertetett irányelvek a [útválasztási követelmények](expressroute-routing.md) és [útválasztási konfigurációja](expressroute-howto-routing-classic.md) oldalakat.
   
   * Engedélyezze az Azure-beli privát társviszony - engedélyezése a társviszony a virtuális gépekhez csatlakozhat / cloud services üzembe helyezett virtuális hálózatokon belül.

   * Engedélyezze a Microsoft társviszony-létesítést – engedélyezze ezt a hozzáférést az Office 365 és Dynamics 365. Ezenkívül az összes Azure PaaS-szolgáltatások érhetők el a Microsoft társviszony-létesítésen keresztül.
     
     > [!IMPORTANT]
     > Gondoskodnia kell arról, hogy külön proxy használatát választja, / csatlakozni a Microsoft fut, a peremhálózati használata az internethez. Az azonos edge használatával az ExpressRoute- és az Internet az aszimmetrikus útválasztás okozhat, és a hálózat kapcsolódási kimaradások miatt.
     > 
     > 
     
     ![útválasztási munkafolyamatok](./media/expressroute-workflows/routing-workflow.png)
5. Virtuális hálózatok összekapcsolása az ExpressRoute-Kapcsolatcsoportok - kapcsolat virtuális hálózatokat az ExpressRoute-kapcsolatcsoportot. Kövesse az utasításokat [összekapcsolása virtuális hálózatok](expressroute-howto-linkvnet-arm.md) a kapcsolatcsoporthoz. Ezek a virtuális hálózatok csak az Azure-előfizetéshez, az ExpressRoute-kapcsolatcsoport a, vagy egy másik előfizetésben is lehet.

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute-kapcsolatcsoport kiépítési állapotok
Egyes ExpressRoute-kapcsolatcsoport két állapota van:

* Service provider kiépítési állapota
* status

Állapot a Microsoft kiépítési állapotát jelöli. Ez a tulajdonság engedélyezve van beállítva, amikor Expressroute-kapcsolatcsoport létrehozása

A kapcsolati szolgáltató kiépítési állapota a kapcsolatszolgáltató oldalán állapotát jelöli. Ez lehet *NotProvisioned*, *kiépítési*, vagy *kiépített*. Az ExpressRoute-kapcsolatcsoport kell tudni használni azt kiépített állapotban kell lennie.

### <a name="possible-states-of-an-expressroute-circuit"></a>Lehetséges, az ExpressRoute-kapcsolatcsoport állapota
Ez a szakasz ismerteti a lehetséges állapotok ExpressRoute-kapcsolatcsoport ki.

**Létrehozáskor**

Az ExpressRoute-kapcsolatcsoport a következő állapotban megjelenik, amint az ExpressRoute-kapcsolatcsoport létrehozása a PowerShell-parancsmag futtatásakor.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Ha a kapcsolatszolgáltató a a kapcsolatcsoport kiépítési folyamatban van**

Az ExpressRoute-kapcsolatcsoport a következő állapotban megjelenik, amint a kapcsolatszolgáltató átadhatja a kulcsot, és azok indította el a kiépítési folyamat.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Ha a kapcsolatszolgáltató a kiépítési folyamat befejeződött**

Az ExpressRoute-kapcsolatcsoport a következő állapotban megjelenik, amint a kapcsolatszolgáltató a kiépítési folyamat befejeződött.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Üzembe helyezett, és engedélyezve az állapotban az áramköri kell tudni használni azt lehet. Ha egy 2. rétegbeli szolgáltatót használ, konfigurálhatja a kapcsolatcsoport útválasztásának csak akkor, amikor az ebben az állapotban van.

**Amikor kapcsolatszolgáltató megszüntet a kapcsolatcsoportot**

A kért a szolgáltatót az ExpressRoute-kapcsolatcsoport megszüntetése, ha megjelenik a kapcsolatcsoportot, állítsa be a következő állapotot, a szolgáltató a megszüntetési folyamat befejezése után.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Ha szeretné újra engedélyezni, ha szükséges, vagy törölje a kapcsolatcsoportot, PowerShell-parancsmagok futtatásához.  

> [!IMPORTANT]
> Ha futtatja a PowerShell-parancsmag törli a kapcsolatcsoport a ServiceProviderProvisioningState kiépítésekor vagy a művelet sikertelen lesz kiépítve. Először megszünteti az ExpressRoute-kapcsolatcsoportot kapcsolatszolgáltatójával együttműködve, és ezután törölje a kapcsolatcsoportot. A Microsoft számlázási a kapcsolatcsoportot, amíg nem futtat a PowerShell-parancsmag használatával törölje a kapcsolatcsoportot, továbbra is.
> 
> 

## <a name="routing-session-configuration-state"></a>Útválasztási konfiguráció munkamenet-állapot
A BGP-t üzembe helyezési állapota jelzi, ha a BGP-munkamenet engedélyezve van a Microsoft Edge-ben. Az állapot, hogy a társviszony-létesítés használni tudják engedélyezni kell.

Fontos, különösen a Microsoft társviszony-létesítés BGP munkamenet-állapot ellenőrzéséhez. A BGP-t üzembe helyezési állapota, valamint van egy másik állapothoz nevű *meghirdetett nyilvános előtagok állapot*. A meghirdetett nyilvános előtagok állapotban kell lennie a *konfigurált* állapot, mind a BGP-munkamenetben, akár és az Útválasztás teljes körű működéséhez. 

Ha a meghirdetett nyilvános előtag állapot értéke egy *ellenőrzés szükséges* állapot, a BGP-munkamenet nincs engedélyezve, ahogy a meghirdetett előtagok nem egyezik meg az AS-számot sem az útválasztási beállításjegyzékek. 

> [!IMPORTANT]
> Ha a meghirdetett nyilvános előtagok állapota *manuális érvényesítésre* állapotba, meg kell nyitnia egy támogatási jegyet a [a Microsoft támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) megbizonyosodhat róla, hogy Ön a tulajdonosa a meghirdetett IP-címeket, és a társított autonóm rendszer száma.
> 
> 

## <a name="next-steps"></a>További lépések
* Az ExpressRoute-kapcsolat konfigurálása.
  
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-arm.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md)

