---
title: A Válaszkeresés projekt referenciája
titlesuffix: Azure Cognitive Services
description: A Project answer keresési végpontra mutató hivatkozás.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: reference
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 28449435479aef0d6a1d8aee3e53de1a78f401b3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396782"
---
# <a name="project-answer-search-v7-reference"></a>A Project answer Search v7-referenciája

A Bing answer SearchAPI egy lekérdezési paramétert ad vissza, és visszaadja a `answerType`: `facts` vagy `entities``searchResponse`. 

A válasz keresési API-t használó alkalmazások a lekérdezési paraméterben lévő előzetes verziójú URL-címmel küldenek kéréseket a végpontnak.  A kérelemnek tartalmaznia kell a `q=searchTerm` paramétert és a *OCP-APIM-Subscription-Key* fejlécet.   

A JSON-válasz elemezhető olyan tényekkel és entitásokkal, amelyek a keresési objektum részleteit tartalmazzák.

## <a name="endpoint"></a>Végpont
A válasz keresési eredményeinek megkereséséhez küldjön egy kérelmet a következő végpontnak. A fejlécek és az URL-paraméterek használatával további specifikációkat határozhat meg.

Végpont lekérése: 
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

```

A kérelemnek a HTTPS protokollt kell használnia, és tartalmaznia kell a következő lekérdezési paramétert:
-  `q=<URL>` – a keresési objektumot azonosító lekérdezés

A kérések elvégzését bemutató Példákért lásd [ C# :](c-sharp-quickstart.md) gyors útmutató vagy [Java](java-quickstart.md)-gyors üzembe helyezés. 

A következő szakaszokban technikai részleteket talál a válaszok objektumairól, a lekérdezési paraméterekről és a keresési eredményeket érintő fejlécekről. 
  
További információ a kérelmeket tartalmazó fejlécekről: [fejlécek](#headers).  
  
További információ a kérelmeket tartalmazó lekérdezési paraméterekről: [lekérdezési paraméterek](#query-parameters).  
  
További információ a válaszban szereplő JSON-objektumokról: [Response Objects](#response-objects).

A lekérdezési URL-cím maximális hossza 2 048 karakter. Annak érdekében, hogy az URL-cím hossza ne haladja meg a korlátot, a lekérdezési paraméterek maximális hosszának 1 500 karakternél rövidebbnek kell lennie. Ha az URL-cím meghaladja az 2 048 karaktert, a kiszolgáló a 404 értéket adja vissza.  

További információ az eredmények használatáról és megjelenítéséről: [használati és megjelenítési követelmények](use-display-requirements.md). 

> [!NOTE]
> Egyes, más keresési API-kra vonatkozó kérelem-fejlécek nem érintik az URL-cím előnézetét
> - Sorpragmákat – a hívó nem szabályozhatja, hogy az URL-előnézet a gyorsítótárat használja-e
> - Cache-Control – a hívó nem szabályozhatja, hogy az URL-előnézet a gyorsítótárat használja-e
> - Felhasználói ügynök
> 
> Bizonyos paraméterek jelenleg nem értelmezhető az URL-cím előnézeti API-hoz, de a jövőben a jobb globalizáció érdekében is felhasználhatók. 
 
## <a name="headers"></a>Fejlécek  
A következő fejlécek lehetnek a kérések és válaszok.  
  
|Fejléc|Leírás|  
|------------|-----------------|  
|Elfogadás|Választható kérelemfejléc.<br /><br /> Az alapértelmezett adathordozó-típus az Application/JSON. Annak megadásához, hogy a válasz [JSON-ld-](https://json-ld.org/)t használ, állítsa az elfogadás fejlécet Application/ld + JSON értékre.|  
|<a name="acceptlanguage" />Accept-Language|Választható kérelemfejléc.<br /><br /> Nyelvek vesszővel elválasztott listája a felhasználói felület sztringjeihez. A lista prioritás szerinti csökkenő sorrendben jelenik meg. További információért, például a várt formátummal kapcsolatos részletekért lásd: [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ez a fejléc és a [setLang](#setlang) lekérdezési paraméter kölcsönösen kizárják egymást, ne adja meg mindkettőt.<br /><br /> Ha beállítja ezt a fejlécet, a CC lekérdezési paramétert is meg kell adnia. A megfelelő piac meghatározásához a Bing a listában talált első támogatott nyelvet használja, és kombinálja azt a `cc` paraméter értékével. Ha a lista nem tartalmaz támogatott nyelvet, a Bing megkeresi a kérelmet támogató legközelebbi nyelvet és piacot, vagy másik lehetőségként egy összesített vagy alapértelmezett piacot használ az eredmények beszerzéséhez. A Bing által használt piac meghatározásához tekintse meg a BingAPIs-Market fejlécet.<br /><br /> Csak akkor használja ezt a fejlécet és a `cc` lekérdezési paramétert, ha több nyelvet ad meg. Ellenkező esetben használja az [mkt](#mkt) és a [setLang](#setlang) lekérdezési paramétereket.<br /><br /> A felhasználóifelület-sztring egy olyan sztring, amelyet feliratként használnak a felhasználói felületen. A JSON-válaszobjektumok tartalmaznak néhány felhasználóifelület-sztringet. A válaszobjektumokban található, a Bing.com tulajdonságaira mutató hivatkozások a megadott nyelvet alkalmazzák.|  
|<a name="market" />BingAPIs-Market|Válaszfejléc.<br /><br /> A kérelem által használt piac. A formátum a következő: \<languageCode\>-\<countryCode\>. Például: en-US.|  
|<a name="traceid" />BingAPIs-TraceId|Válaszfejléc.<br /><br /> A kérelem részleteit tartalmazó naplóbejegyzés azonosítója. Ha hiba történik, rögzítse ezt az azonosítót. Ha nem tudja meghatározni és megoldani a problémát, foglalja bele a kérelembe ezt az azonosítót is a támogatási csoportnak megadott többi információval együtt.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Kötelező kérelemfejléc.<br /><br /> Az előfizetési kulcs, amelyet akkor kapott, amikor feliratkozott a szolgáltatásra a [Cognitive Servicesben](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Választható kérelemfejléc<br /><br /> Alapértelmezés szerint a Bing gyorsítótárazott tartalmat ad vissza, ha van ilyen. Ha nem szeretné, hogy a Bing gyorsítótárazott tartalmat adjon vissza, állítsa a Pragma fejlécet no-cache értékre (például Pragma: no-cache).
|<a name="useragent" />User-Agent|Választható kérelemfejléc.<br /><br /> A kérelmet küldő felhasználói ügynök. A Bing arra használja a felhasználói ügynököt, hogy optimalizált élményt nyújtson a mobilfelhasználóknak. Bár nem kötelező, javasoljuk, hogy mindig adja meg ezt a fejlécet.<br /><br /> A felhasználói ügynöknek a gyakran használt böngészők által küldött sztringgel megegyezőnek kell lennie. A felhasználói ügynökökkel kapcsolatos információért lásd: [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Az alábbiakban példákat láthat a felhasználóiügynök-sztringekre.<br /><ul><li>Windows Phone – Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android – Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone – Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC – Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad – Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Választható kérelem- és válaszfejléc.<br /><br /> A Bing ezt a fejlécet használja ahhoz, hogy következetes viselkedést biztosítson a felhasználók számára a Bing API-hívásaiban. A Bing gyakran tesztel új funkciókat és fejlesztéseket, és az ügyfél-azonosítót használja kulcsként ahhoz, hogy a tesztcsomagokhoz rendelje a forgalmat. Ha a kérelmekben nem ugyanazt az ügyfél-azonosítót használja egy adott felhasználóhoz, előfordulhat, hogy a Bing több ütköző tesztcsomaghoz rendeli hozzá a felhasználót. Az ütköző tesztcsomagok hozzárendelése inkonzisztens felhasználói élményhez vezethet. Például ha a második kérelemhez más tesztcsomag van hozzárendelve, mint az elsőhöz, az váratlan működést eredményezhet. A Bing arra is felhasználhatja az ügyfél-azonosítót, hogy az ügyfél-azonosító keresési előzményeire szabja a webes találatokat, gazdagabb élményt nyújtva a felhasználónak.<br /><br /> A Bing továbbá az ügyfél-azonosító által létrehozott tevékenységek elemzésével az eredmények rangsorolásának javítására is használja a fejlécet. A relevancia javítása segít abban, hogy a Bing API-k jobb minőségű eredményeket biztosítsanak, ami pedig lehetővé teszi a magasabb átkattintási arányt az API fogyasztója számára.<br /><br /> **FONTOS:** Bár nem kötelező, javasoljuk, hogy tekintse annak ezt a fejlécet. Ha ugyanahhoz a végfelhasználóhoz és eszközkombinációhoz több kérelemben is megőrzi az ügyfél-azonosítót, azzal lehetővé teszi, 1) hogy az API fogyasztójának egységes felhasználói élményben legyen része, és 2) magasabb legyen az átkattintási arány a Bing API-k jobb minőségű eredményeinek köszönhetően.<br /><br /> A fejlécre az alábbi alapvető használati szabályok vonatkoznak.<br /><ul><li>Minden felhasználónak, aki használja az eszközön lévő alkalmazást, rendelkeznie kell egy egyedi, Bing által létrehozott ügyfél-azonosítóval.<br /><br/>Ha nem foglalja bele ezt a fejlécet a kérelembe, a Bing létrehoz egy azonosítót, és visszaküldi azt az X-MSEdge-ClientID válaszfejlécben. Ezt a fejlécet csak akkor NEM szabad belefoglalni a kérelembe, amikor a felhasználó először használja az alkalmazást azon az eszközön.<br /><br/></li><li>Használja az ügyfél-azonosítót minden olyan Bing API-kéréshez, amelyet az alkalmazás intéz a felhasználó kapcsán az eszközön.<br /><br/></li><li>**Figyelem:** Győződjön meg arról, hogy ez az ügyfél-azonosító nem a hitelesíthető felhasználói fiókadatok linkable.</li><br/><li>Őrizze meg az ügyfél-azonosítót. Az azonosító böngészőalkalmazásban való megőrzéséhez használjon egy állandó HTTP-cookie-t, amely biztosítja, hogy minden munkamenetben ez az azonosító legyen használva. Ne használjon munkamenet-cookie-t. Más alkalmazások, például a mobilalkalmazások esetében az azonosító megőrzéséhez használja az eszköz állandó tárolóját.<br /><br/>Kérje le a megőrzött ügyfél-azonosítót, amikor a felhasználó ismét használja az alkalmazást az eszközön.</li></ul><br /> **MEGJEGYZÉS:** A Bing-válaszok nem mindig tartalmazzák ezt a fejlécet. Ha a válasz tartalmazza ezt a fejlécet, rögzítse az ügyfél-azonosítót, és használja azt a felhasználó összes további Bing-kérelméhez az adott eszközön.<br /><br /> **MEGJEGYZÉS:** Ha belefoglalja a kérelembe az X-MSEdge-ClientID fejlécet, akkor ne foglaljon bele cookie-kat.|  
|<a name="clientip" />X-MSEdge-ClientIP|Választható kérelemfejléc.<br /><br /> Az ügyféleszköz IPv4- vagy IPv6-címe. Az IP-cím a felhasználó tartózkodási helyének felderítésére szolgál. A Bing arra használja a helyadatokat, hogy meghatározza a biztonságos keresés viselkedését.<br /><br /> **MEGJEGYZÉS:** Bár nem kötelező, javasoljuk, hogy mindig adja meg ezt a fejlécet és az X-Search-Location fejlécet.<br /><br /> Ne rejtse el a címet (például úgy, hogy 0-ra módosítja az utolsó oktettet). Ha elrejti a címet, a tartózkodási hely távol fog esni az eszköz tényleges helyétől, amely ahhoz vezethet, hogy a Bing téves eredményeket fog megadni.|  
|<a name="location" />X-Search-Location|Választható kérelemfejléc.<br /><br /> Kulcs/érték párok pontosvesszővel elválasztott listája, amely leírja az ügyfél földrajzi helyét. A Bing arra használja a helyadatokat, hogy meghatározza a biztonságos keresés viselkedését, illetve releváns helyi tartalmakat adjon vissza. A kulcs/érték párt \<kulcs\>:\<érték\> formátumban adja meg. Az alábbiakban láthatja a felhasználó tartózkodási helyének megadására használt kulcsokat.<br /><br /><ul><li>a lat&mdash;az ügyfél helyének szélességét fokban megadva. A földrajzi szélesség nem lehet -90,0 foknál kisebb és +90,0 foknál nagyobb. A negatív értékek a déli szélességeket, a pozitív értékek pedig az északi szélességeket jelölik.<br /><br /></li><li>hosszú&mdash;az ügyfél helyének hosszúsága fokban megadva. A földrajzi hosszúság nem lehet -180,0 foknál kisebb és +180,0 foknál nagyobb. A negatív értékek a nyugati hosszúságokat, a pozitív értékek pedig a keleti hosszúságokat jelölik.<br /><br /></li><li>újra&mdash; a sugarat méterben, amely meghatározza a koordináták vízszintes pontosságát. Adja át az eszköz helymeghatározási szolgáltatása által visszaadott értéket. A tipikus érték GPS/Wi-Fi esetén 22 m, telefontoronnyal végzett háromszögelés esetén 380 m, fordított IP-keresés esetén pedig 18 000 m.<br /><br /></li><li>a TS&mdash; az UTC UNIX időbélyegét, amikor az ügyfél a helyen volt. (Az UNIX-időbélyeg az 1970. január 1. óta eltelt másodpercek száma).<br /><br /></li><li>head – Választható. Az ügyfél relatív haladási vagy utazási iránya. Az utazás irányt fokban adja meg 0 és 360 között, a tényleges északhoz képest az óramutató járásával megegyező irányban számítva. Csak akkor adja meg ezt a kulcsot, ha az `sp` kulcs értéke nem nulla.<br /><br /></li><li>az SP-&mdash; a horizontális sebesség (sebesség) (méter/másodperc), amelyet az ügyféleszközök utaznak.<br /><br /></li><li>ALT&mdash; az ügyféleszközök magassága méterben.<br /><br /></li><li>are – Választható. A méterben megadott sugár, amely meghatározza a koordináták függőleges pontosságát. A RADIUS alapértelmezett értéke 50 km. Csak akkor adja meg ezt a kulcsot, ha az `alt` kulcsot is megadja.<br /><br /></li></ul> **Megjegyzés:** Habár ezek a kulcsok nem kötelezőek, annál több információt ad meg, annál pontosabb a hely eredményei.<br /><br /> **Megjegyzés:** Javasoljuk, hogy mindig a felhasználó földrajzi helyét határozza meg. Különösen fontos megadni a helyet, ha az ügyfél IP-címe nem tükrözi pontosan a felhasználó fizikai helyét (például ha az ügyfél VPN-t használ). Az optimális eredmény érdekében foglalja bele a kérelembe ezt a fejlécet és az X-MSEdge-ClientIP fejlécet, de legalább ezt a fejlécet.|

> [!NOTE] 
> Ne feledje, hogy a Használati feltételek a fejlécek használatával kapcsolatban is előírják a vonatkozó törvények betartását. Egyes joghatóságokban, például Európában vannak olyan követelmények, amelyek előírják a felhasználói hozzájárulás megszerzését a nyomkövető eszközök felhasználói eszközökön való elhelyezése előtt.
  

## <a name="query-parameters"></a>Lekérdezési paraméterek  
A kérelem tartalmazhat a következő lekérdezési paramétereket. Tekintse meg a szükséges paraméterek oszlopát. A lekérdezési paraméterek kódolásához URL-címet kell megadni.  
  
  
|Name (Név)|Érték|Típus|Kötelező|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|A piac, ahonnan az eredmények származnak. <br /><br />A lehetséges piaci értékek listáját a piaci kódok részben tekintheti meg.<br /><br /> **Megjegyzés:** Az URL-cím előnézeti API jelenleg csak az en-us piacot és nyelvet támogatja.<br /><br />|Sztring|Igen|  
|<a name="query" />q|Az előnézeti URL-cím|Sztring|Igen|  
|<a name="responseformat" />responseFormat|A válaszhoz használandó adathordozó-típus. A következő a lehetséges kis-és nagybetűket megkülönböztető értékek.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Az alapértelmezett érték a JSON. A válasz által tartalmazott JSON-objektumokkal kapcsolatos információkért lásd: [Response Objects](#response-objects).<br /><br />  Ha JsonLd ad meg, a válasz törzse JSON-LD objektumokat tartalmaz, amelyek tartalmazzák a keresési eredményeket. A JSON-LD-vel kapcsolatos információkért lásd: [JSON-ld](https://json-ld.org/).|Sztring|Nem|  
|<a name="safesearch" />safeSearch|Felnőtt tartalmak szűrésére szolgáló szűrő. A következők azok a lehetséges szűrőértékek, amelyek nem különböztetik meg a kis- és nagybetűket.<br /><ul><li>Kikapcsolva&mdash;a weblapokat felnőtt szöveggel, képekkel vagy videókkal.<br /><br/></li><li>Közepes&mdash;a kezdőlapokat felnőtt szöveggel, de nem felnőtt képekkel vagy videókkal.<br /><br/></li><li>A szigorú&mdash;nem adnak vissza weblapokat felnőtt szöveggel, képpel vagy videókkal.</li></ul><br /> Az alapértelmezett érték a Moderate.<br /><br /> **Megjegyzés:** Ha a kérés olyan piacról származik, amelyet a Bing felnőtt házirendje megköveteli, hogy a `safeSearch` szigorú értékre legyen állítva, a Bing figyelmen kívül hagyja a `safeSearch` értéket, és Szigorúat használ.<br/><br/>**MEGJEGYZÉS:** Ha a `site:` lekérdezési operátort használja, előfordulhat, hogy a válasz a `safeSearch` lekérdezési paraméter beállításától függetlenül felnőtteknek szóló tartalmakat fog tartalmazni. Csak akkor használja a `site:` operátort, ha ismeri a webhely tartalmát, és a felnőtteknek szóló tartalmak megjelenítése nem okoz problémát. |Sztring|Nem|  
|<a name="setlang" />setLang|A felhasználói felület sztringjeihez használni kívánt nyelv. A nyelv megadásához használja az ISO 639-1 kétbetűs nyelvkódját. Az angol nyelv nyelvkódja például az EN. Az alapértelmezett érték az EN (angol).<br /><br /> Bár nem kötelező, javasoljuk, hogy mindig adja meg a nyelvet. A `setLang` paramétert általában az `mkt` által meghatározott nyelvre kell állítani, hacsak a felhasználó nem szeretné más nyelven megjeleníteni a felhasználói felület sztringjeit.<br /><br /> Ez a paraméter és az [Accept-Language](#acceptlanguage) fejléc kölcsönösen kizárják egymást, ne adja meg mindkettőt.<br /><br /> A felhasználóifelület-sztring egy olyan sztring, amelyet feliratként használnak a felhasználói felületen. A JSON-válaszobjektumok tartalmaznak néhány felhasználóifelület-sztringet. A válaszobjektumokban található, a Bing.com tulajdonságaira mutató hivatkozások is a megadott nyelvet alkalmazzák.|Sztring|Nem| 


## <a name="response-objects"></a>Válasz objektumok  
A válasz sémája egy [weblap] vagy ErrorResponse, mint a Web Search API. Ha a kérelem meghiúsul, a legfelső szintű objektum a [ErrorResponse](#errorresponse) objektum.


|Objektum|Leírás|  
|------------|-----------------|  
|Weblap|A legfelső szintű JSON-objektum, amely az előzetes verzió attribútumait tartalmazza.|  
|[Fact]|Legfelső szintű JSON-objektum, amely tartalmazza a tényeket.| 
|[Entitások|Az entitás részleteit tartalmazó legfelső szintű JSON-objektum.| 

  
### <a name="error"></a>Hiba  
Meghatározza a hiba előfordulását.  
  
|Elem|Leírás|Típus|  
|-------------|-----------------|----------|  
|<a name="error-code" />kód|A hiba kategóriáját azonosító hibakód. A lehetséges kódok listáját lásd: [hibakódok](#error-codes).|Sztring|  
|<a name="error-message" />üzenet|A hiba leírása.|Sztring|  
|<a name="error-moredetails" />moreDetails|A hibával kapcsolatos további információkat biztosító leírás.|Sztring|  
|<a name="error-parameter" />paraméter|A hibát okozó kérelem lekérdezési paramétere.|Sztring|  
|<a name="error-subcode" />alkód|A hibát azonosító hibakód. Ha például `code` InvalidRequest, `subCode` lehet ParameterInvalid vagy ParameterInvalidValue. |Sztring|  
|<a name="error-value" />érték|A lekérdezési paraméter értéke érvénytelen.|Sztring|  
  

### <a name="errorresponse"></a>ErrorResponse  
Az a legfelső szintű objektum, amelyre a válasz vonatkozik, ha a kérelem meghiúsul.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|_type|Írja be a következőt: hint.|Sztring|  
|hibák <a name="errors" />|Azon hibák listája, amelyek leírják, miért nem sikerült a kérelem végrehajtása.|[Hiba](#error)|  

  
  
### <a name="license"></a>Licenc  
Meghatározza azt a licencet, amely alatt a szöveget vagy a fényképet lehet használni.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|név|A licenc neve.|Sztring|  
|url|Annak a webhelynek az URL-címe, ahol a felhasználó további információkat kaphat a licenccel kapcsolatban.<br /><br /> Hiperhivatkozás létrehozásához használja a nevet és az URL-címet.|Sztring|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
Meghatároz egy szerződéses szabályt a licencek hozzárendeléséhez.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|_type|Egy Type mutató, amely a LicenseAttribution értékre van állítva.|Sztring|  
|engedély|A licenc, amely alatt a tartalom használható.|[Engedély](#license)|  
|licenseNotice|A célként megadott mező mellett megjelenítendő licenc. Például: "szöveg a CC-BY-SA licenc alapján".<br /><br /> A `license` mezőben a licenc nevét és URL-címét használva hozzon létre egy hiperhivatkozást a webhelyre, amely leírja a licenc részleteit. Ezután cserélje le a licenc nevét a `licenseNotice` sztringre (például CC-BY-SA) az imént létrehozott hiperhivatkozásra.|Sztring|  
|mustBeCloseToContent|Logikai érték, amely meghatározza, hogy a szabály tartalmát a szabály hatálya alá eső mező közelében kell-e elhelyezni. Ha az **értéke igaz**, a tartalmat a közeli közelségbe kell helyezni. Ha **false (hamis**) vagy ez a mező nem létezik, akkor a tartalom a hívó saját belátása szerint helyezhető el.|Logikai|  
|targetPropertyName|Annak a mezőnek a neve, amelyre a szabály vonatkozik.|Sztring|  
  

### <a name="link"></a>Hivatkozás  
Meghatározza a hiperhivatkozás összetevőit.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|_type|Írja be a következőt: hint.|Sztring|  
|szöveg|A megjelenített szöveg|Sztring|  
|url|EGY URL-CÍM. Hiperhivatkozás létrehozásához használja az URL-címet és a megjelenítendő szöveget.|Sztring|  
  

### <a name="linkattribution"></a>LinkAttribution  
Egy szerződéses szabályt határoz meg a hivatkozás-hozzárendeléshez.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|_type|Egy Type mutató, amely a LinkAttribution értékre van állítva.|Sztring|  
|mustBeCloseToContent|Logikai érték, amely meghatározza, hogy a szabály tartalmát a szabály hatálya alá eső mező közelében kell-e elhelyezni. Ha az **értéke igaz**, a tartalmat a közeli közelségbe kell helyezni. Ha **false (hamis**) vagy ez a mező nem létezik, akkor a tartalom a hívó saját belátása szerint helyezhető el.|Logikai|  
|targetPropertyName|Annak a mezőnek a neve, amelyre a szabály vonatkozik.<br /><br /> Ha a cél nincs megadva, a hozzárendelés az entitás egészére vonatkozik, és az entitás megjelenítését közvetlenül követően kell megjeleníteni. Ha több szöveges és csatolási hozzárendelési szabály is van, amelyek nem határoznak meg célt, fűzze össze őket, és jelenítse meg azokat a "adatok forrása:" címke használatával. Például: "a < szolgáltató name1\> &#124; < provider name2\>" adatait.|Sztring|  
|szöveg|A jóváírás szövege.|Sztring|  
|url|A szolgáltató webhelyének URL-címe. Hiperhivatkozás létrehozásához használja a `text` és az URL-címet.|Sztring|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Meghatároz egy szerződéses szabályt a média-hozzárendeléshez.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|_type|Egy Type mutató, amely a MediaAttribution értékre van állítva.|Sztring|  
|mustBeCloseToContent|Logikai érték, amely meghatározza, hogy a szabály tartalmát a szabály hatálya alá eső mező közelében kell-e elhelyezni. Ha az **értéke igaz**, a tartalmat a közeli közelségbe kell helyezni. Ha **false (hamis**) vagy ez a mező nem létezik, akkor a tartalom a hívó saját belátása szerint helyezhető el.|Logikai|  
|targetPropertyName|Annak a mezőnek a neve, amelyre a szabály vonatkozik.|Sztring|  
|url|A médiatartalom hiperhivatkozásának létrehozásához használt URL-cím. Ha például a cél egy rendszerkép, az URL-cím használatával kijelölheti a képet.|Sztring|  
  
  
  
### <a name="organization"></a>Szervezet  
Meghatározza a közzétevőt.  
  
Vegye figyelembe, hogy a közzétevő megadhatja a nevét vagy a webhelyét, vagy mindkettőt.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|név|A közzétevő neve.|Sztring|  
|url|A közzétevő webhelyének URL-címe.<br /><br /> Vegye figyelembe, hogy a közzétevő nem rendelkezhet webhellyel.|Sztring|  
  
  

### <a name="webpage"></a>WebPage  
Az előzetes verzióban elérhető weblapokra vonatkozó információkat határozza meg.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|
|név|Az oldal címe, nem feltétlenül a HTML-cím|Sztring|
|url|A ténylegesen bejárt URL-cím (a kérés követte az átirányítást)|Sztring|  
|leírás|Az oldal és a tartalom rövid leírása|Sztring|  
|isFamilyFriendly|A legpontosabb a webes index elemeinél. a valós idejű lekérések ezt az észlelést kizárólag az URL-címen, nem pedig az oldal tartalmán alapulnak.|logikai|
|primaryImageOfPage/contentUrl|Az előzetes verzióban szerepeltetni kívánt reprezentatív kép URL-címe|Sztring| 
  
  
### <a name="querycontext"></a>QueryContext  
Meghatározza azt a lekérdezési környezetet, amelyet a Bing a kérelemhez használt.  
  
|Elem|Leírás|Típus|  
|-------------|-----------------|----------|  
|adultIntent|Logikai érték, amely jelzi, hogy a megadott lekérdezésnek van-e felnőtt szándéka. Az érték **igaz** , ha a lekérdezés felnőtt szándékkal rendelkezik; Ellenkező esetben **hamis**.|Logikai|  
|alterationOverrideQuery|A lekérdezési karakterlánc, amely az eredeti sztring használatára kényszeríti a Bing használatát. Ha például a lekérdezési karakterlánc *saling hátszél*, a felülbírálás lekérdezési karakterlánca a *+ saling hátszél*lesz. Ne felejtse el kódolni a lekérdezési karakterláncot, amely a következőt eredményezi: *% 2Bsaling + hátszél*.<br /><br /> Ez a mező csak akkor szerepel, ha az eredeti lekérdezési karakterlánc helyesírási hibát tartalmaz.|Sztring|  
|alteredQuery|A Bing által a lekérdezés végrehajtásához használt lekérdezési karakterlánc. A Bing a megváltoztatott lekérdezési karakterláncot használja, ha az eredeti lekérdezési karakterlánc helyesírási hibákat foglalt le. Ha például a lekérdezési karakterlánc `saling downwind`, a rendszer a módosított lekérdezési karakterláncot `sailing downwind`.<br /><br /> Ez a mező csak akkor szerepel, ha az eredeti lekérdezési karakterlánc helyesírási hibát tartalmaz.|Sztring|  
|askUserForLocation|Logikai érték, amely azt jelzi, hogy a Bing megköveteli-e a felhasználó tartózkodási helyének pontos eredményének megadását. Ha a felhasználó helyét az [x-MSEdge-ügyfélip](#clientip) és az [x-Search-Location](#location) fejlécek segítségével adta meg, akkor figyelmen kívül hagyhatja ezt a mezőt.<br /><br /> A helyhez kapcsolódó lekérdezések, például a "mai időjárási viszonyok" vagy a "közeli éttermek", amelyeknek szükségük van a felhasználó tartózkodási helyének pontos eredményeinek megadására, ez a mező **igaz**értékre van állítva.<br /><br /> A helyet (például "Seattle Weather") tartalmazó helyhez kapcsolódó lekérdezések esetén ez a mező **hamis**értékre van állítva. Ez a mező **hamis** értékre van állítva olyan lekérdezések esetén is, amelyek nem ismerik fel a helyeket, például a "legjobb értékesítők".|Logikai|  
|originalQuery|A kérelemben megadott lekérdezési karakterlánc.|Sztring|  

### <a name="identifiable"></a>Azonosítható

|Name (Név)|Érték|Típus|  
|-------------|-----------------|----------|
|id|Erőforrás-azonosító|Sztring|
 
### <a name="rankinggroup"></a>RankingGroup
Meghatározza a keresési eredmények csoportot, például a fővonalat.

|Name (Név)|Érték|Típus|  
|-------------|-----------------|----------|
|items|A csoportban megjelenítendő keresési eredmények listája.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Meghatározza a megjelenítendő keresési eredmény-elemeket.

|Name (Név)|Érték|Típus|  
|-------------|-----------------|----------|
|resultIndex|A válaszban megjelenítendő elem nulla alapú indexe. Ha az elem nem tartalmazza ezt a mezőt, a válasz összes elemét megjeleníti. Például megjelenítheti a hírek válaszában szereplő összes újságcikket.|Egész szám|
|answerType|A megjelenítendő tételt tartalmazó válasz. Például: Hírek.<br /><br />A típus használatával keresse meg a választ a SearchResponse objektumban. A típus a SearchResponse mező neve.<br /><br /> A válasz típusát azonban csak akkor használja, ha az objektum tartalmazza az érték mezőt; Ellenkező esetben hagyja figyelmen kívül.|Sztring|
|textualIndex|A textualAnswers megjelenő válasz indexe.| Előjel nélküli egész szám|
|érték|Az az azonosító, amely a megjelenítendő választ vagy egy válasz elemét azonosítja. Ha az azonosító egy választ azonosít, a válasz összes elemét megjeleníti.|Azonosítható|

### <a name="rankingresponse"></a>RankingResponse  
Meghatározza, hogy a keresési eredmények oldalának hol kell lennie, és milyen sorrendben kell elhelyezni a tartalmat.  
  
|Name (Név)|Érték|  
|----------|-----------|  
|<a name="ranking-mainline" />fővonal|A keresési eredmények megjelennek a fővonalon.|  
|<a name="ranking-pole" />pole|A keresési eredményeknek meg kell adni a legjobban látható kezelést (például a fővonalon és az oldalsávon).|  
|oldalsáv <a name="ranking-sidebar" />|Az oldalsávon megjelenítendő keresési eredmények.| 


### <a name="searchresponse"></a>SearchResponse  
Meghatározza azt a legfelső szintű objektumot, amelyet a válasz tartalmaz, amikor a kérelem sikeres.  
  
Vegye figyelembe, hogy ha a szolgáltatás egy szolgáltatásmegtagadási támadást feltételez, a kérelem sikeres lesz (a HTTP-állapotkód 200 OK); a válasz törzse azonban üres lesz.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|_type|Írja be a következőt: SearchResponse.|Sztring|  
|WebPage|Az előnézetet definiáló JSON-objektum|sztring|  
  
  
### <a name="textattribution"></a>TextAttribution  
Meghatároz egy egyszerű szöveges hozzárendelésre vonatkozó szerződési szabályt.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|_type|Egy Type mutató, amely a TextAttribution értékre van állítva.|Sztring|  
|szöveg|A jóváírás szövege.<br /><br /> A szöveges hozzárendelés az entitás egészére vonatkozik, és az entitások megjelenítését követően azonnal megjelenik. Ha több szöveges vagy hivatkozáshoz tartozó olyan szabály van, amely nem határoz meg célt, fűzze össze őket, és jelenítse meg őket "adatok forrása:" címkével.|Sztring| 


## <a name="error-codes"></a>Hibakódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők:  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|200|Sikeres.|  
|400|A lekérdezési paraméterek egyike hiányzik vagy érvénytelen.|  
|401|Az előfizetési kulcs hiányzik vagy érvénytelen.|  
|403|A felhasználó hitelesítése megtörtént (például egy érvényes előfizetési kulcsot használt), de nincs engedélye a kért erőforráshoz.<br /><br /> A Bing akkor is visszaállíthatja ezt az állapotot, ha a hívó havi kvótán túllépte a lekérdezéseket.|  
|410|A kérelem HTTP-t használ a HTTPS protokoll helyett. A HTTPS az egyetlen támogatott protokoll.|  
|429|A hívó meghaladta a lekérdezések másodpercenkénti számát.|  
|500|Váratlan kiszolgálóhiba történt.|

Ha a kérelem sikertelen, a válasz egy [ErrorResponse](#errorresponse) objektumot tartalmaz, amely a hibát okozó [hibák](#error) listáját tartalmazza. Ha a hiba egy paraméterhez kapcsolódik, a `parameter` mező azonosítja a hibát megadó paramétert. Ha a hiba egy paraméter értékhez kapcsolódik, a `value` mező az érvénytelen értéket azonosítja.

```json
{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidRequest", 
      "subCode": "ParameterMissing", 
      "message": "Required parameter is missing.", 
      "parameter": "q" 
    }
  ]
}

