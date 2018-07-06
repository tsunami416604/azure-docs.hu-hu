---
title: Projekt URL-cím előnézete referencia – a Microsoft Cognitive Services |} A Microsoft Docs
description: Projekt URL-cím előnézete végponthoz tartozó hivatkozás.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 46c011d62b6ae51f5f7d292345e6ece0e27a8541
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865875"
---
# <a name="project-url-preview-v7-reference"></a>Projekt URL-cím előnézete v7-referencia

URL-cím előnézete rövid leírást olvashat a webes erőforrások támogatja a blogbejegyzések, hozzászólások a fórumon, előzetes oldalak, stb.  Az URL-cím lehet bármely internethez erőforrás típusa: weblapot, hírek, kép vagy videó. A lekérdezés egy http vagy https sémával; abszolút URL-CÍMNEK kell lennie. relatív URL-címeket vagy más rendszerek például az ftp: / / nem támogatottak.

Előnézeti URL-címet használó webes kéréseket küldjön a végpont található egy lekérdezési paraméterrel megjeleníthető URL-címet.  A kérésnek tartalmaznia kell a *Ocp-Apim-Subscription-Key* fejléc.   

A JSON-válasz elemzése az előzetes verzió információt: név, leírás, az erőforrás *isFamilyFriendly*, és hivatkozásokat tartalmaz, amelyek hozzáférést biztosít egy reprezentatív rendszerképet és a teljes erőforrás.

Megjeleníti a előzetes kódrészletek és a miniatűr képekhez hiperhivatkozással azok forráshelyeket, felhasználó által kezdeményezett URL-címet a közösségi oldalakon, csevegőrobot, vagy hasonló ajánlatok megosztása csak URL-cím előnézete származó adatok kell használnia. Nem másolja, tárolni vagy kell minden projekt URL-cím előnézete érkező adatokat. Tiltsa le a webhely vagy a tartalomtulajdonosok kaphat előzetes érkező kérésekre kell fogadja el.

## <a name="endpoint"></a>Végpont
A kérelem URL-cím előnézete eredmények, a következő végpont egy kérés küldése. A fejlécek és URL-paraméterek használatával további specifikációk meghatározása.

GET-végpont: 
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

````

A kérelem kell a HTTPS protokollt használja, és tartalmazza a lekérdezési paraméter a következő:

 válaszok – a lekérdezést, amely azonosítja az előzetes verzióra az URL-cím 

A következő szakaszok a válaszobjektumok, a lekérdezési paraméterek és a fejlécek, amelyek befolyásolják a keresési eredmények vonatkozó technikai részleteket. 
  
