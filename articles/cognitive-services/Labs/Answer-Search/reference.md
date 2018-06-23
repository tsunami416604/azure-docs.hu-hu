---
title: Válasz keresési referencia - Microsoft kognitív szolgáltatások projekt |} Microsoft Docs
description: A projekt válasz keresése végpont hivatkozás.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 8c95fac0c031ec62a9d98d6c3278bd3b3f345140
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348727"
---
# <a name="project-answer-search-v7-reference"></a>Válasz keresési v7 projekthivatkozást

Bing válasz SearchAPI lekérdezési paramétert fogad, és adja vissza egy `searchResponse` rendelkező `answerType`: `facts` vagy `entities`. 

A válasz keresési API-t használó alkalmazások kérelmet küldeni a végpont URL-címet egy lekérdezési paraméter található megjeleníthető.  A kérelem tartalmaznia kell a `q=searchTerm` paraméter és *Ocp-Apim-előfizetés-kulcs* fejléc.   

A JSON-válasz elemzése a tényeket és a keresés részleteit tartalmazó entitások.

## <a name="endpoint"></a>Végpont
Válasz keresési eredmények kérelmezéséhez kérelmet küld a következő végpontot. A fejlécek és URL-cím paraméterek segítségével további specifikációk meghatározása.

GET. végpont: 
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

````

A kérelem kell a HTTPS protokollt használják, és tartalmazza a következő lekérdezésparaméter a következő:
-  q =<URL> -a Keresés az objektumhoz tartozó lekérdezés

