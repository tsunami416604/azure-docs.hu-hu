---
title: A projekt URL-cím Preview referencia - Microsoft kognitív szolgáltatások |} Microsoft Docs
description: Útmutató a projekt URL-cím előnézeti végpont.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: adc2f83f703e740e40d9ba4fd3ed08ba429e5d97
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348738"
---
# <a name="project-url-preview-v7-reference"></a>URL-cím Preview v7 projekthivatkozást

URL-cím előzetes verziója támogatja a rövid leírása webes erőforrások blogbejegyzések, fórum beszélgetéseket, preview lapok, stb.  Az URL-cím lehet bármely típusú Internet erőforrás: weblap, híreket, kép vagy videó. A lekérdezés egy abszolút URL-CÍMÉT a http vagy https sémával; kell lennie. relatív URL-címeket vagy más rendszerek például ftp: / / használata nem támogatott.

Kép URL-címet használó alkalmazások webes kérelmeket küldeni a végpont URL-címet egy lekérdezési paraméter található megjeleníthető.  A kérelem tartalmaznia kell a *Ocp-Apim-előfizetés-kulcs* fejléc.   

A JSON-válasz elemzése a preview információt: név, leírás az erőforrás *isFamilyFriendly*, és hivatkozásokat tartalmaz, amelyek hozzáférést biztosít egy reprezentatív kép és a teljes erőforrás.

Csak az adatok URL-cím Preview előzetes kódtöredékek és miniatűr képeket hiperhivatkozással ellátott megjelenítéséhez a forrás webhelyek végfelhasználó által kezdeményezett URL-címben osztozik a közösségi média, csevegési botot vagy hasonló ajánlatok kell használnia. Meg kell másolni, tárolja és nem projekt URL-cím Preview származó adatok gyorsítótárazása. Tiltsa le az előzetes verziójú funkciók, amelyek jelenhet meg a webhely vagy a tartalmak tulajdonosai érkező kérésekre kell fogadják el.

## <a name="endpoint"></a>Végpont
A kérelem URL-cím előnézettel, a következő végpont kérelmet küld. A fejlécek és URL-cím paraméterek segítségével további specifikációk meghatározása.

GET. végpont: 
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

````

A kérelem kell a HTTPS protokollt használják, és tartalmazza a következő lekérdezésparaméter a következő:

 q – a lekérdezés, amely azonosítja az előzetes az URL-címe 

A következő szakaszok olyan technikai részleteket a válasz objektumok, a lekérdezés-paraméterek és a fejlécek, amelyek hatással vannak a keresési eredmények között. 
  
