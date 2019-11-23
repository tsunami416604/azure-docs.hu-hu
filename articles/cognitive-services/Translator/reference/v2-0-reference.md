---
title: Translator Text API 2.0-s verzió
titleSuffix: Azure Cognitive Services
description: A Translator Text API v 2.0 dokumentációja.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242501"
---
# <a name="translator-text-api-v20"></a>Translator Text API 2.0-s verzió

> [!IMPORTANT]
> A Translator Text API ezen verziója elavult. [Tekintse meg a Translator Text API 3. verziójának dokumentációját](v3-0-reference.md).

A Translator Text API 2. verziója zökkenőmentesen integrálható az alkalmazásaiba, webhelyeire, eszközeibe és egyéb megoldásaiba, hogy többnyelvű felhasználói élményt nyújtson. Az iparági szabványoknak megfelelően bármilyen hardveres platformon, bármely operációs rendszeren használhatja a nyelvi fordítást és más nyelvekkel kapcsolatos feladatokat, például a szöveg nyelvének észlelését és a beszéd szövegét. További információ: [Translator Text API](../translator-info-overview.md).

## <a name="getting-started"></a>Első lépések
A Translator Text API eléréséhez regisztrálnia kell a [Microsoft Azurera](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Authentication 
A Translator Text API összes hívásához előfizetési kulcs szükséges a hitelesítéshez. Az API három hitelesítési módszert támogat:

- Hozzáférési jogkivonat. A hozzáférési token létrehozásához használja az előfizetési kulcsot, ha POST-kérést küld a hitelesítési szolgáltatásnak. A részletekért tekintse meg a jogkivonat-szolgáltatás dokumentációját. Adja át a hozzáférési jogkivonatot a Translator szolgáltatásnak a `Authorization` fejléc vagy a `access_token` Query paraméter használatával. A hozzáférési jogkivonat 10 percig érvényes. 10 percenként szerezzen be egy új hozzáférési jogkivonatot, és használja továbbra is ugyanazt a hozzáférési jogkivonatot az ismételt kérelmekhez 10 percen belül.
- Közvetlenül használt előfizetési kulcs. Adja át az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejlécben szereplő értékként a Translator Text APIhoz. Ha közvetlenül használja az előfizetési kulcsot, nem kell meghívnia a jogkivonat-hitelesítési szolgáltatást a hozzáférési jogkivonat létrehozásához.
- Egy [Azure Cognitive Services Multi-Service előfizetés](https://azure.microsoft.com/pricing/details/cognitive-services/). Ez a módszer lehetővé teszi, hogy egyetlen titkos kulcsot használjon a kérelmek több szolgáltatáshoz való hitelesítéséhez.
Több szolgáltatásból álló titkos kulcs használata esetén két hitelesítési fejlécet kell tartalmaznia a kérelemmel. Az első fejléc átadja a titkos kulcsot. A második fejléc az előfizetéshez társított régiót adja meg:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

A régiót a több szolgáltatásból álló szöveges API-előfizetéshez kell megadni. A kiválasztott régió az egyetlen régió, amelyet a többszolgáltatásos előfizetési kulcs használatakor használhat a szöveges fordításhoz. Ugyanazt a régiót kell megadnia, amelyet akkor kell kiválasztani, amikor bejelentkezett a több szolgáltatásra szóló előfizetésre a Azure Portal.

Az elérhető régiók a következők: `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`és `westus2`.

Az előfizetési kulcs és a hozzáférési jogkivonat olyan titkos kulcsok, amelyeknek el kell rejteni a nézetből.

## <a name="profanity-handling"></a>Káromkodás-kezelési
A fordítói szolgáltatás általában megőrzi a forrásban lévő káromkodást. A trágárság foka és az olyan kontextus, amely a szavakat a kulturális környezettől függően eltérővé teszi. Így a megcélzott nyelvben a káromkodás foka növelhető vagy csökkenthető.

Ha meg szeretné akadályozni, hogy a fordítás káromkodása a forrás szövegében is megmaradjon, akkor az azt támogató metódusok káromkodás-szűrési beállítását is használhatja. A beállítással megadhatja, hogy szeretné-e megtekinteni a káromkodás törlését vagy jelölését a megfelelő címkékkel, vagy hogy engedélyezni kívánja-e a káromkodást a célhelyen. A `ProfanityAction` elfogadott értékei a következők: `NoAction` (alapértelmezett), `Marked`és `Deleted`.


|profanityAction    |Műveletek |Példa forrása (Japán)  |Példa fordításra (angol)  |
|:--|:--|:--|:--|
|NoAction   |Default (Alapértelmezett): Ugyanaz, mint a beállítás beállítása. A káromkodás a forrás és a cél között lesz továbbítva.        |彼はジャッカスです 。     |Ő egy seggfej.   |
|Megjelölve     |A profán szavakat az XML-címkék fogják tartalmazni \<a káromkodás > és \</profanity >.       |彼はジャッカスです 。 |\<káromkodás > a seggfej\</profanity >.  |
|Törölve    |A profán szavakat a rendszer a pótlás nélkül eltávolítja a kimenetből.     |彼はジャッカスです 。 |Ő a.   |

    
## <a name="excluding-content-from-translation"></a>Tartalom kizárása a fordításból
Ha címkével (például HTML (`contentType=text/html`) rendelkező tartalmat fordít le, időnként hasznos lehet a fordításból kizárni bizonyos tartalmakat. A `class=notranslate` attribútum használatával megadhatja a tartalmat, amely az eredeti nyelven marad. Az alábbi példában az első `div` elem tartalma nem lesz lefordítva, de a második `div` elem tartalma le lesz fordítva.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>Implementációs megjegyzések
Egy szöveges karakterlánc fordítása az egyik nyelvről a másikra.

A kérelem URI-ja `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Visszatérési érték:** A lefordított szöveget jelölő sztring.

Ha korábban már használta `AddTranslation` vagy `AddTranslationArray` egy 5 vagy magasabb minősítésű fordítás megadására ugyanarra a forrásra vonatkozó mondat esetében, `Translate` csak a rendszer számára elérhető legfelső szintű választ adja vissza. "Ugyanaz a forrás-mondat" pontosan ugyanaz (100%-osan), kivéve a kihasználás, a szóközök, a címkék és a központozás végén a mondat végére. Ha a minősítés nem 5 vagy újabb minősítéssel van tárolva, a visszaadott eredmény a Microsoft Translator általi automatikus fordítás lesz.

### <a name="response-class-status-200"></a>Válasz osztály (200-es állapot)

sztring

Válasz tartalmának típusa: Application/XML

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás    |Paraméter típusa|adattípus|
|:--|:--|:--|:--|:--|
|appid  |üres    |Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejlécet használja, hagyja üresen a `appid` mezőt. Egyéb esetben olyan karakterláncot adjon meg, amely `"Bearer" + " " + "access_token"`tartalmaz.|lekérdezés|sztring|
|szöveg|üres   |Kötelező. A lefordítani kívánt szöveget jelölő sztring. A szöveg legfeljebb 10 000 karaktert tartalmazhat.|lekérdezés|sztring|
|from|üres   |Választható. Egy karakterlánc, amely a lefordított szöveg nyelvi kódját jelöli. Például: en, angol nyelven.|lekérdezés|sztring|
|erre:|üres |Kötelező. Egy karakterlánc, amely annak a nyelvnek a kódját jelöli, amelybe a szöveget le kell fordítani.|lekérdezés|sztring|
|contentType|üres    |Választható. A lefordított szöveg formátuma. A támogatott formátumok `text/plain` (alapértelmezett) és `text/html`. Minden HTML-elemnek megfelelően formázottnak, teljes elemnek kell lennie.|lekérdezés|sztring|
|category|üres   |Választható. A fordítás kategóriáját (tartományát) tartalmazó karakterlánc. A mező alapértelmezett értéke: `general`.|lekérdezés|sztring|
|Engedélyezés|üres  |Kötelező, ha a `appid` és az `Ocp-Apim-Subscription-Key` fejléc is üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key|üres  |Kötelező, ha a `appid` és az `Authorization` fejléc is üres marad.|header|sztring|


### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérelem. A bemeneti paraméterek és a részletes hibaüzenetek bejelölése.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Adja meg a kérés időpontját & időpontját, valamint a válasz fejlécében szereplő kérelem AZONOSÍTÓját `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, hogy a hiba továbbra is fennáll-e.|

## <a name="post-translatearray"></a>/TranslateArray közzététele

### <a name="implementation-notes"></a>Implementációs megjegyzések
Lekéri a fordításokat több forrás szöveghez.

A kérelem URI-ja `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

A kérelem törzsének formátuma a következő:

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Ezek az elemek `TranslateArrayRequest`ban találhatók:


* `AppId`: kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejlécet használja, hagyja üresen a `AppId` mezőt. Egyéb esetben olyan karakterláncot adjon meg, amely `"Bearer" + " " + "access_token"`tartalmaz.
* `From`: nem kötelező. Egy karakterlánc, amely a lefordított szöveg nyelvi kódját jelöli. Ha ez a mező üresen marad, a válasz az automatikus nyelvfelismerés eredményét fogja tartalmazni.
* `Options`: nem kötelező. Egy `Options` objektum, amely a következő értékeket tartalmazza. Ezek mind opcionálisak, és alapértelmezés szerint a leggyakoribb beállítások. A megadott elemeknek betűrendes sorrendben kell szerepelniük.
    - `Category`: a fordítás kategóriáját (tartományát) tartalmazó karakterlánc. A mező alapértelmezett értéke: `general`.
    - `ContentType`: a lefordított szöveg formátuma. A támogatott formátumok a következők: `text/plain` (alapértelmezett), `text/xml`és `text/html`. Minden HTML-elemnek megfelelően formázottnak, teljes elemnek kell lennie.
    - `ProfanityAction`: megadja a káromkodások kezelését a korábban leírtaknak megfelelően. Az elfogadott értékek: `NoAction` (alapértelmezett), `Marked`és `Deleted`.
    - `State`: a kérés és a válasz összekapcsolását segítő felhasználói állapot. A válaszban ugyanazt a tartalmat adja vissza a rendszer.
    - `Uri`: a következő URI alapján szűri az eredményeket. Alapértelmezett: `all`.
    - `User`: a felhasználó eredményeinek szűrése. Alapértelmezett: `all`.
* `Texts`: kötelező. A fordítás szövegét tartalmazó tömb. Minden karakterláncnak ugyanabban a nyelven kell lennie. Az összes lefordítható szöveg összege nem lehet hosszabb 10 000 karakternél. A tömb elemeinek maximális száma 2 000.
* `To`: kötelező. Egy karakterlánc, amely annak a nyelvnek a kódját jelöli, amelybe a szöveget le kell fordítani.

Kihagyhatja a nem kötelező elemeket. A `TranslateArrayRequest` közvetlen gyermekeinek elemeit betűrendbe kell rendezni.

A `TranslateArray` metódus `application/xml` vagy `text/xml`t fogad el `Content-Type`hoz.

**Visszatérési érték:** Egy `TranslateArrayResponse` tömb. Minden `TranslateArrayResponse` rendelkezik ezekkel az elemekkel:

* `Error`: hibát jelez, ha van ilyen. Ellenkező esetben NULL értékre van állítva.
* `OriginalSentenceLengths`: egész számok tömbje, amely a forrás szövegében lévő egyes mondatok hosszát jelzi. A tömb hossza a mondatok számát jelzi.
* `TranslatedText`: a lefordított szöveg.
* `TranslatedSentenceLengths`: egész számok tömbje, amely a lefordított szövegben szereplő egyes mondatok hosszát jelzi. A tömb hossza a mondatok számát jelzi.
* `State`: a kérés és a válasz összekapcsolását segítő felhasználói állapot. Ugyanazt a tartalmat adja vissza, mint a kérelem.

Itt látható a válasz törzsének formátuma:

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Válasz osztály (200-es állapot)
A sikeres válasz a korábban ismertetett formátumban `TranslateArrayResponse` tömbök tömbjét tartalmazza.

sztring

Válasz tartalmának típusa: Application/XML

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Data type|
|:--|:--|:--|:--|:--|
|Engedélyezés|üres  |Kötelező, ha a `appid` és az `Ocp-Apim-Subscription-Key` fejléc is üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key|üres|Kötelező, ha a `appid` és az `Authorization` fejléc is üres marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód   |Ok|
|:--|:--|
|400    |Hibás kérelem. A bemeneti paraméterek és a részletes hibaüzenetek bejelölése. Gyakori hibák a következők: <ul><li>A tömb elem nem lehet üres.</li><li>Érvénytelen kategória.</li><li>A nyelvből érvénytelen.</li><li>A nyelvhez érvénytelen.</li><li>A kérelem túl sok elemet tartalmaz.</li><li>A from nyelv nem támogatott.</li><li>A – nyelv nem támogatott.</li><li>A fordítási kérelem túl sok adattal rendelkezik.</li><li>A HTML formátuma nem megfelelő.</li><li>Túl sok karakterlánc lett átadva a fordítási kérelemben.</li></ul>|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Adja meg a kérés időpontját & időpontját, valamint a válasz fejlécében szereplő kérelem AZONOSÍTÓját `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, hogy a hiba továbbra is fennáll-e.|

## <a name="post-getlanguagenames"></a>/GetLanguageNames közzététele

### <a name="implementation-notes"></a>Implementációs megjegyzések
Beolvassa a `languageCodes`paraméterként átadott nyelvek rövid neveit, honosítva az átadott `locale` nyelvre.

A kérelem URI-ja `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

A kérelem törzse tartalmaz egy karakterlánc-tömböt, amely az ISO 639-1 nyelvi kódokat jelöli, amelyeknek a felhasználóbarát neveket kell beolvasniuk. Például:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Visszatérési érték:** Egy olyan karakterlánc-tömb, amely a fordítói szolgáltatás által támogatott nyelvi neveket tartalmaz, honosítva a kért nyelvre.

### <a name="response-class-status-200"></a>Válasz osztály (200-es állapot)
Olyan karakterlánc-tömb, amely a fordítói szolgáltatás által támogatott nyelveket tartalmaz, honosítva a kért nyelvre.

sztring

Válasz tartalmának típusa: Application/XML
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Data type|
|:--|:--|:--|:--|:--|
|appid|üres|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejlécet használja, hagyja üresen a `appid` mezőt. Egyéb esetben olyan karakterláncot adjon meg, amely `"Bearer" + " " + "access_token"`tartalmaz.|lekérdezés|sztring|
|területi beállítás|üres |Kötelező. Egy karakterlánc, amely a következők egyikét jelöli, és a nyelvi nevek honosítására szolgál: <ul><li>Egy nyelvhez társított ISO 639 2-Letter kisbetűs kulturális kód és egy ISO 3166 2-betűs nagybetűs alkulturális kód kombinációja. <li>Az ISO 639 kisbetűs kulturális környezet kódja önmagában.|lekérdezés|sztring|
|Engedélyezés|üres  |Kötelező, ha a `appid` és az `Ocp-Apim-Subscription-Key` fejléc is üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key|üres  |Kötelező, ha a `appid` és az `Authorization` fejléc is üres marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérelem. A bemeneti paraméterek és a részletes hibaüzenetek bejelölése.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Adja meg a kérés időpontját & időpontját, valamint a válasz fejlécében szereplő kérelem AZONOSÍTÓját `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, hogy a hiba továbbra is fennáll-e.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Implementációs megjegyzések
Lekéri a fordítási szolgáltatás által támogatott nyelveket jelölő nyelvi kódok listáját.  a `Translate` és a `TranslateArray` az alábbi nyelvek közül kettőt lefordíthat.

A kérelem URI-ja `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Visszatérési érték:** Egy karakterlánc-tömb, amely a Translator szolgáltatás által támogatott nyelvi kódokat tartalmazza.

### <a name="response-class-status-200"></a>Válasz osztály (200-es állapot)
Egy karakterlánc-tömb, amely a Translator szolgáltatás által támogatott nyelvi kódokat tartalmazza.

sztring

Válasz tartalmának típusa: Application/XML
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Data type|
|:--|:--|:--|:--|:--|
|appid|üres|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejlécet használja, hagyja üresen a `appid` mezőt. Egyéb esetben olyan karakterláncot adjon meg, amely `"Bearer" + " " + "access_token"`tartalmaz.|lekérdezés|sztring|
|Engedélyezés|üres  |Kötelező, ha a `appid` és az `Ocp-Apim-Subscription-Key` fejléc is üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key|üres|Kötelező, ha a `appid` és az `Authorization` fejléc is üres marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérelem. A bemeneti paraméterek és a részletes hibaüzenetek bejelölése.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Adja meg a kérés időpontját & időpontját, valamint a válasz fejlécében szereplő kérelem AZONOSÍTÓját `X-MS-Trans-Info`.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, hogy a hiba továbbra is fennáll-e.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Implementációs megjegyzések
A Speech szintézishez elérhető nyelvek beolvasása.

A kérelem URI-ja `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Visszatérési érték:** Egy karakterlánc-tömb, amely a fordítói szolgáltatás által a Speech szintézis által támogatott nyelvi kódokat tartalmazza.

### <a name="response-class-status-200"></a>Válasz osztály (200-es állapot)
Egy karakterlánc-tömb, amely a fordítói szolgáltatás által a Speech szintézis által támogatott nyelvi kódokat tartalmazza.

sztring

Válasz tartalmának típusa: Application/XML

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Data type|
|:--|:--|:--|:--|:--|
|appid|üres|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejlécet használja, hagyja üresen a `appid` mezőt. Egyéb esetben olyan karakterláncot adjon meg, amely `"Bearer" + " " + "access_token"`tartalmaz.|lekérdezés|sztring|
|Engedélyezés|üres|Kötelező, ha a `appid` és az `Ocp-Apim-Subscription-Key` fejléc is üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key|üres|Kötelező, ha a `appid` és az `Authorization` fejléc is üres marad.|header|sztring|
 
### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400|Hibás kérelem. A bemeneti paraméterek és a részletes hibaüzenetek bejelölése.|
|401|Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Adja meg a kérés időpontját & időpontját, valamint a válasz fejlécében szereplő kérelem AZONOSÍTÓját `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, hogy a hiba továbbra is fennáll-e.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Implementációs megjegyzések
Az átadott szöveg WAV vagy MP3 streamjét adja vissza, amelyet a kívánt nyelven beszél.

A kérelem URI-ja `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Visszatérési érték:** Az átadott szöveg WAV vagy MP3 streamje, amely a kívánt nyelven beszélt.

### <a name="response-class-status-200"></a>Válasz osztály (200-es állapot)

binary

Válasz tartalmának típusa: Application/XML

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Data type|
|:--|:--|:--|:--|:--|
|appid|üres|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejlécet használja, hagyja üresen a `appid` mezőt. Egyéb esetben olyan karakterláncot adjon meg, amely `"Bearer" + " " + "access_token"`tartalmaz.|lekérdezés|sztring|
|szöveg|üres   |Kötelező. Egy karakterlánc, amely egy vagy több mondatot tartalmaz, amelyeket a megadott nyelven kell kiadni az adatfolyamhoz. A szöveg nem lehet hosszabb 2 000 karakternél.|lekérdezés|sztring|
|language|üres   |Kötelező. Egy karakterlánc, amely a szöveg támogatott nyelvi kódját jelöli. A kódnak a `GetLanguagesForSpeak`metódus által visszaadott kódok egyikének kell lennie.|lekérdezés|sztring|
|format|üres|Választható. Egy karakterlánc, amely megadja a Content-Type azonosítót. Jelenleg `audio/wav` és `audio/mp3` érhető el. Az alapértelmezett érték `audio/wav`.|lekérdezés|sztring|
|options|üres    |Választható. A szintetizált beszéd tulajdonságait megadó karakterlánc:<ul><li>`MaxQuality` és `MinSize` megadja a hangjel minőségét. `MaxQuality` a legmagasabb minőséget biztosítja. `MinSize` a legkisebb fájlméretet biztosítja. Az alapértelmezett érték `MinSize`.</li><li>`female` és `male` határozza meg a hang kívánt nemet. A mező alapértelmezett értéke: `female`. A függőleges sáv (<code>\|</code>) használatával több beállítást is megadhat. Például:  `MaxQuality|Male`.</li></li></ul>  |lekérdezés|sztring|
|Engedélyezés|üres|Kötelező, ha a `appid` és az `Ocp-Apim-Subscription-Key` fejléc is üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key|üres  |Kötelező, ha a `appid` és az `Authorization` fejléc is üres marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérelem. A bemeneti paraméterek és a részletes hibaüzenetek bejelölése.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Adja meg a kérés időpontját & időpontját, valamint a válasz fejlécében szereplő kérelem AZONOSÍTÓját `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, hogy a hiba továbbra is fennáll-e.|

## <a name="get-detect"></a>/Detect beolvasása

### <a name="implementation-notes"></a>Implementációs megjegyzések
A szöveg egy szakaszának nyelvét azonosítja.

A kérelem URI-ja `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Visszatérési érték:** Egy karakterlánc, amely két karakterből álló nyelvi kódot tartalmaz a szöveghez.

### <a name="response-class-status-200"></a>Válasz osztály (200-es állapot)

sztring

Válasz tartalmának típusa: Application/XML

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Data type|
|:--|:--|:--|:--|:--|
|appid|üres  |Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejlécet használja, hagyja üresen a `appid` mezőt. Egyéb esetben olyan karakterláncot adjon meg, amely `"Bearer" + " " + "access_token"`tartalmaz.|lekérdezés|sztring|
|szöveg|üres|Kötelező. A szöveget tartalmazó karakterlánc, amelynek a nyelvét azonosítani kell. A szöveg nem lehet hosszabb 10 000 karakternél.|lekérdezés|  sztring|
|Engedélyezés|üres|Kötelező, ha a `appid` és az `Ocp-Apim-Subscription-Key` fejléc is üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key  |üres    |Kötelező, ha a `appid` és az `Authorization` fejléc is üres marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400|Hibás kérelem. A bemeneti paraméterek és a részletes hibaüzenetek bejelölése.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Adja meg a kérés időpontját & időpontját, valamint a válasz fejlécében szereplő kérelem AZONOSÍTÓját `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, hogy a hiba továbbra is fennáll-e.|


## <a name="post-detectarray"></a>/DetectArray közzététele

### <a name="implementation-notes"></a>Implementációs megjegyzések

A karakterláncok tömbben lévő nyelveket azonosítja. Egymástól függetlenül észleli az egyes tömb elemek nyelvét, és a tömb minden egyes sorára vonatkozó eredményt ad vissza.

A kérelem URI-ja `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

A kérelem törzsének formátuma a következő:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

A szöveg nem lehet hosszabb 10 000 karakternél.

**Visszatérési érték:** Egy karakterlánc-tömb, amely két karakterből álló nyelvi kódot tartalmaz a bemeneti tömb minden sorához.

Itt látható a válasz törzsének formátuma:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Válasz osztály (200-es állapot)
a `DetectArray` sikeres volt. Egy olyan karakterlánc-tömböt ad vissza, amely két karakterből álló nyelvi kódot tartalmaz a bemeneti tömb minden sorához.

sztring

Válasz tartalmának típusa: Application/XML
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Data type|
|:--|:--|:--|:--|:--|
|appid|üres|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejlécet használja, hagyja üresen a `appid` mezőt. Egyéb esetben olyan karakterláncot adjon meg, amely `"Bearer" + " " + "access_token"`tartalmaz.|lekérdezés|sztring|
|Engedélyezés|üres|Kötelező, ha a `appid` és az `Ocp-Apim-Subscription-Key` fejléc is üres marad.  Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key|üres|Kötelező, ha a `appid` és az `Authorization` fejléc is üres marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérelem. A bemeneti paraméterek és a részletes hibaüzenetek bejelölése.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Adja meg a kérés időpontját & időpontját, valamint a válasz fejlécében szereplő kérelem AZONOSÍTÓját `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, hogy a hiba továbbra is fennáll-e.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Implementációs megjegyzések

> [!IMPORTANT]
> **Elavult Megjegyzés:** 2018. január 31-ig ez a metódus nem fogad új mondatokat. Hibaüzenet jelenik meg. Tekintse meg az együttműködési fordítási keretrendszer (CTF) változásairól szóló közleményt.

Fordítás hozzáadása a fordítási memóriához.

A kérelem URI-ja `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Válasz osztály (200-es állapot)

sztring

Válasz tartalmának típusa: alkalmazás: XML
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Data type   |
|:--|:--|:--|:--|:--|
|appid|üres|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejlécet használja, hagyja üresen a `appid` mezőt. Egyéb esetben olyan karakterláncot adjon meg, amely `"Bearer" + " " + "access_token"`tartalmaz.|lekérdezés|sztring|
|originalText|üres|Kötelező. Egy karakterlánc, amely a lefordítani kívánt szöveget tartalmazza. A karakterlánc maximális hossza 1 000 karakter.|lekérdezés|sztring|
|translatedText|üres |Kötelező. Egy karakterlánc, amely a célként megadott nyelvre fordított szöveget tartalmazza. A karakterlánc maximális hossza 2 000 karakter.|lekérdezés|sztring|
|from|üres   |Kötelező. Egy karakterlánc, amely a szöveg eredeti nyelvének nyelvi kódját jelöli. Például: en for angol és német nyelven.|lekérdezés|sztring|
|erre:|üres|Kötelező. Egy karakterlánc, amely a szöveg nyelvének fordítására szolgáló nyelv kódját jelöli.|lekérdezés|sztring|
|rating|üres |Választható. Egy egész szám, amely a karakterlánc minőségi minősítését jelöli. Az érték-10 és 10 között van. Az alapértelmezett érték 1.|lekérdezés|egész szám|
|contentType|üres    |Választható. A lefordított szöveg formátuma. A támogatott formátumok `text/plain` és `text/html`. Minden HTML-elemnek megfelelően formázottnak, teljes elemnek kell lennie.    |lekérdezés|sztring|
|category|üres|Választható. A fordítás kategóriáját (tartományát) tartalmazó karakterlánc. A mező alapértelmezett értéke: `general`.|lekérdezés|sztring|
|Felhasználó|üres|Kötelező. Egy karakterlánc, amely a beküldési kezdeményező nyomon követésére szolgál.|lekérdezés|sztring|
|URI|üres|Választható. A fordítás tartalmának helyét tartalmazó karakterlánc.|lekérdezés|sztring|
|Engedélyezés|üres|Kötelező, ha a `appid` és az `Ocp-Apim-Subscription-Key` fejléc is üres marad.  Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.  |header|sztring|
|OCP-Apim-Subscription-Key|üres|Kötelező, ha a `appid` és az `Authorization` fejléc is üres marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérelem. A bemeneti paraméterek és a részletes hibaüzenetek bejelölése.|
|401    |Érvénytelen hitelesítő adatok.|
|410|a `AddTranslation` már nem támogatott.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Adja meg a kérés időpontját & időpontját, valamint a válasz fejlécében szereplő kérelem AZONOSÍTÓját `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, hogy a hiba továbbra is fennáll-e.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Implementációs megjegyzések

> [!IMPORTANT]
> **Elavult Megjegyzés:** 2018. január 31-ig ez a metódus nem fogad új mondatokat. Hibaüzenet jelenik meg. Tekintse meg az együttműködési fordítási keretrendszer (CTF) változásairól szóló közleményt.

A fordítási memóriába fordítások tömbjét adja hozzá. Ez a metódus a `AddTranslation`tömb-verziója.

A kérelem URI-ja `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

A kérelem törzsének formátuma a következő:

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Ezek az elemek `AddtranslationsRequest`ban találhatók:

* `AppId`: kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejlécet használja, hagyja üresen a `AppId` mezőt. Egyéb esetben olyan karakterláncot adjon meg, amely `"Bearer" + " " + "access_token"`tartalmaz.
* `From`: kötelező. Egy karakterlánc, amely a forrás nyelvének nyelvi kódját tartalmazza. A `GetLanguagesForTranslate` metódus által visszaadott nyelvek egyikének kell lennie.
* `To`: kötelező. A célként megadott nyelv nyelvi kódját tartalmazó karakterlánc. A `GetLanguagesForTranslate` metódus által visszaadott nyelvek egyikének kell lennie.
* `Translations`: kötelező. A fordítási memóriába felvenni kívánt fordítások tömbje. Minden fordításnak `OriginalText`, `TranslatedText`és `Rating`nak kell lennie. Az egyes `OriginalText`ok és `TranslatedText`ek maximális mérete 1 000 karakter. Az összes `OriginalText` és `TranslatedText` elem összesen nem lehet hosszabb 10 000 karakternél. A tömb elemeinek maximális száma 100.
* `Options`: kötelező. Beállítások, például `Category`, `ContentType`, `Uri`és `User`. `User` megadása kötelező. a `Category`, a `ContentType`és a `Uri` nem kötelező. A megadott elemeknek betűrendes sorrendben kell szerepelniük.

### <a name="response-class-status-200"></a>Válasz osztály (200-es állapot)
`AddTranslationArray` metódus sikeres volt. 

2018. január 31-ig nem lesznek elfogadva a mondatok. A szolgáltatás az 410-es hibakódtal fog válaszolni.

sztring

Válasz tartalmának típusa: Application/XML
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Data type|
|:--|:--|:--|:--|:--|
|Engedélyezés|üres|Kötelező, ha a `appid` és az `Ocp-Apim-Subscription-Key` fejléc is üres marad.  Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key|üres|Kötelező, ha a `appid` és az `Authorization` fejléc is üres marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérelem. A bemeneti paraméterek és a részletes hibaüzenetek bejelölése.|
|401    |Érvénytelen hitelesítő adatok.|
|410    |a `AddTranslation` már nem támogatott.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Adja meg a kérés időpontját & időpontját, valamint a válasz fejlécében szereplő kérelem AZONOSÍTÓját `X-MS-Trans-Info`.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, hogy a hiba továbbra is fennáll-e.|

## <a name="get-breaksentences"></a>/BreakSentences beolvasása

### <a name="implementation-notes"></a>Implementációs megjegyzések
Megszakítja a szöveg egy szakaszát mondatokra, és egy tömböt ad vissza, amely az egyes mondatok hosszát tartalmazza.

A kérelem URI-ja `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Visszatérési érték:** Egész számok tömbje, amely a mondatok hosszát jelöli. A tömb hossza a mondatok számát jelöli. Az értékek az egyes mondatok hosszát jelölik.

### <a name="response-class-status-200"></a>Válasz osztály (200-es állapot)
Egész számok tömbje, amely a mondatok hosszát jelöli. A tömb hossza a mondatok számát jelöli. Az értékek az egyes mondatok hosszát jelölik.

egész szám

Válasz tartalmának típusa: Application/XML

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Data type|
|:--|:--|:--|:--|:--|
|appid|üres  |Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejlécet használja, hagyja üresen a `appid` mezőt. Egyéb esetben olyan karakterláncot adjon meg, amely `"Bearer" + " " + "access_token"`tartalmaz.|lekérdezés| sztring|
|szöveg|üres   |Kötelező. Egy karakterlánc, amely a mondatokra bontott szöveget jelöli. A szöveg maximális mérete 10 000 karakter.|lekérdezés|sztring|
|language   |üres    |Kötelező. Egy karakterlánc, amely a bemeneti szöveg nyelvi kódját jelöli.|lekérdezés|sztring|
|Engedélyezés|üres|Kötelező, ha a `appid` és az `Ocp-Apim-Subscription-Key` fejléc is üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.   |header|sztring|
|OCP-Apim-Subscription-Key|üres|Kötelező, ha a `appid` és az `Authorization` fejléc is üres marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400|Hibás kérelem. A bemeneti paraméterek és a részletes hibaüzenetek bejelölése.|
|401|Érvénytelen hitelesítő adatok.|
|500|Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Adja meg a kérés időpontját & időpontját, valamint a válasz fejlécében szereplő kérelem AZONOSÍTÓját `X-MS-Trans-Info`.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, hogy a hiba továbbra is fennáll-e.|

## <a name="post-gettranslations"></a>/GetTranslations közzététele

### <a name="implementation-notes"></a>Implementációs megjegyzések
Lekéri egy adott nyelvi pár fordítási tömbjét az áruházból és az MT-motorból. `GetTranslations` eltér a `Translate`tól, hogy az összes elérhető fordítást visszaadja.

A kérelem URI-ja `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

A kérelem törzse tartalmazza a választható `TranslationOptions` objektumot, amelynek formátuma a következő:

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

A `TranslateOptions` objektum a következő listában szereplő értékeket tartalmazza. Ezek mind opcionálisak, és alapértelmezés szerint a leggyakoribb beállítások. A megadott elemeknek betűrendes sorrendben kell szerepelniük.

* `Category`: a fordítás kategóriáját (tartományát) tartalmazó karakterlánc. A mező alapértelmezett értéke: `general`.
* `ContentType`: az egyetlen támogatott beállítás, az alapértelmezett érték pedig `text/plain`.
* `IncludeMultipleMTAlternatives`: logikai jelző, amely azt határozza meg, hogy az MT motorból egynél több alternatíva legyen-e visszaküldve. Az érvényes értékek: `true` és `false` (kis-és nagybetűk megkülönböztetése). Az alapértelmezett érték `false`, amely csak egy alternatív értéket ad vissza. A jelző `true` való beállítása lehetővé teszi mesterséges alternatívák létrehozását, amelyek teljes mértékben integrálva vannak az együttműködő fordítási keretrendszerrel (CTF). A szolgáltatás lehetővé teszi alternatívák visszaadását olyan mondatokhoz, amelyek nem rendelkeznek fordítással a CTF-ben a dekóder *n*-legjobb listájáról származó mesterséges alternatívák hozzáadásával.
    - Értékelés. A minősítések a következőképpen lesznek alkalmazva: 
         - A legjobb automatikus fordítás 5-ös minősítéssel rendelkezik.
       - A CTF-alternatívák a felülvizsgáló tekintélyét tükrözik. Ezek a-10 és + 10 közé esnek.
       - Az automatikusan generált (*n*-Best) fordítási alternatívák a 0 minősítéssel rendelkeznek, és a megfelelési fok 100.
    - Alternatívák száma. A visszaadott alternatívák száma lehet olyan magas, mint a `maxTranslations`ben megadott érték, de alacsonyabb lehet.
    - Nyelvi párok Ez a funkció nem érhető el az egyszerűsített kínai és a hagyományos kínai nyelv közötti fordításhoz mindkét irányban. A Microsoft Translator által támogatott összes többi nyelvi pár számára elérhető.
* `State`: a kérés és a válasz összekapcsolását segítő felhasználói állapot. A válaszban ugyanazt a tartalmat adja vissza a rendszer.
* `Uri`: a következő URI alapján szűri az eredményeket. Ha nincs megadva érték, a rendszer az alapértelmezett `all`.
* `User`: a felhasználó eredményeinek szűrése. Ha nincs megadva érték, a rendszer az alapértelmezett `all`.

A kérelem `Content-Type` `text/xml`nak kell lennie.

**Visszatérési érték:** A válasz formátuma a következő:

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Ez a válasz egy `GetTranslationsResponse` elemet tartalmaz, amely a következő értékeket tartalmazza:

* `Translations`: a találatok tömbje `TranslationMatch` objektumokban tárolva (lásd a következő szakaszt). A fordítások tartalmazhatják az eredeti szöveg enyhe változatait (a zavaros egyezést is beleértve). A fordítások rendezése: a 100% megfelel az elsőnek, a zavaros egyezések továbbra is.
* `From`: Ha a metódus nem ad meg `From` nyelvet, ez az érték az automatikus nyelvfelismerés alapján fog származni. Ellenkező esetben a megadott `From` nyelv lesz.
* `State`: a kérés és a válasz összekapcsolását segítő felhasználói állapot. A `TranslateOptions` paraméterben megadott értéket tartalmazza.

A `TranslationMatch` objektum a következő értékeket tartalmazza:

* `Error`: a hibakód, ha hiba történik egy adott bemeneti sztringnél. Ellenkező esetben ez a mező üres.
* `MatchDegree`: azt jelzi, hogy a bemeneti szöveg mennyire pontosan egyezik az áruházban található eredeti szöveggel. A rendszeren megegyezik a tárolóban lévő bemeneti mondatokkal, beleértve a nem pontos egyezéseket is. A visszaadott érték 0 és 100 között van, ahol a 0 nem hasonlóság, a 100 pedig pontos, kis-és nagybetűket megkülönböztető egyezés.
* `MatchedOriginalText`: az eredeti szöveg, amely megfelelt ehhez az eredményhez. Ezt az értéket csak akkor adja vissza a rendszer, ha az egyező eredeti szöveg eltér a bemeneti szövegtől. Ez egy fuzzy egyezés forrás szövegének visszaküldésére szolgál. Ez az érték nem érkezik vissza a Microsoft Translator eredményeihez.
* `Rating`: a minőségi döntést tevő személy hatóságát jelzi. A gépi fordítás eredményei 5 minősítéssel rendelkeznek. A névtelenül megadott fordítások általában 1 és 4 közötti minősítéssel rendelkeznek. A mérvadóan megadott fordítások általában 6 és 10 közötti minősítéssel rendelkeznek.
* `Count`: a fordítás ezen minősítéssel való kiválasztásának száma. A automatikusan lefordított válasz értéke 0.
* `TranslatedText`: a lefordított szöveg.

### <a name="response-class-status-200"></a>Válasz osztály (200-es állapot)
A korábban ismertetett formátumban `GetTranslationsResponse` objektum.

sztring

Válasz tartalmának típusa: Application/XML
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Data type|
|:--|:--|:--|:--|:--|
|appid|üres|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejlécet használja, hagyja üresen a `appid` mezőt. Egyéb esetben olyan karakterláncot adjon meg, amely `"Bearer" + " " + "access_token"`tartalmaz.|lekérdezés|sztring|
|szöveg|üres|Kötelező. A lefordítani kívánt szöveget jelölő sztring. A szöveg maximális mérete 10 000 karakter.|lekérdezés|sztring|
|from|üres|Kötelező. Egy karakterlánc, amely a lefordított szöveg nyelvi kódját jelöli.|lekérdezés|sztring|
|erre: |üres    |Kötelező. Egy karakterlánc, amely a szöveg nyelvének fordítására szolgáló nyelv kódját jelöli.|lekérdezés|sztring|
|maxTranslations|üres|Kötelező. Egy egész szám, amely a visszaadni kívánt fordítások maximális számát jelöli.|lekérdezés|egész szám|
|Engedélyezés| üres|Kötelező, ha a `appid` és az `Ocp-Apim-Subscription-Key` fejléc is üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|sztring|  header|
|OCP-Apim-Subscription-Key|üres  |Kötelező, ha a `appid` és az `Authorization` fejléc is üres marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérelem. A bemeneti paraméterek és a részletes hibaüzenetek bejelölése.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Adja meg a kérés időpontját & időpontját, valamint a válasz fejlécében szereplő kérelem AZONOSÍTÓját `X-MS-Trans-Info`.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, hogy a hiba továbbra is fennáll-e.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Implementációs megjegyzések
Több fordítási jelölt beolvasása több forrás szöveghez.

A kérelem URI-ja `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

A kérelem törzsének formátuma a következő:

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` az alábbi elemeket tartalmazza:

* `AppId`: kötelező. Ha a `Authorization` fejlécet használja, hagyja üresen a `AppId` mezőt. Egyéb esetben olyan karakterláncot adjon meg, amely `"Bearer" + " " + "access_token"`tartalmaz.
* `From`: kötelező. Egy karakterlánc, amely a lefordított szöveg nyelvi kódját jelöli.
* `MaxTranslations`: kötelező. Egy egész szám, amely a visszaadni kívánt fordítások maximális számát jelöli.
* `Options`: nem kötelező. Egy `Options` objektum, amely a következő értékeket tartalmazza. Ezek mind opcionálisak, és alapértelmezés szerint a leggyakoribb beállítások. A megadott elemeknek betűrendes sorrendben kell szerepelniük.
    - `Category`: a fordítás kategóriáját (tartományát) tartalmazó karakterlánc. A mező alapértelmezett értéke: `general`.
    - `ContentType`: az egyetlen támogatott beállítás, az alapértelmezett érték pedig `text/plain`.
    - `IncludeMultipleMTAlternatives`: logikai jelző, amely azt határozza meg, hogy az MT motorból egynél több alternatíva legyen-e visszaküldve. Az érvényes értékek: `true` és `false` (kis-és nagybetűk megkülönböztetése). Az alapértelmezett érték `false`, amely csak egy alternatív értéket ad vissza. Ha a jelzőt úgy állítja be, hogy `true` lehetővé teszi mesterséges alternatívák létrehozását a fordításban, teljes mértékben integrálva az együttműködő fordítási keretrendszerrel (CTF). A funkció lehetővé teszi olyan mondatok alternatíváinak visszaadását, amelyek nem rendelkeznek alternatívákkal a CTF-ben a dekóder *n*-legjobb listájából származó mesterséges alternatívák hozzáadásával.
        - A minősítések minősítését a következőhöz hasonlóan kell alkalmazni:
          - A legjobb automatikus fordítás 5-ös minősítéssel rendelkezik.
          - A CTF-alternatívák a felülvizsgáló tekintélyét tükrözik. Ezek a-10 és + 10 közé esnek.
          - Az automatikusan generált (*n*-Best) fordítási alternatívák a 0 minősítéssel rendelkeznek, és a megfelelési fok 100.
        - Alternatívák száma. A visszaadott alternatívák száma lehet olyan magas, mint a `maxTranslations`ben megadott érték, de alacsonyabb lehet.
        - Nyelvi párok Ez a funkció nem érhető el az egyszerűsített kínai és a hagyományos kínai nyelv közötti fordításhoz mindkét irányban. A Microsoft Translator által támogatott összes többi nyelvi pár számára elérhető.
* `State`: a kérés és a válasz összekapcsolását segítő felhasználói állapot. A válaszban ugyanazt a tartalmat adja vissza a rendszer.
* `Uri`: a következő URI alapján szűri az eredményeket. Ha nincs megadva érték, a rendszer az alapértelmezett `all`.
* `User`: a felhasználó eredményeinek szűrése. Ha nincs megadva érték, a rendszer az alapértelmezett `all`.
* `Texts`: kötelező. A fordítás szövegét tartalmazó tömb. Minden karakterláncnak ugyanabban a nyelven kell lennie. Az összes lefordítható szöveg összege nem lehet hosszabb 10 000 karakternél. A tömb elemeinek maximális száma 10.
* `To`: kötelező. Egy karakterlánc, amely a szöveg nyelvének fordítására szolgáló nyelv kódját jelöli.

Kihagyhatja a nem kötelező elemeket. A `GetTranslationsArrayRequest` közvetlen gyermekeinek elemeit betűrendbe kell rendezni.

A kérelem `Content-Type` `text/xml`nak kell lennie.

**Visszatérési érték:** A válasz formátuma a következő:

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Minden `GetTranslationsResponse` elem tartalmazza ezeket az értékeket:

* `Translations`: a találatok tömbje `TranslationMatch` objektumokban tárolva (lásd a következő szakaszt). A fordítások tartalmazhatják az eredeti szöveg enyhe változatait (a zavaros egyezést is beleértve). A fordítások rendezése: a 100% megfelel az elsőnek, a zavaros egyezések továbbra is.
* `From`: Ha a metódus nem ad meg `From` nyelvet, ez az érték az automatikus nyelvfelismerés alapján fog származni. Ellenkező esetben a megadott `From` nyelv lesz.
* `State`: a kérés és a válasz összekapcsolását segítő felhasználói állapot. A `TranslateOptions` paraméterben megadott értéket tartalmazza.

A `TranslationMatch` objektum a következő értékeket tartalmazza:
* `Error`: a hibakód, ha hiba történik egy adott bemeneti sztringnél. Ellenkező esetben ez a mező üres.
* `MatchDegree`: azt jelzi, hogy a bemeneti szöveg mennyire pontosan egyezik az áruházban található eredeti szöveggel. A rendszeren megegyezik a tárolóban lévő bemeneti mondatokkal, beleértve a nem pontos egyezéseket is. A visszaadott érték 0 és 100 között van, ahol a 0 nem hasonlóság, a 100 pedig pontos, kis-és nagybetűket megkülönböztető egyezés.
* `MatchedOriginalText`: az eredeti szöveg, amely megfelelt ehhez az eredményhez. Ezt az értéket csak akkor adja vissza a rendszer, ha az egyező eredeti szöveg eltér a bemeneti szövegtől. Ez egy fuzzy egyezés forrás szövegének visszaküldésére szolgál. Ez az érték nem érkezik vissza a Microsoft Translator eredményeihez.
* `Rating`: a minőségi döntést tevő személy hatóságát jelzi. A gépi fordítás eredményei 5 minősítéssel rendelkeznek. A névtelenül megadott fordítások általában 1 és 4 közötti minősítéssel rendelkeznek. A mérvadóan megadott fordítások általában 6 és 10 közötti minősítéssel rendelkeznek.
* `Count`: a fordítás ezen minősítéssel való kiválasztásának száma. A automatikusan lefordított válasz értéke 0.
* `TranslatedText`: a lefordított szöveg.


### <a name="response-class-status-200"></a>Válasz osztály (200-es állapot)

sztring

Válasz tartalmának típusa: Application/XML
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Data type|
|:--|:--|:--|:--|:--|
|Engedélyezés  |üres    |Kötelező, ha a `appid` és az `Ocp-Apim-Subscription-Key` fejléc is üres marad.  Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key|üres  |Kötelező, ha a `appid` és az `Authorization` fejléc is üres marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérelem. A bemeneti paraméterek és a részletes hibaüzenetek bejelölése.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Adja meg a kérés időpontját & időpontját, valamint a válasz fejlécében szereplő kérelem AZONOSÍTÓját `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, hogy a hiba továbbra is fennáll-e.|

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Migrálás Translator Text API v3-ra](../migrate-to-v3.md)


