---
title: Projekt URL-cím előnézete referencia
titlesuffix: Azure Cognitive Services
description: Projekt URL-cím előnézete végponthoz tartozó hivatkozás.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: f7925c3eb14915c2b811ccfcd3a3803b9bd7c806
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222915"
---
# <a name="project-url-preview-v7-reference"></a>Projekt URL-cím előnézete v7-referencia

URL-cím előnézete rövid leírást olvashat a webes erőforrások támogatja a blogbejegyzések, hozzászólások a fórumon, előzetes oldalak, stb. Az URL-cím internetes erőforrás bármilyen típusú lehet: Weblap, hírek, kép vagy videó. A lekérdezés egy http vagy https sémával; abszolút URL-CÍMNEK kell lennie. relatív URL-címeket vagy más rendszerek például az ftp: / / nem támogatottak.

Előnézeti URL-címet használó webes kéréseket küldjön a végpont található egy lekérdezési paraméterrel megjeleníthető URL-címet. A kérésnek tartalmaznia kell a *Ocp-Apim-Subscription-Key* fejléc.

A JSON-válasz elemzése az előzetes verzió információt: név, leírás, az erőforrás *isFamilyFriendly*, és hivatkozásokat tartalmaz, amelyek hozzáférést biztosít egy reprezentatív rendszerképet és a teljes erőforrás.

Megjeleníti a előzetes kódrészletek és a miniatűr képekhez hiperhivatkozással azok forráshelyeket, felhasználó által kezdeményezett URL-címet a közösségi oldalakon, csevegőrobot, vagy hasonló ajánlatok megosztása csak URL-cím előnézete származó adatok kell használnia. Nem másolja, tárolni vagy kell minden projekt URL-cím előnézete érkező adatokat. Tiltsa le a webhely vagy a tartalomtulajdonosok kaphat előzetes érkező kérésekre kell fogadja el.

## <a name="endpoint"></a>Végpont
A kérelem URL-cím előnézete eredmények, a következő végpont egy kérés küldése. A fejlécek és URL-paraméterek használatával további specifikációk meghatározása.

GET-végpont:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

