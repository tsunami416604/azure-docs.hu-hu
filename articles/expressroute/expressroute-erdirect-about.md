---
title: Tudnivalók az Azure ExpressRoute Directről
description: Ismerje meg az Azure ExpressRoute Direct főbb funkcióit, valamint azokat az információkat, amelyek a közvetlen ExpressRoute, például az elérhető SKU-hoz és a technikai követelményekhez szükségesek.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f0faa4992b025783e4e5448dcf04fcfa958ba5ed
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192345"
---
# <a name="about-expressroute-direct"></a>Az ExpressRoute Direct ismertetése

A ExpressRoute Direct lehetővé teszi a közvetlen kapcsolódást a Microsoft globális hálózatához a világ bármely pontján elérhető, stratégiai módon elosztott helyen. A ExpressRoute Direct kettős 100 GB/s vagy 10 GB/s kapcsolatot biztosít, amely támogatja az aktív/aktív kapcsolatot a skálán.

A közvetlen ExpressRoute által biztosított főbb funkciók közé tartoznak a következők:

* Nagy mennyiségű adat betöltése az olyan szolgáltatásokba, mint például a Storage és az Cosmos DB
* Fizikai elkülönítés olyan iparágak esetében, amelyek szabályozott és elkülönített kapcsolatot igényelnek, például: banki, kormányzati és kereskedelmi
* A kapcsolatcsoportok elosztásának üzleti egységen alapuló, részletes szabályzása

## <a name="onboard-to-expressroute-direct"></a>Közvetlen ExpressRoute

A ExpressRoute Direct használata előtt először regisztrálnia kell az előfizetését. A regisztráláshoz küldjön egy e-mailt az <ExpressRouteDirect@microsoft.com> előfizetés-azonosítójával, beleértve az alábbi adatokat:

* A **ExpressRoute Directtel** elérni kívánt forgatókönyvek
* Hely beállításai – az összes hely teljes listájáért tekintse meg a [partnerek és](expressroute-locations-providers.md) a társítási helyek elemet.
* Megvalósítási ütemterv
* Bármilyen egyéb kérdés

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute-szolgáltató és ExpressRoute közvetlen használata

| **ExpressRoute szolgáltató használatával** | **ExpressRoute Direct** | 
| --- | --- |
| Szolgáltatók használatával lehetővé teszi a gyors bevezetést és a meglévő infrastruktúrához való csatlakozást | 100 Gbps/10 GB/s infrastruktúra és az összes réteg teljes felügyeletét igényli
| Integrálható több száz szolgáltatóval, többek között az Ethernettel és a MPLS | Közvetlen/dedikált kapacitás a szabályozott iparágak és a nagy adatfeldolgozás számára |
| Áramköri SKU-i 50 Mbps és 10 GB/s között | Az ügyfél a következő áramköri SKU kombinációját választhatja a 100 GB/s ExpressRoute Direct-on: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> Az ügyfél a következő áramköri SKU kombinációját választhatja 10 GB/s ExpressRoute Direct-on:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Egyetlen bérlőre optimalizált | Egyetlen bérlőre optimalizált több üzleti egységgel és több munkahelyi környezettel

## <a name="expressroute-direct-circuits"></a>Közvetlen ExpressRoute áramkörök

A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. A ExpressRoute használatával kapcsolatokat létesíthet a Microsoft Cloud Services, például a Microsoft Azure és az Office 365 között.

Minden egyes társítási hely rendelkezik hozzáféréssel a Microsoft globális hálózatához, és alapértelmezés szerint bármely régióhoz hozzáférhet a geopolitikai zónában, és az összes globális régióhoz prémium szintű áramkör érhető el.  

A legtöbb forgatókönyvben szereplő funkciók egyenértékűek azon áramkörökkel, amelyek az ExpressRoute-szolgáltatót használják a működéshez. A ExpressRoute Direct szolgáltatással elérhető további részletesség és új képességek támogatásához bizonyos kulcsfontosságú képességek állnak rendelkezésre a ExpressRoute Direct-áramkörökben.

## <a name="circuit-skus"></a>Áramköri SKU-i

A ExpressRoute Direct a nagyméretű adatfeldolgozási forgatókönyveket támogatja az Azure Storage-ban és más big data szolgáltatásokban. Az 100 GB/s ExpressRoute Direct ExpressRoute-áramkörök mostantól támogatják az **40 Gbps** -es és a **100 Gbps** -es adatközpontot is. A fizikai port pár **100 vagy 10 GB/s** , és több virtuális áramkör is lehet. Áramköri méretek:

| **100 GB/s ExpressRoute Direct** | **10 GB/s ExpressRoute Direct** | 
| --- | --- |
| **Előfizetett sávszélesség**: 200 GB/s | **Előfizetett sávszélesség**: 20 GB/s |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

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
    * Opcionális: [kétirányú továbbítási észlelési (BFD)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) támogatás, amely alapértelmezés szerint a ExpressRoute-áramkörök összes privát társán konfigurálva van

## <a name="vlan-tagging"></a>VLAN-címkézés

A ExpressRoute Direct a QinQ és a Dot1Q VLAN-címkézést is támogatja.

* A **QINQ VLAN-címkézés** lehetővé teszi az elkülönített útválasztási tartományok ExpressRoute áramköri alapon történő használatát. Az Azure dinamikusan foglal le egy S-címkét az áramkör létrehozásakor, és nem módosítható. Az áramkör (Private és Microsoft) minden egyes társítása egyedi C-címkét használ a VLAN-hoz. A C-címkének nem kell egyedinek lennie a ExpressRoute közvetlen portjain lévő áramkörökben.

* A **DOT1Q VLAN-címkézés** lehetővé teszi, hogy egyetlen címkézett VLAN legyen a ExpressRoute Direct port pár alapján. A ExpressRoute használt C-címkének egyedinek kell lennie az összes áramkörben és a közvetlen porton belül.

## <a name="workflow"></a>Munkafolyamat

[![munkafolyamat](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

A ExpressRoute Direct a Microsoft globális hálózatának aktív/aktív redundáns kapcsolataival azonos nagyvállalati szintű SLA-t biztosít. A ExpressRoute-infrastruktúra redundáns és a Microsoft globális hálózattal való kapcsolat redundáns és sokrétű, és ennek megfelelően méretezhető az ügyfelek igényei szerint. 

## <a name="next-steps"></a>Következő lépések

[Az ExpressRoute Direct konfigurálása](expressroute-howto-erdirect.md)
