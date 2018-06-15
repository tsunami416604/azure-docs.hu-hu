---
title: Munkafolyamatok konfigurálásához ExpressRoute-kapcsolatcsoportot |} Microsoft Docs
description: Ezen a lapon végigvezeti a munkafolyamatokat a ExpressRoute-kapcsolatcsoportot és társviszony konfigurálása
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''
ms.assetid: 55e0418c-e0bf-44a7-9aa1-720076df9297
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: cherylmc
ms.openlocfilehash: cba1b2cfee379e7d2b079bcb3089981ef1044d66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850776"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Az ExpressRoute kapcsolatcsoport-kiépítési munkafolyamatai és a kapcsolatcsoportok állapotai
Ezen a lapon végigvezeti a szolgáltatás üzembe helyezési és konfigurációs munkafolyamatok útválasztási magas szinten.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Az alábbi ábra és a megfelelő lépéseket kell követnie ahhoz, hogy rendelkezik kiépített ExpressRoute-kapcsolatcsoportot feladatokat jeleníti végpontok közötti. 

1. PowerShell segítségével konfigurálhatja az ExpressRoute-kapcsolatcsoportot. Kövesse az utasításokat a [létrehozása ExpressRoute-Kapcsolatcsoportok](expressroute-howto-circuit-classic.md) cikkben olvashat.
2. A szolgáltató közötti kapcsolatot sorrendje. Ez a folyamat függően változik. Kapcsolat rendezése kapcsolatos további részletekért forduljon a kapcsolat szolgáltatóját.
3. Győződjön meg arról, hogy a kapcsolatcsoport van kiépítve sikeresen üzembe helyezési állapota a Powershellen keresztül ExpressRoute-kapcsolatcsoportot ellenőrzésével. 
4. Útválasztási tartományok konfigurálása. Ha a kapcsolat szolgáltatójánál, 3. rétegbeli kezeli, a vállalat konfigurálja a kapcsolatcsoport útválasztást. Ha a kapcsolat szolgáltatójánál csak a 2. rétegbeli szolgáltatásokat biztosít, konfigurálnia kell egy leírt irányelveket útválasztási a [útválasztási követelmények](expressroute-routing.md) és [útválasztási konfigurációja](expressroute-howto-routing-classic.md) lapokat.
   
   * Azure magánhálózati társviszony-létesítés engedélyezése – engedélyeznie kell a társviszony csatlakozni a virtuális gépek / felhőszolgáltatások telepített virtuális hálózatokon belül.
   * Engedélyezze az Azure nyilvános társviszony - engedélyeznie kell az Azure nyilvános társviszony-létesítés Ha nyilvános IP-címek futó Azure-szolgáltatásokhoz. Ez a követelmény az Azure-erőforrások eléréséhez, ha úgy döntött, az Azure magánhálózati társviszony-létesítés útválasztás alapértelmezett engedélyezése.
   * Engedélyezze a Microsoft társviszony - hozzáférés Office 365 és Dynamics 365 engedélyeznie kell ezt. 
     
     > [!IMPORTANT]
     > Győződjön meg arról, hogy egy külön proxyt használ, / Edge böngésző számára csatlakoztatni a Microsoft a akkor használata az interneten. Az azonos él használ ExpressRoute és az internetről is aszimmetrikus útválasztási okozhat, és következtében a hálózati kapcsolat kimaradások.
     > 
     > 
     
     ![](./media/expressroute-workflows/routing-workflow.png)
5. Virtuális hálózatok csatolása az ExpressRoute-Kapcsolatcsoportok - hozzákapcsolhatja virtuális hálózatok az ExpressRoute-kapcsolatcsoportot. Kövesse az utasításokat [Vnetek csatolásához](expressroute-howto-linkvnet-arm.md) a kapcsolatcsoport számára. A Vnetek, ExpressRoute-kapcsolatcsoport Azure ugyanahhoz az előfizetéshez lehet, vagy lehet egy másik előfizetésben.

## <a name="expressroute-circuit-provisioning-states"></a>Kiépítés állapotok ExpressRoute-kapcsolatcsoportot
Minden egyes ExpressRoute-kapcsolatcsoportot két állapota van:

* Szolgáltatás szolgáltató üzembe helyezési állapota
* status

Állapotát a Microsoft a kiépítési állapotát jeleníti meg. Ez a tulajdonság engedélyezve van beállítva, ha Expressroute-kapcsolatcsoportot létrehozni

