---
title: Mi az Azure virtuális hálózati hálózati hálózati szerződési szolgáltatás?
titlesuffix: Azure Virtual Network
description: Virtuális hálózati NAT-szolgáltatások, erőforrások, architektúra és megvalósítás áttekintése. Ismerje meg, hogyan működik a virtuális hálózati nat, és hogyan használhatja a NAT-átjáró erőforrásait a felhőben.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2020
ms.author: allensu
ms.openlocfilehash: 4b34d4208d8686cdac3f8164d2cf7efb2d881346
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79409898"
---
# <a name="what-is-virtual-network-nat"></a>Mi az a virtuális hálózati NAT?

A virtuális hálózati hálózati címfordítás (hálózati címfordítás) leegyszerűsíti a virtuális hálózatok csak kimenő internetkapcsolatát. Ha egy alhálózaton konfigurálva van, az összes kimenő kapcsolat a megadott statikus nyilvános IP-címeket használja.  Kimenő kapcsolat lehetséges terheléselosztó vagy nyilvános IP-címek közvetlenül a virtuális gépekhez csatlakoztatott nélkül. A NAT teljes mértékben felügyelt és rendkívül rugalmas.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" width="256" title="Virtuális hálózati NAT">
</p>



*Ábra: Virtuális hálózati NAT*

## <a name="static-ip-addresses-for-outbound-only"></a>Statikus IP-címek csak kimenő célokra

Kimenő kapcsolat definiálható minden nat-tal rendelkező alhálózathoz.  Ugyanazon a virtuális hálózaton belül több alhálózat különböző NAT-okkal rendelkezhet. Az alhálózat konfigurálása a használandó [NAT-átjáró-erőforrás](./nat-gateway-resource.md) megadásával történik. Bármely virtuálisgép-példány összes UDP- és TCP kimenő folyamata nat-t fog használni. 

