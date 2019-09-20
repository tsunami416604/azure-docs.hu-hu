---
title: Kapcsolatos az ExpressRoute közvetlen – Azure |} A Microsoft Docs
description: Ez az oldal áttekintést nyújt a ExpressRoute Directről
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 916175401cd993392094b2bb16f8fc8746a4d2a8
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123401"
---
# <a name="about-expressroute-direct"></a>Az ExpressRoute Direct ismertetése

Az ExpressRoute közvetlen lehetőséget nyújt a közvetlenül a Microsoft társviszony-létesítési helyszínek stratégiai a világ különböző pontjain található globális hálózatának csatlakoztathatnak. A ExpressRoute Direct kettős 100 GB/s vagy 10 GB/s kapcsolatot biztosít, amely támogatja az aktív/aktív kapcsolatot a skálán.

Az ExpressRoute közvetlen biztosít fő funkcióját tartalmazza, de nem korlátozódik:

* Nagy mennyiségű adat betöltése az olyan szolgáltatásokba, mint például a Storage és az Cosmos DB
* Az olyan iparágak fizikai elkülönítése, amelyek szabályozottak és dedikált és elkülönített kapcsolatokat igényelnek, például: Banki, kormányzati és kereskedelmi
* A kapcsolatcsoportok elosztásának üzleti egységen alapuló, részletes szabályzása

## <a name="onboard-to-expressroute-direct"></a>Közvetlen ExpressRoute

A ExpressRoute Direct használata előtt először regisztrálnia kell az előfizetését. E-mail küldése a regisztrációhoz <ExpressRouteDirect@microsoft.com> meg előfizetési azonosítóját, beleértve az alábbiakat:

* Szeretne elvégezni a forgatókönyvek **ExpressRoute közvetlen**
* Hely beállításai – lásd: [partnerek és társviszony-létesítési helyszínek](expressroute-locations-providers.md) teljes listáját az összes hely
* Megvalósítás idővonala
* Vannak még kérdések

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>Az ExpressRoute használatával egy szolgáltatót és az ExpressRoute közvetlen

| **Az ExpressRoute-szolgáltató használatával** | **Az ExpressRoute közvetlen** | 
| --- | --- |
| Már használja a szolgáltatók számára a gyors bevezetése és a meglévő infrastruktúra kapcsolat engedélyezése | 100 Gbps/10 GB/s infrastruktúra és az összes réteg teljes felügyeletét igényli
| Integrálható a több száz, többek között mpls virtuális Magánhálózat és az Ethernet-szolgáltatók | A szabályozott iparágakban és nagyméretű adatfeldolgozás közvetlen/dedikált kapacitás |
| Kapcsolatcsoportok termékváltozatot 50 MB/s a 10 GB/s | Az ügyfél a következő áramköri SKU kombinációját választhatja a 100 GB/s ExpressRoute Direct-on: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 GB/s</li><li>100 GB/s</li></ul> Az ügyfél a következő áramköri SKU kombinációját választhatja 10 GB/s ExpressRoute Direct-on:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Egyetlen bérlő optimalizálva | Egyetlen bérlőre optimalizált több üzleti egységgel és több munkahelyi környezettel

## <a name="expressroute-direct-circuits"></a>Közvetlen ExpressRoute-Kapcsolatcsoportok

A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. A ExpressRoute használatával kapcsolatokat létesíthet a Microsoft Cloud Services, például a Microsoft Azure és az Office 365 között.

Minden társviszony-létesítési helyszínen férhet hozzá a Microsoft globális hálózatának, és alapértelmezés szerint a geopolitikai zónában lévő bármelyik régióban hozzáférhet, és hozzáférhet az összes globális régiókban a prémium szintű expressroute-kapcsolatcsoporthoz.  

A legtöbb esetben a funkció megegyezik, amelyek használatához az ExpressRoute-szolgáltató megfelelően működjenek. A további granularitási és az ExpressRoute közvetlen használatával nyújtott új képességek, vannak bizonyos legfontosabb képességei a közvetlen ExpressRoute-Kapcsolatcsoportok.