```

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
|<a name="market" />BingAPIs-Market|Válaszfejléc.<br /><br /> A kérelem által használt piac. A formátum a következő: \<languageCode\>-\<countryCode\>. Például: en-US.|
|<a name="traceid" />BingAPIs-TraceId|Válaszfejléc.<br /><br /> A kérelem részleteit tartalmazó naplóbejegyzés azonosítója. Ha hiba történik, rögzítse ezt az azonosítót. Ha nem tudja meghatározni és megoldani a problémát, foglalja bele a kérelembe ezt az azonosítót is a támogatási csoportnak megadott többi információval együtt.|
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Kötelező kérelemfejléc.<br /><br /> Az előfizetési kulcs, amelyet akkor kapott, amikor feliratkozott a szolgáltatásra a [Cognitive Servicesben](https://www.microsoft.com/cognitive-services/).|
|<a name="clientid" />X-MSEdge-ClientID|Választható kérelem- és válaszfejléc.<br /><br /> A Bing ezt a fejlécet használja ahhoz, hogy következetes viselkedést biztosítson a felhasználók számára a Bing API-hívásaiban. A Bing gyakran tesztel új funkciókat és fejlesztéseket, és az ügyfél-azonosítót használja kulcsként ahhoz, hogy a tesztcsomagokhoz rendelje a forgalmat. Ha a kérelmekben nem ugyanazt az ügyfél-azonosítót használja egy adott felhasználóhoz, előfordulhat, hogy a Bing több ütköző tesztcsomaghoz rendeli hozzá a felhasználót. Az ütköző tesztcsomagok hozzárendelése inkonzisztens felhasználói élményhez vezethet. Például ha a második kérelemhez más tesztcsomag van hozzárendelve, mint az elsőhöz, az váratlan működést eredményezhet. A Bing arra is felhasználhatja az ügyfél-azonosítót, hogy az ügyfél-azonosító keresési előzményeire szabja a webes találatokat, gazdagabb élményt nyújtva a felhasználónak.<br /><br /> A Bing továbbá az ügyfél-azonosító által létrehozott tevékenységek elemzésével az eredmények rangsorolásának javítására is használja a fejlécet. A relevancia javítása segít abban, hogy a Bing API-k jobb minőségű eredményeket biztosítsanak, ami pedig lehetővé teszi a magasabb átkattintási arányt az API fogyasztója számára.<br /><br />A fejlécre az alábbi alapvető használati szabályok vonatkoznak.<br /><ul><li>Minden felhasználónak, aki használja az eszközön lévő alkalmazást, rendelkeznie kell egy egyedi, Bing által létrehozott ügyfél-azonosítóval.<br /><br/>Ha nem foglalja bele ezt a fejlécet a kérelembe, a Bing létrehoz egy azonosítót, és visszaküldi azt az X-MSEdge-ClientID válaszfejlécben. Ezt a fejlécet csak akkor NEM szabad belefoglalni a kérelembe, amikor a felhasználó először használja az alkalmazást azon az eszközön.<br /><br/></li><li>Használja az ügyfél-azonosítót minden olyan Bing API-kéréshez, amelyet az alkalmazás intéz a felhasználó kapcsán az eszközön.<br /><br/></li><li>**FIGYELEM:** Biztosítania kell, hogy az ügyfél-azonosító ne legyen összekapcsolhatónak, bármely forrásának hitelesíthetőnek felhasználóifiók-adatokat.</li><br/><li>Őrizze meg az ügyfél-azonosítót. Az azonosító böngészőalkalmazásban való megőrzéséhez használjon egy állandó HTTP-cookie-t, amely biztosítja, hogy minden munkamenetben ez az azonosító legyen használva. Ne használjon munkamenet-cookie-t. Más alkalmazások, például a mobilalkalmazások esetében az azonosító megőrzéséhez használja az eszköz állandó tárolóját.<br /><br/>Kérje le a megőrzött ügyfél-azonosítót, amikor a felhasználó ismét használja az alkalmazást az eszközön.</li></ul><br /> **MEGJEGYZÉS:** A Bing-válaszok is, vagy nem feltétlenül tartalmazzák ezt a fejlécet. Ha a válasz tartalmazza ezt a fejlécet, rögzítse az ügyfél-azonosítót, és használja azt a felhasználó összes további Bing-kérelméhez az adott eszközön.<br /><br /> **MEGJEGYZÉS:** Ha az X-MSEdge-ClientID adja meg, nem tartalmazhat cookie-kat a kérésben.|
|<a name="clientip" />X-MSEdge-ClientIP|Választható kérelemfejléc.<br /><br /> Az ügyféleszköz IPv4- vagy IPv6-címe. Az IP-cím a felhasználó tartózkodási helyének felderítésére szolgál. A Bing arra használja a helyadatokat, hogy meghatározza a biztonságos keresés viselkedését.<br /><br /> Ne rejtse el a címet (például úgy, hogy 0-ra módosítja az utolsó oktettet). Ha elrejti a címet, a tartózkodási hely távol fog esni az eszköz tényleges helyétől, amely ahhoz vezethet, hogy a Bing téves eredményeket fog megadni.|

## <a name="query-parameters"></a>Lekérdezési paraméterek
A kérelem lekérdezési paraméterek tartalmazhat. Tekintse meg a szükséges oszlop, paraméter szükséges. URL-címet kell kódolása a lekérdezési paramétereket. A lekérdezés egy http vagy https sémával; abszolút URL-CÍMNEK kell lennie. nem támogatjuk a relatív URL-címeket vagy más rendszerek például az ftp: / /

|Name (Név)|Value|Typo|Szükséges|
|----------|-----------|----------|--------------|
|<a name="mkt" />mkt|A piac, ahonnan az eredmények származnak. <br /><br />Piaci a lehetséges értékek listáját lásd: [piaci kódok](#market-codes).<br /><br /> **MEGJEGYZÉS:** Az URL-cím előzetes API jelenleg csak támogatja a földrajzi RÉGIÓJA és az angol nyelvű.<br /><br />|Karakterlánc|Igen|
|<a name="query" />q|Az előzetes verzióra az URL-cím|Karakterlánc|Igen|
|<a name="responseformat" />responseFormat|Az adathordozó-típus használata a válaszhoz. A kis-és értékek a következők.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Az alapértelmezett érték a JSON. A JSON-fájllal kapcsolatos információk objektumok, hogy a válasz tartalmazza, lásd: [Válaszobjektumok](#response-objects).<br /><br />Ha JsonLd adja meg, a válasz törzse tartalmazza a keresési eredményeket tartalmazó JSON-LD objektumok. A JSON-LD kapcsolatos információkért lásd: [JSON-LD](http://json-ld.org/).|Karakterlánc|Nem|
|<a name="safesearch"/>safeSearch|Érvénytelen felnőtt tartalom, vagy a hamisított tartalom le van tiltva, hibakód: 400, és a *isFamilyFriendly* jelző nem ad vissza. <p>Jogi felnőtt tartalom, az alábbi történik. Állapotkód: 200, adja vissza, és a *isFamilyFriendly* jelző false értékre van állítva.<ul><li>safeSearch=strict: Cím, leírás, URL-cím és a lemezkép nem állítható vissza.</li><li>biztonságos keresés = közepes; Cím, URL-cím és leírás, de nem a leíró képet kaphat.</li><li>biztonságos keresés kikapcsolása:; = Minden válasz objektumok/elemet – title, URL-címet, leírást és képet kaphat.</li></ul> |Karakterlánc|Nem kötelező. </br> Biztonságos keresés alapértelmezés szerint szigorú =.|

## <a name="response-objects"></a>Válasz objektumok
A válasz sémája vagy egy [weblap] vagy byl vrácen Prvek, ahogy a webes keresési API-t. Ha a kérelem meghiúsul, a legfelső szintű objektum a [byl vrácen Prvek](#errorresponse) objektum.

|Objektum|Leírás|
|------------|-----------------|
|[WebPage](#webpage)|Legfelső szintű JSON-objektum, amely tartalmazza az előzetes verzió attribútumai.|

### <a name="error"></a>Hiba
Határozza meg a következő hiba történt.

|Elem|Leírás|Typo|
|-------------|-----------------|----------|
|<a name="error-code" />Kód|A hiba kódja, amely azonosítja a hiba kategóriáját. Lehetséges kódok listáját lásd: [hibakódok](#error-codes).|Karakterlánc|
|<a name="error-message" />üzenet|A hiba leírása.|Karakterlánc|
|<a name="error-moredetails" />moreDetails|Egy leírást, amely a hibával kapcsolatos további információkat biztosít.|Karakterlánc|
|<a name="error-parameter" />A paraméter|A lekérdezési paraméter, amely a hibát okozó a kérésben.|Karakterlánc|
|<a name="error-subcode" />subCode|A hiba kódja, amely azonosítja a hibát. Például ha `code` InvalidRequest, akkor `subCode` ParameterInvalid vagy ParameterInvalidValue is lehet. |Karakterlánc|
|<a name="error-value" />value|A lekérdezési paraméter értéke, amely nem érvényes.|Karakterlánc|

### <a name="errorresponse"></a>Byl vrácen Prvek
A legfelső szintű objektum, amely a válasz tartalmazza, ha a kérés nem teljesíthető.

|Name (Név)|Value|Typo|
|----------|-----------|----------|
|_type|Mutató típusa.|Karakterlánc|
|<a name="errors" />Hibák|Miért nem sikerült a kérelem miatt hibák listája.|[Error](#error)[]|

### <a name="webpage"></a>WebPage
Meghatározza, milyen kapcsolatos információkat egy előzetes verzióban érhető el a weblapot.

|Name (Név)|Value|Typo|
|----------|-----------|----------|
|név|Az oldal címe, nem feltétlenül a HTML-cím|Karakterlánc|
|url|Az URL-cím, amely ténylegesen volt bejárt (kérelem előfordulhat, hogy felvette a átirányítások)|Karakterlánc|
|leírás|Az oldal és a tartalom rövid leírása|Karakterlánc|
|isFamilyFriendly|A legpontosabb a webes index; eleme valós idejű fetches tegye alapján kizárólag az URL-címet, és nem az oldal tartalmát az észlelés|logikai|
|primaryImageOfPage/contentUrl|Tartalmazza az előzetes verzióban érhető el egy reprezentatív kép URL-címe|Karakterlánc|

### <a name="identifiable"></a>Azonosításra alkalmas
|Name (Név)|Value|Typo|
|-------------|-----------------|----------|
|id|Egy erőforrás-azonosítója|Karakterlánc|

## <a name="error-codes"></a>Hibakódok

Az alábbi táblázat a lehetséges HTTP-állapotkódok, amely egy kérés adja vissza.

|Állapotkód|Leírás|
|-----------------|-----------------|
|200|Siker.|
|400|A lekérdezési paraméterek egyike hiányzik vagy érvénytelen.|
|400|ServerError, subCode ResourceError: A kért URL nem érhető el|
|400|ServerError, subCode ResourceError: A kért URL-cím nem adta vissza sikerkódot (beleértve a Ha a visszaadott HTTP 404)|
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

|Kód|SubCode|Leírás
|-|-|-
|Kiszolgálóhibái|UnexpectedError<br/>ResourceError<br/>Nincs implementálva|HTTP-állapotkód: 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Letiltva|A Bing InvalidRequest adja vissza, ha bármelyik részét a kérés érvénytelen, nem. Például egy kötelező paraméter hiányzik, vagy egy paraméter értéke nem érvényes.<br/><br/>Ha a hiba ParameterMissing vagy ParameterInvalidValue, a a HTTP-állapotkód: 400.<br/><br/>Ha a HTTPS helyett a HTTP protokollt használja, a Bing HttpNotAllowed adja vissza, és a HTTP-állapotkód: 410.
|RateLimitExceeded|Nincsenek alárendelt kódok|Minden alkalommal, amikor a lekérdezések másodpercenkénti (lekérdezési QPS) és a lekérdezések száma (QPM) havi kvótát túllépi a Bing RateLimitExceeded adja vissza.<br/><br/>Ha túllépi QPS, a Bing adja vissza a 429-es HTTP-állapotkód:, és Ha elfogynak QPM, a Bing adja vissza a 403-as.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|A Bing InvalidAuthorization adja vissza, ha a Bing a hívó nem tudja hitelesíteni. Ha például a `Ocp-Apim-Subscription-Key` fejléc hiányzik, vagy az előfizetési kulcs nem érvényes.<br/><br/>A redundancia akkor fordul elő, ha egynél több hitelesítési módszer adja meg.<br/><br/>Ha a hiba InvalidAuthorization, a HTTP-állapotkód: a 401-es.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|A Bing InsufficientAuthorization adja vissza, ha a hívó nem rendelkezik engedéllyel az erőforrás eléréséhez. Ez akkor fordulhat elő, ha az előfizetési kulcs le lett tiltva, vagy lejárt. <br/><br/>Ha a hiba InsufficientAuthorization, a HTTP-állapotkód: a 403-as.

## <a name="next-steps"></a>További lépések
- [C# – rövid útmutató](csharp.md)
- [Java – rövid útmutató](java-quickstart.md)
- [JavaScript – rövid útmutató](javascript.md)
- [Node – rövid útmutató](node-quickstart.md)
- [Python – rövid útmutató](python-quickstart.md)
