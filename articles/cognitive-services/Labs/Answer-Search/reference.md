---
title: A projekt válaszkeresés referencia – a Microsoft Cognitive Services |} A Microsoft Docs
description: Referencia a projekt válaszkeresés végpont.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: a12761c2d913cd7ffaa2cbc2cd42576c6bc96434
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866984"
---
# <a name="project-answer-search-v7-reference"></a>Projekt válaszkeresés v7-referencia

Bing – válasz SearchAPI egy lekérdezési paramétert, és adja vissza egy `searchResponse` a `answerType`: `facts` vagy `entities`. 

A válasz keresési API-t használó alkalmazásokat az előzetes verzióra egy lekérdezési paraméter az URL-címet az végpontra kérést küldhet.  A kérésnek tartalmaznia kell a `q=searchTerm` paraméter és *Ocp-Apim-Subscription-Key* fejléc.   

A JSON-válasz elemzése tényeket és a keresés Objekt kapcsolatos adatokat tartalmazó entitásokhoz.

## <a name="endpoint"></a>Végpont
Válaszkeresés eredmények kéréséhez, a következő végpont egy kérés küldése. A fejlécek és URL-paraméterek használatával további specifikációk meghatározása.

GET-végpont: 
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

````

A kérelem kell a HTTPS protokollt használja, és tartalmazza a lekérdezési paraméter a következő:
-  a q =<URL> – a lekérdezést, amely azonosítja az objektumot a keresés

