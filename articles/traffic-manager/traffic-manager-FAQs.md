---
title: Az Azure Traffic Manager - – gyakori kérdések |} Microsoft Docs
description: Ez a cikk ismerteti a Traffic Manager gyakran feltett kérdésekre adott válaszok
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
ms.date: 03/18/2018
ms.author: kumud
ms.openlocfilehash: d9db669ab905fb51390f6ca80736af4cde13d902
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>A TRAFFIC Manager gyakori kérdések (GYIK)

## <a name="traffic-manager-basics"></a>A TRAFFIC Manager alapjai

### <a name="what-ip-address-does-traffic-manager-use"></a>Milyen IP-címet használ a Traffic Manager?

A [Traffic Manager működése](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), a Traffic Manager DNS szintjén működik. DNS-válaszok irányíthatják az ügyfeleket a megfelelő szolgáltatásvégpontot küldi. Ügyfelek majd csatlakozni a szolgáltatás végpontjához közvetlenül, nem Traffic Manager használatával.

Ezért a Traffic Manager nem adja meg egy végpont vagy IP-címe az ügyfelek csatlakoznak. Ha a szolgáltatás, amely is be kell állítani a szolgáltatás, nem a Traffic Manager statikus IP-címet.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Támogatja a Traffic Manager "kapcsolódó" munkamenetek?

A [Traffic Manager működése](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), a Traffic Manager DNS szintjén működik. DNS-válaszok irányíthatják az ügyfeleket a megfelelő szolgáltatásvégpontot használ. Az ügyfelek a szolgáltatási végpont közvetlenül kapcsolódik, nem Traffic Manager használatával. A Traffic Manager emiatt nem látható a HTTP-forgalmat az ügyfél és a kiszolgáló között.

Emellett a forrás IP-címét a DNS-lekérdezés érkezett a Traffic Manager által a rekurzív DNS-szolgáltatás, az ügyfél nem tartozik. Ezért a Traffic Manager nem tudja nyomon követni az egyes ügyfelek, és "kapcsolódó" munkamenetek nem valósítható meg. Ez a korlátozás gyakori megoldás, hogy minden DNS-alapú forgalom felügyeleti rendszer, és nincs meghatározott felvétele a Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Miért jelenik meg egy HTTP-hiba a Traffic Manager használata esetén?

A [Traffic Manager működése](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), a Traffic Manager DNS szintjén működik. DNS-válaszok irányíthatják az ügyfeleket a megfelelő szolgáltatásvégpontot használ. Ügyfelek majd csatlakozni a szolgáltatás végpontjához közvetlenül, nem Traffic Manager használatával. A TRAFFIC Manager funkciója nem lásd: HTTP-forgalom ügyfél és kiszolgáló között. Ezért a HTTP-hibát látja kell hamarosan az alkalmazásból. Az ügyfél kapcsolódni az alkalmazáshoz az összes DNS megoldási lépések teljesülnek. A Traffic Manager meg az alkalmazás adatforgalmat rendelkező beavatkozás, amely tartalmazza.

További vizsgálat ezért az alkalmazás kell összpontosítania.

