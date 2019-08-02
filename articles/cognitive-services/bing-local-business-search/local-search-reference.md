---
title: A Bing helyi üzleti keresési API v7-dokumentációja
titleSuffix: Azure Cognitive Services
description: Ismerteti a Bing helyi üzleti keresési API programozási elemeit.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 36d87ee9db68c47ee96519cb6b04ff24478427d6
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423404"
---
# <a name="bing-local-business-search-api-v7-reference"></a>A Bing helyi üzleti keresési API v7-dokumentációja

A helyi üzleti keresési API keresési lekérdezést küld a Bingnek, így olyan eredményeket érhet el, amelyek éttermeket, szállodákat vagy más helyi vállalkozásokat foglalnak magukban. Helyek esetén a lekérdezés megadhatja a helyi vállalat nevét vagy kategóriáját (például éttermek közelében). Az entitástalálatok személyek, helyek vagy dolgok lehetnek. Ebben a kontextusban az üzleti entitások, Államok, országok/régiók stb. is helyette.  

Ez a szakasz technikai részleteket tartalmaz a válasz objektumokról, valamint a keresési eredményeket érintő lekérdezési paraméterekről és fejlécekről. A kérések elvégzését bemutató példákat a [helyi üzleti keresés C# ](quickstarts/local-quickstart.md) rövid útmutatója vagy a [helyi üzleti keresés Java](quickstarts/local-search-java-quickstart.md)rövid útmutatója című témakörben talál. 
  
További információ a kérelmeket tartalmazó fejlécekről: [fejlécek](#headers).  
  
További információ a kérelmeket tartalmazó lekérdezési paraméterekről: [lekérdezési paraméterek](#query-parameters).  
  
További információ a válaszban szereplő JSON-objektumokról: [Response Objects](#response-objects).

További információ az eredmények használatáról és megjelenítéséről: [használati és megjelenítési követelmények](use-display-requirements.md).


  
## <a name="endpoint"></a>Végpont  
Ha helyi üzleti eredményeket szeretne kérni, küldjön egy GET-kérést a következőre: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
A kérelemnek a HTTPS protokollt kell használnia.  
  
> [!NOTE]
> Az URL-cím maximális hossza 2 048 karakter. Annak érdekében, hogy az URL-cím hossza ne haladja meg a korlátot, a lekérdezési paraméterek maximális hosszának 1 500 karakternél rövidebbnek kell lennie. Ha az URL-cím meghaladja az 2 048 karaktert, a kiszolgáló a 404 értéket adja vissza.  
  
  
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
|<a name="clientid" />X-MSEdge-ClientID|Választható kérelem- és válaszfejléc.<br /><br /> A Bing ezt a fejlécet használja ahhoz, hogy következetes viselkedést biztosítson a felhasználók számára a Bing API-hívásaiban. A Bing gyakran tesztel új funkciókat és fejlesztéseket, és az ügyfél-azonosítót használja kulcsként ahhoz, hogy a tesztcsomagokhoz rendelje a forgalmat. Ha a kérelmekben nem ugyanazt az ügyfél-azonosítót használja egy adott felhasználóhoz, előfordulhat, hogy a Bing több ütköző tesztcsomaghoz rendeli hozzá a felhasználót. Az ütköző tesztcsomagok hozzárendelése inkonzisztens felhasználói élményhez vezethet. Például ha a második kérelemhez más tesztcsomag van hozzárendelve, mint az elsőhöz, az váratlan működést eredményezhet. A Bing arra is felhasználhatja az ügyfél-azonosítót, hogy az ügyfél-azonosító keresési előzményeire szabja a webes találatokat, gazdagabb élményt nyújtva a felhasználónak.<br /><br /> A Bing továbbá az ügyfél-azonosító által létrehozott tevékenységek elemzésével az eredmények rangsorolásának javítására is használja a fejlécet. A relevancia javítása segít abban, hogy a Bing API-k jobb minőségű eredményeket biztosítsanak, ami pedig lehetővé teszi a magasabb átkattintási arányt az API fogyasztója számára.<br /><br /> **FONTOS** Bár nem kötelező, érdemes figyelembe vennie ezt a fejlécet. Ha ugyanahhoz a végfelhasználóhoz és eszközkombinációhoz több kérelemben is megőrzi az ügyfél-azonosítót, azzal lehetővé teszi, 1) hogy az API fogyasztójának egységes felhasználói élményben legyen része, és 2) magasabb legyen az átkattintási arány a Bing API-k jobb minőségű eredményeinek köszönhetően.<br /><br /> A fejlécre az alábbi alapvető használati szabályok vonatkoznak.<br /><ul><li>Minden felhasználónak, aki használja az eszközön lévő alkalmazást, rendelkeznie kell egy egyedi, Bing által létrehozott ügyfél-azonosítóval.<br /><br/>Ha nem foglalja bele ezt a fejlécet a kérelembe, a Bing létrehoz egy azonosítót, és visszaküldi azt az X-MSEdge-ClientID válaszfejlécben. Ezt a fejlécet csak akkor NEM szabad belefoglalni a kérelembe, amikor a felhasználó először használja az alkalmazást azon az eszközön.<br /><br/></li><li>Használja az ügyfél-azonosítót minden olyan Bing API-kéréshez, amelyet az alkalmazás intéz a felhasználó kapcsán az eszközön.<br /><br/></li><li>**FIGYELMET** Győződjön meg arról, hogy ez az ügyfél-azonosító nem a hitelesíthető felhasználói fiókadatok linkable.</li><br/><li>Őrizze meg az ügyfél-azonosítót. Az azonosító böngészőalkalmazásban való megőrzéséhez használjon egy állandó HTTP-cookie-t, amely biztosítja, hogy minden munkamenetben ez az azonosító legyen használva. Ne használjon munkamenet-cookie-t. Más alkalmazások, például a mobilalkalmazások esetében az azonosító megőrzéséhez használja az eszköz állandó tárolóját.<br /><br/>Kérje le a megőrzött ügyfél-azonosítót, amikor a felhasználó ismét használja az alkalmazást az eszközön.</li></ul><br /> **MEGJEGYZÉS:** A Bing-válaszok esetleg nem tartalmazzák ezt a fejlécet. Ha a válasz tartalmazza ezt a fejlécet, rögzítse az ügyfél-azonosítót, és használja azt a felhasználó összes további Bing-kérelméhez az adott eszközön.<br /><br /> **MEGJEGYZÉS:** Ha belefoglalja az X-MSEdge-ClientID, akkor nem tartalmazhat cookie-kat a kérelemben.|  
|<a name="clientip" />X-MSEdge-ClientIP|Választható kérelemfejléc.<br /><br /> Az ügyféleszköz IPv4- vagy IPv6-címe. Az IP-cím a felhasználó tartózkodási helyének felderítésére szolgál. A Bing arra használja a helyadatokat, hogy meghatározza a biztonságos keresés viselkedését.<br /><br /> **MEGJEGYZÉS:** Bár nem kötelező, javasoljuk, hogy mindig ezt a fejlécet és az X-Search-Location fejlécet határozza meg.<br /><br /> Ne rejtse el a címet (például úgy, hogy 0-ra módosítja az utolsó oktettet). Ha elrejti a címet, a tartózkodási hely távol fog esni az eszköz tényleges helyétől, amely ahhoz vezethet, hogy a Bing téves eredményeket fog megadni.|  
|<a name="location" />X-Search-Location|Választható kérelemfejléc.<br /><br /> Kulcs/érték párok pontosvesszővel elválasztott listája, amely leírja az ügyfél földrajzi helyét. A Bing arra használja a helyadatokat, hogy meghatározza a biztonságos keresés viselkedését, illetve releváns helyi tartalmakat adjon vissza. A kulcs/érték párt \<kulcs\>:\<érték\> formátumban adja meg. Az alábbiakban láthatja a felhasználó tartózkodási helyének megadására használt kulcsokat.<br /><br /><ul><li>&mdash;az ügyfél helyének szélessége, fokban megadva. A földrajzi szélesség nem lehet -90,0 foknál kisebb és +90,0 foknál nagyobb. A negatív értékek a déli szélességeket, a pozitív értékek pedig az északi szélességeket jelölik.<br /><br /></li><li>&mdash;az ügyfél helyének hosszúsága fokban megadva. A földrajzi hosszúság nem lehet -180,0 foknál kisebb és +180,0 foknál nagyobb. A negatív értékek a nyugati hosszúságokat, a pozitív értékek pedig a keleti hosszúságokat jelölik.<br /><br /></li><li>adja&mdash; újra a Sugárt méterben, amely meghatározza a koordináták vízszintes pontosságát. Adja át az eszköz helymeghatározási szolgáltatása által visszaadott értéket. A tipikus érték GPS/Wi-Fi esetén 22 m, telefontoronnyal végzett háromszögelés esetén 380 m, fordított IP-keresés esetén pedig 18 000 m.<br /><br /></li><li>TS&mdash; az UTC UNIX időbélyege, amikor az ügyfél a helyen volt. (Az UNIX-időbélyeg az 1970. január 1. óta eltelt másodpercek száma).<br /><br /></li><li>head – Választható. Az ügyfél relatív haladási vagy utazási iránya. Az utazás irányt fokban adja meg 0 és 360 között, a tényleges északhoz képest az óramutató járásával megegyező irányban számítva. Csak akkor adja meg ezt a kulcsot, ha az `sp` kulcs értéke nem nulla.<br /><br /></li><li>SP&mdash; a vízszintes sebesség (sebesség) (méter/másodperc), amelyet az ügyféleszközök utaznak.<br /><br /></li><li>&mdash; az ügyfél-eszköz magassága méterben.<br /><br /></li><li>are – Választható. A méterben megadott sugár, amely meghatározza a koordináták függőleges pontosságát. A RADIUS alapértelmezett értéke 50 km. Csak akkor adja meg ezt a kulcsot, ha az `alt` kulcsot is megadja.<br /><br /></li></ul> **MEGJEGYZÉS:** Habár ezek a kulcsok nem kötelezőek, annál több információt ad meg, annál pontosabb a hely eredményei.<br /><br /> **MEGJEGYZÉS:** Javasoljuk, hogy mindig a felhasználó földrajzi helyét határozza meg. Különösen fontos megadni a helyet, ha az ügyfél IP-címe nem tükrözi pontosan a felhasználó fizikai helyét (például ha az ügyfél VPN-t használ). Az optimális eredmény érdekében foglalja bele a kérelembe ezt a fejlécet és az X-MSEdge-ClientIP fejlécet, de legalább ezt a fejlécet.|

> [!NOTE] 
> Ne feledje, hogy a Használati feltételek a fejlécek használatával kapcsolatban is előírják a vonatkozó törvények betartását. Egyes joghatóságokban, például Európában vannak olyan követelmények, amelyek előírják a felhasználói hozzájárulás megszerzését a nyomkövető eszközök felhasználói eszközökön való elhelyezése előtt.
  

## <a name="query-parameters"></a>Lekérdezési paraméterek  
A kérelem tartalmazhat a következő lekérdezési paramétereket. Tekintse meg a szükséges paraméterek oszlopát. A lekérdezési paraméterek kódolásához URL-címet kell megadni.  
  
  
|Name (Név)|Value|Type|Kötelező|  
|----------|-----------|----------|--------------|
|<a name="count" />száma|A visszaadni kívánt eredmények száma, a `offset` paraméterben megadott indextől kezdődően.|Sztring|Nem|   
|<a name="localCategories" />localCategories|A keresés üzleti kategóriában definiált beállítások listája.  Lásd: [helyi üzleti kategóriák keresése](local-categories.md)|Sztring|Nem|  
|<a name="mkt" />mkt|A piac, ahonnan az eredmények származnak. <br /><br />A lehetséges piaci értékek listáját a piaci kódok részben tekintheti meg.<br /><br /> **MEGJEGYZÉS:** A helyi üzleti keresési API jelenleg csak az en-us piacot és nyelvet támogatja.<br /><br />|Sztring|Igen|
|<a name="offset"/>eltolás|A `count` paraméter által megadott eredmények elindítására szolgáló index.|Egész szám|Nem|  
|<a name="query" />q|A felhasználó keresési kifejezése.|Sztring|Nem|  
|<a name="responseformat" />responseFormat|A válaszhoz használandó adathordozó-típus. A következő a lehetséges kis-és nagybetűket megkülönböztető értékek.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Az alapértelmezett érték a JSON. A válasz által tartalmazott JSON-objektumokkal kapcsolatos információkért lásd: [Response Objects](#response-objects).<br /><br />  Ha JsonLd ad meg, a válasz törzse JSON-LD objektumokat tartalmaz, amelyek tartalmazzák a keresési eredményeket. A JSON-LD-vel kapcsolatos információkért lásd: [JSON-ld](https://json-ld.org/).|Karakterlánc|Nem|  
|<a name="safesearch" />safeSearch|Felnőtt tartalmak szűrésére szolgáló szűrő. A következők azok a lehetséges szűrőértékek, amelyek nem különböztetik meg a kis- és nagybetűket.<br /><ul><li>A&mdash;weblapok visszaküldése felnőtt szöveggel, képekkel vagy videókkal.<br /><br/></li><li>Közepes&mdash;hozamú weblapok felnőtt szöveggel, de nem felnőtt képekkel vagy videókkal.<br /><br/></li><li>A&mdash;szigorú nem ad vissza weblapokat felnőtt szöveggel, képpel vagy videókkal.</li></ul><br /> Az alapértelmezett érték a Moderate.<br /><br /> **MEGJEGYZÉS:** Ha a kérés olyan piacról származik, amelyet a Bing felnőtt házirendje `safeSearch` szigorú értékre van állítva, a Bing figyelmen `safeSearch` kívül hagyja az értéket, és szigorú értéket használ.<br/><br/>**MEGJEGYZÉS:** Ha a `site:` lekérdezési operátort használja, akkor előfordulhat, hogy a válasz felnőtt tartalmat is tartalmaz, függetlenül attól, hogy `safeSearch` a lekérdezési paraméter melyik értékre van beállítva. Csak akkor használja a `site:` operátort, ha ismeri a webhely tartalmát, és a felnőtteknek szóló tartalmak megjelenítése nem okoz problémát. |Sztring|Nem|  
|<a name="setlang" />setLang|A felhasználói felület sztringjeihez használni kívánt nyelv. A nyelv megadásához használja az ISO 639-1 kétbetűs nyelvkódját. Az angol nyelv nyelvkódja például az EN. Az alapértelmezett érték az EN (angol).<br /><br /> Bár nem kötelező, javasoljuk, hogy mindig adja meg a nyelvet. A `setLang` paramétert általában az `mkt` által meghatározott nyelvre kell állítani, hacsak a felhasználó nem szeretné más nyelven megjeleníteni a felhasználói felület sztringjeit.<br /><br /> Ez a paraméter és az [Accept-Language](#acceptlanguage) fejléc kölcsönösen kizárják egymást, ne adja meg mindkettőt.<br /><br /> A felhasználóifelület-sztring egy olyan sztring, amelyet feliratként használnak a felhasználói felületen. A JSON-válaszobjektumok tartalmaznak néhány felhasználóifelület-sztringet. A válaszobjektumokban található, a Bing.com tulajdonságaira mutató hivatkozások is a megadott nyelvet alkalmazzák.|Sztring|Nem| 


## <a name="response-objects"></a>Válasz objektumok  
Az alábbi JSON-válaszok objektumai lehetnek a válaszban. Ha a kérelem sikeres, a válasz legfelső szintű objektuma a [SearchResponse](#searchresponse) objektum. Ha a kérelem meghiúsul, a legfelső szintű objektum a [ErrorResponse](#errorresponse) objektum.


|Object|Leírás|  
|------------|-----------------|  
|[Helyben](#place)|A helyi üzleti adatok, például egy étterem vagy egy Hotel adatainak meghatározása.|  

  
### <a name="error"></a>Hiba  
Meghatározza a hiba előfordulását.  
  
|Elem|Leírás|Type|  
|-------------|-----------------|----------|  
|<a name="error-code" />kód|A hiba kategóriáját azonosító hibakód. A lehetséges kódok listáját lásd: hibakódok [](#error-codes).|Sztring|  
|<a name="error-message" />üzenetet|A hiba leírása.|Karakterlánc|  
|<a name="error-moredetails" />moreDetails|A hibával kapcsolatos további információkat biztosító leírás.|Sztring|  
|<a name="error-parameter" />paraméter|A hibát okozó kérelem lekérdezési paramétere.|Karakterlánc|  
|<a name="error-subcode" />subCode|A hibát azonosító hibakód. Ha `code` például a InvalidRequest, `subCode` ParameterInvalid vagy ParameterInvalidValue lehet. |Sztring|  
|<a name="error-value" />érték|A lekérdezési paraméter értéke érvénytelen.|Karakterlánc|  
  

### <a name="errorresponse"></a>ErrorResponse  
Az a legfelső szintű objektum, amelyre a válasz vonatkozik, ha a kérelem meghiúsul.  
  
|Name (Név)|Érték|Type|  
|----------|-----------|----------|  
|_type|Írja be a következőt: hint.|Karakterlánc|  
|<a name="errors" />hibák|Azon hibák listája, amelyek leírják, miért nem sikerült a kérelem végrehajtása.|[Hiba](#error) []|  

  
  
### <a name="license"></a>Licenc  
Meghatározza azt a licencet, amely alatt a szöveget vagy a fényképet lehet használni.  
  
|Name (Név)|Érték|Type|  
|----------|-----------|----------|  
|name|A licenc neve.|Sztring|  
|url|Annak a webhelynek az URL-címe, ahol a felhasználó további információkat kaphat a licenccel kapcsolatban.<br /><br /> Hiperhivatkozás létrehozásához használja a nevet és az URL-címet.|Sztring|  


### <a name="link"></a>Összekapcsolás  
Meghatározza a hiperhivatkozás összetevőit.  
  
|Name (Név)|Érték|Type|  
|----------|-----------|----------|  
|_type|Írja be a következőt: hint.|Sztring|  
|text|A megjelenített szöveg|Karakterlánc|  
|url|EGY URL-CÍM. Hiperhivatkozás létrehozásához használja az URL-címet és a megjelenítendő szöveget.|Karakterlánc|  
  


  
### <a name="organization"></a>Szervezet  
Meghatározza a közzétevőt.  
  
Vegye figyelembe, hogy a közzétevő megadhatja a nevét vagy a webhelyét, vagy mindkettőt.  
  
|Name (Név)|Érték|Type|  
|----------|-----------|----------|  
|name|A közzétevő neve.|Sztring|  
|url|A közzétevő webhelyének URL-címe.<br /><br /> Vegye figyelembe, hogy a közzétevő nem rendelkezhet webhellyel.|Karakterlánc|  
  
  

### <a name="place"></a>Hely  
Meghatározza a helyi vállalkozások adatait, például egy éttermet vagy egy szállodát.  
  
|Name (Név)|Value|Type|  
|----------|-----------|----------|  
|_type|Írja be a következőt: hint, amely a következők egyikére állítható be:<br /><br /><ul><li>Szálloda</li><li>LocalBusiness<br /></li><li>Étterem</ul><li>|Sztring|  
|cím|Az a levelezési cím, ahol az entitás található.|PostalAddress|  
|entityPresentationInfo|További információ az entitás típusának meghatározásához használható entitásról (például a célzásokról). Például, hogy egy étterem vagy egy Hotel. A `entityScenario` mező értéke listaelem.|EntityPresentationInfo|  
|name|Az entitás neve.|Sztring|  
|telefonon|Az entitás telefonszáma.|Karakterlánc|  
|url|Az entitás webhelyének URL-címe.<br /><br /> Ezt az URL-címet és az entitás nevét használva hozzon létre egy hiperhivatkozást, amely rákattintáskor a felhasználó az entitás webhelyére kerül.|Sztring|  
|webSearchUrl|A Bing keresési eredményének URL-címe erre a helyre vonatkozóan.|Karakterlánc| 
  
  
### <a name="querycontext"></a>QueryContext  
Meghatározza azt a lekérdezési környezetet, amelyet a Bing a kérelemhez használt.  
  
|Elem|Leírás|Type|  
|-------------|-----------------|----------|  
|adultIntent|Logikai érték, amely jelzi, hogy a megadott lekérdezésnek van-e felnőtt szándéka. Az érték **igaz** , ha a lekérdezés felnőtt szándékkal rendelkezik; Ellenkező esetben **hamis**.|Logikai|  
|alterationOverrideQuery|A lekérdezési karakterlánc, amely az eredeti sztring használatára kényszeríti a Bing használatát. Ha például a lekérdezési karakterlánc *saling hátszél*, a felülbírálás lekérdezési karakterlánca a *+ saling hátszél*lesz. Ne felejtse el kódolni a lekérdezési karakterláncot, amely a következőt eredményezi: *% 2Bsaling + hátszél*.<br /><br /> Ez a mező csak akkor szerepel, ha az eredeti lekérdezési karakterlánc helyesírási hibát tartalmaz.|Karakterlánc|  
|alteredQuery|A Bing által a lekérdezés végrehajtásához használt lekérdezési karakterlánc. A Bing a megváltoztatott lekérdezési karakterláncot használja, ha az eredeti lekérdezési karakterlánc helyesírási hibákat foglalt le. Ha például a lekérdezési karakterlánc `saling downwind`, a megváltoztatott lekérdezési karakterlánc `sailing downwind`lesz.<br /><br /> Ez a mező csak akkor szerepel, ha az eredeti lekérdezési karakterlánc helyesírási hibát tartalmaz.|Karakterlánc|  
|askUserForLocation|Logikai érték, amely azt jelzi, hogy a Bing megköveteli-e a felhasználó tartózkodási helyének pontos eredményének megadását. Ha a felhasználó helyét az [x-MSEdge-ügyfélip](#clientip) és az [x-Search-Location](#location) fejlécek segítségével adta meg, akkor figyelmen kívül hagyhatja ezt a mezőt.<br /><br /> A helyhez kapcsolódó lekérdezések, például a "mai időjárási viszonyok" vagy a "közeli éttermek", amelyeknek szükségük van a felhasználó tartózkodási helyének pontos eredményeinek megadására, ez a mező **igaz**értékre van állítva.<br /><br /> A helyet (például "Seattle Weather") tartalmazó helyhez kapcsolódó lekérdezések esetén ez a mező **hamis**értékre van állítva. Ez a mező **hamis** értékre van állítva olyan lekérdezések esetén is, amelyek nem ismerik fel a helyeket, például a "legjobb értékesítők".|Logikai|  
|originalQuery|A kérelemben megadott lekérdezési karakterlánc.|Sztring|  

### <a name="identifiable"></a>Azonosítható

|Name (Név)|Érték|Type|  
|-------------|-----------------|----------|
|id|Erőforrás-azonosító|Karakterlánc|
 
### <a name="rankinggroup"></a>RankingGroup
Meghatározza a keresési eredmények csoportot, például a fővonalat.

|Name (Név)|Value|Type|  
|-------------|-----------------|----------|
|items|A csoportban megjelenítendő keresési eredmények listája.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Meghatározza a megjelenítendő keresési eredmény-elemeket.

|Name (Név)|Value|Type|  
|-------------|-----------------|----------|
|resultIndex|A válaszban megjelenítendő elem nulla alapú indexe. Ha az elem nem tartalmazza ezt a mezőt, a válasz összes elemét megjeleníti. Például megjelenítheti a hírek válaszában szereplő összes újságcikket.|Egész szám|
|answerType|A megjelenítendő tételt tartalmazó válasz. Például: Hírek.<br /><br />A típus használatával keresse meg a választ a SearchResponse objektumban. A típus a SearchResponse mező neve.<br /><br /> A válasz típusát azonban csak akkor használja, ha az objektum tartalmazza az érték mezőt; Ellenkező esetben hagyja figyelmen kívül.|Sztring|
|textualIndex|A textualAnswers megjelenő válasz indexe.| Előjel nélküli egész szám|
|value|Az az azonosító, amely a megjelenítendő választ vagy egy válasz elemét azonosítja. Ha az azonosító egy választ azonosít, a válasz összes elemét megjeleníti.|Azonosítható|

### <a name="rankingresponse"></a>RankingResponse  
Meghatározza, hogy a keresési eredmények oldalának hol kell lennie, és milyen sorrendben kell elhelyezni a tartalmat.  
  
|Name (Név)|Value|  
|----------|-----------|  
|<a name="ranking-mainline" />Mainline|A keresési eredmények megjelennek a fővonalon.|  
|<a name="ranking-pole" />sark|A keresési eredményeknek meg kell adni a legjobban látható kezelést (például a fővonalon és az oldalsávon).|  
|<a name="ranking-sidebar" />oldalsáv|Az oldalsávon megjelenítendő keresési eredmények.| 

### <a name="searchresponse"></a>SearchResponse  
Meghatározza azt a legfelső szintű objektumot, amelyet a válasz tartalmaz, amikor a kérelem sikeres.  
  
Vegye figyelembe, hogy ha a szolgáltatás egy szolgáltatásmegtagadási támadást feltételez, a kérelem sikeres lesz (a HTTP-állapotkód 200 OK); a válasz törzse azonban üres lesz.  
  
|Name (Név)|Érték|Type|  
|----------|-----------|----------|  
|_type|Írja be a következőt: SearchResponse.|Sztring|  
|Helyek|A keresési lekérdezéshez kapcsolódó entitások listája.|JSON-objektum|  
|queryContext|Egy objektum, amely a kérelemhez használt Bing lekérdezési karakterláncot tartalmazza.<br /><br /> Ez az objektum tartalmazza a felhasználó által megadott lekérdezési karakterláncot. Tartalmazhat egy olyan megváltoztatott lekérdezési karakterláncot is, amely a lekérdezéshez használt Bing, ha a lekérdezési karakterlánc helyesírási hibát tartalmazott.|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>Hibakódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők:  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|200|Sikeres művelet.|  
|400|A lekérdezési paraméterek egyike hiányzik vagy érvénytelen.|  
|401|Az előfizetési kulcs hiányzik vagy érvénytelen.|  
|403|A felhasználó hitelesítése megtörtént (például egy érvényes előfizetési kulcsot használt), de nincs engedélye a kért erőforráshoz.<br /><br /> A Bing akkor is visszaállíthatja ezt az állapotot, ha a hívó havi kvótán túllépte a lekérdezéseket.|  
|410|A kérelem HTTP-t használ a HTTPS protokoll helyett. A HTTPS az egyetlen támogatott protokoll.|  
|429|A hívó meghaladta a lekérdezések másodpercenkénti számát.|  
|500|Váratlan kiszolgálóhiba történt.|

Ha a kérelem sikertelen, a válasz egy [ErrorResponse](#errorresponse) objektumot tartalmaz, amely a hibát okozó [hibák](#error) listáját tartalmazza. Ha a hiba egy paraméterhez kapcsolódik, a `parameter` mező azonosítja a hibát megadó paramétert. Ha a hiba egy paraméter értékéhez kapcsolódik, akkor a `value` mező nem érvényes értéket azonosít.

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
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|A Bing visszaadja a InvalidAuthorization, ha a Bing nem tudja hitelesíteni a hívót. Például hiányzik a `Ocp-Apim-Subscription-Key` fejléc, vagy az előfizetési kulcs érvénytelen.<br/><br/>A redundancia akkor fordul elő, ha egynél több hitelesítési módszert ad meg.<br/><br/>Ha a hiba InvalidAuthorization, a HTTP-állapotkód 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|A Bing visszaadja a InsufficientAuthorization, ha a hívónak nincs engedélye az erőforrás elérésére. Ez akkor fordulhat elő, ha az előfizetési kulcs le van tiltva vagy lejárt. <br/><br/>Ha a hiba InsufficientAuthorization, a HTTP-állapotkód 403.

## <a name="next-steps"></a>További lépések
- [Helyi üzleti keresés – rövid útmutató](quickstarts/local-quickstart.md)
- [Helyi üzleti keresés – Java rövid útmutató](quickstarts/local-search-java-quickstart.md)
- [Helyi üzleti keresési csomópont rövid útmutatója](quickstarts/local-search-node-quickstart.md)
- [Helyi üzleti keresés – Python rövid útmutató](quickstarts/local-search-python-quickstart.md)
