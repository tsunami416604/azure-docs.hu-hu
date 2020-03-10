---
title: Projekt URL-címének előzetes verziója
titlesuffix: Azure Cognitive Services
description: A projekt URL-címének előnézeti végpontjának referenciája
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: f92c0faaaa3aa0cd2af16a031f3bed4c6b41fc22
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393955"
---
# <a name="project-url-preview-v7-reference"></a>Projekt URL-címének előzetes verziója – dokumentáció

Az URL-cím előzetes verziója támogatja a blogbejegyzések webes erőforrásainak rövid leírását, a fórum vitafórumait, az előnézeti lapokat stb. Az URL-cím bármilyen típusú internetes erőforrás lehet: weblap, hírek, képek vagy videók. A lekérdezésnek abszolút URL-címnek kell lennie, http-vagy https-sémával. a relatív URL-címek vagy más sémák, például a ftp://nem támogatottak.

Az URL-címet használó alkalmazások webes kéréseket küldenek a végpontnak egy lekérdezési paraméterben az előnézet URL-címével. A kérelemnek tartalmaznia kell a *OCP-APIM-Subscription-Key* fejlécet.

A JSON-válasz elemezhető az előzetes verzióra vonatkozó információk: név, az erőforrás, a *isFamilyFriendly*és a hivatkozások, amelyek hozzáférést biztosítanak a reprezentatív képhez és a teljes erőforráshoz.

Csak az URL-cím előnézetének adatait kell használnia, hogy megjelenjenek az előnézeti kódrészletek és a miniatűr képek a forrás helyükre mutató hiperhivatkozások, a felhasználó által kezdeményezett URL-cím megosztása a közösségi médián, a csevegési bot vagy hasonló ajánlatokban. A Project URL előzetes verziójából kapott adatok nem másolhatók, tárolhatók és nem gyorsítótárazható. Tiszteletben kell hagynia minden olyan kérést, amely letiltja a webhely vagy a tartalom tulajdonosai által esetlegesen megjelenő előnézeteket.

## <a name="endpoint"></a>Végpont
Az URL-cím előnézeti eredményeinek megkereséséhez küldjön egy kérelmet a következő végpontnak. A fejlécek és az URL-paraméterek használatával további specifikációkat határozhat meg.

Végpont lekérése:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

