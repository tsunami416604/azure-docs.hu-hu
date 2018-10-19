---
title: Tudnivalók az Azure ExpressRoute közvetlen |} A Microsoft Docs
description: Ez az oldal áttekintést az ExpressRoute közvetlen (előzetes verzió)
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: ce4bc88f6b599bb3f5f760b2ba83c81f2b6acc5f
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405178"
---
# <a name="about-expressroute-direct-preview"></a>Kapcsolatos az ExpressRoute közvetlen (előzetes verzió)

Az ExpressRoute Direct közvetlen csatlakozást tesz lehetővé az ügyfelek számára a Microsoft globális hálózatához a világszerte stratégiai pontokon elhelyezett társviszony-létesítési helyszíneken át. Az ExpressRoute közvetlen biztosít kettős 100 GB/s sebességű kapcsolatot, amely támogatja az aktív/aktív kapcsolat ipari méretekben. 

Az ExpressRoute közvetlen biztosít fő funkcióját tartalmazza, de nem korlátozódik:

* Nagy mennyiségű adat betöltése az olyan szolgáltatásokba, mint például a Storage és az Cosmos DB 
* Fizikai elkülönítés a szabályozott, valamint dedikált és elszigetelt kapcsolódást igénylő iparágak, például a banki, kormányzati és kiskereskedelmi ágazatok számára. 
* A kapcsolatcsoportok elosztásának üzleti egységen alapuló, részletes szabályzása

> [!IMPORTANT]
> Az ExpressRoute közvetlen jelenleg előzetes verzióban érhető el.
>
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enroll-in-the-preview"></a>Regisztráció az előzetes verzióra

Mielőtt használná az ExpressRoute közvetlen, először regisztrálnia kell előfizetését az előzetes verzióban érhető el. Ehhez küldjön egy e-mailt a következő címre az előfizetés azonosítójával: <ExpressRouteDirect@microsoft.com>. Az ExpressRoute közvetlen egy olyan nagyvállalati szintű szolgáltatás. Adjon meg további részleteket:

* Szeretne elvégezni a forgatókönyvek **ExpressRoute közvetlen**
* Hely beállításai – lásd: [partnerek és társviszony-létesítési helyszínek](expressroute-locations-providers.md) teljes listáját az összes hely
* Megvalósítás idővonala
* Vannak még kérdések

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>Az ExpressRoute használatával egy szolgáltatót és az ExpressRoute közvetlen

| **Az ExpressRoute-szolgáltató használatával** | **Az ExpressRoute közvetlen** | 
| --- | --- | 
| Már használja a szolgáltató gyors bevezetése és a meglévő infrastruktúra kapcsolat engedélyezése | 100 GB/s-infrastruktúra és az összes réteg teljes felügyeleti igényel
| Integrálható a több száz, többek között mpls virtuális Magánhálózat és az Ethernet-szolgáltatók | A szabályozott iparágakban és nagyméretű adatfeldolgozás közvetlen/dedikált kapacitás | 
| Kapcsolatcsoportok termékváltozatot a 50 MB/s – 10 GB/s | Kapcsolatcsoportok termékváltozatot a 5 GB/s, 100 GB/s (5 GB/s sebességű, 10 GB/s, 40 GB/s, 100 GB/s)
| Egyetlen bérlő optimalizálva | Egyetlen bérlő/Felhőbeli szolgáltatók optimalizált / több üzleti egység

## <a name="expressroute-direct-circuits"></a>Közvetlen ExpressRoute-Kapcsolatcsoportok

A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute használatával kapcsolatokat létesíthet olyan Microsoft-felhőszolgáltatásokkal, mint például a Microsoft Azure, az Office 365 és a Dynamics 365.  

Minden társviszony-létesítési helyszínen férhet hozzá a Microsoft globális hálózatának, és alapértelmezés szerint a geopolitikai zónában lévő bármelyik régióban hozzáférhet, és hozzáférhet az összes globális régiókban a prémium szintű expressroute-kapcsolatcsoporthoz.  

A legtöbb esetben a funkció megegyezik, amelyek használatához az ExpressRoute-szolgáltató megfelelően működjenek. A további granularitási és az ExpressRoute közvetlen használatával nyújtott új képességek, vannak bizonyos legfontosabb képességei a közvetlen ExpressRoute-Kapcsolatcsoportok.

## <a name="circuit-skus"></a>Kapcsolatcsoport SKU-k

Az ExpressRoute közvetlen támogatja az adatmennyiség nagymértékű Adatbetöltési forgatókönyveket az Azure storage és más big Data típusú adatok szolgáltatásokba. Az ExpressRoute Kapcsolatcsoportok az ExpressRoute közvetlen most is támogatási **40 GB/s** és **100 GB/s** kapcsolatcsoport SKU-k. 

## <a name="vlan-tagging"></a>VLAN-címkézés

Az ExpressRoute közvetlen támogatja QinQ és a Dot1Q VLAN-címkézést.

* **VLAN-címkézés QinQ** lehetővé teszi elkülönített útválasztási tartományok egy ExpressRoute-kapcsolatcsoport alapon történik. Az Azure dinamikusan foglalja le az S-címke kapcsolatcsoport létrehozásakor, és nem módosítható. Minden társviszony-létesítést a kapcsolatcsoport (privát és Microsoft) felhasznál egy egyedi C-címkét a VLAN-t. A C-címke nem szükséges egyedinek kell lennie az ExpressRoute közvetlen portokon Kapcsolatcsoportok között. 

* **VLAN-címkézés Dot1Q** lehetővé teszi, hogy az egyetlen VLAN címkézett egy ExpressRoute közvetlen port pár alapon történik. A társviszony-létesítéshez használt C-címke minden Kapcsolatcsoportok és az ExpressRoute közvetlen port párt a társviszony-létesítéseket egyedinek kell lennie.

## <a name="workflow"></a>Munkafolyamat

![munkafolyamat](./media/expressroute-erdirect-about/workflow1.png)

## <a name="sla"></a>SLA

Az ExpressRoute közvetlen aktív-aktív redundáns kapcsolatok a Microsoft globális hálózatban ugyanazon nagyvállalati szintű SLA-t biztosít. ExpressRoute-infrastruktúra képes legyen redundáns, és a Microsoft globális hálózati kapcsolatokra redundáns és számos különböző, és ennek megfelelően méretezi az ügyfél követelményeinek. Az előzetes verzióban lesz nem biztosítunk szolgáltatói szerződést, és csak nem éles számítási feladatokhoz érdemes megfontolni.

## <a name="next-steps"></a>További lépések

[Az ExpressRoute közvetlen konfigurálása](expressroute-howto-erdirect.md)
