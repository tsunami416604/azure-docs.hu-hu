---
title: Kapcsolatos az ExpressRoute közvetlen – Azure |} A Microsoft Docs
description: Ez az oldal ExpressRoute közvetlen áttekintést nyújt.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: e598cc03a1b7b4999719152540866c7168130e03
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807482"
---
# <a name="about-expressroute-direct"></a>Az ExpressRoute Direct ismertetése

Az ExpressRoute közvetlen lehetőséget nyújt a közvetlenül a Microsoft társviszony-létesítési helyszínek stratégiai a világ különböző pontjain található globális hálózatának csatlakoztathatnak. Az ExpressRoute közvetlen kettős 100 GB/s és 10 GB/s sebességű kapcsolatot, amely támogatja az aktív/aktív kapcsolat ipari méretekben biztosít.

Az ExpressRoute közvetlen biztosít fő funkcióját tartalmazza, de nem korlátozódik:

* Nagy mennyiségű adat betöltése az olyan szolgáltatásokba, mint például a Storage és az Cosmos DB
* Szabályozott módon, és például dedikált és elkülönített kapcsolatot igénylő iparágakban a fizikai elszigetelése: Banki szolgáltatások, kormányzati és kereskedelmi
* A kapcsolatcsoportok elosztásának üzleti egységen alapuló, részletes szabályzása

## <a name="onboard-to-expressroute-direct"></a>Az ExpressRoute közvetlen előkészítése

Mielőtt használná az ExpressRoute közvetlen, először regisztrálnia kell az előfizetés. E-mail küldése a regisztrációhoz <ExpressRouteDirect@microsoft.com> meg előfizetési azonosítóját, beleértve az alábbiakat:

* Szeretne elvégezni a forgatókönyvek **ExpressRoute közvetlen**
* Hely beállításai – lásd: [partnerek és társviszony-létesítési helyszínek](expressroute-locations-providers.md) teljes listáját az összes hely
* Megvalósítás idővonala
* Vannak még kérdések

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>Az ExpressRoute használatával egy szolgáltatót és az ExpressRoute közvetlen

| **Az ExpressRoute-szolgáltató használatával** | **Az ExpressRoute közvetlen** | 
| --- | --- |
| Már használja a szolgáltatók számára a gyors bevezetése és a meglévő infrastruktúra kapcsolat engedélyezése | 100 GB/s/10 GB/s-infrastruktúra és az összes réteg teljes felügyeleti igényel
| Integrálható a több száz, többek között mpls virtuális Magánhálózat és az Ethernet-szolgáltatók | A szabályozott iparágakban és nagyméretű adatfeldolgozás közvetlen/dedikált kapacitás |
| Kapcsolatcsoportok termékváltozatot 50 MB/s a 10 GB/s | Ügyfél lehet, hogy a következő termékváltozatok 100 GB/s expressroute kapcsolatcsoport kombinációját közvetlen kiválasztása: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 GB/s</li><li>100 GB/s</li></ul> Ügyfél lehet, hogy a következő termékváltozatok a 10 GB/s az ExpressRoute kapcsolatcsoport kombinációját közvetlen kiválasztása:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Egyetlen bérlő optimalizálva | Egyetlen bérlő/Felhőbeli szolgáltatók optimalizált / több üzleti egység

## <a name="expressroute-direct-circuits"></a>Közvetlen ExpressRoute-Kapcsolatcsoportok

A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute használatával kapcsolatokat létesíthet olyan Microsoft-felhőszolgáltatásokkal, mint például a Microsoft Azure, az Office 365 és a Dynamics 365.  

Minden társviszony-létesítési helyszínen férhet hozzá a Microsoft globális hálózatának, és alapértelmezés szerint a geopolitikai zónában lévő bármelyik régióban hozzáférhet, és hozzáférhet az összes globális régiókban a prémium szintű expressroute-kapcsolatcsoporthoz.  

A legtöbb esetben a funkció megegyezik, amelyek használatához az ExpressRoute-szolgáltató megfelelően működjenek. A további granularitási és az ExpressRoute közvetlen használatával nyújtott új képességek, vannak bizonyos legfontosabb képességei a közvetlen ExpressRoute-Kapcsolatcsoportok.

