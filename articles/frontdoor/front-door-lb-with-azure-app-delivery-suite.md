---
title: Azure bejárati ajtajának szolgáltatás – az Azure-hatóságuknál kézbesítési suite terheléselosztás |} A Microsoft Docs
description: Ez a cikk segít a további tudnivalók az Azure javasolja a terheléselosztás és a hozzá tartozó kézbesítési alkalmazáscsomag
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 253ec8c0d1d6725e6ae5c47c79882284c633d6e9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984466"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Terheléselosztás és az Azure application kézbesítés

## <a name="introduction"></a>Bevezetés
A Microsoft Azure több globális és regionális szolgáltatásokat kínál a kezelése, hogyan ossza a hálózati forgalmat és elosztott terhelésű: a Traffic Manager, bejárati ajtajának szolgáltatás, Application Gateway és a Load Balancer.  Együtt az Azure számos régióban és a zónaszintű architektúra, ezek a szolgáltatások együttes használata lehetővé teszi nagy teherbírású, méretezhető, nagy teljesítményű alkalmazásokat hozhat létre.

![Alkalmazáscsomag-kézbesítés ][1]
 
Ezek a szolgáltatások megszakadnak a két kategóriába sorolhatók:
1. **Globális terheléselosztási szolgáltatás** , mint például a Traffic Manager és a bejárati ajtajának terjesztése Ön végfelhasználóitól érkező forgalmat a regionális háttérrendszerekre között, felhőkön és még a hibrid helyszíni szolgáltatások között. Globális terheléselosztás irányítja a forgalmat a legközelebbi szolgáltatás háttérrendszerhez, és reagálás a módosításokat a szolgáltatás megbízhatóságát és teljesítményét a felhasználók folyamatosan elérhető, a maximális teljesítményének fenntartása érdekében. 
2. **Regionális terheléselosztási szolgáltatás** például a Standard Load Balancer vagy az Application Gateway lehetővé teszi a virtuális hálózatok (Vnetek) forgalom elosztásához a virtuális gépek (VM) vagy egy adott régión belül zónaszintű Szolgáltatásvégpontok között.

Az alkalmazás a globális és regionális szolgáltatások kombinálásával biztosít egy végpontok közötti megbízható, nagy teljesítményű, és biztonságos módon irányítani a forgalmat, és a felhasználók az IaaS, PaaS és helyszíni szolgáltatások. A következő szakaszban ismertetünk az ilyen szolgáltatásokkal.

## <a name="global-load-balancing"></a>Terhelés globális kiegyenlítéséhez
**A TRAFFIC Manager** globális DNS terheléselosztást biztosít. Megvizsgálja a beérkező DNS és a egy megfelelően működő háttér megfelelően a vásárlói ki van választva az útválasztási házirend fűzi hozzá. Útválasztási módszerek lehetőségek közül választhat:
- Teljesítmény-útválasztási a kérelmező küldeni a legközelebbi háttérrendszer késés szempontjából.
- Az Útválasztás a háttérrendszernek, más háttérrendszerek vissza, az összes forgalom irányítása be prioritás.
- Súlyozott ciklikus időszeletelés útválasztás, amely elosztja a forgalmat, amely az egyes háttérrendszerek van rendelve a súlyozás alapján.
- Győződjön meg arról, hogy meghatározott földrajzi régióban lévő kérelmezőket irányítja a háttérrendszerek ezekben a régiókban leképezve a földrajzi útválasztásának (például Spanyolország érkező kérelmek legyen átirányítva, az Egyesült Államok – kelet-Azure-régióban)
- Alhálózat útválasztási, amely lehetővé teszi, hogy az IP-címet, hogy azok érkező kéréseket küld az adott háttérrendszer címtartományok háttérkomponenseinek (például a vállalati központ IP-címtartományból csatlakozás minden felhasználó kapja meg, mint az általános különböző webes tartalom felhasználó)

Az ügyfél közvetlenül csatlakozik az adott háttérrendszer. Az Azure Traffic Manager észleli, ha egy háttérszolgáltatás nem kifogástalan, és majd átirányítja a megfelelő egy másik példánya az ügyfelek. Tekintse meg [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) dokumentációban talál további információt a szolgáltatás.

