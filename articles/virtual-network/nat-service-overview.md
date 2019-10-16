---
title: Mi az Azure NAT szolgáltatás?
titlesuffix: Azure NAT
description: Az Azure NAT szolgáltatás funkcióinak, architektúrájának és megvalósításának áttekintése. Ismerje meg, hogyan működik a NAT szolgáltatás, és hogyan használható a felhőben.
services: nat
documentationcenter: na
author: asudbring
ms.service: nat
Customer intent: As an IT administrator, I want to learn more about the Azure NAT service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/21/2019
ms.author: allensu
ms.openlocfilehash: 04464716384c1700571096443963499b0ca1b261
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376503"
---
# <a name="what-is-azure-nat-service-public-preview"></a>Mi az Azure NAT szolgáltatás (nyilvános előzetes verzió)

Az Azure NAT szolgáltatásával kimenő kapcsolatokat biztosíthat a nyilvános IP-címekhez a virtuális hálózaton lévő összes virtuális géphez. A NAT szolgáltatás különböző típusú munkaterheléseket tud biztosítani azáltal, hogy igény szerinti fordítások nélkül biztosítja a fordításokat.

Megadhatja, hogy egy virtuális hálózat mely alhálózatai használják a NAT szolgáltatást. A kimenő kapcsolatok egy vagy több IP-címmel is méretezhetők. Az üresjárati időkorlátot pedig 4 – 120 percig is beállíthatja. A NAT szolgáltatás a TCP alaphelyzetbe állítását is elküldi, ha az inaktív kapcsolatok újra fel lesznek használva, mivel az Üresjárati időkorlát lejárta után lenne. 

[Availability Zones](../availability-zones/az-overview.md) forgatókönyvek létrehozásához helyezze el a NAT szolgáltatást egy adott rendelkezésre állási zónában.

A NAT szolgáltatás felügyelt, rugalmas szolgáltatást biztosít a kimenő kapcsolatok számára. A NAT szolgáltatást a standard nyilvános IP-cím erőforrásaival és a standard Load balancerrel kombinálva további bejövő forgatókönyveket hozhat létre.

