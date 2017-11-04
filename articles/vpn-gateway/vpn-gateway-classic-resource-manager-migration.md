---
title: "VPN Gateway klasszikus – erőforrás-kezelő áttelepítésének |} Microsoft Docs"
description: "Ezen a lapon a VPN Gateway hagyományos erőforrás-kezelő áttelepítése áttekintést nyújt."
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: amsriva
ms.openlocfilehash: 1164fc24355657af22b6befaad74685ebbc2b5cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>VPN-átjáró klasszikus – erőforrás-kezelő áttelepítése
VPN-átjáróként is áttelepíthetők a klasszikus a Resource Manager üzembe helyezési modellben. További tudnivalók Azure Resource Manager [szolgáltatásait és előnyeit](../azure-resource-manager/resource-group-overview.md). Ez a cikk azt hogyan kell áttelepíteni a klasszikus üzembe helyezés újabb alapú Resource Manager modellt adatok találhatók. 

VPN-átjárók a rendszer a virtuális hálózat a klasszikus Resource Manager rendszerbe történő áttelepítés részeként telepíti át. Ez az áttelepítés egyszerre csak egy virtuális hálózat végezhető el. Nincs olyan további eszközök vagy áttelepítési Előfeltételek követelmény. Áttelepítési lépések dokumentálásukat pedig azonosak legyenek a meglévő virtuális hálózat áttelepítése [IaaS erőforrások áttelepítési lap](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Nincs állásidő nélkül adatok elérési útja az áttelepítés során, és így meglévő alkalmazások továbbra is az áttelepítés során a helyi kapcsolat megszakadása nélkül működik. A nyilvános IP-cím, a VPN-átjáró társított nem változtatja meg az áttelepítési folyamat során. Ez azt jelenti, hogy nem kell konfigurálnia a helyszíni útválasztót, az áttelepítés befejeződése után.  

A modell a erőforrás-kezelő nem azonos a klasszikus modellt, és virtuális hálózati átjárók, a helyi hálózati átjáró és a kapcsolat erőforrások tevődik össze. Ezek képviselik a VPN-átjáró, a helyszíni címtér és a kettő közötti kapcsolatot képviselő helyi-hely. Áttelepítés befejeződése után az átjáró nem klasszikus modellben érhető el, és a Resource Manager modellt használja a virtuális hálózati átjárók, a helyi hálózati átjáró és a kapcsolat objektumok összes felügyeleti műveletet kell végrehajtani.

## <a name="supported-scenarios"></a>Támogatott esetek
VPN-kapcsolat leggyakoribb forgatókönyve az erőforrás-kezelő áttelepítésének klasszikus tartoznak. A támogatott helyzetek a következők-

* Pont-hely kapcsolat
* Hely-hely kapcsolat a VPN-átjáró csatlakozik a helyi helye
* VNet közötti VPN-átjárók használatával két Vnetek VNet-kapcsolatot
* Több Vnetek csatlakozik megegyezik a helyi helye
* Többhelyes kapcsolat
* A kényszerített bújtatás Vnetek engedélyezve

Nem támogatott forgatókönyvek például a-  

* Az ExpressRoute-átjáró és a VPN-átjáró virtuális hálózat jelenleg nem támogatott.
* Átvitel közben forgatókönyvek, amelyekben Virtuálisgép-bővítmények a helyszíni kiszolgálók csatlakoznak. VPN-kapcsolat korlátozások átvitel közben részleteket lejjebb olvashatja.

> [!NOTE]
> A Resource Manager modellt CIDR érvényesítésre több szigorú, mint a klasszikus modellt. Áttelepítése előtt győződjön meg arról, hogy klasszikus címtartományok megadott megfelelnek-e érvényes CIDR-formátumban a áttelepítésének megkezdése előtt. CIDR érvényesíteni lehessen bármely közös CIDR érvényesség-ellenőrzők használatával. A virtuális hálózat vagy érvénytelen CIDR tartományok áttelepítésekor a helyi telephely eredményezne hibaállapotban van.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Virtuális hálózat virtuális hálózat kapcsolat áttelepítése
A VNet-kapcsolatot a klasszikus virtuális hálózatot a helyi webhelyhez megjelenítése a csatlakoztatott virtuális hálózat létrehozásával értük el. Az ügyfelek kell hoznia két helyi telephelyre, amely a két Vnetek szükséges kapcsolódik egymáshoz. Ezek volt majd a megfelelő Vnetekhez csatlakoztatni IPsec-alagút segítségével a két Vnetek közötti kapcsolat létrehozásához. Ebben a modellben rendelkezik kezelhetőségi kihívás, mivel egy virtuális hálózat tartomány cím módosításai biztosítani kell a megfelelő hely ábrázolás. A Resource Manager modellt ezt a megoldást már nem szükséges. A két Vnetek közötti kapcsolat közvetlenül elérhető a kapcsolati erőforrást "Vnet2Vnet" kapcsolattípus használja. 

![Képernyőkép a virtuális hálózat virtuális hálózat áttelepítése.](./media/vpn-gateway-migration/migration1.png)

Virtuális hálózat áttelepítése során azt észleli, hogy az aktuális virtuális hálózat VPN Gateway csatlakoztatott entitás egy másik virtuális hálózatot, és győződjön meg arról, hogy mindkét Vnetek áttelepítésének befejezése után már nem mutatunk be a virtuális hálózatot képviselő két helyi telephelyre. A klasszikus modellt két VPN-átjárók, a két helyi telephelyre és a közöttük két kapcsolatok alakította két VPN-átjárók és két kapcsolatok Vnet2Vnet típusú erőforrás-kezelő modellhez.

## <a name="transit-vpn-connectivity"></a>Átvitel VPN-kapcsolat
VPN-átjárók konfigurálhatja a topológia úgy, hogy a helyi kapcsolat egy vnet érhető el, egy másik virtuális hálózatot, amely közvetlenül csatlakozik a helyi csatlakozva. Ez az átvitel VPN-kapcsolat amelyekben első VNet-példány a helyszíni erőforrások átvitel során, hogy a VPN-átjáró, amely közvetlenül csatlakozik a helyi csatlakoztatott virtuális keresztül csatlakoznak. Ezt a konfigurációt a klasszikus üzembe helyezési modellel eléréséhez kell létrehozni a helyi webhely, amely mindkét a csatlakoztatott virtuális hálózatot képviselő előtagok van összesítve, és a helyszíni címtér. A representational helyi webhely majd csatlakozik-e a virtuális hálózat eléréséhez, az átvitel közben kapcsolat. A klasszikus modellt hasonló kezelhetőségi kihívást is rendelkezik, mivel a helyszíni címtartomány változást is fenn kell tartani a virtuális hálózat és a helyszíni összesítés képviselő helyi helykiszolgálón. A támogatott erőforrás-kezelő átjáró BGP-támogatás bevezetése egyszerűbbé teszi kezelhetőségi, mivel csatlakoztatott átjáró megtanulhassa útvonalakat a helyszíni előtagok manuális módosítás nélkül.

![Képernyőkép a tranzit útválasztás forgatókönyve.](./media/vpn-gateway-migration/migration2.png)

Jelenleg átalakítás VNet VNet-kapcsolatot anélkül, hogy a helyi telephely, mert az az átvitel közben forgatókönyv közvetve csatlakozik a helyi vnet helyszíni kapcsolata megszakad. A kapcsolat megszakadása mérsékelhető a következő két módon áttelepítés befejeződött- 

* A BGP engedélyezéséhez a csatlakoztatott együtt, és a helyszíni VPN-átjárók. Kapcsolat nélkül a konfigurációs változásokat BGP engedélyezése állítja vissza, mert útvonalak megtanulta, és meghirdetett VNet-átjárók között. Vegye figyelembe, hogy BGP beállítás csak akkor érhető el, a Standard és magasabb SKU.
* A helyszíni hely képviselő helyi hálózati átjáró érintett vnet explicit kapcsolatot létrehozni. Ez is igényelnének, létrehozása és konfigurálása az IPsec-alagutat a helyszíni útválasztó-konfiguráció megváltoztatását.

## <a name="next-steps"></a>Következő lépések
VPN-átjáró áttelepítési támogatás megismerését követően navigáljon [IaaS-erőforrásokra a klasszikus az erőforrás-kezelő áttelepítésének platform által támogatott](../virtual-machines/windows/migration-classic-resource-manager-ps.md) a kezdéshez.