A kapcsolati szolgáltató üzembe helyezési állapota a kapcsolat szolgáltatójánál oldalon állapotát jeleníti meg. Ez lehet *NotProvisioned*, *kiépítési*, vagy *kiépítve*. Az ExpressRoute-kapcsolatcsoport is használni tudja kiépítve állapotban kell lennie.

### <a name="possible-states-of-an-expressroute-circuit"></a>Az ExpressRoute-kapcsolatcsoportot lehetséges állapota
Ez a szakasz ki a lehetséges állapotok az ExpressRoute-kapcsolatcsoportot.

**A létrehozás időpontjában**

A következő állapotot okozta ExpressRoute-kapcsolatcsoportot megjelenik, amint az ExpressRoute-kapcsolatcsoportot létrehozni a PowerShell-parancsmag futtatása.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Amikor kapcsolat szolgáltatójánál éppen a kapcsolatcsoport kiépítése**

Az ExpressRoute-kapcsolatcsoport a következő állapotban megjelenik, amint a szolgáltatás kulcs átadása a kapcsolat szolgáltatóját, és azok elindította a telepítési folyamatot.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Ha a kapcsolat szolgáltatóját az üzembe helyezési folyamat befejeződött**

A következő állapotot okozta ExpressRoute-kapcsolatcsoportot jelenik meg, amint a kapcsolat szolgáltatójánál a kiépítési folyamat befejeződött.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Üzembe helyezve, és engedélyezve a kapcsolatcsoport egyetlen állapotát is használni tudja lehet. Ha egy 2. rétegbeli szolgáltatót használja, beállíthatja a kapcsolatcsoport útválasztást csak akkor, ha az ebben az állapotban van.

**Ha a kapcsolat szolgáltatójánál megszüntetés a kapcsolatcsoport van**

A szolgáltató az ExpressRoute-kapcsolatcsoport kiosztásának megszüntetése a kért a következő állapotának beállítása után a szolgáltató a megszüntetési folyamatot a kapcsolatcsoport látják.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Ha szeretné, engedélyezheti, ha szükséges, vagy a PowerShell-parancsmagok a kapcsolatcsoport törléséhez.  

> [!IMPORTANT]
> Ha törli a kapcsolatcsoport a ServiceProviderProvisioningState kiépítésekor vagy kiépítve a művelet sikertelen lesz a PowerShell-parancsmagot kell futtatnia. A kapcsolat szolgáltatójánál kiosztásának megszüntetése először az ExpressRoute-kapcsolatcsoport adjon működnek, és törölje a kapcsolatcsoport. A Microsoft továbbra is a kapcsolatcsoport számlázási, amíg nem futtat a kapcsolatcsoport törlése a PowerShell-parancsmagot.
> 
> 

## <a name="routing-session-configuration-state"></a>Munkamenet-konfiguráció útválasztási állapota
A BGP üzembe helyezési állapota értesíti Önt arról, ha a BGP-munkamenetet a Microsoft edge engedélyezve lett. Az állapot meg fogja tudni használni a társviszony-létesítést engedélyezni kell.

Fontos, különösen a Microsoft társviszony-létesítés BGP munkamenet-állapot ellenőrzéséhez. Mellett a BGP üzembe helyezési állapota, van egy másik állapothoz nevű *meghirdetett nyilvános előtag állapot*. A meghirdetett nyilvános előtag állapotban kell lennie a *konfigurált* állapot, mind a BGP-munkamenethez be kell, és a végpontok közötti működéséhez irányításához. 

Ha a meghirdetett nyilvános előtag állapot beállítása a *szükséges érvényesítési* állapotba kerül, a BGP-munkamenet nincs engedélyezve, mint a hirdetett előtagok nem egyezik a AS számot sem az útválasztási nyilvántartó. 

> [!IMPORTANT]
> Ha a meghirdetett nyilvános előtag állapota *manuális érvényesítési* állapotba kerül, meg kell nyitnia a támogatási jegy [Microsoft támogatási szolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , és adja meg a megbizonyosodhat róla, hogy Ön a tulajdonosa az IP-címek meghirdetett mentén társított autonóm rendszer számát.
> 
> 

## <a name="next-steps"></a>Következő lépések
* Az ExpressRoute-kapcsolat konfigurálása.
  
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-arm.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md)

