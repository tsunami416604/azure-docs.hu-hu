---
title: Az Azure szabványos Load Balancer áttekintése |} Microsoft Docs
description: Azure Standard terheléselosztó funkcióinak áttekintése
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 15bbd32d982bdefc5665421b828ce0c8234b7257
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="azure-load-balancer-standard-overview"></a>Az Azure Load Balancer szabványos áttekintése

Az Azure Load Balancer lehetővé teszi az alkalmazások és a szolgáltatások magas rendelkezésre állás létrehozása. Terheléselosztó bejövő, valamint az olyan kimenő forgatókönyvekhez használható, és kis késés, nagyobb teljesítményt nyújt, és akár több millió összes TCP és UDP-alkalmazás flow méretezi. 

Ez a cikk szabványos terheléselosztó összpontosít.  Azure Load Balancer általánosabb áttekintéséért lásd [Load Balancer áttekintése](load-balancer-overview.md) is.

## <a name="what-is-standard-load-balancer"></a>Mi az a szabványos terheléselosztót?

Standard terheléselosztó egy új terheléselosztó termék összes TCP és UDP-alkalmazások egy bővített és részletesebb szolgáltatás alapvető terheléselosztó beállításához.  Miközben sok a hasonlóság, fontos Ismerkedjen meg a különbségek ebben a cikkben leírt módon.

Standard terheléselosztó nyilvános vagy belső terheléselosztó használható. És egy virtuális gépet egy nyilvános és egy belső terheléselosztó erőforrás lehet csatlakoztatni.

A Load Balancer erőforrás funkciók mindig egész időtúllépést, egy szabályt, a állapotmintáihoz és a háttér-készlet definíciójának beolvasása.  Egy erőforrás több szabály is tartalmazhat. A háttérkészlet virtuális gépek elhelyezheti a háttérkészlet, a virtuális gép hálózati erőforrás megadásával.  A virtuálisgép-méretezési csoport esetén ez a paraméter továbbítja a hálózati profil, és kibontva.

Egyik legfőbb szempontja a virtuális hálózat a az erőforrás.  Közben alapvető terheléselosztó létezik rendelkezésre állási csoportok hatókörében, szabványos terheléselosztó teljesen integrálva van a virtuális hálózat a hatókörét, és minden virtuális hálózati fogalmak alkalmazni.

Load Balancer erőforrások objektumai belül, amely akkor is express hogyan Azure kell programot, a több-bérlős-infrastruktúrát a létrehozni kívánt forgatókönyvhöz.  Nincs terheléselosztó erőforrások és a tényleges infrastruktúra; közötti közvetlen kapcsolat Terheléselosztó létrehozása nem példányt létrehozni, mindig érhető el a kapacitás és nem indítási vagy skálázás késések kell figyelembe venni. 

