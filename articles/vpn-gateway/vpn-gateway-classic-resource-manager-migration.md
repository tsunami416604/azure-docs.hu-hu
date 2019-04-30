---
title: VPN Gateway Klasszikusról Resource Manager-áttelepítés |} A Microsoft Docs
description: Ez az oldal nyújt a VPN Gateway klasszikus Resource Manager az áttelepítés áttekintését.
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
ms.openlocfilehash: b65b47389611bcc0e5acb3c7ebff672f72a87581
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761584"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>VPN-átjáró klasszikusról Resource Manager-áttelepítés
VPN-átjárókkal is áttelepíthetők a klasszikusból Resource Manager-alapú üzemi modellbe. További információ az Azure Resource Manager [funkciók és előnyök](../azure-resource-manager/resource-group-overview.md). Ebben a cikkben azt bemutatják, hogyan kell áttelepíteni a klasszikus üzemi modellben az újabb, Resource Manager-alapú modell. 

VPN-átjárók a virtuális hálózatok közötti áttelepítés klasszikusról Resource Manager részeként települnek át. Az áttelepítés egyszerre csak egy virtuális hálózat végezhető el. Esetében nem követelmény, amelyek további eszközök vagy a migrálás előfeltételei tekintetében. Áttelepítés lépései megegyeznek a meglévő VNet-migrálás, és vannak dokumentálva [IaaS-erőforrások áttelepítése lap](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Áttelepítés során nem adatok elérési utat jár, és így a számítási feladatokat szeretne továbbra is működnek a helyszíni kapcsolat megszakadása nélkül áttelepítés során. A VPN-átjáró társított nyilvános IP-cím nem változtatja meg az áttelepítési folyamat során. Ez azt jelenti, hogy nem kell konfigurálnia a helyszíni útválasztót, az áttelepítés befejeződése után.  

A modell a Resource Managerben eltér a klasszikus modellt és a virtuális hálózati átjárók, a helyi hálózati átjárók és a kapcsolati erőforrás tevődik össze. Ezek képviselik a VPN-átjáró, a helyszíni címtér és a kettő közötti kapcsolatot képviselő helyi hely. Áttelepítés befejeződése után az átjárók nem lesznek elérhetőek a klasszikus modellben, és a Resource Manager modellel összes műveletek a virtuális hálózati átjárók, a helyi hálózati átjárók és a kapcsolat objektumot kell elvégezni.

## <a name="supported-scenarios"></a>Támogatott esetek
Leggyakrabban használt VPN-kapcsolat forgatókönyvek klasszikusról Resource Manager-áttelepítés esnek. Közé tartozik a támogatott forgatókönyveket –

* Pont-hely kapcsolat
* Hely-hely kapcsolat VPN-átjáróval csatlakozik a helyszíni helyen
* Virtuális hálózatok közötti VPN-átjárók használatával két virtuális hálózat közötti kapcsolat
* Több virtuális hálózat csatlakozik ugyanaz a helyszíni helyen
* Többhelyes kapcsolat
* Kényszerített bújtatás engedélyezve van a virtuális hálózatok

Nem támogatott forgatókönyvek között megtalálható az-  

* Virtuális hálózat az ExpressRoute-átjáró és a VPN Gateway jelenleg nem támogatott.
* Átvitel forgatókönyvek, ahol a Virtuálisgép-bővítmények csatlakoznak a helyszíni kiszolgálók. VPN-csatlakozási korlátozásokat átvitel lásd lent.

> [!NOTE]
> A Resource Manager-modell CIDR érvényesítésre több szigorú, mint a klasszikus modellt. Való migrálás előtt győződjön meg arról, hogy adott címtartományok klasszikus megfelelnek-e érvényes CIDR-formátumban a migrálás megkezdése előtt. CIDR használatával minden olyan közös CIDR-érvényesítők érvényesíthető. VNet- vagy helyek érvénytelen CIDR-tartományok áttelepítésekor a sikertelen állapotú eredményezne.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Virtuális hálózat virtuális hálózatok közötti kapcsolat áttelepítése
Hozzon létre egy helyi hely ábrázolása a csatlakoztatott virtuális hálózatok közötti virtuális hálózatok közötti kapcsolatot klasszikus VNet értük el. Ügyfelek kell hoznia két helyi telephelyre, amely a két virtuális hálózat szükséges kapcsolódik egymáshoz. Ezután ezek is csatlakozik a megfelelő virtuális hálózatokat IPsec-alagutat létesíteni a két virtuális hálózat közötti kapcsolatot. Ez a modell rendelkezik kezelhetőségi kihívásokat, mivel egy virtuális hálózaton címtartomány cím módosításokat is fenn kell tartani a megfelelő helyi hely reprezentációban szereplő. Ez a megoldás a Resource Manager-modellben már nincs szükség. A két virtuális hálózat közötti kapcsolat érhető el, közvetlenül a kapcsolati erőforrás kapcsolat típusa: Vnet2Vnet"használatával. 

![Képernyőkép a virtuális hálózat virtuális hálózatok közötti áttelepítése.](./media/vpn-gateway-migration/migration1.png)

Virtuális hálózatok közötti áttelepítés során azt észleli, hogy az aktuális virtuális hálózat VPN-átjáróhoz csatlakoztatott entitás egy másik virtuális hálózathoz, és győződjön meg arról, hogy mindkét virtuális hálózat migrálásának befejezése után már nem jelennének meg a másik virtuális hálózat jelölő két helyi telephelyre. Resource Manager modellre két VPN-átjárót és két kapcsolat típusa: Vnet2Vnet alakította két VPN-átjárók, a két helyi telephelyre és a közöttük a két kapcsolattal a klasszikus modellt.

## <a name="transit-vpn-connectivity"></a>Átvitel VPN-kapcsolat
VPN-átjárók konfigurálhatja a topológia úgy, hogy egy másik virtuális hálózathoz, amely közvetlenül csatlakozik a helyszíni összekötő úgy érhető el, egy virtuális hálózat címtere használó helyszíni kapcsolatok. Ez a VPN-kapcsolatok átvitel példányok az első virtuális hálózat hol csatlakoznak a helyszíni erőforrások elérése a csatlakoztatott virtuális hálózat, amely közvetlenül csatlakozik a helyszíni VPN-átjáróhoz való átvitel során. Ez a konfiguráció a klasszikus üzemi modellben eléréséhez kell a helyi webhely, amely rendelkezik összesített előtagok jelölő mindkét a csatlakoztatott virtuális hálózat létrehozása, és a helyszíni címtér. A representational helyi webhelyhez kapcsolódik, a virtuális hálózatok közötti kapcsolatok átvitel érdekében. A klasszikus modellt is tartalmaz hasonló kezelhetőségi kihívásokat, mivel bármely módosítása a helyi címtartományának is fenn kell tartani a virtuális hálózat és helyszíni összesítés képviselő helyi webhelyen. BGP támogatása a támogatott erőforrás-kezelő átjárók bevezetése egyszerűbbé teszi kezelhetőségi, mivel csatlakoztatott átjáró további útvonalakat a helyszíni címelőtagokat manuális módosítása nélkül.

![Képernyőfelvétel a tranzit útválasztási forgatókönyv.](./media/vpn-gateway-migration/migration2.png)

VNet azt átalakítása Vnetek közötti kapcsolatot anélkül, hogy a helyek, mivel a átviteli forgatókönyv megszakad a kapcsolat a helyszíni, amely a helyszíni közvetve csatlakozik a virtuális hálózathoz. Megszakadt a kapcsolat a következő két módon mérsékelhető a migrálás után – 

* A BGP engedélyezéséhez kapcsolódnak egymáshoz, és a helyszíni VPN-átjárókon. BGP engedélyezése visszaállítja a kapcsolatot minden egyéb konfigurációs módosítása nélkül, mivel útvonalak megtanult és meghirdetett virtuális hálózati átjáró között. Vegye figyelembe, hogy a BGP-beállítás csak a Standard és a magasabb szintű termékváltozatok rendelkezésre.
* A helyi hálózati átjáróhoz a helyszíni helyet az érintett virtuális hálózat egy explicit kapcsolatot létesíteni. Ez is cseréjével konfiguráció létrehozása és konfigurálása az IPsec-alagutat a helyszíni útválasztón.

## <a name="next-steps"></a>További lépések
Miután megismerkedett a VPN gateway áttelepítés támogatása, látogasson el [platform által támogatott áttelepítés IaaS-erőforrások klasszikusból Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-ps.md) a kezdéshez.