Példák azt mutatják be, hogyan kéréseit: [C# gyorsútmutató](c-sharp-quickstart.md) vagy [Java rövid](java-quickstart.md). 

A következő szakaszok a válaszobjektumok, a lekérdezési paraméterek és a fejlécek, amelyek befolyásolják a keresési eredmények vonatkozó technikai részleteket. 
  
További információ a fejlécekről, amely a kérések tartalmaznia kell: [fejlécek](#headers).  
  
További információ a lekérdezési paraméterek, amely a kérések tartalmaznia kell: [lekérdezési paramétereket](#query-parameters).  
  
A JSON-fájllal kapcsolatos információk objektumok, hogy a válasz tartalmazza, lásd: [válaszobjektumok](#response-objects).

A maximális lekérdezési URL-cím hossza 2048 karakter lehet. Győződjön meg arról, hogy az URL-cím hossza ne haladja meg a korlátot, a lekérdezési paraméterek maximális hossza legfeljebb 1500 karakterből kell lennie. Ha az URL-cím 2048 karakternél hosszabb, a kiszolgáló a 404 nem található adja vissza.  

Engedélyezett használatát és a megjelenített eredmények kapcsolatos információkért lásd: [használja és megjelenítési követelményeihez](use-display-requirements.md). 

> [!NOTE]
> Néhány kérelemfejlécek jelentős más keresési API-k nem befolyásolják a következő URL-cím előnézete
> - Direktiva pragma – a hívó nem rendelkezik URL-cím előnézete használ-e gyorsítótár felett
> - Gyorsítótár-vezérlő esetében – a hívó nem rendelkezik URL-cím előnézete használ-e gyorsítótár felett
> - Felhasználói ügynök

> Is néhány paraméter nem jelenleg jelentéssel bíró URL-cím előzetes API-hoz, de a jövőben a továbbfejlesztett globalizációs használhatók. 
 
## <a name="headers"></a>Fejlécek  
Az alábbiakban a fejlécek, köztük a kérést és választ.  
  
|Fejléc|Leírás|  
|------------|-----------------|  
|Elfogadás|Nem kötelező kérelem fejléce.<br /><br /> Az alapértelmezett adathordozó-típus az application/json. Adja meg, hogy a válasz a [JSON-LD](http://json-ld.org/), állítsa be az Accept fejléc alkalmazás/ld + json.|  
|<a name="acceptlanguage" />Fogadja el nyelv|Nem kötelező kérelem fejléce.<br /><br /> Nyelvek használandó felhasználói felület karakterláncok vesszővel tagolt listája. A lista egy csökkenő sorrendben. További információk, beleértve a várt formátum: [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ezt a fejlécet és a [setLang](#setlang) lekérdezési paraméter, egymást kölcsönösen kizáró&mdash;ne adja meg mindkettőt.<br /><br /> Ha beállította ezt a fejlécet, meg kell adni a [cc](#cc) lekérdezési paraméter. Annak megállapításához, a piac eredményeket ad vissza, a Bing, megkeresi a listából, és egyesíti az első támogatott nyelvet használja az `cc` paraméter értéke. Ha a lista nem tartalmazza a támogatott nyelven, a Bing megkeresi a legközelebbi nyelvet és a piacon, amely támogatja a kérés, vagy használ egy összesített vagy alapértelmezett piaci az eredményeket a. Annak megállapításához, a piac, a Bing által használt, tekintse meg a piacra jutási BingAPIs fejléc.<br /><br /> Használja ezt a fejlécet és a `cc` lekérdezési paraméter csak akkor, ha több nyelvet ad meg. Ellenkező esetben a [mkt](#mkt) és [setLang](#setlang) lekérdezési paramétereket.<br /><br /> A felhasználói felület karakterlánc egy karakterláncérték, amely egy címkét egy felhasználói felületen. A JSON-válasz objektumok nincsenek néhány felhasználói felület karakterláncokat. A válaszobjektumok Bing.com tulajdonságait mutató hivatkozásokat a megadott nyelvre a alkalmazni.|  
|<a name="market" />BingAPIs piaci|Válaszfejléc.<br /><br /> A piac, a kérelem által használt. Az űrlap \<languageCode\>-\<countryCode\>. Például: hu-hu.|  
|<a name="traceid" />BingAPIs-TraceId|Válaszfejléc.<br /><br /> A kérelem részleteit tartalmazó a naplóbejegyzés azonosítója. Ha hiba történik, rögzítése forrástároló. Ha nem határozza meg, és a probléma megoldásához, tartalmazzák ezt az Azonosítót és az egyéb adatokat, hogy a támogatási csoporthoz.|  
|<a name="subscriptionkey" />OCP-Apim-Subscription-Key|Szükséges a kérelem fejlécében.<br /><br /> Az előfizetési kulcsot, ezt a szolgáltatást a regisztráció során kapott [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Direktiva pragma|Nem kötelező kérelem fejléce<br /><br /> Alapértelmezés szerint a Bing gyorsítótárazott tartalmat vissza, ha elérhető. Gyorsítótárazott tartalom visszaadó megakadályozni a Bing, állítsa be a Pragma fejléc no-cache (például Pragma: no-cache).
|<a name="useragent" />Felhasználói ügynök|Nem kötelező kérelem fejléce.<br /><br /> A felhasználói ügynök, a kérelmet indító. A Bing a felhasználói ügynök használatával mobil felhasználók számára egy optimalizált felhasználói élményt biztosíthat. Bár nem kötelező, csak Ön javasoljuk, hogy mindig adja meg ezt a fejlécet.<br /><br /> A felhasználói ügynök ugyanazt a karakterláncot, amely bármely általánosan használt böngésző elküldi kell lennie. Felhasználói ügynökök kapcsolatos információkért lásd: [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> A következő példák felhasználói ügynök karakterláncokat.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibilis. MSIE 10.0; Windows Phone 8.0-s; A Trident vagy 6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U. Android 2.3.5; en-us; SCH-I500 Build/MÉZESKALÁCS) AppleWebKit/533.1 (KHTML; például gekkó) vagy 4.0-s verziójú Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone operációs rendszer 6_1, például a Mac OS X) AppleWebKit/536.26 (KHTML; például gekkó) Mobile/10B142 iPhone4; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3. WOW64; A Trident/7.0; Touch; RV:11.0), például gekkó<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU-OS 7_0 például a Mac OS X) AppleWebKit/537.51.1 (például gekkó KHTML) verziója/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Választható kérések és válaszok fejléc.<br /><br /> A Bing a fejléc használatával biztosítható a felhasználók konzisztens viselkedés között Bing API-hívások. Bing gyakran légi járművek új szolgáltatásait és fejlesztéseit, és hozzárendeléséhez a forgalmat a különböző repülőjáratok kulcsként használ az ügyfél-Azonosítót. Az azonos ügyfél-Azonosítót egy felhasználóhoz több kérés között nem használja, ha majd Bing lehet, hogy a felhasználó hozzárendelése több ütköző repülőjáratok. Több ütköző repülőjáratok folyamatban van rendelve egy konzisztens felhasználói élményt vezethet. Például ha a második kérés, mint az első különböző repülési hozzárendeléssel rendelkezik, a tapasztalatok lehet váratlan. A Bing segítségével is az ügyfél-azonosító webes találatokat, hogy az ügyfél a megfelelő kódolási sebesség kiválasztását azonosítóhoz tartozó keresési előzmények, a felhasználó egy gazdagabb élményt nyújtva.<br /><br /> A Bing is használja ezt a fejlécet a tevékenység egy ügyfél-azonosítót. által generált elemzésével eredmény módosítják a rangsort javítása érdekében A relevancia alapján végzett fejlesztések segítség az eredmények a Bing API-k és üzembe viszont lehetővé teszi, hogy nagyobb átkattintásos arányt API fogyasztók számára jobb minőségű.<br /><br /> **Fontos:** bár nem kötelező, csak akkor érdemes ezt a kötelező fejlécet. (1.) a felhasználó a API a Bing API-k fogadása, egységes felhasználói élmény, és (2) a nagyobb átkattintásos sebesség jobb minőségű eredmények via megőrzése az ügyfél-azonosító több kérés között ugyanazon felhasználó és eszköz kombinációja lehetővé teszi.<br /><br /> Az alábbiakban az alapszintű használati szabályok, amelyek a alkalmazni ezt a fejlécet.<br /><ul><li>Minden egyes felhasználó esetében használja az alkalmazást az eszközön rendelkeznie kell egy egyedi, a Bing létrehozott ügyfél-azonosítót.<br /><br/>Ha a kérés nem adja meg ezt a fejlécet, a Bing létrehoz egy Azonosítót, és visszaadja az X-MSEdge-ClientID fejlécet. Az egyetlen alkalom, hogy a kérelem kell tartalmazza ezt a fejlécet a felhasználó használja az alkalmazást az eszközön lévő első alkalommal.<br /><br/></li><li>Az ügyfél-azonosító használata minden egyes Bing API-kérelem, amely az alkalmazás lehetővé teszi a felhasználó az eszközön.<br /><br/></li><li>**Figyelem:** gondoskodnia kell arról, hogy az ügyfél-azonosító ne legyen összekapcsolhatónak, bármely forrásának hitelesíthetőnek felhasználóifiók-adatokat.</li><br/><li>Megőrzése az ügyfél-azonosítót. Egy böngészőalkalmazásban Azonosítóját megőrizni, használjon állandó HTTP cookie-k, használja az összes munkamenetek között. Ne használjon egy munkamenetcookie-t. Más alkalmazások, például a mobilalkalmazások esetében használja az eszköz tartós tároláshoz megőrizni az azonosítója.<br /><br/>A következő alkalommal, amikor a felhasználó használja az alkalmazást, hogy az eszközről, amely megőrizte ügyfél-azonosító beszerzése.</li></ul><br /> **Megjegyzés:** Bing-válaszok is, vagy nem feltétlenül tartalmazzák ezt a fejlécet. Ha a válasz tartalmazza ezt a fejlécet, rögzítheti az ügyfél-Azonosítót, és használhatja azt a felhasználót az eszközön lévő összes további Bing kérelmet.<br /><br /> **Megjegyzés:** Ha adja meg az X-MSEdge-ClientID, nem tartalmazhat-e be a cookie-kat a kérésben.|  
|<a name="clientip" />X-MSEdge-Ügyfélip|Nem kötelező kérelem fejléce.<br /><br /> Az ügyféleszköz IPv4 vagy IPv6-cím. Az IP-cím a felhasználó földrajzi helye felderítésére szolgál. A Bing biztonságos keresési viselkedés meghatározására használja a helyre vonatkozó adatokat.<br /><br /> **Megjegyzés:** bár nem kötelező, akkor arra biztatjuk mindig adja meg ezt a fejlécet és az X-Search-Location fejlécet.<br /><br /> A cím nem rejtse fel (például úgy, hogy az utolsó oktettet módosítása 0). A cím eredmények a helyen nincs folyamatban bárhol az eszköz tényleges hely közelében obfuscating, emiatt a Bing szolgálja ki a hibás eredményeket.|  
|<a name="location" />A hely keresése X|Nem kötelező kérelem fejléce.<br /><br /> Kulcs/érték párok, amelyek bemutatják, az ügyfél földrajzi helye pontosvesszővel tagolt listája. A Bing a helyre vonatkozó adatokat használ, annak meghatározásához, hogy biztonságos keresés és a megfelelő helyi tartalom. Adja meg a kulcs/érték pár, \<kulcs\>:\<érték\>. Az alábbiakban a kulcsokat, amelyekkel a felhasználó helyét adja meg.<br /><br /><ul><li>szél&mdash;az ügyfél helye fokban szélességét. A földrajzi szélesség kell lennie, nagyobb vagy egyenlő-90.0 és kisebb vagy egyenlő, mint +90.0. Negatív értékek azt jelzik, déli földrajzi szélesség és pozitív értékek azt jelzik, Észak-szélességet.<br /><br /></li><li>hosszú&mdash;az ügyfél helye fokban hosszúságát. Lehet, hogy a földrajzi hosszúság nagyobb vagy egyenlő-180.0 és kisebb vagy egyenlő, mint +180.0. Negatív értékek azt jelzik, nyugati hosszúságot, és pozitív értékek azt jelzik, kelet-hosszúságot.<br /><br /></li><li>re&mdash; a radius, a mérőszámok, amely megadja a koordináták a vízszintes pontosságát. Írjon az eszköz helye szolgáltatás által visszaadott értéket. Tipikus értékek 22m, a GPS és Wi-Fi, esetében a cella tower keresztárfolyam 380m és az IP-címkeresés 18,000m lehet.<br /><br /></li><li>TS&mdash; az UTC UNIX timestamp, amikor az ügyfél volt-e a helyen. (A UNIX-időbélyegző az 1970. január 1. óta eltelt másodpercek számát.)<br /><br /></li><li>a fő&mdash;nem kötelező. Az ügyfél relatív fejléc vagy utazási irányát. Adja meg a 0 és 360, a számbavételi képest igaz északi elforgatása jobbra fok utazási irányát. Adja meg a kulcs csak akkor, ha a `sp` kulcs nem nulla.<br /><br /></li><li>SP&mdash; a vízszintes sebesség (sebesség), a másodpercenkénti, az ügyféleszközön utazik méterben.<br /><br /></li><li>ALT&mdash; a magasság, az ügyféleszközön, a mérőszámok.<br /><br /></li><li>vannak&mdash;nem kötelező. A radius méterben, amely meghatározza a koordináták a függőleges pontosságát. RADIUS alapértelmezés szerint 50 alapján. Adja meg ezt a kulcsot csak akkor, ha adja meg a `alt` kulcsot.<br /><br /></li></ul> **Megjegyzés:** bár ezek a kulcsok megadása nem kötelező, a további adatokat ad meg, annál pontosabb helyet eredménye.<br /><br /> **Megjegyzés:** hosszúan mindig adja meg a felhasználó földrajzi helyét. A hely megadása akkor kifejezetten fontos, ha az ügyfél IP-cím nem tükrözik a felhasználó fizikai helye (például, ha az ügyfél használja a VPN). Az optimális eredmények elérése érdekében akkor tartalmaznia kell ezt a fejlécet és az X-MSEdge-Ügyfélip fejléc, de legalább ezt a fejlécet kell tartalmaznia.|

> [!NOTE] 
> Ne feledje, hogy a vonatkozó törvényeket, beleértve a következő fejlécek az vonatkozó megfelelőségi a használati feltételek szükséges. Ha például bizonyos országokban, például Európában, nincsenek felhasználói beleegyezés előtt bizonyos követési eszközök elhelyezése a felhasználói eszközök beszerzése.
  

## <a name="query-parameters"></a>Lekérdezési paraméterek  
A kérelem lekérdezési paraméterek tartalmazhat. Tekintse meg a szükséges oszlop, paraméter szükséges. URL-címet kell kódolása a lekérdezési paramétereket.  
  
  
|Name (Név)|Érték|Típus|Szükséges|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|A piacon, honnan származnak az eredményeket. <br /><br />Piaci a lehetséges értékek listáját lásd: [piaci kódok](#market-codes).<br /><br /> **Megjegyzés:** az URL-cím előzetes API jelenleg csak támogatja az en-us piacon és a nyelvet.<br /><br />|Sztring|Igen|  
|<a name="query" />válaszok|Az előzetes verzióra az URL-cím|Sztring|Igen|  
|<a name="responseformat" />responseFormat|Az adathordozó-típus használata a válaszhoz. A kis-és értékek a következők.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Az alapértelmezett érték a JSON. A JSON-fájllal kapcsolatos információk objektumok, hogy a válasz tartalmazza, lásd: [Válaszobjektumok](#response-objects).<br /><br />  Ha JsonLd adja meg, a válasz törzse tartalmazza a keresési eredményeket tartalmazó JSON-LD objektumok. A JSON-LD kapcsolatos információkért lásd: [JSON-LD](http://json-ld.org/).|Sztring|Nem|  
|<a name="safesearch" />biztonságos keresés|Felnőtteknek szóló tartalmak szűrésére használt szűrő. A kis-és szűrő lehetséges értékek a következők.<br /><ul><li>Ki&mdash;weblapok és felnőtteknek szóló szöveg, képek és videók adja vissza.<br /><br/></li><li>Mérsékelt&mdash;felnőtt szöveget, de nem felnőtt lemezképek vagy videók weblapok adja vissza.<br /><br/></li><li>A szigorú&mdash;weblapok és felnőtteknek szóló szöveg, képek és videók nem adott vissza.</li></ul><br /> Alapértelmezett értéke közepes.<br /><br /> **Megjegyzés:** , ha a kérelem egy piaci származik, a Bing felnőtt szabályzat megköveteli, hogy `safeSearch` értéke Strict, a Bing figyelmen kívül hagyja a `safeSearch` értékét, és szigorú használja.<br/><br/>**Megjegyzés:** használatakor a `site:` operátor, annak az esélyét, hogy a válasz tartalmazhat felnőtt tartalom, függetlenül attól, hogy mi van a `safeSearch` lekérdezési paraméter értéke. Használat `site:` csak akkor, ha tisztában a webhely tartalmának, és a forgatókönyv támogatja a lehetőségét, felnőtt tartalom. |Sztring|Nem|  
|<a name="setlang" />setLang|A felhasználói felület karakterláncokat használni kívánt nyelvet. Válassza ki a nyelvet, az ISO 639-1-2 levelek nyelvi kód használatával. Például angol nyelvi kódját áll EN. Az alapértelmezett érték EN (angol nyelven).<br /><br /> Bár nem kötelező, mindig adja meg a nyelvet. Általában beállítása `setLang` által megadott nyelvre `mkt` , kivéve, ha a felhasználó szeretne-e a felhasználói felület karakterláncok egy másik nyelven jelenik meg.<br /><br /> Ez a paraméter és a [Accept-nyelv](#acceptlanguage) fejléc, egymást kölcsönösen kizáró&mdash;ne adja meg mindkettőt.<br /><br /> A felhasználói felület karakterlánc egy karakterláncérték, amely egy címkét egy felhasználói felületen. A JSON-válasz objektumok nincsenek néhány felhasználói felület karakterláncokat. A válaszobjektumok Bing.com tulajdonságait mutató hivatkozásokat is, a a megadott nyelvre a alkalmazni.|Sztring|Nem| 


## <a name="response-objects"></a>Válasz objektumok  
A válasz sémája vagy egy [weblap] vagy byl vrácen Prvek, ahogy a webes keresési API-t. Ha a kérelem meghiúsul, a legfelső szintű objektum a [byl vrácen Prvek](#errorresponse) objektum.


|Objektum|Leírás|  
|------------|-----------------|  
|[Weblap]|Legfelső szintű JSON-objektum, amely tartalmazza az előzetes verzió attribútumai.|  
|[(Tény)]|Legfelső szintű JSON-objektum, amely tartalmazza a tényeket.| 
|[Entitások|Legfelső szintű JSON-objektum, amely tartalmazza az entitás adatai.| 

  
### <a name="error"></a>Hiba  
Határozza meg a következő hiba történt.  
  
|Elem|Leírás|Típus|  
|-------------|-----------------|----------|  
|<a name="error-code" />Kód|A hiba kódja, amely azonosítja a hiba kategóriáját. Lehetséges kódok listáját lásd: [hibakódok](#error-codes).|Sztring|  
|<a name="error-message" />üzenet|A hiba leírása.|Sztring|  
|<a name="error-moredetails" />moreDetails|Egy leírást, amely a hibával kapcsolatos további információkat biztosít.|Sztring|  
|<a name="error-parameter" />a paraméter|A lekérdezési paraméter, amely a hibát okozó a kérésben.|Sztring|  
|<a name="error-subcode" />Alkód|A hiba kódja, amely azonosítja a hibát. Például ha `code` InvalidRequest, akkor `subCode` ParameterInvalid vagy ParameterInvalidValue is lehet. |Sztring|  
|<a name="error-value" />érték|A lekérdezési paraméter értéke, amely nem érvényes.|Sztring|  
  

### <a name="errorresponse"></a>Byl vrácen Prvek  
A legfelső szintű objektum, amely a válasz tartalmazza, ha a kérés nem teljesíthető.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Mutató típusa.|Sztring|  
|<a name="errors" />hibák|Miért nem sikerült a kérelem miatt hibák listája.|[Hiba](#error)]|  

  
  
### <a name="license"></a>Licenc  
Határozza meg a licenc, amely alatt a szöveges vagy fénykép használható.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|név|A licenc neve.|Sztring|  
|url|Egy webhely, ahol a felhasználó kaphat-e további információ a licenc URL-címe.<br /><br /> A nevét és URL-cím használatával hivatkozás létrehozása.|Sztring|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
Licenc tesznek elérhetővé; ilyenek szerződéses szabályt definiálja.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Típus mutató LicenseAttribution értékre van állítva.|Sztring|  
|licenc|A licenc, amely alatt a tartalom is használható.|[Licenc](#license)|  
|licenseNotice|A licenc mellett a megcélzott mező megjelenítéséhez. Ha például "CC biztonsági Társítás licenc szöveg".<br /><br /> A licenc nevét és URL-címet használja a `license` mezőt, a webhely, amelyen a licenc részleteit mutató hivatkozás létrehozása. Ezután cserélje le a licenc neve az a `licenseNotice` karakterláncot (például CC-a-SA) az újonnan létrehozott hivatkozás.|Sztring|  
|mustBeCloseToContent|Logikai érték, amely meghatározza, hogy a szabály a tartalmát kell helyezni a közelében a mezőt, amely a szabály vonatkozik. Ha **igaz**, a tartalma közelében kell elhelyezni. Ha **hamis**, vagy ez a mező nem létezik, a tartalmát a hívó belátása szerint is elhelyezhető.|Logikai|  
|targetPropertyName|Az a mező neve, amely a szabály vonatkozik.|Sztring|  
  

### <a name="link"></a>Hivatkozás  
Az összetevők a hivatkozás határozza meg.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Mutató típusa.|Sztring|  
|szöveg|A megjelenítendő szöveg.|Sztring|  
|url|EGY URL-CÍMET. Az URL-címet használja, és hivatkozás létrehozása szöveg megjelenítéséhez.|Sztring|  
  

### <a name="linkattribution"></a>LinkAttribution  
Hivatkozás tesznek elérhetővé; ilyenek szerződéses szabályt definiálja.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Típus mutató LinkAttribution értékre van állítva.|Sztring|  
|mustBeCloseToContent|Logikai érték, amely meghatározza, hogy a szabály a tartalmát kell helyezni a közelében a mezőt, amely a szabály vonatkozik. Ha **igaz**, a tartalma közelében kell elhelyezni. Ha **hamis**, vagy ez a mező nem létezik, a tartalmát a hívó belátása szerint is elhelyezhető.|Logikai|  
|targetPropertyName|Az a mező neve, amely a szabály vonatkozik.<br /><br /> Ha egy cél nincs megadva, a tesznek elérhetővé; ilyenek az entitás egészére vonatkozik, és közvetlenül az a entitás bemutató következő üzenetnek kell megjelennie. Ha több szöveg- és tesznek elérhetővé; ilyenek szabály, amely nem adja meg a cél, kell összefűzni őket és megjeleníti őket használatával egy "adatait:" címkét. Például "származó adatok < szolgáltató Név1\> &#124; < szolgáltató name2\>".|Sztring|  
|szöveg|A felhasználási szöveg.|Sztring|  
|url|A szolgáltató webhelyéről URL-címe. Használat `text` és hivatkozás létrehozása URL-CÍMÉT.|Sztring|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Határozza meg az adathordozó felhasználási szerződéses szabály.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Típus mutató MediaAttribution értékre van állítva.|Sztring|  
|mustBeCloseToContent|Logikai érték, amely meghatározza, hogy a szabály a tartalmát kell helyezni a közelében a mezőt, amely a szabály vonatkozik. Ha **igaz**, a tartalma közelében kell elhelyezni. Ha **hamis**, vagy ez a mező nem létezik, a tartalmát a hívó belátása szerint is elhelyezhető.|Logikai|  
|targetPropertyName|Az a mező neve, amely a szabály vonatkozik.|Sztring|  
|url|A médiatartalmak hivatkozás létrehozásához használt URL-címe. Például ha a cél egy képet, lenne használhatja az URL-címet, hogy a kép kattintható.|Sztring|  
  
  
  
### <a name="organization"></a>Szervezet  
Határozza meg a kiadó.  
  
Vegye figyelembe, hogy a közzétevő neve vagy a webhely vagy mindkét előfordulhat, hogy biztosítani.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|név|A közzétevő nevét.|Sztring|  
|url|A kiadó webhelye URL-címe.<br /><br /> Vegye figyelembe, hogy a közzétevő nem ad egy webhelyet.|Sztring|  
  
  

### <a name="webpage"></a>Weblap  
Meghatározza, milyen kapcsolatos információkat egy előzetes verzióban érhető el a weblapot.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|
|név|Az oldal címe, nem feltétlenül a HTML-cím|Sztring|
|url|Az URL-cím, amely ténylegesen volt bejárt (kérelem előfordulhat, hogy felvette a átirányítások)|Sztring|  
|leírás|Az oldal és a tartalom rövid leírása|Sztring|  
|isFamilyFriendly|A legpontosabb a webes index; eleme valós idejű fetches tegye alapján kizárólag az URL-címet, és nem az oldal tartalmát az észlelés|logikai|
|primaryImageOfPage/contentUrl|Tartalmazza az előzetes verzióban érhető el egy reprezentatív kép URL-címe|Sztring| 
  
  
### <a name="querycontext"></a>QueryContext  
Meghatározza a Bing a kéréshez használt lekérdezési környezet.  
  
|Elem|Leírás|Típus|  
|-------------|-----------------|----------|  
|adultIntent|Logikai érték, amely azt jelzi, hogy a megadott lekérdezés felnőtteknek szánt tartalom. Az érték **igaz** amennyiben a lekérdezés felnőtteknek szánt; ellenkező esetben **hamis**.|Logikai|  
|alterationOverrideQuery|A lekérdezési karakterlánc használatával kényszerítheti a Bing, az eredeti karakterlánc használatához. Például, ha a lekérdezési karakterlánc *saling szélirányban fekvő*, a felülbírálási karakterlánc lesz *+ saling szélirányban fekvő*. Ne felejtse el a lekérdezési karakterláncot, amelynek eredménye kódolása *% 2Bsaling + szélirányban fekvő*.<br /><br /> Ez a mező része, csak akkor, ha az eredeti lekérdezési karakterláncot tartalmaz egy gépelési hibát.|Sztring|  
|alteredQuery|A lekérdezési karakterlánc, a lekérdezés végrehajtása a Bing által használt. Bing formában lekérdezési karakterláncot használ, ha az eredeti lekérdezési karakterláncot a helyesírási hibákat tartalmaz. Például, ha a lekérdezési karakterlánc `saling downwind`, a módosított lekérdezési karakterlánc lesz `sailing downwind`.<br /><br /> Ez a mező része, csak akkor, ha az eredeti lekérdezési karakterláncot tartalmaz egy gépelési hibát.|Sztring|  
|askUserForLocation|Logikai érték, amely azt jelzi, hogy szükséges-e a Bing pontos eredményeket a felhasználó földrajzi helye. Ha a felhasználó földrajzi helye az a [X-MSEdge-Ügyfélip](#clientip) és [hely keresése X](#location) fejlécek, figyelmen kívül hagyhatja ezt a mezőt.<br /><br /> Hely figyelembe lekérdezésekhez, például az "a mai időjárás-" vagy "éttermek a közelben", amelyet a felhasználó földrajzi helye pontos eredményeket kell Ez a mező értéke **igaz**.<br /><br /> Hely figyelembe lekérdezések, amelyek tartalmazzák a helyre (például "Seattle weather"), ez a mező értéke **hamis**. Ez a mező értéke is **hamis** lekérdezések, amelyek nem helyfüggő, például a "ajánlott eladók".|Logikai|  
|originalQuery|A kérésben megadott lekérdezési karakterlánc.|Sztring|  

### <a name="identifiable"></a>Azonosításra alkalmas
|Name (Név)|Érték|Típus|  
|-------------|-----------------|----------|
|id|Egy erőforrás-azonosítója|Sztring|
 
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
|answerType|A válasz, amely tartalmazza az elem megjelenítésére. Ha például híreket.<br /><br />A típus használatával találja a választ a SearchResponse objektumban. A típus egy SearchResponse mező nevét.<br /><br /> Azonban a választípushoz használja, csak akkor, ha ez az objektum tartalmazza az érték mezőbe; Ellenkező esetben figyelmen kívül hagyhatja azt.|Sztring|
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
|í_rja be|Írja be a mutatót, amely SearchResponse van beállítva.|Sztring|  
|Weblap|JSON-objektum, amely meghatározza az előzetes verzió|sztring|  
  
  
### <a name="textattribution"></a>TextAttribution  
Egyszerű szöveges tesznek elérhetővé; ilyenek szerződéses szabályt definiálja.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Típus mutató TextAttribution értékre van állítva.|Sztring|  
|szöveg|A felhasználási szöveg.<br /><br /> Szöveg tesznek elérhetővé; ilyenek az entitás egészére vonatkozik, és közvetlenül az a entitás bemutató következő üzenetnek kell megjelennie. Ha több szöveg vagy hivatkozás tesznek elérhetővé; ilyenek szabályt, amely nem adja meg a cél, kell összefűzni őket és megjeleníti őket használatával egy "adatait:" címkét.|Sztring| 


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
- [C# gyorsútmutató](c-sharp-quickstart.md)
- [Java a rövid útmutató](java-quickstart.md)
- [Csomópont a rövid útmutató](node-quickstart.md)
- [Python a rövid útmutató](python-quickstart.md)