## <a name="circuit-skus"></a>Kapcsolatcsoport SKU-k

Az ExpressRoute közvetlen támogatja az adatmennyiség nagymértékű Adatbetöltési forgatókönyveket az Azure storage és más big Data típusú adatok szolgáltatásokba. Az ExpressRoute-Kapcsolatcsoportok a 100 GB/s az ExpressRoute közvetlen most is támogatja a **40 GB/s** és **100 GB/s** kapcsolatcsoport SKU-k. A fizikai port pár **100 és 10 GB/s** csak és több virtuális kapcsolatok is rendelkezhet. Kapcsolatcsoport méretek:

| **Az ExpressRoute közvetlen 100 GB/s** | **Az ExpressRoute közvetlen 10 GB/s** | 
| --- | --- |
| **Az előfizetett sávszélesség**: 200 Gbps | **Az előfizetett sávszélesség**: 20 GB/s |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 GB/s</li><li>100 GB/s</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Technikai követelmények

* A Microsoft Enterprise peremhálózati útválasztóhoz (MSEE) felületek:
    * Kettős 10 vagy 100 Gigabit Ethernet portok csak pár útválasztó között
    * Egyetlen mód LR Fiber-kapcsolat
    * IPv4 és IPv6
    * IP MTU 1500 bytes

* Kapcsoló-vagy útválasztó rétegbeli 2/Layer 3 kapcsolatot:
    * Támogatnia kell a 1 802.1Q (Dot1Q) vagy két kód (QinQ) 802.1Q címkét beágyazás
    * Ethertype = 0x8100
    * Fel kell vennie a külső VLAN-címke (STAG) – a Microsoft által megadott VLAN-azonosító alapján *QinQ csak a alkalmazni*
    * Támogatnia kell több BGP-munkamenetek (VLAN) port- és eszköz
    * IPv4 és IPv6-kapcsolatok. *Az IPv6 nincs további alárendelt felület jön létre. IPv6-címet fogja hozzáadni meglévő alárendelt felület*. 
    * Nem kötelező: [Kétirányú továbbítás észlelése (BFD)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) támogatja, amelyen konfigurálva van az összes privát társviszony létesítése az ExpressRoute-Kapcsolatcsoportok alapértelmezés szerint

## <a name="vlan-tagging"></a>VLAN-címkézés

Az ExpressRoute közvetlen támogatja QinQ és a Dot1Q VLAN-címkézést.

* **VLAN-címkézés QinQ** lehetővé teszi elkülönített útválasztási tartományok egy ExpressRoute-kapcsolatcsoport alapon történik. Az Azure dinamikusan foglalja le az S-címke kapcsolatcsoport létrehozásakor, és nem módosítható. Minden társviszony-létesítést a kapcsolatcsoport (privát és Microsoft) felhasznál egy egyedi C-címkét a VLAN-t. A C-címke nem szükséges egyedinek kell lennie az ExpressRoute közvetlen portokon Kapcsolatcsoportok között.

* **VLAN-címkézés Dot1Q** lehetővé teszi, hogy az egyetlen VLAN címkézett egy ExpressRoute közvetlen port pár alapon történik. A társviszony-létesítéshez használt C-címke minden Kapcsolatcsoportok és az ExpressRoute közvetlen port párt a társviszony-létesítéseket egyedinek kell lennie.

## <a name="workflow"></a>Munkafolyamat

[![A munkafolyamat](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

Az ExpressRoute közvetlen aktív-aktív redundáns kapcsolatok a Microsoft globális hálózatban ugyanazon nagyvállalati szintű SLA-t biztosít. ExpressRoute-infrastruktúra képes legyen redundáns, és a Microsoft globális hálózati kapcsolatokra redundáns és számos különböző, és ennek megfelelően méretezi az ügyfél követelményeinek. 

## <a name="next-steps"></a>További lépések

[Az ExpressRoute közvetlen konfigurálása](expressroute-howto-erdirect.md)
