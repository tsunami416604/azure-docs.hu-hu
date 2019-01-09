---
title: Mi az az Azure Load Balancer?
titlesuffix: Azure Load Balancer
description: Az Azure Load Balancer funkciói, architektúrája és implementálása. Ismerje meg a Load Balancer működését, és használja ki előnyeit a felhőben.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2018
ms.author: kumud
ms.openlocfilehash: 7b0a837bf23145c5ac5e5775d4324582fa3a6b20
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120835"
---
# <a name="what-is-azure-load-balancer"></a>Mi az az Azure Load Balancer?

Az Azure Load Balancerrel méretezheti alkalmazásait, és magas szintre emelheti szolgáltatásai rendelkezésre állását. A Load Balancer támogatja mind a bejövő, mind a kimenő forgatókönyveket, alacsony késleltetést és magas teljesítményt biztosít, és minden TCP- és UDP-alkalmazáshoz több millió folyamatig méretezhető.  

A Load Balancer a Load Balancer előterébe érkező új, bejövő folyamatokat a háttérkészlet példányaiban osztja el a szabályok és állapotminták szerint. 

Emellett a nyilvános Load Balancer a magánhálózati IP-címek nyilvános IP-címmé fordításával kimenő kapcsolatokat képes biztosítani a virtuális hálózat virtuális gépei (VM-ek) számára.

Az Azure Load Balancer kétféle termékváltozatban érhető el: alap- és standard szinten. Az egyes típusok méretezése, elérhető szolgáltatásai és díjszabása eltérő. Bármely, az Alapszintű Load Balancerrel létrehozható forgatókönyv a Standard Load Balancerrel is végrehajtható, habár az eljárás némileg különbözhet. A Load Balancer tanulmányozása során fontos, hogy megismerkedjen az alapokkal és a termékváltozat-specifikus különbségekkel.

## <a name="why-use-load-balancer"></a>Miért érdemes a Load Balancert használni? 

Az Azure Load Balancer funkciói:

* A bejövő internetes forgalom terheléselosztása a virtuális gépekre. Ez a konfiguráció [Nyilvános Load Balancer](#publicloadbalancer) néven ismert.
* Virtuális gépek közötti forgalom terheléselosztása egy virtuális hálózaton belül. Egy hibrid forgatókönyv esetén egy helyszíni hálózatról is elérheti a Load Balancer előterét. Mindkét forgatókönyv egy [Belső Load Balancer](#internalloadbalancer)-ként ismert konfigurációt használ.
* Forgalom továbbítása portokon egy adott virtuális gép adott portjára bejövő hálózati címfordítási (NAT-) szabályokkal.
* Nyilvános Load Balancer használatával biztosíthat [kimenő kapcsolatot](load-balancer-outbound-connections.md) a virtuális hálózat virtuális gépei számára.


>[!NOTE]
> Az Azure teljeskörűen felügyelt terheléselosztási megoldások együttesét biztosítja a különböző forgatókönyvekre. Ha Transport Layer Security (TLS) protokoll-lezárást („SSL-kiszervezés”) vagy per-HTTP/HTTPS kérelmeket vagy alkalmazásréteg-feldolgozást keres, tekintse át az [Application Gatewayt](../application-gateway/application-gateway-introduction.md) ismertető cikket. Ha globális DNS-terheléselosztást keres, tekintse át a [Traffic Managert](../traffic-manager/traffic-manager-overview.md) ismertető cikket. A végpontok közötti forgatókönyvek esetében előnyt jelenthet ezen megoldások igény szerinti kombinációja.

## <a name="what-are-load-balancer-resources"></a>Mik azok a Load Balancer-erőforrások?

Egy Load Balancer-erőforrás létezhet nyilvános vagy belső terheléselosztóként. A Load Balancer-erőforrás funkciói egy előtér, egy szabály, egy állapotminta és egy háttérkészlet definíciójaként vannak meghatározva. A virtuális gépek háttérkészletbe helyezése a virtuális gép háttérkészletének megadásával lehetséges.

A Load Balancer-erőforrások olyan objektumok, amelyeken belül megadhatja, hogy az Azure hogyan programozza a több-bérlős infrastruktúrát a kívánt forgatókönyv eléréséhez. A Load Balancer-erőforrások és a tulajdonképpeni infrastruktúra között nincs közvetlen kapcsolat. Egy Load Balancer létrehozásával nem jön létre példány, és a kapacitás mindig rendelkezésre áll. 

## <a name="fundamental-load-balancer-features"></a>Alapvető Load Balancer-funkciók

A Load Balancer az alábbi alapvető képességeket biztosítja a TCP- és UDP-alkalmazások számára:

* **Terheléselosztás**

    Az Azure Load Balancerrel létrehozhat egy terheléselosztó szabályt, amely az előtérbe érkező forgalmat elosztja a háttérkészlet példányain. Az Azure Load Balancer kivonatalapú algoritmust használ a bejövő folyamatok elosztásához, és ennek megfelelően újraírja a folyamatok fejlécét a háttérkészletek példányaihoz. Ha az állapotadat-mintavétel ép háttérbeli végpontot talál, rendelkezésre áll egy kiszolgáló az új folyamatok fogadására.
    
    Alapértelmezés szerint a Load Balancer ötpontos kivonatolást használ (amely a forrás IP-címéből, a forrásportból, a cél IP-címéből, a célportból és az IP-protokollszámból áll), hogy az elérhető kiszolgálókra irányítsa az új folyamatokat. Ha szeretné, egy adott szabályhoz választhatja a két- vagy hárompontos kivonatolást, így affinitást hozhat létre egy adott forrás-IP-címhez. Az adott csomagadatfolyam minden csomagja ugyanarra a példányra érkezik az elosztott terhelésű előtér mögött. Amikor az ügyfél új folyamatot indít el ugyanarról a forrás-IP-címről, a forrásport módosul. Ennek következtében lehetséges, hogy az ötpontos kivonatolás egy másik háttérbeli végpontra irányítja a forgalmat.

    További információt a [Load Balancer elosztási módját bemutató cikk](load-balancer-distribution-mode.md) tartalmaz. A következő kép a kivonatalapú elosztást szemlélteti:

    ![Kivonatalapú elosztás](./media/load-balancer-overview/load-balancer-distribution.png)

    *Ábra: Kivonat-alapú terjesztési*

* **Porttovábbítás**

    A Load Balancerrel létrehozhat egy bejövő NAT-szabályt egy adott előtérbeli IP-cím adott portjáról érkező forgalomnak egy, a virtuális hálózaton belüli adott háttérpéldány egy adott portjára való porttovábbításához. Ezt ugyanaz a kivonatalapú elosztást végzi el, mint a terheléselosztást. Ennek a funkciónak gyakori forgatókönyvei az Azure-beli virtuális hálózaton belüli önálló virtuálisgép-példányok felé indított RDP- vagy SSH-munkamenetek. Több belső végpontot is irányíthat a különböző portokhoz ugyanazon az előtérbeli IP-címen. Az előtérbeli IP-címek segítségével távoli felügyeletéhez a virtuális gépek az interneten egy további jump boxon szükségessége nélkül.

* **Alkalmazásfüggetlen és átlátható**

    A Load Balancer nem működik közvetlenül együtt a TCP-vel, az UDP-vel vagy az alkalmazásréteggel, és bármely TCP- vagy UDP-alkalmazásforgatókönyvet támogatja.  A Load Balancer nem szakít meg vagy indít folyamatokat, nem lép kapcsolatba a folyamat adatcsomagjával, semelyik alkalmazásnak nem biztosít átjárófunkciót, és a protokolláris kézfogások mindig közvetlenül az ügyfél és a háttérkészletbeli példány között zajlanak le.  Egy beérkező folyamatra mindig egy virtuális géptől érkezik a válasz.  Amikor a folyamat megérkezik a virtuális gépre, az eredeti IP-cím is megmarad.  A következő néhány példa jobban illusztrálja az átláthatóságot:
    - Minden végpontnak csak egy virtuális gép felel.  Például egy TCP-kézfogás mindig egy ügyfél és a kiválasztott háttérbeli virtuális gép között történik.  Egy kérésre adott választ az előtérnek mindig egy háttérbeli virtuális gép hoz létre. Amikor sikeresen érvényesíti a kapcsolódást egy előtérhez, akkor a végponttól végpontig terjedő kapcsolatot érvényesíti legalább egy háttérbeli virtuális gép felé.
    - Az alkalmazás-adatcsomagok átláthatóak a Load Balancer számára, és bármely UDP- vagy TCP-alkalmazás támogatható. Olyan adatcsomagok esetében, amelyekhez HTTP-kérésre alkalmazásrétegbeli adatcsomagok feldolgozására vagy manipulációjára van szükség (például HTTP URL-címek elemzése), használjon egy 7-es rétegű terheléselosztót, amilyen például az [Application Gateway](https://azure.microsoft.com/services/application-gateway) is.
    - Mivel a Load Balancer független a TCP-adatcsomagoktól és a TLS-tehermentesítés („SSL”) nem áll rendelkezésre, a Load Balancerrel létrehozhat végponttól végpontig érő, titkosított forgatókönyveket, és a magán a virtuális gépen létező TLS-kapcsolat megszakításával a TLS-alkalmazásokhoz nagy léptékű felskálázásra tehet szert.  A TLS-munkamenet kulcskezelési kapacitását például csak a háttérkészlethez hozzáadott virtuális gépek típusa és száma korlátozza.  Ha „SSL-tehermentesítésre” vagy alkalmazásréteg-kezelésre van szüksége, esetleg tanúsítványkezelést szeretne delegálni az Azure-ra, akkor inkább használja az Azure 7. rétegű terheléselosztó [Application Gatewayét](https://azure.microsoft.com/services/application-gateway).
        

* **Automatikus újrakonfigurálás**

    Ha le- vagy felskálázza a példányokat, a Load Balancer azonnal újrakonfigurálja magát. A virtuális gépek hozzáadása vagy a háttérkészletből történő eltávolítása újrakonfigurálja a Load Balancert anélkül, hogy a rendszer további műveleteket hajtana végre a Load Balancer erőforrásán.

* **Állapotminták**

    A háttérkészlet példányainak állapotát a Load Balancer Ön által megadott állapotmintákkal határozza meg. Ha egy mintavételező nem válaszol, a Load Balancer nem küld új kapcsolatokat a nem megfelelő állapotú példányra. A meglévő kapcsolatok nem változnak, és egészen addig folytatódnak, amíg az alkalmazás le nem állítja a folyamatot, üresjárati időtúllépés történik, vagy a virtuális gép leáll.
     
    A Load Balancer [különböző állapotminta-típusokat](load-balancer-custom-probe-overview.md#types) biztosít a TCP-, HTTP- és HTTPS-végpontokhoz.

    Ezenkívül klasszikus a cloud services használata esetén engedélyezett típust:  [A vendégügynök](load-balancer-custom-probe-overview.md#guestagent).  Ezt az állapotmintát csak végső esetben szabad használni, és ha egyéb lehetőség is elérhető, akkor a használata nem javasolt.
    
* **Kimenő kapcsolatok (SNAT)**

    A virtuális hálózaton belül magánhálózati IP-címekről kiinduló, az interneten található nyilvános IP-címek felé irányuló folyamatok lefordíthatók a Load Balancer egy előtérbeli IP-címére. Amikor egy nyilvános előtéret egy terheléselosztási szabállyal hozzákötünk egy háttérbeli virtuális géphez, az Azure átprogramozza a kapcsolatokat, hogy a rendszer automatikusan lefordítsa őket a nyilvános előtérbeli IP-címre.

    * Engedélyezze az egyszerű frissítést és a szolgáltatások vészhelyreállítását, mert az előtér dinamikusan átirányítható a szolgáltatás egy másik példányára.
    * Könnyebb a hozzáférés-vezérlési listák (ACL) kezelése. Az előtérbeli IP-címekkel kifejezett ACL-ek nem változnak a szolgáltatások fel- vagy leskálázása, vagy újbóli üzembe helyezése során.  A kimenő kapcsolatok a gépek számánál kisebb mennyiségű IP-címre történő lefordítása csökkentheti az engedélyezéssel járó terheket.

    További információt a [kimenő kapcsolatokról](load-balancer-outbound-connections.md) szóló cikkben talál.

A Standard Load Balancer az alapvető képességeken kívül további termékváltozat-specifikus képességekkel rendelkezik. Tekintse át a cikk többi részét a részletekért.

## <a name="skus"></a> Load Balancer-termékváltozatok összehasonlítása

A Load Balancer mind az Alapszintű, mind a Standard termékváltozatot támogatja, amelyek csak forgatókönyv-méretezésben, funkciókban és díjszabásban térnek el. Bármely, az Alapszintű Load Balancer verzióval megvalósított forgatókönyv létrehozható a Standard Load Balancer verzióval is. Valójában mindkét termékváltozat esetében hasonlók az API-k, és a termékváltozat specifikációján keresztül hívja meg őket a rendszer. A Load Balancer termékváltozatait támogató API és a nyilvános IP-cím a 2017-08-01-es API-tól kezdve érhető el. Mindkét termékváltozatban megegyezik az általános API és a struktúra.

Azonban attól függően, hogy melyik termékváltozatot választja, a teljes forgatókönyv-konfiguráció némileg eltérhet. A Load Balancer dokumentációjában jelölve van, ha egy cikk csak egy adott termékváltozatra érvényes. A különbségek összehasonlításához és megértéséhez lásd az alábbi táblázatot. További információt [a Standard Load Balancer áttekintésében](load-balancer-standard-overview.md) talál.

>[!NOTE]
> Az új kialakítások során a Standard Load Balancert érdemes alkalmazni. 

Az önálló virtuális gépeket, a rendelkezésre állási csoportokat és a Virtual Machine Scale-készleteket csak egy termékváltozathoz csatlakoztathatja, sosem kettőhöz egyszerre. Ha nyilvános IP-címekkel használja őket, a Load Balancer és a nyilvános IP-cím termékváltozatának egyeznie kell. A Load Balancer és a nyilvános IP-cím termékváltozata nem módosítható.

_Habár még nem kötelező, érdemes egyértelműen meghatározni a termékváltozatokat._  Jelenleg csak a legalapvetőbb módosításokra nyílik lehetőség. Ha nincs meghatározva a termékváltozat, a rendszer ezt úgy értelmezi, hogy az Alapszintű termékváltozat 2017-08-01-es API-verzióját szeretné használni.

>[!IMPORTANT]
>A Standard Load Balancer egy új Load Balancer termék, és nagyrészt az Alapszintű Load Balancer kibővítéseként lehet értelmezni. A két termék között fontos és szándékos különbségek vannak. Bármely, az Alapszintű Load Balancer verzióval a végpontok között megvalósított forgatókönyv létrehozható a Standard Load Balancer verzióval is. Ha már jártas az Alapszintű Load Balancer használatában, ismerkedjen meg a Standard Load Balancerrel is, hogy megértse a viselkedésben bekövetkezett legutóbbi változtatásokat és azok hatását a Standard és Alapszintű verziók között. Alaposan olvassa át ezt a szakaszt.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

További információt [a Load Balancer szolgáltatás korlátairól](https://aka.ms/lblimits) szóló cikkben találhat. A Standard Load Balancerről további részleteket az [áttekintés](load-balancer-standard-overview.md), a [díjszabás](https://aka.ms/lbpricing) és az [SLA](https://aka.ms/lbsla) szakaszban talál.

## <a name="concepts"></a>Alapelvek

### <a name = "publicloadbalancer"></a>Nyilvános Load Balancer

A nyilvános Load Balancer a bejövő forgalom nyilvános IP-címét és portszámát hozzárendeli a virtuális gép magánhálózati IP-címéhez és portszámához, és a virtuális gépről érkező válaszforgalomnál ennek fordított sorrendjében jár el. A terheléselosztási szabályok alkalmazásával adott típusú forgalmat oszthat el több virtuális gép vagy szolgáltatás között. A webkérések adatforgalmát például eloszthatja több webkiszolgáló között.

A következő ábrán egy elosztott terhelésű, webes forgalmat bonyolító végpont látható, amelynek terhelése három virtuális gép között oszlik el a nyilvános és a 80-as TCP-porthoz. Ez a három virtuális gép egy elosztott terhelésű készlet részeit képezi.

![Nyilvános Load Balancer-példa](./media/load-balancer-overview/IC727496.png)

*Ábra: Terheléselosztás a webes forgalom nyilvános Load Balancer használatával*

Amikor az internetes ügyfelek weblapkéréseket küldenek egy webalkalmazás nyilvános IP-címére a 80-as TCP-porton, az Azure Load Balancer elosztja a kéréseket az elosztott terhelésű készlet három virtuális gépe között. A Load Balancer-algoritmusokról további információt a [Load Balancer-funkciókat](load-balancer-overview.md##fundamental-load-balancer-features) ismertető szakaszban találhat.

Alapértelmezés szerint az Azure Load Balancer több virtuálisgép-példány között egyenlően osztja el a hálózati forgalmat. A munkamenet-affinitást is beállíthatja. További információt a [Load Balancer elosztási módját bemutató cikk](load-balancer-distribution-mode.md) tartalmaz.

### <a name = "internalloadbalancer"></a> Belső Load Balancer

A belső Load Balancer csak a virtuális hálózaton belüli, vagy az Azure-infrastruktúrát VPN-en keresztül elérő erőforrások felé irányítja a forgalmat. Ebből a szempontból egy belső Load Balancer eltér a nyilvános Load Balancertől. Az Azure-infrastruktúra korlátozza egy virtuális hálózat elosztott terhelésű előtérbeli IP-címeihez való hozzáférést. előtérbeli IP-címek és virtuális hálózatok közvetlenül soha nem érhetők el egy internet-végponthoz. A belső üzletági alkalmazások az Azure-ban futnak, és csak az Azure-ból vagy a helyszíni erőforrásokból érhetők el.

Egy belső Load Balancer a következő típusú terheléselosztásokat teszi lehetővé:

* **Egy virtuális hálózaton belüli**: A terheléselosztás virtuális gépek a virtuális hálózatban az azonos virtuális hálózatban található virtuális gépek készletét.
* **Létesítmények közötti virtuális hálózatok**: Terheléselosztás a helyszíni számítógépek, amelyek az ugyanazon virtuális hálózaton belüli virtuális gépek számára. 
* **A többrétegű alkalmazások**: Terheléselosztás internetkapcsolattal rendelkező többrétegű alkalmazásokhoz, ahol a háttérbeli rétegekből amelyek nem internetes elérésű. A háttérbeli szinteknél az internetkapcsolattal rendelkező szintekről kell elosztani a terhelést (lásd a következő ábrát).
* **Az üzletági alkalmazások**: Üzleti alkalmazásokhoz, amelyek az Azure-ban anélkül, hogy további load balancer hardveres vagy szoftveres terheléselosztást. Ebben a forgatókönyvben helyszíni kiszolgálók szerepelnek, amelyek az elosztott terhelésű forgalmat bonyolító számítógépek készletébe tartoznak.

![Belső Load Balancer-példa](./media/load-balancer-overview/IC744147.png)

*Ábra: Terheléselosztás a többrétegű alkalmazások nyilvános, mind a belső Load Balancer használatával*

## <a name="pricing"></a>Díjszabás
A Standard Load Balancer használatának árát a beállított terheléselosztási szabályok száma, valamint a kimenő és bejövő adatok feldolgozott mennyisége határozza meg. A Standard Load Balancer díjszabásáról a [Load Balancer díjszabását](https://azure.microsoft.com/pricing/details/load-balancer/) ismertető oldalon talál további információt.

Az Alapszintű Load Balancer használata ingyenes.

## <a name="sla"></a>SLA

A Standard Load Balancer SLA-ról a [Load Balancer SLA](https://aka.ms/lbsla) oldalán talál további információt. 

## <a name="limitations"></a>Korlátozások

- A Load Balancer egy TCP- és UDP-termék az adott IP-protokollok terheléselosztásához és porttovábbításához.  A TCP és az UDP támogatja a terheléselosztási szabályokat és a bejövő NAT-szabályokat, de egyéb IP-protokollok (mint például az ICMP) nem támogatják őket. A Load Balancer nem szakít meg folyamatokat, nem válaszol rájuk, és semmilyen egyéb módon nem lép kapcsolatba egy UDP- vagy TCP-folyamat adatcsomagjával. Nem egy proxy. Egy előtér kapcsolatának sikeres érvényesítését a terheléselosztásnál vagy belső NAT-szabálynál (TCP vagy UDP) használt protokollal megegyező sávban kell elvégezni, _és_ a virtuális gépek közül legalább egynek választ kell előállítania egy ügyfél számára, hogy választ kapjon egy előtérbeli helyről.  Ha nem érkezik sávon belüli válasz a Load Balancer előteréből, az azt jelzi, hogy egyetlen virtuális gép sem tudott válaszolni.  Egy Load Balancer előterével nem lehetséges kapcsolatot létesíteni, ha nincs válaszra képes virtuális gép.  Ez vonatkozik a kimenő kapcsolatokra is, ahol a [porthelyettesítő SNAT](load-balancer-outbound-connections.md#snat) csak a TCP-hez és UDP-hez támogatott – bármely más IP-protokoll, többek között az ICMP is sikertelen lesz.  A mérsékléshez rendeljen hozzá egy példányszintű nyilvános IP-címet.
- A nyilvános Load Balancerektől eltérően (amelyek [kimenő kapcsolatokat](load-balancer-outbound-connections.md) biztosítanak a virtuális hálózaton belüli magánhálózati IP-címek nyilvános IP-címekre való váltásakor) a belső Load Balancerek nem fordítják le a kintről érkező kapcsolatokat egy belső Load Balancer előterébe, mivel mindkettő magánhálózati IP-címtérben található.  Ezzel elkerülhető a lehetséges SNAT-portfogyás az egyedi belső IP-címtéren belül, ahol nincs szükség a fordításra.  Ennek mellékhatása, hogy ha a háttérkészlet egy virtuális gépéről egy kimenő folyamat megkísérel egy folyamatot a belső Load Balancer előterébe juttatni, abba a készletbe, amelyben található, _és_ a rendszer saját magára képezi le, akkor a folyamat egyik forrása sem egyezik, és a folyamat meghiúsul.  Ha a folyamat nincs visszairányítva ugyanarra a háttérkészletben található virtuális gépre, amely létrehozta a folyamatot az előtér számára, akkor a folyamat sikeres lesz.   Amikor a folyamat saját magára mutat vissza, úgy tűnhet, mintha az előtérhez érkező kimenő folyamat a virtuális gépről származna, és a hozzá kapcsolódó bejövő folyamat pedig a virtuális gépről saját maga felé mutatna. A vendég operációs rendszer szempontjából ugyanazon folyamat bejövő és kimenő részei nem egyeznek a virtuális gépen belül. A TCP-verem nem fogja ugyanazon folyam részeiként felismerni a folyamat két felét, mivel a forrás és a cél nem egyezik.  Amikor a folyamatot a háttérkészlet bármely más virtuális gépéhez irányítja a rendszer, a folyamat két fele egyezni fog, és a virtuális gép sikeresen válaszolhat a folyamatnak.  Ennek a forgatókönyvnek a tünete az időszakos kapcsolati időtúllépés, amikor a folyamat visszatér a folyamatot létrehozó háttérhez. Több gyakori kerülő megoldás létezik ennek a forgatókönyvnek a megvalósítására (a folyamatok indítása a háttérkészletből a háttérkészlethez tartozó belső Load Balancer előterébe), amelyekhez vagy egy proxyréteget kell beilleszteni a belső Load Balancer mögé, vagy [DSR stílusú szabályokat kell alkalmazni](load-balancer-multivip-overview.md).  Az ügyfelek egy belső Load Balancert ötvözhetnek bármely, harmadik féltől származó proxyval, vagy helyettesíthetik belső [Application Gatewayyel](../application-gateway/application-gateway-introduction.md) a HTTP/HTTPS protokollra korlátozott proxyforgatókönyvekhez. Habár a mérsékléshez használható nyilvános Load Balancer, a létrejövő forgatókönyvnél valószínű lesz az [SNAT elfogyása](load-balancer-outbound-connections.md#snat), és ez körültekintő felügyelet nélkül inkább kerülendő.

## <a name="next-steps"></a>További lépések

A cikk az Azure Load Balancer szolgáltatásról nyújtott áttekintést. Ha meg szeretne ismerkedni a Load Balancer használatával, hozzon létre egyet, hozzon létre virtuális gépeket egy testreszabott, telepített IIS-bővítménnyel, és ossza el a webalkalmazás terhelését a virtuális gépek között. Ennek ismertetéséhez tekintse meg az [alapszintű Load Balancer létrehozását](quickstart-create-basic-load-balancer-portal.md) ismertető rövid útmutatót.