>[!NOTE] 
>Az Azure NAT szolgáltatás jelenleg nyilvános előzetes verzióként érhető el. Jelenleg csak korlátozott számú [régióban](#regions)érhető el. Ez az előzetes verzió szolgáltatási szintű szerződés nélkül érhető el, ezért nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.comsupport/legal/preview-supplemental-terms).

## <a name="nat-gateways"></a>NAT-átjárók

Az Azure NAT szolgáltatás NAT Gateway-erőforrást biztosít. Ez az erőforrás egy vagy több nyilvános IP-címmel konfigurálható a [standard nyilvános IP-cím](../virtual-network/virtual-network-ip-addresses-overview-arm.md#standard) vagy a [standard nyilvános IP-előtag](../virtual-network/public-ip-address-prefix.md) erőforrásaival vagy mindkettővel. A NAT szolgáltatást úgy használhatja, hogy egy virtuális hálózati erőforrás alhálózatait egy adott NAT-átjáró használatára konfigurálja. Egy NAT-átjáró ugyanazon virtuális hálózat több alhálózatán is használható. A különböző virtuális hálózatoknak külön NAT-átjáróra van szükségük.

Az alhálózat a NAT-átjáró használatával indul el a kimenő kapcsolatokhoz, ha egy virtuális hálózat alhálózatához van konfigurálva. A NAT-átjáró lecseréli egy alhálózat alapértelmezett internetes célját, és egy UDR konfigurációját nem szükséges. A kimenő fordítások igény szerint, virtuális gépek létrehozásával érhetők el.

>[!IMPORTANT]
>A kimenő folyamatok nem lesznek sikeresek, amíg nem rendel hozzá legalább egy nyilvános IP-címet a NAT-átjáróhoz.

>[!NOTE]
> Ha a virtuális gépek kiosztása garantált, vagy a kimenő kapcsolatok meghatározásának részletességére van szükség egy példány szintjén, vagy a példányok vagy a jótállási forrás hálózati címfordítási (SNAT) portok egy adott példányra, használja a [standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) a [kimenő szabályokkal](../load-balancer/load-balancer-outbound-rules-overview.md) a Load Balancer Pool-alapú kimenő forrás hálózati címfordítás (SNAT) definiálásához.   Ez a rugalmasság jelentős mértékben növeli az összetettséget és a további tervezést, ha a különböző munkaterhelések kombinálhatók, vagy a munkaterhelések kimenő kapcsolata nem lehet pontosan méretezni az egyes példányokat.

## <a name="combination-of-inbound-and-outbound-scenarios-and-skus"></a>A bejövő és kimenő forgatókönyvek és az SKU-megoldások kombinációja

A NAT-átjáróval azonos alhálózatokon létrehozhat bejövő és kimenő forgatókönyveket, ha a NAT-átjárót a szabványos nyilvános IP-címekkel együtt egyesíti a virtuális és standard nyilvános terheléselosztó-végpontokhoz, beleértve a rajtuk alapuló szolgáltatásokat is. A NAT-átjáró és a standard nyilvános IP-cím és a standard nyilvános Load Balancer is a flow irányának ismerete. A kimenő folyamatok esetében a NAT-átjáró az összes többi kimenő forgatókönyvet felváltja, és elsőbbséget élvez a virtuális gépeken [Load Balancer kimenő kapcsolat](../load-balancer/load-balancer-outbound-connections.md) és [a példány-szintű nyilvános IP-címek](../load-balancer/load-balancer-outbound-connections.md) tekintetében. A bejövő folyamatok, a példány szintű nyilvános IP-címek vagy a terheléselosztó nyilvános végpontjai egyszerre használhatók a NAT-átjáróval.  Ha ez a forgatókönyv elkészült, a bejövő/kimenő folyamatok ugyanazon alhálózatokon lesznek sikeresek.  Az eredményül kapott forgatókönyv a bejövő forgatókönyvek esetében "biztonságos, alapértelmezés szerint", mivel NSG szükséges, és explicit módon engedélyeznie kell a bejövő forgalmat.

Lehetősége van arra, hogy kibontsa az üzemelő példányokat egy virtuális hálózatban az alhálózatokra, hogy az befolyásolja a kimenő viselkedést. Nyilvános IP-címeket is használhat a virtuális gépeken, illetve a terheléselosztó kimenő hálózati címfordításának (SNAT) az egyik alhálózaton és a NAT-átjárón lévő kimenő kapcsolatok esetében az adott alhálózat ugyanazon a virtuális hálózaton. Az erőforrásokkal rendelkező NAT-átjárók nem kombinálhatók alapszintű nyilvános IP-cím-erőforrások vagy alapszintű terheléselosztó-erőforrások használatával ugyanazon az alhálózaton. Az alapszintű SKU-erőforrásokat üzembe helyezheti ugyanazon virtuális hálózat egy másik alhálózatán.

## <a name="network-address-translation"></a>Hálózati címfordítás

A konfiguráció befejezését követően a NAT-átjáró a virtuális gép példányának magánhálózati IP-címeinek lefordításával minden TCP-és UDP-alkalmazáshoz szolgáltatja a port maszkolási forrás hálózati címfordítását (SNAT).

A magánhálózati IP-címekről érkező folyamatokat a rendszer egy vagy több nyilvános IP-címre fordítja le a konfiguráció alapján. A fordítás azért történik, mert a forrás magánhálózati IP-cím forrásának hálózati címfordítása (SNAT) egy nyilvános IP-címhez van módosítva. A port maszkolása azt jelenti, hogy a forrás port a fordítás után megkülönbözteti a folyamatokat, és leegyszerűsíti a több-egy/több leképezést. Az eredményül kapott forgalomnak a nyilvános IP-címtartomány alapján való kiosztása a forrásként szolgáló virtuális gépek által megkezdhető folyamatokhoz van rendelve. Több kapcsolat ugyanahhoz a cél nyilvános IP-címhez, IP protokollhoz és célport-kombinációhoz egy további portot használ.

Ha a virtuális gépek kimenő kapcsolatokat hajtanak végre ezeken az alhálózatokon, a forrás portjai igény szerint vannak megadva, és a folyamat befejeződése után szabadulnak fel, vagy a tétlen időtúllépés eléri. A nyilvános IP-címek 55 000 SNAT-portot biztosítanak, és több nyilvános IP-cím használatával is kibővíthető. A NAT-átjáró a kimenő kapcsolatok skálázásának növeléséhez megkísérli a portok használatát a különböző célhelyekre irányuló folyamatokhoz. 

Egy alhálózaton belül több számítási feladatot is megadhat egyszerűen.  A Azure Load Balancer kimenő kapcsolataitól eltérően nem kell előosztania vagy előkészítenie a virtuális gép SNAT-portjainak fogyasztásának legrosszabb esetét. Ehelyett a konfigurált alhálózatokon lévő összes virtuális gép kimenő kapcsolatainak teljes mennyiségét tervezi. Minden rendelkezésre álló portot igény szerint minden példányhoz meg kell osztani.

## <a name="timeout-and-tcp-resets"></a>Időtúllépés és TCP-visszaállítás

A NAT-átjáró üresjárati időtúllépéseket valósít meg, és a nem létező folyamatok esetében elküldi a TCP-alapértékeket.  Az időtúllépést 4 perc (alapértelmezett) és 120 perc között lehet konfigurálni. A rendszer az első TCP-csomagot adja vissza a forráshoz, amikor a NAT-átjáróhoz érkezik, és nem létezik megfelelő kapcsolat. Ha egy folyamat elérte az üresjárati időkorlátot, a folyamat el lesz távolítva a NAT-Átjáróból, és a port elérhetővé válik a létrehozandó következő folyamat számára. Ha például egy TCP-kapcsolat elérte az üresjárati időkorlátot, és egy már időtúllépést okozó kapcsolathoz további csomagok is láthatók, akkor a rendszer az első TCP-t küldi el a forrásnak.  Az alkalmazás a TCP-Keepalives használatával jelezheti és szinkronizálhatja a végponti állapotot, ha szükséges.

## <a name="availability-zones"></a>Rendelkezésre állási zónák

Ha szükséges, a NAT-átjárók egy adott rendelkezésre állási zónában helyezhetők el.  

A zónákhoz tartozó nyilvános IP-címnek meg kell egyeznie a NAT-átjáróval megegyező rendelkezésre állási zónával. A nyilvános IP-előtag nem támogatja a rendelkezésre állási zónák használatát, és nem használható.

A zónákhoz tartozó NAT-átjáró konfigurálásakor a NAT-átjáró adatsíkja a kért rendelkezésre állási zónához van igazítva és el van különítve.

Egy adott zónában található virtuális géppel igazíthatja a NAT-átjáró adatsíkjait.  Ezt az igazítást egy regionális alhálózat létrehozásával teheti meg, amely csak az alhálózathoz konfigurált NAT-átjáróval azonos zónában lévő virtuális gépeket tartalmazza.

A NAT-átjáró rendelkezésre állási zónájának elhelyezése nem módosítható, és nem alakítható át egy NAT-átjáró a területi rendszerről a régióra vagy a régióra.

## <a name="outbound-connection-service-comparison"></a>Kimenő kapcsolatok szolgáltatásának összehasonlítása

A NAT-átjáró és a Load Balancer kimenő kapcsolat különböző forgatókönyvekhez készült. Az egyszerűség, az igény szerinti kiosztás és a virtuális hálózati szint méretezése, illetve a Pool-centrikus, a példányok részletessége és a bonyolultabb forgatókönyvek a legfontosabb kompromisszumok.  Számos más viselkedési különbség és tervezési szempont is létezik.

A NAT szolgáltatás lehetővé teszik a kimenő kapcsolatok konfigurálását a virtuális hálózatok számára a [standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) készleten alapuló konfigurációja helyett a [Kimenő szabályok](../load-balancer/load-balancer-outbound-rules-overview.md)használatával.  A kimenő kapcsolatok egyszerűen konfigurálhatók és méretezhetők az alhálózatok számára, és a forgatókönyvek korábban nem hozhatók létre.

A NAT-szolgáltatás elsőbbséget élvez a terheléselosztó kimenő SNAT szemben, de a bejövő és kimenő forgatókönyvek esetében mindkettő kombinálhatók. Ha a bejövő forgatókönyveket nyilvános IP-címekkel és terheléselosztó-végpontokkal kombinálja, a szabványos nyilvános IP-cím és a standard Load Balancer esetében is kihasználhatja a "biztonság alapértelmezés szerint" lehetőséget.  Csak egy NSG létezése után, amely explicit módon engedélyezi a forgalmat, a bejövő forgalom engedélyezett lesz.

A NAT szolgáltatás kiszámítható kimenő kapcsolatot tud létrehozni a virtuális hálózat számára. Ha csak a kimenő kapcsolat szükséges, nem kell konfigurálnia a terheléselosztást a kimenő szabályokkal, csatlakoztatni a gépeket a készlethez, ki kell számítania és meg kell határoznia a SNAT, vagy ki kell osztania a nyilvános IP-címeket az egyes gépekhez. A terheléselosztó rendszergazdája helyett a NAT-átjáró erőforrás rendszergazdájához delegálhatja a kimenő kapcsolat felelősségét.

A NAT szolgáltatás lehetővé teszi a kimenő kapcsolatok megtervezését a virtuális hálózatok vagy alhálózatok maximális számítási feladataihoz, a virtuálisgép-példányok maximális skálázása helyett. További igényeket is igénybe vehet, ha a teljes virtuális hálózat összes alhálózata számára dinamikusan növeli a nyilvános IP-címek számát. A terheléselosztó SNAT-portok kiosztása és újratervezése helyett a NAT szolgáltatás megosztja a kimenő kapcsolathoz elérhető SNAT-portokat, és igény szerint benyújtja azokat a feltört vagy nem könnyen kiszámítható számítási feladatokhoz.  A Load Balancer egy adott virtuálisgép-példányhoz adott számú SNAT-portot előre foglal le.  

A NAT szolgáltatás mindig visszaadja a TCP-visszaállítási (első) csomagokat a küldőnek a nem létező folyamatokhoz (például Üresjárati időkorlát miatt). A standard Load Balancer konfigurálható úgy, hogy a [TCP alaphelyzetbe állítása](../load-balancer/load-balancer-tcp-reset.md)engedélyezve legyen, amely a TCP első csomagokat egy kapcsolat mindkét végpontján létrehozza az Üresjárati időkorlát időpontjában.

## <a name="regions"></a>Térségek

A NAT szolgáltatás ezekben a régiókban érhető el
- USA 2. keleti régiója
- USA nyugati középső régiója

## <a name="limitations"></a>Korlátozások

- A NAT szolgáltatás nem kompatibilis az alapszintű nyilvános IP-címekkel vagy az alapszintű Load balancerekkel ugyanazon az alhálózaton.  Léteznie kell egy NAT-átjáró által nem kiszolgált alhálózaton.
- A TCP-és UDP-alapú alkalmazás-protokollok támogatottak.
- A NAT-átjárók konfigurálhatók egy virtuális hálózat egy vagy több alhálózatán is.
- Az IPv6 nem támogatott.
- A nyilvános IP-előtag nem támogatja a többhelyes elhelyezést, és nem használható a Zona NAT-átjáróval.

## <a name="next-steps"></a>Következő lépések

- [Visszajelzés küldése a NAT szolgáltatás menetrendje számára](https://aka.ms/natuservoice) .
