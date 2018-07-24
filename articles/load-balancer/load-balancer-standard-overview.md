---
title: Az Azure Standard Load Balancer áttekintése |} A Microsoft Docs
description: Az Azure Standard Load Balancer funkcióinak áttekintése
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
ms.date: 07/20/2018
ms.author: kumud
ms.openlocfilehash: 1a7f37d3f95701779a16cf5dc6844fb67ee7f956
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215101"
---
# <a name="azure-load-balancer-standard-overview"></a>Az Azure Load Balancer Standard áttekintése

Az Azure Load Balancer lehetővé teszi az alkalmazások skálázását és magas rendelkezésre állású szolgáltatások létrehozása. Load Balancer bejövő, valamint a kimenő forgatókönyveket is használható, és alacsony késleltetésű, nagy átviteli sebességet, és akár több milliónyi összes TCP és UDP-alkalmazás méretezhető. 

Ez a cikk a Standard Load Balancer összpontosít.  Az Azure Load Balancerhez általános áttekintést, tekintse át a [Load Balancer áttekintése](load-balancer-overview.md) is.

## <a name="what-is-standard-load-balancer"></a>Mi a Standard Load Balancer?

A standard Load Balancer, amely egy új terheléselosztó-egy kibontott és részletesebb funkciókészlethez alapszintű terheléselosztóval szemben az összes TCP és UDP-alkalmazásokhoz.  Miközben sok Hasonlóságok, fontos ismerje meg az eltéréseket az ebben a cikkben ismertetett módon.

A Standard Load Balancer nyilvános vagy belső terheléselosztóként is használhatja. És a virtuális gépek is csatlakoztathatók egy nyilvános és a egy belső terheléselosztó erőforrás.

A Load Balancer erőforrásfüggvények időtúllépést, egy szabályt, az állapotfigyelő mintavételező és a háttérkiszolgáló-készlet definíciója szerint mindig ki.  Egy erőforrás tartalmazhat több szabály. Virtuális gépek, helyezze a háttérkészlet, adja meg a háttérkészlet, a virtuális gép Hálózatiadapter-erőforrásból.  Ezt a paramétert a hálózati profil átadni és bővített virtuálisgép-méretezési csoportok használata esetén.

Egyik legfőbb szempontja az erőforrás a virtuális hálózat hatókörét.  Alapszintű Load Balancer hatókörébe tartozó rendelkezésre állási csoport létezik, míg egy Standard Load Balancer teljesen integrálva van a virtuális hálózat hatókörét, és minden virtuális hálózat a fogalmak alkalmazhatók.

Load Balancer-erőforrások olyan objektumok, amelyen belül fejezhető ki hogyan a Azure kell a több-bérlős-infrastruktúrát a létrehozni kívánt forgatókönyvhöz a program.  Nincs terheléselosztó-erőforrások és a tényleges infrastruktúra közötti közvetlen kapcsolat Terheléselosztó létrehozása nem hoz létre egy példányt, mindig érhető el a kapacitás és nincsenek indítási vagy fontolja meg az késleltetések méretezést. 

