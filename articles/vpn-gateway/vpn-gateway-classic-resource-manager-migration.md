---
title: VPN-átjáró klasszikus az erőforrás-kezelő áttelepítéséhez | Microsoft dokumentumok
description: Ez a lap áttekintést nyújt a KLASSZIKUS VPN-átjáró az Erőforrás-kezelőáttelepítésről.
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
ms.date: 02/06/2020
ms.author: amsriva
ms.openlocfilehash: c1a75630c6419816b048495ee87d24c81979af16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064767"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>VPN-átjáró klasszikus az Erőforrás-kezelő áttelepítéséhez
A VPN-átjárók mostantól áttelepíthetők a klasszikusról az Erőforrás-kezelő telepítési modelljére. Az Azure Resource Manager [funkcióiról és előnyeiről olvashat bővebben.](../azure-resource-manager/management/overview.md) Ebben a cikkben részletesen bemutatjuk, hogyan lehet áttérni a klasszikus központi telepítések újabb Erőforrás-kezelő alapú modell. 

A VPN-átjárók a virtuális hálózatok klasszikusról erőforrás-kezelőre való áttelepítésének részeként kerülnek áttelepítésre. Ez az áttelepítés történik egy virtuális hálózat egy időben. Nincs további követelmény az eszközök és előfeltételek az áttelepítés. Az áttelepítési lépések megegyeznek a meglévő virtuális hálózatok áttelepítésével, és az [IaaS-erőforrások áttelepítési lapján](../virtual-machines/windows/migration-classic-resource-manager-ps.md)vannak dokumentálva. Nincs adatelérési út állásidő az áttelepítés során, és így a meglévő számítási feladatok továbbra is működnek anélkül, hogy az áttelepítés során a helyszíni kapcsolat elvesztése. A VPN-átjáróhoz társított nyilvános IP-cím nem változik az áttelepítési folyamat során. Ez azt jelenti, hogy az áttelepítés befejezése után nem kell újrakonfigurálnia a helyszíni útválasztót.  

Az Erőforrás-kezelőben a modell eltér a klasszikus modelltől, és virtuális hálózati átjárókból, helyi hálózati átjárókból és kapcsolati erőforrásokból áll. Ezek magát a VPN-átjárót, a helyszíni címteret és a kettő közötti kapcsolatot képviselő helyi helyet jelölik. Az áttelepítés befejezése után az átjárók nem érhetők el a klasszikus modellben, és a virtuális hálózati átjárókon, a helyi hálózati átjárókon és a kapcsolatobjektumokon lévő összes felügyeleti műveletet erőforrás-kezelő modellhasználatával kell végrehajtani.

## <a name="supported-scenarios"></a>Támogatott esetek
A leggyakoribb VPN-kapcsolati forgatókönyveket a klasszikus erőforrás-kezelői áttelepítés fedi le. A támogatott forgatókönyvek közé tartoznak a

* Pont a hely közötti kapcsolatra
* Hely-hely kapcsolat vpn-átjáróval a helyszíni helyhez
* Virtuális hálózat és virtuális hálózat közötti kapcsolat két virtuális hálózat között VPN-átjárók használatával
* Több virtuális hálózat csatlakozik ugyanahhoz a telephelyen
* Többtelephelyes kapcsolat
* Kényszerített bújtatási engedélyezett virtuális hálózatok

A nem támogatott forgatókönyvek közé tartoznak a  

* Virtuális hálózat az ExpressRoute-átjáró és a VPN-átjáró jelenleg nem támogatott.
* Átviteli forgatókönyvek, ahol a virtuálisgép-bővítmények a helyszíni kiszolgálókhoz csatlakoznak. A tranzit VPN-kapcsolat korlátait az alábbiakban részletezzük.

> [!NOTE]
> Cidr érvényesítés e Resource Manager modell szigorúbb, mint a klasszikus modell. Az áttelepítés előtt győződjön meg arról, hogy a klasszikus címtartományok megfelelnek az érvényes CIDR formátumnak az áttelepítés megkezdése előtt. A CIDR bármely közös CIDR validátor segítségével érvényesíthető. Virtuális hálózat vagy az érvénytelen CIDR-tartományokkal rendelkező helyi helyek áttelepítése sikertelen állapotot eredményezne.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Virtuális hálózat és virtuális hálózat közötti kapcsolat áttelepítése
Virtuális hálózat és virtuális hálózat kapcsolat a klasszikus érhető el a csatlakoztatott virtuális hálózat helyi helyre prezentációlétrehozásával. Az ügyfeleknek két helyi helyet kellett létrehozniuk, amelyek a két virtuális hálózatot képviselték, amelyeket össze kellett kapcsolni. Ezek ezután csatlakoztak a megfelelő virtuális hálózatok iPsec-alagút segítségével kapcsolatot létesíteni a két virtuális hálózat között. Ez a modell kezelhetőségi kihívásokkal rendelkezik, mivel az egyik virtuális hálózat címtartományának módosításait a megfelelő helyi helyábrázolásban is fenn kell tartani. Az Erőforrás-kezelő modellben erre a megoldásra már nincs szükség. A két virtuális hálózat közötti kapcsolat közvetlenül elérhető a "Vnet2Vnet" kapcsolattípus használatával a Connection erőforrásban. 