Kérelmek kell tartalmazó fejlécek kapcsolatos információkért lásd: [fejlécek](#headers).  
  
Tartalmaznia kell kérelmek lekérdezés paraméterekkel kapcsolatos további információkért lásd: [lekérdezési paramétert](#query-parameters).  
  
Információ a JSON-objektumokat, hogy a válasz tartalmazza, a következő témakörben: [válasz objektumok](#response-objects).

A maximális lekérdezési URL-cím hossza 2048 karakter. Győződjön meg arról, hogy az URL-cím hossza nem haladja meg a korlátot, a lekérdezési paraméterek maximális hossza legfeljebb 1500 karakterből kell. Ha az URL-cím hossza meghaladja a 2048 karakter, a kiszolgáló nem található 404 adja vissza.  

Engedélyezett használatát és a megjelenített eredmények kapcsolatos információkért lásd: [használja, és megjeleníti a követelmények](use-display-requirements.md). 

> [!NOTE]
> Néhány kérelemfejléc, így a lényeges más keresési API-k nincsenek hatással a kép URL-címe
> - Pragma – a hívó nem tudja befolyásolni kép URL-címet használ-e gyorsítótár
> - Felhasználói ügynök – most Url előnézeti API nem biztosít különböző válaszok származó PC, hordozható számítógép vagy mobileszköz hívások.

> Is néhány paraméter nem jelenleg jelentéssel bíró URL-cím előnézeti API-hoz, de a jövőben jobb globalizálás használható. 
 
## <a name="headers"></a>Fejlécek  
Az alábbiakban a fejlécek, amely egy kérelem-válasz tartalmazza.  
  
|Fejléc|Leírás|  
|------------|-----------------|   
|<a name="market" />Piacra jutási BingAPIs|Válaszfejléc.<br /><br /> A piacon, a kérelem által használt. Az űrlap \<languageCode\>-\<országhívószám\>. Például: hu-hu.|  
|<a name="traceid" />BingAPIs-TraceId|Válaszfejléc.<br /><br /> Az Eseménynapló-bejegyzés, amely tartalmazza a részleteket a kérelem az azonosítója. Ha hiba lép fel, rögzítése ezt. Ha nem határozza meg és hárítsa el a problémát, tartalmazzák ezt az Azonosítót és az egyéb adatokat, hogy a támogatási csapatával.|  
|<a name="subscriptionkey" />Az OCP-Apim-előfizetés-kulcs|Szükséges kérelemfejlécet.<br /><br /> Az Előfizetés kulcs, hogy az ehhez a szolgáltatáshoz, a regisztráció során [kognitív szolgáltatások](https://www.microsoft.com/cognitive-services/).|  
|<a name="clientid" />X-MSEdge-ClientID|Nem kötelező kérelem-válasz fejléce.<br /><br /> Bing felhasználók biztosít a konzisztens viselkedése Bing API-hívások használja ezt a fejlécet. Bing gyakran repülőútra új szolgáltatásait és fejlesztéseit, és különböző járatok forgalom hozzárendelése kulcsaként használ az ügyfél-azonosító. Címzett több kérés során a felhasználó nem használja az ugyanazon ügyfél-azonosító, ha a Bing is hozzárendelheti a felhasználó több ütköző járatok. Több ütköző járatok lett hozzárendelve inkonzisztens felhasználói élményt is járhat. Például ha a második kérelmet egy másik repülési-hozzárendelést, mint az első, a felhasználói élmény lehet váratlan. Is, a Bing használatával az ügyfél-azonosító személyessé tétele érdekében, hogy az ügyfél webes eredmények keresési előzmények gazdagabb élményt biztosítva a felhasználói azonosítót.<br /><br /> Bing is használja ezt a fejlécet a tevékenység egy ügyfél-azonosítót. által generált elemzésével eredmény rangsort javítása érdekében A fontos fejlesztéseket kézbesíteni Bing API-k és pedig lehetővé teszi, hogy a nagyobb kattintások sebesség API fogyasztó eredmények magasabb színvonalú segít.<br /><br />Az alábbiakban az alapvető használati szabályok ezt a fejlécet.<br /><ul><li>Minden olyan felhasználóhoz, használja az alkalmazást az eszközön rendelkeznie kell egy egyedi, a Bing létrehozott ügyfél-azonosítót.<br /><br/>Ha a kérelem nem adja meg ezt a fejlécet, a Bing Azonosítót hoz létre, és adja vissza, az X-MSEdge-ClientID válaszfejléc. A csak, hogy nem bele kell foglalni ezt a fejlécet a kérelem, az első alkalommal a felhasználó használja az alkalmazást azokon az eszközökön.<br /><br/></li><li>Az ügyfél-Azonosítót használja az alkalmazás lehetővé teszi a felhasználó az eszköz minden egyes Bing API-kérelemhez.<br /><br/></li><li>**Figyelem:** meg kell győződnie arról, hogy az ügyfél-azonosító nem összekapcsolhatónak forrásának hitelesíthetőnek felhasználói fiók adatok számára.</li><br/><li>Megőrzése az ügyfél-azonosítót. Továbbra is fennáll az azonosító egy webböngésző alkalmazást, állandó HTTP cookie-k segítségével ellenőrizze az azonosító olyan munkamenetekben. Ne használjon egy munkamenetcookie-t. Más alkalmazások, például a mobile apps szolgáltatásban használja az eszköz állandó tároló megőrizni a azonosítóját.<br /><br/>A felhasználó használja az alkalmazást azokon az eszközökön, amikor legközelebb megőrizte ügyfél-azonosító beolvasása.</li></ul><br /> **Megjegyzés:** Bing válaszok is, vagy nem tartalmazza ezt a fejlécet. A válasz tartalmazza ezt a fejlécet, ha rögzíti az ügyfél-Azonosítót, és a későbbi Bing azokon az eszközökön a felhasználó használja azt.<br /><br /> **Megjegyzés:** Ha adja meg az X-MSEdge-ClientID, nem tartalmazhat-e be a cookie-kat a kérelemben.|  
|<a name="clientip" />X-MSEdge-Ügyfélip|Nem kötelező kérelemfejlécet.<br /><br /> Az ügyféleszköz IPv4 vagy IPv6-cím. Az IP-címet a felhasználó földrajzi helye felderítésére szolgál. Bing a Tartózkodásihely-adatok a biztonságos keresési viselkedés meghatározására használja.<br /><br />  Nem takarják a címet (például, ha megváltoztatja az utolsó oktett 0). A cím eredmények nem bárhol mellett az eszköz tényleges helyére a helyen obfuscating, emiatt a Bing hibás eredmények szolgál.|  
<br /><br /></li></ul>   

## <a name="query-parameters"></a>Lekérdezési paraméterek  
A kérelem tartalmazhatják a következő lekérdezési paramétereket. Tekintse meg a szükséges oszlop kötelező paraméterhez. URL-címet kell kódolni a lekérdezési paramétereket. A lekérdezés egy abszolút URL-CÍMÉT a http vagy https sémával; kell lennie. nem támogatjuk a relatív URL-címeket vagy más rendszerek például ftp: / /
  
  
|Name (Név)|Érték|Típus|Szükséges|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|A piacon, honnan származnak az eredményeket. <br /><br />A lehetséges piaci értékek listáját lásd: [piaci kódok](#market-codes).<br /><br /> **Megjegyzés:** az URL-cím előnézeti API jelenleg csak USA geográfiai és támogatja angol nyelven.<br /><br />|Sztring|Igen|  
|<a name="query" />A Q|Az URL-cím, az előzetes|Sztring|Igen|  
|<a name="responseformat" />responseFormat|Az adathordozó típusát, a válasz használatára. A következő értékeket a lehetséges azonban nem.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Az alapértelmezett érték a JSON-NÁ. A JSON információt objektumok, hogy a válasz tartalmazza, a következő témakörben: [válasz objektumok](#response-objects).<br /><br />  Ha JsonLd ad meg, az adott válasz törzsének JSON-LD objektumokat tartalmazza, amelyek tartalmaznak a keresési eredmények között. A JSON-LD kapcsolatos információkért lásd: [JSON-LD](http://json-ld.org/).|Sztring|Nem|
|<a name="safesearch"/>biztonságos keresés|Felnőtt tartalom érvénytelen vagy hamisított tartalom le van tiltva, hibakód: 400, és a *isFamilyFriendly* jelző nem ad vissza. <p>Jogi felnőtt tartalom, az alábbi történik. Állapotkód adja vissza a 200 lesz, és a *isFamilyFriendly* jelző hamisra van állítva.<ul><li>biztonságos keresés = szigorú: cím, a leírás, a URL-cím és a lemezkép nem állítható vissza.</li><li>biztonságos keresés = mérsékelt; Cím, URL-cím és leírás, de nem a leíró kép lekérése.</li><li>biztonságos keresés = off; Minden válasz objektumok/elemet – cím, az URL-címet, a leírás és a kép lekérése.</li></ul> |Sztring|Nem szükséges. </br> Alapértelmezés szerint biztonságos keresés az = szigorú.| 

## <a name="response-objects"></a>Válasz objektumok  
A válasz séma vagy egy [weblap] vagy ErrorResponse, ahogy a webes keresés API. A kérés nem teljesíthető, ha a legfelső szintű objektum van-e a [ErrorResponse](#errorresponse) objektum.


|Objektum|Leírás|  
|------------|-----------------|  
|[A weblap](#webpage)|Felső szintű JSON-objektum Preview attribútumokat tartalmazó.|  

  
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
  

### <a name="webpage"></a>A weblap  
Meghatározza, milyen információkat kapcsolatos egy Preview a weblap.  
  
|Name (Név)|Érték|Típus|  
|----------|-----------|----------|
|név|A lap címét, nem feltétlenül a HTML-cím|Sztring|
|url|A ténylegesen lett bejárt URL-CÍMÉT (kérelem előfordulhat, hogy követte-átirányítások)|Sztring|  
|leírás|Rövid leírása a a lap és a tartalom|Sztring|  
|isFamilyFriendly|A webes index; elemek legpontosabb valós idejű fetches tegye az észlelés alapján kizárólag az URL-címet, és nem az oldal tartalmát|logikai|
|primaryImageOfPage/contentUrl|Az előzetes felvenni egy reprezentatív kép URL-címe|Sztring| 


### <a name="identifiable"></a>Azonosításra alkalmas
|Name (Név)|Érték|Típus|  
|-------------|-----------------|----------|
|id|Egy erőforrás-azonosítója|Sztring|
 

## <a name="error-codes"></a>Hibakódok

Az alábbiakban a lehetséges HTTP-állapotkódok, egy kérelem ad vissza.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|200|Siker.|  
|400|A lekérdezés-paraméterek egyike hiányzik vagy érvénytelen.| 
|400|ServerError, ResourceError Alkód: A kért URL-cím nem érhető el|
|400|ServerError, ResourceError Alkód: A kért URL-cím nem adott vissza (beleértve a Ha által visszaadott HTTP 404-es) kód|
|400|InvalidRequest, letiltva Alkód: A kért URL-cím felnőtt tartalom tartalmazhat, és le lett tiltva| 
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
- [C# gyors üzembe helyezés](csharp.md)
- [Java gyors üzembe helyezés](java-quickstart.md)
- [JavaScript gyors üzembe helyezés](javascript.md)
- [Csomópont gyors üzembe helyezés](node-quickstart.md)
- [Python gyors üzembe helyezés](python-quickstart.md)