A NAT kompatibilis a szabványos termékváltozat [nyilvános IP-cím erőforrásaival](./virtual-network-ip-addresses-overview-arm.md#standard) vagy [nyilvános IP-előtag-erőforrásokkal,](./public-ip-address-prefix.md) illetve ezek kombinációjával.  Nyilvános IP-előtag közvetlenül is használható, vagy az előtag nyilvános IP-címeit több NAT-átjáró-erőforrás között is eloszthatja. A NAT az előtag IP-címtartományba irányuló összes forgalmat ápolja.  A központi telepítések IP-engedélyezési listája mostantól egyszerű.

Az alhálózat összes kimenő forgalmát a NAT automatikusan, ügyfélkonfiguráció nélkül dolgozza fel.  A felhasználó által definiált útvonalaknem szükségesek. A NAT elsőbbséget élvez a többi [kimenő forgatókönyvvel szemben,](../load-balancer/load-balancer-outbound-connections.md) és felváltja az alhálózat alapértelmezett internetes célját.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>Igény szerinti SNAT több IP-címmel a méretezéshez

A NAT a "porthálózati címfordítást" (PNAT vagy PAT) használja, és a legtöbb számítási feladathoz ajánlott. A dinamikus vagy eltérő számítási feladatok könnyen elhelyezhetők az igény szerinti kimenő forgalom allokációjával. A kimenő erőforrások széles körű előtervezése, előre felosztása és végső soron túlterhelése elkerülhető. Az SNAT-port erőforrásai meg vannak osztva, és az összes alhálózat között elérhetők egy adott NAT-átjáró-erőforrás használatával, és szükség esetén rendelkezésre állnak.

A NAT-hoz csatolt nyilvános IP-cím legfeljebb 64 000 egyidejű folyamatot biztosít az UDP és a TCP számára. Egyetlen IP-címmel kezdheti, és legfeljebb 16 nyilvános IP-címet skálázhat.

A NAT lehetővé teszi a virtuális hálózatról az internetre irányuló folyamatok létrehozását. Az internetről érkező forgalom visszaadása csak aktív folyamatra válaszul engedélyezett.

A terheléselosztó kimenő SNAT-tal ellentétben a NAT-nak nincsenek korlátozásai arra vonatkozóan, hogy egy virtuálisgép-példány privát IP-címe melyik kimenő kapcsolatot hozhat létre.  A másodlagos IP-konfigurációk kimenő internetkapcsolatot hozhatnak létre a NAT-tal.

## <a name="coexistence-of-inbound-and-outbound"></a>A bejövő és kimenő konélmények együttélése

A NAT a következő szabványos termékváltozat-erőforrásokkal kompatibilis:

- [Load Balancer](../load-balancer/load-balancer-overview.md)
- [Nyilvános IP-cím](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Nyilvános IP-előtag](../virtual-network/public-ip-address-prefix.md)

A NAT-tal együtt használva ezek az erőforrások bejövő internetkapcsolatot biztosítanak az alhálózat(ok)hoz. A NAT az alhálózat(ok) összes kimenő internetkapcsolatát biztosítja.

A NAT és a kompatibilis standard termékváltozat-szolgáltatások tisztában vannak a folyamat elindításának irányával. A bejövő és a kimenő forgatókönyvek egymás mellett létezhetnek. Ezek a forgatókönyvek a megfelelő hálózati címfordításokat kapják, mivel ezek a szolgáltatások tisztában vannak a folyamat irányával. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" width="512" title="Virtuális hálózati nat-folyamat iránya">
</p>

*Ábra: Virtuális hálózati NAT-folyamat iránya*

## <a name="fully-managed-highly-resilient"></a>Teljes körűen irányított, rendkívül rugalmas

A NAT a kezdetektől teljesen ki van kicsinyített. Nincs szükség felföldi vagy kibővített műveletre.  Az Azure kezeli a NAT működését.  A NAT mindig több tartalék tartománnyal rendelkezik, és több hiba is képes fenntartani a szolgáltatás kimaradása nélkül.

## <a name="tcp-reset-for-unrecognized-flows"></a>TCP reset ismeretlen folyamatok esetén

A NAT privát oldala TCP Reset csomagokat küld a nem létező TCP-kapcsolaton való kommunikációra irányuló kísérletekhez. Erre példa az olyan kapcsolatok, amelyek elérték az tétlen időoutot. A következő fogadott csomag egy TCP reset-et ad vissza a privát IP-címre a kapcsolat lezárásának jelzésére és kényszerítésére.

A NAT nyilvános oldala nem hoz létre TCP Reset csomagokat vagy más forgalmat.  Csak az ügyfél virtuális hálózata által termelt forgalom kerül kibocsátásra.

## <a name="configurable-tcp-idle-timeout"></a>Konfigurálható TCP tétlen időmeghosszabbítás

A rendszer egy 4 perces alapértelmezett TCP-időoutot használ, amely akár 120 percre is növelhető. A folyamatminden tevékenysége visszaállíthatja az alapjárati időzítőt is, beleértve a TCP keepalives-t is.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Regionális vagy zónaelkülönítés rendelkezésre állási zónákkal

A NAT alapértelmezés szerint regionális. [Rendelkezésre állási zónák forgatókönyvek](../availability-zones/az-overview.md) létrehozásakor a NAT elkülöníthető egy adott zónában (zónaszintű központi telepítés).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" width="512" title="Virtuális hálózati hálózati hálózati kapcsolat rendelkezésre állási zónákkal">
</p>

*Ábra: Virtuális hálózati hálózati hálózati kapcsolat rendelkezésre állási zónákkal*

## <a name="multi-dimensional-metrics-for-observability"></a>Többdimenziós mérőszámok a megfigyelhetőség érdekében

A NAT működését az Azure Monitorban elérhetőtöbbdimenziós metrikák segítségével figyelheti. Ezek a mérőszámok a használat megfigyelésére és a hibaelhárításra használhatók.  A NAT-átjáró-erőforrások a következő metrikákat teszik elérhetővé:
- Bájt
- Csomagok
- Eldobott csomagok
- Összes SNAT-kapcsolat
- Az SNAT-kapcsolat állapota intervallumonként áttűnések.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>SLA

Általános elérhetőség esetén a NAT-adatok elérési útja legalább 99,9%.


## <a name="pricing"></a>Díjszabás

A NAT átjáró számlázása két külön méter:

| Mérő | Rate (Egységár) |
| --- | --- |
| Erőforrás-órák | $0.045/óra |
| Feldolgozott adatok | $0,045/GB |

Az erőforrásórák azt az időtartamot számlálják, amely alatt a NAT-átjáró-erőforrás létezik.
A NAT-átjáró-erőforrás által feldolgozott összes forgalom feldolgozott adatfeldolgozási fiókok.

## <a name="availability"></a>Rendelkezésre állás

Virtual Network NAT and the NAT gateway resource are available in all Azure public cloud [regions](https://azure.microsoft.com/global-infrastructure/regions/).

## <a name="support"></a>Támogatás

A NAT normál támogatási csatornákon keresztül támogatott.

## <a name="feedback"></a>Visszajelzés

Azt akarjuk tudni, hogyan tudjuk javítani a szolgáltatást. Javaslatot tesz, és szavazni, hogy mit kell építeni a következő [UserVoice a NAT](https://aka.ms/natuservoice).


## <a name="limitations"></a>Korlátozások

* A NAT kompatibilis a szabványos termékváltozat nyilvános IP-címével, nyilvános IP-előtaggal és terheléselosztó erőforrásokkal. Az alapvető erőforrások, például az alapterhelés-elosztó és az ezekből származó termékek nem kompatibilisek a NAT-tal.  Az alapvető erőforrásokat olyan alhálózaton kell elhelyezni, amely nincs konfigurálva a Hálózati címtón.
* Az IPv4-címcsalád támogatott.  A NAT nem lép kapcsolatba az IPv6-címcsaláddal.  A hálózati címt nem lehet iPv6-előtaggal rendelkező alhálózaton telepíteni.
* NSG-folyamatnaplózás nem támogatott, ha nat használatával.
* A Hálózati atta nem terjedhet ki több virtuális hálózatra.

## <a name="next-steps"></a>További lépések

* További információ a [NAT átjáró-erőforrásról.](./nat-gateway-resource.md)
* [Mondja el, mit kell építeni a következő virtuális hálózati NAT a UserVoice](https://aka.ms/natuservoice).