## <a name="circuit-skus"></a>Kapcsolatcsoport SKU-k

Az ExpressRoute közvetlen támogatja az adatmennyiség nagymértékű Adatbetöltési forgatókönyveket az Azure storage és más big Data típusú adatok szolgáltatásokba. Az 100 GB/s ExpressRoute Direct ExpressRoute-áramkörök mostantól támogatják az **40 Gbps** -es és a **100 Gbps** -es adatközpontot is. A fizikai port pár **100 vagy 10 GB/s** , és több virtuális áramkör is lehet. Áramköri méretek:

| **100 GB/s ExpressRoute Direct** | **10 GB/s ExpressRoute Direct** | 
| --- | --- |
| **Előfizetett sávszélesség**: 200 GB/s | **Előfizetett sávszélesség**: 20 GB/s |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 GB/s</li><li>100 GB/s</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Technikai követelmények

* Microsoft Enterprise Edge router (MSEE) interfészek:
    * Kettős 10 vagy 100 Gigabit Ethernet-portok csak az útválasztó párok között
    * Single Mode LR Fiber-kapcsolat
    * IPv4 és IPv6
    * IP-MTU 1500 bájt

* Switch/router 2. réteg/3. rétegbeli kapcsolata:
    * 1 802.1 Q (Dot1Q) vagy két tag 802.1 Q (QinQ) címke beágyazását kell támogatnia
    * EtherType = 0x8100
    * Hozzá kell adnia a külső VLAN-címkét (STAG) a Microsoft által megadott VLAN-azonosító alapján – *csak a QinQ*
    * Egy porton és eszközön több BGP-munkamenetet (VLAN) kell támogatnia
    * IPv4-és IPv6-kapcsolat. *Az IPv6 esetében nem jön létre további alkapcsolat. Az IPv6-cím hozzá lesz adva a meglévő alkapcsolathoz*. 
    * Nem kötelező: [Kétirányú továbbítási észlelés (BFD)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) támogatása, amely alapértelmezés szerint a ExpressRoute-áramkörök összes privát társán konfigurálva van

## <a name="vlan-tagging"></a>VLAN-címkézés

Az ExpressRoute közvetlen támogatja QinQ és a Dot1Q VLAN-címkézést.

* **VLAN-címkézés QinQ** lehetővé teszi elkülönített útválasztási tartományok egy ExpressRoute-kapcsolatcsoport alapon történik. Az Azure dinamikusan foglalja le az S-címke kapcsolatcsoport létrehozásakor, és nem módosítható. Minden társviszony-létesítést a kapcsolatcsoport (privát és Microsoft) felhasznál egy egyedi C-címkét a VLAN-t. A C-címke nem szükséges egyedinek kell lennie az ExpressRoute közvetlen portokon Kapcsolatcsoportok között.

* **VLAN-címkézés Dot1Q** lehetővé teszi, hogy az egyetlen VLAN címkézett egy ExpressRoute közvetlen port pár alapon történik. A társviszony-létesítéshez használt C-címke minden Kapcsolatcsoportok és az ExpressRoute közvetlen port párt a társviszony-létesítéseket egyedinek kell lennie.

## <a name="workflow"></a>Munkafolyamat

[![munkafolyamat](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

Az ExpressRoute közvetlen aktív-aktív redundáns kapcsolatok a Microsoft globális hálózatban ugyanazon nagyvállalati szintű SLA-t biztosít. ExpressRoute-infrastruktúra képes legyen redundáns, és a Microsoft globális hálózati kapcsolatokra redundáns és számos különböző, és ennek megfelelően méretezi az ügyfél követelményeinek. 

## <a name="next-steps"></a>További lépések

[Az ExpressRoute közvetlen konfigurálása](expressroute-howto-erdirect.md)