**Az Azure bejárati ajtajának szolgáltatás** dinamikus webhely gyorsításként (DSA), beleértve a globális HTTP-terheléselosztást biztosít.  Úgy tűnik, bejövő HTTP-kérelmeket a legközelebbi service háttérrendszer útvonalakat és a megadott gazdagépnév, az URL-cím és a konfigurált szabályok régiót.  
Bejárati ajtajának megszünteti a HTTP-kérelmekre, a Microsoft hálózatához a peremhálózaton, és aktívan mintavételek alkalmazás vagy infrastruktúra állapotát vagy a késés módosítások észleléséhez.  Bejárati ajtajának majd mindig irányítja a forgalmat a leggyorsabb és a rendelkezésre álló (megfelelő) háttérrendszeréhez. Bejárati ajtajának hivatkoznak [útválasztási architektúra](front-door-routing-architecture.md) részletei és [forgalom-útválasztási módszerek](front-door-routing-methods.md) további információ a szolgáltatás.

## <a name="regional-load-balancing"></a>Regionális terheléselosztási
Az Application Gateway alkalmazáskézbesítési vezérlőt (ADC) szolgáltatásként biztosítja, amely számos 7. rétegbeli terheléselosztási lehetőséget nyújt alkalmazásának. Lehetővé teszi az ügyfelek optimalizálhatják a webfarmok termelékenységét a processzorigényes SSL-lezárások az Alkalmazásátjáró felé történő kiszervezésével. Egyéb 7. rétegbeli útválasztási lehetőségeket tartalmazza, Ciklikus időszeleteléses elosztását bejövő forgalom, a cookie-alapú munkamenet-affinitást, a URL-cím-alapú útválasztás és a egy application gateway mögött több webhelyet is üzemeltethet. Az Application Gateway konfigurálható egy internetre irányuló átjáró, egy belső átjáró vagy mindkettőt. Application Gateway-példány az Azure teljes körűen felügyelt, méretezhető és magas rendelkezésre állású. Diagnosztikai és naplózási képességek széles skáláját biztosítja a jobb kezelhetőség érdekében.
Load Balancer nagy teljesítményű, kis késleltetésű 4. rétegű terheléselosztási szolgáltatásokat nyújtó UDP és TCP protokoll minden, az Azure SDN-verem szerves részét képezi. Kezeli a bejövő és kimenő kapcsolatokat. A nyilvános és belső elosztott terhelésű végpontok konfigurálása, és definiáljon szabályokat a bejövő kapcsolatok leképezése háttérbeli készletet destinations TCP- és HTTP-állapotellenőrzéséhez beállítások használatával kezelheti a szolgáltatás rendelkezésre állása.


## <a name="choosing-a-global-load-balancer"></a>Egy globális a terheléselosztót választja
A Traffic Manager és az Azure bejárati ajtajának között globális terheléselosztó globális útválasztás kiválasztásakor érdemes mi hasonló, és mi a két szolgáltatás.   Mindkét szolgáltatás megadása
- **Többszörös közötti redundancia:** egy adott régióban leáll, ha forgalmat zökkenőmentesen régió felé irányítja a legközelebbi beavatkozása nélkül, az alkalmazás tulajdonosa.
- **Legközelebb eső régióban útválasztás:** automatikusan adatforgalmat legközelebb eső régió

</br>Az alábbi táblázat a Traffic Manager és az Azure bejárati ajtajának szolgáltatás közötti különbségeket ismerteti:</br>

