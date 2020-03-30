---
title: Azure Traffic Manager – gyakori kérdések
description: Ez a cikk választ ad a Traffic Managerrel kapcsolatos gyakori kérdésekre
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: rohink
ms.openlocfilehash: acdac6e3eafc5251ebd31a34bcb9a4db34f0ebbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254364"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Traffic Manager gyakori kérdések (GYIK)

## <a name="traffic-manager-basics"></a>A Traffic Manager alapjai

### <a name="what-ip-address-does-traffic-manager-use"></a>Milyen IP-címet használ a Traffic Manager?

Ahogy azt a [Traffic Manager működése](../traffic-manager/traffic-manager-how-it-works.md)című részben is ismertetett, a Traffic Manager dns-szinten működik. DNS-válaszokat küld a közvetlen ügyfeleknek a megfelelő szolgáltatásvégpontra. Az ügyfelek ezután közvetlenül a szolgáltatásvégponthoz csatlakoznak, nem a Traffic Manageren keresztül.

Ezért a Traffic Manager nem ad meg végpontot vagy IP-címet az ügyfelek számára, hogy csatlakozzanak. Ha statikus IP-címet szeretne a szolgáltatáshoz, azt a szolgáltatásban kell konfigurálni, nem pedig a Traffic Managerben.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Milyen típusú forgalmat lehet irányítani a Traffic Manager használatával?
A [Traffic Manager működésének](../traffic-manager/traffic-manager-how-it-works.md)magyarázata szerint a Traffic Manager-végpont bármely, az Azure-on belül vagy kívül üzemeltetett internet-kiszolgáló lehet. Ezért a Traffic Manager a nyilvános internetről származó forgalmat olyan végpontok halmazába irányíthatja, amelyek szintén az internetfelé néznek. Ha olyan végpontokkal rendelkezik, amelyek egy magánhálózaton belül vannak (például az [Azure Load Balancer](../load-balancer/concepts-limitations.md#internalloadbalancer)belső verziója), vagy a felhasználók ilyen belső hálózatokról DNS-kérelmeket adnak, akkor a Traffic Manager nem irányíthatja ezt a forgalmat.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Támogatja a Traffic Manager a "ragadós" munkameneteket?

Ahogy azt a [Traffic Manager működése](../traffic-manager/traffic-manager-how-it-works.md)című részben is ismertetett, a Traffic Manager dns-szinten működik. Dns-válaszokat használ, hogy az ügyfeleket a megfelelő szolgáltatásvégpontra irányítsa. Az ügyfelek közvetlenül a szolgáltatásvégponthoz csatlakoznak, nem a Traffic Manageren keresztül. Ezért a Traffic Manager nem látja az ügyfél és a kiszolgáló közötti HTTP-forgalmat.

Ezenkívül a Traffic Manager által fogadott DNS-lekérdezés forrás IP-címe a rekurzív DNS-szolgáltatáshoz tartozik, nem pedig az ügyfélhez. Ezért a Traffic Manager nem tudja nyomon követni az egyes ügyfeleket, és nem tudja megvalósítani a "ragadós" munkameneteket. Ez a korlátozás minden DNS-alapú forgalomkezelő rendszerre vonatkozik, és nem csak a Traffic Managerre vonatkozik.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Miért jelenik meg HTTP-hiba a Traffic Manager használatakor?

Ahogy azt a [Traffic Manager működése](../traffic-manager/traffic-manager-how-it-works.md)című részben is ismertetett, a Traffic Manager dns-szinten működik. Dns-válaszokat használ, hogy az ügyfeleket a megfelelő szolgáltatásvégpontra irányítsa. Az ügyfelek ezután közvetlenül a szolgáltatásvégponthoz csatlakoznak, nem a Traffic Manageren keresztül. A Traffic Manager nem látja az ügyfél és a kiszolgáló közötti HTTP-forgalmat. Ezért minden HTTP-hiba látható kell származnia az alkalmazásból. Ahhoz, hogy az ügyfél csatlakozzon az alkalmazáshoz, az összes DNS-feloldási lépés befejeződött. Ez magában foglalja a Traffic Manager által az alkalmazás forgalmi folyamaton végzett interakciót.

A további vizsgálatnak ezért a kérelemre kell összpontosítania.

Az ügyfél böngészőjéből küldött HTTP-állomásfejléc a leggyakoribb problémaforrás. Győződjön meg arról, hogy az alkalmazás úgy van beállítva, hogy elfogadja a megfelelő állomásfejlécet a használt tartománynévhez. Az Azure App Service-t használó végpontok esetében olvassa el egy [webalkalmazás egyéni tartománynevének konfigurálása az Azure App Service-ben a Traffic Manager használatával](../app-service/configure-domain-traffic-manager.md)című témakört.

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Milyen hatással van a Traffic Manager használatára?

Ahogy azt a [Traffic Manager működése](../traffic-manager/traffic-manager-how-it-works.md)című részben is ismertetett, a Traffic Manager dns-szinten működik. Mivel az ügyfelek közvetlenül csatlakoznak a szolgáltatásvégpontokhoz, a Traffic Manager használata kor a kapcsolat létrehozása után nincs teljesítménybeli hatás.

Mivel a Traffic Manager dns-szintű alkalmazásokkal integrálódik, további DNS-keresési szükséges a DNS-feloldási láncba való beszúrásához. A Traffic Manager hatása a DNS-feloldási időre minimális. A Traffic Manager névkiszolgálók globális hálózatát használja, és [az anycast](https://en.wikipedia.org/wiki/Anycast) hálózatot használja annak biztosítására, hogy a DNS-lekérdezések mindig a legközelebbi elérhető névkiszolgálóhoz legyenek irányítva. Ezenkívül a DNS-válaszok gyorsítótárazása azt jelenti, hogy a Traffic Manager használata során felmerülő további DNS-késés csak a munkamenetek töredékére vonatkozik.

A Teljesítmény metódus a forgalmat a legközelebbi elérhető végponthoz irányítja. A nettó eredmény az, hogy az ezzel a módszerrel kapcsolatos általános teljesítményhatásnak minimálisnak kell lennie. A DNS-késés növekedését a végpontalacsonyabb hálózati késésével kell ellensúlyozni.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Milyen alkalmazásprotokollokat használhatok a Traffic Managerrel?

Ahogy azt a [Traffic Manager működése](../traffic-manager/traffic-manager-how-it-works.md)című részben is ismertetett, a Traffic Manager dns-szinten működik. Miután a DNS-keresése befejeződött, az ügyfelek közvetlenül az alkalmazás végpontjához csatlakoznak, nem a Traffic Manageren keresztül. Ezért a kapcsolat bármilyen alkalmazásprotokollt használhat. Ha a TCP protokollt választja figyelési protokollként, a Traffic Manager végpontállapot-figyelése alkalmazásprotokollok használata nélkül is elvégezhető. Ha úgy dönt, hogy az állapot ellenőrzése egy alkalmazás protokoll használatával, a végpont képesnek kell lennie arra, hogy válaszoljon a HTTP vagy HTTPS GET kéréseket.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Használhatom a Traffic Managert "meztelen" tartománynévvel?

Igen. Ha meg szeretné tudni, hogyan hozhat létre aliasrekordot a tartománynév csúcspontjához, hogy hivatkozzon egy Azure Traffic Manager-profilra, olvassa el az [Aliasrekord konfigurálása csúcstartománynevek támogatására a Traffic Manager segítségével című témakört.](../dns/tutorial-alias-tm.md)

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>A Traffic Manager figyelembe veszi az ügyfél alhálózati címet a DNS-lekérdezések kezelésekor? 

Igen, a kapott DNS-lekérdezés forrás IP-címe mellett (amely általában a DNS-feloldó IP-címe), a Földrajzi, Teljesítmény és Alhálózati útválasztási módszerek keresésekor a forgalomkezelő figyelembe veszi az ügyfél alhálózati címét is, ha a kérést a végfelhasználó nevében küldő feloldó tartalmazza a lekérdezésben.  
Pontosabban, [RFC 7871 – Ügyfél-alhálózat a DNS-lekérdezésekben,](https://tools.ietf.org/html/rfc7871) amely egy [dns-bővítménymechanizmust (EDNS0)](https://tools.ietf.org/html/rfc2671) biztosít, amely az azt támogató feloldóktól továbbadhatja az ügyfél alhálózati címét.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Mi az a DNS TTL, és milyen hatással van a felhasználókra?

Amikor egy DNS-lekérdezés a Traffic Managerre ér, beállítja az élő adásidő (TTL) nevű válasz értékét. Ez az érték, amelynek egysége másodpercben van, azt jelzi a DNS-feloldóknak, hogy mennyi ideig kell gyorsítótárazni ezt a választ. Bár a DNS-feloldók nem garantáltan gyorsítótárazják ezt az eredményt, a gyorsítótárazás lehetővé teszi számukra, hogy a Traffic Manager DNS-kiszolgálói helyett válaszoljanak a gyorsítótáron kívüli későbbi lekérdezésekre. Ez a következő hatásokkal jár:

- a magasabb Élettartam csökkenti a Traffic Manager DNS-kiszolgálóin megjelenő lekérdezések számát, ami csökkentheti az ügyfél költségeit, mivel a kiszolgált lekérdezések száma számlázható használat.
- a magasabb TTL potenciálisan csökkentheti a DNS-keresések hez szükséges időt.
- a magasabb TTL azt is jelenti, hogy az adatok nem tükrözik a traffic manager által a szondázási ügynökökön keresztül kapott legfrissebb állapotinformációkat.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Milyen magasra vagy alacsonyra állíthatom be a Traffic Manager-válaszok TTL-jét?

Beállíthatja, hogy a DNS TTL profilonként akár 0 másodperc legyen, és akár 2 147 483 647 másodperc (az [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )szabványnak megfelelő maximális tartomány). A 0-s TTL azt jelenti, hogy az alsóbb rétegbeli DNS-feloldók nem gyorsítótárazják a lekérdezési válaszokat, és minden lekérdezésvárhatóan a Traffic Manager DNS-kiszolgálóit keresi a feloldáshoz.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Hogyan tudom megérteni a profilomra érkező lekérdezések mennyiségét? 

A Traffic Manager által biztosított mérőszámok egyike a profil által megválaszolt lekérdezések száma. Ezeket az információkat profilszintű összesítéskor kaphatja le, vagy tovább feloszthatja, hogy láthassa a lekérdezések mennyiségét, ahol adott végpontokat adtak vissza. Ezenkívül riasztásokat állíthat be, amelyek értesítik, ha a lekérdezésválasz kötete átlépi a beállított feltételeket. További részletekért a [Traffic Manager mérőszámai és riasztásai.](traffic-manager-metrics-alerts.md)

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager Földrajzi forgalomútválasztási módszer

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Milyen használati esetekben hasznos a földrajzi útválasztás?

Földrajzi útválasztási típus használható minden olyan forgatókönyvben, ahol az Azure-ügyfél nek meg kell különböztetni a felhasználók földrajzi régiók alapján. A Földrajzi forgalom útválasztási módszerével például más felhasználói élményt adhat az adott régiókból származó felhasználóknak, mint más régiókból származók. Egy másik példa a helyi adatszuverenitási megbízások betartása, amelyek megkövetelik, hogy egy adott régió felhasználói csak az adott régió végpontjai által szolgálják ki.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Hogyan dönthetem el, hogy a Teljesítmény-útválasztási módszert vagy a Földrajzi útválasztási módszert használjam?How I decide if i should use Performance routing method or Geographic routing method?

A két népszerű útválasztási módszer közötti legfontosabb különbség az, hogy a Teljesítmény-útválasztási metódusban az elsődleges cél a forgalom küldése a végpontra, amely a legalacsonyabb késleltetést biztosítja a hívó számára, míg a földrajzi útválasztásban az elsődleges cél egy földrajzi a hívók számára, így szándékosan irányíthatja őket egy adott végpontra. Az átfedés történik, mivel van összefüggés a földrajzi közelség és az alacsonyabb késés, bár ez nem mindig igaz. Lehet, hogy egy végpont egy másik földrajzi, amely jobb késési élményt biztosít a hívó, és ebben az esetben a teljesítmény útválasztás elküldi a felhasználót, hogy a végpont, de a földrajzi útválasztás mindig elküldi őket a végpont leképezve a földrajzi régióban. További egyértelművé tenni, fontolja meg a következő példát - a földrajzi útválasztás hozhat nem gyakori leképezések, például az összes forgalmat Ázsiából a végpontok az Egyesült Államokban és az összes amerikai forgalmat végpontok Ázsiában. Ebben az esetben a földrajzi útválasztás szándékosan pontosan azt teszi, amire beállította, és a teljesítményoptimalizálás nem szempont. 
>[!NOTE]
>Előfordulhatnak olyan esetek, ahol szükség lehet mind a teljesítmény és a földrajzi útválasztási képességek, ezek a forgatókönyvek beágyazott profilok lehet jó választás. Például beállíthat egy földrajzi útválasztást tartalmazó szülőprofilt, ahol az Észak-Amerikából származó összes forgalmat egy egyesült államokbeli végpontokkal rendelkező beágyazott profilba küldi, és teljesítmény-útválasztással küldi el ezeket a forgalmat a legjobb végpontra az adott készleten belül. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Melyek azok a régiók, amelyeket a Traffic Manager földrajzi útválasztáshoz támogat?

A Traffic Manager által használt ország-/régióhierarchia [itt](traffic-manager-geographic-regions.md)található. Bár ez az oldal naprakész marad a módosításokkal, programozott módon is lekérheti ugyanazokat az információkat az [Azure Traffic Manager REST API használatával.](https://docs.microsoft.com/rest/api/trafficmanager/) 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Hogyan határozza meg a forgalomkezelő, hogy a felhasználó honnan kérdez?

A Traffic Manager megvizsgálja a lekérdezés forrás IP-címét (ez valószínűleg egy helyi DNS-feloldó, aki a felhasználó nevében végzi a lekérdezést), és egy belső IP-cím-régióleképezést használ a hely meghatározásához. Ez a térkép folyamatosan frissül, hogy figyelembe vegye az internet változásait. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Garantált-e, hogy a Traffic Manager minden esetben pontosan meg tudja határozni a felhasználó pontos földrajzi helyét?

Nem, a Traffic Manager nem tudja garantálni, hogy a DNS-lekérdezés forrás IP-címéből kikövetkeztetünk földrajzi régió a következő okok miatt mindig megfelel a felhasználó helyének:

- Először is, amint azt az előző GYIK, a forrás IP-cím látjuk, hogy a DNS-feloldó csinál a keresési nevében a felhasználó. Bár a DNS-feloldó földrajzi helye jó proxy a felhasználó földrajzi helyéhez, a DNS-feloldó szolgáltatás és az ügyfél által választott DNS-feloldó szolgáltatás lábnyomától függően is eltérő lehet. Például egy Malajziában található ügyfél megadhatja az eszköz beállításaiban egy DNS-feloldó szolgáltatást, amelynek szingapúri DNS-kiszolgálója lehet, hogy kiválasztják az adott felhasználó/eszköz lekérdezési feloldási kezelésével. Ebben az esetben a Traffic Manager csak a szingapúri helynek megfelelő feloldó IP-címét láthatja. Lásd még a lap ügyfélalhálózati címének támogatásával kapcsolatos korábbi gyakori kérdéseket.

- Másodszor, a Traffic Manager egy belső térképet használ az IP-cím földrajzi régió fordításához. Bár ezt a térképet folyamatosan érvényesítik és frissítik, hogy növelje pontosságát és figyelembe vegye az internet változó jellegét, még mindig fennáll annak a lehetősége, hogy információink nem pontosan ábrázolják az összes IP földrajzi helyét Címek.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Egy végpont fizikailag ugyanabban a régióban kell lennie, mint amelyhez földrajzi útválasztáshoz van konfigurálva?

Nem, a végpont helye nem vezet be korlátozásokat arra vonatkozóan, hogy mely régiók képezhetők le hozzá. Például egy végpont az USA-közép-Azure-régióban lehet az összes indiai felhasználók irányulrá.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Rendelhetek földrajzi régiókat olyan profil végpontjaihoz, amelyek nincsenek földrajzi útválasztásra konfigurálva?

Igen, ha egy profil útválasztási módja nem földrajzi, az [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) használatával földrajzi régiókat rendelhet az adott profil végpontjaihoz. Nem földrajzi útválasztási típusprofilok esetén ezt a konfigurációt a rendszer figyelmen kívül hagyja. Ha egy ilyen profilt később földrajzi útválasztási típusra módosít, a Traffic Manager használhatja ezeket a hozzárendeléseket.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Miért jelenik meg hibaüzenet, amikor egy meglévő profil útválasztási módját földrajzira próbálom módosítani?

A földrajzi útválasztással rendelkező profil összes végpontjának legalább egy régiót hozzá kell rendelve. Egy meglévő profil átalakítása földrajzi útválasztási típus, először is hozzá kell rendelnie a földrajzi régiók at all its endpoints az [Azure Traffic Manager REST API-t,](https://docs.microsoft.com/rest/api/trafficmanager/) mielőtt az útválasztási típus földrajzi. Portál használata esetén először törölje a végpontokat, módosítsa a profil útválasztási módszerét földrajzira, majd adja hozzá a végpontokat a földrajzi régióleképezésükkel együtt.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Miért ajánlott, hogy az ügyfelek egymásba ágyazott profilokat hozzanak létre a végpontok helyett egy olyan profil alatt, amelynek földrajzi útválasztása engedélyezve van?

Egy régió csak egy végponthoz rendelhető egy profilon belül, ha a földrajzi útválasztási módszert használja. Ha ez a végpont nem egy beágyazott típus, amelyhez gyermekprofil van csatolva, ha a végpont nem megfelelő állapotú, a Traffic Manager továbbra is forgalmat küld hozzá, mivel az alternatív a forgalom küldésének nem jobb. A Traffic Manager nem feladatátvétel tegy másik végpontra, még akkor sem, ha a hozzárendelt régió a végponthoz rendelt "szülője", amely nem megfelelő állapotú (például ha egy spanyolországi régióval rendelkező végpont nem kifogástalan állapotú, akkor nem történik meg a feladatátvétel egy másik végpontra, amely európa által kijelölt régióval). Ez annak biztosítására történik, hogy a Traffic Manager tiszteletben tartsa az ügyfél profiljában beállított földrajzi határokat. Ahhoz, hogy egy végpont nem megfelelő állapotú ként egy végpontra kerüljön az előnye, hogy földrajzi régiókat kell hozzárendelni egymásba ágyazott, több végpontot tartalmazó profilokhoz az egyes végpontok helyett. Ily módon, ha a beágyazott gyermekprofil egy végpontja meghibásodik, a forgalom feladatátvételt végezhet ugyanazon beágyazott gyermekprofilon belül egy másik végpontra.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Vannak-e olyan korlátozások az API-verzió, amely támogatja ezt az útválasztási típust?

Igen, csak a 2017-03-01-es API-verzió és az újabb támogatja a Földrajzi útválasztási típust. Régebbi API-verziók nem használhatók földrajzi útválasztási típusú profilok létrehozásához vagy földrajzi régiók végpontokhoz rendeléséhez. Ha egy régebbi API-verzió használatával lekéri a profilokat egy Azure-előfizetésből, a földrajzi útválasztási típusú profil nem kerül vissza. Emellett a régebbi API-verziók használatakor minden olyan profil, amely nek földrajzi régió-hozzárendeléssel rendelkező végpontja van, nem jelenik meg a földrajzi régió-hozzárendelése.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Traffic Manager-segédhálózati forgalom útválasztási módja

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Milyen használati esetekben hasznos az alhálózati útválasztás?

Az alhálózati útválasztás lehetővé teszi a DNS-kérelmek IP-címének forrás IP-címe által azonosított felhasználók adott csoportjainak szolgáltatását. Egy példa lehet mutató különböző tartalom, ha a felhasználók csatlakoznak a honlapon a vállalati HQ. Egy másik az lenne, hogy bizonyos internetszolgáltatók felhasználói csak olyan végpontokhoz férnének hozzá, amelyek csak az IPv4-kapcsolatokat támogatják, ha ezek az internetszolgáltatók az IPv6 használatakor par alatti teljesítménnyel rendelkeznek.
Az alhálózati útválasztási módszer használatának másik oka a beágyazott hossz-szelvények más profiljaival együtt. Ha például földrajzi útválasztási módszert szeretne használni a felhasználók földrajzi kerítéséhez, de egy adott internetszolgáltató esetében egy másik útválasztási módszert szeretne, szülőprofilként használhat egy alhálózati útválasztási módszert, és felülbírálhatja az internetszolgáltatót egy adott gyermek használatához. profilt, és a standard Földrajzi profil mindenki más számára.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Honnan tudja a Traffic Manager a végfelhasználó IP-címét?

A végfelhasználói eszközök általában DNS-feloldót használnak a DNS-keresések hez a nevükben. Az ilyen feloldók kimenő IP-címe az, amit a Traffic Manager a forrás IP-címnek tekint. Ezenkívül az alhálózati útválasztási módszer azt is megvizsgálja, hogy van-e EDNS0 extended client alhálózati (ECS) információ, amelyet a kéréssel együtt továbbítottak. Ha az ECS-információ jelen van, akkor ez a cím határozza meg az útválasztást. EcS-információ hiányában a lekérdezés forrás IP-címe útválasztási célokra szolgál.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Hogyan adhatom meg az IP-címeket az alhálózati útválasztás használatakor?

A végponthoz társítani hozandó IP-címek kétféleképpen adhatók meg. Először is használhatja a négy pontozott tizedes oktett jelöléssel a kezdő és záró címeket, hogy adja meg a tartomány (például 1.2.3.4-5.6.7.8 vagy 3.4.5.6-3.4.5.6). Másodszor, a CIDR jelölés segítségével megadhatja a tartományt (például 1.2.3.0/24). Több tartományt is megadhat, és mindkét jelölési típust használhatja egy tartománykészletben. Néhány korlátozás érvényes.

-    A címtartományok átfedése nem lehet, mivel minden IP-címet csak egyetlen végponthoz kell leképezni.
-    A kezdőcím nem lehet több, mint a zárócím.
-    A CIDR jelölés esetén a "/" előtti IP-címnek kell lennie a tartomány kezdőcímének (például az 1.2.3.0/24 érvényes, de az 1.2.3.4.4/24 nem érvényes)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Hogyan adhatok meg tartalékvégpontot az alhálózati útválasztás használatakor?

Az alhálózati útválasztással rendelkező profilban, ha olyan végpontja van, amelyhez nincs enek képezve, minden olyan kérés, amely nem egyezik meg más végpontokkal, ide lesz irányítva. Erősen ajánlott, hogy van egy ilyen tartalék végpont a profilban, mivel a Traffic Manager ad vissza egy NXDOMAIN választ, ha egy kérelem érkezik, és nincs leképezve egyetlen végpontra, vagy ha le van képezve egy végpontra, de ez a végpont nem kifogástalan.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Mi történik, ha egy végpont le van tiltva egy alhálózati útválasztási típusú profilban?

Az alhálózati útválasztással rendelkező profilban, ha le van tiltva egy végpont, a Traffic Manager úgy fog eltekinteni, mintha az adott végpont és az általa birtokolt alhálózati hozzárendelések nem léteznének. Ha egy lekérdezés, amely volna egyeztetni az IP-cím leképezése érkezik, és a végpont le van tiltva, a Traffic Manager visszaad egy tartalék végpont (egy leképezés nélkül), vagy ha egy ilyen végpont nincs jelen, visszaad egy NXDOMAIN választ.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Traffic Manager Többértékű forgalomútválasztási módszer

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Milyen használati esetekben hasznos a többértékű útválasztás?

A többértékű útválasztás több kifogástalan végpontot ad vissza egyetlen lekérdezési válaszban. Ennek fő előnye, hogy ha egy végpont nem kifogástalan, az ügyfél több lehetőséget, hogy újra anélkül, hogy egy másik DNS-hívás (amely visszaadja ugyanazt az értéket egy upstream cache). Ez olyan rendelkezésre állásérzékeny alkalmazásokra vonatkozik, amelyek minimálisra szeretnék csökkenteni az állásidőt.
A MultiValue útválasztási módszer másik használata, ha egy végpont "kettős homed" mind az IPv4- és Az IPv6-címekhez, és mindkét lehetőséget meg szeretné adni a hívónak, amikor kapcsolatot kezdeményez a végponttal.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Hány végpontot ad vissza a rendszer a többértékű útválasztás használatközben?

Megadhatja a visszaadandó végpontok maximális számát, és a MultiValue csak annyi kifogástalan végpontot ad vissza, ha egy lekérdezés érkezik. A konfiguráció maximális lehetséges értéke 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Ugyanazt a végpontkészletet kapom, ha többszintű útválasztást használ?

Nem tudjuk garantálni, hogy minden lekérdezésben ugyanazokat a végpontokat adja vissza. Ezt az a tény is befolyásolja, hogy egyes végpontok nem lesznek egészségtelenek, amikor is nem kerülnek be a válaszba.

## <a name="real-user-measurements"></a>Valós felhasználói mérések

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Milyen előnyökkel jár a valós felhasználói mérések használata?

Ha teljesítmény-útválasztási módszert használ, a Traffic Manager a legjobb Azure-régiót választja a végfelhasználó számára a forrás IP- és EDNS-ügyfél-alhálózat vizsgálatával (ha átadják), és ellenőrzi azt a szolgáltatás által karbantartott hálózati késési intelligenciával. A valós felhasználói mérések ezt növelik a végfelhasználói bázis számára azáltal, hogy tapasztalataik hozzájárulnak ehhez a késési táblához, valamint biztosítják, hogy ez a táblázat megfelelően lefedje azokat a végfelhasználói hálózatokat, amelyekből a végfelhasználók az Azure-hoz csatlakoznak. Ez nagyobb pontosságot eredményez a végfelhasználó útválasztásában.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Használhatom a valós felhasználói méréseket nem Azure-régiókkal?

A valós felhasználói mérések csak az Azure-régiók eléréséhez használt késésről mérik és jelentik a jelentéseket. Ha nem Azure-régiókban üzemeltetett végpontokkal használ teljesítményalapú útválasztást, továbbra is élvezheti ezt a funkciót azáltal, hogy megnöveli a késési információkat a végponthoz társított reprezentatív Azure-régióról.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Melyik útválasztási módszer élvezi a valós felhasználói mérések előnyeit?

A valós felhasználói mérések által szerzett további információk csak a teljesítmény-útválasztási módszert használó profilokra vonatkoznak. A Valós felhasználói mérések hivatkozás az összes profilból elérhető, amikor az Azure Portalon keresztül tekinti meg.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Külön-külön kell engedélyeznem a valós felhasználói méréseket az egyes profilokban?

Nem, előfizetésenként csak egyszer kell engedélyeznie, és az összes mért és jelentett késési információ minden profil számára elérhető.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Hogyan kapcsolhatom ki a Valódi felhasználói méréseket az előfizetésemhez?

Leállíthatja a valós felhasználói mérésekhez kapcsolódó díjak felhalmozását, ha leállítja a késéses mérések gyűjtését és visszaküldését az ügyfélalkalmazásból. Ha például a javascriptet weblapokba ágyazva méri, a JavaScript eltávolításával vagy a hívás meghívásának kikapcsolásával leállíthatja ezt a funkciót.

A valódi felhasználói méréseket a kulcs törlésével is kikapcsolhatja. Miután törölte a kulcsot, a Traffic Managernek ezzel a kulccsal küldött mérések elvesznek.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Használhatom a Valós felhasználói méréseket a weboldalaktól eltérő ügyfélalkalmazásokkal?

Igen, a valós felhasználói mérések célja a különböző típusú végfelhasználói ügyfeleken keresztül gyűjtött adatok betöltése. Ez a gyIK az új típusú ügyfélalkalmazások támogatásával frissül.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Hány mérés történik minden alkalommal, amikor a Valós felhasználói mérések et engedélyezve weboldalam megjelenik?

Ha valós felhasználói méréseket használ a megadott JavaScript méréssel, minden oldalrenderelés hat mérést eredményez. Ezeket ezután jelentik vissza a Traffic Manager szolgáltatásnak. A Szolgáltatásért a Traffic Manager szolgáltatásnak jelentett mérések száma alapján számítunk fel díjat. Ha például a felhasználó a mérések során, de a jelentés előtt elnavigál a weboldalról, akkor ezek a mérések számlázási célokra nem lesznek figyelembe véve.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Késleltetés van, mielőtt a Valós felhasználói mérések parancsfájl elindul na a weboldalamon?

Nem, nincs programozott késleltetés a parancsfájl meghívása előtt.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Használhatom a valós felhasználói méréseket csak azokat az Azure-régiókat, amelyeket mérni szeretnék?

Nem, minden alkalommal, amikor meghívja, a valós felhasználói mérések parancsfájl a szolgáltatás által meghatározott hat Azure-régiókészletét méri. Ez a készlet változik a különböző meghívások között, és ha nagyszámú ilyen meghívás történik, a mérési lefedettség különböző Azure-régiókra terjed ki.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Korlátozhatom a mérések számát egy adott számra?

A mérésI JavaScript be van ágyazva a weboldalba, és ön teljes mértékben szabályozhatja, hogy mikor kezdje el és hagyja abba a használatát. Mindaddig, amíg a Traffic Manager szolgáltatás kap egy kérelmet az Azure-régiók mérhető listájának, a régiók egy sor ad vissza.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Megnézhetem az ügyfélalkalmazás által a valós felhasználói mérések részeként végzett méréseket?

Mivel a mérési logika az ügyfélalkalmazásból fut, teljes mértékben szabályozhatja, hogy mi történik, beleértve a késésmérések megtekintését is. A Traffic Manager nem jelenti az előfizetéshez kapcsolódó kulcs alatt kapott mérések összesített nézetét.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Módosíthatom a Traffic Manager által biztosított mérési parancsfájlt?

Miközben ön irányítja a weboldalba ágyazott beállításokat, erősen ellenezzük, hogy bármilyen módosítást hajtson végre a mérési parancsfájlon annak érdekében, hogy megfelelően mérje és jelentse a késéseket.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Lehetséges lesz-e, hogy mások is lássák a kulcsot, amelyet a valós felhasználói mérésekhez használok?

Amikor beágyazza a mérési szkriptet egy weboldalra, mások is láthatják a parancsfájlt és a Valós felhasználói mérések (RUM) kulcsot. Fontos azonban tudni, hogy ez a kulcs eltér az előfizetés-azonosítójától, és a Traffic Manager által generált csak erre a célra használható. A RUM-kulcs ismerete nem veszélyezteti az Azure-fiók biztonságát.

### <a name="can-others-abuse-my-rum-key"></a>Mások is visszaélhetnek a RUM-kulcsommal?

Bár lehetséges, hogy mások a kulcs használatával rossz adatokat küldjenek az Azure-ba, néhány rossz mérés nem változtatja meg az útválasztást, mivel figyelembe veszik az összes többi méréssel együtt. Ha módosítania kell a kulcsokat, újra létrehozhatja azt a kulcsot, amikor is a régi kulcs elvetésre kerül.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>A mérési JavaScriptet minden weboldalamra be kell helyeznem?

A valós felhasználói mérések a mérések számának növekedésével több értéket biztosítanak. Miután azt mondta, hogy ez a döntés, hogy meg kell tenni, hogy az összes weboldalakat, vagy válasszuk néhány. Azt javasoljuk, hogy először is, hogy azt a leglátogatottabb oldalon, ahol a felhasználó várhatóan marad az oldalon öt másodperc vagy több.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>A Forgalomkezelő azonosíthatja a végfelhasználókkal kapcsolatos információkat, ha valós felhasználói méréseket használok?

A megadott mérési JavaScript használataesetén a Traffic Manager láthatja a végfelhasználó ügyfél IP-címét és az általuk használt helyi DNS-feloldó forrás IP-címét. A Traffic Manager csak az ügyfél IP-címét használja, miután csonkolást végzett, hogy ne tudja azonosítani a méréseket küldő konkrét végfelhasználót.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>A valós felhasználói méréseket mérő weblapnak a Traffic Managert kell használnia az útválasztáshoz?

Nem, nem kell használnia a Traffic Managert. A Traffic Manager útválasztási oldala a Valós felhasználó mérése résztől elkülönítve működik, és bár nagyszerű ötlet, hogy mindkettő ugyanabban a webes tulajdonban legyen, nem kell.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Kell-e olyan szolgáltatást üzemeltetnem az Azure-régiókban, amelyet a valós felhasználói mérésekhez használhatok?

Nem, nem kell üzemeltetnie semmilyen kiszolgálóoldali összetevőt az Azure-ban a valós felhasználói mérések működéséhez. A JavaScript mérésáltal letöltött egyképpontos képet és a különböző Azure-régiókban futtató szolgáltatást az Azure üzemelteti és kezeli. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Növekedni fog az Azure sávszélesség-használata a valós felhasználói mérések használatakor?

Az előző válaszban említettek szerint a valós felhasználói mérések kiszolgálóoldali összetevői az Azure tulajdonában és kezelésében találhatók. Ez azt jelenti, hogy az Azure-sávszélesség-használat nem fog növekedni, mert a valós felhasználói mérések használata. Ez nem tartalmazza az Azure által felszámított díjakon kívüli sávszélesség-használatot. Minimálisra csökkentjük a használt sávszélességet, ha csak egy képpontképet tölt ünk le a késés egy Azure-régióba történő méréséhez. 

## <a name="traffic-view"></a>Forgalomnézet

### <a name="what-does-traffic-view-do"></a>Mire történik a Traffic View?

A Traffic View a Traffic Manager szolgáltatása, amely segít jobban megérteni a felhasználókat és a felhasználói élményüket. A Traffic Manager által fogadott lekérdezéseket és a szolgáltatás által karbantartott hálózati késleltetési intelligencia táblákat használja a következők biztosításához:

- Azok a régiók, ahonnan a felhasználók az Azure-beli végpontokhoz csatlakoznak.
- Az ezekből a régiókból csatlakozó felhasználók száma.
- Az Azure-régiók, amelyekhez azok a beirányítva.
- A késés itapasztalat, hogy ezek az Azure-régiókban.

Ez az információ elérhető a portálon található földrajzi térképfedvényés táblázatos nézeteken keresztül történő felhasználáshoz, amellett, hogy nyers adatként letölthető.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Hogyan profitálhatok a Traffic View használatából?

A Traffic View a Traffic Manager-profilok által kapott forgalom átfogó nézetét tartalmazza. Különösen azt lehet használni, hogy megértsék, ahol a felhasználói bázis csatlakozik, és ugyanilyen fontos, hogy mi az átlagos késés tapasztalat. Ezeket az információkat használhatja, hogy megtalálja azokat a területeket, amelyekre összpontosítania kell, például az Azure-lábnyom egy olyan régióra való kiterjesztésével, amely alacsonyabb késésű felhasználókat képes kiszolgálni. Egy másik betekintést lehet származtatni a Traffic View, hogy a minták a forgalom a különböző régiókban, ami viszont segíthet döntéseket növekvő vagy csökkenő feltalálni ezekben a régiókban.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Miben különbözik a Traffic View a Traffic Manager metrikáitól az Azure-figyelőn keresztül?

Az Azure Monitor használható a profil és a végpontok által fogadott forgalom összesített szintű megértéséhez. Azt is lehetővé teszi, hogy nyomon kövesse a végpontok állapotát az állapot-ellenőrzési eredmények felfedésével. Ha ezeken túl kell lépnie, és meg kell értenie, hogy a végfelhasználó milyen élményben van regionális szinten az Azure-hoz való csatlakozás, a Traffic View használható ennek eléréséhez.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>A Traffic View használja az EDNS-ügyfél alhálózati adatait?

Az Azure Traffic Manager által kiszolgált DNS-lekérdezések az útválasztás pontosságának növelése érdekében figyelembe veszik az ECS-adatokat. De amikor létrehozza az adatkészletet, amely megmutatja, hogy a felhasználók honnan csatlakoznak, a Traffic View csak a DNS-feloldó IP-címét használja.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Hány nap adatot használ a Traffic View?

A Traffic View úgy hozza létre a kimenetét, hogy feldolgozza az adatokat az azt megelőző hét napból, amikor ön megtekinti azadatokat. Ez egy mozgó ablak, és a legfrissebb adatokat fogja használni minden alkalommal, amikor meglátogatja.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Hogyan kezeli a Traffic View a külső végpontokat?

Ha az Azure-régiókon kívül üzemeltetett külső végpontokat használ egy Traffic Manager-profilban, beállíthatja azt egy Azure-régióhoz, amely a késési jellemzőinek proxyja (ez valójában szükséges, ha teljesítmény-útválasztási módszert használ). Ha rendelkezik ezzel az Azure-régió leképezés, hogy az Azure-régió késési metrikák lesznek használva a Traffic View kimenet létrehozásakor. Ha nincs megadva Azure-régió, a késési adatok üresek lesznek a külső végpontok adataiban.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Engedélyeznem kell a Traffic View nézetet az előfizetésem minden egyes profiljához?

Az előzetes verzió ban a Traffic View előfizetési szinten engedélyezve volt. Az általános elérhetőség előtt végrehajtott fejlesztések részeként most már engedélyezheti a Forgalomnézetet egy profil szinten, lehetővé téve, hogy részletesebb encikbe tegye ezt a funkciót. Alapértelmezés szerint a Traffic View le lesz tiltva egy profilhoz.

>[!NOTE]
>Ha az előzetes verzió ban engedélyezte a Traffic View-t egy előfizetési szinten, most újra engedélyeznie kell azt az adott előfizetés alatti minden egyes profilhoz.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Hogyan tudom kikapcsolni a Forgalomnézetet?

A Portál vagy a REST API használatával bármely profilhoz kikapcsolhatja a Traffic View nézetet. 

### <a name="how-does-traffic-view-billing-work"></a>Hogyan működik a Traffic View számlázása?

A Traffic View díjszabása a kimenet létrehozásához használt adatpontok számán alapul. Jelenleg az egyetlen támogatott adattípus a profil által fogadott lekérdezések. Ezenkívül csak akkor kell fizetnie a feldolgozásért, amely akkor történt, ha engedélyezve van a Forgalomnézet. Ez azt jelenti, hogy ha egy hónap bizonyos időszakában engedélyezi a Forgalomnézet et, és máskor kikapcsolja, csak a szolgáltatás engedélyezése közben feldolgozott adatpontok számítanak bele a számlába.

## <a name="traffic-manager-endpoints"></a>Traffic Manager-végpontok

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Használhatom a Traffic Managert több előfizetésvégpontjával?

Több előfizetésből származó végpontok használata nem lehetséges az Azure Web Apps alkalmazásokkal. Az Azure Web Apps megköveteli, hogy a webalkalmazásokhoz használt egyéni tartományneveket csak egyetlen előfizetésen belül használja. Nem lehet több előfizetésből származó webalkalmazásokat használni ugyanazzal a tartománynévvel.

Más végponttípusok esetében a Traffic Manager több előfizetésből származó végpontokkal is használható. Az Erőforrás-kezelőben bármely előfizetésvégpontja hozzáadható a Traffic Managerhez, feltéve, hogy a Traffic Manager-profilt konfiguráló személy olvasási hozzáféréssel rendelkezik a végponthoz. Ezek az engedélyek az [Azure Resource Manager szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/role-assignments-portal.md)használatával adhatók meg.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Használhatom a Traffic Managert a Cloud Service "Staging" bővítőhelyekkel?

Igen. A Felhőszolgáltatás "átmeneti" bővítőhelyei külső végpontként konfigurálhatók a Traffic Managerben. Állapot-ellenőrzések továbbra is az Azure-végpontok díja továbbra is.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Támogatja a Traffic Manager az IPv6-végpontokat?

A Traffic Manager jelenleg nem biztosít IPv6-címzésre képes névkiszolgálókat. A Traffic Managert azonban továbbra is használhatják az IPv6-végpontokhoz csatlakozó IPv6-ügyfelek. Az ügyfél nem küld DNS-kérelmeket közvetlenül a Traffic Managernek. Ehelyett az ügyfél rekurzív DNS-szolgáltatást használ. A csak IPv6-ügyfél iPv6-on keresztül küld kéréseket a rekurzív DNS-szolgáltatásnak. Ezután a rekurzív szolgáltatásnak képesnek kell lennie arra, hogy kapcsolatba lépjen a Traffic Manager névkiszolgálóival az IPv4 használatával.

A Traffic Manager a végpont DNS-nevével vagy IP-címével válaszol. Az IPv6-végpont okának támogatása két lehetőség közül választhat. A végpontot hozzáadhatja a kapcsolódó AAAA rekordtal rendelkező DNS-névként, és a Traffic Manager állapotellenőrzést végez a végponton, és a lekérdezési válaszban CNAME rekordtípusként adja vissza. Azt is hozzáadhatja, hogy a végpont közvetlenül az IPv6-cím használatával, és a Traffic Manager ad vissza egy AAAA típusú rekordot a lekérdezési válasz.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Használhatom a Traffic Manager-t egynél több webalkalmazással ugyanabban a régióban?

A Traffic Manager általában a különböző régiókban telepített alkalmazásokra irányítja a forgalmat. Azonban azt is használható, ha egy alkalmazás egynél több központi telepítés ugyanabban a régióban. A Traffic Manager Azure-végpontok nem teszik lehetővé, hogy egynél több Web App-végpont ugyanabból az Azure-régióból kell hozzáadni ugyanahhoz a Traffic Manager-profilhoz.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Hogyan helyezhetem át a Traffic Manager-profil Azure-végpontjait egy másik erőforráscsoportba vagy előfizetésbe?

A Traffic Manager-profilhoz társított Azure-végpontok az erőforrás-azonosítók használatával vannak nyomon követve. Ha egy végpontként használt Azure-erőforrás (például nyilvános IP,Klasszikus felhőszolgáltatás, WebApp vagy más, beágyazott módon használt Traffic Manager-profil) egy másik erőforráscsoportba vagy előfizetésbe kerül, az erőforrás-azonosító megváltozik. Ebben az esetben jelenleg frissítenie kell a Traffic Manager-profilt először a törléssel, majd a végpontok hozzáadásával a profilhoz.

## <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager-végpontmonitorozás

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>A Traffic Manager rugalmas az Azure-régió hibáival szemben?

A Traffic Manager a magas rendelkezésre állású alkalmazások Azure-beli kézbesítésének kulcsfontosságú eleme.
A magas rendelkezésre állás biztosításához a Traffic Managernek kivételesen magas rendelkezésre állási szinttel kell rendelkeznie, és ellenállónak kell lennie a regionális hibákkal szemben.

A Traffic Manager-összetevők szándékosan rugalmasak bármely Azure-régió teljes meghibásodásával szemben. Ez a rugalmasság a Traffic Manager összes összetevőjére vonatkozik: a DNS-névkiszolgálókra, az API-ra, a tárolási rétegre és a végpontfigyelési szolgáltatásra.

Abban a valószínűtlen esetben, ha egy teljes Azure-régió kimaradása, traffic manager várhatóan továbbra is megfelelően működik. A több Azure-régióban telepített alkalmazások támaszkodhatnak a Traffic Manager-re, hogy a forgalmat az alkalmazásuk egy rendelkezésre álló példányára irányítsák.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Milyen hatással van az erőforráscsoport helyének kiválasztása a Forgalomkezelőre?

Traffic Manager egy egységes, globális szolgáltatás. Ez nem regionális. Az erőforráscsoport helyének kiválasztása nem számít az adott erőforráscsoportban üzembe helyezett Traffic Manager-profilok számára.

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport adjon meg egy helyet, amely meghatározza az adott erőforráscsoportban üzembe helyezett erőforrások alapértelmezett helyét. Traffic Manager-profil létrehozásakor az erőforráscsoportban jön létre. Minden Traffic Manager-profil **globális** helyet használ, felülírva az erőforráscsoport alapértelmezett helyét.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Hogyan állapítható meg az egyes végpontok aktuális állapotát?

Az egyes végpontok aktuális figyelési állapota a teljes profil mellett megjelenik az Azure Portalon. Ez az információ a Traffic Monitor [REST API-n](https://msdn.microsoft.com/library/azure/mt163667.aspx), a [PowerShell-parancsmagokon](https://docs.microsoft.com/powershell/module/az.trafficmanager)és a [platformfüggetlen Azure CLI-n](../cli-install-nodejs.md)keresztül is elérhető.

Az Azure Monitor segítségével is nyomon követheti a végpontok állapotát, és megtekintheti azok vizuális megjelenítését. Az Azure Monitor használatáról az [Azure Monitoring dokumentációjában](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)olvashat bővebben.

### <a name="can-i-monitor-https-endpoints"></a>Figyelhetem a HTTPS-végpontokat?

Igen. A Traffic Manager https-en keresztül támogatja a szondázást. Konfigurálja a **HTTPS protokollt** a figyelési konfigurációban.

A Forgalomkezelő nem tud tanúsítványellenőrzést biztosítani, beleértve a következőket:

* A kiszolgálóoldali tanúsítványok nincsenek érvényesítve
* Az SNI kiszolgálóoldali tanúsítványok nincsenek érvényesítve
* Az ügyféltanúsítványok nem támogatottak

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Használok IP-címet vagy DNS-nevet végpont hozzáadásakor?

A Traffic Manager három féleképpen támogatja a végpontok hozzáadását – DNS-névként, IPv4-címként és IPv6-címként. Ha a végpontot IPv4- vagy IPv6-címként adja hozzá, a lekérdezési válasz A vagy AAAA rekordtípusú lesz. Ha a végpontdns-névként lett hozzáadva, akkor a lekérdezési válasz CNAME típusú lesz. Végpontok IPv4- vagy IPv6-címként való hozzáadása csak akkor engedélyezett, ha a végpont **Külső**típusú.
Az összes útválasztási módszert és figyelési beállítást a három végpontcímzési típus támogatja.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Milyen típusú IP-címeket használhatok végpont hozzáadásakor?

A Traffic Manager lehetővé teszi, hogy IPv4- vagy IPv6-címeket használjon a végpontok megadásához. Van néhány korlátozás, amelyek az alábbiakban felsorolt:

- A fenntartott privát IP-címtereknek megfelelő címek nem engedélyezettek. Ezek a címek magukban foglalják az 1918., RFC 6890, RFC 5737, RFC 3068, RFC 2544 és RFC 5771-ben szólította kat.
- A cím nem tartalmazhat portszámokat (megadhatja a profilkonfigurációs beállításokban használandó portokat)
- Egy profilban nincs két végpont azonos cél IP-címmel

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Használhatok különböző végpontcímzési típusokat egyetlen profilon belül?

Nem, a Traffic Manager nem teszi lehetővé a végpontcímzési típusok keverését egy profilon belül, kivéve egy olyan profil esetét a MultiValue útválasztási típussal, ahol az IPv4 és az IPv6 címzési típusok at keverheti

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Mi történik, ha egy bejövő lekérdezés bejegyzéstípusa eltér a végpontok címzési típusához társított bejegyzéstípustól?

Amikor egy lekérdezés tanusított egy profilt, a Traffic Manager először megkeresi a végpontot, amelyet vissza kell adni a megadott útválasztási módszer és a végpontok állapota szerint. Ezután megvizsgálja a bejövő lekérdezésben kért rekordtípust és a végponthoz társított rekordtípust, mielőtt az alábbi táblázat alapján választ advissza.

A MultiValue-en kívüli útválasztási módszerrel rendelkező profilok esetében:

|Bejövő lekérdezési kérelem|     Végpont típusa|     Válasz: Feltéve|
|--|--|--|
|BÁRMELY |    A / AAAA / CNAME |    Célvégpont| 
|A |    A / CNAME |    Célvégpont|
|A |    AAAA |    NODATA (NODATA) |
|AAAA |    AAAA / CNAME |    Célvégpont|
|AAAA |    A |    NODATA (NODATA) |
|CNAME |    CNAME |    Célvégpont|
|CNAME     |A / AAAA |    NODATA (NODATA) |
|

MultiValue értékű útválasztási metódussal rendelkező profilok esetén:

|Bejövő lekérdezési kérelem|     Végpont típusa |    Válasz: Feltéve|
|--|--|--|
|BÁRMELY |    A és AAAA keveréke |    Célvégpontok|
|A |    A és AAAA keveréke |    Csak az A típusú célvégpontok|
|AAAA    |A és AAAA keveréke|     Csak Az AAAA típusú célvégpontok|
|CNAME |    A és AAAA keveréke |    NODATA (NODATA) |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Használhatok profilt iPv4 / IPv6 címzett végpontokkal beágyazott profilban?

Igen, azzal a kivétellel, hogy egy MultiValue típusú profil nem lehet szülőprofil beágyazott profilkészletben.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Leállítottam egy webalkalmazás végpontját a Traffic Manager-profilomban, de nem kapok forgalmat, még az újraindítás után sem. Hogyan tudom ezt helyrehozni?

Ha egy Azure-webalkalmazás-végpont leáll Traffic Manager leállítja az állapotát, és újraindítja az állapot-ellenőrzések csak azt követően, hogy észleli, hogy a végpont újraindult. A késleltetés elkerülése érdekében tiltsa le, majd engedélyezze újra a végpontot a Traffic Manager-profilban a végpont újraindítása után.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Használhatom a Traffic Managert akkor is, ha az alkalmazásom nem támogatja a HTTP-t vagy a HTTPS-t?

Igen. Megadhatja a TCP-t, mivel a figyelési protokoll és a Traffic Manager kezdeményezhet TCP-kapcsolatot, és megvárhatja a végpontválaszát. Ha a végpont válaszol a csatlakozási kérelemre a kapcsolat létrehozásához adott válaszsal, az időtúllépési időszakon belül, akkor a végpont kifogástalanként van megjelölve.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Milyen konkrét válaszok szükségesek a végponttól a TCP-figyelés használatakor?

TCP-figyelés esetén a Traffic Manager háromutas TCP-kézfogást indít el egy SYN-kérelem elküldésével a megadott portvégpontra. Ezután egy ideig megvárja a SYN-ACK-választ a végpontról (az időtúllépések beállításaiban megadva).

- Ha a figyelési beállításokban megadott időbeli megadott időn belül syn-ACK válasz érkezik, akkor ez a végpont kifogástalannak minősül. A FIN vagy FIN-ACK a Traffic Manager várt válasza, amikor rendszeresen leállít egy szoftvercsatornát.
- Ha a megadott időtúllépés után SYN-ACK válasz érkezik, a Traffic Manager rst-vel válaszol a kapcsolat visszaállításához.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Milyen gyorsan mozgatja a Traffic Manager a felhasználókat egy nem megfelelő állapotú végpontról?

A Traffic Manager több beállítást is biztosít, amelyek segítségével az alábbiak szerint szabályozhatja a Traffic Manager-profil feladatátvételi viselkedését:

- megadhatja, hogy a Traffic Manager gyakrabban vizsgálja a végpontokat a Probing Időköz 10 másodpercre beállításával. Ez biztosítja, hogy minden végpont nem kifogástalan lesz a lehető leghamarabb észlelhető. 
- megadhatja, hogy mennyi ideig várjon, mielőtt egy állapot-ellenőrzési kérelem időtúl(minimális idő túlérték 5 másodperc).
- megadhatja, hogy hány hiba fordulhat elő, mielőtt a végpont nem kifogástalanként van megjelölve. Ez az érték 0 alacsony lehet, ebben az esetben a végpont nem kifogástalan, amint nem sikerül az első állapot-ellenőrzés. Azonban a minimális 0 érték használata a tolerálható hibák száma vezethet végpontok kivenni a forgatás miatt átmeneti problémák, amelyek előfordulhatnak idején szondázás.
- megadhatja, hogy a DNS-válasz legidejének (TTL) 0 legyen. Ez azt jelenti, hogy a DNS-feloldók nem tudják gyorsítótárazni a választ, és minden új lekérdezés olyan választ kap, amely tartalmazza a Traffic Manager legfrissebb állapotadatait.

Ezekkel a beállításokkal a Traffic Manager 10 másodpercnél alatt biztosíthat feladatátvételt, miután egy végpont nem megfelelő állapotúvá válik, és dns-lekérdezés történik a megfelelő profillal.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Hogyan adhatok meg különböző figyelési beállításokat a profil különböző végpontjaihoz?

A Traffic Manager figyelési beállításai profilszintenként vannak. Ha csak egy végponthoz kell másik figyelési beállítást használnia, akkor ezt a végpontot [beágyazott profilként](traffic-manager-nested-profiles.md) teheti meg, amelynek figyelési beállításai eltérnek a szülőprofiltól.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Hogyan rendelhetek HTTP-fejléceket a Traffic Manager állapotellenőrzéseihez a végpontjaimhoz?

A Traffic Manager lehetővé teszi, hogy egyéni fejléceket adjon meg a végpontok számára kezdeményezett HTTP(S) állapotellenőrzésekben. Ha egyéni fejlécet szeretne megadni, ezt megteheti a profil szintjén (az összes végpontra vonatkozik), vagy megadhatja azt a végpont szintjén. Ha egy fejléc mindkét szinten meg van adva, akkor a végpont szintjén megadott fejléc felülírja az első profilszintet.
Ennek egyik gyakori használati esete az állomásfejlécek megadása, hogy a Traffic Manager-kérelmek megfelelően irányíthatók legyenek egy több-bérlős környezetben üzemeltetett végpontra. Ennek másik felhasználási esete a Traffic Manager-kérelmek azonosítása a végpont HTTP(S) kérelemnaplóiból

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Milyen állomásfejlécet használnak a végpont állapotellenőrzései?

Ha nincs megadva egyéni állomásfejléc-beállítás, a Traffic Manager által használt állomásfejléc a profilban konfigurált végpontcél DNS-neve, ha ez elérhető.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Melyek azok az IP-címek, amelyekről az állapotvizsgálatok származnak?

Kattintson [ide](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) a JSON-fájl megtekintéséhez, amely felsorolja azokat az IP-címeket, amelyekből a Traffic Manager állapotellenőrzése származhat. Tekintse át a JSON-fájlban felsorolt IP-címeket, és győződjön meg arról, hogy az ezekből az IP-címekről érkező kapcsolatok engedélyezettek a végpontokon az állapotának ellenőrzéséhez.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Hány állapotellenőrzést várhatok a Forgalomkezelőtől a végpontomon?

A végpontot elérő Traffic Manager állapotellenőrzések száma a következőktől függ:

- a figyelési időközhöz beállított érték (a kisebb időköz azt jelenti, hogy egy adott időszakban több kérés landol a végponton).
- azoknak a helyeknek a száma, ahonnan az állapotvizsgálatok származnak (azok az IP-címek, ahonnan ezekre az ellenőrzésekre számíthat, az előző GYIK-ben szerepelnek).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Hogyan kaphatok értesítést, ha az egyik végpontom leáll?

A Traffic Manager által biztosított metrikák egyike a végpontok állapota egy profilban. Ez a profilon belüli összes végpont összesítéseként (például a végpontok 75%-a kifogástalan állapotú), vagy végpontonkénti szinten. A Traffic Manager-metrikák az Azure Monitoron keresztül vannak elérhetővé téve, és [a riasztási képességek](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) használatával értesítéseket kaphat, ha a végpont állapotának változása történik. További részletekért lásd: [Traffic Manager mérőszámok és riasztások.](traffic-manager-metrics-alerts.md)  

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager beágyazott profilok

### <a name="how-do-i-configure-nested-profiles"></a>Hogyan konfigurálhatom a beágyazott profilokat?

A beágyazott Traffic Manager-profilok konfigurálhatók az Azure Resource Manager és a klasszikus Azure REST API-k, az Azure PowerShell-parancsmagok és a platformfüggetlen Azure CLI-parancsok használatával. Az új Azure portalon keresztül is támogatottak.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Hány réteg beágyazási nem Traffic Manger támogatja?

A profilok legfeljebb 10 szint mélyre ágyazhatók. A "Hurkok" nem engedélyezettek.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Keverhetek más végponttípusokat beágyazott gyermekprofilokkal ugyanabban a Traffic Manager-profilban?

Igen. Nincsenek korlátozások arra vonatkozóan, hogy hogyan kombinálhatja a különböző típusú végpontokat egy profilon belül.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Hogyan vonatkozik a számlázási modell a beágyazott profilokra?

A beágyazott profilok használatának nincs negatív árképzési hatása.

A Traffic Manager számlázásának két összetevője van: végpontállapot-ellenőrzés és több millió DNS-lekérdezés

* Végpont állapot-ellenőrzései: Nincs díj a gyermek profil, ha konfigurálva van egy végpont egy szülő profilban. A gyermekprofil végpontjainak figyelése a szokásos módon történik.
* DNS-lekérdezések: Minden lekérdezés csak egyszer számít. A szülőprofilelleni lekérdezés, amely végpontot ad vissza egy gyermekprofilból, csak a fölérendelt profilba számít bele.

További részletekért tekintse meg a [Traffic Manager díjszabási oldalát.](https://azure.microsoft.com/pricing/details/traffic-manager/)

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>A beágyazott profilok teljesítményre gyakorolt hatása van?

Nem. A beágyazott profilok használata nem gyakorol teljesítményhatást.

A Traffic Manager névkiszolgálói az egyes DNS-lekérdezések feldolgozásakor belsőleg haladnak át a profilhierarchián. A szülőprofildns-lekérdezése egy végpontot záró ponttal rendelkező DNS-választ kaphat egy gyermekprofilból. Egyetlen CNAME rekord használatos, függetlenül attól, hogy egyetlen profilt vagy beágyazott profilt használ. Nincs szükség CNAME rekord létrehozására a hierarchia minden egyes profiljához.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Hogyan számítja ki a Traffic Manager a szülőprofil beágyazott végpontjának állapotát?

A szülőprofil nem végez közvetlenül állapotellenőrzést a gyermeken. Ehelyett a gyermekprofil végpontjainak állapotát használják a gyermekprofil általános állapotának kiszámításához. Ez az információ propagált fel a beágyazott profil hierarchia állapotának meghatározásához a beágyazott végpont. A szülőprofil ezt az összesített állapotot használja annak meghatározására, hogy a forgalom átirányítható-e a gyermekre.

Az alábbi táblázat a Traffic Manager állapotellenőrzései egy beágyazott végpont viselkedését ismerteti.

| Gyermekprofil-figyelő állapota | Szülő végpontfigyelő állapota | Megjegyzések |
| --- | --- | --- |
| Letiltva. A gyermekprofil le van tiltva. |Leállítva |A szülővégpont állapota Leállítva, nem letiltva. A Letiltott állapot annak jelzésére van fenntartva, hogy letiltotta a végpontot a szülőprofilban. |
| Leromlott. Legalább egy gyermekprofil-végpont degradált állapotban van. |Online: a gyermekprofilban lévő online végpontok száma legalább a MinChildEndpoints értéke.<BR>CheckingEndpoint: az online és checkingendpoint végpontok száma a gyermek profil legalább a MinChildEndpoints értéke.<BR>Leromlott: egyébként. |A forgalom az állapotellenőrzésvégpont végpontja felé lesz irányítva. Ha a MinChildEndpoints túl magasra van állítva, a végpont mindig leromlott. |
| Online. Legalább egy gyermekprofil-végpont online állapot. Nincs végpont a degradált állapotban. |Lásd fent. | |
| Végpontok ellenőrzése. Legalább egy gyermekprofil-végpont a "CheckingEndpoint". Nincs olyan végpont, amely "Online" vagy "Degraded" lenne. |Ugyanaz, mint fent. | |
| Inaktív. Minden gyermekprofil-végpont le tiltva vagy leállítva, vagy ennek a profilnak nincsenek végpontjai. |Leállítva | |

## <a name="next-steps"></a>Következő lépések:

- További információ a Traffic Manager [végpontfigyelésről és az automatikus feladatátvételről.](../traffic-manager/traffic-manager-monitoring.md)
- További információ a Traffic Manager [forgalomirányítási módszereiről.](../traffic-manager/traffic-manager-routing-methods.md)