```

A kérelemnek a HTTPS protokollt kell használnia, és tartalmaznia kell a következő lekérdezési paramétert:

q – az előnézet URL-címét azonosító lekérdezés

A következő szakaszokban technikai részleteket talál a válaszok objektumairól, a lekérdezési paraméterekről és a keresési eredményeket érintő fejlécekről.

További információ a kérelmeket tartalmazó fejlécekről: [fejlécek](#headers).

További információ a kérelmeket tartalmazó lekérdezési paraméterekről: [lekérdezési paraméterek](#query-parameters).

További információ a válaszban szereplő JSON-objektumokról: [Response Objects](#response-objects).

A lekérdezési URL-cím maximális hossza 2 048 karakter. Annak érdekében, hogy az URL-cím hossza ne haladja meg a korlátot, a lekérdezési paraméterek maximális hosszának 1 500 karakternél rövidebbnek kell lennie. Ha az URL-cím meghaladja az 2 048 karaktert, a kiszolgáló a 404 értéket adja vissza.

További információ az eredmények használatáról és megjelenítéséről: [használati és megjelenítési követelmények](use-display-requirements.md).

> [!NOTE]
> Egyes, más keresési API-kra vonatkozó kérelem-fejlécek nem érintik az URL-cím előnézetét
> - Sorpragmákat – a hívó nem szabályozhatja, hogy az URL-előnézet a gyorsítótárat használja-e
> - Felhasználói ügynök – egyelőre az URL-előnézet API nem biztosít különböző válaszokat a PC-ről, laptopról vagy mobilról származó hívásokhoz.
> 
> Bizonyos paraméterek jelenleg nem értelmezhető az URL-cím előnézeti API-hoz, de a jövőben a jobb globalizáció érdekében is felhasználhatók.

## <a name="headers"></a>Fejlécek
A következő fejlécek lehetnek a kérések és válaszok.

|Fejléc|Leírás|
|------------|-----------------|
|<a name="market" />BingAPIs-Market|Válaszfejléc.<br /><br /> A kérelem által használt piac. A formátum a következő: \<languageCode\>-\<countryCode\>. Például: en-US.|
|<a name="traceid" />BingAPIs-TraceId|Válaszfejléc.<br /><br /> A kérelem részleteit tartalmazó naplóbejegyzés azonosítója. Ha hiba történik, rögzítse ezt az azonosítót. Ha nem tudja meghatározni és megoldani a problémát, foglalja bele a kérelembe ezt az azonosítót is a támogatási csoportnak megadott többi információval együtt.|
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Kötelező kérelemfejléc.<br /><br /> Az előfizetési kulcs, amelyet akkor kapott, amikor feliratkozott a szolgáltatásra a [Cognitive Servicesben](https://www.microsoft.com/cognitive-services/).|
|<a name="clientid" />X-MSEdge-ClientID|Választható kérelem- és válaszfejléc.<br /><br /> A Bing ezt a fejlécet használja ahhoz, hogy következetes viselkedést biztosítson a felhasználók számára a Bing API-hívásaiban. A Bing gyakran tesztel új funkciókat és fejlesztéseket, és az ügyfél-azonosítót használja kulcsként ahhoz, hogy a tesztcsomagokhoz rendelje a forgalmat. Ha a kérelmekben nem ugyanazt az ügyfél-azonosítót használja egy adott felhasználóhoz, előfordulhat, hogy a Bing több ütköző tesztcsomaghoz rendeli hozzá a felhasználót. Az ütköző tesztcsomagok hozzárendelése inkonzisztens felhasználói élményhez vezethet. Például ha a második kérelemhez más tesztcsomag van hozzárendelve, mint az elsőhöz, az váratlan működést eredményezhet. A Bing arra is felhasználhatja az ügyfél-azonosítót, hogy az ügyfél-azonosító keresési előzményeire szabja a webes találatokat, gazdagabb élményt nyújtva a felhasználónak.<br /><br /> A Bing továbbá az ügyfél-azonosító által létrehozott tevékenységek elemzésével az eredmények rangsorolásának javítására is használja a fejlécet. A relevancia javítása segít abban, hogy a Bing API-k jobb minőségű eredményeket biztosítsanak, ami pedig lehetővé teszi a magasabb átkattintási arányt az API fogyasztója számára.<br /><br />A fejlécre az alábbi alapvető használati szabályok vonatkoznak.<br /><ul><li>Minden felhasználónak, aki használja az eszközön lévő alkalmazást, rendelkeznie kell egy egyedi, Bing által létrehozott ügyfél-azonosítóval.<br /><br/>Ha nem foglalja bele ezt a fejlécet a kérelembe, a Bing létrehoz egy azonosítót, és visszaküldi azt az X-MSEdge-ClientID válaszfejlécben. Ezt a fejlécet csak akkor NEM szabad belefoglalni a kérelembe, amikor a felhasználó először használja az alkalmazást azon az eszközön.<br /><br/></li><li>Használja az ügyfél-azonosítót minden olyan Bing API-kéréshez, amelyet az alkalmazás intéz a felhasználó kapcsán az eszközön.<br /><br/></li><li>**Figyelem:** Győződjön meg arról, hogy ez az ügyfél-azonosító nem a hitelesíthető felhasználói fiókadatok linkable.</li><br/><li>Őrizze meg az ügyfél-azonosítót. Az azonosító böngészőalkalmazásban való megőrzéséhez használjon egy állandó HTTP-cookie-t, amely biztosítja, hogy minden munkamenetben ez az azonosító legyen használva. Ne használjon munkamenet-cookie-t. Más alkalmazások, például a mobilalkalmazások esetében az azonosító megőrzéséhez használja az eszköz állandó tárolóját.<br /><br/>Kérje le a megőrzött ügyfél-azonosítót, amikor a felhasználó ismét használja az alkalmazást az eszközön.</li></ul><br /> **MEGJEGYZÉS:** A Bing-válaszok nem mindig tartalmazzák ezt a fejlécet. Ha a válasz tartalmazza ezt a fejlécet, rögzítse az ügyfél-azonosítót, és használja azt a felhasználó összes további Bing-kérelméhez az adott eszközön.<br /><br /> **MEGJEGYZÉS:** Ha belefoglalja a kérelembe az X-MSEdge-ClientID fejlécet, akkor ne foglaljon bele cookie-kat.|
|<a name="clientip" />X-MSEdge-ClientIP|Választható kérelemfejléc.<br /><br /> Az ügyféleszköz IPv4- vagy IPv6-címe. Az IP-cím a felhasználó tartózkodási helyének felderítésére szolgál. A Bing arra használja a helyadatokat, hogy meghatározza a biztonságos keresés viselkedését.<br /><br /> Ne rejtse el a címet (például úgy, hogy 0-ra módosítja az utolsó oktettet). Ha elrejti a címet, a tartózkodási hely távol fog esni az eszköz tényleges helyétől, amely ahhoz vezethet, hogy a Bing téves eredményeket fog megadni.|

## <a name="query-parameters"></a>Lekérdezési paraméterek
A kérelem tartalmazhat a következő lekérdezési paramétereket. Tekintse meg a szükséges paraméterek oszlopát. A lekérdezési paraméterek kódolásához URL-címet kell megadni. A lekérdezésnek abszolút URL-címnek kell lennie, http-vagy https-sémával. nem támogatjuk a relatív URL-címeket vagy más, például a ftp://-sémákat

|Name (Név)|Érték|Típus|Kötelező|
|----------|-----------|----------|--------------|
|<a name="mkt" />mkt|A piac, ahonnan az eredmények származnak. <br /><br />A lehetséges piaci értékek listáját a piaci kódok részben tekintheti meg.<br /><br /> **Megjegyzés:** Az URL-cím előnézeti API jelenleg csak az USA földrajzát és az angol nyelvet támogatja.<br /><br />|Sztring|Igen|
|<a name="query" />q|Az előnézeti URL-cím|Sztring|Igen|
|<a name="responseformat" />responseFormat|A válaszhoz használandó adathordozó-típus. A következő a lehetséges kis-és nagybetűket megkülönböztető értékek.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Az alapértelmezett érték a JSON. A válasz által tartalmazott JSON-objektumokkal kapcsolatos információkért lásd: [Response Objects](#response-objects).<br /><br />Ha JsonLd ad meg, a válasz törzse JSON-LD objektumokat tartalmaz, amelyek tartalmazzák a keresési eredményeket. A JSON-LD-vel kapcsolatos információkért lásd: [JSON-ld](https://json-ld.org/).|Sztring|Nem|
|<a name="safesearch"/>safeSearch|Az illegális felnőtt tartalmak vagy a kalóz tartalmak blokkolva vannak a 400 hibakód miatt, és a rendszer nem adja vissza a *isFamilyFriendly* jelzőt. <p>A jogi felnőtt tartalom esetében az alábbi viselkedést láthatja. Az állapotkód a 200 értéket adja vissza, és a *isFamilyFriendly* jelző hamis értékre van állítva.<ul><li>safeSearch = Strict: a cím, a leírás, az URL-cím és a rendszerkép nem lesz visszaadva.</li><li>safeSearch = mérsékelt; Adja meg a címet, az URL-címet és a leírást, de ne a leíró képet.</li><li>safeSearch = kikapcsolva; Az összes válasz objektum/elem – cím, URL, leírás és rendszerkép lekérése.</li></ul> |Sztring|Nem kötelező. </br> Alapértelmezés szerint a safeSearch = strict.|

## <a name="response-objects"></a>Válasz objektumok
A válasz sémája egy [weblap] vagy ErrorResponse, mint a Web Search API. Ha a kérelem meghiúsul, a legfelső szintű objektum a [ErrorResponse](#errorresponse) objektum.

|Objektum|Leírás|
|------------|-----------------|
|[Weblap](#webpage)|A legfelső szintű JSON-objektum, amely az előzetes verzió attribútumait tartalmazza.|

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
|hibák <a name="errors" />|Azon hibák listája, amelyek leírják, miért nem sikerült a kérelem végrehajtása.|[Hiba](#error)[]|

### <a name="webpage"></a>WebPage
Az előzetes verzióban elérhető weblapokra vonatkozó információkat határozza meg.

|Name (Név)|Érték|Típus|
|----------|-----------|----------|
|név|Az oldal címe, nem feltétlenül a HTML-cím|Sztring|
|url|A ténylegesen bejárt URL-cím (a kérés követte az átirányítást)|Sztring|
|leírás|Az oldal és a tartalom rövid leírása|Sztring|
|isFamilyFriendly|A legpontosabb a webes index elemeinél. a valós idejű lekérések ezt az észlelést kizárólag az URL-címen, nem pedig az oldal tartalmán alapulnak.|logikai|
|primaryImageOfPage/contentUrl|Az előzetes verzióban szerepeltetni kívánt reprezentatív kép URL-címe|Sztring|

### <a name="identifiable"></a>Azonosítható
|Name (Név)|Érték|Típus|
|-------------|-----------------|----------|
|id|Erőforrás-azonosító|Sztring|

## <a name="error-codes"></a>Hibakódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők:

|Állapotkód|Leírás|
|-----------------|-----------------|
|200|Sikeres.|
|400|A lekérdezési paraméterek egyike hiányzik vagy érvénytelen.|
|400|ServerError, alkód ResourceError: a kért URL-cím nem érhető el|
|400|ServerError, alkód ResourceError: a kért URL-cím nem adott vissza sikerességi kódot (beleértve, ha a HTTP 404 értéket adta vissza)|
|400|InvalidRequest, alkód Letiltva: a kért URL-cím tartalmazhat felnőtt tartalmat, és blokkolva lett|
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
- [C# – rövid útmutató](csharp.md)
- [Java – rövid útmutató](java-quickstart.md)
- [JavaScript – rövid útmutató](javascript.md)
- [Node – rövid útmutató](node-quickstart.md)
- [Python – rövid útmutató](python-quickstart.md)
