---
title: Mi az Azure standard Load Balancer?
titleSuffix: Azure Load Balancer
description: Ezzel a képzési útvonallal megismerheti az Azure standard Load Balancer funkcióinak áttekintését.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: allensu
ms.openlocfilehash: b4db0746f64bfde04f09b589ac6beb6dc74d13f2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978682"
---
# <a name="azure-standard-load-balancer-overview"></a>Az Azure standard Load Balancer áttekintése

Azure Load Balancer lehetővé teszi az alkalmazások skálázását és magas rendelkezésre állású szolgáltatások létrehozását. A Load Balancer használható a bejövő és a kimenő forgatókönyvekhez, valamint alacsony késést és nagy átviteli sebességet biztosít, és akár akár több millió folyamatot is képes az összes TCP-és UDP-alkalmazáshoz. 

Ez a cikk standard Load Balancerra összpontosít.  A Azure Load Balancer általános áttekintéséhez tekintse át az [Load Balancer áttekintését](load-balancer-overview.md) is.

## <a name="what-is-standard-load-balancer"></a>Mi az a standard Load Balancer?

A standard Load Balancer egy új Load Balancer termék minden TCP-és UDP-alkalmazáshoz, és az alapszintű Load Balanceron beállított bővített és részletesebb funkciókkal rendelkezik.  Habár sok hasonlóság van, fontos, hogy megismerkedjen a jelen cikkben ismertetett különbségekkel.

A standard Load Balancer nyilvános vagy belső Load Balancer is használhatja. A virtuális gépek egy nyilvános és egy belső Load Balancer erőforráshoz is csatlakoztathatók.

A Load Balancer erőforrás funkciói mindig előtérként, szabályként, állapot-mintavételként és háttérbeli készlet-definícióként vannak kifejezve.  Egy erőforrás több szabályt is tartalmazhat. A virtuális gépeket a háttér-készletbe helyezheti a háttér-készletnek a virtuális gép NIC-erőforrásból való megadásával.  Ezt a paramétert a hálózati profil továbbítja, és kibontja a virtuálisgép-méretezési csoportok használatakor.

Az egyik kulcsfontosságú szempont az erőforráshoz tartozó virtuális hálózat hatóköre.  Míg az alapszintű Load Balancer a rendelkezésre állási csoport hatókörén belül van, a standard Load Balancer teljes mértékben integrálva van a virtuális hálózat és az összes virtuális hálózati koncepció alkalmazási körébe.

Load Balancer erőforrások olyan objektumok, amelyeken belül kipróbálhatja, hogy az Azure hogyan programba hozza a több-bérlős infrastruktúrát a létrehozni kívánt forgatókönyv eléréséhez.  Nincs közvetlen kapcsolat a Load Balancer erőforrások és a tényleges infrastruktúra között; Load Balancer létrehozása nem hoz létre példányt, a kapacitás mindig rendelkezésre áll, és nincs szükség indítási vagy méretezési késésre. 

### <a name="backend"></a>Háttér-készlet

Standard Load Balancer háttér-készletek a virtuális hálózat bármely virtuálisgép-erőforrására kiterjeszthetők.  Akár 1000 háttér-példányt is tartalmazhat.  A backend-példány egy IP-konfiguráció, amely egy hálózati adapter erőforrásának tulajdonsága.

A háttér-készlet különálló virtuális gépeket, rendelkezésre állási csoportokat vagy virtuálisgép-méretezési csoportokat tartalmazhat.  Az erőforrásokat a háttér-készletben is felhasználhatja. Load Balancer erőforrásban akár 150 erőforrást is összekapcsolhat a háttér-készletben.

A háttérbeli készlet kialakításának megtervezése során a felügyeleti műveletek időtartamának további optimalizálása érdekében megtervezheti a minimális számú egyéni háttér-készlet erőforrását.  Nem különbözik az adatsíkok teljesítményének vagy méretezésének.