![Képernyőkép a virtuális hálózat és a virtuális hálózat áttelepítéséről.](./media/vpn-gateway-migration/migration1.png)

A virtuális hálózatok áttelepítése során azt észleljük, hogy a csatlakoztatott entitás az aktuális virtuális hálózat VPN-átjáró egy másik virtuális hálózat, és biztosítja, hogy ha mindkét virtuális hálózat áttelepítése befejeződött, akkor már nem jelenik meg két helyi hely, amely a másik virtuális hálózat. A klasszikus modell két VPN-átjárók, két helyi helyek és két kapcsolat közöttük átalakul Resource Manager modell két VPN-átjárók és két típusú kapcsolatok Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Tranzit VPN-kapcsolat
A VPN-átjárók egy topológiában konfigurálhatók, így a virtuális hálózat helyszíni kapcsolata egy másik virtuális hálózathoz való csatlakozással érhető el, amely közvetlenül kapcsolódik a helyszíni szolgáltatásokhoz. Ez a tranzit VPN-kapcsolat, ahol az első virtuális hálózat példányai a helyszíni erőforrásokhoz csatlakoznak a vpn-átjáróhoz való átvitelen keresztül a csatlakoztatott virtuális hálózathoz, amely közvetlenül kapcsolódik a helyszíni hálózathoz. A klasszikus központi telepítési modell konfigurációjának eléréséhez létre kell hoznia egy helyi helyet, amely összesített előtagokat is biztosít, amelyek a csatlakoztatott virtuális hálózatot és a helyszíni címteret egyaránt képviselik. Ez a reprezentációs helyi hely ezután csatlakozik a virtuális hálózathoz a tranzitkapcsolat elérése érdekében. Ez a klasszikus modell is hasonló kezelhetőségi kihívásokkal rendelkezik, mivel a helyszíni címtartományban bekövetkező változásokat a virtuális hálózat és a helyszíni összesítés összességét képviselő helyi webhelyen is fenn kell tartani. A BGP-támogatás bevezetése az Erőforrás-kezelő által támogatott átjárókban leegyszerűsíti a kezelhetőséget, mivel a csatlakoztatott átjárók az előtagok manuális módosítása nélkül is tanulhatják az útvonalakat a helyszínen.

![Képernyőkép az átszállítási útválasztási forgatókönyvről.](./media/vpn-gateway-migration/migration2.png)

Mivel a virtuális hálózat virtuális hálózatra való átalakítása helyi helyek nélkül történik, a tranzitforgatókönyv elveszíti a helyszíni kapcsolatot a helyszíni kapcsolathoz, amely közvetetten csatlakozik a helyszíni hálózathoz. Az összekapcsoltság elvesztése a következő két módon mérsékelhető, miután az áttelepítés befejeződött - 

* Engedélyezze a BGP-t a vpn-átjárókon, amelyek össze kapcsolódnak a helyszíni csatlakozásokhoz. A BGP engedélyezése minden más konfigurációmódosítás nélkül visszaállítja a kapcsolatot, mivel az útvonalakat a virtuális hálózat átjárói között tanulják és hirdetik. Vegye figyelembe, hogy a BGP-beállítás csak standard és magasabb változaton érhető el.
* Explicit kapcsolat létrehozása az érintett virtuális hálózatról a helyi hálózati átjáróval, amely a helyszíni helyet képviseli. Ehhez a helyszíni útválasztó konfigurációjának módosítására is szükség van az IPsec-alagút létrehozásához és konfigurálásához.

## <a name="next-steps"></a>További lépések
Miután megismerkedett a VPN-átjáró áttelepítésének támogatásával, az első lépésekhez nyissa meg [az IaaS-erőforrások platform által támogatott áttelepítését a klasszikusról az Erőforrás-kezelőre.](../virtual-machines/windows/migration-classic-resource-manager-ps.md)

