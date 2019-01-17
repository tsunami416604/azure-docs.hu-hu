---
title: Mi az Azure Standard Load Balancer?
titlesuffix: Azure Load Balancer
description: Az Azure Standard Load Balancer funkcióinak áttekintése
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/11/2019
ms.author: kumud
ms.openlocfilehash: 2eb2fbb1d184bf58923748278d4989a271adf434
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352580"
---
# <a name="azure-standard-load-balancer-overview"></a>Az Azure Standard Load Balancer áttekintése

Az Azure Load Balancer lehetővé teszi az alkalmazások skálázását és magas rendelkezésre állású szolgáltatások létrehozása. Load Balancer bejövő, valamint a kimenő forgatókönyveket is használható, és alacsony késleltetésű, nagy átviteli sebességet, és akár több milliónyi összes TCP és UDP-alkalmazás méretezhető. 

Ez a cikk a Standard Load Balancer összpontosít.  Az Azure Load Balancerhez általános áttekintést, tekintse át a [Load Balancer áttekintése](load-balancer-overview.md) is.

## <a name="what-is-standard-load-balancer"></a>Mi az a standard Load Balancer?

A standard Load Balancer, amely egy új terheléselosztó-egy kibontott és részletesebb funkciókészlethez alapszintű terheléselosztóval szemben az összes TCP és UDP-alkalmazásokhoz.  Miközben sok Hasonlóságok, fontos ismerje meg az eltéréseket az ebben a cikkben ismertetett módon.

A Standard Load Balancer nyilvános vagy belső terheléselosztóként is használhatja. És a virtuális gépek is csatlakoztathatók egy nyilvános és a egy belső terheléselosztó erőforrás.

A Load Balancer erőforrásfüggvények időtúllépést, egy szabályt, az állapotfigyelő mintavételező és a háttérkiszolgáló-készlet definíciója szerint mindig ki.  Egy erőforrás tartalmazhat több szabály. Virtuális gépek, helyezze a háttérkészlet, adja meg a háttérkészlet, a virtuális gép Hálózatiadapter-erőforrásból.  Ezt a paramétert a hálózati profil átadni és bővített virtuálisgép-méretezési csoportok használata esetén.

Egyik legfőbb szempontja az erőforrás a virtuális hálózat hatókörét.  Alapszintű Load Balancer hatókörébe tartozó rendelkezésre állási csoport létezik, míg egy Standard Load Balancer teljesen integrálva van a virtuális hálózat hatókörét, és minden virtuális hálózat a fogalmak alkalmazhatók.

Load Balancer-erőforrások olyan objektumok, amelyen belül fejezhető ki hogyan a Azure kell a több-bérlős-infrastruktúrát a létrehozni kívánt forgatókönyvhöz a program.  Nincs terheléselosztó-erőforrások és a tényleges infrastruktúra közötti közvetlen kapcsolat Terheléselosztó létrehozása nem hoz létre egy példányt, mindig érhető el a kapacitás és nincsenek indítási vagy fontolja meg az késleltetések méretezést. 

>[!NOTE]
> Az Azure teljes körűen felügyelt terheléselosztási a szituációhoz kínál megoldásokat kínál.  Ha a TLS-lezárást ("SSL-alapú kiszervezéshez") vagy HTTP/HTTPS Kérelemfeldolgozás application layer keres, tekintse át [Application Gateway](../application-gateway/application-gateway-introduction.md).  Ha globális DNS-terheléselosztást keres, tekintse át a [Traffic Managert](../traffic-manager/traffic-manager-overview.md) ismertető cikket.  A végpontok közötti forgatókönyvek részesülhetnek ezekkel a megoldásokkal kombinálva, igény szerint.

## <a name="why-use-standard-load-balancer"></a>Miért érdemes használni a Standard Load Balancer?

A Standard Load Balancer használatával skálázhatók az alkalmazások, és magas rendelkezésre állás érhető el akár kis méretű üzemelő példányoknál, akár nagy és összetett, többzónás architektúrák esetében.

Tekintse át az alábbi táblázatban áttekintheti az alapszintű Load Balancer és a Standard Load Balancer közötti különbségeket:

>[!NOTE]
> Az új kialakítások során a Standard Load Balancert érdemes alkalmazni. 

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Felülvizsgálat [szolgáltatási korlátozásaival terheléselosztó](https://aka.ms/lblimits), valamint [díjszabás](https://aka.ms/lbpricing), és [SLA](https://aka.ms/lbsla).


### <a name="backend"></a>Háttérkészlet

Standard Load Balancer háttérkészletek bontsa ki a virtuális hálózat bármely virtuális gép típusú erőforrást.  Legfeljebb 1000 háttérpéldányokkal tartalmazhat.  A háttér-példány egy IP-konfigurációt, amely egy hálózati adapter erőforrás tulajdonsága.

A háttérkészlet önálló virtuális gépek, a rendelkezésre állási csoportok vagy a virtual machine scale sets is tartalmazhat.  A háttérkészletében található erőforrások azokat is ötvözve. Kombinálhatja a háttérkészletben Load Balancer-erőforrásonként legfeljebb 150 erőforrásokat.

Amikor kiválasztja a háttérkészlet tervezéséről, megtervezheti a legkevésbé egyedi készlet háttérerőforrásokhoz tovább optimalizálható a felügyeleti műveletek időtartama száma.  Nincs adat adatsík teljesítmény vagy a méretezési csoport nincs különbség.

### <a name="probes"></a>Állapotadat-mintavételek
  
A standard Load Balancer támogatásával bővült [HTTPS állapotadat-mintavételek](load-balancer-custom-probe-overview.md#httpprobe) (HTTP-mintavétel a Transport Layer Security (TLS) burkoló) pontosan az a HTTPS-alkalmazások figyelésére.  

Emellett, ha a teljes háttérkészlet [le mintavételek](load-balancer-custom-probe-overview.md#probedown), a Standard Load Balancer lehetővé teszi, hogy a létrehozott összes TCP-kapcsolatok a folytatáshoz. (Az alapszintű Load Balancer összes TCP-kapcsolatok az összes példányra megszűnik).

Felülvizsgálat [Load Balancer állapot-mintavételei](load-balancer-custom-probe-overview.md) részleteiről.

### <a name="az"></a>A rendelkezésre állási zónák

Standard Load Balancer támogatja a további képességek régióban, ahol érhetők el rendelkezésre állási zónák.  Ezek a funkciók hozzáadódnak az összes Standard Load Balancer biztosít.  A Standard Load Balancer nyilvános és belső rendelkezésre állási zónák konfigurációk érhetők el.

A rendelkezésre állási zónákban régióban üzembe helyezve, alapértelmezés szerint nem zónaszintű és előtérrendszerek válnak zónaredundáns.   Egy zónaredundáns előtér survives zóna hiba- és minden a zónák a dedikált infrastruktúra által egyidejűleg biztosítja. 

Ezenkívül garantálhatja a frontend egy adott zónához. A zónaszintű előtérbeli sorsát osztanak meg a megfelelő zónához, és csak egy zóna dedikált infrastruktúra által kiszolgált.

Zónaközi terheléselosztás háttérkészlete számára elérhető, és a virtuális hálózat bármely virtuális gép típusú erőforrást háttérkészlet része lehet.

Felülvizsgálat [részletes leírásáért lásd a rendelkezésre állási zónák képességekkel kapcsolatos](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a> Diagnosztika

A standard Load Balancer többdimenziós metrikák az Azure monitoron keresztül biztosít.  Ezek a metrikák szűrve, csoportosítva és egy adott dimenzió szétbontva.  Teljesítmény és a szolgáltatás állapotát a jelenlegi és korábbi betekintést nyújtanak.  A Resource Health is támogatott.  Alábbiakban a támogatott diagnosztikai rövid áttekintése:

| Metrika | Leírás |
| --- | --- |
| Virtuális IP-cím elérhetősége | A standard Load Balancer folyamatosan gyakorol az adatok útvonalat egy adott régión belül a terheléselosztó előtérrendszerét, egészen az SDN-verem, amely támogatja a virtuális Géphez való. Kifogástalan állapotú példányok továbbra is, a mérés követi az alkalmazás elosztott terhelésű forgalmat az adott elérési úton. Az adatok elérési útja, az ügyfelek által használt is ellenőrzi. A mérték az alkalmazás számára, és nem ütközik más műveleteket.|
| Dedikált IP-CÍMMEL rendelkezésre állása | A standard Load Balancer egy elosztott Állapotfigyelő szolgáltatás, amely figyeli az alkalmazás végponti állapotát a konfigurációs beállításoknak megfelelően tesztelés használ. Ez a metrika összesítést tartalmaz, vagy egy végpont szűrt-nézetben minden egyes példány végpont a terheléselosztóban tárolókészlet.  Láthatja, hogyan a Load Balancer megtekinti a állapot-mintavételi konfigurációban aszinkronitást az alkalmazás állapotát.
| Szinkronizálás a mi csomagok | A standard Load Balancer nem TCP-kapcsolatok leáll, és TCP vagy UDP packet flow interakcióba. Folyamatok és azok kézfogások mindig a forrás- és a Virtuálisgép-példány között vannak. A TCP protokoll forgatókönyvek hatékonyabb elhárításához végezhet SZIN felhasználása csomagok számlálók tudni, hogy hány TCP-kapcsolat kísérletet tesz. A metrika a fogadott TCP SZIN csomagok számát jelenti.|
| SNAT-kapcsolatok | A standard Load Balancer előtérbeli nyilvános IP-címre van masqueraded kimenő folyamatok számát jelenti. SNAT portjait egy kimeríthető erőforrást. Ez a metrika biztosíthat az arra utalhat, hogy hogyan érdemes az alkalmazás van szüksége az SNAT a kimenő folyamatokhoz.  Sikeres és sikertelen kimenő SNAT folyamatok számlálói készül jelentés, és használható ismertetünk az elhárításukkal és a kimenő forgalom állapotának ismertetése.|
| Bájt számlálók | A standard Load Balancer az adatokat egy előtér-feldolgozott jelentések.|
| Csomag-számlálók | A standard Load Balancer jelentés a feldolgozott előtér-csomagokat.|

Felülvizsgálat [Standard Load Balancer diagnosztikai veszik górcső alá részletes](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>Magas rendelkezésre ÁLLÁS portok

Standard Load Balancer támogatja egy új típusú szabály.  

Terheléselosztási szabályok segítségével győződjön meg arról, az alkalmazás méretezési és magas megbízhatóságú konfigurálhatja. Egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabály használatakor a Standard Load Balancer kiszolgálónként egy belső Standard Load Balancer előtérbeli IP-cím minden rövid élettartamú port terheléselosztási folyamat szolgálnak.  A funkció akkor hasznos, más esetekben, ahol nem praktikus, vagy nem kívánatos egyedi port megadása.

Egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabály lehetővé teszi, hogy aktív-passzív vagy aktív – aktív n + 1 forgatókönyvek létrehozása a hálózati virtuális berendezések, és minden alkalmazás, amely igényel bejövő portokat nagyobb adattartományokat.  Az állapotfigyelő mintavételező segítségével határozza meg, melyik háttérrendszereket kell kapnia új folyamatok.  A hálózati biztonsági csoport segítségével port tartomány forgatókönyv Emulálhatja.

>[!IMPORTANT]
> Ha azt tervezi, egy hálózati virtuális berendezés használata, útmutatást, hogy a terméket a magas rendelkezésre ÁLLÁSÚ portok az tesztelték a tárolóeszközök gyártójával egyeztetve ellenőrizze, és hajtsa végre a megvalósítás vonatkozó konkrét útmutatást. 

Felülvizsgálat [magas rendelkezésre ÁLLÁSÚ portok veszik górcső alá részletes](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Alapértelmezés szerint biztonságossá tétele

A standard Load Balancer található teljesen előkészítve a virtuális hálózathoz.  A virtuális hálózaton egy privát, lezárt hálózat.  Standard Load Balancer Terheléselosztók és a Standard nyilvános IP-címeket úgy tervezték, hogy lehetővé teszik a virtuális hálózaton elérhetők a virtuális hálózaton kívül, mert ezek az erőforrások mostantól alapértelmezés szerint lezárt, kivéve, ha megnyitja őket. Ez azt jelenti, hálózati biztonsági csoportok (NSG) használnak az explicit módon engedélyezi és engedélyezett forgalom engedélyezett.  A teljes virtuális adatközpont létrehozhat és NSG-t keresztül döntse el, mit és mikor érdemes lesz elérhető.  Ha nem rendelkezik alhálózat NSG-t vagy a hálózati Adaptert a virtuális gép erőforrás, forgalom nem engedélyezett az erőforrás eléréséhez.

NSG-ket és hogyan alkalmazhatja őket a forgatókönyvhöz kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/security-overview.md).

### <a name="outbound"></a> Kimenő kapcsolatok

Load Balancer bejövő és kimenő forgatókönyveket támogatja.  A standard Load Balancer jelentősen eltér attól az alapszintű Load Balancer megállapodást kimenő kapcsolatokat.

Forrás hálózati cím címfordítás (SNAT) nyilvános IP-címet a terheléselosztó előtérrendszer belső, privát IP-címek a virtuális hálózat leképezése szolgál.

A standard Load Balancer vezet be az új algoritmust a [hatékony, méretezhető és kiszámítható SNAT algoritmus](load-balancer-outbound-connections.md#snat) , és lehetővé teszi, hogy új funkciókat vehetnek igénybe, eltávolítja a félreérthetőség és kényszeríti explicit konfigurációk inkább oldalán hatásokat. Ezek a változások bontakozik ki új funkciókkal engedélyezéséhez szükségesek. 

Ne feledje, a Standard Load Balancer használata során a fő tenets a következők:

- egy szabály befejezése után a terheléselosztó erőforrás meghajtók.  az Azure minden programozási konfigurációban származik.
- több előtérrendszer érhetők el, ha az összes előtérrendszer használja, és egyes előtérrendszereken szorozza meg a rendelkezésre álló SNAT portok száma
- Válassza ki, és szabályozhatja, ha nem szeretné egy adott előtérbeli a kimenő kapcsolatokhoz használt.
- kimenő forgatókönyveket explicit és kimenő kapcsolat nem létezik, amíg meg van adva.
- terheléselosztási szabályok következtet, hogyan van programozva SNAT. Terheléselosztási szabályok az adott protokollt. SNAT adott protokoll és konfigurációs kell ennek megfelelően létrehozása helyett mellékhatása.

#### <a name="multiple-frontends"></a>Több előtérrendszer
Ha szeretne további SNAT portok mert sebességhez, vagy már tapasztalt a kimenő kapcsolatok számára egy nagy kereslet, azt is megteheti növekményes SNAT port készlet további előtérrendszer, szabályok és háttérkészletek ugyanahhoz a virtuális géphez való konfigurálásával az erőforrásokat.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Melyik előtéri használt vezérlő kimenő
Ha azt szeretné, csak oly módon, hogy egy adott előtérbeli IP-cím kimenő kapcsolatok korlátozásához, igény szerint letilthatja a szabályt, amely a kimenő leképezés fejezi ki a kimenő SNAT.

#### <a name="control-outbound-connectivity"></a>Ellenőrző kimenő kapcsolatok
A standard Load Balancer létezik a virtuális hálózati környezetében.  Egy virtuális hálózat egy elkülönített, privát hálózaton.  Csak egy nyilvános IP-cím-hozzárendelés létrejött, a nyilvános kapcsolódási nem engedélyezett.  Elérheti [virtuális hálózati Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md) mert azok a belső és a helyi a virtuális hálózathoz.  Ha szeretné a virtuális hálózatán kívüli célhelyre kimenő kapcsolatot, két lehetősége van:
- Standard Termékváltozatú nyilvános IP-cím hozzárendelése a virtuális gép típusú erőforrást példányszintű nyilvános IP-címet, vagy
- Helyezze a virtuális gép típusú erőforrást egy nyilvános Standard Load Balancer háttérkészlete.

Mindkettő lehetővé teszi a kimenő kapcsolat a virtuális hálózaton kívüli virtuális hálózatot. 

Ha Ön _csak_ rendelkezik egy belső Standard Load Balancer a háttérkészlet, ahol a virtuális gép típusú erőforrást társítva, a virtuális gép csak virtuális hálózati erőforrások elérhetővé és [a VNet szolgáltatás Végpontok](../virtual-network/virtual-network-service-endpoints-overview.md).  Követheti a kimenő kapcsolat létrehozásához az előző bekezdésben leírt lépéseket.

Kimenő kapcsolat, a Standard termékváltozatok marad, mielőtt hozzá nem rendelt virtuálisgép-erőforrás.

Felülvizsgálat [kimenő kapcsolatok veszik górcső alá részletes](load-balancer-outbound-connections.md).

### <a name="multife"></a>Több előtérrendszer
Load Balancer támogatja a több-több előtérrendszer szabályokat.  A standard Load Balancer bontja ki a kimenő forgatókönyveket.  Kimenő forgatókönyvek lényegében egy bejövő terheléselosztási szabály inverzét is.  A terheléselosztó bejövő szabályt is létrehoz egy társítás a kimenő kapcsolatok számára. Standard Load Balancer egy virtuális gép típusú erőforrást egy terheléselosztási szabály társított összes előtérrendszer használja.  Emellett a terheléselosztási szabály egy paramétert, és lehetővé teszi egy terheléselosztási szabályt le a kimenő kapcsolatot, amely lehetővé teszi az adott előtérrendszer, akár egy sem az alkalmazásában.

Összehasonlításáért alapszintű Load Balancer véletlenszerűen kiválaszt egyetlen frontend, és nincs lehetőség a szabályozására, hogy melyik lett kiválasztva.

Felülvizsgálat [kimenő kapcsolatok veszik górcső alá részletes](load-balancer-outbound-connections.md).

### <a name="operations"></a> Kezelési műveletek

Standard Load Balancer-erőforrások egy teljesen új infrastruktúra platformon létezik.  Ez lehetővé teszi, hogy a Standard termékváltozatok esetében gyorsabb felügyeleti műveletek és befejezési idők általában kevesebb mint 30 másodperc / Standard Termékváltozatú erőforrás.  Háttérkészletek növeli a méretét, a háttérbeli szükséges időtartam készlet változik is növelését.

Standard Load Balancer-erőforrások módosításához és a egy Standard nyilvános IP-cím áthelyezése egy virtuális gép másik sokkal gyorsabb.

## <a name="migration-between-skus"></a>Termékváltozatok közötti áttelepítés

SKU-k, amelyek nem módosítható. Kövesse az ebben a szakaszban egy erőforrás SKU áthelyezheti egy másikra.

>[!IMPORTANT]
>Tekintse át a teljes termékváltozatok közötti különbségeket, és gondosan kell vizsgálni a forgatókönyv a dokumentumot.  Szükség lehet további módosításokat igazíthatja a forgatókönyvet.

### <a name="migrate-from-basic-to-standard-sku"></a>A Standard Termékváltozat az alapszintű áttelepítése

1. Hozzon létre egy új Standard erőforrást (a Load Balancer és a nyilvános IP-címek, szükség szerint). Hozza létre újra a szabályok és mintavétel definíciókat.  Egy, a tcp/443-as TCP-mintavétel használatakor is korábban, érdemes lehet módosítani a mintavételi protokoll egy HTTPS-mintavétel, és adja hozzá az útvonalat.

2. Új létrehozása vagy a meglévő NSG-t a hálózati adapter vagy alhálózat engedélyezett elosztott terhelésű forgalmat, mintavétel, valamint az engedélyezni kívánt bármely egyéb forgalom.

3. Távolítsa el az alapszintű Termékváltozat erőforrásokat (Load Balancer és nyilvános IP-címek területen, amennyiben alkalmazhatók) összes Virtuálisgép-példányt. Győződjön meg arról, egy rendelkezésre állási csoport összes Virtuálisgép-példányt is eltávolítja.

4. Csatolja az összes Virtuálisgép-példány új Standard Termékváltozatú erőforrásra.

### <a name="migrate-from-standard-to-basic-sku"></a>Standard áttelepíteni az alapszintű Termékváltozat

1. Hozzon létre egy új alapszintű erőforrást (a Load Balancer és a nyilvános IP-címek, szükség szerint). Hozza létre újra a szabályok és mintavétel definíciókat.  Egy HTTPS-mintavétel váltani egy, a tcp/443-as TCP-mintavétel. 

2. Távolítsa el a Standard Termékváltozat erőforrások (Load Balancer és nyilvános IP-címek területen, amennyiben alkalmazhatók) összes Virtuálisgép-példányt. Győződjön meg arról, egy rendelkezésre állási csoport összes Virtuálisgép-példányt is eltávolítja.

3. Az új alapszintű Termékváltozat erőforrások csatolása az összes Virtuálisgép-példányok.

>[!IMPORTANT]
>
>Az alapszintű és Standard termékváltozatok általi használatára vonatkozó korlátozások is.
>
>Magas rendelkezésre ÁLLÁS portokat és a Standard termékváltozat diagnosztikai érhetők el csak a Standard termékváltozat. Nem lehet áttelepíteni a Standard Termékváltozat az alapszintű Termékváltozat az és nem is megőrzik ezeket a funkciókat.
>
>Sem az alapszintű, Standard Termékváltozatú különbségek számú rendelkezik, ebben a cikkben ismertetett módon.  Ellenőrizze, hogy tudomásul veszi és készítse elő azokat.
>
>SKU-k megfelelő a Load Balancer és a nyilvános IP-cím erőforrást kell használni. Alapszintű Termékváltozat a Standard Termékváltozat erőforrásokat és vegyesen nem rendelkezhet. Nem csatolhat mindkét SKU-hoz egyszerre önálló virtuális gépeket, rendelkezésre állási csoportban lévő virtuális gépeket vagy virtuálisgép-méretezési csoport típusú erőforrásokat.

## <a name="region-availability"></a>Régiónkénti elérhetőség

A standard Load Balancer jelenleg nyilvános felhő minden régióban.

## <a name="sla"></a>SLA

Standard Load Balancer Terheléselosztók egy 99,99 %-os SLA-val érhetők el.  Tekintse át a [Standard Load Balancer SLA](https://aka.ms/lbsla) részleteiről.

## <a name="pricing"></a>Díjszabás

Standard Load Balancerhez használati díjat számítunk fel.

- Száma beállított terheléselosztási és kimenő szabályok szabályok (bejövő NAT-szabályok nem számítanak bele a szabályok teljes száma)
- Adatok mennyisége a bejövő és kimenő függetlenül szabály feldolgozása. 

A Standard Load Balancer díjszabásáról a [Load Balancer díjszabását](https://azure.microsoft.com/pricing/details/load-balancer/) ismertető oldalon talál további információt.

## <a name="limitations"></a>Korlátozások

- SKU-k, amelyek nem módosítható. Meglévő erőforrás-Termékváltozat nem módosítható.
- Egy különálló virtuális gép típusú erőforrást, a rendelkezésre állási csoport erőforrás, vagy a virtuálisgép-méretezési készlet erőforrás egy SKU, soha nem is hivatkozhat.
- A terheléselosztási szabállyal a két virtuális hálózat nem terjedhetnek ki.  Előterek és azok kapcsolódó háttérpéldányokkal ugyanahhoz a virtuális hálózathoz kell működnie.  
- Load Balancer és előtérrendszerek globális virtuális társhálózatok között nem érhetők el.
- [Helyezze át az előfizetési műveleteket](../azure-resource-manager/resource-group-move-resources.md) nem támogatja a Standard Termékváltozat LB és a PIP-erőforrásokat.
- Webes feldolgozói szerepkörök egy VNet és más Microsoft-platformszolgáltatások nélkül elérhető lehet, ha csak egy belső Standard Load Balancer miatt a hogyan előtti – VNet-szolgáltatások és más platform sem services függvény mellékhatása szolgál. Meg kell támaszkodjon Ez a megfelelő szolgáltatás, saját maga, vagy az alapul szolgáló, előzetes értesítés nélkül platformmegbízhatósági módosíthatja. Meg kell mindig feltételezze, hogy létre kell [kimenő kapcsolat](load-balancer-outbound-connections.md) explicit módon, ha egy belső Standard Load Balancer csak használata esetén szükséges.
- A Load Balancer egy TCP- és UDP-termék az adott IP-protokollok terheléselosztásához és porttovábbításához.  A TCP és az UDP támogatja a terheléselosztási szabályokat és a bejövő NAT-szabályokat, de egyéb IP-protokollok (mint például az ICMP) nem támogatják őket. A Load Balancer nem szakít meg folyamatokat, nem válaszol rájuk, és semmilyen egyéb módon nem lép kapcsolatba egy UDP- vagy TCP-folyamat adatcsomagjával. Nem egy proxy. Sikeres érvényesítésének feltétele lesz egy előtér-kapcsolatának végre kell hajtania sávon hely ugyanazt a protokollt egy terheléselosztási és bejövő NAT-szabályt (TCP vagy UDP) használt a _és_ a virtuális gépek legalább egyikének kell létrehozni a válasz-ügyfélhez megtekintheti egy előtér-válaszát.  Sávon belüli választ nem fogad a terheléselosztó előtérrendszerét azt jelzi, hogy nincsenek olyan virtuális gépek is tud válaszolni.  Nem alkalmas interakcióba egy terheléselosztó előtérrendszerhez, anélkül, hogy egy virtuális gép tud válaszolni.  Ez vonatkozik a kimenő kapcsolatokra is, ahol a [porthelyettesítő SNAT](load-balancer-outbound-connections.md#snat) csak a TCP-hez és UDP-hez támogatott – bármely más IP-protokoll, többek között az ICMP is sikertelen lesz.  A mérsékléshez rendeljen hozzá egy példányszintű nyilvános IP-címet.
- Eltérően ez nyilvános Terheléselosztók [kimenő kapcsolatok](load-balancer-outbound-connections.md) átállás magánhálózati IP-címek a virtuális hálózaton belül, a nyilvános IP-címeket, ha belső Terheléselosztók nem fordítandó kimenő származik kapcsolatokat az előtér-alkalmazást is egy belső terheléselosztó magánhálózati IP-címtér találhatók.  Ezzel elkerülhető a lehetséges SNAT Erőforrásfogyás belül egyedi belső IP-címtér ahol fordítása nem kötelező.  A mellékhatása, hogy ha egy kimenő folyam egy virtuális gépről a háttér-készlet előtér-, a belső terheléselosztó, mely a készletben található folyamat megpróbálja _és_ le van képezve vissza magát, a folyamat mindkét alsó nem felelnek meg, és a flow meghiúsulnak. .  Ha a folyamat volt nem feleltethető meg vissza ugyanazon a virtuális Gépen, a háttér-készlet, amely létrehozta a folyamatot, hogy az előtér-, a folyamat sikeres lesz.   Amikor a folyamat vissza magát a maps oly módon, hogy a virtuális gép az előtér-, a kimenő folyam jelenik meg, és oly módon, hogy a virtuális gép saját maga a megfelelő bejövő forgalom jelenik meg. A vendég operációs rendszer szempontjából ugyanazon folyamat bejövő és kimenő részei nem egyeznek a virtuális gépen belül. A TCP-verem nem fogja ugyanazon folyam részeiként felismerni a folyamat két felét, mivel a forrás és a cél nem egyezik.  A folyamat bármely másik virtuális Géphez a háttérkészlet képez le, amikor a folyamat a feleket egyezni fog, és a virtuális gép sikeresen válaszolhat a folyamatot.  Ebben a forgatókönyvben a jelenség nem állandó hálózati kapcsolat időtúllépésének. Is megbízhatóan elérése érdekében ebben a forgatókönyvben számos gyakori megoldás (a háttérkészlet, a háttérbeli folyamatok származó készletek megfelelő belső terheléselosztó előtérrendszerhez) többek között vagy a külső proxy mögött a belső terheléselosztási beszúrása A terheléselosztó vagy [DSR stílusszabályok használatával](load-balancer-multivip-overview.md).  Habár a mérsékléshez használható nyilvános Load Balancer, a létrejövő forgatókönyvnél valószínű lesz az [SNAT elfogyása](load-balancer-outbound-connections.md#snat), és ez körültekintő felügyelet nélkül inkább kerülendő.

## <a name="next-steps"></a>További lépések

- A megismerése [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md).
- Ismerje meg [állapotadat-mintavételek](load-balancer-custom-probe-overview.md).
- Tudjon meg többet [rendelkezésre állási zónák](../availability-zones/az-overview.md).
- Ismerje meg [Standard Load Balancer diagnosztikai](load-balancer-standard-diagnostics.md).
- Ismerje meg [többdimenziós metrikák támogatott](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) a diagnosztikai [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).
- A megismerése [terheléselosztó kimenő kapcsolatok](load-balancer-outbound-connections.md).
- Ismerje meg [kimenő szabályok](load-balancer-outbound-rules-overview.md).
- Ismerje meg [TCP üresjárati a alaphelyzetbe](load-balancer-tcp-reset.md).
- Ismerje meg [magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabályok a Standard Load Balancer](load-balancer-ha-ports-overview.md).
- A megismerése [több Előtérrendszer rendelkező terheléselosztó](load-balancer-multivip-overview.md).
- Ismerje meg [virtuális hálózatok](../virtual-network/virtual-networks-overview.md).
- Tudjon meg többet [hálózati biztonsági csoportok](../virtual-network/security-overview.md).
- Ismerje meg [virtuális hálózati Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md).
- A másik hívóbetűt némelyikét [hálózat képességeivel](../networking/networking-overview.md) az Azure-ban.
- Tudjon meg többet [terheléselosztó](load-balancer-overview.md).