További információ a fejlécekről, amely a kérések tartalmaznia kell: [fejlécek](#headers).  
  
További információ a lekérdezési paraméterek, amely a kérések tartalmaznia kell: [lekérdezési paramétereket](#query-parameters).  
  
A JSON-fájllal kapcsolatos információk objektumok, hogy a válasz tartalmazza, lásd: [válaszobjektumok](#response-objects).

A maximális lekérdezési URL-cím hossza 2048 karakter lehet. Győződjön meg arról, hogy az URL-cím hossza ne haladja meg a korlátot, a lekérdezési paraméterek maximális hossza legfeljebb 1500 karakterből kell lennie. Ha az URL-cím 2048 karakternél hosszabb, a kiszolgáló a 404 nem található adja vissza.  

Engedélyezett használatát és a megjelenített eredmények kapcsolatos információkért lásd: [használja és megjelenítési követelményeihez](use-display-requirements.md). 

> [!NOTE]
> Néhány kérelemfejlécek jelentős más keresési API-k nem befolyásolják a következő URL-cím előnézete
> - Direktiva pragma – a hívó nem rendelkezik URL-cím előnézete használ-e gyorsítótár felett
> - Felhasználói ügynök – egyelőre előzetes API Url nem biztosít különböző válaszokat ad a Közösségtől származó PC, hordozható számítógép vagy mobileszköz hívásokat.

> Is néhány paraméter nem jelenleg jelentéssel bíró URL-cím előzetes API-hoz, de a jövőben a továbbfejlesztett globalizációs használhatók. 
 
## <a name="headers"></a>Fejlécek  
Az alábbiakban a fejlécek, köztük a kérést és választ.  
  
|Fejléc|Leírás|  
|------------|-----------------|   
|<a name="market" />BingAPIs piaci|Válaszfejléc.<br /><br /> A piac, a kérelem által használt. Az űrlap \<languageCode\>-\<countryCode\>. Például: hu-hu.|  
|<a name="traceid" />BingAPIs-TraceId|Válaszfejléc.<br /><br /> A kérelem részleteit tartalmazó a naplóbejegyzés azonosítója. Ha hiba történik, rögzítése forrástároló. Ha nem határozza meg, és a probléma megoldásához, tartalmazzák ezt az Azonosítót és az egyéb adatokat, hogy a támogatási csoporthoz.|  
|<a name="subscriptionkey" />OCP-Apim-Subscription-Key|Szükséges a kérelem fejlécében.<br /><br /> Az előfizetési kulcsot, ezt a szolgáltatást a regisztráció során kapott [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="clientid" />X-MSEdge-ClientID|Választható kérések és válaszok fejléc.<br /><br /> A Bing a fejléc használatával biztosítható a felhasználók konzisztens viselkedés között Bing API-hívások. Bing gyakran légi járművek új szolgáltatásait és fejlesztéseit, és hozzárendeléséhez a forgalmat a különböző repülőjáratok kulcsként használ az ügyfél-Azonosítót. Az azonos ügyfél-Azonosítót egy felhasználóhoz több kérés között nem használja, ha majd Bing lehet, hogy a felhasználó hozzárendelése több ütköző repülőjáratok. Több ütköző repülőjáratok folyamatban van rendelve egy konzisztens felhasználói élményt vezethet. Például ha a második kérés, mint az első különböző repülési hozzárendeléssel rendelkezik, a tapasztalatok lehet váratlan. A Bing segítségével is az ügyfél-azonosító webes találatokat, hogy az ügyfél a megfelelő kódolási sebesség kiválasztását azonosítóhoz tartozó keresési előzmények, a felhasználó egy gazdagabb élményt nyújtva.<br /><br /> A Bing is használja ezt a fejlécet a tevékenység egy ügyfél-azonosítót. által generált elemzésével eredmény módosítják a rangsort javítása érdekében A relevancia alapján végzett fejlesztések segítség az eredmények a Bing API-k és üzembe viszont lehetővé teszi, hogy nagyobb átkattintásos arányt API fogyasztók számára jobb minőségű.<br /><br />Az alábbiakban az alapszintű használati szabályok, amelyek a alkalmazni ezt a fejlécet.<br /><ul><li>Minden egyes felhasználó esetében használja az alkalmazást az eszközön rendelkeznie kell egy egyedi, a Bing létrehozott ügyfél-azonosítót.<br /><br/>Ha a kérés nem adja meg ezt a fejlécet, a Bing létrehoz egy Azonosítót, és visszaadja az X-MSEdge-ClientID fejlécet. Az egyetlen alkalom, hogy a kérelem kell tartalmazza ezt a fejlécet a felhasználó használja az alkalmazást az eszközön lévő első alkalommal.<br /><br/></li><li>Az ügyfél-azonosító használata minden egyes Bing API-kérelem, amely az alkalmazás lehetővé teszi a felhasználó az eszközön.<br /><br/></li><li>**Figyelem:** gondoskodnia kell arról, hogy az ügyfél-azonosító ne legyen összekapcsolhatónak, bármely forrásának hitelesíthetőnek felhasználóifiók-adatokat.</li><br/><li>Megőrzése az ügyfél-azonosítót. Egy böngészőalkalmazásban Azonosítóját megőrizni, használjon állandó HTTP cookie-k, használja az összes munkamenetek között. Ne használjon egy munkamenetcookie-t. Más alkalmazások, például a mobilalkalmazások esetében használja az eszköz tartós tároláshoz megőrizni az azonosítója.<br /><br/>A következő alkalommal, amikor a felhasználó használja az alkalmazást, hogy az eszközről, amely megőrizte ügyfél-azonosító beszerzése.</li></ul><br /> **Megjegyzés:** Bing-válaszok is, vagy nem feltétlenül tartalmazzák ezt a fejlécet. Ha a válasz tartalmazza ezt a fejlécet, rögzítheti az ügyfél-Azonosítót, és használhatja azt a felhasználót az eszközön lévő összes további Bing kérelmet.<br /><br /> **Megjegyzés:** Ha adja meg az X-MSEdge-ClientID, nem tartalmazhat-e be a cookie-kat a kérésben.|  
|<a name="clientip" />X-MSEdge-Ügyfélip|Nem kötelező kérelem fejléce.<br /><br /> Az ügyféleszköz IPv4 vagy IPv6-cím. Az IP-cím a felhasználó földrajzi helye felderítésére szolgál. A Bing biztonságos keresési viselkedés meghatározására használja a helyre vonatkozó adatokat.<br /><br />  A cím nem rejtse fel (például úgy, hogy az utolsó oktettet módosítása 0). A cím eredmények a helyen nincs folyamatban bárhol az eszköz tényleges hely közelében obfuscating, emiatt a Bing szolgálja ki a hibás eredményeket.|  
<br /><br /></li></ul>   

## <a name="query-parameters"></a>Lekérdezési paraméterek  
A kérelem lekérdezési paraméterek tartalmazhat. Tekintse meg a szükséges oszlop, paraméter szükséges. URL-címet kell kódolása a lekérdezési paramétereket. A lekérdezés egy http vagy https sémával; abszolút URL-CÍMNEK kell lennie. nem támogatjuk a relatív URL-címeket vagy más rendszerek például az ftp: / /
  
  
|Name (Név)|Érték|Típus|Szükséges|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|A piacon, honnan származnak az eredményeket. <br /><br />Piaci a lehetséges értékek listáját lásd: [piaci kódok](#market-codes).<br /><br /> **Megjegyzés:** az URL-cím előzetes API jelenleg csak támogatja a földrajzi RÉGIÓJA és az angol nyelvű.<br /><br />|Sztring|Igen|  
|<a name="query" />válaszok|Az előzetes verzióra az URL-cím|Sztring|Igen|  
|<a name="responseformat" />responseFormat|Az adathordozó-típus használata a válaszhoz. A kis-és értékek a következők.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Az alapértelmezett érték a JSON. A JSON-fájllal kapcsolatos információk objektumok, hogy a válasz tartalmazza, lásd: [Válaszobjektumok](#response-objects).<br /><br />  Ha JsonLd adja meg, a válasz törzse tartalmazza a keresési eredményeket tartalmazó JSON-LD objektumok. A JSON-LD kapcsolatos információkért lásd: [JSON-LD](http://json-ld.org/).|Sztring|Nem|
|<a name="safesearch"/>biztonságos keresés|Érvénytelen felnőtt tartalom, vagy a hamisított tartalom le van tiltva, hibakód: 400, és a *isFamilyFriendly* jelző nem ad vissza. <p>Jogi felnőtt tartalom, az alábbi történik. Állapotkód: 200, adja vissza, és a *isFamilyFriendly* jelző false értékre van állítva.<ul><li>biztonságos keresés = szigorú: cím, leírás, URL-cím és a lemezkép nem állítható vissza.</li><li>biztonságos keresés = közepes; Cím, URL-cím és leírás, de nem a leíró képet kaphat.</li><li>biztonságos keresés kikapcsolása:; = Minden válasz objektumok/elemet – title, URL-címet, leírást és képet kaphat.</li></ul> |Sztring|Nem kötelező. </br> Biztonságos keresés alapértelmezés szerint szigorú =.| 

## <a name="response-objects"></a>Válasz objektumok  
A válasz sémája vagy egy [weblap] vagy byl vrácen Prvek, ahogy a webes keresési API-t. Ha a kérelem meghiúsul, a legfelső szintű objektum a [byl vrácen Prvek](#errorresponse) objektum.


|Objektum|Leírás|  
|------------|-----------------|  
|[Weblap](#webpage)|Legfelső szintű JSON-objektum, amely tartalmazza az előzetes verzió attribútumai.|  

  
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
  

### <a name="webpage"></a>Weblap  
Meghatározza, milyen kapcsolatos információkat egy előzetes verzióban érhető el a weblapot.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|
|név|Az oldal címe, nem feltétlenül a HTML-cím|Sztring|
|url|Az URL-cím, amely ténylegesen volt bejárt (kérelem előfordulhat, hogy felvette a átirányítások)|Sztring|  
|leírás|Az oldal és a tartalom rövid leírása|Sztring|  
|isFamilyFriendly|A legpontosabb a webes index; eleme valós idejű fetches tegye alapján kizárólag az URL-címet, és nem az oldal tartalmát az észlelés|logikai|
|primaryImageOfPage/contentUrl|Tartalmazza az előzetes verzióban érhető el egy reprezentatív kép URL-címe|Sztring| 


### <a name="identifiable"></a>Azonosításra alkalmas
|Name (Név)|Érték|Típus|  
|-------------|-----------------|----------|
|id|Egy erőforrás-azonosítója|Sztring|
 

## <a name="error-codes"></a>Hibakódok

Az alábbi táblázat a lehetséges HTTP-állapotkódok, amely egy kérés adja vissza.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|200|Siker.|  
|400|A lekérdezési paraméterek egyike hiányzik vagy érvénytelen.| 
|400|Kiszolgálóhibái, ResourceError Alkód: A kért URL nem érhető el|
|400|Kiszolgálóhibái, ResourceError Alkód: A kért URL-cím nem adta vissza sikerkódot (beleértve a Ha a visszaadott HTTP 404)|
|400|InvalidRequest, letiltott Alkód: A kért URL-cím tartalmazhat felnőtt tartalom, és le lett tiltva| 
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
- [C# gyorsútmutató](csharp.md)
- [Java a rövid útmutató](java-quickstart.md)
- [JavaScript a rövid útmutató](javascript.md)
- [Csomópont a rövid útmutató](node-quickstart.md)
- [Python a rövid útmutató](python-quickstart.md)

