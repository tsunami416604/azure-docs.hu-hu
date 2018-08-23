---
title: Az Azure Traffic Manager – gyakori kérdések |} A Microsoft Docs
description: Ez a cikk ismerteti a Traffic Managerrel kapcsolatos gyakori kérdésekre adott válaszok
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: kumud
ms.openlocfilehash: 6c196d16258e4bf000f998899086c7a6d0197fba
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054278"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>A TRAFFIC Manager – gyakori kérdések (GYIK)

## <a name="traffic-manager-basics"></a>A TRAFFIC Manager alapjai

### <a name="what-ip-address-does-traffic-manager-use"></a>Milyen IP-címet nem használja a Traffic Manager?

A [Traffic Manager működése](../traffic-manager/traffic-manager-how-it-works.md), a Traffic Manager a DNS szintjén működik. A megfelelő szolgáltatási végpont ügyfelek DNS-válaszok küld. Az ügyfelek ezután csatlakozik a szolgáltatásvégpont közvetlenül, nem Traffic Manageren keresztül.

A Traffic Manager ezért nem biztosít egy végpontot, illetve az ügyfelek IP-címet. Ha szeretne statikus IP-címet kell konfigurálni, a szolgáltatás nem a Traffic Manager, a szolgáltatás.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Milyen típusú forgalom lehet irányítva a Traffic Manager használatával?
A [Traffic Manager működése](../traffic-manager/traffic-manager-how-it-works.md), egy Traffic Manager-végpont lehet bármely internetkapcsolattal rendelkező belül vagy kívül az Azure-szolgáltatás. Ezért a Traffic Manager érkező forgalmat a nyilvános internetről végpontok között az, hogy rendszer emellett az internetre irányuló irányíthatja. Ha végpontokat, amelyek egy privát hálózaton belül (például egy belső verzióját [Azure Load Balancer](../load-balancer/load-balancer-overview.md#internalloadbalancer)) vagy a felhasználók ezeket a forgalom a Traffic Manager nem használható ilyen belső hálózatokról és DNS kér.


### <a name="does-traffic-manager-support-sticky-sessions"></a>A Traffic Manager támogatja a "kiemelt" munkamenetek?

A [Traffic Manager működése](../traffic-manager/traffic-manager-how-it-works.md), a Traffic Manager a DNS szintjén működik. DNS-válaszok használatával a közvetlen ügyfelek számára, hogy a megfelelő szolgáltatásvégpontot. Ügyfelek csatlakoznak a szolgáltatásvégpont közvetlenül, nem Traffic Manageren keresztül. A Traffic Manager ezért nem látja a HTTP-forgalmat az ügyfél és a kiszolgáló között.

Ezenkívül a Traffic Manager által fogadott DNS-lekérdezés forrás IP-címét a rekurzív DNS-szolgáltatás, az ügyfél nem tartozik. A Traffic Manager ezért semmilyen módon nem lehet nyomon követése az egyes ügyfelek rendelkezik, és "rögzített" munkamenetek nem valósítható meg. Ez a korlátozás az összes forgalom DNS-alapú felügyeleti rendszeren közös, és nem adott Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Miért látok HTTP-hibát, amikor a Traffic Manager használatával?

A [Traffic Manager működése](../traffic-manager/traffic-manager-how-it-works.md), a Traffic Manager a DNS szintjén működik. DNS-válaszok használatával a közvetlen ügyfelek számára, hogy a megfelelő szolgáltatásvégpontot. Az ügyfelek ezután csatlakozik a szolgáltatásvégpont közvetlenül, nem Traffic Manageren keresztül. A TRAFFIC Manager nem talál HTTP-forgalom ügyfél és kiszolgáló közötti nem. Ezért az alkalmazásból érkező lehet bármilyen HTTP-hibát látja. Az ügyfél kapcsolódni az alkalmazáshoz minden DNS-feloldási lépést nem teljes. Beavatkozás, amely a Traffic Manager az alkalmazás adatforgalmat rendelkezik, amely tartalmazza.

További vizsgálat ezért irányul, az alkalmazást.

Az ügyfél böngészője által küldött HTTP-állomásfejlécet az problémák leggyakoribb forrása. Győződjön meg arról, hogy az alkalmazás használ a tartománynév helyes állomás fejlécével fogadására van konfigurálva. A végpontok az Azure App Service használatával, lásd: [webes alkalmazás egy egyéni tartománynév konfigurálása az Azure App Service, Traffic Managert használva a](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Mit jelent a teljesítményre gyakorolt hatását a Traffic Manager használatával?

A [Traffic Manager működése](../traffic-manager/traffic-manager-how-it-works.md), a Traffic Manager a DNS szintjén működik. Mivel az ügyfelek közvetlenül kapcsolódik a szolgáltatásvégpontokat, ez nincs teljesítmény hatással lesz felszámítva, ha a Traffic Manager használatával, ha a kapcsolat létrejött.

A Traffic Manager a DNS szintjén alkalmazásokba van integrálva, mivel azt a DNS-feloldási lánc beszúrni egy további DNS-címkeresés szükséges. A művelet hatása a Traffic Manager DNS-feloldási idő minimális. A TRAFFIC Manager egy globális névkiszolgáló-hálózat, és használja [csomópontválasztásos](https://en.wikipedia.org/wiki/Anycast) DNS biztosításához hálózatkezelés lekérdezések mindig legyenek irányítva a legközelebbi elérhető névkiszolgáló. Emellett a DNS-válaszok gyorsítótárazását azt jelenti, hogy a Traffic Managert használva a felmerülő további DNS-késés csak azokra a munkamenetek tört.

A teljesítmény metódus a legközelebbi elérhető végpontra irányítja a forgalmat. Az eredmény az, hogy ez a módszer társított általános teljesítményre gyakorolt minimális legyen-e. DNS-késés növelése a kisebb hálózati késést a végponthoz való lesz eltolva.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Milyen alkalmazásprotokollok használható a Traffic Managerrel?

A [Traffic Manager működése](../traffic-manager/traffic-manager-how-it-works.md), a Traffic Manager a DNS szintjén működik. A DNS-címkeresés befejeződése után az ügyfelek az alkalmazás végpontjának közvetlenül kapcsolódni, nem Traffic Manageren keresztül. A kapcsolat, ezért bármely alkalmazás protokollt használhat. Akkor válassza ki azt a TCP, a monitorozási protokoll, a Traffic Manager végpont szolgáltatásállapot-figyelést végezhető bármilyen alkalmazás protokollok használata nélkül. Ha szeretné, hogy a health-protokoll segítségével választja, a végpont képesnek kell lennie HTTP vagy HTTPS GET kérelmek megválaszolására.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Használhatom-e a Traffic Manager "csupasz" tartománynév?

Nem. A DNS-szabványok nem engedélyeznek CNAME-rekordokat más DNS-rekordok az azonos nevű párhuzamosan. DNS-zóna felső pontja (vagy legfelső szintű) mindig tartalmazza a két meglévő DNS-rekordok; a SOA és a mérvadó Névkiszolgálói rekordokat. Ez azt jelenti, hogy egy CNAME rekordot a DNS-szabványok megsértése nélkül nem a zóna legfelső pontján hozható létre.

A TRAFFIC Manager egy DNS CNAME-rekordot a személyes DNS-név hozzárendelése szükséges. Például leképezheti `www.contoso.com` DNS-nevével a Traffic Manager profil `contoso.trafficmanager.net`. Ezenkívül a Traffic Manager-profilt egy második DNS CNAME rekord jelezze az ügyfélnek csatlakoznia melyik végponthoz adja vissza.

A probléma megkerüléséhez azt javasoljuk egy HTTP-átirányítás forgalom a csupasz tartománynévnek nevéből, egy másik URL-címet, amely ezután használhatja a Traffic Manager használatával. Például a "contoso.com" csupasz tartománynévnek is irányítsa át a felhasználókat a "www.contoso.com" a Traffic Manager DNS-névre mutató CNAME.

A Traffic Manager csupasz tartományok esetében teljes körűen támogatja még a szolgáltatás-megvalósítás van követi. A szolgáltatás kérelem által a támogatási regisztrálhatja [lehetőségre szavazott azt, hogy a közösségi visszajelzések webhelyünkön](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Nem a Traffic Manager fontolja meg az ügyfél alhálózati cím DNS-lekérdezések kezelése esetén? 
Igen, a DNS-lekérdezés forrás IP-címe mellett kap (Ez általában a DNS-feloldó IP-címét), Geographic és a teljesítmény-útválasztási módszerek keresések végrehajtása, ha a traffic manager is figyelembe veszi az ügyfél alhálózati cím esetén tartalmazza a lekérdezés által a feloldó a kérés a végfelhasználó nevében.  
Pontosabban a [RFC 7871 – ügyfél-alhálózathoz, a DNS-lekérdezések](https://tools.ietf.org/html/rfc7871) biztosít, amely egy [Bővítménymechanizmussal DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) feloldók, amelyek támogatják ezt az ügyfél-alhálózat címen át is.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Mi a DNS-Élettartamot, és hogyan ez befolyásolja a felhasználók?

A Traffic Manager DNS-lekérdezést hajtanak végre, ha a válasz ideje-élettartam (TTL) nevű beállít egy értéket. Ez az érték, amelynek egység másodpercen belül van, azt jelzi, hogy a DNS feloldók aktiválásához a mennyi ideig gyorsítótárazza a választ a. Amíg DNS feloldók nem biztos, hogy a gyorsítótár ezt az eredményt, gyorsítótárazás, lehetővé teszi, hogy válaszolni a lekérdezésekre későbbi ki a gyorsítótárat a Traffic Manager DNS-kiszolgálók helyett. Ez hatással van a válaszokat a következő:
- nagyobb TTL csökkenti a Traffic Manager DNS-kiszolgálókon, ami csökkentheti a költségeket az ügyfél mivel kiszolgált lekérdezések száma számlázható használati land lekérdezések száma.
- nagyobb TTL potenciálisan csökkentheti a hajtsa végre egy DNS-címkeresés szükséges időt.
- nagyobb TTL azt is jelenti, hogy az adatok nem tükrözi a legfrissebb állapotinformációkat, amely a Traffic Manager rendelkezik kombináljuk ellenőrzési ügynökeit.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Hogyan lehet magas vagy alacsony állítható az élettartam a Traffic Manager-válaszok?

Beállíthatja, jelenleg egy profil szintjén, a DNS-Élettartamot kérelemegységszámot 0 másodperc és magas, mint 2 147 483 647 másodpercek száma (a megfelelő tartomány maximuma [– az RFC 1035](https://www.ietf.org/rfc/rfc1035.txt )). A 0 azt jelenti, hogy az alsóbb rétegbeli DNS feloldók nem gyorsítótárazzák a lekérdezési válaszadás felgyorsítása érdekében, és az összes lekérdezés várhatóan a Traffic Manager DNS-kiszolgálók a feloldásához elérésére TTL.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Hogyan képes megérteni a kötet várható a saját profil lekérdezések? 
A mérőszámok egyikét a megadott Traffic Manager által a lekérdezés száma válaszolt-profillal. Ezt az információt kaphat egy profil szintű összesítés, vagy akkor is feloszthatja az azt tovább lásd: a kötetet, ahol a meghatározott végpontokhoz műveletnek lekérdezések. Emellett állíthat be riasztásokat, amelyek figyelmeztetik, ha a lekérdezés válaszkötet átlép feltételek állított be. További részletekért [Traffic Manager-mérőszámok és riasztások](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>A TRAFFIC Manager Geographic forgalom-útválasztási módszer

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Mik az egyes használati esetek, ahol földrajzi útválasztásának hasznos? 
Földrajzi útválasztási típus használható minden olyan forgatókönyvben ahol Azure-ügyfeleket kell különbséget tenni a felhasználók földrajzi régió alapján. Például a földrajzi forgalom-útválasztási módszert használja, engedélyezheti a felhasználók számára az adott régióban, mint el más régiókból eltérő felhasználói élmény. Egy másik példa, hogy egy adott régióban felhasználók kiszolgálása az internetszolgáltatójuk csak által az adott régióban végpontok igénylő helyi adatok szuverenitását megbízások megfelel.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Hogyan válasszak a Teljesítménycentrikus útválasztási mód vagy a földrajzi útválasztási mód használata kell? 
Ez a két legnépszerűbb útválasztási módszer közötti fő különbség, hogy a teljesítmény-útválasztási módszer elsődleges célja, hogy a forgalmat küldeni a végpontot, amely a legkisebb késéssel képes biztosítani a hívónak. mivel a Geographic útválasztás elsődleges célja az, hogy egy-egy térségen kényszerítése időkorlát a hívók számára, hogy szándékosan irányíthatja őket egy adott végpontnak. Bár ez nem mindig igaz az átfedés között földrajzi egyezés és kisebb késést biztosít, mivel történik. Előfordulhat, hogy lehet egy végpontot egy másik földrajzi hely, amely a hívó késés jobb felhasználói élményt biztosíthat, és ebben az esetben Teljesítménycentrikus útválasztást küld a felhasználónak, hogy a végpont, de a földrajzi útválasztásának mindig elküldi őket a végpont számára leképezett azok a földrajzi régióban. Hogy még törölje a jelet, fontolja meg az alábbi példa - Geographic-útválasztást, teheti például nem szokványos leképezések Ázsia minden forgalmat küldeni az Amerikai Egyesült Államokban és Ázsiában-végpontokra irányuló minden Egyesült Államokbeli forgalmat végpontok. Ebben az esetben földrajzi útválasztásának szándékosan elvégzi az pontosan mit konfigurált rá és teljesítményoptimalizálás nem veszi figyelembe. 
>[!NOTE]
>Előfordulhat, hogy forgatókönyvek, ahol előfordulhat, hogy mind a teljesítmény és földrajzi útválasztási lehetőségeket, az ezen forgatókönyvek beágyazott profilok remek választás a következőkhöz lehetnek. Például, ahol az összes forgalom küldése az Észak-Amerika beágyazott-profilhoz, amely rendelkezik az Amerikai Egyesült Államokban végpontok földrajzi útválasztási szülő profil beállítása és használata a teljesítmény-útválasztási e forgalmat küldeni az optimális végpontot, hogy a csoporton belül. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Mik a Traffic Manager által támogatott földrajzi útválasztásának régiók? 
A Traffic Manager által használt ország/régió-hierarchia található [Itt](traffic-manager-geographic-regions.md). Amíg ezt oldal megőrzött módosításai naprakészen szerepeljenek, szoftveresen is ugyanazokat az információkat használatával lekérhető a [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Hogyan nem a traffic manager, hogy hol éppen lekérdezi a felhasználó a? 
Traffic Manager megvizsgálja a forrás IP-címét a lekérdezés (Ez nagy valószínűséggel a egy helyi DNS-feloldási ennek során a felhasználó nevében lekérdezése), és a egy belső IP-cím terület térkép segítségével határozza meg, a helyet. Ezen a térképen az interneten változásainak rendszeresen frissül. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Ez garantálja, hogy a Traffic Manager helyesen meghatározhatja a pontos minden esetben a felhasználó földrajzi helye?
Nem, a Traffic Manager nem tudja garantálni, hogy a földrajzi régiót, azt a DNS-lekérdezés forrás IP-címről kikövetkeztetni mindig felel meg a felhasználó földrajzi helye a következő okok miatt: 

- Először az előző – gyakori kérdések leírtak a a forrás IP-cím, láthatjuk, hogy egy DNS-feloldási ennek során a Keresés a felhasználó nevében. Bár a földrajzi helyet, a DNS-feloldási a felhasználó földrajzi helye jó proxy, azt is lehet különböző attól függően, hogy a DNS-feloldási szolgáltatás és az adott DNS-feloldási szolgáltatás használatát választotta egy ügyfél kapacitásigényéhez. Tegyük fel Malajzia található ügyfelek eszköz beállításait használja megadhatja egy DNS-feloldási szolgáltatás, amelynek DNS-kiszolgáló a szingapúri előfordulhat, hogy első követi a lekérdezés megoldások felhasználó és eszköz kezelésére. Ebben az esetben a Traffic Manager csak tekintheti meg a feloldó IP-címet, amely megfelel a szingapúri helyre. Lásd még a korábbi vonatkozó ügyfél alhálózati cím támogatás – gyakori kérdések ezen az oldalon.

- Második a Traffic Manager használja egy belső térkép földrajzi régióban fordítási IP-cím. A térkép érvényesítve, és növeli az adatok pontosságát, és a fiók számára az internetről fenyegető jellege rendszeresen frissíteni, miközben továbbra is fennáll a lehetősége, hogy az információk ne legyen-e a földrajzi helyet az IP-pontos másolatát címek.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>A végpont helyezkedjenek el azzal, konfigurálva van az a földrajzi útválasztásának ugyanabban a régióban nem kell? 
A végpont helye nem, amelyre régióban is le lehet képezni, korlátozás nélkül ír elő. Például a egy végpontot az Egyesült Államok – közép-India Azure-régióhoz indiai átirányítva, hogy minden felhasználó rendelkezhet.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>A végpontok egy profilt, amely nincs konfigurálva ehhez a földrajzi útválasztásának is hozzárendelhetők földrajzi régiók? 

Igen, ha egy profil útválasztási módszer nem földrajzi, használhatja a [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) földrajzi régiók hozzárendelése végpontokat a profilhoz. Nem földrajzi útválasztási típus-profilok esetén figyelmen kívül ezt a konfigurációt. Ha egy ilyen profil földrajzi útválasztási típus módosítja egy későbbi időpontban, a Traffic Manager használhatja ezeket a hozzárendeléseket.


### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Miért jelenik meg hibaüzenet jelenik meg a-útválasztási módszert egy meglévő profilt, módosítsa Geographic?

Földrajzi útválasztási profil alatt a végpontok rendelve legalább egy régió van szükség. Földrajzi útválasztási típus konvertál egy meglévő profilt, szüksége lesz a használó végpontok földrajzi régiók társítása a [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) a földrajzi útválasztási típusának módosítása előtt. Portál használata esetén először törölje a végpontot, módosítsa a profil-útválasztási módszert földrajzi, és adja hozzá az a végpontok a földrajzi régió hozzárendelése együtt. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Miért van, erősen ajánlott, hogy ügyfeleink helyett végpontokat a profilhoz a beágyazott profilok létrehozása az földrajzi Útválasztás engedélyezése mellett? 

Egy régióban egy profilon belül csak egy végpont rendelhetők, ha a földrajzi útválasztási típus használatával. Ha nem, hogy a végpont egy beágyazott típus egy gyermek-profil csatlakozik, a forgalmat, hogy a végpont történik a nem megfelelő állapotú, ha Manager továbbra is forgalmat küldeni, a tulajdonos alternatív nem küldött forgalom nem minden jobb óta. A TRAFFIC Manager elvégzi a nem végezhető el egy másik végpontra, még akkor, amikor a hozzárendelt régió rendelve a végpontot, hogy nem megfelelő állapotú (például ha egy végpontot, amely rendelkezik régió Spanyolország nem kifogástalan, még a nem egy másik végponti feladatátvétel nem megy a régióban "szülője" a régió, Európa hozzárendelt rendelkezik). Ez történik, győződjön meg arról, hogy a Traffic Manager tiszteletben tartja a földrajzi határokon, hogy egy ügyfél rendelkezik-e a telepítő a profilban. Az előnye, hogy egy másik végpont-ba irányuló feladatátvétel kapni, ha a végpont nem megfelelő állapotú, javasoljuk, hogy a földrajzi régiók kell rendelni a benne lévő egyes végpontokat helyett több végponttal rendelkező beágyazott profilok. Ezzel a módszerrel a egy végpontot a beágyazott gyermek profil sikertelen lesz, ha forgalmat is feladatátvételt egy másik végpontra belül ugyanazt a beágyazott gyermek-profilt.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Vannak-e az API-verzióban, amely támogatja az útválasztási típus korlátozások?

Igen, csak az API 2017-03-01-es verzió és az újabb támogatja a földrajzi útválasztásának írja. Bármely régebbi API-verziók nem használható földrajzi útválasztási típusú létrehozott profilok vagy földrajzi régiók hozzárendelése végpontok. Profilok lekérése egy Azure-előfizetéshez egy régebbi API-verzió használata esetén minden olyan földrajzi útválasztási típusú profil nem jelennek meg. Ezenkívül régebbi API-verziók használata esetén minden olyan profilt, amely rendelkezik egy földrajzi hozzárendelés végpontokat, nem rendelkezik a földrajzi régió hozzárendelése látható adott vissza.

## <a name="real-user-measurements"></a>Valós felhasználói mérések

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Mik a valós felhasználói mérések használatának előnyei?
Teljesítmény-útválasztási módszer használatakor a Traffic Manager szerzi vizsgálatával szerezheti be a forrás IP-cím és EDNS ügyfél alhálózat (ha az átadott) csatlakozni a végfelhasználó számára az ajánlott Azure-régióban, és ellenőrizni annak szemben a hálózati várakozási intelligenciát a szolgáltatás karbantartja. Valós felhasználói mérések fokozza a Ez az a teljes felhasználói bázis azzal, hogy azok hozzájárulnak a késés táblázat biztosítása mellett ez a tábla megfelelő kiterjedő a végfelhasználó hálózatokat, a végfelhasználók csatlakoztatása az Azure-élményt. Ez továbbítani tudja a végfelhasználók számára a nagyobb pontosság vezet.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Valós felhasználói mérések használhatom az-Azure régiók?
Valós felhasználói mérések méri, és csak a késéssel jelentések Azure-régióban érhető el. Nem Azure-régiókban lévő üzemeltetett végpontokkal rendelkező teljesítményalapú útválasztást használ, ha továbbra is élvezheti a funkció által késés információ reprezentatív Azure-régió választotta volna társítani a végpontot kell nőtt.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Útválasztási módszer a valós felhasználói mérések számos előnyt biztosít?
A további információk a valós felhasználói mérések tapasztalatok csak a teljesítmény-útválasztási módszert használó profilok vonatkoznak. Vegye figyelembe, hogy a valós felhasználói mérések hivatkozás esetén érhető el az összes profil megtekintése az Azure Portalon keresztül.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Van szükségem ahhoz, hogy valós felhasználói mérések egyes profilokat külön-külön?
Nem, csak engedélyeznie kell azt egyszer előfizetésenként és minden késési adatok mérni, és jelentett érhetők el az összes profilra.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Hogyan lehet kikapcsolni a valós Felhasználómérés az előfizetésemet?
Állítsa le összegyűjtése és vissza késési mérések küldését az ügyfélalkalmazásban leállításakor valós felhasználói mérések kapcsolatos díjakat halmoz fel. Például mérési JavaScript weblapokhoz beágyazott, állítsa le a JavaScript, vagy ha az oldal jelenik meg a meghívási kikapcsolásával a funkció használatát.

Is kikapcsolhatja valós Felhasználóiélmény-mérések úgy, hogy a kulcs törlése. A kulcs a törölt bármely mérési küldi el a Traffic Manager, a kulcs nem őrződnek meg.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Használhatok valós felhasználói mérések eltérő weboldalak ügyfélalkalmazások?
Igen, valós felhasználói mérések célja, hogy a végfelhasználó ügyfelek különböző típusú keresztül gyűjtött adatokat. Ez a GYIK frissülni fog a ügyfélalkalmazások új típusú támogatott beolvasása.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Hány mérések minden alkalommal, amikor a valós felhasználói mérések engedélyezve van a weblap jelenik meg?
Valós felhasználói mérések a méréshez megadott JavaScript használata esetén minden egyes oldalmegjelenítések hat méréseket eredményez. Ezek ezután jelentett vissza a Traffic Manager szolgáltatáshoz. Vegye figyelembe, hogy ez a szolgáltatás-mérések száma alapján kell fizetnie a Traffic Manager szolgáltatásnak küldött jelentésben. Például ha a felhasználó ellép erről a weblapot, míg a mérés, de mielőtt azt jelentették, ezek a mértékek nem lesznek figyelembe véve a számlázás tekintetében.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Van-e késést valós felhasználói mérések parancsfájl futtatása a saját weblap előtt?
Nem, nem lesz programozott késleltetés, a szkript meghívása előtt.

### <a name="can-i-use-configure-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Használható a valós felhasználói mérések konfigurálása az csak az Azure-régiók mérése szeretnék?
Indítva, nem, minden egyes alkalommal a valós felhasználói mérések parancsfájlja hat Azure-régiókban a szolgáltatás által meghatározott készletét. A beállított állatpotát módosításokat, és nagy számú ilyen indítások fordulhat elő, ha a mérték lefedettség kiterjedő különböző Azure-régióban.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Korlátozhatja a kérés érkezett egy adott számot mérések száma?
A mérési JavaScript van beágyazva a weblapot, és van a teljes körű felügyeletet során való indítása és leállítása, használja azt. Mindaddig, amíg a Traffic Manager szolgáltatás Azure-régiók listáját mért kérelmet kap, a rendszer régiók készletét adja vissza.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Láthatja a saját ügyfélalkalmazás valós felhasználói mérések részeként mérésekből?
A mérési logika fut az ügyfélalkalmazásban, mivel Ön teljes hozzáféréssel, mi történik, többek között a késési mérések jelent meg. A TRAFFIC Manager nem készít jelentést az összesített nézet a mérések alapján a kulcs az Ön előfizetéséhez kapcsolva kapott.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Módosíthatja a Traffic Manager által biztosított mérési parancsfájlt?
Habár mi van beágyazva a weblapon irányítását, kifejezetten nem ajánlott, győződjön meg arról, hogy méri, és az késleltetések jelenti helyesen a mérési parancsfájlt olyan módosítások végrehajtását.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Ez lehet majd mások is lássák a valós felhasználói mérések használhatják kulcsot?
A mérési szkript egy beágyazott lehet majd mások is lássák a parancsfájlt és a valós felhasználói mérések (RUM) kulccsal. Azonban fontos, hogy tudja, hogy ezt a kulcsot eltér az előfizetés-azonosítóját, és akkor jön létre, a Traffic Manager által csak erre a célra használható. A az kulcsot, hogy nem veszélyezteti a az Azure-fiók biztonságát.

### <a name="can-others-abuse-my-rum-key"></a>Mások gyalázhat saját az kulcsot?
Bár a másokkal való használatához a kulcs téves információkat küldhet az Azure-bA vegye figyelembe, hogy néhány nem megfelelő mérések nem módosítja az Útválasztás, mivel azt figyelembe venni a többi mérések együtt érkező. Ha módosítania kell a kulcsok, újra létrehozhatja a kulcs ekkor a régi kulcsot el lesz vetve.

###  <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Kell helyezni a mérési JavaScript összes weblapok?
Valós felhasználói mérések mérések növekedés számának további értéket kínál. Eldöntéséhez, hogy kell helyezni, a weblapok vagy egy select néhány kellene ugyanakkor, hogy. Azt javasoljuk, így a leggyakrabban felkeresett oldal ahol a felhasználó az adott oldalon öt másodperc vagy annál maradni várható első lépésként.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>A végfelhasználók információt azonosíthatók a Traffic Manager által valós felhasználói mérések használata?
A megadott mérési JavaScript használata esetén a Traffic Manager a végfelhasználó ügyfél IP-címét és a helyi DNS-feloldási használják a forrás IP-címe lesz. A TRAFFIC Manager ügyfél IP-címet használja, csak után azt nem lehet azonosítani az adott végfelhasználó, aki a mérések küldött csonkolt. 

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>A weblap mérési valós felhasználói mérések kell használnia a Traffic Manager útválasztási nem?
Nem, akkor nem kell a Traffic Manager használatát. A Traffic Manager útválasztási oldalon külön a valódi felhasználók mérési részéről működik, és bár egy jó ötlete van két webes ugyanahhoz a tulajdonsághoz, akkor nem kell lennie.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Van szükségem bármely szolgáltatás az Azure-régiók és a valós Felhasználóiélmény-mérések használatához?
Nem, nem kell minden olyan kiszolgáló melletti összetevők működéséhez a valós Felhasználóiélmény-mérések az Azure-ban üzemeltetni. A vastagságú rendszerképet letöltötte a mérési JavaScript és a szolgáltatás fut, a különböző Azure-régióban üzemeltetett és az Azure által felügyelt. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Saját Azure sávszélesség-használat növeli valós felhasználói mérések használata esetén?
Ahogy már említettük, az előző válasz, valós felhasználói mérések kiszolgálóoldali összetevőinek birtokolt és felügyelt az Azure-ban. Ez azt jelenti, hogy az Azure sávszélesség-használat oka, hogy használja a valós felhasználói mérések nem nő. Vegye figyelembe, hogy melyik Azure-szolgáltatások díjait kívül bármely sávszélesség-használat nem tartalmaz. Csak egy vastagságú lemezkép letöltése a mérték a késést, Azure-régióba történő által használt sávszélesség minimálisra azt. 

## <a name="traffic-view"></a>Forgalomnézet

### <a name="what-does-traffic-view-do"></a>Mire használható a Traffic View?
A TRAFFIC View funkciója a Traffic Manager, amely segítséget nyújt a felhasználók számára, és hogyan szerzett kapcsolatban. A Traffic Manager és a hálózati késés üzletiintelligencia-táblák, amely a szolgáltatás kezeli a következő biztosítson által fogadott lekérdezések használja:
- A régiók, ahol a felhasználók csatlakozzanak a végpontok az Azure-ban.
- Az ezekben a régiókban csatlakozó felhasználók mennyisége.
- Az Azure-régióban, amely azokat a rendszer első irányítja.
- Késés szerzett ezen Azure-régióban.

Ez az információ felhasználásához földrajzi térképként rétegen történjen, és a táblázatos nézet mellett érhető el, hogy töltse le a nyers adatok a portálon keresztül érhető el.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Hogyan milyen előnyei származhatnak a Traffic View?

A TRAFFIC View áttekintést nyújt az általános a forgalom a Traffic Manager-profilok kap. Különösen használat, a felhasználói bázis kapcsolódik a és egyaránt fontosabb átlagos késése szerzett van. Ezután használhatja ezeket az adatokat, amelyben kell összpontosítania, például az Azure erőforrás-igényű, amely a felhasználók kisebb késéssel régióba kibontásával területek található. Egy másik insight származtatást forgalmi nézet használatával forgalmat különböző régiók, amelyek viszont segítségével megfontoltabb döntéseket hozhat a növelésével vagy csökkentésével ezekben a régiókban készlet mintáinak megtekintéséhez.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Miben különbözik a Traffic View a Traffic Manager-metrikák elérhető az Azure monitor?

Az Azure Monitor segítségével a profilját, és a végpont által fogadott forgalom összesített szintű ismertetése. Lehetővé teszi a végpontok állapotát nyomon teszi elérhetővé az egészségügyi ellenőrzésének az eredménye. Ezeket nemcsak és csatlakozás az Azure-bA egy regionális szinten a végfelhasználói élmény megértése van szüksége, amikor a Traffic View, amelyek eléréséhez használható.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Ezeket a Traffic View EDNS ügyfél alhálózati információkat használja?

Szolgálja ki az Azure Traffic Manager DNS-lekérdezéseket fontolja meg az Útválasztás és pontosabbá ECS információkat. De amikor hoz létre az adatkészlet, amely mutatja, ahol a felhasználók csatlakozik, a Traffic View csak a DNS-feloldó IP-címet használ.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Hány napnyi adat nem használja a Traffic View?

A TRAFFIC View kimenetét fel az adatokat, a hét nap, a nap előtt megelőző Ön megtekintéskor hoz létre. Ez egy mozgó időablak, és minden alkalommal, látogasson el a legfrissebb adatokat fogja használni.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Hogyan kezeli a Traffic View külső végpontokat?

Külső végpontok Azure Traffic Manager-profil-régiók kívül tárolt használatakor kiválaszthatja, hogy egy Azure-régióban, amely a késési jellemzői (ez valóban szükséges teljesítmény-útválasztási módszer használatakor) proxy leképezve. -E ezt a hozzárendelést az Azure-régióban, azt, hogy Azure-régió késés metrikák használható a Traffic View kimeneti létrehozásakor. Ha nincs Azure-régió van megadva, akkor a késési adatok azokat a külső végpontokat adatok üres lesz.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Kell forgalmi nézet engedélyezése az egyes profilok egy előfizetésben?

Az előzetes verzió ideje alatt a Traffic View-előfizetés szintjén lett engedélyezve. A tettük az általános rendelkezésre állás előtti fejlesztések részeként hogy mostantól engedélyezheti a Traffic View profil szintjén, lehetővé téve, hogy ez a funkció részletesebb engedélyezése. Alapértelmezés szerint a Traffic View profil letiltásra kerül.

>[!NOTE]
>Ha az előfizetés szintjén a Traffic View előzetes verzió ideje alatt engedélyezve van, most minden előfizetés alatt a profil engedélyezheti újra kell.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Hogyan kapcsolhatja ki a Traffic View? 
A Traffic View kikapcsolhatja minden profilhoz a portálon vagy REST API használatával. 

### <a name="how-does-traffic-view-billing-work"></a>Hogyan működik a Traffic View számlázás?

Forgalom díjszabásának megtekintése a kimenet létrehozásához használt adatpontok száma alapján. A támogatott csak adattípus jelenleg a profilt kap a lekérdezések. Emellett csak számlázzuk ki, amely azért volt szükség, ha engedélyezve van a Traffic View feldolgozását. Ez azt jelenti, hogy forgalmi nézet engedélyezése egy adott időszakban az adott hónapban, és egyéb időszakokban kikapcsolása, ha csak az adatpontok feldolgozása közben a szolgáltatás már engedélyezve van a számla felé száma.

## <a name="traffic-manager-endpoints"></a>Traffic Manager-végpontok

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Használható a Traffic Manager több előfizetésekből végpontokkal rendelkező?

Több előfizetés végpontok használata nem lehetséges az Azure Web Apps szolgáltatással. Az Azure Web Apps megköveteli, hogy minden a Web Apps használt egyéni tartománynév csak használatos egyetlen előfizetésben jön létre. Nem alkalmas Web Apps használatát több előfizetés azonos tartománynévvel.

Minden olyan végpont esetében egynél több előfizetéssel a végpontok Traffic Manager használata. A Resource Managerben minden olyan előfizetésből végpontokat is hozzáadhatók a Traffic Manager, mindaddig, amíg a személy, a Traffic Manager-profil konfigurálása a végponthoz olvasási hozzáféréssel rendelkezik. Ezek az engedélyek használatával is megadható [Azure Resource Manager szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/role-assignments-portal.md).


### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Használható a Traffic Manager felhőalapú szolgáltatás staging (átmeneti) pontokat használó?

Igen. Felhőszolgáltatás staging (átmeneti) tárhelyek beállítható a Traffic Manager külső végpontokat. Állapot-ellenőrzések továbbra is lehet díjon számoljuk el az Azure-végpontot. A külső végpont típus használatban van, mert a mögöttes szolgáltatás módosításait a rendszer nem követi automatikusan. Külső végpontokat, a Traffic Manager nem tudja észlelni a Felhőszolgáltatás leállítása vagy törlése. Ezért a Traffic Manager továbbra is állapot-ellenőrzések díjszabása mindaddig, amíg a végpont le van tiltva vagy törölve.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>A Traffic Manager támogatja az IPv6-os végpontjaiig?

A TRAFFIC Manager jelenleg nem biztosít IPv6-addressible névkiszolgálókat. A Traffic Manager továbbra is használható az IPv6-alapú ügyfelek IPv6-végpontokhoz való csatlakozáshoz. Egy ügyfél nem kérést DNS közvetlenül a Traffic Manager. Ehelyett az ügyfél használja egy rekurzív DNS-szolgáltatás. Egy csak IPv6-alapú ügyfél kérelmeket küld a rekurzív DNS szolgáltatás IPv6-n keresztül. Majd a rekurzív szolgáltatás használatával az IPv4 Traffic Manager névkiszolgálóit csatlakoznia kell.

A TRAFFIC Manager válaszol a végpont DNS-névvel. IPv6 végpont támogatása érdekében a végpont DNS-név IPv6-címre mutató DNS AAAA típusú rekord léteznie kell. A TRAFFIC Manager állapot-ellenőrzések csak IPv4-címeket támogatja. A szolgáltatás elérhetővé tenni egy IPv4-végpontot, a DNS-nevét kell.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Használhatom-e a Traffic Manager ugyanabban a régióban egynél több webalkalmazást?

Általában a Traffic Manager több különböző régióban üzembe helyezett alkalmazások forgalmat szolgál. Azonban azt is használható, egy alkalmazás rendelkezik ugyanabban a régióban több üzemelő. Azure Traffic Manager-végpontot Azure régióban hozzáadni ugyanazon a Traffic Manager-profil nem teszi lehetővé több webalkalmazás-végpont.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group"></a>Hogyan helyezhetek át Azure-beli saját Traffic Manager-profil egy másik erőforráscsoportba?

Azure-végpontok Traffic Manager-profil társított nyomon követi az erőforrás-azonosítók használatával. Ha egy Azure-erőforrás-végpontként (például nyilvános IP-cím, klasszikus Cloud Service, webalkalmazás vagy beágyazott módon használt egy másik Traffic Manager-profil) használt egy másik erőforráscsoportban található, az erőforrás-azonosító módosítások került. Ebben a forgatókönyvben jelenleg, frissítenie kell a Traffic Manager-profil által először, majd vissza a végpontokat a profilhoz. 

##  <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager végpont figyelése

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>A Traffic Manager hatással a meghibásodások az Azure-régióban van?

A TRAFFIC Manager az Azure-ban magas rendelkezésre állású alkalmazások kézbesítésének fő összetevője.
Magas rendelkezésre állást biztosít, a Traffic Manager egy rendkívül magas szintű rendelkezésre állást és a regionális meghibásodással rugalmasak lehetnek.

A kialakításból fakadóan Traffic Manager-összetevők olyan rugalmas bármely Azure-régióhoz befejezése sikertelen. A rugalmasság az összes Traffic Manager-összetevőkre vonatkozik: a DNS-kiszolgálók, az API-t, a tárolási réteget és az endpoint monitoring szolgáltatás név.

A teljes Azure-régióhoz szolgáltatáskimaradások nem túl valószínű esetben, a Traffic Manager várhatóan szokásos módon működhet tovább. Több Azure-régióban üzembe helyezett alkalmazások támaszkodhat a Traffic Manager az alkalmazás elérhető példányhoz forgalomnak.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Hogyan befolyásolja a Traffic Manager a kiválasztott erőforráscsoport helye?

A TRAFFIC Manager szolgáltatás olyan egyetlen, globális szolgáltatás. Már nem regionális. A kiválasztott erőforráscsoport helye lényegtelen Traffic Manager-profilok az erőforráscsoport üzembe helyezve.

Az Azure Resource Manager összes erőforráscsoportra adjon meg egy helyet, amely megadja, hogy az alapértelmezett helye az erőforráscsoport üzembe helyezett erőforrásokra van szükség. Traffic Manager-profil létrehozásakor létrejön egy erőforráscsoportban. Traffic Manager-profilok használatához **globális** helyükre, mint az erőforrás-csoport alapértelmezés felülbírálása.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Hogyan állapítható meg minden végpont aktuális állapota?

Az aktuális figyelési állapotát végpontot, a teljes profil mellett megjelenik az Azure Portalon. Ez az információ is a forgalmat figyelő keresztül érhető el [REST API-val](https://msdn.microsoft.com/library/azure/mt163667.aspx), [PowerShell-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager), és [platformfüggetlen Azure parancssori felület](../cli-install-nodejs.md).

Az Azure Monitor segítségével nyomon követheti a végpontok állapotát, és tekintse meg a vizuális ábrázolását. Az Azure Monitor használatával kapcsolatos további információkért lásd: a [Azure Monitoring dokumentáció](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>Figyelheti a HTTPS-végpontokat?

Igen. A TRAFFIC Manager támogatja a HTTPS-kapcsolaton keresztül-tesztelés. Konfigurálása **HTTPS** a figyelési konfiguráció protokollként.

A TRAFFIC manager nem tud biztosítani az összes tanúsítvány érvényesítése többek között:

* Nem ellenőrzi a kiszolgálóoldali tanúsítványokat
* SNI kiszolgáló oldali tanúsítványok nem támogatottak.
* Ügyfél-tanúsítványok nem támogatottak.

### <a name="i-stopped-an-azure-cloud-service--web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Egy Azure-felhőszolgáltatásban leállítottam vagy webes alkalmazás végpontot a Traffic Manager-profil, de nem érkezik forgalom tudok újraindítani azt után is. Hogyan javíthatom ezt?

Ha a Azure-beli felhőalapú szolgáltatás, vagy webes alkalmazás végpontjának leállt a Traffic Manager befejezi az állapotát, és újraindítja az állapot-ellenőrzések csak akkor, ha azt észleli, hogy a végpont újraindítása. Ez a késleltetés elkerülése érdekében tiltsa le, és ezután újraengedélyezni, hogy a végpont a Traffic Manager-profilt, a végpont újraindítását követően.   

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Használhatom a Traffic Manager, még akkor is, ha az alkalmazásom nincs HTTP vagy HTTPS támogatása?

Igen. Megadhatja a TCP figyelési protokollként, és a Traffic Manager TCP-kapcsolatot kezdeményez, és várja meg a végpont válaszát. Ha a végpont válaszol az időkorláton belül, a kapcsolat létrehozása a kapcsolódási kérelem választ, hogy a végpont kifogástalan állapotúként jelölésű.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Milyen válaszokat jóvá kell hagynia a végpontot, TCP-figyelés használatakor?

TCP-figyelés használata esetén a Traffic Manager végpont a következő a megadott port külön kérelmet küld egy háromutas TCP kézfogás elindítja. Majd megvárja, amíg egy bizonyos idő (a megadott időtúllépési beállításai) a végpont válaszára. Ha a végpont válaszol SZIN – Nyugtázási választ külön kérésre megadott figyelési beállítások az időkorláton belül, majd, hogy a végpont kifogástalan számít. Ha a szinkronizálás a mi-ACK válasz érkezik, a Traffic Manager biztonsági és a egy ÜZE válaszolva alaphelyzetbe állítja a kapcsolatot.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Milyen gyorsan helyezze át a Traffic Manager a nem megfelelő állapotú felhasználókat?

A TRAFFIC Manager több, amelyek segítségével a következőképpen a Traffic Manager-profil feladatátvételi viselkedését vezérlő beállításokat biztosítja:
- megadhatja, hogy a Traffic Manager-mintavételek a végpontok gyakrabban 10 másodperc, a tesztelés időköz beállításával. Ez biztosítja, hogy bármely végpont nem megfelelő állapotú fog lehető leghamarabb észlelt. 
- megadhat mennyi ideig kell várni, mielőtt egy állapotának ellenőrzése a kérés többször (minimális időtúllépés értéke 5 mp-ben).
- megadhatja, hogy hány sikertelen akkor fordulhat elő, mielőtt a végpont állapotúként van megjelölve. Ez az érték alacsony, mint 0, ahol a végpont eset nem kifogástalannak van jelölve, amint az első állapot-ellenőrzés sikertelen lehet. Azonban a minimális értéke 0 használata a eltűrt hibák száma vezethet folyamatban van a rotációból miatt fordulhat elő,-tesztelés időpontjában átmeneti problémákat végpontok.
- a DNS-választ kell 0 megegyezik az az idő-az-élettartam (TTL) is megadhat. Ezt úgy, hogy a DNS-feloldók nem gyorsítótárazza a választ, és minden egyes új lekérdezés lekérdezi a válasz azt jelenti, hogy a legfrissebb állapotinformációkat, amely a Traffic Manager rendelkezik, amely magában foglalja.

A Traffic Manager ezek a beállítások használatával biztosíthat feladatátvételi teszteket a 10 másodperc után a végpont nem megfelelő állapotú kerül, és egy DNS-lekérdezést jön létre a megfelelő profilt ellen.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Hogyan lehet megadni a profilt különböző végpontok különböző figyelési beállításokat?

A TRAFFIC Manager figyelési beállítások vannak egy profil szint szerint. Ha csak egy végpont használandó figyelési beállítás van szüksége, azt hajtható végre, hogy a végpont, hogy egy [profil beágyazott](traffic-manager-nested-profiles.md) amelynek figyelési beállítások nem azonosak a szülő-profilt.

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Milyen gazdagép fejléc nincs végpont állapot-ellenőrzések használatát?

A TRAFFIC Manager HTTP a protokolu HTTPS állapot-ellenőrzések állomásfejléc használ. A Traffic Manager által használt fejlécét kötelező a végpont target a profilban konfigurált nevét. Az állomásfejlécnek a használt érték nem adható meg külön a cél tulajdonságból.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Mik azok az IP-címek, amelyről az állapot-ellenőrzések származnak?

Kattintson a [Itt](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) megtekintéséhez a JSON-fájlt, amely felsorolja az IP-címek, mely Traffic Manager állapot-ellenőrzések összefoglalót. Tekintse át az IP-címek a JSON-fájlban felsorolt győződjön meg arról, hogy ezek az IP-címekről érkező bejövő kapcsolatok ellenőrizze a megfelelő állapotot, a végpontok engedélyezettek.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Hány állapot-ellenőrzések saját végpontra várhatók a Traffic Managerből?

A Traffic Manager állapot számát ellenőrzi, hogy az endpoint meghiúsulni függ a következők:
- az érték, amely a figyelési időköz beállítása (kisebb időköz azt jelenti, hogy további kérések bármely adott időszakban a végponton üzenetsorokra).
- a helyek, ahol az állapot-ellenőrzések száma (az IP-címeket, ha várhatóan az ellenőrzések szerepel a fenti – gyakori kérdések) származnak.

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Hogyan tudok értesülhet a végpontok egyikét leállása? 
A Traffic Manager által biztosított metrikákat egyik profilban végpontok állapotát. Összes végpont a profil belül összesítést, láthatja a (például a végpontok 75 %-át olyan kifogástalan), vagy jelenleg egy végpont szintenként. A TRAFFIC Manager-metrikák érhetők el az Azure Monitor használatával, és használhatja a [riasztási funkciókat](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) , értesítéseket kaphat, ha a végpont állapotának módosítása. További részletekért lásd: [Traffic Manager-mérőszámok és riasztások](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>A TRAFFIC Manager beágyazott profilok

### <a name="how-do-i-configure-nested-profiles"></a>Hogyan konfigurálhatom a beágyazott profilok?

Beágyazott Traffic Manager-profilok használatával az Azure Resource Manager és a klasszikus Azure REST API-k, az Azure PowerShell-parancsmagok és a platformfüggetlen Azure CLI-parancsokat is konfigurálható. Az új Azure Portalon keresztül akkor is támogatottak.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Hány rétegeket, a beágyazási biztosítja a Traffic Manager támogatja?

Legfeljebb 10 szintnél mélyebb profilok ágyazhatja be. "Hurkokat" használata nem engedélyezett.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Kombinálhatom más végponttípusok az egymásba ágyazott alárendelt-profiljaival, a Traffic Manager-profilt?

Igen. Nem vonatkoznak korlátozások a hogyan kombinálja a profilon belül a különböző típusú végpontok.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Hogyan vonatkozik a számlázási modellt a beágyazott profilok?

Nincs nem negatív, a beágyazott profilok használata díjköteles.

A TRAFFIC Manager díjszabása két részből áll: endpoint állapot-ellenőrzések és több millió DNS-lekérdezések

* Végpont állapot-ellenőrzések: semmilyen díjat nem egy gyermek profil-végpontként egy szülő-profil konfigurálásakor. A gyermek-profilban a végpontok figyelése a szokásos módon történik.
* DNS-lekérdezések: minden egyes lekérdezés csak egyszer tranzakciónak. A szülő-profil csak egy lekérdezést a szülő-profilt, amely adja vissza egy végpontot egy gyermek profil beleszámít.

További részletek: a [díjszabását ismertető lapon a Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Van egy beágyazott profilok gyakorolt hatást?

Nem. Ez nem lesz felszámítva, ha a beágyazott profilokkal teljesítmény hatással.

A Traffic Manager névkiszolgálóit a profil-hierarchia minden DNS-lekérdezés feldolgozása során belső haladnak át. Egy szülő-profilt egy DNS-lekérdezést is az a végpont DNS válasz érkezik egy gyermek profil. Egy CNAME rekordját akár egyetlen profilban vagy beágyazott profilok az szolgál. Hiba esetén nem kell az egyes profilok egy CNAME rekord létrehozása a hierarchiában.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Hogyan számítja ki a Traffic Manager egy szülő-profilhoz a beágyazott végpont állapotát?

A szülő-profil nem állapotellenőrzéseket hajthat végre az alárendelt közvetlenül. Ehelyett az alárendelt profil végpontok állapotát rendszer kiszámítja a gyermek profil általános állapotát. Ezt az információt propagálja felfelé a beágyazott végpont állapotának meghatározásához a beágyazott profilhierarchiában. A szülő-profil az összesített állapota alapján határozza meg, hogy irányítható a forgalom a gyermek.

A következő táblázat ismerteti a Traffic Manager állapot-ellenőrzések egy beágyazott végpont viselkedését.

| Gyermek profil figyelő állapota | Szülőhely szolgáltatásvégpont-figyelő állapota | Megjegyzések |
| --- | --- | --- |
| Letiltott. A gyermek profil le van tiltva. |Leállítva |A szülő végpont állapota leáll, nincs letiltva. A Letiltva állapotot jelzi, hogy le van tiltva a végpont a szülő-profilban van fenntartva. |
| Csökkent a teljesítménye. Legalább egy alárendelt profilvégpontjához csökkentett teljesítményű állapotban van. |Online: az alárendelt profilban Online végpontok száma legalább a minchildendpoints tulajdonság értékét.<BR>CheckingEndpoint: a gyermek-profilban Online plusz CheckingEndpoint végpontok száma legalább a minchildendpoints tulajdonság értékét.<BR>Csökkentett teljesítményű: egyéb. |Forgalom állapot CheckingEndpoint egy végpontja van irányítva. Ha a minchildendpoints tulajdonság értéke túl magas, a végpont mindig csökkent a teljesítménye. |
| Online. Legalább egy alárendelt profilvégpontjához Online állapotban. Nem figyelt olyan végpont csökkentett teljesítményű állapotban van. |Lásd a fenti. | |
| CheckingEndpoints. Legalább egy alárendelt profilvégpontjához "CheckingEndpoint". Nincs végpont sem "Online" vagy "Csökkentett teljesítményű" |Ugyanaz, mint a fenti. | |
| Inaktív. Az összes profil gyermekvégpontok vagy le van tiltva, vagy leállt, vagy ehhez a profilhoz nincsenek végpontok rendelkezik. |Leállítva | |

## <a name="next-steps"></a>Következő lépések:
- További tudnivalók a Traffic Manager [végpont ellenőrzési és automatikus feladatátvételi](../traffic-manager/traffic-manager-monitoring.md).
- További tudnivalók a Traffic Manager [forgalom-útválasztási módszerek](../traffic-manager/traffic-manager-routing-methods.md).