>[!NOTE]
> Az Azure teljes körűen felügyelt terheléselosztási a szituációhoz kínál megoldásokat kínál.  Ha a TLS-lezárást ("SSL-alapú kiszervezéshez") vagy HTTP/HTTPS Kérelemfeldolgozás application layer keres, tekintse át [Application Gateway](../application-gateway/application-gateway-introduction.md).  Ha a globális DNS-terheléselosztás, tekintse át [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  A végpontok közötti forgatókönyvek részesülhetnek ezekkel a megoldásokkal kombinálva, igény szerint.

## <a name="why-use-standard-load-balancer"></a>Miért érdemes használni a Standard Load Balancer?

A Standard Load Balancer használatával skálázhatók az alkalmazások, és magas rendelkezésre állás érhető el akár kis méretű üzemelő példányoknál, akár nagy és összetett, többzónás architektúrák esetében.

Tekintse át az alábbi táblázatban áttekintheti az alapszintű Load Balancer és a Standard Load Balancer közötti különbségeket:

>[!NOTE]
> Új minták el kell fogadnia a Standard Load Balancer. 

| | Standard termékváltozat | Alapszintű termékváltozat |
| --- | --- | --- |
| Háttérkiszolgáló-készlet mérete | akár 1000 példányt | akár 100 példány |
| Háttérbeli címkészlet végpontok | bármelyik virtuális gépet, egyetlen virtuális hálózattal, programhoz való Blend alkalmazást a virtuális gépek rendelkezésre állási csoportok, köztük a virtuálisgép-méretezési csoport állítja be. | egy egyetlen rendelkezésre állási csoport vagy a virtuális gép méretezési csoportban lévő virtuális gépek beállítása |
| Rendelkezésre állási zónák | a zónaszintű és zónaredundáns az előtérrendszer bejövő és kimenő, kimenő forgalom leképezések stabilitást biztosít az zóna hiba, zónaközi terheléselosztás | / |
| Diagnosztika | Az Azure Monitor bájt és csomag számlálókat, egészségügyi többdimenziós metrikák mintavételi állapota, (TCP SZIN) kapcsolódási kísérletek, kimenő kapcsolat állapota (SNAT sikeres és sikertelen folyamatok), aktív adatsík mérések | Az Azure Log Analytics nyilvános Load Balancer csak, SNAT Erőforrásfogyás riasztás, háttérbeli címkészlet állapotfigyelő száma |
| Magas rendelkezésre ÁLLÁS portok | belső Load Balancer | / |
| Alapértelmezés szerint biztonságossá tétele | alapértelmezett lezárult a nyilvános IP-cím és a Load Balancer végpontok és a egy hálózati biztonsági csoportot kell használni kifejezetten engedélyezett a forgalom áramlását | alapértelmezett megnyitva, a hálózati biztonsági csoport nem kötelező |
| [Kimenő kapcsolatok](load-balancer-outbound-connections.md) | Több előtérrendszer és a egy terheléselosztási szabályt az elutasítás fiókkal. Egy kimenő forgatókönyv _kell_ explicit módon hozhatók létre a virtuális gép nem tudja használni a kimenő kapcsolatot.  [Virtuális hálózati Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md) kimenő kapcsolat nélkül érhető el, és nem beleszámít a feldolgozott adatokat.  Bármely nyilvános IP-címeket, beleértve a nem érhető el, mint a virtuális hálózati Szolgáltatásvégpontokkal az Azure PaaS-szolgáltatások keresztül kimenő kapcsolat és a feldolgozott adatok felé számát el kell érni. Ha csak egy belső terheléselosztó szolgálja ki a virtuális gépek, alapértelmezett SNAT keresztül kimenő kapcsolatok nem érhetők el. Kimenő SNAT programozás az átviteli protokoll adott protokoll, a bejövő terheléselosztási szabály alapján. | Egyetlen előtér véletlenszerűen kiválasztott, amikor több előtérrendszer jelen.  Belső Load Balancer szolgálja ki a virtuális gépek, alapértelmezett SNAT használ. |
| [Több előtérrendszer](load-balancer-multivip-overview.md) | Bejövő és [kimenő](load-balancer-outbound-connections.md) | Csak bejövő |
| [Az állapotfigyelő mintavételező le viselkedés](load-balancer-custom-probe-overview.md) | TCP-kapcsolatok a példány mintavételi le életben maradjon __és__ az összes mintavételek lefelé | A példány mintavételi le életben maradjon, TCP-kapcsolatokat. Összes TCP-kapcsolatok le az összes mintavételek leáll. |
| Kezelési műveletek | A legtöbb műveletek < 30 másodperc | 60 – 90 másodperc tipikus |
| SLA | 99,99 %-os két kifogástalan állapotú virtuális gépek elérési útjához | A virtuális gép SLA implicit | 
| Díjszabás | Feldolgozott adatok bejövő szabályok száma alapján, számlázunk ki vagy kimenő társított erőforrás  | díjmentes |

Felülvizsgálat [szolgáltatási korlátozásaival terheléselosztó](https://aka.ms/lblimits), valamint [díjszabás](https://aka.ms/lbpricing), és [SLA](https://aka.ms/lbsla).


### <a name="backend"></a>Háttérkészlet

Standard Load Balancer háttérkészletek bontja ki a virtuális hálózat bármely virtuális gép típusú erőforrást.  Legfeljebb 1000 háttérpéldányokkal tartalmazhat.  A háttér-példány egy IP-konfigurációt, amely egy hálózati adapter erőforrás tulajdonsága.

A háttérkészlet önálló virtuális gépek, a rendelkezésre állási csoportok vagy a virtual machine scale sets is tartalmazhat.  A háttérkészletében található erőforrások azokat is ötvözve. Kombinálhatja a háttérkészletben Load Balancer-erőforrásonként legfeljebb 150 erőforrásokat.

Amikor kiválasztja a háttérkészlet tervezéséről, megtervezheti a legkevésbé egyedi készlet háttérerőforrásokhoz tovább optimalizálható a felügyeleti műveletek időtartama száma.  Nincs adat adatsík teljesítmény vagy a méretezési csoport nincs különbség.

## <a name="az"></a>A rendelkezésre állási zónák

Standard Load Balancer támogatja a további képességek régióban, ahol érhetők el rendelkezésre állási zónák.  Ezek a funkciók hozzáadódnak az összes Standard Load Balancer biztosít.  A Standard Load Balancer nyilvános és belső rendelkezésre állási zónák konfigurációk érhetők el.

A rendelkezésre állási zónákban régióban üzembe helyezve, alapértelmezés szerint nem zónaszintű és előtérrendszerek válnak zónaredundáns.   Egy zónaredundáns előtér survives zóna hiba- és minden a zónák a dedikált infrastruktúra által egyidejűleg biztosítja. 

Ezenkívül garantálhatja a frontend egy adott zónához. A zónaszintű előtérbeli sorsát osztanak meg a megfelelő zónához, és csak egy zóna dedikált infrastruktúra által kiszolgált.

Zónaközi terheléselosztás háttérkészlete számára elérhető, és a virtuális hálózat bármely virtuális gép típusú erőforrást háttérkészlet része lehet.

Felülvizsgálat [részletes leírásáért lásd a rendelkezésre állási zónák képességekkel kapcsolatos](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a> Diagnosztika

A standard Load Balancer többdimenziós metrikák az Azure monitoron keresztül biztosít.  Ezek a metrikák szűrve, csoportosítva és egy adott dimenzió szétbontva.  Teljesítmény és a szolgáltatás állapotát a jelenlegi és korábbi betekintést nyújtanak.  A Resource Health is támogatott.  Alábbiakban a támogatott diagnosztikai rövid áttekintése:

| Metrika | Leírás |
| --- | --- |
| Virtuális IP-cím elérhetősége | Load Balancer Standard folyamatosan gyakorol az adatok útvonalat egy adott régión belül a terheléselosztó előtérrendszerét, egészen az SDN-verem, amely támogatja a virtuális Géphez való. Kifogástalan állapotú példányok továbbra is, a mérés követi az alkalmazás elosztott terhelésű forgalmat az adott elérési úton. Az adatok elérési útja, az ügyfelek által használt is ellenőrzi. A mérték az alkalmazás számára, és nem ütközik más műveleteket.|
| Dedikált IP-CÍMMEL rendelkezésre állása | Load Balancer Standard használ egy elosztott Állapotfigyelő szolgáltatás, amely figyeli az alkalmazás végponti állapotát a konfigurációs beállításoknak megfelelően-tesztelés. Ez a metrika összesítést tartalmaz, vagy egy végpont szűrt-nézetben minden egyes példány végpont a terheléselosztóban tárolókészlet.  Láthatja, hogyan a Load Balancer megtekinti a állapot-mintavételi konfigurációban aszinkronitást az alkalmazás állapotát.
| Szinkronizálás a mi csomagok | Load Balancer Standard nem TCP-kapcsolatok leáll vagy TCP vagy UDP packet flow interakcióba. Folyamatok és azok kézfogások mindig a forrás- és a Virtuálisgép-példány között vannak. A TCP protokoll forgatókönyvek hatékonyabb elhárításához végezhet SZIN felhasználása csomagok számlálók tudni, hogy hány TCP-kapcsolat kísérletet tesz. A metrika a fogadott TCP SZIN csomagok számát jelenti.|
| SNAT-kapcsolatok | Load Balancer Standard előtérbeli nyilvános IP-címre van masqueraded kimenő folyamatok számát jelenti. SNAT portjait egy kimeríthető erőforrást. Ez a metrika biztosíthat az arra utalhat, hogy hogyan érdemes az alkalmazás van szüksége az SNAT a kimenő folyamatokhoz.  Sikeres és sikertelen kimenő SNAT folyamatok számlálói készül jelentés, és használható ismertetünk az elhárításukkal és a kimenő forgalom állapotának ismertetése.|
| Bájt számlálók | Load Balancer Standard jelentések az előtér-feldolgozott adatokat.|
| Csomag-számlálók | Load Balancer Standard a jelentés a feldolgozott előtér-csomagokat.|

Felülvizsgálat [Standard Load Balancer diagnosztikai veszik górcső alá részletes](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>Magas rendelkezésre ÁLLÁS portok

Standard Load Balancer támogatja egy új típusú szabály.  

Terheléselosztási szabályok segítségével győződjön meg arról, az alkalmazás méretezési és magas megbízhatóságú konfigurálhatja. Egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabállyal, a Standard Load Balancer használatakor kiszolgálónként egy belső Standard Load Balancer előtérbeli IP-cím minden rövid élettartamú port terheléselosztási folyamat nyújt.  A funkció akkor hasznos, más esetekben, ahol nem praktikus, vagy nem kívánatos egyedi port megadása.

Egy magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabály lehetővé teszi, hogy aktív-passzív vagy aktív – aktív n + 1 forgatókönyvek létrehozása a hálózati virtuális berendezések, és minden alkalmazás, amely igényel bejövő portokat nagyobb adattartományokat.  Az állapotfigyelő mintavételező segítségével határozza meg, melyik háttérrendszereket kell kapnia új folyamatok.  A hálózati biztonsági csoport segítségével port tartomány forgatókönyv Emulálhatja.

>[!IMPORTANT]
> Ha azt tervezi, egy hálózati virtuális berendezés használata, útmutatást, hogy a terméket a magas rendelkezésre ÁLLÁSÚ portok az tesztelték a tárolóeszközök gyártójával egyeztetve ellenőrizze, és hajtsa végre a megvalósítás vonatkozó konkrét útmutatást. 

Felülvizsgálat [magas rendelkezésre ÁLLÁSÚ portok veszik górcső alá részletes](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Alapértelmezés szerint biztonságossá tétele

A standard Load Balancer található teljesen előkészítve a virtuális hálózathoz.  A virtuális hálózaton egy privát, lezárt hálózat.  Standard Load Balancer Terheléselosztók és a Standard nyilvános IP-címeket úgy tervezték, hogy lehetővé teszik a virtuális hálózaton elérhetők a virtuális hálózaton kívül, mert ezek az erőforrások mostantól alapértelmezés szerint lezárt, kivéve, ha megnyitja őket. Ez azt jelenti, hálózati biztonsági csoportok (NSG) használnak az explicit módon engedélyezi és engedélyezett forgalom engedélyezett.  A teljes virtuális adatközpont létrehozhat és NSG-t keresztül döntse el, mit és mikor érdemes lesz elérhető.  Ha nem rendelkezik alhálózat NSG-t vagy a hálózati Adaptert a virtuális gép erőforrás, forgalom nem engedélyezett az erőforrás eléréséhez.

NSG-ket és hogyan alkalmazhatja őket a forgatókönyvhöz kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/security-overview.md).

### <a name="outbound"></a> Kimenő kapcsolatok

Load Balancer bejövő és kimenő forgatókönyveket támogatja.  A standard Load Balancer jelentősen eltér az alapszintű Load Balancer megállapodást kimenő kapcsolatokat.

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
Load Balancer támogatja a több-több előtérrendszer szabályokat.  A standard Load Balancer bontja ki a kimenő forgatókönyveket.  Kimenő forgatókönyvek lényegében egy bejövő terheléselosztási szabály inverzét is.  A bejövő terheléselosztási szabály is létrehoz egy társítás a kimenő kapcsolatok számára. Standard Load Balancer terheléselosztási szabály egy virtuális gép erőforráshoz tartozó összes előtérrendszer használja.  Emellett egy paraméterrel a terheléselosztási szabály, és lehetővé teszi, hogy letiltsa a kimenő kapcsolatot, amely lehetővé teszi az adott előtérrendszer, akár egy sem az alkalmazásában terheléselosztási szabály.

Összehasonlításáért alapszintű Load Balancer véletlenszerűen kiválaszt egyetlen frontend, és nincs lehetőség a szabályozására, hogy melyik lett kiválasztva.

Felülvizsgálat [kimenő kapcsolatok veszik górcső alá részletes](load-balancer-outbound-connections.md).

### <a name="operations"></a> Kezelési műveletek

Standard Load Balancer-erőforrások egy teljesen új infrastruktúra platformon létezik.  Ez lehetővé teszi, hogy a Standard termékváltozatok esetében jelentősen gyorsabb felügyeleti műveletek és befejezési idők általában kevesebb mint 30 másodperc / Standard Termékváltozatú erőforrás.  Vegye figyelembe, hogy a méret növekedését háttérkészletek, a háttérrendszer szükséges időtartam készlet változik is növelését.

Standard Load Balancer-erőforrások módosításához és a egy Standard nyilvános IP-cím áthelyezése egy virtuális gép másik sokkal gyorsabb.

## <a name="migration-between-skus"></a>Termékváltozatok közötti áttelepítés

SKU-k, amelyek nem módosítható. Kövesse az ebben a szakaszban egy erőforrás SKU áthelyezheti egy másikra.

>[!IMPORTANT]
>Tekintse át a teljes termékváltozatok közötti különbségeket, és gondosan kell vizsgálni a forgatókönyv a dokumentumot.  Szükség lehet további módosításokat igazíthatja a forgatókönyvet.

### <a name="migrate-from-basic-to-standard-sku"></a>A Standard Termékváltozat az alapszintű áttelepítése

1. Hozzon létre egy új Standard erőforrást (a Load Balancer és a nyilvános IP-címek, szükség szerint). Hozza létre újra a szabályok és mintavétel definíciókat.

2. Új létrehozása vagy a meglévő NSG-t a hálózati adapter vagy alhálózat engedélyezett elosztott terhelésű forgalmat, mintavétel, valamint az engedélyezni kívánt bármely egyéb forgalom.

3. Távolítsa el az alapszintű Termékváltozat erőforrásokat (Load Balancer és nyilvános IP-címek területen, amennyiben alkalmazhatók) összes Virtuálisgép-példányt. Győződjön meg arról, egy rendelkezésre állási csoport összes Virtuálisgép-példányt is eltávolítja.

4. Csatolja az összes Virtuálisgép-példány új Standard Termékváltozatú erőforrásra.

### <a name="migrate-from-standard-to-basic-sku"></a>Standard áttelepíteni az alapszintű Termékváltozat

1. Hozzon létre egy új alapszintű erőforrást (a Load Balancer és a nyilvános IP-címek, szükség szerint). Hozza létre újra a szabályok és mintavétel definíciókat. 

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

## <a name="region-availability"></a>Regionális elérhetőség

Load Balancer Standard érhető el jelenleg az összes nyilvános régióban.

## <a name="sla"></a>SLA

Standard Load Balancer Terheléselosztók egy 99,99 %-os SLA-val érhetők el.  Tekintse át a [Standard Load Balancer SLA](https://aka.ms/lbsla) részleteiről.

## <a name="pricing"></a>Díjszabás

Standard Load Balancer, amely a díjazás a terheléselosztási szabályok konfigurálása és a feldolgozott bemeneti és kimeneti adatok alapján. A Standard Load Balancer a díjszabásról, látogasson el a [Load Balancer díjszabása](https://aka.ms/lbpricing) lapot.

## <a name="limitations"></a>Korlátozások

- SKU-k, amelyek nem módosítható. Meglévő erőforrás-Termékváltozat nem módosítható.
- Egy különálló virtuális gép típusú erőforrást, a rendelkezésre állási csoport erőforrás, vagy a virtuálisgép-méretezési készlet erőforrás egy SKU, soha nem is hivatkozhat.
- A terheléselosztási szabállyal a két virtuális hálózat nem terjedhetnek ki.  Előterek és azok kapcsolódó háttérpéldányokkal ugyanahhoz a virtuális hálózathoz kell működnie.  
- Load Balancer és előtérrendszerek globális virtuális társhálózatok között nem érhetők el.
- [Helyezze át az előfizetési műveleteket](../azure-resource-manager/resource-group-move-resources.md) nem támogatja a Standard Termékváltozat LB és a PIP-erőforrásokat.
- Webes feldolgozói szerepkörök egy VNet és más Microsoft-platformszolgáltatások nélkül elérhető lehet, ha csak egy belső Standard Load Balancer miatt a hogyan előtti – VNet-szolgáltatások és más platform sem services függvény mellékhatása szolgál. Meg kell támaszkodjon Ez a megfelelő szolgáltatás, saját maga, vagy az alapul szolgáló, előzetes értesítés nélkül platformmegbízhatósági módosíthatja. Meg kell mindig feltételezze, hogy létre kell [kimenő kapcsolat](load-balancer-outbound-connections.md) explicit módon, ha egy belső Standard Load Balancer csak használata esetén szükséges.
- Terheléselosztó, amely a TCP vagy UDP-terheléselosztás és porttovábbítást a konkrét IP-protokollt.  Terheléselosztási szabályok és bejövő NAT-szabályokat a TCP és UDP támogatott és más IP-protokollok, beleértve az ICMP esetében nem támogatott. Terheléselosztó nem zárja be, válaszol, vagy ellenkező esetben interaktívan UDP vagy TCP-folyamat a hasznos. Akkor sem proxy. Sikeres érvényesítésének feltétele lesz egy előtér-kapcsolatának végre kell hajtania sávon hely ugyanazt a protokollt egy terheléselosztási és bejövő NAT-szabályt (TCP vagy UDP) használt a _és_ a virtuális gépek legalább egyikének kell létrehozni a válasz-ügyfélhez megtekintheti egy előtér-válaszát.  Sávon belüli választ nem fogad a terheléselosztó előtérrendszerét azt jelzi, hogy nincsenek olyan virtuális gépek is tud válaszolni.  Nem alkalmas interakcióba egy terheléselosztó előtérrendszerhez, anélkül, hogy egy virtuális gép tud válaszolni.  Ugyanez vonatkozik a kimenő kapcsolatok ahol [port helyettesítő SNAT](load-balancer-outbound-connections.md#snat) van csak a TCP és UDP; támogatott más IP-protokollok többek között az ICMP is sikertelenek lesznek.  Példányszintű nyilvános IP-címet hozzárendelni csökkentése érdekében.
- Eltérően ez nyilvános Terheléselosztók [kimenő kapcsolatok](load-balancer-outbound-connections.md) átállás magánhálózati IP-címek a virtuális hálózaton belül, a nyilvános IP-címeket, ha belső Terheléselosztók nem fordítandó kimenő származik kapcsolatokat az előtér-alkalmazást is egy belső terheléselosztó magánhálózati IP-címtér találhatók.  Ezzel elkerülhető a lehetséges SNAT Erőforrásfogyás belül egyedi belső IP-címtér ahol fordítása nem kötelező.  A mellékhatása, hogy ha egy kimenő folyam egy virtuális gépről a háttér-készlet előtér-, a belső terheléselosztó, mely a készletben található folyamat megpróbálja _és_ le van képezve vissza magát, a folyamat mindkét alsó nem felelnek meg, és a flow meghiúsulnak. .  Ha a folyamat volt nem feleltethető meg vissza ugyanazon a virtuális Gépen, a háttér-készlet, amely létrehozta a folyamatot, hogy az előtér-, a folyamat sikeres lesz.   Amikor a folyamat vissza magát a maps oly módon, hogy a virtuális gép az előtér-, a kimenő folyam jelenik meg, és oly módon, hogy a virtuális gép saját maga a megfelelő bejövő forgalom jelenik meg. A vendég operációs rendszer szempontjából a bejövő és kimenő részeit ugyanezt a folyamatot a virtuális gépen belül nem egyezik. A TCP protokollkészlet nem ismerik fel ezeket a feleket az adott adatfolyam részeként ugyanezt a folyamatot, a forrás- és nem egyeznek.  A folyamat bármely másik virtuális Géphez a háttérkészlet képez le, amikor a folyamat a feleket egyezni fog, és a virtuális gép sikeresen válaszolhat a folyamatot.  Ebben a forgatókönyvben a jelenség nem állandó hálózati kapcsolat időtúllépésének. Is megbízhatóan elérése érdekében ebben a forgatókönyvben számos gyakori megoldás (a háttérkészlet, a háttérbeli folyamatok származó készletek megfelelő belső terheléselosztó előtérrendszerhez) többek között vagy egy harmadik féltől származó proxy mögött a belső terheléselosztási beszúrási A terheléselosztó vagy [DSR stílusszabályok használatával](load-balancer-multivip-overview.md).  Nyilvános Load Balancer csökkentése érdekében használhat, miközben a hibalehetőség-e az eredményül kapott forgatókönyv [SNAT Erőforrásfogyás](load-balancer-outbound-connections.md#snat) és el kell kerülni, kivéve, ha körültekintően felügyelt.

## <a name="next-steps"></a>További lépések

- A megismerése [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md)
- Tudjon meg többet [rendelkezésre állási zónák](../availability-zones/az-overview.md).
- Ismerje meg [Standard Load Balancer diagnosztikai](load-balancer-standard-diagnostics.md).
- Ismerje meg [többdimenziós metrikák támogatott](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers) a diagnosztikai [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).
- A megismerése [terheléselosztó kimenő kapcsolatok](load-balancer-outbound-connections.md)
- Ismerje meg [Standard Load Balancer a magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabályok](load-balancer-ha-ports-overview.md)
- A megismerése [terheléselosztót, több Előtérrendszer](load-balancer-multivip-overview.md)
- Ismerje meg [virtuális hálózatok](../virtual-network/virtual-networks-overview.md).
- Tudjon meg többet [hálózati biztonsági csoportok](../virtual-network/security-overview.md).
- Ismerje meg [virtuális hálózati Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md)
- A másik hívóbetűt némelyikét [hálózat képességeivel](../networking/networking-overview.md) az Azure-ban.
- Tudjon meg többet [terheléselosztó](load-balancer-overview.md).