A HTTP-host fejléc, az ügyfél böngészője által küldött egy a leggyakoribb problémák. Győződjön meg arról, hogy az alkalmazás használja a tartománynév helyes-e állomásfejlécét elfogadására van konfigurálva. Az Azure App Service használó végpontok, lásd: [egy webalkalmazást az egyéni tartománynév konfigurálása az Azure App Service szolgáltatásban a Traffic Managerrel](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Mi az a teljesítményre gyakorolt hatását a Traffic Manager használatával?

A [Traffic Manager működése](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), a Traffic Manager DNS szintjén működik. Ügyfelek közvetlenül kapcsolódik a végpontok, mivel nincs a Traffic Manager használatakor, miután létrejött a kapcsolat felmerülő teljesítmény hatással.

A Traffic Manager DNS szinten alkalmazások integrálható, mert a DNS-feloldási láncba beszúrni egy további DNS-címkeresés legyen szükség. A Traffic Manager DNS-feloldási idő gyakorolt hatása a minimális. A TRAFFIC Manager névkiszolgálók globális hálózata, és használja a [nem egyedi](https://en.wikipedia.org/wiki/Anycast) DNS biztosításához hálózati lekérdezéseket a rendszer mindig irányítja át a legközelebbi rendelkezésre álló neve-kiszolgáló. Ezenkívül DNS-válaszok gyorsítótárazásának azt jelenti, hogy a további DNS késését a Traffic Manager használatával csak a munkamenetek töredéke alatt érvényes.

A teljesítmény metódus irányítja a forgalmat a legközelebbi elérhető végpontnak. A nettó eredménye, hogy ez a módszer társított általános teljesítményre gyakorolt minimális legyen. A DNS-késés növekedése lesz eltolva a alacsonyabb hálózati késés a végpontnak.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Milyen alkalmazás protokollok használhatók a Traffic Managerrel?

A [Traffic Manager működése](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), a Traffic Manager DNS szintjén működik. Ha a DNS-keresés befejeződött, ügyfelek csatlakozhatnak az alkalmazás végpontjának közvetlenül, nem Traffic Manager használatával. A kapcsolat, ezért bármely alkalmazás protokoll használható. Ha TCP választja, a figyelési protokoll, a Traffic Manager végpont állapotfigyelés állásideje nélkül végezhetők el bármely alkalmazás protokollok használatával. Ha szeretné, hogy egy alkalmazás protokoll segítségével állapotát, a végponthoz HTTP vagy HTTPS-GET-kérésekre való válaszolás tenni.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Használható a Traffic Manager "csupasz" tartománynévvel?

Nem. A DNS-szabványokból párhuzamosan működhet más DNS-rekordok az azonos nevű CNAME nem teszik lehetővé. A legfelső pontján (vagy a főtanúsítvány) a DNS-zónák mindig tartalmazza a már meglévő DNS-rekordok két; a SOA és a mérvadó Névkiszolgálói rekordokat. Ez azt jelenti, hogy a CNAME rekord nem hozható létre a zóna felső pontja a DNS-szabványokból megsértése nélkül.

A TRAFFIC Manager egy DNS CNAME rekord hozzárendelését a kreatív DNS-név szükséges. Például www.contoso.com leképezheti a Traffic Manager-profil DNS neve contoso.trafficmanager.net. Emellett a Traffic Manager-profil adja vissza egy második DNS CNAME jelzéséhez, hogy melyik végponthoz az ügyfélnek csatlakoznia kell.

A probléma megoldása érdekében azt javasoljuk, a csupasz tartomány neve egy másik URL-címre, amely ezután használhatja a Traffic Manager forgalom közvetlen HTTP átirányítás. Például a "contoso.com" tartomány csupasz is irányítsa át a felhasználókat a CNAME "www.contoso.com" mutat, a Traffic Manager DNS-nevét.

Teljes támogatást nyújt a Traffic Manager csupasz tartományok zajlik, a szolgáltatás várakozó fájlok számát a. A támogatási kérelem által a szolgáltatás regisztrálhatja [a közösségi visszajelzés helyen szavazott](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Nem a Traffic Manager fontolja meg az ügyfél alhálózati címét DNS-lekérdezések kezelése során? 
Igen, a DNS-lekérdezést a forrás IP-címe mellett kap (amely általában a DNS-feloldási IP-címét), a Geographic és teljesítmény útválasztási metódusait végrehajtott keresési műveletek végrehajtásakor a traffic manager is figyelembe veszi az ügyfél alhálózati cím esetén a lekérdezés által a kérést a felhasználó nevében feloldó tartalmazza.  
Pontosabban [RFC 7871 – ügyfél alhálózati a DNS-lekérdezések](https://tools.ietf.org/html/rfc7871) biztosít egy [bővítmény mechanizmus a DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) a feloldókat, amelyek támogatják ezt az ügyfél alhálózati cím át is.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>DNS-élettartam és mi az a hatása a felhasználók?

Ha egy DNS-lekérdezést a Traffic Manager fájljai, a válaszban idő-Élettartam (TTL) nevű megad egy értéket. Ezt az értéket, amelynek egység másodpercen belül van, azt jelzi, hogy DNS feloldókat után a mennyi ideig gyorsítótárazza a választ a. Amíg a DNS-feloldókat nem garantált, hogy ez az eredmény gyorsítótár, gyorsítótárazás lehetővé teszik, hogy a gyorsítótár a Traffic Manager DNS-kiszolgálók megkerülve ki további lekérdezések válaszolni. Ez hatással van a válaszok az alábbiak szerint:
- magasabb TTL csökkenti a lekérdezések száma, amelyek a Traffic Manager DNS-kiszolgálók, amelyek csökkentheti a költségeket ügyfél mivel kiszolgált lekérdezések számlázható használati meg.
- magasabb TTL potenciálisan csökkentheti a DNS-címkeresést elvégzéséhez szükséges idő.
- magasabb TTL is azt jelenti, hogy, hogy az adatok nem tükrözi a legfrissebb állapottal kapcsolatos adatok, amelyek a Traffic Manager megkapta a vizsgálathoz használt ügynökök keresztül.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Hogyan magas vagy alacsony lehet állítható be az élettartam a Traffic Manager-válaszok?

Beállíthatja, egy profil szintek, a DNS-élettartam kell lennie az engedélyezett minimumérték megegyezik 0 másodperc és 2 147 483 647 másodpercig magas (a tartomány maximális megfelelő [RFC-1035 szabványnak megfelelően](https://www.ietf.org/rfc/rfc1035.txt )). A 0 azt jelenti, hogy alárendelt DNS-feloldókat nem gyorsítótárazzák a válaszokban, és az összes lekérdezés várhatóan elérni a Traffic Manager DNS kiszolgálók a névfeloldás TTL.

## <a name="traffic-manager-geographic-traffic-routing-method"></a>A TRAFFIC Manager Geographic forgalom-útválasztási módszert

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Néhány alkalmazási helyzetét, ahol földrajzi útválasztási akkor hasznos, melyek? 
Amikor az ügyfél egy Azure kell különbséget tenni a felhasználóknak a földrajzi régiók alapján földrajzi útválasztási típus használható egyik szituációban. Például a földrajzi forgalom-útválasztási módszert használ, engedélyezheti a felhasználók számára az adott régióban el más régiókból mint egy másik felhasználói élményt. Egy másik példa megfelel a helyi adatok közös joghatóság alá megbízás szükséges, hogy a felhasználók egy adott régióban tudja megjeleníteni csak az adott régióban végpontok.

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Mik a Traffic Manager által támogatott földrajzi irányításához régiók? 
A Traffic Manager által használt ország vagy régió hierarchiában található [Itt](traffic-manager-geographic-regions.md). Ezen a lapon módosításai naprakészen szerepeljenek tartják, amíg szoftveresen is olvashatók be ugyanazokat az információkat használja a [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Hogyan nem a traffic manager meg, ha a felhasználó kérdezi le, a? 
A TRAFFIC Manager ellenőrzi, hogy a forrás IP-cím a lekérdezés (Ez a legvalószínűbb ok az a helyi DNS-feloldási, ennek során a felhasználó nevében lekérdezése) és olyan belső IP-címet hozzárendelni a régió alapján határozza meg a helyet. Ez a térkép az interneten változásainak folyamatosan frissül. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Ez garantálja, hogy a Traffic Manager megfelelően meghatározhatja a pontos minden esetben a felhasználó földrajzi helye?
Nem, a Traffic Manager nem garantálja, hogy a földrajzi régiót, azt következtethető ki, a DNS-lekérdezést a forrás IP-címe mindig felel meg a felhasználó földrajzi helye a következő okok miatt: 

- Először a korábbi GYIK leírtak a a forrás IP-cím látható, hogy a DNS-feloldási, ennek során a felhasználó nevében a keresés. Bár a földrajzi helyet, a DNS-feloldási esetén a felhasználó földrajzi helye a helyes proxy, azt is csökkentése a DNS-feloldó szolgáltatás és a megadott DNS-feloldó szolgáltatás használatát választotta az ügyfél másik függően. Tegyük fel az ügyfél malajziai található kell megadni az eszköz beállításait használja a DNS-feloldó szolgáltatás, amelynek DNS-kiszolgáló szingapúri előfordulhat, hogy első választja ki a kezelni a lekérdezés megoldások, hogy a felhasználó vagy eszköz. A Traffic Manager ebben az esetben csak tekintheti meg a feloldó IP-címet, amely megfelel a szingapúri helyre. További információ a korábbi alhálózati cím ügyféltámogatás vonatkozó gyakran ismételt kérdések ezen a lapon.

- Második a Traffic Manager használja egy belső leképezés az a földrajzi régióban fordítási IP-címet. Amíg ez a leképezés érvényesítése és növelik a pontosságát, és a fiókot használja az internet változó jellege folyamatosan frissített, továbbra is fennáll, hogy az információ nincs az IP-címek földrajzi helye pontos másolatát lehetőségét.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Nem egy olyan végpont szükséges elhelyezésűek megegyezik a földrajzi útválasztásra van konfigurálva az ugyanabban a régióban? 
Nem, a végpont helye korlátozza a dokumentumkulcsban nem amelyen régiók rendelhetők hozzá. Például egy olyan végpont Egyesült Államok középső Azure-régióban lehet származó átirányítja az összes felhasználó.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>I rendelhet földrajzi régiókhoz egy profilt, amely nincs konfigurálva ehhez a földrajzi útválasztási végpontok? 

Igen, ha a profil-útválasztási módszert nincs földrajzi, használhatja a [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) földrajzi régiókhoz hozzárendelése végpontokat a profilhoz. Nem földrajzi útválasztási típus-profilok esetén ez a konfiguráció figyelmen kívül hagyja. Ha egy ilyen profil földrajzi útválasztási típus módosítja egy későbbi időpontban, a Traffic Manager használja ezeket a leképezéseket.


### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Miért jelenik hiba módosítsa egy meglévő profilt útválasztási módszer Geographic közben?

Földrajzi útválasztási olyan profilban a végpontok szükséges rendelve legalább egy régiót. Egy meglévő profilt átalakítása földrajzi útválasztás típusa, először a használó végpontok földrajzi régió hozzárendelni a [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) a földrajzi útválasztási típusának módosítása előtt. Portál használatával, ha először törölje a végpontokat, módosítsa a profil útválasztási módszer földrajzi, és adja hozzá az a földrajzi régió leképezése együtt a végpontok. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Miért érdemes az erősen ajánlott, ha az ügyfelek beágyazott profilok olyan profilban végpontok helyett hozzon létre a földrajzi engedélyezett útválasztással? 

A régió egy profilon belül csak egy végpont rendelhetők, ha a földrajzi útválasztási típus használatával. Ha a végpont nem beágyazott típus egy gyermek-profil csatlakozik, a forgalom adott végpont is sérült állapotba, ha Manager továbbra is forgalmat küldeni, a tulajdonos alternatív, nem minden forgalom nem minden jobb továbbításának óta. A TRAFFIC Manager elvégzi a másik végpont nem feladatátvétel, még akkor is, amikor a a régió rendelve a végpontot, amely a hiba nem megfelelő állapotú (például, ha olyan végponttal, amely rendelkezik régió Spanyolország kerül sérült állapotba, nézzük meg egy másik végpont nem feladatátvétel, a régió "szülője" a régió Európa rendelve rendelkezik). Erre azért van szükség, győződjön meg arról, hogy a Traffic Manager tiszteletben tartja a földrajzi határokat, hogy rendelkezik-e az ügyfél a telepítő a profilban. Ahhoz, hogy az az előnye, hogy egy másik végponthoz feladatátadásra, a végpont kerül sérült állapotba, ha, javasoljuk, hogy földrajzi régiókhoz helyett egyedi végpontok belül több végpontot beágyazott profilt kell rendelni. Ezzel a módszerrel a beágyazott gyermektevékenységének profilban a végpont nem sikerül, ha forgalmat feladatátvétel történhet beágyazott gyermektevékenységének ugyanazzal a profillal belül egy másik végpontjával.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Vannak-e az API-verzió, amely támogatja a útválasztási típus korlátozásai?

Igen, csak a API-verzió 2017-03-01 és az újabb támogatja a földrajzi útválasztási típusa. Bármely régebbi kiadását API nem használható földrajzi útválasztási típusú létrehozott profilok vagy földrajzi régiókhoz hozzárendelése végpontok. Profilok beolvasása az Azure-előfizetésből olyan régebbi verziójú API használata esetén a profil földrajzi útválasztási típusú nem jelennek meg. Emellett régebbi API-verziók használata esetén olyan profilt, amely a földrajzi régióban hozzárendelésekor végpontok, akkor nem rendelkezik a földrajzi régióban hozzárendelése látható adott vissza.

## <a name="real-user-measurements"></a>Valós felhasználóiélmény-mérések

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Mik a tényleges felhasználói mérések használatának előnyeit?
Teljesítmény útválasztási módszer használata esetén a Traffic Manager választja ki a forrás IP-cím és EDNS ügyfél alhálózati vizsgálatával (ha az átadott) csatlakozni a végfelhasználó számára az ajánlott Azure-régió, és megőrzi azt a hálózati késés intelligence a szolgáltatás ellen ellenőrzése. Valós felhasználói mérések javítja ezt a végfelhasználó alap azzal, hogy a felhasználói élmény hozzájárulnak a késés táblázat állapotának biztosítása mellett ez a táblázat megfelelő kiterjedő a végfelhasználó hálózatokat ahol a végfelhasználók csatlakozik az Azure. Ennek eredménye a végfelhasználók számára a útválasztását a megnövekedett pontosság.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Használható az-Azure régiók valós felhasználói mérések?
Valós felhasználói mérések mértékeket és csak az Azure-régiók eléréséhez késés jelentéseket. Ha használ az Azure-régiókban futó végpontokon teljesítmény-alapú útválasztási, révén továbbra is kihasználhatja a Ez a szolgáltatás által nőtt késés információ reprezentatív Azure-régiót kell lennie ehhez a végponthoz társított választott ki.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Útválasztási módszer előnyökkel jár a tényleges felhasználói mérésekből?
Valós felhasználói mérések keresztül szerzett további információk csak a teljesítmény-útválasztási módszert használó profilok alkalmazhatók. Vegye figyelembe, hogy a tényleges felhasználói mérések hivatkozás érhetők el az összes profil az Azure portálon keresztül megtekintésekor.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Kell engedélyezni a valós felhasználói mérések minden profilt külön-külön?
Nem, csak engedélyeznie kell azt egyszer előfizetésenként és a késési adatokat mért és jelentett érhetők el az összes profilra.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Hogyan kikapcsolni a valós felhasználói mérések az előfizetést?
Le is keletkezhetnek költségek valós felhasználói mérések kapcsolódik, ha kikapcsolja az összegyűjtése és hátsó késés mérések történő küldéséhez az ügyfélalkalmazást. Például mérési JavaScript weblapok beágyazott, leállíthatja, ezzel a szolgáltatással a JavaScript eltávolításával vagy kikapcsolásával a hívására, ha a lap megjelenítése.

Is kikapcsolhatja valós felhasználói mérések által a kulcs törlése. A kulcs törlése után a rendszer elveti a bármely mérési felvétele a Traffic Manager küldött ezzel a kulccsal.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Használhatok valós felhasználói mérések ügyfélalkalmazások weblapok kívül?
Igen, a valós felhasználói mérések gyûjtött végfelhasználó ügyfelek különböző típusú adatok szolgál. Ez a GYIK frissíti ügyfélalkalmazások új típusú támogatott beolvasása.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Hány mérések minden alkalommal, amikor a valós felhasználói mérések weblap engedélyezve lett megjelenítve?
Amikor valós felhasználói mérések használják a mérés JavaScript megadott, minden oldal megjelenítési hat méréseket eredményezi. Ezek ezután jelentett vissza a Traffic Manager szolgáltatásba. Vegye figyelembe, hogy van szó, a mértékek száma alapján ez a szolgáltatás jelentette a Traffic Manager szolgáltatásnak. Például ha a felhasználó elhagyja a weblap nyit, amíg a mérés, de azt jelentették előtt, a fenti mérések nem veszi figyelembe számlázási célokra.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Van egy késleltetés valós felhasználói mérések parancsfájl futtatása a weblap a előtt?
Nem, nem lesz programozott késleltetés, mielőtt meghívták a parancsfájlt.

### <a name="can-i-use-configure-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Használható a tényleges felhasználói mérések konfigurálása csak az Azure-régiók mérni kívánt?
Meghívták, nem, minden alkalommal a tényleges felhasználói mérések parancsfájlja hat Azure-régiók a szolgáltatás által meghatározott csoportja. A beállított között különböző módosításokat, és számos ilyen indítások fordulhat elő, amikor a mérési érvényességének különböző Azure-régiók történjen-e.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Korlátozhatja a megadott mérések száma?
JavaScript ágyazva a weblapot, és a mérési: a teljes felügyeletet keresztül, ha a kezdési és befejezési használja azt. Mindaddig, amíg a Traffic Manager szolgáltatás Azure-régiók listáját mérendő kérelmet kap, régiók készlete is megjelennek.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Láthatja a saját ügyfélalkalmazás valós felhasználói mérések részeként mérések?
A mérési programot az ügyfélalkalmazást fut, mert éppen teljes hozzáférés, mit jelent meg, a késés mérések beleértve történik. A TRAFFIC Manager nem egy összesítő nézetet alapján a kulcs az Ön előfizetéséhez kapcsolva kapott mérési jelentést.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Módosíthatja a Traffic Manager által megadott mérési parancsfájl?
Amíg feletti Mi a weblapon van beágyazva, kifejezetten nem ajánlott, győződjön meg arról, hogy azt méri, és jelenti helyesen a késések fordulnak elő a mérési parancsfájlt olyan módosítások végrehajtását.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Ez lehet majd mások is láthatják a kulcs valós felhasználói mérések használata?
A mérési parancsfájl egy weboldalra beágyazásakor lehet majd mások is láthatják a parancsfájlt és a tényleges felhasználói mérések (RUM) kulcsát. De fontos tudni, hogy a kulcs eltér az előfizetés-azonosítóval, és van Manager által generált forgalom csak az erre a célra használt. A RUM kulcs ismerete nem veszélyezteti az Azure-fiók biztonsági.

### <a name="can-others-abuse-my-rum-key"></a>Való mások visszaélés a RUM kulcsot?
Habár téves információkat küldhet az Azure-bA a kulcs mások számára is lehetséges vegye figyelembe, hogy néhány nem megfelelő mérések nem módosítja a útválasztási, mivel azt figyelembe továbbá minden más mérési érkező. Ha módosítania kell a kulcsokat, újra hozhat létre a kulcsot, amikor a régi kulcs lesz vetve.

###  <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Kell állítani a weblapokat a mérés JavaScript?
Valós felhasználói mérések további kamatoztathatják mérések növelje a számának. Rendelkezik, amely említett, akkor a eldönthető, hogy mikor szükséges behelyezi a weblapok vagy select kevés. Azt javasoljuk, révén a legtöbbet megtekintett lapot, hogy öt másodpercenként, vagy több gombra a felhasználó várt elindításához.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>A végfelhasználók információt azonosítható Traffic Manager által valós felhasználói mérések használata esetén?
A megadott mérési JavaScript használata esetén a Traffic Manager lesz lássák az ügyfél IP-címét a végfelhasználó és a forrás IP-címét a helyi DNS-feloldási használnak. A TRAFFIC Manager használ az ügyfél IP-cím csak után nem lehet azonosítani a mérések küldő végfelhasználói méretűre csonkolja azt. 

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>A weblap mérési valós felhasználói mérések kell használnia a Traffic Manager útválasztási használ?
Nem, nem szükséges a Traffic Manager használata. A Traffic Manager útválasztási oldalon külön-külön a valódi felhasználók mérési részéről működik, és bár a legjobb ötlet való két webes ugyanahhoz a tulajdonsághoz, azokat nem kell lennie.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Van bármilyen valós felhasználói mérések használni kívánt Azure-régiók a szolgáltatást?
Nem, nem kell server ügyféloldali összetevők közül bármelyik Azure valós felhasználói mérések működéséhez üzemeltetésére. Egyetlen képpontos kép tölti le a mérés JavaScript és a szolgáltatás fut az Azure különböző régiókban birtokolt, és az Azure-ban felügyelt. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Növeli a saját Azure sávszélesség-használat valós felhasználói mérések használatakor?
Ahogyan az előző válasz, a kiszolgálóoldali összetevőket valós felhasználói mérések birtokolt vagy kezelt az Azure-ban. Ez azt jelenti, hogy a Azure sávszélesség-használat nem növekszik, mivel a tényleges felhasználói mérések használható. Vegye figyelembe, hogy milyen Azure percalapú kívül bármely sávszélesség-használat nem tartalmaz. Azt a csak egy egyetlen képpontos lemezkép letöltése a várakozási Azure-régióhoz mértékhez által használt sávszélesség minimálisra csökkenthető. 

## <a name="traffic-view"></a>Traffic View

### <a name="what-does-traffic-view-do"></a>Mire használható a forgalom nézetet?
Forgalom nézet lehetővé teszi a Traffic Manager segítségével többet szeretne tudni a felhasználók, és hogyan tapasztalataikat. A Traffic Manager és a hálózati késés üzletiintelligencia-táblák biztosítja, hogy a következő megőrzi a szolgáltatás által fogadott lekérdezések használja:
- A régiókban, ahol a felhasználók csatlakoznak a végpontokat az Azure-ban található.
- Ezek a területek a csatlakozó felhasználók mennyiségét.
- Az Azure-régiók, amelyhez ezeket a rendszer első irányítja át.
- Azok a Azure-régiók késés kezelését.

Ez az információ is felhasználhatják földrajzi térképként területre történjen, és a táblázatos nézet nem érhető el, a nyers adatokat ahhoz, hogy töltse le a portálon keresztül érhető el.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Hogyan is kihasználhatja a forgalom nézet használatával?

Forgalom nézet lehetővé teszi a forgalom a Traffic Manager-profilok kap átfogó képet. Különösen használat megtudhatja, hogy ha a felhasználói bázis modellelemek és egyaránt fontos az átlagos várakozási élmény van. Ez az információ segítségével majd területeken, ahol kell összpontosítania, például az Azure erőforrásigényét kibontásával, amely ki tud szolgálni azoknak a felhasználóknak, kisebb késést régióban található. Egy másik insight származik forgalom nézet használata a forgalmat különböző régiókban, amely viszont segíthet döntést hozni bővítése vagy csökkentése az érintett régiók készlet mintáinak megjelenítéséhez.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Miben különbözik nézet forgalom a Traffic Manager metrikák Azure figyelő keresztül elérhető?

Az Azure figyelő tudni összesített szinten, a profil és a végpont által fogadott használható. Lehetővé teszi a végpontok állapotát nyomon jelentkezik, mintha a health ellenőrzésének az eredménye. Ezek túlmutató és csatlakozás az Azure-ba, a regionális szintű a végfelhasználói élmény megértése kell, amikor forgalom megtekintése, amelyek eléréséhez használható.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Használ a forgalom nézet EDNS ügyfél alhálózati adatokat?

A DNS-lekérdezések Azure Traffic Manager által kiszolgált fontolja meg a ECS pontosabbá az útválasztási adatokat. De jeleníti meg, ahol a felhasználók csatlakozik adatkészlettel létrehozásakor forgalom nézet csak a DNS-feloldási IP-címét használja.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Hány napnyi adat nem használható a forgalom nézet?

Forgalom megtekintése a hét nap szerinti napot megelőző megelőző Ön megtekintéskor adatfeldolgozás hoz létre annak kimenetét. Ez az ablak áthelyezése, és a legfrissebb adatok meglátogatása fog történni.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Hogyan forgalom nézet külső végpontok száma kezelni?

Külső végpontok Azure Traffic Manager-profil-régiók kívül tárolt használatakor kiválaszthatja azt az Azure-régió, amely a késés jellemzői (ez valóban szükséges teljesítmény útválasztási módszer használatakor) esetén a proxy leképezve. Ha ezt a hozzárendelést az Azure-régió, adott Azure-régiót késés metrikák lesz használható, ha a forgalom nézet kimenet létrehozása. Ha nincs Azure-régió van megadva, akkor a késési adatok mindegyik külső végpont adatok üres lesz.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Kell forgalom nézet engedélyezése az egyes profilok az előfizetést?

A próbaidőszak alatt forgalom nézet egy előfizetés szintjén lett engedélyezve. A javulás azt a nyilvános elérhetőség utáni előtt részeként forgalom nézet egy profil szinten, lehetővé téve, hogy ez a funkció engedélyezése részletesebb mostantól engedélyezhető. Alapértelmezés szerint forgalom nézet egy profil letiltásra kerül.

>[!NOTE]
>Ha forgalom nézet egy előfizetés szintjén preview ideje alatt engedélyezve van, most kell minden, az adott előfizetéshez tartozó profil engedélyezze újra.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Hogyan lehet kikapcsolni az forgalom nézetet? 
Kikapcsolhatja a forgalom nézet, a portál vagy a REST API használatával minden profil esetében. 

### <a name="how-does-traffic-view-billing-work"></a>Hogyan működik a forgalom nézet számlázási?

Forgalom nézet árképzési a kimeneti létrehozásához használt adatpontok száma alapján. Jelenleg csak támogatott adattípusa a lekérdezéseket, a profil kap. Ezenkívül Ön csak számlázása a feldolgozás, ha engedélyezve van a forgalom nézet végezhető el. Ez azt jelenti, hogy forgalom nézet engedélyezése a hónap bizonyos ideig, és kapcsolja ki a többi időszakban során, ha csak a feldolgozása közben a szolgáltatás kellett adatpontok számát, a számlán felé engedélyezve.

## <a name="traffic-manager-endpoints"></a>Traffic Manager-végpontok

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Használhatom a Traffic Manager végpontok több előfizetést?

Több előfizetés végpontok használata nem lehetséges az Azure Web Apps. Az Azure Web Apps megköveteli, hogy bármely a webalkalmazásokkal használt egyéni tartománynév csak egyetlen előfizetéssel belül használják. Nincs lehetőség a Web Apps használja több előfizetéssel, valamint az ugyanazon a néven.

Más típusú végpontok esetében célszerű a Traffic Manager végpontokon egynél több előfizetésnek a használatát. Az erőforrás-kezelőben a bármely előfizetés is lehet végpontokat hozzáadni a Traffic Manager mindaddig, amíg a személy a Traffic Manager-profil konfigurálása a végpont olvasási hozzáféréssel rendelkezik. Ezek az engedélyek segítségével engedélyezhetők [Azure Resource Manager szerepköralapú hozzáférés-vezérlést (RBAC)](../active-directory/role-based-access-control-configure.md).


### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Használhatom a Traffic Manager Felhőszolgáltatás "Átmeneti" üzembe helyezési ponti?

Igen. A felhőalapú szolgáltatás "átmeneti" üzembe helyezési ponti konfigurálhatók a Traffic Manager külső végpontok száma. Állapot-ellenőrzési eredményeire továbbra is van szó, az Azure-végpontok díj. A külső végpont típus használatban van, mert a mögöttes szolgáltatás módosítása nem átveszik automatikusan. Külső végpontok száma, a Traffic Manager nem tudja észlelni a felhőalapú szolgáltatás leállítása vagy törlése. Ezért a Traffic Manager továbbra is fennáll az állapot-ellenőrzési eredményeire számlázási mindaddig, amíg a végpont letiltott vagy törölt.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>A Traffic Manager támogatja az IPv6-végpontot?

A TRAFFIC Manager jelenleg nem biztosít IPv6-addressible névkiszolgálók. A Traffic Manager továbbra is használható IPv6-ügyfelek IPv6-végpontot csatlakozik. Egy ügyfél nem tesz, azaz DNS-kérelmek közvetlenül a Traffic Manager. Ehelyett az ügyfél használ egy rekurzív DNS-szolgáltatás. Egy csak IPv6-alapú ügyfél kérelmeket küld a rekurzív DNS-szolgáltatás IPv6. A rekurzív szolgáltatás majd a Traffic Manager névkiszolgálók használatával az IPv4-alapú csatlakoznia kell.

A TRAFFIC Manager válaszol, a végpont DNS-nevét. IPv6 végpont támogatása érdekében az IPv6-címet a végpont DNS-név mutató DNS AAAA rekord léteznie kell. A TRAFFIC Manager állapotellenőrzést csak támogatja az IPv4-címeket. A szolgáltatásnak kell tenni a DNS-névvel IPv4 végpontja.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Használható együtt ugyanabban a régióban egynél több webalkalmazás Traffic Manager?

Általában a Traffic Manager irányítja a forgalmat különböző régiókban központilag telepített alkalmazások használják. Azonban azt is használható ahol egy alkalmazásnak egynél több központi telepítési ugyanabban a régióban. Egy Azure régióban fel kell venni a ugyanazt a Traffic Manager-profilt a Traffic Manager Azure-végpontok több webalkalmazás-végpont nem teszik lehetővé.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group"></a>Hogyan Azure-végpontok a Traffic Manager-profil áthelyezése egy másik erőforráscsoportban található?

Azure-végpontok Traffic Manager-profil társított követi az erőforrás-azonosítók használatával. Ha egy Azure-erőforrás (például nyilvános IP-cím, klasszikus felhőalapú szolgáltatás, webalkalmazás vagy egy másik Traffic Manager-profil egy beágyazott módon) végpontjaként használt átkerül egy másik erőforráscsoportban található, az erőforrás-azonosító változása. Ebben a forgatókönyvben jelenleg, frissítenie kell a Traffic Manager-profil első törlésével és majd vissza a végpontokat a profilhoz hozzáadni. 

##  <a name="traffic-manager-endpoint-monitoring"></a>A TRAFFIC Manager-végpont figyelése

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Is lehetséges legyen az Azure-régió hibákat Traffic Manager?

A TRAFFIC Manager a magas rendelkezésre állású alkalmazások az Azure-ban kézbesítését nyilvános kulcsokra épülő.
Magas rendelkezésre állás biztosításához, hogy a Traffic Manager kell egy rendkívül magas szintű rendelkezésre állási pedig rugalmasak lehetnek regionális hiba esetén.

Úgy lett kialakítva a Traffic Manager összetevői bármely Azure régióban teljes meghibásodásával rugalmas. A rugalmasság Traffic Manager összes összetevőjének vonatkozik: a DNS-beli név kiszolgálók, az API-t, a tárolási réteg és a szolgáltatás figyelése végpont.

A teljes Azure-régiót kimaradás valószínű esemény a Traffic Manager várhatóan továbbra is megfelelően működnek. A több Azure-régiók telepített alkalmazások támaszkodhat a Traffic Manager át tudja irányítani a forgalmat az alkalmazás elérhető példányhoz.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Hogyan befolyásolja az erőforráscsoport helye megválasztása Traffic Manager?

A TRAFFIC Manager szolgáltatás egyetlen globális szolgáltatás. Nincs regionális. Az erőforráscsoport helye megválasztása mindegy, az erőforráscsoport telepített Traffic Manager-profilok.

Az Azure Resource Manager szükséges összes erőforrás-csoportot, és adjon meg egy helyet, amely megadja, hogy az erőforráscsoport üzembe helyezett erőforrás alapértelmezett helye. Traffic Manager-profil létrehozásakor az erőforráscsoportban jön létre. Traffic Manager-profilokat használja **globális** az helyeként, az erőforrás-csoport alapértelmezett felülbírálása.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Hogyan állapítható meg az egyes végpontok aktuális állapotát?

Minden egyes végpont, a teljes profil mellett az aktuális figyelési állapotát az Azure-portálon jelenik meg. Ez az információ is megtalálható a forgalmat figyelő [REST API](https://msdn.microsoft.com/library/azure/mt163667.aspx), [PowerShell-parancsmagok](https://msdn.microsoft.com/library/mt125941.aspx), és [platformfüggetlen Azure CLI](../cli-install-nodejs.md).

Azure Monitor segítségével nyomon követheti a végpontok állapotát, és tekintse meg a vizuális ábrázolását. Azure-figyelővel kapcsolatban bővebben lásd: a [figyelése Azure dokumentációs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>Figyelheti a HTTPS-végpontnak?

Igen. A TRAFFIC Manager támogatja a HTTPS-KAPCSOLATON keresztül számlálása. Konfigurálása **HTTPS** a figyelési konfiguráció protokollt.

A TRAFFIC manager nem tud biztosítani a leellenőrizni a tanúsítvány, beleértve:

* Kiszolgálóoldali tanúsítványokat a rendszer nem érvényesíti
* Az SNI kiszolgálóoldali tanúsítványok nem támogatottak.
* Ügyfél-tanúsítványok használata nem támogatott

### <a name="i-stopped-an-azure-cloud-service--web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Azure-felhőszolgáltatás leállítása I / webes alkalmazás végpontjának a Traffic Manager-profil, de nem érkezik a forgalom azt szeretnék újraindítása után is. Hogyan lehet elhárításához?

Amikor az Azure felhőalapú szolgáltatás / webes alkalmazás végpontjának állapotának ellenőrzése leállított Traffic Manager leállítja és újraindítja az állapot-ellenőrzési eredményeire, csak azt észleli, hogy a végpont újraindítása után. Ez a késleltetés elkerülése érdekében tiltsa le, és majd újból engedélyezi, hogy a Traffic Manager-profil a végpont-végpont újraindítása után.   

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Használhatom a Traffic Manager, még akkor is, ha az alkalmazás nem rendelkezik támogatja a HTTP vagy HTTPS?

Igen. A figyelési protokollként megadhatja a TCP, és a Traffic Manager TCP-kapcsolatot kezdeményez, és várja meg a végpont válaszára. Ha a végpont válaszol a csatlakozást az időkorláton belül a kapcsolat kérelemre adott végpontra kifogástalan állapotúként jelölése.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Milyen adott válaszok szükségesek a végpontról, ha a TCP-figyelés használatával?

TCP-figyelési használata esetén a Traffic Manager egy háromutas TCP kézfogás SZIN kérelmet küld a megadott port a(z) kezdődik. Majd megvárja, egy adott időn belül (a megadott időtúllépési beállításai) a végpontról választ. Ha a végpont a megadott figyelési beállítások időkorláton belül válaszol a SZIN-Nyugtázási SZIN kérelemre, adott végpontra kifogástalan tekinthető. Ha a szinkronizálás a mi-Nyugtázási választ, a Traffic Manager által vissza egy RST válaszol alaphelyzetbe állítja a kapcsolatot.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Milyen gyors helyezze át a Traffic Manager elhagyja a nem megfelelő végpont a felhasználók?

A TRAFFIC Manager biztosít több beállítások, melyek segíthetnek működését feladatátvételi Traffic Manager-profil a következőképpen:
- megadhatja, hogy a Traffic Manager-vizsgálat a végpontok gyakrabban úgy, hogy a Probing időköz: 10 másodperc. Ez biztosítja, hogy a végpont nem kifogástalan állapotra minél hamarabb észlelte. 
- megadhat mennyi ideig eltelte után a health kérelem alkalommal tekintse meg (minimális időtúllépési érték 5 másodperc).
- megadhatja, hogy hány hiba akkor fordulhat elő, előtt a végpont megfelelő állapotúként van megjelölve. Az érték alacsony, mint 0, amelyben eset a végpont van jelölve, amint az első állapot-ellenőrzés sikertelen lehet. Azonban az a minimális értéke 0 használja a megengedett számú sikertelen vezethet kívül Elforgatás bármely probing időpontjában előforduló átmeneti hibái miatt elérhetetlenné végpontok.
- az idő-live (TTL) is megadhat az engedélyezett minimumérték megegyezik 0 lehet a DNS-választ. Ennek során, úgy, hogy a DNS-feloldókat nem gyorsítótárazza a választ, és minden új lekérdezés lekérdezi a válasz azt jelenti, hogy a legfrissebb állapotinformációkat, amely a Traffic Manager rendelkezik, amely magában foglalja.

Ezekkel a beállításokkal a Traffic Manager biztosíthat a feladatátvételeket a 10 másodperc után a végpont kerül sérült állapotba, és egy DNS-lekérdezés alapján a megfelelő profilt történik.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Hogyan adhat meg egy másik végpontok különböző monitoringbeállításokat?

A TRAFFIC Manager figyelési beállítások erővel egy profil szintenként. Figyelési beállítás használata csak egy végpont van szüksége, ha is elvégezhető, hogy a végpont, mivel egy [profil beágyazott](traffic-manager-nested-profiles.md) amelynek figyelési beállítások eltérnek a szülő profil.

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Milyen host fejléc nincs végpont ellenőrzi használata?

A TRAFFIC Manager állomásfejléc használja a HTTP és HTTPS állapotellenőrzést. A Traffic Manager által használt állomásfejléc esetén a végpont cél a profilban konfigurált. Az értéket, a host fejléc nem adható meg külön-külön a target tulajdonság.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Mik azok az IP-címek, amelyből állapotát ellenőrzi származik?

Kattintson a [Itt](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) megtekintéséhez a JSON-fájl, amely tartalmazza az IP-címek, mely a Traffic Manager állapotellenőrzést is származik. Tekintse át az IP-cím szerepel a JSON-fájl használatával biztosítható, hogy ezek IP-címekről érkező bejövő kapcsolatok engedélyezése a végpontok, az állapotadatok kereséséhez.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Hány állapotellenőrzést a végpontra mutató várhatók a Traffic Manager?

A Traffic Manager-állapot számát ellenőrzi, hogy a végpont elérése függ a következő:
- az értéket, amelyet a figyelési időköz beállítása (kisebb időköz azt jelenti, hogy a végpont a megadott időtartamon üzenetsorokra további kérelmeket).
- Ha állapotát ellenőrzi a helyek számánál (az IP-címeket is várt ezen ellenőrzések szerepel az előző – gyakori kérdések) származnak.

## <a name="traffic-manager-nested-profiles"></a>A TRAFFIC Manager beágyazott profilok

### <a name="how-do-i-configure-nested-profiles"></a>Hogyan konfigurálhatók a beágyazott profilok?

Beágyazott Traffic Manager-profilok úgy konfigurálhatók, az Azure Resource Manager és a klasszikus Azure REST API-k, Azure PowerShell-parancsmagok és platformfüggetlen Azure parancssori felület parancsait. Ezeket az új Azure-portálon is támogatja.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Hány rétegének beágyazási biztosítja Traffic Manager támogatja?

Legfeljebb 10 szintnél mélyebb profilok ágyazhatja be. "A hurokban" nem engedélyezettek.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Kombinálhatom a beágyazott gyermektevékenységének profilokkal, ugyanazt a Traffic Manager-profilt a más típusú végpontok esetében?

Igen. Nincsenek a hogyan kombinálásával egy profilon belül a különböző típusú végpontok korlátozások.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Hogyan felel meg a számlázási modellt a beágyazott profilok?

Nincs nem negatív árképzési hatását a beágyazott profilok használatával.

A TRAFFIC Manager számlázási két részből áll: végpont állapot-ellenőrzési eredményeire és a DNS-lekérdezések több millió

* Végpont állapotellenőrzést: ingyenesek gyermek profil konfigurálásakor szülő profilban végpontjaként. A gyermek profil végpontjainak figyelését a szokásos módon lesz számlázva.
* DNS-lekérdezések: minden egyes lekérdezés csak egyszer akkor számít. A szülő-profilt, amely a végpont ad vissza egy gyermek profil elleni lekérdezés csak a szülő profil elleni számít.

Teljes részletekért lásd: a [árképzést ismertető oldalra Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Van a teljesítményre gyakorolt hatás beágyazott profilok?

Nem. Nincs beágyazott profilok használata során felmerülő teljesítmény hatással.

A Traffic Manager névkiszolgálók a profil hierarchia minden DNS-lekérdezés feldolgozása közben belső haladnak át. A DNS-lekérdezés egy szülő-profilhoz fogadhat, amelynek van végpontja egy DNS-válasz egy gyermek-profilt. CNAME rekord szolgál, függetlenül attól, hogy egyetlen profilhoz vagy beágyazott profilok. Nincs szükség az egyes profilok CNAME rekord létrehozása a hierarchiában.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Hogyan számítja ki a Traffic Manager egy beágyazott végpont egy szülő profil állapotát?

A szülő profil nem ellenőrzi a állapota a gyermek közvetlenül. Ehelyett az alárendelt profil végpontok állapotát rendszer kiszámítja a gyermek profil általános állapotát. Ez az információ az egymásba ágyazott profilelérési hierarchia a beágyazott végpont állapotának mentése propagálja. A szülő-profil ez összesített állapotát annak meghatározásához, hogy a gyermek a forgalom irányítható használ.

A következő táblázat ismerteti a Traffic Manager ellenőrzi egy beágyazott végpont viselkedését.

| Gyermek profil figyelő állapota | Szülőhely végpont-figyelő állapota | Megjegyzések |
| --- | --- | --- |
| Letiltott. A gyermek profil le van tiltva. |Leállítva |A szülő végpont állapota leáll, nincs letiltva. A letiltott állapotát jelzi, hogy le van tiltva a végpont a szülő profil számára van fenntartva. |
| A csökkentett teljesítményt. Legalább egy alárendelt profilvégpontjához csökkentett teljesítményű állapotban van. |Online: az alárendelt profilban Online végpontok számának értéke legalább a MinChildEndpoints.<BR>CheckingEndpoint: a gyermek profil Online plus CheckingEndpoint végpontok számának értéke legalább a MinChildEndpoints.<BR>Csökkentett teljesítményű: más módon. |Az állapot CheckingEndpoint végpont továbbítódik. Ha a MinChildEndpoints túl magasra van állítva, a végpont mindig csökkentett teljesítményt. |
| Online. Legalább egy alárendelt profilvégpontjához Online állapotban. Nincs végpont a csökkentett teljesítményű állapotban van. |Fent látható. | |
| CheckingEndpoints. Legalább egy profil maxcardinality értéke "CheckingEndpoint". Nincsenek végpontok "Online" vagy "Csökkentett teljesítményű" |Ugyanaz, mint a fentiek közül. | |
| Inaktív. Gyermek profil végpontjai, vagy le van tiltva, vagy leállt, vagy ehhez a profilhoz nincsenek végpontok rendelkezik. |Leállítva | |

## <a name="next-steps"></a>Következő lépések:
- A Traffic Manager további [végpont figyelési és automatikus feladatátvételt](../traffic-manager/traffic-manager-monitoring.md).
- A Traffic Manager további [forgalom-útválasztási módszerei](../traffic-manager/traffic-manager-routing-methods.md).