### <a name="probes"></a>Állapot-mintavételek
  
A standard Load Balancer támogatja a [https-állapotú](load-balancer-custom-probe-overview.md#httpprobe) mintavételeket (http-mintavétel TRANSPORT Layer Security (TLS) burkolóval) a https-alkalmazások pontos figyeléséhez.  

Emellett, ha a teljes háttérrendszer- [készlet](load-balancer-custom-probe-overview.md#probedown)mintavételt végez, standard Load Balancer lehetővé teszi az összes létesített TCP-kapcsolat folytatását. (Az alapszintű Load Balancer leállítja az összes TCP-kapcsolatot az összes példányra).

A részletekért tekintse át [Load Balancer Health-szondákat](load-balancer-custom-probe-overview.md) .

### <a name="az"></a>Availability Zones

>[!IMPORTANT]
>Tekintse át [Availability Zones](../availability-zones/az-overview.md) kapcsolódó témaköröket, beleértve a régióra vonatkozó információkat is.

Standard Load Balancer támogatja a további képességeket azokban a régiókban, ahol Availability Zones elérhető.  Ezek a funkciók minden standard Load Balancer számára növekményes.  Availability Zones konfigurációk nyilvános és belső standard Load Balancer számára érhetők el.

A nem zónákra kiterjedő frontendek alapértelmezés szerint a Availability Zones-vel rendelkező régiókban való üzembe helyezéskor lesznek redundánsak.   A Zone-redundáns frontend megmarad a zóna meghibásodása esetén, és az összes zónában egyidejűleg dedikált infrastruktúra szolgálja ki. 

Emellett egy adott zónához is garantálhatja a felületet. A zónákhoz tartozó előtér-megosztások a megfelelő zónával rendelkeznek, és csak dedikált infrastruktúra szolgálnak ki egyetlen zónában.

A többzónás terheléselosztás elérhető a háttér-készlethez, és a vnet található bármely virtuálisgép-erőforrás egy háttér-készlet része lehet.

Tekintse át [Availability Zones kapcsolódó képességeinek részletes megvitatását](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a>Diagnosztika

Standard Load Balancer többdimenziós metrikákat biztosít Azure Monitoron keresztül.  Ezek a metrikák szűrhetők, csoportosíthatók és kibonthatók egy adott dimenzióra vonatkozóan.  A szolgáltatás teljesítményének és állapotának aktuális és korábbi betekintést nyújtanak.  A Resource Health is támogatott.  A következő rövid áttekintést nyújt a támogatott diagnosztika szolgáltatásról:

| Metrika | Leírás |
| --- | --- |
| VIP-elérhetőség | Standard Load Balancer folyamatosan gyakorolja az adatelérési utat a régión belülről a Load Balancer kezelőfelületre egészen az SDN-veremig, amely támogatja a virtuális gépet. Amíg a kifogástalan állapotú példányok megmaradnak, a mérés ugyanazt az útvonalat követi, mint az alkalmazás elosztott terhelésű forgalma. Az ügyfelek által használt adatelérési út is érvényesítve lesz. A mérték láthatatlan az alkalmazás számára, és nem zavarja a többi műveletet.|
| DIP rendelkezésre állása | A standard Load Balancer egy elosztott állapot-ellenőrzési szolgáltatást használ, amely figyeli az alkalmazás-végpont állapotát a konfigurációs beállításoknak megfelelően. Ez a metrika összesített vagy végpontilag szűrt nézetet biztosít a Load Balancer készlet minden egyes példány-végpontja számára.  Láthatja, hogyan tekintheti meg Load Balancer az alkalmazás állapotát az állapot mintavételi konfigurációjának megfelelően.
| SYN-csomagok | Standard Load Balancer nem szakítja meg a TCP-kapcsolatokat, vagy nem kommunikál a TCP-vagy UDP-csomagok forgalmával. A folyamatok és a kézfogások mindig a forrás és a virtuálisgép-példány között vannak. A TCP protokollal kapcsolatos forgatókönyvek jobb megoldásához használhatja az SYN-csomagok számlálóit, hogy megtudja, hány TCP-kapcsolati kísérletet hajt végre a rendszer. A metrika a fogadott TCP SYN-csomagok számát jelenti.|
| SNAT-kapcsolatok | Standard Load Balancer a nyilvános IP-címhez tartozó kimenő folyamatok számát jelenti. A SNAT-portok kimeríthető erőforrások. Ez a metrika arra utalhat, hogy az alkalmazás milyen mértékben támaszkodik a SNAT a kimenő folyamatokból.  A sikeres és sikertelen kimenő SNAT folyamatokra vonatkozó számlálókat jelentettek, és felhasználhatók a kimenő folyamatok állapotának hibakeresésére és megismerésére.|
| Bájtok számlálói | Standard Load Balancer az előtérben feldolgozott adatmennyiséget jelenti.|
| Csomagok számlálói | Standard Load Balancer jelentést készít az előtér által feldolgozott csomagokról.|

Tekintse át [standard Load Balancer diagnosztika részletes megvitatását](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>HA portok

Standard Load Balancer támogatja az új típusú szabályt.  

Konfigurálhatja a terheléselosztási szabályokat, hogy az alkalmazások méretezése és megbízhatósága megbízható legyen. Ha ha egy ha portok terheléselosztási szabályt használ, a standard Load Balancer a belső standard Load Balancer előtérbeli IP-címének minden ideiglenes portján adatfolyam-terheléselosztást fog biztosítani.  A szolgáltatás más esetekben hasznos, ha nem praktikus vagy nemkívánatos az egyes portok megadására.

Egy HA portos terheléselosztási szabály lehetővé teszi, hogy aktív-passzív vagy aktív-aktív n + 1 forgatókönyvet hozzon létre a hálózati virtuális berendezésekhez és bármely olyan alkalmazáshoz, amelyhez nagy tartományú bejövő portok szükségesek.  Az állapot-mintavétel segítségével meghatározhatja, hogy mely háttérrendszer kapjon új folyamatokat.  A porttartomány-forgatókönyvek emulálása hálózati biztonsági csoporttal végezhető el.

>[!IMPORTANT]
> Ha hálózati virtuális berendezést szeretne használni, a gyártótól tájékozódhat arról, hogy a termékük a HA portokkal lett tesztelve, és követik a megvalósításra vonatkozó útmutatást. 

Tekintse át [a ha-portok részletes megvitatását](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Alapértelmezés szerint biztonságos

Standard Load Balancer teljes mértékben bekerül a virtuális hálózatba.  A virtuális hálózat egy privát, zárt hálózat.  Mivel a standard Load Balancer és a standard nyilvános IP-címek úgy vannak kialakítva, hogy a virtuális hálózat a virtuális hálózaton kívülről is elérhető legyen, ezek az erőforrások mostantól alapértelmezés szerint le lesznek zárva, kivéve, ha megnyitja őket. Ez azt jelenti, hogy a hálózati biztonsági csoportok (NSG-EK) mostantól lehetővé teszik az engedélyezett forgalom explicit módon történő engedélyezését és engedélyezési módját.  Létrehozhatja a teljes virtuális adatközpontot, és eldöntheti, hogy milyen és mikor legyen elérhető a NSG.  Ha nem rendelkezik NSG a virtuális gép erőforrásának alhálózatán vagy hálózati ADAPTERén, a forgalom nem jogosult az erőforrás elérésére.

Ha többet szeretne megtudni a NSG és a forgatókönyvre való alkalmazásáról, tekintse meg a [hálózati biztonsági csoportok](../virtual-network/security-overview.md)című témakört.

### <a name="outbound"></a>Kimenő kapcsolatok

Load Balancer támogatja a bejövő és kimenő forgatókönyveket.  Standard Load Balancer jelentősen eltér az alapszintű Load Balancer a kimenő kapcsolatokkal kapcsolatban.

A forrás hálózati címfordítás (SNAT) a belső, magánhálózati IP-címek a virtuális hálózaton a nyilvános IP-címekre való leképezésére szolgál Load Balancer felületeken.

A standard Load Balancer egy új algoritmust vezet be egy [robusztusabb, méretezhető és kiszámítható SNAT algoritmushoz](load-balancer-outbound-connections.md#snat) , és lehetővé teszi az új képességeket, eltávolítja a kétértelműség és az explicit konfigurációkat. Ezek a változtatások szükségesek ahhoz, hogy az új funkciók felmerülhetnek. 

Ezek a legfontosabb alapelvek a standard Load Balancer használatának megemlékezéséhez:

- a szabály befejezése a Load Balancer erőforrást vezeti.  Az Azure minden programozása a konfigurációtól származik.
- Ha több előtér áll rendelkezésre, a rendszer az összes előtérbeli felületet felhasználja, és minden egyes előtér megszorozza az elérhető SNAT-portok számát
- kiválaszthatja és szabályozhatja, hogy nem kíván-e használni egy adott előtér-felületet a kimenő kapcsolatokhoz.
- a kimenő forgatókönyvek explicit módon vannak megadva, és a kimenő kapcsolatok addig nem léteznek, amíg meg nem adták.
- a terheléselosztási szabályok azt a következtetést jelentik, hogy a SNAT hogyan van programozva. A terheléselosztási szabályok a protokoll-specifikusak. A SNAT a protokoll-specifikus, és a konfigurációnak tükröznie kell ezt ahelyett, hogy mellékhatást kellene létrehoznia.

#### <a name="multiple-frontends"></a>Több előtérrendszer
Ha további SNAT-portokra van szüksége, mert várt vagy már magas keresletet tapasztal a kimenő kapcsolatok esetében, növekményes SNAT is hozzáadhat, ha további előtér-, szabály-és háttér-készleteket is konfigurál ugyanahhoz a virtuális géphez. erőforrások.

#### <a name="control-which-frontend-is-used-for-outbound"></a>A kimenő előtérbeli felület szabályozása
Ha korlátozni szeretné a kimenő kapcsolatokat, hogy csak egy adott előtér-IP-címről származzanak, akkor letilthatja a kimenő SNAT a kimenő leképezést kifejező szabályon.

#### <a name="control-outbound-connectivity"></a>Kimenő kapcsolat vezérlése
Standard Load Balancer létezik a virtuális hálózat kontextusában.  A virtuális hálózat egy elkülönített, magánhálózati hálózat.  Ha nem létezik nyilvános IP-címmel rendelkező társítás, a nyilvános kapcsolat nem engedélyezett.  Elérheti a [VNet szolgáltatás-végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) , mivel azok a virtuális hálózatban és a helyi hálózaton belül vannak.  Ha kimenő kapcsolatot szeretne létesíteni a virtuális hálózatán kívüli célhoz, két lehetőség közül választhat:
- rendeljen hozzá egy standard SKU nyilvános IP-címet a virtuálisgép-erőforráshoz tartozó példány-szintű nyilvános IP-címnek
- Helyezze a virtuális gép erőforrását egy nyilvános standard Load Balancer háttér-készletéből.

Mindkettő lehetővé teszi a virtuális hálózat kimenő kapcsolatát a virtuális hálózaton kívülre. 

Ha _csak_ belső standard Load Balancer van társítva ahhoz a háttér-készlethez, amelyben a virtuális gép erőforrása található, a virtuális gép csak virtuális hálózati erőforrásokat és [VNet-végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md)érhet el.  A kimenő kapcsolatok létrehozásához kövesse az előző bekezdésben ismertetett lépéseket.

A standard SKU-hoz nem társított virtuálisgép-erőforrások kimenő kapcsolata korábban is megmarad.

Tekintse át [a kimenő kapcsolatok részletes megvitatását](load-balancer-outbound-connections.md).

### <a name="multife"></a>Több frontend
Load Balancer több előtérbeli felülettel rendelkező szabályt is támogat.  Standard Load Balancer kibontja ezt a kimenő forgatókönyvek számára.  A kimenő forgatókönyvek lényegében egy bejövő terheléselosztási szabály inverze.  A bejövő terheléselosztási szabály is létrehoz egy hozzárendelést a kimenő kapcsolatok számára. Standard Load Balancer a virtuálisgép-erőforrásokhoz társított összes előtérbeli felületet egy terheléselosztási szabályon keresztül használja.  Emellett a terheléselosztási szabály egyik paramétere, amely lehetővé teszi egy terheléselosztási szabály letiltását a kimenő kapcsolathoz, ami lehetővé teszi adott előtérbeli elemek kiválasztását, beleértve a none elemet is.

Az összehasonlításhoz az alapszintű Load Balancer véletlenszerűen választja ki az egyetlen előtérbeli felületet, és nincs lehetőség a kiválasztására.

Tekintse át [a kimenő kapcsolatok részletes megvitatását](load-balancer-outbound-connections.md).

### <a name="operations"></a>Felügyeleti műveletek

Standard Load Balancer erőforrások egy teljesen új infrastruktúra-platformon találhatók.  Ez lehetővé teszi, hogy a standard SKU-ra és a befejezési időpontokra vonatkozó gyorsabb felügyeleti műveletek standard SKU-erőforrás esetén általában kevesebb, mint 30 másodperc.  Ahogy a háttérbeli készletek mérete megnövekszik, a háttérbeli készlet változásaihoz szükséges időtartam is növekszik.

A standard Load Balancer erőforrásokat módosíthatja, és egy szabványos nyilvános IP-címet helyezhet át egy virtuális gépről egy másikra.

## <a name="migration-between-skus"></a>Áttelepítési SKU között

Az SKU-ket nem változtathatók meg. Kövesse az ebben a szakaszban található lépéseket az egyik erőforrásból a másikra való áttéréshez.

>[!IMPORTANT]
>Tekintse át ezt a dokumentumot teljes egészében, hogy megértse az SKU-i különbségeket, és alaposan megvizsgálta a forgatókönyvet.  Előfordulhat, hogy további módosításokat kell végeznie a forgatókönyv igazításához.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrálás alapszintről standard SKU-ra

1. Hozzon létre egy új szabványos erőforrást (szükség szerint Load Balancer és nyilvános IP-címek). Hozza létre újra a szabályokat és a mintavételi definíciókat.  Ha korábban TCP-mintavételt használt a 443-as vagy a TCP-porton, érdemes lehet módosítani a mintavételi protokollt egy HTTPS-mintavételre, és hozzá kell adnia egy elérési utat.

2. Hozzon létre új vagy frissítsen meglévő NSG a hálózati adapteren vagy az alhálózaton, hogy lehetővé tegye a terheléses terheléselosztást, a mintavételt, valamint az engedélyezni kívánt egyéb forgalmat.

3. Távolítsa el az alapszintű SKU-erőforrásokat (Load Balancer és a nyilvános IP-címeket) az összes VM-példányból. Ügyeljen arra, hogy egy rendelkezésre állási csoport összes virtuálisgép-példányát is eltávolítsa.

4. Csatolja az összes VM-példányt az új szabványos SKU-erőforrásokhoz.

>[!IMPORTANT]
>
>Az egyező SKU-ket Load Balancer és nyilvános IP-erőforrásokhoz kell használni. Az alapszintű SKU-erőforrások és a szabványos SKU-erőforrások keveréke nem lehetséges. Nem csatolhat mindkét SKU-hoz egyszerre önálló virtuális gépeket, rendelkezésre állási csoportban lévő virtuális gépeket vagy virtuálisgép-méretezési csoport típusú erőforrásokat.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Standard Load Balancer jelenleg minden Azure-régióban elérhető.

## <a name="sla"></a>Szolgáltatási szerződés 

A standard Load Balancer 99,99%-os SLA-val érhető el.  A részletekért tekintse át a [standard Load BALANCER SLA](https://aka.ms/lbsla) -t. 

## <a name="pricing"></a>Díjszabás 

Standard Load Balancer használat díjat számítunk fel. 

- Konfigurált terheléselosztási és kimenő szabályok száma (a bejövő NAT-szabályok nem számítanak bele a szabályok teljes számára) 
- A bejövő és kimenő adatmennyiség, a szabálytól függetlenül.

A Standard Load Balancer díjszabásáról a [Load Balancer díjszabását](https://azure.microsoft.com/pricing/details/load-balancer/) ismertető oldalon talál további információt.

## <a name="limitations"></a>Korlátozások

- Az SKU-ket nem változtathatók meg. A meglévő erőforrások SKU-jának módosítása nem lehetséges.
- Egy önálló virtuálisgép-erőforrás, rendelkezésre állási készlet erőforrás vagy virtuálisgép-méretezési csoport erőforrása hivatkozhat egyetlen SKU-ra, soha nem mindkettőre.
- Egy Load Balancer szabály nem terjedhet ki két virtuális hálózatra.  A frontendeknek és a hozzájuk kapcsolódó háttérbeli példányoknak ugyanabban a virtuális hálózatban kell lenniük.  
- Az [előfizetési műveletek](../azure-resource-manager/management/move-resource-group-and-subscription.md) nem támogatottak a standard SKU LB és a pip-erőforrások esetében.
- A VNet és más Microsoft-platformokat nem tartalmazó webes feldolgozói szerepkörök elérhetők a csak belső standard Load Balancer mögötti példányokból, mivel a VNet szolgáltatások és egyéb platform-szolgáltatások funkcióinak mellékhatásai is hatással lehetnek. Ezt nem kell felhasználnia, mert a saját maga vagy a mögöttes platform értesítés nélkül változhat. Mindig feltételezni kell, hogy a [kimenő kapcsolatot](load-balancer-outbound-connections.md) explicit módon kell létrehoznia, ha csak belső standard Load Balancer használata esetén szükséges.
- A Load Balancer egy TCP- és UDP-termék az adott IP-protokollok terheléselosztásához és porttovábbításához.  A TCP és az UDP támogatja a terheléselosztási szabályokat és a bejövő NAT-szabályokat, de egyéb IP-protokollok (mint például az ICMP) nem támogatják őket. A Load Balancer nem szakít meg folyamatokat, nem válaszol rájuk, és semmilyen egyéb módon nem lép kapcsolatba egy UDP- vagy TCP-folyamat adatcsomagjával. Nem egy proxy. Az előtérhöz való csatlakozás sikeres ellenőrzéséhez a terheléselosztásban vagy a bejövő NAT-szabályban (TCP vagy UDP) használt protokollnak kell futnia, _és_ legalább az egyik virtuális gépnek választ kell adnia az ügyfél számára, hogy megtekintse az előtértől kapott választ.  Nem érkezett sávon kívüli válasz a Load Balancer előtérből azt jelzi, hogy egyetlen virtuális gép sem tudott válaszolni.  Nem lehet kommunikálni Load Balancer előtérrel anélkül, hogy a virtuális gép válaszolni tudjon.  Ez vonatkozik a kimenő kapcsolatokra is, ahol a [porthelyettesítő SNAT](load-balancer-outbound-connections.md#snat) csak a TCP-hez és UDP-hez támogatott – bármely más IP-protokoll, többek között az ICMP is sikertelen lesz.  A mérsékléshez rendeljen hozzá egy példányszintű nyilvános IP-címet.
- A belső terheléselosztási szolgáltatásokkal ellentétben, amelyek [kimenő kapcsolatokat](load-balancer-outbound-connections.md) biztosítanak a virtuális hálózaton belüli magánhálózati IP-címekről a nyilvános IP-címekre való áttéréskor, a belső terheléselosztó nem fordítja le a kimenő kapcsolatot a belső Load Balancer előtér-hálózatával, mivel mindkettő a magánhálózati IP-címtartomány részét képezi.  Ezzel elkerülhető, hogy a SNAT kimerülése az egyedi belső IP-címtartomány belsejében történjen, ahol a fordítás nem szükséges.  Ennek mellékhatása az, hogy ha a háttér-készletben lévő virtuális gépről érkező kimenő forgalom egy olyan belső Load Balancer előtér-végpontját kísérli meg, amelyben a készlet található, _és_ az önmagához van rendelve, a folyamat mindkét lába nem egyezik, és a folyamat sikertelen lesz.  Ha a folyamat nem adott vissza ugyanarra a virtuális gépre a háttér-készletben, amely létrehozta az előtér-folyamatot, a folyamat sikeres lesz.   Ha a folyamat magához a rendszerhez kapcsolódik, a kimenő folyamat úgy tűnik, hogy a virtuális gépről a kezelőfelületről származik, és úgy tűnik, hogy a megfelelő bejövő folyamat a virtuális gépről származik. A vendég operációs rendszer szempontjából ugyanazon folyamat bejövő és kimenő részei nem egyeznek a virtuális gépen belül. A TCP-verem nem fogja ugyanazon folyam részeiként felismerni a folyamat két felét, mivel a forrás és a cél nem egyezik.  Ha a folyamat a háttérbeli készlet bármely más virtuális gépe felé mutat, a folyamat felei megegyeznek, és a virtuális gép sikeresen tud válaszolni a folyamatra.  A forgatókönyv tünete átmeneti kapcsolati időtúllépés. A forgatókönyv megbízható megvalósításának számos gyakori megkerülő megoldása van (a háttér-készletből a háttér-készletekből származó, a belső Load Balancer előtér-környezetből származó folyamatokat), amelyek a belső Load Balancer vagy a [DSR-stílus szabályainak használatával](load-balancer-multivip-overview.md)vagy egy külső gyártótól származó proxy beszúrását is magukban foglalják.  Habár a mérsékléshez használható nyilvános Load Balancer, a létrejövő forgatókönyvnél valószínű lesz az [SNAT elfogyása](load-balancer-outbound-connections.md#snat), és ez körültekintő felügyelet nélkül inkább kerülendő.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Load Balancerról](load-balancer-overview.md).
- Tudnivalók a [standard Load Balancer és a Availability Zones](load-balancer-standard-availability-zones.md)használatáról.
- Tudnivalók az [állapot](load-balancer-custom-probe-overview.md)-mintavételekről.
- Ismerje meg a [standard Load Balancer diagnosztikát](load-balancer-standard-diagnostics.md).
- Ismerje meg a [Azure monitor](../monitoring-and-diagnostics/monitoring-overview.md) [-diagnosztika támogatott többdimenziós mérőszámait](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) .
- Tudnivalók a [Kimenő kapcsolatok Load Balancer](load-balancer-outbound-connections.md)használatáról.
- További információ a [kimenő szabályokról](load-balancer-outbound-rules-overview.md).
- További információ [a TCP alaphelyzetbe állításáról üresjáratban](load-balancer-tcp-reset.md).
- További tudnivalók a HA-portok terheléselosztási [szabályaival standard Load Balancer](load-balancer-ha-ports-overview.md).
- Ismerje meg, hogyan használhatja [a Load Balancert több előtér-felülettel](load-balancer-multivip-overview.md).
- További információ a [hálózati biztonsági csoportokról](../virtual-network/security-overview.md).
