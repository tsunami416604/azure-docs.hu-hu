---
title: A Bing helyi üzleti Search API 7-es referencia |} A Microsoft Docs
description: A programozási a helyi vállalati keresési Bing-API-elemeket ismerteti.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: dc394093e9e2db223defe61229832ce5448bdfaa
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748679"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Helyi üzleti keresési Bing-API 7-es referencia

A helyi üzleti keresési API-t küld, beleértve az eredmények eléréséhez a Bing keresés lekérdezése például éttermekre, hotels vagy más helyi üzletek. A helyeket a lekérdezés is adja meg a helyi az üzleti és a egy kategóriát (például éttermek a közelben) nevét. Az entitástalálatok személyek, helyek vagy dolgok lehetnek. Ebben a környezetben helyen, üzleti entitásokat, állapotok, országban, stb.  

Ez a szakasz a válaszobjektumok, és a lekérdezési paraméterek és a fejlécek, amelyek befolyásolják a keresési eredmények vonatkozó technikai részleteket ismertet. Példák azt mutatják be, hogyan kéréseit: [helyi üzleti keresési C# rövid](quickstarts/local-quickstart.md) vagy [helyi üzleti keresési Java a rövid útmutató](quickstarts/local-search-java-quickstart.md). 
  
További információ a fejlécekről, amely a kérések tartalmaznia kell: [fejlécek](#headers).  
  
További információ a lekérdezési paraméterek, amely a kérések tartalmaznia kell: [lekérdezési paramétereket](#query-parameters).  
  
A JSON-fájllal kapcsolatos információk objektumok, hogy a válasz tartalmazza, lásd: [válaszobjektumok](#response-objects).

Engedélyezett használatát és a megjelenített eredmények kapcsolatos információkért lásd: [használja és megjelenítési követelményeihez](use-display-requirements.md).


  
## <a name="endpoint"></a>Végpont  
Kérelem helyi üzleti eredményeit, küldjön egy GET kérelmet kell: 

``` 
https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search

```
  
A kérelemnek a HTTPS protokollt kell használnia.  
  
> [!NOTE]
> Az URL-cím legfeljebb 2048 karakter. Győződjön meg arról, hogy az URL-cím hossza ne haladja meg a korlátot, a lekérdezési paraméterek maximális hossza legfeljebb 1500 karakterből kell lennie. Ha az URL-cím 2048 karakternél hosszabb, a kiszolgáló a 404 nem található adja vissza.  
  
  
## <a name="headers"></a>Fejlécek  
Az alábbiakban a fejlécek, köztük a kérést és választ.  
  
|Fejléc|Leírás|  
|------------|-----------------|  
|Elfogadás|Választható kérelemfejléc.<br /><br /> Az alapértelmezett adathordozó-típus az application/json. Adja meg, hogy a válasz a [JSON-LD](http://json-ld.org/), állítsa be az Accept fejléc alkalmazás/ld + json.|  
|<a name="acceptlanguage" />Accept-Language|Választható kérelemfejléc.<br /><br /> Nyelvek vesszővel elválasztott listája a felhasználói felület sztringjeihez. A lista prioritás szerinti csökkenő sorrendben jelenik meg. További információért, például a várt formátummal kapcsolatos részletekért lásd: [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ez a fejléc és a [setLang](#setlang) lekérdezési paraméter kölcsönösen kizárják egymást, ne adja meg mindkettőt.<br /><br /> Ha beállítja ezt a fejlécet, akkor a [cc](#cc) lekérdezési paramétert is meg kell adnia. A megfelelő piac meghatározásához a Bing a listában talált első támogatott nyelvet használja, és kombinálja azt a `cc` paraméter értékével. Ha a lista nem tartalmaz támogatott nyelvet, a Bing megkeresi a kérelmet támogató legközelebbi nyelvet és piacot, vagy másik lehetőségként egy összesített vagy alapértelmezett piacot használ az eredmények beszerzéséhez. A Bing által használt piac meghatározásához tekintse meg a BingAPIs-Market fejlécet.<br /><br /> Csak akkor használja ezt a fejlécet és a `cc` lekérdezési paramétert, ha több nyelvet ad meg. Ellenkező esetben használja az [mkt](#mkt) és a [setLang](#setlang) lekérdezési paramétereket.<br /><br /> A felhasználóifelület-sztring egy olyan sztring, amelyet feliratként használnak a felhasználói felületen. A JSON-válaszobjektumok tartalmaznak néhány felhasználóifelület-sztringet. A válaszobjektumokban található, a Bing.com tulajdonságaira mutató hivatkozások a megadott nyelvet alkalmazzák.|  
|<a name="market" />BingAPIs-Market|Válaszfejléc.<br /><br /> A kérelem által használt piac. A formátum a következő: \<languageCode\>-\<countryCode\>. Például: en-US.|  
|<a name="traceid" />BingAPIs-TraceId|Válaszfejléc.<br /><br /> A kérelem részleteit tartalmazó naplóbejegyzés azonosítója. Ha hiba történik, rögzítse ezt az azonosítót. Ha nem tudja meghatározni és megoldani a problémát, foglalja bele a kérelembe ezt az azonosítót is a támogatási csoportnak megadott többi információval együtt.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Kötelező kérelemfejléc.<br /><br /> Az előfizetési kulcs, amelyet akkor kapott, amikor feliratkozott a szolgáltatásra a [Cognitive Servicesben](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Választható kérelemfejléc<br /><br /> Alapértelmezés szerint a Bing gyorsítótárazott tartalmat ad vissza, ha van ilyen. Ha nem szeretné, hogy a Bing gyorsítótárazott tartalmat adjon vissza, állítsa a Pragma fejlécet no-cache értékre (például Pragma: no-cache).
|<a name="useragent" />User-Agent|Választható kérelemfejléc.<br /><br /> A kérelmet küldő felhasználói ügynök. A Bing arra használja a felhasználói ügynököt, hogy optimalizált élményt nyújtson a mobilfelhasználóknak. Bár nem kötelező, javasoljuk, hogy mindig adja meg ezt a fejlécet.<br /><br /> A felhasználói ügynöknek a gyakran használt böngészők által küldött sztringgel megegyezőnek kell lennie. A felhasználói ügynökökkel kapcsolatos információért lásd: [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Az alábbiakban példákat láthat a felhasználóiügynök-sztringekre.<br /><ul><li>Windows Phone – Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android – Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone – Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC – Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad – Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Választható kérelem- és válaszfejléc.<br /><br /> A Bing ezt a fejlécet használja ahhoz, hogy következetes viselkedést biztosítson a felhasználók számára a Bing API-hívásaiban. A Bing gyakran tesztel új funkciókat és fejlesztéseket, és az ügyfél-azonosítót használja kulcsként ahhoz, hogy a tesztcsomagokhoz rendelje a forgalmat. Ha a kérelmekben nem ugyanazt az ügyfél-azonosítót használja egy adott felhasználóhoz, előfordulhat, hogy a Bing több ütköző tesztcsomaghoz rendeli hozzá a felhasználót. Az ütköző tesztcsomagok hozzárendelése inkonzisztens felhasználói élményhez vezethet. Például ha a második kérelemhez más tesztcsomag van hozzárendelve, mint az elsőhöz, az váratlan működést eredményezhet. A Bing arra is felhasználhatja az ügyfél-azonosítót, hogy az ügyfél-azonosító keresési előzményeire szabja a webes találatokat, gazdagabb élményt nyújtva a felhasználónak.<br /><br /> A Bing továbbá az ügyfél-azonosító által létrehozott tevékenységek elemzésével az eredmények rangsorolásának javítására is használja a fejlécet. A relevancia javítása segít abban, hogy a Bing API-k jobb minőségű eredményeket biztosítsanak, ami pedig lehetővé teszi a magasabb átkattintási arányt az API fogyasztója számára.<br /><br /> **FONTOS:** Bár nem kötelező, javasoljuk, hogy tekintse annak ezt a fejlécet. Ha ugyanahhoz a végfelhasználóhoz és eszközkombinációhoz több kérelemben is megőrzi az ügyfél-azonosítót, azzal lehetővé teszi, 1) hogy az API fogyasztójának egységes felhasználói élményben legyen része, és 2) magasabb legyen az átkattintási arány a Bing API-k jobb minőségű eredményeinek köszönhetően.<br /><br /> A fejlécre az alábbi alapvető használati szabályok vonatkoznak.<br /><ul><li>Minden felhasználónak, aki használja az eszközön lévő alkalmazást, rendelkeznie kell egy egyedi, Bing által létrehozott ügyfél-azonosítóval.<br /><br/>Ha nem foglalja bele ezt a fejlécet a kérelembe, a Bing létrehoz egy azonosítót, és visszaküldi azt az X-MSEdge-ClientID válaszfejlécben. Ezt a fejlécet csak akkor NEM szabad belefoglalni a kérelembe, amikor a felhasználó először használja az alkalmazást azon az eszközön.<br /><br/></li><li>Használja az ügyfél-azonosítót minden olyan Bing API-kéréshez, amelyet az alkalmazás intéz a felhasználó kapcsán az eszközön.<br /><br/></li><li>**Figyelem:** gondoskodnia kell arról, hogy az ügyfél-azonosító ne legyen összekapcsolhatónak, bármely forrásának hitelesíthetőnek felhasználóifiók-adatokat.</li><br/><li>Őrizze meg az ügyfél-azonosítót. Az azonosító böngészőalkalmazásban való megőrzéséhez használjon egy állandó HTTP-cookie-t, amely biztosítja, hogy minden munkamenetben ez az azonosító legyen használva. Ne használjon munkamenet-cookie-t. Más alkalmazások, például a mobilalkalmazások esetében az azonosító megőrzéséhez használja az eszköz állandó tárolóját.<br /><br/>Kérje le a megőrzött ügyfél-azonosítót, amikor a felhasználó ismét használja az alkalmazást az eszközön.</li></ul><br /> **MEGJEGYZÉS:** A Bing-válaszok nem mindig tartalmazzák ezt a fejlécet. Ha a válasz tartalmazza ezt a fejlécet, rögzítse az ügyfél-azonosítót, és használja azt a felhasználó összes további Bing-kérelméhez az adott eszközön.<br /><br /> **MEGJEGYZÉS:** Ha belefoglalja a kérelembe az X-MSEdge-ClientID fejlécet, akkor ne foglaljon bele cookie-kat.|  
|<a name="clientip" />X-MSEdge-ClientIP|Választható kérelemfejléc.<br /><br /> Az ügyféleszköz IPv4- vagy IPv6-címe. Az IP-cím a felhasználó tartózkodási helyének felderítésére szolgál. A Bing arra használja a helyadatokat, hogy meghatározza a biztonságos keresés viselkedését.<br /><br /> **MEGJEGYZÉS:** Bár nem kötelező, javasoljuk, hogy mindig adja meg ezt a fejlécet és az X-Search-Location fejlécet.<br /><br /> Ne rejtse el a címet (például úgy, hogy 0-ra módosítja az utolsó oktettet). Ha elrejti a címet, a tartózkodási hely távol fog esni az eszköz tényleges helyétől, amely ahhoz vezethet, hogy a Bing téves eredményeket fog megadni.|  
|<a name="location" />X-Search-Location|Választható kérelemfejléc.<br /><br /> Kulcs/érték párok pontosvesszővel elválasztott listája, amely leírja az ügyfél földrajzi helyét. A Bing arra használja a helyadatokat, hogy meghatározza a biztonságos keresés viselkedését, illetve releváns helyi tartalmakat adjon vissza. A kulcs/érték párt \<kulcs\>:\<érték\> formátumban adja meg. Az alábbiakban láthatja a felhasználó tartózkodási helyének megadására használt kulcsokat.<br /><br /><ul><li>szél&mdash;az ügyfél helye fokban szélességét. A földrajzi szélesség nem lehet -90,0 foknál kisebb és +90,0 foknál nagyobb. A negatív értékek a déli szélességeket, a pozitív értékek pedig az északi szélességeket jelölik.<br /><br /></li><li>hosszú&mdash;az ügyfél helye fokban hosszúságát. A földrajzi hosszúság nem lehet -180,0 foknál kisebb és +180,0 foknál nagyobb. A negatív értékek a nyugati hosszúságokat, a pozitív értékek pedig a keleti hosszúságokat jelölik.<br /><br /></li><li>re&mdash; a radius, a mérőszámok, amely megadja a koordináták a vízszintes pontosságát. Adja át az eszköz helymeghatározási szolgáltatása által visszaadott értéket. A tipikus érték GPS/Wi-Fi esetén 22 m, telefontoronnyal végzett háromszögelés esetén 380 m, fordított IP-keresés esetén pedig 18 000 m.<br /><br /></li><li>TS&mdash; az UTC UNIX timestamp, amikor az ügyfél volt-e a helyen. (Az UNIX-időbélyeg az 1970. január 1. óta eltelt másodpercek száma).<br /><br /></li><li>head – Választható. Az ügyfél relatív haladási vagy utazási iránya. Az utazás irányt fokban adja meg 0 és 360 között, a tényleges északhoz képest az óramutató járásával megegyező irányban számítva. Csak akkor adja meg ezt a kulcsot, ha az `sp` kulcs értéke nem nulla.<br /><br /></li><li>SP&mdash; a vízszintes sebesség (sebesség), a másodpercenkénti, az ügyféleszközön utazik méterben.<br /><br /></li><li>ALT&mdash; a magasság, az ügyféleszközön, a mérőszámok.<br /><br /></li><li>are – Választható. A méterben megadott sugár, amely meghatározza a koordináták függőleges pontosságát. RADIUS alapértelmezés szerint 50 alapján. Csak akkor adja meg ezt a kulcsot, ha az `alt` kulcsot is megadja.<br /><br /></li></ul> **Megjegyzés:** bár ezek a kulcsok megadása nem kötelező, a további adatokat ad meg, annál pontosabb helyet eredménye.<br /><br /> **Megjegyzés:** hosszúan mindig adja meg a felhasználó földrajzi helyét. Különösen fontos megadni a helyet, ha az ügyfél IP-címe nem tükrözi pontosan a felhasználó fizikai helyét (például ha az ügyfél VPN-t használ). Az optimális eredmény érdekében foglalja bele a kérelembe ezt a fejlécet és az X-MSEdge-ClientIP fejlécet, de legalább ezt a fejlécet.|

> [!NOTE] 
> Ne feledje, hogy a Használati feltételek a fejlécek használatával kapcsolatban is előírják a vonatkozó törvények betartását. Egyes joghatóságokban, például Európában vannak olyan követelmények, amelyek előírják a felhasználói hozzájárulás megszerzését a nyomkövető eszközök felhasználói eszközökön való elhelyezése előtt.
  

## <a name="query-parameters"></a>Lekérdezési paraméterek  
A kérelem lekérdezési paraméterek tartalmazhat. Tekintse meg a szükséges oszlop, paraméter szükséges. URL-címet kell kódolása a lekérdezési paramétereket.  
  
  
|Name (Név)|Érték|Típus|Szükséges|  
|----------|-----------|----------|--------------|
|<a name="count" />Száma|A számát adja vissza, kezdve az index által megadott resuts a `offset` paraméter.|Karakterlánc|Nem|   
|<a name="localCategories" />localCategories|Keresés üzleti kategória szerint meghatározó beállítások listája.  Lásd: [helyi üzleti kategóriák keresése](local-categories.md)|Karakterlánc|Nem|  
|<a name="mkt" />mkt|A piac, ahonnan az eredmények származnak. <br /><br />Piaci a lehetséges értékek listáját lásd: [piaci kódok](#market-codes).<br /><br /> **Megjegyzés:** a helyi üzleti Search API jelenleg csak támogatja az en-us piacon és a nyelvet.<br /><br />|Karakterlánc|Igen|
|<a name="offset"/>eltolás|Az index által megadott eredmények elindításához a `count` paraméter.|Egész szám|Nem|  
|<a name="query" />válaszok|A felhasználó keresési kifejezés.|Karakterlánc|Nem|  
|<a name="responseformat" />responseFormat|Az adathordozó-típus használata a válaszhoz. A kis-és értékek a következők.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Az alapértelmezett érték a JSON. A JSON-fájllal kapcsolatos információk objektumok, hogy a válasz tartalmazza, lásd: [Válaszobjektumok](#response-objects).<br /><br />  Ha JsonLd adja meg, a válasz törzse tartalmazza a keresési eredményeket tartalmazó JSON-LD objektumok. A JSON-LD kapcsolatos információkért lásd: [JSON-LD](http://json-ld.org/).|Karakterlánc|Nem|  
|<a name="safesearch" />safeSearch|Felnőtt tartalmak szűrésére szolgáló szűrő. A következők azok a lehetséges szűrőértékek, amelyek nem különböztetik meg a kis- és nagybetűket.<br /><ul><li>Ki&mdash;weblapok és felnőtteknek szóló szöveg, képek és videók adja vissza.<br /><br/></li><li>Mérsékelt&mdash;felnőtt szöveget, de nem felnőtt lemezképek vagy videók weblapok adja vissza.<br /><br/></li><li>A szigorú&mdash;weblapok és felnőtteknek szóló szöveg, képek és videók nem adott vissza.</li></ul><br /> Az alapértelmezett érték a Moderate.<br /><br /> **Megjegyzés:** , ha a kérelem egy piaci származik, a Bing felnőtt szabályzat megköveteli, hogy `safeSearch` értéke Strict, a Bing figyelmen kívül hagyja a `safeSearch` értékét, és szigorú használja.<br/><br/>**MEGJEGYZÉS:** Ha a `site:` lekérdezési operátort használja, előfordulhat, hogy a válasz a `safeSearch` lekérdezési paraméter beállításától függetlenül felnőtteknek szóló tartalmakat fog tartalmazni. Csak akkor használja a `site:` operátort, ha ismeri a webhely tartalmát, és a felnőtteknek szóló tartalmak megjelenítése nem okoz problémát. |Karakterlánc|Nem|  
|<a name="setlang" />setLang|A felhasználói felület sztringjeihez használni kívánt nyelv. A nyelv megadásához használja az ISO 639-1 kétbetűs nyelvkódját. Az angol nyelv nyelvkódja például az EN. Az alapértelmezett érték az EN (angol).<br /><br /> Bár nem kötelező, javasoljuk, hogy mindig adja meg a nyelvet. A `setLang` paramétert általában az `mkt` által meghatározott nyelvre kell állítani, hacsak a felhasználó nem szeretné más nyelven megjeleníteni a felhasználói felület sztringjeit.<br /><br /> Ez a paraméter és az [Accept-Language](#acceptlanguage) fejléc kölcsönösen kizárják egymást, ne adja meg mindkettőt.<br /><br /> A felhasználóifelület-sztring egy olyan sztring, amelyet feliratként használnak a felhasználói felületen. A JSON-válaszobjektumok tartalmaznak néhány felhasználóifelület-sztringet. A válaszobjektumokban található, a Bing.com tulajdonságaira mutató hivatkozások is a megadott nyelvet alkalmazzák.|Karakterlánc|Nem| 


## <a name="response-objects"></a>Válasz objektumok  
Az alábbiakban a JSON-válasz objektumok, amelyek a válasz tartalmazhat. Ha a kérelem sikeres, a válaszban a legfelső szintű objektum van-e a [SearchResponse](#searchresponse) objektum. Ha a kérelem meghiúsul, a legfelső szintű objektum a [byl vrácen Prvek](#errorresponse) objektum.


|Objektum|Leírás|  
|------------|-----------------|  
|[Hely](#place)|Egy étterem vagy egy szállodai Vendég például a helyi vállalkozásokkal kapcsolatos információk határozza meg.|  

  
### <a name="error"></a>Hiba  
Határozza meg a következő hiba történt.  
  
|Elem|Leírás|Típus|  
|-------------|-----------------|----------|  
|<a name="error-code" />Kód|A hiba kódja, amely azonosítja a hiba kategóriáját. Lehetséges kódok listáját lásd: [hibakódok](#error-codes).|Karakterlánc|  
|<a name="error-message" />üzenet|A hiba leírása.|Karakterlánc|  
|<a name="error-moredetails" />moreDetails|Egy leírást, amely a hibával kapcsolatos további információkat biztosít.|Karakterlánc|  
|<a name="error-parameter" />A paraméter|A lekérdezési paraméter, amely a hibát okozó a kérésben.|Karakterlánc|  
|<a name="error-subcode" />Alkód|A hiba kódja, amely azonosítja a hibát. Például ha `code` InvalidRequest, akkor `subCode` ParameterInvalid vagy ParameterInvalidValue is lehet. |Karakterlánc|  
|<a name="error-value" />Érték|A lekérdezési paraméter értéke, amely nem érvényes.|Karakterlánc|  
  

### <a name="errorresponse"></a>Byl vrácen Prvek  
A legfelső szintű objektum, amely a válasz tartalmazza, ha a kérés nem teljesíthető.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Mutató típusa.|Karakterlánc|  
|<a name="errors" />Hibák|Miért nem sikerült a kérelem miatt hibák listája.|[Hiba](#error)]|  

  
  
### <a name="license"></a>Licenc  
Határozza meg a licenc, amely alatt a szöveges vagy fénykép használható.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|név|A licenc neve.|Karakterlánc|  
|url|Egy webhely, ahol a felhasználó kaphat-e további információ a licenc URL-címe.<br /><br /> A nevét és URL-cím használatával hivatkozás létrehozása.|Karakterlánc|  


### <a name="link"></a>Hivatkozás  
Az összetevők a hivatkozás határozza meg.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Mutató típusa.|Karakterlánc|  
|szöveg|A megjelenítendő szöveg.|Karakterlánc|  
|url|EGY URL-CÍMET. Az URL-címet használja, és hivatkozás létrehozása szöveg megjelenítéséhez.|Karakterlánc|  
  


  
### <a name="organization"></a>Szervezet  
Határozza meg a kiadó.  
  
Vegye figyelembe, hogy a közzétevő neve vagy a webhely vagy mindkét előfordulhat, hogy biztosítani.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|név|A közzétevő nevét.|Karakterlánc|  
|url|A kiadó webhelye URL-címe.<br /><br /> Vegye figyelembe, hogy a közzétevő nem ad egy webhelyet.|Karakterlánc|  
  
  

### <a name="place"></a>Hely  
Határozza meg a helyi vállalati, például egy étterem vagy Szálloda kapcsolatos információkat.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Írja be a mutatót, amely a következők egyikére lehet beállítani:<br /><br /><ul><li>Szálloda</li><li>LocalBusiness<br /></li><li>Étterem</ul><li>|Karakterlánc|  
|Cím|A postai címét, ahol az entitás.|[PostalAddress](#postaladdress)|  
|entityPresentationInfo|További információ az entitás, például a mutatók, amelyek segítségével az entitás típusa határozza meg. Például hogy egy étterem vagy szállodában. A `entityScenario` mező ListItem értékre van állítva.|[EntityPresentationInfo](#entitypresentationinfo)|  
|név|Az entitás nevét.|Karakterlánc|  
|Telefon|Az entitás telefonszáma.|Karakterlánc|  
|url|Az a entitás webhely URL-címe.<br /><br /> Az entitás nevét, valamint az URL-cím használata hivatkozás létrehozása, amely rákattintáskor végigvezeti a felhasználót az entitás webhelyre.|Karakterlánc|  
|webSearchUrl|A Bing keresési eredmény ehhez a helyhez URL-címe.|Karakterlánc| 
  
  
### <a name="querycontext"></a>QueryContext  
Meghatározza a Bing a kéréshez használt lekérdezési környezet.  
  
|Elem|Leírás|Típus|  
|-------------|-----------------|----------|  
|adultIntent|Logikai érték, amely azt jelzi, hogy a megadott lekérdezés felnőtteknek szánt tartalom. Az érték **igaz** amennyiben a lekérdezés felnőtteknek szánt; ellenkező esetben **hamis**.|Logikai|  
|alterationOverrideQuery|A lekérdezési karakterlánc használatával kényszerítheti a Bing, az eredeti karakterlánc használatához. Például, ha a lekérdezési karakterlánc *saling szélirányban fekvő*, a felülbírálási karakterlánc lesz *+ saling szélirányban fekvő*. Ne felejtse el a lekérdezési karakterláncot, amelynek eredménye kódolása *% 2Bsaling + szélirányban fekvő*.<br /><br /> Ez a mező része, csak akkor, ha az eredeti lekérdezési karakterláncot tartalmaz egy gépelési hibát.|Karakterlánc|  
|alteredQuery|A lekérdezési karakterlánc, a lekérdezés végrehajtása a Bing által használt. Bing formában lekérdezési karakterláncot használ, ha az eredeti lekérdezési karakterláncot a helyesírási hibákat tartalmaz. Például, ha a lekérdezési karakterlánc `saling downwind`, a módosított lekérdezési karakterlánc lesz `sailing downwind`.<br /><br /> Ez a mező része, csak akkor, ha az eredeti lekérdezési karakterláncot tartalmaz egy gépelési hibát.|Karakterlánc|  
|askUserForLocation|Logikai érték, amely azt jelzi, hogy szükséges-e a Bing pontos eredményeket a felhasználó földrajzi helye. Ha a felhasználó földrajzi helye az a [X-MSEdge-Ügyfélip](#clientip) és [hely keresése X](#location) fejlécek, figyelmen kívül hagyhatja ezt a mezőt.<br /><br /> Hely figyelembe lekérdezésekhez, például az "a mai időjárás-" vagy "éttermek a közelben", amelyet a felhasználó földrajzi helye pontos eredményeket kell Ez a mező értéke **igaz**.<br /><br /> Hely figyelembe lekérdezések, amelyek tartalmazzák a helyre (például "Seattle weather"), ez a mező értéke **hamis**. Ez a mező értéke is **hamis** lekérdezések, amelyek nem helyfüggő, például a "ajánlott eladók".|Logikai|  
|originalQuery|A kérésben megadott lekérdezési karakterlánc.|Karakterlánc|  

### <a name="identifiable"></a>Azonosításra alkalmas
|Name (Név)|Érték|Típus|  
|-------------|-----------------|----------|
|id|Egy erőforrás-azonosítója|Karakterlánc|
 
### <a name="rankinggroup"></a>RankingGroup
Határozza meg a keresési eredmények csoport, mint például mainline.
|Name (Név)|Érték|Típus|  
|-------------|-----------------|----------|
|elem|Keresési eredmények megjelennek a csoport listája.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Határozza meg a keresési eredmény elemek megjelenítéséhez.
|Name (Név)|Érték|Típus|  
|-------------|-----------------|----------|
|resultIndex|A cikk a válasz megjelenítéséhez a nulla alapú indexét. A cikk nem tartalmazza ezt a mezőt, a válasz jelennek meg az összes elemet. Például megjelenítik az összes hírek a hírek választ.|Egész szám|
|answerType|A válasz, amely tartalmazza az elem megjelenítésére. Ha például híreket.<br /><br />A típus használatával találja a választ a SearchResponse objektumban. A típus egy SearchResponse mező nevét.<br /><br /> Azonban a választípushoz használja, csak akkor, ha ez az objektum tartalmazza az érték mezőbe; Ellenkező esetben figyelmen kívül hagyhatja azt.|Karakterlánc|
|textualIndex|Az index a textualAnswers megjelenítése a válaszról.| Előjel nélküli egész szám|
|érték|Az azonosítója, amely azonosítja a válasz megjelenítéséhez vagy egy elemet a válasz megjelenítéséhez. Ha az azonosító azonosítja a választ, a válasz elemek megjelenítése.|Azonosításra alkalmas|

### <a name="rankingresponse"></a>RankingResponse  
Határozza meg, ahol a keresési eredmények lap tartalmának kell helyezni, és milyen sorrendben.  
  
|Name (Név)|Érték|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|A keresési eredmények megjelennek a által.|  
|<a name="ranking-pole" />Pole|A keresési eredmények között meg kell adni a legfeltűnőbb kezelése (például a fent a által látható és az oldalsávot).|  
|<a name="ranking-sidebar" />Az oldalsáv|A keresési eredmények megjelennek az oldalsávon.| 

### <a name="searchresponse"></a>SearchResponse  
Meghatározza a legfelső szintű objektum, amely a válasz tartalmazza, ha a kérelem sikeres.  
  
Vegye figyelembe, hogy a szolgáltatás egy szolgáltatásmegtagadási támadást gyanítja, ha a kérés sikeres lesz (HTTP-állapotkód: a 200-as rendben); azonban a válasz törzse üres lesz.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Írja be a mutatót, amely SearchResponse van beállítva.|Karakterlánc|  
|Forráshelyek|Az entitások, amelyek megfelelnek a keresési lekérdezés a listája.|JSON-objektum|  
|QueryContext|A Bing a kéréshez használt lekérdezési karakterláncot tartalmazó objektum.<br /><br /> Ez az objektum a lekérdezési karakterláncot tartalmaz, a felhasználó által megadott. Egy, a Bing a lekérdezéshez használt, ha a lekérdezési karakterláncot tartalmaz egy gépelési hibát formában lekérdezési karakterláncot is tartalmazhat.|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>Hibakódok

Az alábbi táblázat a lehetséges HTTP-állapotkódok, amely egy kérés adja vissza.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|200|Siker.|  
|400|A lekérdezési paraméterek egyike hiányzik vagy érvénytelen.|  
|401|Az előfizetési kulcs hiányzik vagy nem érvényes.|  
|403|A felhasználó hitelesítése (például használni őket egy érvényes előfizetési kulcsot), de azok nem rendelkezik engedéllyel a kért erőforrás.<br /><br /> Bing is előfordulhat, hogy ez az állapot vissza. Ha a hívó lekérdezéseit kiszolgálónként havi kvóta túllépve.|  
|410|A kérelem HTTP helyett a HTTPS protokollt használja. HTTPS az egyetlen támogatott protokoll.|  
|429|A hívó lekérdezéseit egy második kvóta túllépve.|  
|500|Váratlan kiszolgálóhiba.|

Ha a kérelem meghiúsul, a válasz tartalmaz egy [byl vrácen Prvek](#errorresponse) listáját tartalmazó objektum [hiba](#error) objektumok, amelyek a hiba okáról. Ha a hiba kapcsolatos a paramétert, a `parameter` mező azonosítja a probléma paraméter. És ha a hiba kapcsolatos egy paraméterérték a `value` mező azonosítja az értéket, amely nem érvényes.

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

A következő értékeket a lehetséges hiba kód és a részleges hiba kódja.

|Kód|Alkód|Leírás
|-|-|-
|Kiszolgálóhibái|UnexpectedError<br/>ResourceError<br/>Nincs implementálva|HTTP-állapotkód: 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Letiltva|A Bing InvalidRequest adja vissza, ha bármelyik részét a kérés érvénytelen, nem. Például egy kötelező paraméter hiányzik, vagy egy paraméter értéke nem érvényes.<br/><br/>Ha a hiba ParameterMissing vagy ParameterInvalidValue, a a HTTP-állapotkód: 400.<br/><br/>Ha a HTTPS helyett a HTTP protokollt használja, a Bing HttpNotAllowed adja vissza, és a HTTP-állapotkód: 410.
|RateLimitExceeded|Nincsenek alárendelt kódok|Minden alkalommal, amikor a lekérdezések másodpercenkénti (lekérdezési QPS) és a lekérdezések száma (QPM) havi kvótát túllépi a Bing RateLimitExceeded adja vissza.<br/><br/>Ha túllépi QPS, a Bing adja vissza a 429-es HTTP-állapotkód:, és Ha elfogynak QPM, a Bing adja vissza a 403-as.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|A Bing InvalidAuthorization adja vissza, ha a Bing a hívó nem tudja hitelesíteni. Ha például a `Ocp-Apim-Subscription-Key` fejléc hiányzik, vagy az előfizetési kulcs nem érvényes.<br/><br/>A redundancia akkor fordul elő, ha egynél több hitelesítési módszer adja meg.<br/><br/>Ha a hiba InvalidAuthorization, a HTTP-állapotkód: a 401-es.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|A Bing InsufficientAuthorization adja vissza, ha a hívó nem rendelkezik engedéllyel az erőforrás eléréséhez. Ez akkor fordulhat elő, ha az előfizetési kulcs le lett tiltva, vagy lejárt. <br/><br/>Ha a hiba InsufficientAuthorization, a HTTP-állapotkód: a 403-as.

## <a name="next-steps"></a>További lépések
- [Helyi üzleti keresés a rövid útmutató](quickstarts/local-quickstart.md)
- [Helyi üzleti keresési Java a rövid útmutató](quickstarts/local-search-java-quickstart.md)
- [Helyi üzleti Search-Node-quickstart](quickstarts/local-search-node-quickstart.md)
- [Helyi üzleti keresési Python a rövid útmutató](quickstarts/local-search-python-quickstart.md)