| Traffic Manager | Azure Front Door Service |
| --------------- | ------------------------ |
|**Bármely protokoll:** mert Traffic Manager a DNS-rétegben működik, irányíthatja bármilyen típusú hálózati forgalmat; HTTP, a TCP, UDP, stb. | **HTTP-gyorsítás:** bejárati ajtajának forgalom küldése a proxyn keresztül, a Microsoft Edge-hálózat.  Emiatt a HTTP (S)-kérelmek tekintse meg a teljesítmény és a késés fejlesztései csökkenti a késéseket az SSL-egyeztetési és a gyakori elérésű AFD kapcsolatot az alkalmazás használatával.|
|**A helyszíni útválasztási:** útválasztási egy DNS-rétegben, a forgalom mindig irányul pont-pont.  Útválasztás a fiókirodában, a helyszíni adatközponthoz is igénybe vehet egy közvetlen elérési útja; a saját hálózaton is a Traffic Managerrel. | **Független méretezhetősége:** mert bejárati ajtajának működik együtt a HTTP-kérelem, kéréseket a különböző URL-cím elérési átirányíthatók a másik háttér / regionális szolgáltatási készletek (mikroszolgáltatás-alapú) alapuló szabályokat és az egyes alkalmazás mikroszolgáltatások állapotát.|
|**A számlázás formátum:** DNS-alapú számlázás méretezi a felhasználóival és a szolgáltatások több felhasználóval rendelkező, magasabb szintű használati költségek csökkentése érdekében trületek. |**A beágyazott biztonsági:** bejárati ajtajának lehetővé teszi, hogy a szabályokat, például a sebességkorlátozás és IP-ACL-ing a háttérrendszerekre védelmére, mielőtt a forgalom eléri az alkalmazást. 

</br>A teljesítménnyel, a működési követelmények és a bejárati ajtó a HTTP-munkaterhelések biztonsági funkcióit javasoljuk, ügyfelek bejárati ajtajának használja a HTTP számítási feladatai.    A TRAFFIC Manager és a bejárati ajtajának segítségével párhuzamosan minden forgalmat az alkalmazásához. 

## <a name="building-with-azures-application-delivery-suite"></a>Az Azure tartalomkézbesítési alkalmazáscsomag való fejlesztés 
Azt javasoljuk, hogy a websites, API-k, az összes szolgáltatás alapvetően georedundáns beállítással rendelkeznek, és a felhasználók a legközelebbi (legkisebb késéssel) a forgalom továbbítására helyet őket, amikor csak lehetséges.  Traffic Manager, bejárati ajtajának szolgáltatás, Application Gateway és a Load Balancer szolgáltatások kombinálásával lehetővé teszi a build földrajzilag és zonally redundáns megbízhatóság, a méretezési csoport és a teljesítmény maximalizálása érdekében.

Az alábbi ábrán egy példa szolgáltatás, amely az ezek a szolgáltatások olyan kombinációját használja hozhat létre egy globális webszolgáltatás ismertetünk.   Ebben az esetben a fejlesztő használatban van a Traffic Manager irányíthatja a fájl- és objektum kézbesítési globális háttérrendszerek bejárati ajtajának használatával globálisan irányíthatja az URL-cím elérési utak, amelyek megfelelnek a minta/tároló / * a szolgáltatáshoz, a migrálás az App Service-útválasztás az összes többi közben az Application Gateway-átjárók regionális kérelmeket.

A régióban, az IaaS-szolgáltatás, az alkalmazás fejlesztőjének úgy döntött, hogy bármely URL-címek, amelyek megfelelnek a minta/képek / * üzemeltetett virtuális gépek, amelyek eltérnek a web farm többi a dedikált készletből.

Emellett az alapértelmezett VM-készletet, a dinamikus tartalmat szolgáltató kommunikáljon egy háttérbeli futó adatbázis magas rendelkezésre állású fürt kell. A teljes üzembe helyezés Azure Resource Manageren keresztül állítható be.

Az alábbi ábra az ebben a forgatókönyvben architektúráját mutatja be:

![Kézbesítési alkalmazáscsomag részletes architektúrája][2] 

> [!NOTE]
> Ebben a példában az csak a terheléselosztási szolgáltatások, Azure által kínált számos lehetséges konfigurációk egyik. A TRAFFIC Manager, a bejárati ajtajának, az Application Gateway és a Load Balancer lehet vegyes és terheléselosztási igényeinek legjobban megfeleljen egyeztetését. Például ha SSL alapú kiszervezést, illetve a 7. rétegbeli feldolgozása már nem szükséges, Load Balancer az Application Gateway helyett használható.


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozzon létre egy bejárati ajtajának](quickstart-create-front-door.md).
- Ismerje meg, [bejárati ajtajának működése](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png