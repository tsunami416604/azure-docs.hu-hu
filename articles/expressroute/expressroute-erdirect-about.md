---
title: Az Azure ExpressRoute Direct bemondója
description: Ez a lap áttekintést nyújt az ExpressRoute Direct
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f26fdd75e0a6c6228d329a8b9be4743072ae54c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083606"
---
# <a name="about-expressroute-direct"></a>Az ExpressRoute Direct ismertetése

Az ExpressRoute Direct lehetővé teszi, hogy közvetlenül csatlakozzon a Microsoft globális hálózatához a világszerte stratégiailag elosztott társviszony-létesítési helyeken. Az ExpressRoute Direct két 100 Gb/s vagy 10 Gb/s sebességű kapcsolatot biztosít, amely támogatja az aktív/aktív kapcsolatot.

Az ExpressRoute Direct által biztosított főbb szolgáltatások közé tartoznak, de nem korlátozódnak a következőkre:

* Nagy mennyiségű adat betöltése az olyan szolgáltatásokba, mint például a Storage és az Cosmos DB
* Fizikai elszigeteltség a szabályozott és célzott és elszigetelt összekapcsolhatóságot igénylő iparágak számára, mint például: bank, kormányzat és kiskereskedelem
* A kapcsolatcsoportok elosztásának üzleti egységen alapuló, részletes szabályzása

## <a name="onboard-to-expressroute-direct"></a>Alaplap az ExpressRoute Direct-be

Az ExpressRoute Direct használata előtt először regisztrálnia kell az előfizetését. A regisztráláshoz küldjön <ExpressRouteDirect@microsoft.com> egy e-mailt az előfizetésazonosítójával, amely tartalmazza az alábbi adatokat:

* Az **ExpressRoute Direct** teljének forgatókönyvei
* Helybeállítások – az összes hely teljes listájáért tekintse meg a [Partnerek és társviszony-létesítési helyek](expressroute-locations-providers.md) megtekintését
* Megvalósítási ütemterv
* Bármilyen egyéb kérdés

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute szolgáltató és ExpressRoute Direct használatával

| **ExpressRoute szolgáltató használatával** | **ExpressRoute Direct** | 
| --- | --- |
| A szolgáltatók segítségével lehetővé teszi a gyors bevezetést és a meglévő infrastruktúrához való kapcsolódást | 100 Gbps/10 Gbps infrastruktúrát és az összes réteg teljes körű felügyeletét igényli
| Több száz szolgáltatóval integrálható, beleértve az Ethernetet és az MPLS-t | Közvetlen/dedikált kapacitás a szabályozott iparágak és a tömeges adatbetöltés számára |
| Áramkörök sk-ek 50 Mbps és 10 Gb/s | Az ügyfél a következő áramköri suk-ok kombinációját választhatja ki a 100 Gbit/s-os ExpressRoute Direct-en: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbit/s</li></ul> Az ügyfél a következő áramköri suk-ok kombinációját választhatja ki 10 Gbit/s-os ExpressRoute Direct-en:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Egybérlős használatra optimalizálva | Egybérlős, több részleggel és több munkakörnyezettel rendelkező bérlőre optimalizálva

## <a name="expressroute-direct-circuits"></a>ExpressRoute közvetlen áramkörök

A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute segítségével kapcsolatokat létesíthet a Microsoft felhőszolgáltatásaival, például a Microsoft Azure-ral és az Office 365-tel.

Minden társviszony-létesítési hely hozzáfér a Microsoft globális hálózatához, és alapértelmezés szerint hozzáférhet a geopolitikai zónák bármely régiójához, és prémium szintű kapcsolatokkal rendelkező összes globális régióhoz hozzáférhet.  

A legtöbb esetben a funkció egyenértékű az ExpressRoute-szolgáltatót használó áramkörökműködésével. Az ExpressRoute Direct használatával kínált további részletesség és új képességek támogatása érdekében vannak bizonyos kulcsfontosságú képességek, amelyek az ExpressRoute direct-áramköröken léteznek.

## <a name="circuit-skus"></a>Áramkör SKUs

Az ExpressRoute Direct támogatja a tömeges adatbetöltési forgatókönyveket az Azure storage-ba és más big data-szolgáltatásokba. A 100 Gb/s-os ExpressRoute Direct ExpressRoute-áramkörei mostantól **40 Gbit/s és** **100 Gbit/s-os áramköri** suk-okat is támogatnak. A fizikai portpárok csak **100 vagy 10 Gb/s,** és több virtuális kapcsolatcsoport is lehet. Áramkörméretek:

| **100 Gbit/s sebességű ExpressRoute Direct** | **10 Gbit/s sebességű ExpressRoute Direct** | 
| --- | --- |
| **Feliratkozott sávszélesség:** 200 Gbps | **Feliratkozott sávszélesség:** 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbit/s</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Műszaki követelmények

* Microsoft Enterprise Edge útválasztó (MSEE) összeköttetések:
    * Két 10 vagy 100 gigabites Ethernet port csak az útválasztópáron keresztül
    * Egymódusú LR Fiber kapcsolat
    * IPv4 és IPv6
    * IP MTU 1500 bájt

* Kapcsoló/útválasztó réteg 2/3.
    * Támogatnia kell 1 802.1Q (Dot1Q) címke vagy két Tag 802.1Q (QinQ) tag beágyazása
    * Étertípus = 0x8100
    * Hozzá kell adnia a külső VLAN címkét (STAG) a Microsoft által megadott VLAN-azonosító alapján - *csak a QinQ-n alkalmazható*
    * Portonként és eszközönként több BGP-munkamenetet (VLAN) kell támogatnia
    * IPv4- és IPv6-kapcsolat. *Az IPv6-hoz nem jön létre további alkapcsolat. Az IPv6-cím hozzáadódik a meglévő alkapcsolathoz.* 
    * Nem kötelező: [Kétirányú továbbításészlelése (BFD)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) támogatása, amely alapértelmezés szerint az ExpressRoute-áramkörök összes privát társviszony-létesítésén konfigurálva van

## <a name="vlan-tagging"></a>VLAN címkézés

Az ExpressRoute Direct támogatja a QinQ és a Dot1Q VLAN címkézést is.

* **A QinQ VLAN tagging** lehetővé teszi az elkülönített útválasztási tartományokat ExpressRoute-kapcsolatonkénti alapon. Az Azure dinamikusan lefoglal egy S-tag a kör létrehozása, és nem módosítható. Minden társviszony-létesítés a kör (Private és a Microsoft) fogja használni egy egyedi C-Tag, mint a VLAN. A C-Tag nem szükséges, hogy egyedi áramkörök között az ExpressRoute Direct portok.

* **A Dot1Q VLAN Tagging** lehetővé teszi egyetlen címkézett VLAN-t expressroute Direct portpáronként. A társviszony-létesítéshez használt C-tagnak egyedinek kell lennie az ExpressRoute Direct portpár összes áramkörén és társviszony-létesítésén.

## <a name="workflow"></a>Munkafolyamat

[![Munkafolyamat](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

Az ExpressRoute Direct ugyanazt a nagyvállalati szintű SLA-t biztosítja aktív/aktív redundáns kapcsolatokkal a Microsoft Global Network hálózatba. Az ExpressRoute-infrastruktúra redundáns, és a Microsoft Global Network hálózathoz való kapcsolódás redundáns, változatos, és az ügyféligényeknek megfelelően méretezhető. 

## <a name="next-steps"></a>További lépések

[Az ExpressRoute Direct konfigurálása](expressroute-howto-erdirect.md)