{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidAuthorization", 
      "subCode": "AuthorizationMissing", 
      "message": "Authorization is required.", 
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

A lehetséges hibakód és az alhibakódok értéke a következő:

|Kód|SubCode|Leírás
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Nincs implementálva|A HTTP-állapotkód 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokkolt|A Bing visszaadja a InvalidRequest, ha a kérelem bármely része érvénytelen. Például hiányzik egy kötelező paraméter, vagy a paraméter értéke érvénytelen.<br/><br/>Ha a hiba ParameterMissing vagy ParameterInvalidValue, a HTTP-állapotkód 400.<br/><br/>Ha HTTPS helyett HTTP protokollt használ, a Bing visszaadja a HttpNotAllowed, a HTTP-állapotkód pedig 410.
|RateLimitExceeded|Nincsenek alkódok|A Bing visszaadja a RateLimitExceeded, amikor a másodpercenkénti lekérdezések (QPS) vagy a havi lekérdezés (QPM) kvóta meghaladja a kvótát.<br/><br/>Ha túllépi a QPS, a Bing a 429-as HTTP-állapotkódot adja vissza, és ha túllépi a QPM, a Bing visszaadja a 403 értéket.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|A Bing visszaadja a InvalidAuthorization, ha a Bing nem tudja hitelesíteni a hívót. A `Ocp-Apim-Subscription-Key` fejléc például hiányzik, vagy az előfizetési kulcs érvénytelen.<br/><br/>A redundancia akkor fordul elő, ha egynél több hitelesítési módszert ad meg.<br/><br/>Ha a hiba InvalidAuthorization, a HTTP-állapotkód 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|A Bing visszaadja a InsufficientAuthorization, ha a hívónak nincs engedélye az erőforrás elérésére. Ez akkor fordulhat elő, ha az előfizetési kulcs le van tiltva vagy lejárt. <br/><br/>Ha a hiba InsufficientAuthorization, a HTTP-állapotkód 403.

## <a name="next-steps"></a>Következő lépések
- [C# – rövid útmutató](c-sharp-quickstart.md)
- [Java – rövid útmutató](java-quickstart.md)
- [Node – rövid útmutató](node-quickstart.md)
- [Python – rövid útmutató](python-quickstart.md)