Bemutatják, hogyan kérelmeket példákért lásd [C# gyors üzembe helyezés](c-sharp-quickstart.md) vagy [Java gyors üzembe helyezés](java-quickstart.md). 

A következő szakaszok olyan technikai részleteket a válasz objektumok, a lekérdezés-paraméterek és a fejlécek, amelyek hatással vannak a keresési eredmények között. 
  
Kérelmek kell tartalmazó fejlécek kapcsolatos információkért lásd: [fejlécek](#headers).  
  
Tartalmaznia kell kérelmek lekérdezés paraméterekkel kapcsolatos további információkért lásd: [lekérdezési paramétert](#query-parameters).  
  
Információ a JSON-objektumokat, hogy a válasz tartalmazza, a következő témakörben: [válasz objektumok](#response-objects).

A maximális lekérdezési URL-cím hossza 2048 karakter. Győződjön meg arról, hogy az URL-cím hossza nem haladja meg a korlátot, a lekérdezési paraméterek maximális hossza legfeljebb 1500 karakterből kell. Ha az URL-cím hossza meghaladja a 2048 karakter, a kiszolgáló nem található 404 adja vissza.  

Engedélyezett használatát és a megjelenített eredmények kapcsolatos információkért lásd: [használja, és megjeleníti a követelmények](use-display-requirements.md). 

> [!NOTE]
> Néhány kérelemfejléc, így a lényeges más keresési API-k nincsenek hatással a kép URL-címe
> - Pragma – a hívó nem tudja befolyásolni kép URL-címet használ-e gyorsítótár
> - A Cache-Control – a hívó nem tudja befolyásolni kép URL-címet használ-e gyorsítótár
> - Felhasználói ügynök

> Is néhány paraméter nem jelenleg jelentéssel bíró URL-cím előnézeti API-hoz, de a jövőben jobb globalizálás használható. 
 
## <a name="headers"></a>Fejlécek  
Az alábbiakban a fejlécek, amely egy kérelem-válasz tartalmazza.  
  
|Fejléc|Leírás|  
|------------|-----------------|  
|Elfogadás|Nem kötelező kérelemfejlécet.<br /><br /> Az alapértelmezett médiatípus nincs az application/json. Adja meg, hogy a válasz a [JSON-LD](http://json-ld.org/), állítsa be az Accept fejlécet alkalmazás/ld + json.|  
|<a name="acceptlanguage" />Fogadja el nyelv|Nem kötelező kérelemfejlécet.<br /><br /> Nyelvek használandó felhasználói felület karakterláncok vesszővel tagolt listáját. A lista van csökkenő sorrendben. További információ, beleértve a várt formátum: [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ezt a fejlécet, és a [setLang](#setlang) lekérdezésparaméter, egymást kölcsönösen kizáró&mdash;nem adható meg egyszerre.<br /><br /> Ha megadta ezt a fejlécet, meg kell adni a [cc](#cc) lekérdezési paraméter. Annak megállapításához, a piacon vissza az eredményeket, a Bing megkeresi a listából, és egyesíti a első támogatott nyelvet használja a `cc` paraméter értékét. Ha a lista nem tartalmazza a támogatott nyelvi, Bing megkeresi a legközelebbi nyelvet és a piaci, amely támogatja a kérelem, vagy használja egy összesített vagy alapértelmezett piaci a eredmény elérése érdekében. Annak megállapításához, a Bing használt piacára, tekintse meg a piacra jutási BingAPIs fejléc.<br /><br /> Használja ezt a fejlécet, és a `cc` lekérdezésparaméter csak akkor, ha több nyelvet ad meg. Ellenkező esetben használja a [mkt](#mkt) és [setLang](#setlang) lekérdezési paramétert.<br /><br /> A felhasználói felület karakterlánc: a felhasználói felületen címkeként használt karakterlánc. A JSON-válasz objektumok nincsenek néhány felhasználói felület karakterláncok. A válasz objektumok Bing.com tulajdonságok mutató hivatkozások alkalmazza a megadott nyelven.|  
|<a name="market" />Piacra jutási BingAPIs|Válaszfejléc.<br /><br /> A piacon, a kérelem által használt. Az űrlap \<languageCode\>-\<országhívószám\>. Például: hu-hu.|  
|<a name="traceid" />BingAPIs-TraceId|Válaszfejléc.<br /><br /> Az Eseménynapló-bejegyzés, amely tartalmazza a részleteket a kérelem az azonosítója. Ha hiba lép fel, rögzítése ezt. Ha nem határozza meg és hárítsa el a problémát, tartalmazzák ezt az Azonosítót és az egyéb adatokat, hogy a támogatási csapatával.|  
|<a name="subscriptionkey" />Az OCP-Apim-előfizetés-kulcs|Szükséges kérelemfejlécet.<br /><br /> Az Előfizetés kulcs, hogy az ehhez a szolgáltatáshoz, a regisztráció során [kognitív szolgáltatások](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Nem kötelező fejléc<br /><br /> Alapértelmezés szerint a Bing gyorsítótárazott tartalom visszaadása, ha elérhető. Gyorsítótárazott tartalom visszaküldésével a Bing megakadályozása érdekében állítsa a Pragma fejléc no-cache (például Pragma: no-cache).
|<a name="useragent" />Felhasználói ügynök|Nem kötelező kérelemfejlécet.<br /><br /> A felhasználói ügynök származó a kérelmet. Bing optimalizált élményt biztosít a mobil felhasználók a felhasználói ügynök használja. Bár nem kötelező, meg hosszúan mindig meg ezt a fejlécet.<br /><br /> A felhasználói ügynök által a leggyakrabban használt böngészők ugyanazt a karakterláncot kell lennie. Felhasználói ügynök kapcsolatos információkért lásd: [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> A következő példák felhasználói ügynök karakterláncok.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibilis; MSIE 10.0; Windows Phone 8.0; Trident vagy 6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/MÉZESKALÁCS) AppleWebKit/533.1 (KHTML; például gekkó) vagy 4.0-s verziójú Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone operációs rendszer 6_1, például a Mac OS X) AppleWebKit/536.26 (KHTML; például gekkó) Mobile/10B142 iPhone4; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (a Windows NT 6.3; WOW64; Trident/7.0; Touch; RV:11.0), például gekkó<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU-OS 7_0 például a Mac OS X) AppleWebKit/537.51.1 (például gekkó KHTML) verzió/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Nem kötelező kérelem-válasz fejléce.<br /><br /> Bing felhasználók biztosít a konzisztens viselkedése Bing API-hívások használja ezt a fejlécet. Bing gyakran repülőútra új szolgáltatásait és fejlesztéseit, és különböző járatok forgalom hozzárendelése kulcsaként használ az ügyfél-azonosító. Címzett több kérés során a felhasználó nem használja az ugyanazon ügyfél-azonosító, ha a Bing is hozzárendelheti a felhasználó több ütköző járatok. Több ütköző járatok lett hozzárendelve inkonzisztens felhasználói élményt is járhat. Például ha a második kérelmet egy másik repülési-hozzárendelést, mint az első, a felhasználói élmény lehet váratlan. Is, a Bing használatával az ügyfél-azonosító személyessé tétele érdekében, hogy az ügyfél webes eredmények keresési előzmények gazdagabb élményt biztosítva a felhasználói azonosítót.<br /><br /> Bing is használja ezt a fejlécet a tevékenység egy ügyfél-azonosítót. által generált elemzésével eredmény rangsort javítása érdekében A fontos fejlesztéseket kézbesíteni Bing API-k és pedig lehetővé teszi, hogy a nagyobb kattintások sebesség API fogyasztó eredmények magasabb színvonalú segít.<br /><br /> **Fontos:** bár nem kötelező, vegye figyelembe ezt a kötelező fejlécet. Megőrzése az ügyfél-azonosító címzett több kérés során ugyanazon felhasználó és eszköz kombináció lehetővé teszi a 1.) az API fogyasztó úgy, hogy a következetes felhasználói élményt biztosít, és 2) magasabb kattintások díjszabás keresztül eredmények magasabb színvonalú fogadjon a Bing API-k.<br /><br /> Az alábbiakban az alapvető használati szabályok ezt a fejlécet.<br /><ul><li>Minden olyan felhasználóhoz, használja az alkalmazást az eszközön rendelkeznie kell egy egyedi, a Bing létrehozott ügyfél-azonosítót.<br /><br/>Ha a kérelem nem adja meg ezt a fejlécet, a Bing Azonosítót hoz létre, és adja vissza, az X-MSEdge-ClientID válaszfejléc. A csak, hogy nem bele kell foglalni ezt a fejlécet a kérelem, az első alkalommal a felhasználó használja az alkalmazást azokon az eszközökön.<br /><br/></li><li>Az ügyfél-Azonosítót használja az alkalmazás lehetővé teszi a felhasználó az eszköz minden egyes Bing API-kérelemhez.<br /><br/></li><li>**Figyelem:** meg kell győződnie arról, hogy az ügyfél-azonosító nem összekapcsolhatónak forrásának hitelesíthetőnek felhasználói fiók adatok számára.</li><br/><li>Megőrzése az ügyfél-azonosítót. Továbbra is fennáll az azonosító egy webböngésző alkalmazást, állandó HTTP cookie-k segítségével ellenőrizze az azonosító olyan munkamenetekben. Ne használjon egy munkamenetcookie-t. Más alkalmazások, például a mobile apps szolgáltatásban használja az eszköz állandó tároló megőrizni a azonosítóját.<br /><br/>A felhasználó használja az alkalmazást azokon az eszközökön, amikor legközelebb megőrizte ügyfél-azonosító beolvasása.</li></ul><br /> **Megjegyzés:** Bing válaszok is, vagy nem tartalmazza ezt a fejlécet. A válasz tartalmazza ezt a fejlécet, ha rögzíti az ügyfél-Azonosítót, és a későbbi Bing azokon az eszközökön a felhasználó használja azt.<br /><br /> **Megjegyzés:** Ha adja meg az X-MSEdge-ClientID, nem tartalmazhat-e be a cookie-kat a kérelemben.|  
|<a name="clientip" />X-MSEdge-Ügyfélip|Nem kötelező kérelemfejlécet.<br /><br /> Az ügyféleszköz IPv4 vagy IPv6-cím. Az IP-címet a felhasználó földrajzi helye felderítésére szolgál. Bing a Tartózkodásihely-adatok a biztonságos keresési viselkedés meghatározására használja.<br /><br /> **Megjegyzés:** bár nem kötelező, meg hosszúan mindig adja meg ezt a fejlécet, és az X-keresési-helyet megjelölő fejlécet.<br /><br /> Nem takarják a címet (például, ha megváltoztatja az utolsó oktett 0). A cím eredmények nem bárhol mellett az eszköz tényleges helyére a helyen obfuscating, emiatt a Bing hibás eredmények szolgál.|  
|<a name="location" />X keresése|Nem kötelező kérelemfejlécet.<br /><br /> Az ügyfél földrajzi helye leíró kulcs/érték párok pontosvesszővel elválasztott listája. Bing használja a helyére vonatkozó információkat, annak meghatározásához, hogy biztonságos keresés és a kapcsolódó helyi tartalom. Adja meg, mint a kulcs/érték pár \<kulcs\>:\<érték\>. Az alábbiakban a kulcsokhoz, használja a felhasználó helyének megadására.<br /><br /><ul><li>LAT&mdash;a szélesség fok, az ügyfél helye. A szélesség kell lennie nullánál-90.0 és kisebb vagy egyenlő, mint +90.0. Negatív értékek azt jelzik, déli földrajzi szélesség és pozitív értékek azt jelzik, északi szélességet.<br /><br /></li><li>hosszú&mdash;az ügyfél helye fokban a hosszúsági koordinátákkal meghatározni. A hosszúság kell lennie nullánál-180.0 és kisebb vagy egyenlő, mint +180.0. Negatív értékek azt jelzik, Nyugat hosszúságot, és pozitív értékek azt jelzik, eastern hosszúságot.<br /><br /></li><li>-&mdash; a radius, a mérőszámok, adja meg a koordináták a vízszintes pontosságát. Az eszköz helymeghatározó szolgáltatás által visszaadott értéket átadni. Tipikus értékek 22m GPS/Wi-Fi, a cella torony keresztárfolyam 380m és a fordított IP-címkeresés 18,000m lehet.<br /><br /></li><li>TS&mdash; az UTC UNIX timestamp, amikor az ügyfél volt-e az adott helyen. (A UNIX-időbélyeg érték 1970. január 1. óta eltelt percek számát.)<br /><br /></li><li>HEAD&mdash;nem kötelező. Az ügyfél relatív címsor vagy utazás irányát. Adja meg a 0 – 360, a számbavételi jobbra viszonyítva igaz északi fok utazás irányát. Adja meg a kulcs csak akkor, ha a `sp` kulcs értéke nem nulla.<br /><br /></li><li>SP&mdash; a vízszintes sebesség (sebesség), az a másodpercenkénti, az ügyféleszközök utazik mérőszámok.<br /><br /></li><li>ALT&mdash; ügyfél eszköz mérőszámok magasság.<br /><br /></li><li>vannak&mdash;nem kötelező. A radius, a mérőszámok, amely meghatározza a koordináták a függőleges pontosságát. RADIUS az alapértelmezett érték 50 kilométerben. Adja meg ezt a kulcsot csak akkor, ha megadja a `alt` kulcs.<br /><br /></li></ul> **Megjegyzés:** annak ellenére, hogy ezek a kulcsok nem kötelező, a további információt ad meg, annál pontosabb a hely eredményei.<br /><br /> **Megjegyzés:** hosszúan mindig adja meg a felhasználó földrajzi helye. Különösen fontos, ha az ügyfél IP-cím nem pontosan mutassa a felhasználó fizikai helyét (például, ha az ügyfél használ VPN) biztosít a hely. Az optimális eredmény ezt a fejlécet, és az X-MSEdge-Ügyfélip fejléc kell foglalni, de mindenképpen tartalmaznia kell ezt a fejlécet.|

> [!NOTE] 
> Ne feledje, hogy a vonatkozó törvényeket, beleértve az ezek a fejlécek vonatkozó megfelelőségi a használati feltételek esetében. Például bizonyos országokban, Európa, például nincsenek beszerzése a felhasználói hozzájárulás előtt bizonyos követési eszközök felhasználói eszközökön.
  

## <a name="query-parameters"></a>Lekérdezési paraméterek  
A kérelem tartalmazhatják a következő lekérdezési paramétereket. Tekintse meg a szükséges oszlop kötelező paraméterhez. URL-címet kell kódolni a lekérdezési paramétereket.  
  
  
|Name (Név)|Érték|Típus|Szükséges|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|A piacon, honnan származnak az eredményeket. <br /><br />A lehetséges piaci értékek listáját lásd: [piaci kódok](#market-codes).<br /><br /> **Megjegyzés:** az URL-cím előnézeti API jelenleg csak a támogatja en-us piaci és nyelvi.<br /><br />|Sztring|Igen|  
|<a name="query" />A Q|Az URL-cím, az előzetes|Sztring|Igen|  
|<a name="responseformat" />responseFormat|Az adathordozó típusát, a válasz használatára. A következő értékeket a lehetséges azonban nem.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Az alapértelmezett érték a JSON-NÁ. A JSON információt objektumok, hogy a válasz tartalmazza, a következő témakörben: [válasz objektumok](#response-objects).<br /><br />  Ha JsonLd ad meg, az adott válasz törzsének JSON-LD objektumokat tartalmazza, amelyek tartalmaznak a keresési eredmények között. A JSON-LD kapcsolatos információkért lásd: [JSON-LD](http://json-ld.org/).|Sztring|Nem|  
|<a name="safesearch" />biztonságos keresés|Felnőtt tartalom szűrésére használt szűrő. A következő értékeket a lehetséges azonban nem szűrő.<br /><ul><li>Ki&mdash;felnőtt szöveg, képek vagy videók weblapok adja vissza.<br /><br/></li><li>Mérsékelt&mdash;felnőtt szöveget, de nem felnőtt képek vagy videók weblapok adja vissza.<br /><br/></li><li>Szigorú&mdash;felnőtt szöveg, képek vagy videók weblapok nem adják vissza.</li></ul><br /> Az alapértelmezett érték a közepes.<br /><br /> **Megjegyzés:** Ha a kérelem egy piaci származik, hogy a Bing felnőtt házirend megköveteli, hogy `safeSearch` értéke Strict, a Bing figyelmen kívül hagyja a `safeSearch` értékét, és a Strict használja.<br/><br/>**Megjegyzés:** használatakor a `site:` lekérdezési operátor nincs az esélye, hogy a válasz tartalmazhat felnőtt tartalom, függetlenül attól, hogy mi a `safeSearch` lekérdezési paraméter értéke. Használjon `site:` csak akkor, ha tisztában lehet a tartalmat a helyen, és adott esetben támogatja a felnőtt tartalom lehetőségét. |Sztring|Nem|  
|<a name="setlang" />setLang|A felhasználói felület karakterláncok használni kívánt nyelvet. Adja meg a nyelvet a ISO 639-1-2 levelek nyelvi kódot. Például az angol nyelvi kódját EN. Az alapértelmezett érték EN (angol nyelvű).<br /><br /> Bár nem kötelező, a nyelvi mindig meg kell adnia. Általában beállított `setLang` által megadott azonos nyelvének `mkt` kivéve, ha a felhasználó szeretne-e a felhasználói felület karakterláncok más nyelven jelenik meg.<br /><br /> Ez a paraméter és a [elfogadás-nyelv](#acceptlanguage) fejléc, egymást kölcsönösen kizáró&mdash;nem adható meg egyszerre.<br /><br /> A felhasználói felület karakterlánc: a felhasználói felületen címkeként használt karakterlánc. A JSON-válasz objektumok nincsenek néhány felhasználói felület karakterláncok. A válasz objektumok Bing.com tulajdonságok mutató hivatkozások is, a megadott nyelv alkalmazni.|Sztring|Nem| 


## <a name="response-objects"></a>Válasz objektumok  
A válasz séma vagy egy [weblap] vagy ErrorResponse, ahogy a webes keresés API. A kérés nem teljesíthető, ha a legfelső szintű objektum van-e a [ErrorResponse](#errorresponse) objektum.


|Objektum|Leírás|  
|------------|-----------------|  
|[A weblap]|Felső szintű JSON-objektum Preview attribútumokat tartalmazó.|  
|[Tény]|Felső szintű JSON-objektum, amely adatokat tartalmaznak.| 
|[Entitások|Felső szintű JSON-objektum entitás adatait tartalmazó.| 

  
### <a name="error"></a>Hiba  
Határozza meg a következő hiba történt.  
  
|Elem|Leírás|Típus|  
|-------------|-----------------|----------|  
|<a name="error-code" />Kód|A hibakód, amely azonosítja a hiba kategóriáját. Lehetséges kódok listáját lásd: [hibakódok](#error-codes).|Sztring|  
|<a name="error-message" />üzenet|A hiba leírása.|Sztring|  
|<a name="error-moredetails" />moreDetails|Egy leírást, amely a hibával kapcsolatos további információkat biztosít.|Sztring|  
|<a name="error-parameter" />A paraméter|A következő lekérdezésparaméter a hibát okozó a kérelemben.|Sztring|  
|<a name="error-subcode" />Alkód|Meghatározza a hibát a hibakód. Például ha `code` van InvalidRequest, `subCode` ParameterInvalid vagy ParameterInvalidValue is lehet. |Sztring|  
|<a name="error-value" />érték|A lekérdezési paraméter értéke, amely nem érvényes.|Sztring|  
  

### <a name="errorresponse"></a>ErrorResponse  
A legfelső szintű objektum, amely a válasz tartalmazza, ha a kérelem sikertelen lesz.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Mutató típus.|Sztring|  
|<a name="errors" />hibák|Miért nem sikerült a kérelem miatt hibák listája.|[Hiba](#error)]|  

  
  
### <a name="license"></a>Licenc  
Határozza meg a licenc, amely alatt a szöveges vagy fénykép használható.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|név|A licenc neve.|Sztring|  
|url|Egy webhely, ahol a felhasználó további információkat a licencet szeretne kapni URL-CÍMÉT.<br /><br /> A nevét és URL-cím segítségével hivatkozás létrehozása.|Sztring|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
Licenc attribútumára szerződéses szabályt definiálja.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Típusú javaslat, amely LicenseAttribution van beállítva.|Sztring|  
|licenc|A licenc, amely alatt a tartalom is használható.|[Licenc](#license)|  
|licenseNotice|A licenc a célként megadott mező melletti megjelenítéséhez. Például "szöveg" CC biztonsági Társítás licence.<br /><br /> Használja a licenc nevét és URL-címet a `license` mezőben azt a webhelyet, a licenc részleteit ismerteti hivatkozás létrehozásához. Ezután cserélje le a licenckiszolgáló neve az a `licenseNotice` karakterlánc (például a CC-által-SA), a most létrehozott hivatkozás.|Sztring|  
|mustBeCloseToContent|Logikai érték, amely meghatározza, hogy a szabály tartalmát kell helyezni közelében a mezőt, amelyekre a szabály vonatkozik. Ha **igaz**, a tartalom közel kell elhelyezni. Ha **hamis**, vagy ez a mező nem létezik, a tartalmát a hívó belátása is elhelyezhető.|Logikai|  
|targetPropertyName|Az a mező neve, amely a szabály vonatkozik.|Sztring|  
  

### <a name="link"></a>Hivatkozás  
Az összetevők a hivatkozás határozza meg.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Mutató típus.|Sztring|  
|szöveg|A megjelenített szöveg.|Sztring|  
|url|EGY URL-CÍMET. Az URL-címet használja, és hivatkozás létrehozása szöveg megjelenítése.|Sztring|  
  

### <a name="linkattribution"></a>LinkAttribution  
Meghatározza a hivatkozás attribútumára szerződéses szabályt.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Típusú javaslat, amely LinkAttribution van beállítva.|Sztring|  
|mustBeCloseToContent|Logikai érték, amely meghatározza, hogy a szabály tartalmát kell helyezni közelében a mezőt, amelyekre a szabály vonatkozik. Ha **igaz**, a tartalom közel kell elhelyezni. Ha **hamis**, vagy ez a mező nem létezik, a tartalmát a hívó belátása is elhelyezhető.|Logikai|  
|targetPropertyName|Az a mező neve, amely a szabály vonatkozik.<br /><br /> Ha a cél nincs megadva, módjával az entitás egészére vonatkozik, és az entitás bemutató követő üzenetnek kell megjelennie. Ha több szöveg és a hivatkozás attribútumára szabály, amely nem ad meg egy cél, kell őket összefűzésére és megjelenítésükhöz használja a "adatait:" címke. Például "adatait < szolgáltató Név1\> &#124; < szolgáltató Név2\>".|Sztring|  
|szöveg|A attribútumára szöveget.|Sztring|  
|url|A szolgáltató webhelyéről URL-CÍMÉT. Használjon `text` és hivatkozás létrehozása URL-címet.|Sztring|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Media attribútumára szerződéses szabályt definiálja.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Típusú javaslat, amely MediaAttribution van beállítva.|Sztring|  
|mustBeCloseToContent|Logikai érték, amely meghatározza, hogy a szabály tartalmát kell helyezni közelében a mezőt, amelyekre a szabály vonatkozik. Ha **igaz**, a tartalom közel kell elhelyezni. Ha **hamis**, vagy ez a mező nem létezik, a tartalmát a hívó belátása is elhelyezhető.|Logikai|  
|targetPropertyName|Az a mező neve, amely a szabály vonatkozik.|Sztring|  
|url|Az URL-cím médiatartalmak hivatkozás létrehozására használhatja. Például ha a cél kép, szeretné használni az URL-címet, hogy a lemezkép kattintható.|Sztring|  
  
  
  
### <a name="organization"></a>Szervezet  
Határozza meg a gyártót.  
  
Vegye figyelembe, hogy a közzétevő neve vagy a webhely vagy mindkettő által biztosított.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|név|A közzétevő nevét.|Sztring|  
|url|A közzétevő webhely URL-CÍMÉT.<br /><br /> Vegye figyelembe, hogy a közzétevő nem biztosítanak egy webhelyet.|Sztring|  
  
  

### <a name="webpage"></a>A weblap  
Meghatározza, milyen információkat kapcsolatos egy Preview a weblap.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|
|név|A lap címét, nem feltétlenül a HTML-cím|Sztring|
|url|A ténylegesen lett bejárt URL-CÍMÉT (kérelem előfordulhat, hogy követte-átirányítások)|Sztring|  
|leírás|Rövid leírása a a lap és a tartalom|Sztring|  
|isFamilyFriendly|A webes index; elemek legpontosabb valós idejű fetches tegye az észlelés alapján kizárólag az URL-címet, és nem az oldal tartalmát|logikai|
|primaryImageOfPage/contentUrl|Az előzetes felvenni egy reprezentatív kép URL-címe|Sztring| 
  
  
### <a name="querycontext"></a>QueryContext  
Meghatározza a Bing használt a kérelem lekérdezési környezet.  
  
|Elem|Leírás|Típus|  
|-------------|-----------------|----------|  
|adultIntent|Egy logikai érték, amely azt jelzi, hogy a megadott lekérdezés felnőtt leképezés. Az érték **igaz** amennyiben a lekérdezés felnőtt leképezés; ellenkező esetben **hamis**.|Logikai|  
|alterationOverrideQuery|A lekérdezési karakterlánc használatával kényszerítheti a Bing, az eredeti karakterláncot használva. Például, ha a lekérdezési karakterlánc *saling szélirányban fekvő*, a felülbírálási karakterlánc lesz *+ saling szélirányban fekvő*. Ne felejtse el a lekérdezési karakterláncot, amelynek eredménye kódolása *% 2Bsaling + szélirányban fekvő*.<br /><br /> Ez a mező szerepel, csak akkor, ha az eredeti lekérdezési karakterláncot tartalmaz helyesen adta-e hibát.|Sztring|  
|alteredQuery|A lekérdezési karakterlánc használják a Bing, a lekérdezés végrehajtásához. Bing használ a módosított lekérdezési karakterláncot, ha az eredeti lekérdezési karakterláncot tartalmaz helyesen írta-e. Például, ha a lekérdezési karakterlánc `saling downwind`, a módosított lekérdezési karakterlánc lesz `sailing downwind`.<br /><br /> Ez a mező szerepel, csak akkor, ha az eredeti lekérdezési karakterláncot tartalmaz helyesen adta-e hibát.|Sztring|  
|askUserForLocation|Logikai érték, amely jelzi, hogy szükséges-e a Bing arra, hogy pontos eredményeket a felhasználó helyét. Ha a felhasználó földrajzi helye használatával meg a [X-MSEdge-Ügyfélip](#clientip) és [X-keresési-hely](#location) fejlécek, akkor figyelmen kívül hagyhatja ezt a mezőt.<br /><br /> Hely kompatibilis lekérdezések, például "az aktuális időjárási" vagy "éttermekben közeli", amelyeket a felhasználó földrajzi helye pontos eredményeket, így ez a mező értéke **igaz**.<br /><br /> A hely kompatibilis lekérdezések, amelyek közé tartozik a helye (például "Seattle időjárási"), ez a mező értéke **hamis**. Ez a mező értéke is **hamis** lekérdezések, amelyek nincsenek helyfüggő, például a "legjobb eladók".|Logikai|  
|originalQuery|A kérelemben megadott lekérdezési karakterlánc.|Sztring|  

### <a name="identifiable"></a>Azonosításra alkalmas
|Name (Név)|Érték|Típus|  
|-------------|-----------------|----------|
|id|Egy erőforrás-azonosítója|Sztring|
 
### <a name="rankinggroup"></a>RankingGroup
Határozza meg a keresési eredmények csoport, például a mainline.
|Name (Név)|Érték|Típus|  
|-------------|-----------------|----------|
|elem|A csoport megjelennek a keresési eredmények listáját.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Határozza meg a keresési eredmény elemek megjelenítése.
|Name (Név)|Érték|Típus|  
|-------------|-----------------|----------|
|resultIndex|A válasz megjelenítendő elemének nulla alapú indexét. Ha az elem nem tartalmazza ezt a mezőt, minden elem szerepel a válasz. Összes írt hírcikkeket például megjelennek a hírek válasz.|Egész szám|
|answerType|A választ, amely tartalmazza az elemet megjelenítéséhez. Például híreket.<br /><br />A típus használatával megtalálja a választ a SearchResponse objektumban. A típus egy SearchResponse mező nevét.<br /><br /> A válasz típusa azonban használja, csak akkor, ha ez az objektum tartalmazza az érték mezőben; Ellenkező esetben figyelmen kívül hagyja azt.|Sztring|
|textualIndex|A válasz a megjelenítendő textualAnswers indexe.| Előjel nélküli egészként.|
|érték|Az azonosítója, amely azonosítja a megjelenítendő választ vagy a megjelenítendő válasz egy elemet. Ha az azonosító azonosítja a választ, a válasz elemek megjelenítése.|Azonosításra alkalmas|

### <a name="rankingresponse"></a>RankingResponse  
Határozza meg, ahol a keresési eredmények Laptartalom kell helyezni, és milyen sorrendben.  
  
|Name (Név)|Érték|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|A keresési eredmények a mainline megjelennek.|  
|<a name="ranking-pole" />sarkpontot|A keresési eredmények között, amely a legjobban látható kezelés biztosított kell (például a a mainline fölött megjelenő szöveg és az oldalsó sáv).|  
|<a name="ranking-sidebar" />oldalsó sáv|A keresési eredmények megjelenítéséhez az oldalsávon.| 


### <a name="searchresponse"></a>SearchResponse  
Meghatározza a legfelső szintű objektum, amely a válasz tartalmazza, ha a kérelem sikeres.  
  
Vegye figyelembe, hogy a szolgáltatás egy szolgáltatásmegtagadási támadás gyanítja, ha a kérelem sikeres lesz (HTTP-állapotkód értéke 200 OK); azonban a választörzs üres lesz.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Írja be a mutatót, amely SearchResponse van beállítva.|Sztring|  
|A weblap|A JSON-objektumból, amely meghatározza a kép|sztring|  
  
  
### <a name="textattribution"></a>TextAttribution  
Egyszerű szöveges attribútumára szerződéses szabályt definiálja.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|  
|í_rja be|Típusú javaslat, amely TextAttribution van beállítva.|Sztring|  
|szöveg|A attribútumára szöveget.<br /><br /> Szöveg attribútumára az entitás egészére vonatkozik, és az entitás bemutató követő üzenetnek kell megjelennie. Ha több szöveg vagy hivatkozás attribútumára szabály, amely nem ad meg egy cél, kell őket összefűzésére és megjelenítésükhöz használja a "adatait:" címke.|Sztring| 


## <a name="error-codes"></a>Hibakódok

Az alábbiakban a lehetséges HTTP-állapotkódok, egy kérelem ad vissza.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|200|Siker.|  
|400|A lekérdezés-paraméterek egyike hiányzik vagy érvénytelen.|  
|401|Az Előfizetés kulcs hiányzik, vagy érvénytelen.|  
|403|A felhasználó hitelesítése (például azok használt olyan érvényes előfizetés-kulcs), de nem rendelkeznek engedéllyel a kért erőforrásra.<br /><br /> Bing is vissza ezt az állapotot, ha a hívó a lekérdezések száma havi kvóta túllépve.|  
|410|A kérelem HTTP helyett a HTTPS protokollt használja. HTTPS az egyetlen támogatott protokoll.|  
|429|A hívó a lekérdezések egy második kvóta túllépve.|  
|500|Váratlan kiszolgálóhiba.|

A kérés nem teljesíthető, ha a válasz tartalmaz egy [ErrorResponse](#errorresponse) listáját tartalmazó objektum [hiba](#error) objektumok, amelyek ismertetik a hiba okáról. Ha a hiba kapcsolódik a paramétert, a `parameter` mező azonosítja a probléma paraméter. És ha a hiba kapcsolódik egy paraméterérték a `value` mező azonosítja az értéket, amely nem érvényes.

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

A következő értékeket a lehetséges hiba kódja és alárendelt hiba kódot.

|Kód|Alkód|Leírás
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Nincs implementálva|HTTP-állapotkód: 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Letiltva|Bing InvalidRequest adja vissza, ha valamely része a kérelem érvénytelen. Például egy kötelező paraméter hiányzik, vagy egy paraméter értéke érvénytelen.<br/><br/>A hiba ParameterMissing vagy ParameterInvalidValue, a HTTP-állapotkód akkor 400.<br/><br/>Ha HTTPS helyett a HTTP protokollt használ, a Bing HttpNotAllowed adja vissza, és a HTTP-állapotkód: 410.
|RateLimitExceeded|Nincsenek alárendelt kódok|Bing RateLimitExceeded adja vissza, ha az túllépi a lekérdezések / másodperc (QPS) vagy a lekérdezések száma (QPM) havi kvóta.<br/><br/>Ha QPS túllépi Bing HTTP-állapotkód 429, és ha QPM túllépi Bing beolvasása 403-as.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing InvalidAuthorization adja vissza, ha a Bing a hívó nem tudja hitelesíteni. Például a `Ocp-Apim-Subscription-Key` fejléc hiányzik, vagy az Előfizetés kulcs nem érvényes.<br/><br/>Redundancia akkor fordul elő, ha egynél több hitelesítési módszer adja meg.<br/><br/>A hiba InvalidAuthorization, a HTTP-állapotkód akkor 401-es.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing InsufficientAuthorization adja vissza, ha a hívó nem jogosult az erőforrás elérésére. Ez akkor fordulhat elő, ha az Előfizetés kulcs le van tiltva, vagy lejárt. <br/><br/>A hiba InsufficientAuthorization, a HTTP-állapotkód akkor 403-as.

## <a name="next-steps"></a>További lépések
- [C# gyors üzembe helyezés](c-sharp-quickstart.md)
- [Java gyors üzembe helyezés](java-quickstart.md)
- [Csomópont gyors üzembe helyezés](node-quickstart.md)
- [Python gyors üzembe helyezés](python-quickstart.md)