>[!NOTE]
> Azure teljes körűen felügyelt terheléselosztási megoldások az forgatókönyvek együttesét nyújtja.  Ha a TLS-záráshoz ("SSL kiszervezési"), vagy a HTTP/HTTPS Kérelemfeldolgozás alkalmazás réteg, tekintse át a [Alkalmazásátjáró](../application-gateway/application-gateway-introduction.md).  Ha a globális DNS a(z) terheléselosztást, tekintse át [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Előfordulhat, hogy a végpont forgatókönyvek előnyt ezek a megoldások kombinálásával, igény szerint.

## <a name="why-use-standard-load-balancer"></a>Standard terheléselosztó miért érdemes használni?

Virtuális adatközpontok, a nagy és összetett több zóna architektúrák kis méretű telepítések az összes szabványos terheléselosztó használható.

Tekintse át az alábbi táblázatban a szabványos terheléselosztó és alapvető terheléselosztó közötti különbséget áttekintése:

>[!NOTE]
> Új tervek érdemes megfontolni, szabványos terheléselosztó használatát. 

| | Standard termékváltozat | Alapszintű termékváltozat |
| --- | --- | --- |
| Háttér-készlet mérete | legfeljebb 1000 példányok | legfeljebb 100 példányok |
| Háttér címkészletet végpontok | Egyetlen virtuális hálózatban, beleértve a virtuális gépek rendelkezésre állási csoportok esetében, amelyet minden virtuális gép virtuálisgép-skálázási készletekben. | virtuális gépek egy egyetlen rendelkezésre állási készlet vagy a virtuális gép méretezési beállítása |
| Rendelkezésre állási zónák | Zónaredundáns és zonal frontends a bejövő és kimenő, a kimenő forgalom hozzárendelések után is megmaradnak a zóna hiba, terheléselosztás kereszt-zóna | / |
| Diagnosztika | Azure figyelő bájt és csomag számlálók, egészségügyi többdimenziós metrikákat a állapot, kapcsolódási kísérletek (TCP SZIN), kimenő kapcsolat állapota (SNAT sikeres és sikertelen adatfolyamok), az aktív adatforrás vezérlősík mérések mintavételi modulja | Az Azure Naplóelemzés csak a nyilvános terheléselosztót, SNAT Erőforrásfogyás riasztást, háttér címkészletet állapotfigyelő száma |
| Magas rendelkezésre ÁLLÁSÚ portok | Belső terheléselosztó | / |
| Alapértelmezés szerint biztonságos | alapértelmezett le a következő nyilvános IP-cím és a terheléselosztó végpontokat és a hálózati biztonsági csoport kell használni kifejezetten engedélyezett forgalom áramlását felé | alapértelmezett megnyitva, a hálózati biztonsági csoport nem kötelező |
| Kimenő kapcsolatok | Egy szabályban a több frontends lemondáshoz. Egy kimenő forgatókönyv _kell_ explicit módon hozhatók létre a virtuális gép nem használható a kimenő kapcsolat.  [Virtuális hálózat Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md) kimenő kapcsolat nélkül elérhető, és nem számítanak bele a feldolgozott adatokat.  Nyilvános IP-címek, beleértve az Azure PaaS szolgáltatások nem érhetők el a virtuális hálózat szolgáltatás végpontként kell kimenő kapcsolat és a feldolgozott adatok felé száma keresztül érhető el. Ha csak egy belső terheléselosztó van egy virtuális gépet szolgáltató, alapértelmezett SNAT keresztül kifelé irányuló kapcsolatok nem érhetők el. Kimenő SNAT programozás az átviteli protokoll adott protokoll a bejövő terheléselosztási szabály alapján. | Egyetlen előtér véletlenszerűen kiválasztott, ha több frontends jelen.  Csak belső terheléselosztó van egy virtuális gépet szolgáltató, amikor az alapértelmezett SNAT szolgál. |
| Több frontends | Bejövő és kimenő | Csak bejövő |
| Felügyeleti műveletek | A legtöbb műveletek < 30 másodperc | 60-90 másodpercet tipikus |
| SLA | a két kifogástalan állapotú virtuális gép elérési útja 99,99 %-os | A virtuális gép SLA implicit | 
| Díjszabás | A szabályok száma alapján, adatfeldolgozási bejövő vagy kimenő társított erőforrás  | Nem kell fizetni |

Felülvizsgálati [szolgáltatási korlátait terheléselosztóhoz](https://aka.ms/lblimits), valamint [árképzési](https://aka.ms/lbpricing), és [SLA](https://aka.ms/lbsla).


### <a name="backend"></a>Háttérkészlet

Standard terheléselosztó háttérkészletek bővíti a virtuális hálózatban lévő virtuális gép erőforrásokhoz.  Legfeljebb 1000 háttér példányokat tartalmazhat.  A háttér-példány IP-konfigurációt, amely a hálózati erőforrás-tulajdonság.

A háttérkészlet önálló virtuális gépek, a rendelkezésre állási készletek vagy a virtuálisgép-méretezési csoportok tartalmazhatnak.  Akkor is keverése háttérkészlethez erőforrásokat, és ezeket az erőforrásokat, akár 150 teljes kombinációja tartalmazhat.

Annak eldöntéséhez, hogy a háttérkészlet megtervezésére, megtervezheti a legkevésbé egyedi háttér címkészletet erőforrásokat tovább optimalizálhatja a kezelési műveletek időtartama száma.  Nincs különbség az adatok vezérlősík teljesítményt és a skála.

## <a name="az"></a>Rendelkezésre állási zónák

>[!NOTE]
> Használandó [rendelkezésre állási zónák Preview](https://aka.ms/availabilityzones) szabványos terheléselosztót igényel a [előfizetési rendelkezésre állási zónák](https://aka.ms/availabilityzones).

Standard terheléselosztó további képességek azokban a régiókban, ahol a rendelkezésre állási zónák elérhetők támogatja.  Ezek a szolgáltatások akkor növekményes összes szabványos terheléselosztóhoz biztosít.  Rendelkezésre állási zónák konfigurációk nyilvános és a belső szabványos terheléselosztó érhetők el.

A rendelkezésre állási zónák régióban telepítésekor alapértelmezés szerint nem zonal frontends vált zónaredundáns.   Zónaredundáns időtúllépést survives zóna hibát, és a zónák mindegyikében dedikált infrastruktúra által egyidejűleg kiszolgált. 

Ezen felül egy adott zónához időtúllépést garantálható. Zonal időtúllépést sorsáról osztanak meg a megfelelő zónához, és csak egy zóna dedikált infrastruktúra által kiszolgált.

Kereszt-zóna terheléselosztás háttérkészlet érhető el, és a virtuálisgép-erőforrás a vneten belül háttérkészlet része lehet.

Felülvizsgálati [részletes leírást a rendelkezésre állási zónák kapcsolódó képességek](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a> diagnosztika

Standard terheléselosztó többdimenziós metrikák Azure figyelő keresztül biztosít.  A metrikák lehet szűrni, csoportosítva, és betekintést aktuális és előzménynaplók teljesítmény és a szolgáltatás állapotát.  Erőforrás állapota is támogatott.  Az alábbiakban olvashat rövid áttekintést támogatott diagnosztika:

| Metrika | Leírás |
| --- | --- |
| VIP rendelkezésre állása | Load Balancer szabványos folyamatosan gyakorolja az elérési útja a régión belül egészen a SDN-készlet, amely támogatja a virtuális gép előtér terheléselosztóhoz. Mindaddig, amíg megfelelő példányok marad, a mérési követi az alkalmazás terheléselosztott forgalom az adott elérési úton. Az elérési útja az ügyfelek által használt is van hitelesítve. A mérték nem látható, hogy az alkalmazást, és nem ütközik más műveleteket.|
| DIP rendelkezésre állása | Load Balancer szabványos használ egy elosztott szolgáltatás, amely figyeli a konfigurációs beállítások alapján az alkalmazás végpontjának állapotát probing állapotát. Ez a metrika összesítő biztosít, vagy egy végpont szűrt-nézetben minden egyes példány végpont a terheléselosztó-készlet.  Láthatja, hogyan terheléselosztó megtekinti a mintavételi állapotkonfiguráció jelöli az alkalmazás állapotát.
| Szinkronizálás a mi csomagok | Load Balancer szabványos nem állítsa le a TCP-kapcsolatok vagy TCP vagy UDP-csomagok adatfolyamok kommunikál. Adatfolyamok és azok kézfogások mindig a forrás- és a Virtuálisgép-példány közé esnek. Jobb elhárításában a TCP protokollt, hogy SZIN használatát csomagok számlálók tudni, hogy hány TCP-kapcsolat kísérletet tesz. A metrika a fogadott TCP SZIN csomagok számát jelenti.|
| SNAT kapcsolatok | Load Balancer szabványos jelentések előtérbeli nyilvános IP-címre van masqueraded kimenő adatfolyamok száma. SNAT portjait egy kimeríthető erőforrás. Ez a metrika biztosíthat utalhat, hogy hogyan fokozottan az alkalmazás SNAT kimenő kezdeményezésű folyamatok függő van.  Sikeres és sikertelen kimenő SNAT adatfolyamok számlálói jelenti és hibaelhárításához, és megismerheti a kimenő forgalom állapotának használható.|
| Bájt számlálók | Load Balancer szabványos jelentések az előtér-feldolgozott adatokat.|
| Csomag számlálók | Load Balancer szabványos jelentések egy előtér-feldolgozott a csomagokat.|

Felülvizsgálati [részletes leírását a szokásos terhelés terheléselosztó diagnosztikai](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>Magas rendelkezésre ÁLLÁSÚ portok

Standard Load Balancer egy új típusú szabály támogatja.  

Terheléselosztási szabályok az alkalmazás méretezési és magas megbízható konfigurálhatja. Amikor az egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabály, szabványos terheléselosztó folyamata terheléselosztási minden rövid élettartamú port egy belső szabványos terheléselosztó tartozó előtérbeli IP-cím / biztosít.  A szolgáltatás akkor hasznos, az egyéb forgatókönyvek, ahol nem praktikus, vagy megadhat egyéni portokat a nemkívánatos.

Egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabály lehetővé teszi, hogy aktív-passzív vagy aktív-aktív n + 1 esetek létrehozása hálózati virtuális gépek és az összes alkalmazást, amely nagy adattartományokat vizsgálnak át bejövő portra van szükség.  Egy állapotmintáihoz segítségével határozza meg, melyik háttérkiszolgálókon kell kapnia új folyamatok.  A hálózati biztonsági csoportok segítségével egy port tartomány forgatókönyv emulálni.

>[!IMPORTANT]
> Ha azt tervezi, hogy a hálózati virtuális készülék használja, ellenőrizze az gyártójánál, hogy tesztelték a terméket a magas rendelkezésre ÁLLÁSÚ portok útmutatót, és kövesse a végrehajtására vonatkozó konkrét útmutatást. 

Felülvizsgálati [részletes leírását a magas rendelkezésre ÁLLÁSÚ portok](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Alapértelmezés szerint biztonságos

Standard terheléselosztó teljesen előkészítve a virtuális hálózathoz.  A virtuális hálózat egy saját, lezárt hálózati.  Szabványos Terheléselosztókról, illetve a szabványos nyilvános IP-címeket úgy tervezték, hogy engedélyezze a virtuális hálózaton kívülről is hozzáférhetnek a virtuális hálózat, mert ezekhez az erőforrásokhoz alapértelmezés szerinti zárva, kivéve, ha megnyitja őket. Ez azt jelenti, hogy hálózati biztonsági csoportokkal (NSG-k) használnak az explicit módon engedélyezi, és a engedélyezett forgalmat.  A teljes virtuális adatközpont létrehozhat és NSG keresztül döntse el, mi, és ha annak elérhetőnek kell lennie.  Ha az alhálózat egy NSG-t vagy a virtuálisgép-erőforrás hálózati adapter nem rendelkezik, azt nem ad engedélyt forgalom az erőforrás elérésére.

Az NSG-k és hogyan kell alkalmazni a forgatókönyvnek kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md).

### <a name="outbound"></a> Kimenő kapcsolatok

Terheléselosztó bejövő és kimenő forgatókönyveket támogat.  Standard terheléselosztó jelentősen eltér alapvető terheléselosztó kimenő kapcsolatok tekintetében.

Forrás hálózati cím fordítási (SNAT) szolgál a virtuális hálózat belső, privát IP-cím hozzárendelése terheléselosztó frontends nyilvános IP-címeit.

Standard terheléselosztó bevezet egy új algoritmust a [stabil, méretezhető és kiszámítható SNAT algoritmus](load-balancer-outbound-connections.md#snat) és lehetővé teszi, hogy új képességek, eltávolítja a félreérthetőség és kényszeríti explicit konfigurációk ahelyett, hogy hatások oldalán. Ezeket a módosításokat is szükséges ahhoz, hogy az új szolgáltatások merülnek fel. 

A kulcs tenets megjegyezhető az szabványos terheléselosztó használatakor az alábbiak:

- szabály létrehozása után a Load Balancer erőforrás meghajtók.  minden Azure programozását konfigurációjában származik.
- Ha több frontends érhetők el, az összes frontends használatosak, és minden előtér szorozza meg a rendelkezésre álló SNAT portok száma
- Válassza ki, és szabályozhatja, ha nem szeretné az adott időtúllépést a kimenő kapcsolatokhoz használt.
- kimenő forgatókönyvek a következők explicit, és a kimenő kapcsolat nem létezik, amíg meg van adva.
- terheléselosztási szabályok következtethető ki, hogyan SNAT programozott-e. Terheléselosztási szabályok adott protokoll. SNAT adott protokoll és konfigurációs kell ennek megfelelően helyett hozzon létre egy mellékhatása.

#### <a name="multiple-frontends"></a>Több frontends
Ha azt szeretné további SNAT portok, mert számított vagy már a kimenő kapcsolatok nagy igényt ró tapasztal, azt is megteheti növekményes SNAT port készlet további frontends, szabályok és háttérkészletek ugyanahhoz a virtuális géphez erőforrások.

#### <a name="control-which-frontend-is-used-for-outbound"></a>A vezérlő melyik előtér használt kimenő
Ha szeretné korlátozni az egy adott előtérbeli IP-cím csak oly módon, hogy a kimenő kapcsolatokat, letilthatja a szabályt, amely a kimenő leképezés fejezi ki a kimenő SNAT opcionálisan.

#### <a name="control-outbound-connectivity"></a>Kimenő kapcsolódás vezérlő
Standard terheléselosztó létezik a virtuális hálózati környezetében.  Egy virtuális hálózat egy saját, elkülönített hálózatot.  Csak nyilvános IP-címek egy hozzárendelés létrejött, a nyilvános kapcsolódási nem engedélyezett.  Érhető el [VNet Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md) ezek ugyanis a vállalaton belüli és a helyi a virtuális hálózathoz.  Ha azt szeretné, a virtuális hálózaton kívüli egy célra kimenő kapcsolat létrehozásához, két lehetősége van:
- egy Standard Termékváltozat nyilvános IP-címet rendeljen a virtuális gép erőforrásának példányszintű nyilvános IP-címet vagy
- a szabványos nyilvános terheléselosztó háttérkészletének helyezze el a virtuálisgép-erőforrást.

Egyaránt lehetővé teszi a kimenő kapcsolat a virtuális hálózat a virtuális hálózaton kívülről. 

Ha Ön _csak_ egy belső szabványos elosztott terhelésű a háttérkészlet, ahol a virtuálisgép-erőforrás társítva, a virtuális gép csak képes elérni a virtuális hálózati erőforrások és [VNet szolgáltatás Végpontok](../virtual-network/virtual-network-service-endpoints-overview.md).  A kimenő kapcsolat létrehozása az előző bekezdésben ismertetett lépések követésével.

Egy virtuálisgép-erőforrás nincs hozzárendelve a Standard termékváltozat marad, mielőtt kimenő kapcsolatok.

Felülvizsgálati [részletes leírását a kimenő kapcsolatok](load-balancer-outbound-connections.md).

### <a name="multife"></a>Több frontends
Terheléselosztó támogatja a több-több frontends szabályokat.  Standard terheléselosztó bontja ki a kimenő forgatókönyvek.  Kimenő forgatókönyvek a következők lényegében egy bejövő terheléselosztási szabály inverzét.  A bejövő terheléselosztási szabály is kimenő kapcsolatok társult hoz létre. Standard terheléselosztó terheléselosztási szabály keresztül virtuálisgép-erőforrás társított összes frontends használja.  Emellett a terheléselosztással paraméterként szabály, és lehetővé teszi, hogy letiltsa a kimenő kapcsolat, amely lehetővé teszi az adott frontends nincs beleértve alkalmazásában terheléselosztási szabály.

Az összehasonlításhoz alapvető terheléselosztó egyetlen előtérbeli véletlenszerűen választ, és nem tudja vezérelni, hogy melyik kiválasztott van.

Felülvizsgálati [részletes leírását a kimenő kapcsolatok](load-balancer-outbound-connections.md).

### <a name="operations"></a> Felügyeleti műveletek

Standard terheléselosztó erőforrások létezik egy teljesen új infrastruktúra platformon.  Ez lehetővé teszi, hogy a Standard termékváltozat jelentősen gyorsabb felügyeleti műveletek és befejezési idők általában Standard Termékváltozat erőforrásonként kevesebb, mint 30 másodperc.  Vegye figyelembe, hogy a méret növekedését háttérkészletek menüpontot, háttér szükséges időtartam készlet változik is növelését.

Standard terheléselosztó erőforrások módosítja, és a szabványos nyilvános IP-cím áthelyezése egy olyan virtuális gépet másik sokkal gyorsabb.

## <a name="migration-between-skus"></a>SKU közötti áttelepítés

Termékváltozat nem változtatható. Kövesse az ebben a szakaszban található áthelyezése egy erőforrás SKU másik.

>[!IMPORTANT]
>Tekintse át a termékváltozatok közötti különbségek megismeréséhez, és gondosan kell vizsgálni a forgatókönyv a teljes dokumentumot.  Szükség lehet további módosításokat a forgatókönyv igazíthatók.

### <a name="migrate-from-basic-to-standard-sku"></a>Az alapvető át Standard Termékváltozat

1. Hozzon létre egy új szabványos erőforrást (a terheléselosztó és a nyilvános IP-címek, szükség esetén). Hozza létre újra a szabályok és a mintavételi definíciókat.

2. Létrehozhat új, vagy frissítse a hálózati adapter vagy az alhálózat meglévő NSG engedélyezett terheléselosztott forgalom, mintavétel, valamint minden más forgalom engedélyezni kívánja.

3. Az alapszintű Termékváltozat erőforrások (a terheléselosztó és a megfelelő, nyilvános IP) eltávolítása az összes Virtuálisgép-példányok. Győződjön meg arról, hogy távolítsa el az összes Virtuálisgép-példányok egy rendelkezésre állási készlet.

4. Az összes Virtuálisgép-példányok csatolni az új Standard Termékváltozat-erőforrások.

### <a name="migrate-from-standard-to-basic-sku"></a>Alapszintű Termékváltozat Standard áttelepítése

1. Hozzon létre egy új alapszintű erőforrást (a terheléselosztó és a nyilvános IP-címek, szükség esetén). Hozza létre újra a szabályok és a mintavételi definíciókat. 

2. A Standard Termékváltozat-erőforrások (a terheléselosztó és a megfelelő, nyilvános IP) eltávolítása az összes Virtuálisgép-példányok. Győződjön meg arról, hogy távolítsa el az összes Virtuálisgép-példányok egy rendelkezésre állási készlet.

3. Az összes Virtuálisgép-példányok csatolni az új alapszintű Termékváltozat-erőforrások.

>[!IMPORTANT]
>
>A Basic és Standard termékváltozat általi használatára vonatkozó korlátozások is.
>
>Magas rendelkezésre ÁLLÁSÚ portok és a szabványos metódust diagnosztikai találhatók csak a Standard Termékváltozat. Nem az alapszintű Termékváltozat a Standard Termékváltozat át, és nem is megőrzik, ezeket a szolgáltatásokat.
>
>Basic és Standard Termékváltozat egyaránt rendelkezik különbségek számos ebben a cikkben leírt módon.  Győződjön meg arról, hogy tudomásul veszi, és készítse elő azokat a.
>
>Megfelelő termékváltozatok terheléselosztó és a nyilvános IP-erőforrások kell használni. Alapszintű Termékváltozat Standard Termékváltozat erőforrásokat és vegyesen tartalmazhat. Nem lehet csatolni a önálló virtuális gépek, virtuális gépek egy rendelkezésre állási készlet erőforrás vagy egy virtuálisgép-méretezési állítsa erőforrások mindkét termékváltozatok egyidejűleg.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Load Balancer szabványos már érhető el nyilvános felhő minden régióban.

## <a name="sla"></a>SLA

Egy 99,99 % SLA-t a szabványos Terheléselosztók érhetők el.  Tekintse át a [szokásos terhelés terheléselosztó SLA](https://aka.ms/lbsla) részleteiről.

## <a name="pricing"></a>Díjszabás

Standard terheléselosztó terheléselosztási szabályok konfigurálva és az összes bejövő és kimenő adatfeldolgozási száma alapján megterhelni termék. Standard terheléselosztóhoz árakról, látogasson el a [Load Balancer árképzési](https://aka.ms/lbpricing) lap.

## <a name="limitations"></a>Korlátozások

- Betöltési terheléselosztó háttér példányok nem található a virtuális hálózatok társviszonyban most. Háttér-példányainak ugyanabban a régióban kell lennie.
- Termékváltozat nem változtatható. A meglévő erőforrás Termékváltozata nem módosítható.
- Egy különálló virtuális gép erőforrás rendelkezésre állási csoport erőforrás, vagy a virtuálisgép-méretezési készlet erőforrás egyik Termékváltozatáról, soha nem is hivatkozhat.
- [Riasztások figyelése Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md) jelenleg nem támogatottak.
- [Előfizetés műveletek áthelyezése](../azure-resource-manager/resource-group-move-resources.md) Standard Termékváltozat LB és PIP erőforrások esetén nem támogatottak.

## <a name="next-steps"></a>További lépések

- További információ használatáról [szabványos terheléselosztó és a rendelkezésre állási zónák](load-balancer-standard-availability-zones.md)
- További információ [rendelkezésre állási zónák](../availability-zones/az-overview.md).
- További tudnivalók [normál terhelésen terheléselosztó diagnosztika](load-balancer-standard-diagnostics.md).
- További tudnivalók [többdimenziós metrikák támogatott](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers) a diagnosztikai [Azure figyelő](../monitoring-and-diagnostics/monitoring-overview.md).
- További információ használatáról [terheléselosztó kimenő kapcsolatok](load-balancer-outbound-connections.md)
- További tudnivalók [szabványos terheléselosztó a magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabályok](load-balancer-ha-ports-overview.md)
- További információ használatáról [több Frontends rendelkező terheléselosztó](load-balancer-multivip-overview.md)
- További tudnivalók [virtuális hálózatok](../virtual-network/virtual-networks-overview.md).
- További információ [hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md).
- További tudnivalók [VNet Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md)
- Ismerje meg, azzal kapcsolatban, a másik kulccsal [hálózati lehetőségeket](../networking/networking-overview.md) az Azure-ban.
- További információ [terheléselosztó](load-balancer-overview.md).
