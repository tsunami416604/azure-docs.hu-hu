---
title: Bing helyi vállalati keresési API–7– referencia
titleSuffix: Azure Cognitive Services
description: Ez a cikk technikai részleteket tartalmaz a válaszobjektumokról, valamint a lekérdezési paraméterekről és -fejlécekről, amelyek hatással vannak a keresési eredményekre.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: db764a73aa1bb18ef2fc0f8f6e5ffe8fd60d388c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74075700"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Bing helyi vállalati keresési API 7-es verzióinak hivatkozása

A Helyi vállalkozás keresési API-ja keresési lekérdezést küld a Bingnek, hogy olyan találatokat kapjon, amelyek éttermeket, szállodákat vagy más helyi vállalkozásokat tartalmaznak. Helyek esetén a lekérdezés megadhatja a helyi vállalkozás vagy egy kategória nevét (például a közelemben lévő éttermeket). Az entitástalálatok személyek, helyek vagy dolgok lehetnek. Ebben az összefüggésben üzleti entitások, államok, országok/régiók stb.  

Ez a szakasz a válaszobjektumok, valamint a keresési eredményeket befolyásoló lekérdezési paraméterek és fejlécek technikai részleteit tartalmazza. A kérelmek lebonyolítását bemutató példákat a [Helyi üzleti keresés C# rövid útmutatójában](quickstarts/local-quickstart.md) vagy [a Helyi üzleti keresés Java gyorsútmutatójában talál.](quickstarts/local-search-java-quickstart.md) 
  
A kérelmekhez szükséges fejlécekről a Fejlécek című [témakörben](#headers)talál további információt.  
  
A kérelmeknek tartalmazniuk kell a lekérdezési paraméterekről a [Lekérdezési paraméterek](#query-parameters)című témakörben talál.  
  
A válasz által tartalmazott JSON-objektumokról a [Válaszobjektumok című](#response-objects)témakörben talál további információt.

Az eredmények megengedett használatáról és megjelenítéséről a [Használati és megjelenítési követelmények című](use-display-requirements.md)témakörben talál.


  
## <a name="endpoint"></a>Végpont  
A helyi üzleti eredmények igényléséhez küldjön get-kérelmet a következő nek: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
A kérelemnek a HTTPS protokollt kell használnia.  
  
> [!NOTE]
> Az URL maximális hossza 2048 karakter. Annak érdekében, hogy az URL-cím hossza ne haladja meg a korlátot, a lekérdezési paraméterek maximális hosszának 1500 karakternél kisebbnek kell lennie. Ha az URL-cím meghaladja a 2048 karaktert, a kiszolgáló 404 Nem található értéket ad vissza.  
  
  
## <a name="headers"></a>Fejlécek  
A következő fejlécek, amelyek et a kérelem és a válasz tartalmazhat.  
  
|Fejléc|Leírás|  
|------------|-----------------|  
|Elfogadás|Választható kérelemfejléc.<br /><br /> Az alapértelmezett adathordozó-típus az alkalmazás/json. Ha azt szeretné megadni, hogy a válasz [JSON-LD-t](https://json-ld.org/)használjon, állítsa az Accept fejlécet alkalmazás/ld+json ra.|  
|<a name="acceptlanguage" />Accept-Language|Választható kérelemfejléc.<br /><br /> Nyelvek vesszővel elválasztott listája a felhasználói felület sztringjeihez. A lista prioritás szerinti csökkenő sorrendben jelenik meg. További információért, például a várt formátummal kapcsolatos részletekért lásd: [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ez a fejléc és a [](#setlang)setLang&mdash; lekérdezési paraméter kölcsönösen kizárják egymást, ne adja meg mindkettőt.<br /><br /> Ha beállítja ezt a fejlécet, akkor a cc lekérdezési paramétert is meg kell adnia. A megfelelő piac meghatározásához a Bing a listában talált első támogatott nyelvet használja, és kombinálja azt a `cc` paraméter értékével. Ha a lista nem tartalmaz támogatott nyelvet, a Bing megkeresi a kérelmet támogató legközelebbi nyelvet és piacot, vagy másik lehetőségként egy összesített vagy alapértelmezett piacot használ az eredmények beszerzéséhez. A Bing által használt piac meghatározásához tekintse meg a BingAPIs-Market fejlécet.<br /><br /> Csak akkor használja ezt a fejlécet és a `cc` lekérdezési paramétert, ha több nyelvet ad meg. Ellenkező esetben használja az [mkt](#mkt) és a [setLang](#setlang) lekérdezési paramétereket.<br /><br /> A felhasználóifelület-sztring egy olyan sztring, amelyet feliratként használnak a felhasználói felületen. A JSON-válaszobjektumok tartalmaznak néhány felhasználóifelület-sztringet. A válaszobjektumokban található, a Bing.com tulajdonságaira mutató hivatkozások a megadott nyelvet alkalmazzák.|  
|<a name="market" />BingAPIs-Market|Válaszfejléc.<br /><br /> A kérelem által használt piac. A formátum a következő: \<languageCode\>-\<countryCode\>. Például: en-US.|  
|<a name="traceid" />BingAPIs-TraceId|Válaszfejléc.<br /><br /> A kérelem részleteit tartalmazó naplóbejegyzés azonosítója. Ha hiba történik, rögzítse ezt az azonosítót. Ha nem tudja meghatározni és megoldani a problémát, foglalja bele a kérelembe ezt az azonosítót is a támogatási csoportnak megadott többi információval együtt.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Kötelező kérelemfejléc.<br /><br /> Az előfizetési kulcs, amelyet akkor kapott, amikor feliratkozott a szolgáltatásra a [Cognitive Servicesben](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Választható kérelemfejléc<br /><br /> Alapértelmezés szerint a Bing gyorsítótárazott tartalmat ad vissza, ha van ilyen. Ha nem szeretné, hogy a Bing gyorsítótárazott tartalmat adjon vissza, állítsa a Pragma fejlécet no-cache értékre (például Pragma: no-cache).
|<a name="useragent" />User-Agent|Választható kérelemfejléc.<br /><br /> A kérelmet küldő felhasználói ügynök. A Bing arra használja a felhasználói ügynököt, hogy optimalizált élményt nyújtson a mobilfelhasználóknak. Bár nem kötelező, javasoljuk, hogy mindig adja meg ezt a fejlécet.<br /><br /> A felhasználói ügynöknek a gyakran használt böngészők által küldött sztringgel megegyezőnek kell lennie. A felhasználói ügynökökkel kapcsolatos információért lásd: [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Az alábbiakban példákat láthat a felhasználóiügynök-sztringekre.<br /><ul><li>Windows Phone – Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android – Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone – Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC – Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad – Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Választható kérelem- és válaszfejléc.<br /><br /> A Bing ezt a fejlécet használja ahhoz, hogy következetes viselkedést biztosítson a felhasználók számára a Bing API-hívásaiban. A Bing gyakran tesztel új funkciókat és fejlesztéseket, és az ügyfél-azonosítót használja kulcsként ahhoz, hogy a tesztcsomagokhoz rendelje a forgalmat. Ha a kérelmekben nem ugyanazt az ügyfél-azonosítót használja egy adott felhasználóhoz, előfordulhat, hogy a Bing több ütköző tesztcsomaghoz rendeli hozzá a felhasználót. Az ütköző tesztcsomagok hozzárendelése inkonzisztens felhasználói élményhez vezethet. Például ha a második kérelemhez más tesztcsomag van hozzárendelve, mint az elsőhöz, az váratlan működést eredményezhet. A Bing arra is felhasználhatja az ügyfél-azonosítót, hogy az ügyfél-azonosító keresési előzményeire szabja a webes találatokat, gazdagabb élményt nyújtva a felhasználónak.<br /><br /> A Bing továbbá az ügyfél-azonosító által létrehozott tevékenységek elemzésével az eredmények rangsorolásának javítására is használja a fejlécet. A relevancia javítása segít abban, hogy a Bing API-k jobb minőségű eredményeket biztosítsanak, ami pedig lehetővé teszi a magasabb átkattintási arányt az API fogyasztója számára.<br /><br /> **FONTOS:** Bár nem kötelező, javasoljuk, hogy tekintse annak ezt a fejlécet. Ha ugyanahhoz a végfelhasználóhoz és eszközkombinációhoz több kérelemben is megőrzi az ügyfél-azonosítót, azzal lehetővé teszi, 1) hogy az API fogyasztójának egységes felhasználói élményben legyen része, és 2) magasabb legyen az átkattintási arány a Bing API-k jobb minőségű eredményeinek köszönhetően.<br /><br /> A fejlécre az alábbi alapvető használati szabályok vonatkoznak.<br /><ul><li>Minden felhasználónak, aki használja az eszközön lévő alkalmazást, rendelkeznie kell egy egyedi, Bing által létrehozott ügyfél-azonosítóval.<br /><br/>Ha nem foglalja bele ezt a fejlécet a kérelembe, a Bing létrehoz egy azonosítót, és visszaküldi azt az X-MSEdge-ClientID válaszfejlécben. Ezt a fejlécet csak akkor NEM szabad belefoglalni a kérelembe, amikor a felhasználó először használja az alkalmazást azon az eszközön.<br /><br/></li><li>Használja az ügyfél-azonosítót minden olyan Bing API-kéréshez, amelyet az alkalmazás intéz a felhasználó kapcsán az eszközön.<br /><br/></li><li>**FIGYELEM:** Gondoskodnia kell arról, hogy ez az ügyfélazonosító ne kapcsolódjon hitelesíthető felhasználói fiókadatokhoz.</li><br/><li>Őrizze meg az ügyfél-azonosítót. Az azonosító böngészőalkalmazásban való megőrzéséhez használjon egy állandó HTTP-cookie-t, amely biztosítja, hogy minden munkamenetben ez az azonosító legyen használva. Ne használjon munkamenet-cookie-t. Más alkalmazások, például a mobilalkalmazások esetében az azonosító megőrzéséhez használja az eszköz állandó tárolóját.<br /><br/>Kérje le a megőrzött ügyfél-azonosítót, amikor a felhasználó ismét használja az alkalmazást az eszközön.</li></ul><br /> **MEGJEGYZÉS:** A Bing-válaszok nem mindig tartalmazzák ezt a fejlécet. Ha a válasz tartalmazza ezt a fejlécet, rögzítse az ügyfél-azonosítót, és használja azt a felhasználó összes további Bing-kérelméhez az adott eszközön.<br /><br /> **MEGJEGYZÉS:** Ha belefoglalja a kérelembe az X-MSEdge-ClientID fejlécet, akkor ne foglaljon bele cookie-kat.|  
|<a name="clientip" />X-MSEdge-ClientIP|Választható kérelemfejléc.<br /><br /> Az ügyféleszköz IPv4- vagy IPv6-címe. Az IP-cím a felhasználó tartózkodási helyének felderítésére szolgál. A Bing arra használja a helyadatokat, hogy meghatározza a biztonságos keresés viselkedését.<br /><br /> **MEGJEGYZÉS:** Bár nem kötelező, javasoljuk, hogy mindig adja meg ezt a fejlécet és az X-Search-Location fejlécet.<br /><br /> Ne rejtse el a címet (például úgy, hogy 0-ra módosítja az utolsó oktettet). Ha elrejti a címet, a tartózkodási hely távol fog esni az eszköz tényleges helyétől, amely ahhoz vezethet, hogy a Bing téves eredményeket fog megadni.|  
|<a name="location" />X-Search-Location|Választható kérelemfejléc.<br /><br /> Kulcs/érték párok pontosvesszővel elválasztott listája, amely leírja az ügyfél földrajzi helyét. A Bing arra használja a helyadatokat, hogy meghatározza a biztonságos keresés viselkedését, illetve releváns helyi tartalmakat adjon vissza. A kulcs/érték párt \<kulcs\>:\<érték\> formátumban adja meg. Az alábbiakban láthatja a felhasználó tartózkodási helyének megadására használt kulcsokat.<br /><br /><ul><li>Lat&mdash;Az ügyfél tartózkodási helyének szélessége, fokokban. A földrajzi szélesség nem lehet -90,0 foknál kisebb és +90,0 foknál nagyobb. A negatív értékek a déli szélességeket, a pozitív értékek pedig az északi szélességeket jelölik.<br /><br /></li><li>hosszú&mdash;Az ügyfél helyének hosszúsága fokban. A földrajzi hosszúság nem lehet -180,0 foknál kisebb és +180,0 foknál nagyobb. A negatív értékek a nyugati hosszúságokat, a pozitív értékek pedig a keleti hosszúságokat jelölik.<br /><br /></li><li>re&mdash; A sugár méterben, amely meghatározza a koordináták vízszintes pontosságát. Adja át az eszköz helymeghatározási szolgáltatása által visszaadott értéket. A tipikus érték GPS/Wi-Fi esetén 22 m, telefontoronnyal végzett háromszögelés esetén 380 m, fordított IP-keresés esetén pedig 18 000 m.<br /><br /></li><li>ts&mdash; Az UTC UNIX időbélyege, amikor az ügyfél a helyszínen volt. (Az UNIX-időbélyeg az 1970. január 1. óta eltelt másodpercek száma).<br /><br /></li><li>head – Választható. Az ügyfél relatív haladási vagy utazási iránya. Az utazás irányt fokban adja meg 0 és 360 között, a tényleges északhoz képest az óramutató járásával megegyező irányban számítva. Csak akkor adja meg ezt a kulcsot, ha az `sp` kulcs értéke nem nulla.<br /><br /></li><li>sp&mdash; A vízszintes sebesség (sebesség), méter per másodpercben, hogy az ügyfél eszköz utazik.<br /><br /></li><li>alt&mdash; Az ügyféleszköz magassága méterben.<br /><br /></li><li>are – Választható. A méterben megadott sugár, amely meghatározza a koordináták függőleges pontosságát. A sugár alapértelmezés szerint 50 kilométer. Csak akkor adja meg ezt a kulcsot, ha az `alt` kulcsot is megadja.<br /><br /></li></ul> **MEGJEGYZÉS:** Bár ezek a kulcsok nem kötelezőek, minél több információt ad meg, annál pontosabbak a helyadatok eredményei.<br /><br /> **MEGJEGYZÉS:** Javasoljuk, hogy mindig adja meg a felhasználó földrajzi helyét. Különösen fontos megadni a helyet, ha az ügyfél IP-címe nem tükrözi pontosan a felhasználó fizikai helyét (például ha az ügyfél VPN-t használ). Az optimális eredmény érdekében foglalja bele a kérelembe ezt a fejlécet és az X-MSEdge-ClientIP fejlécet, de legalább ezt a fejlécet.|

> [!NOTE] 
> Ne feledje, hogy a Használati feltételek a fejlécek használatával kapcsolatban is előírják a vonatkozó törvények betartását. Egyes joghatóságokban, például Európában vannak olyan követelmények, amelyek előírják a felhasználói hozzájárulás megszerzését a nyomkövető eszközök felhasználói eszközökön való elhelyezése előtt.
  

## <a name="query-parameters"></a>Lekérdezési paraméterek  
A kérelem a következő lekérdezési paramétereket tartalmazhatja. A szükséges paramétereket a Kötelező oszlopban található. A lekérdezési paramétereket URL-címmel kell kódolni.  
  
  
|Név|Érték|Típus|Kötelező|  
|----------|-----------|----------|--------------|
|<a name="count" />Számít|A visszaadandó eredmények száma, kezdve `offset` a paraméter által megadott indexszel.|Sztring|Nem|   
|<a name="localCategories" />localCategories (helyi kategóriák)|Az üzleti kategória szerint keresést meghatározó lehetőségek listája.  Lásd: [Helyi üzleti kategóriák keresése](local-categories.md)|Sztring|Nem|  
|<a name="mkt" />mkt|A piac, ahonnan az eredmények származnak. <br /><br />A lehetséges piaci értékek listáját a Piaci kódok.<br /><br /> **MEGJEGYZÉS:** A Local Business Search API jelenleg csak az en-us piacot és a nyelvet támogatja.<br /><br />|Sztring|Igen|
|<a name="offset"/>Eltolás|A `count` paraméter által megadott eredmények indításához.|Egész szám|Nem|  
|<a name="query" />K|A felhasználó keresési kifejezése.|Sztring|Nem|  
|<a name="responseformat" />responseFormat (válaszformátum)|A válaszhoz használandó adathordozó-típus. A kis- és nagybetűket nem megkülönböztető lehetséges értékek a következők.<br /><ul><li>JSON</li><li>JSONLD között</li></ul><br /> Az alapértelmezett érték a JSON. A válasz által tartalmazott JSON-objektumokról a Válaszobjektumok című [témakörben](#response-objects)talál további információt.<br /><br />  Ha a JsonLd értéket adja meg, a választörzs a keresési eredményeket tartalmazó JSON-LD objektumokat is tartalmazza. A JSON-LD-ről a [JSON-LD](https://json-ld.org/)című témakörben talál további információt.|Sztring|Nem|  
|<a name="safesearch" />safeSearch|Felnőtt tartalmak szűrésére szolgáló szűrő. A következők azok a lehetséges szűrőértékek, amelyek nem különböztetik meg a kis- és nagybetűket.<br /><ul><li>Kikapcsolva:&mdash;Felnőtteknek szóló weblapok, képek vagy videók.<br /><br/></li><li>Moderált&mdash;Return weboldalak felnőtt szöveggel, de felnőtt képek és videók nem.<br /><br/></li><li>Szigorú&mdash;Ne térjen vissza felnőtt szöveget, képet vagy videót tartalmazó weblapokat.</li></ul><br /> Az alapértelmezett érték a Moderate.<br /><br /> **MEGJEGYZÉS:** Ha a kérés olyan piacról érkezik, `safeSearch` amelyet a Bing felnőttkori szabályzata szigorúra állít, a Bing figyelmen kívül hagyja az `safeSearch` értéket, és szigorúat használ.<br/><br/>**MEGJEGYZÉS:** Ha a `site:` lekérdezési operátort használja, előfordulhat, hogy a válasz a `safeSearch` lekérdezési paraméter beállításától függetlenül felnőtteknek szóló tartalmakat fog tartalmazni. Csak akkor használja a `site:` operátort, ha ismeri a webhely tartalmát, és a felnőtteknek szóló tartalmak megjelenítése nem okoz problémát. |Sztring|Nem|  
|<a name="setlang" />setLang|A felhasználói felület sztringjeihez használni kívánt nyelv. A nyelv megadásához használja az ISO 639-1 kétbetűs nyelvkódját. Az angol nyelv nyelvkódja például az EN. Az alapértelmezett érték az EN (angol).<br /><br /> Bár nem kötelező, javasoljuk, hogy mindig adja meg a nyelvet. A `setLang` paramétert általában az `mkt` által meghatározott nyelvre kell állítani, hacsak a felhasználó nem szeretné más nyelven megjeleníteni a felhasználói felület sztringjeit.<br /><br /> Ez a paraméter és az [](#acceptlanguage)Accept-Language&mdash; fejléc kölcsönösen kizárják egymást, ne adja meg mindkettőt.<br /><br /> A felhasználóifelület-sztring egy olyan sztring, amelyet feliratként használnak a felhasználói felületen. A JSON-válaszobjektumok tartalmaznak néhány felhasználóifelület-sztringet. A válaszobjektumokban található, a Bing.com tulajdonságaira mutató hivatkozások is a megadott nyelvet alkalmazzák.|Sztring|Nem| 


## <a name="response-objects"></a>Válaszobjektumok  
A következő a JSON válasz objektumok, amelyek a válasz tartalmazhat. Ha a kérelem sikeres, a válasz legfelső szintű objektuma a [SearchResponse](#searchresponse) objektum. Ha a kérés sikertelen, a legfelső szintű objektum az [ErrorResponse](#errorresponse) objektum.


|Objektum|Leírás|  
|------------|-----------------|  
|[Hely](#place)|Egy helyi vállalkozással, például étteremmel vagy szállodával kapcsolatos információkat határoz meg.|  

  
### <a name="error"></a>Hiba  
A bekövetkezett hibát határozza meg.  
  
|Elem|Leírás|Típus|  
|-------------|-----------------|----------|  
|<a name="error-code" />Kód|A hibakategóriát azonosító hibakód. A lehetséges kódok listáját a [Hibakódok ( Hibakódok](#error-codes)) tartalmazza.|Sztring|  
|<a name="error-message" />Üzenetet|A hiba leírása.|Sztring|  
|<a name="error-moredetails" />moreDetails (részletek)|A hibával kapcsolatos további információkat tartalmazó leírás.|Sztring|  
|<a name="error-parameter" />Paraméter|A hibát okozó kérelem lekérdezési paramétere.|Sztring|  
|<a name="error-subcode" />Alkód|A hibát azonosító hibakód. Ha például `code` az InvalidRequest, `subCode` akkor lehet ParameterInvalid vagy ParameterInvalidValue. |Sztring|  
|<a name="error-value" />value|A lekérdezési paraméter érvénytelen értéke.|Sztring|  
  

### <a name="errorresponse"></a>Hibaválasz  
A válasz által a kérelem sikertelensége esetén a legfelső szintű objektum.  
  
|Név|Érték|Típus|  
|----------|-----------|----------|  
|_type|Írja be a tippet.|Sztring|  
|<a name="errors" />Hibák|A kérelem sikertelensse okait leíró hibák listája.|[Hiba:[]](#error)|  

  
  
### <a name="license"></a>Licenc  
Meghatározza azt a licencet, amely alatt a szöveg vagy fénykép használható.  
  
|Név|Érték|Típus|  
|----------|-----------|----------|  
|név|A licenc neve.|Sztring|  
|url|Egy olyan webhely URL-címe, ahol a felhasználó további információkat kaphat a licencről.<br /><br /> Hivatkozás létrehozásához használja a nevet és az URL-címet.|Sztring|  


### <a name="link"></a>Hivatkozás  
A hivatkozás összetevőit határozza meg.  
  
|Név|Érték|Típus|  
|----------|-----------|----------|  
|_type|Írja be a tippet.|Sztring|  
|szöveg|A megjelenített szöveg.|Sztring|  
|url|Egy URL-t. Hivatkozás létrehozásához használja az URL-címet és a megjelenített szöveget.|Sztring|  
  


  
### <a name="organization"></a>Szervezet  
Közzétevőt határoz meg.  
  
Ne feledje, hogy a közzétevő megadhatja a nevét, a webhelyét vagy mindkettőt.  
  
|Név|Érték|Típus|  
|----------|-----------|----------|  
|név|A kiadó neve.|Sztring|  
|url|A közzétevő webhelyének URL-címe.<br /><br /> Ne feledje, hogy a közzétevő nem biztosít webhelyet.|Sztring|  
  
  

### <a name="place"></a>Hely  
Egy helyi vállalkozással, például étteremmel vagy szállodával kapcsolatos információkat határoz meg.  
  
|Név|Érték|Típus|  
|----------|-----------|----------|  
|_type|Írja be az emlékeztetőt, amely a következő képpen állítható be:<br /><br /><ul><li>Szálloda</li><li>LocalBusiness (Helyi vállalkozás)<br /></li><li>Étterem</ul><li>|Sztring|  
|address|A szervezet székhelye szerinti postai cím.|Postai cím|  
|entityPresentationInfo|További információk az entitásról, például a tippeket, amelyek segítségével meghatározhatja az entitás típusát. Például, hogy ez egy étterem vagy szálloda. A `entityScenario` mező beállítása ListItem.|EntityPresentationInfo|  
|név|Az entitás neve.|Sztring|  
|Telefon|A szervezet telefonszáma.|Sztring|  
|url|Az entitás webhelyének URL-címe.<br /><br /> Ezzel az URL-címmel és az entitás nevével együtt olyan hivatkozást hozhat létre, amelyre kattintva a felhasználó az entitás webhelyére kerül.|Sztring|  
|webSearchUrl|A Bing keresési eredményének URL-címe erre a helyre.|Sztring| 
  
  
### <a name="querycontext"></a>QueryContext (Lekérdezési környezet)  
A Bing által a kérelemhez használt lekérdezési környezetet határozza meg.  
  
|Elem|Leírás|Típus|  
|-------------|-----------------|----------|  
|felnőttSzándék|Logikai érték, amely azt jelzi, hogy a megadott lekérdezés felnőtt szándékkal rendelkezik-e. Az érték **akkor igaz,** ha a lekérdezés felnőtt szándékkal rendelkezik; ellenkező esetben **hamis**.|Logikai|  
|módosításOverrideQuery|A bing az eredeti karakterlánc használatára való kényszerítéséhez használandó lekérdezési karakterlánc. Ha például a lekérdezési karakterlánc *szélirányban tart,* a felülbírálási lekérdezési karakterlánc *+saling lesz hátszélben.* Ne felejtse el kódolni a lekérdezési karakterláncot, amely *%2Bsaling+hátszéllel jár.*<br /><br /> Ez a mező csak akkor jelenik meg, ha az eredeti lekérdezési karakterlánc helyesírási hibát tartalmaz.|Sztring|  
|módosítottLekérdezés|A Bing által a lekérdezés végrehajtásához használt lekérdezési karakterlánc. A Bing a módosított lekérdezési karakterláncot használja, ha az eredeti lekérdezési karakterlánc helyesírási hibákat tartalmaz. Ha például a lekérdezési karakterlánc a `saling downwind`módosított `sailing downwind`lekérdezési karakterlánc lesz.<br /><br /> Ez a mező csak akkor jelenik meg, ha az eredeti lekérdezési karakterlánc helyesírási hibát tartalmaz.|Sztring|  
|askUserForLocation|Logikai érték, amely azt jelzi, hogy a Bing nek szüksége van-e a felhasználó tartózkodási helyére a pontos eredmények biztosításához. Ha a felhasználó helyét az [X-MSEdge-ClientIP](#clientip) és az [X-Search-Location](#location) fejlécek használatával adta meg, figyelmen kívül hagyhatja ezt a mezőt.<br /><br /> A helymeghatározási lekérdezések, például a "mai időjárás" vagy a "közeli éttermek" esetében, amelyeknek a felhasználó tartózkodási helyére van szükségük a pontos eredmények biztosításához, ez a mező **igaz**értékre van állítva.<br /><br /> A helyet tartalmazó helyadatokat tartalmazó lekérdezések (például "Seattle időjárása") esetén ez a mező **hamis**értékre van állítva. Ez a mező **hamis** beállításra van állítva olyan lekérdezéseknél is, amelyek nem helytudatosak, például a "bestsellerek".|Logikai|  
|originalQuery|A lekérdezési karakterlánc a kérelemben megadott módon.|Sztring|  

### <a name="identifiable"></a>Azonosítható

|Név|Érték|Típus|  
|-------------|-----------------|----------|
|id|Erőforrás-azonosító|Sztring|
 
### <a name="rankinggroup"></a>Rangsorolási csoport
Egy keresési eredménycsoportot határoz meg, például a fővonalat.

|Név|Érték|Típus|  
|-------------|-----------------|----------|
|Elemek|A csoportban megjelenítendő keresési eredmények listája.|RankingItem elem|

### <a name="rankingitem"></a>RankingItem elem
A megjelenítendő keresési eredményelemet határozza meg.

|Név|Érték|Típus|  
|-------------|-----------------|----------|
|resultIndex|A megjelenítendő válaszban szereplő elem nulla alapú indexe. Ha a cikk nem tartalmazza ezt a mezőt, jelenítse meg a válasz ban szereplő összes cikket. Például jelenítse meg az összes újságcikket a Hírek válaszban.|Egész szám|
|answerType típus|A megjelenítendő elemet tartalmazó válasz. Például: Hírek.<br /><br />A típus használatával keresse meg a választ a SearchResponse objektumban. A típus egy SearchResponse mező neve.<br /><br /> A választípust azonban csak akkor használja, ha ez az objektum tartalmazza az értékmezőt; ellenkező esetben figyelmen kívül hagyja azt.|Sztring|
|textualIndex|A válasz indexe a textualAnswers feliratban.| Aláíratlan egész szám|
|érték|Az azonosító, amely azonosítja a megjelenítendő választ vagy a megjelenítendő válasz egy elemét. Ha az azonosító választ azonosít, jelenítse meg a válasz összes elemét.|Azonosítható|

### <a name="rankingresponse"></a>RangsorolásI válasz  
Azt határozza meg, hogy a keresési eredmények oldaltartalmának hol és milyen sorrendben legyen elhelyezése.  
  
|Név|Érték|  
|----------|-----------|  
|<a name="ranking-mainline" />Mainline|A fővonalon megjelenítendő keresési eredmények.|  
|<a name="ranking-pole" />Pole|A keresési eredmények, amelyeket a leglátványosabb kezelésnek kell biztosítani (például a fővonal és az oldalsáv felett).|  
|<a name="ranking-sidebar" />Tálaló|Az oldalsávon megjelenítendő keresési eredmények.| 

### <a name="searchresponse"></a>SearchResponse (Keresésválasz)  
Azt a legfelső szintű objektumot határozza meg, amelyet a válasz a kérelem sikeressége esetén tartalmaz.  
  
Vegye figyelembe, hogy ha a szolgáltatás szolgáltatásmegtagadási támadást gyanít, a kérés sikeres lesz (a HTTP állapotkódja 200 OK); azonban a válasz törzse üres lesz.  
  
|Név|Érték|Típus|  
|----------|-----------|----------|  
|_type|Írja be a searchresponse-t.|Sztring|  
|Helyek|A keresési lekérdezéshez kapcsolódó entitások listája.|JSON-objektum|  
|queryContext|Olyan objektum, amely a Bing által a kérelemhez használt lekérdezési karakterláncot tartalmazza.<br /><br /> Ez az objektum a felhasználó által megadott lekérdezési karakterláncot tartalmazza. Tartalmazhat egy módosított lekérdezési karakterláncot is, amelyet a Bing használt a lekérdezéshez, ha a lekérdezési karakterlánc helyesírási hibát tartalmazott.|[QueryContext (Lekérdezési környezet)](#querycontext)|  


## <a name="error-codes"></a>Hibakódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|200|Siker.|  
|400|A lekérdezési paraméterek egyike hiányzik vagy érvénytelen.|  
|401|Az előfizetési kulcs hiányzik vagy érvénytelen.|  
|403|A felhasználó hitelesítve van (például érvényes előfizetési kulcsot használtak), de nincs engedélye a kért erőforráshoz.<br /><br /> A Bing akkor is visszaadhatja ezt az állapotot, ha a hívó túllépte a havi kvótát.|  
|410|A kérelem HTTP protokollt használt a HTTPS protokoll helyett. A HTTPS az egyetlen támogatott protokoll.|  
|429|A hívó túllépte a másodpercenkénti lekérdezési kvótát.|  
|500|Váratlan kiszolgálóhiba történt.|

Ha a kérés sikertelen, a válasz egy [ErrorResponse](#errorresponse) objektumot tartalmaz, amely [a hibaobjektumok](#error) listáját tartalmazza, amelyek leírják, hogy mi okozta a hibát. Ha a hiba egy paraméterhez `parameter` kapcsolódik, a mező azonosítja a problémát okozó paramétert. És ha a hiba egy paraméterértékhez kapcsolódik, a `value` mező azonosítja az érvénytelen értéket.

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

A következőkben a lehetséges hibakód és alhibakód értékek találhatók.

|Kód|Alkód|Leírás
|-|-|-
|Kiszolgálóhiba|Váratlan hiba<br/>Erőforráshiba<br/>Nincs megvalósítva|A HTTP állapotkódja 500.
|Érvénytelen kérelem|Paraméter hiányzik<br/>ParameterInvalidValue érték<br/>HttpNem engedélyezett<br/>Blokkolva|A Bing az InvalidRequest függvényt adja vissza, ha a kérés bármely része érvénytelen. Például egy szükséges paraméter hiányzik, vagy egy paraméterérték érvénytelen.<br/><br/>Ha a hiba ParameterMissing vagy ParameterInvalidValue, a HTTP-állapotkód 400.<br/><br/>Ha https helyett a HTTP protokollt használja, a Bing a HttpNotAllowed értéket adja vissza, a HTTP-állapotkód pedig 410.
|RateLimitExceeded|Nincsenek alkódok|A Bing visszaadja a RateLimitExceeded értéket, ha túllépi a lekérdezések másodpercenkénti (QPS) vagy lekérdezések havonta (QPM) kvótát.<br/><br/>Ha túllépi a QPS protokollt, a Bing a 429-es HTTP-állapotkódot adja vissza, és ha túllépi a QPM értéket, akkor a Bing 403 értéket ad vissza.
|InvalidAuthorization (Érvénytelen engedélyezés)|Engedély hiányzik<br/>EngedélyezésRedundancy|A Bing invalidAuthorization értéket ad vissza, ha a Bing nem tudja hitelesíteni a hívót. Például a `Ocp-Apim-Subscription-Key` fejléc hiányzik, vagy az előfizetési kulcs érvénytelen.<br/><br/>A redundancia akkor következik be, ha egynél több hitelesítési módszert ad meg.<br/><br/>Ha a hiba InvalidAuthorization, a HTTP-állapotkód 401.
|Elégtelen engedélyezés|Engedélyezésletiltva<br/>Engedélyezés Lejárt|A Bing nem licencet ad vissza, ha a hívónak nincs engedélye az erőforrás eléréséhez. Ez akkor fordulhat elő, ha az előfizetési kulcs le van tiltva, vagy lejárt. <br/><br/>Ha a hiba insufficientAuthorization, a HTTP-állapotkód 403.

## <a name="next-steps"></a>További lépések
- [Helyi üzleti keresés rövid útmutató](quickstarts/local-quickstart.md)
- [Helyi üzleti keresés Java – rövid útmutató](quickstarts/local-search-java-quickstart.md)
- [A helyi üzleti keresési csomópont rövid útmutatója](quickstarts/local-search-node-quickstart.md)
- [Helyi üzleti keresés Python rövid útmutató](quickstarts/local-search-python-quickstart.md)
