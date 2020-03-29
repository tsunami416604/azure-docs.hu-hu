---
title: Fordító szöveg API-v2.0
titleSuffix: Azure Cognitive Services
description: A Translator Text API 2.0-s számának referenciadokumentációja.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72242501"
---
# <a name="translator-text-api-v20"></a>Fordító szöveg API-v2.0

> [!IMPORTANT]
> A Translator Text API ezen verziója elavult. [Tekintse meg a Translator Text API 3-as verziójának dokumentációját.](v3-0-reference.md)

A Translator Text API 2-es verziója zökkenőmentesen integrálható alkalmazásaiba, webhelyeibe, eszközeibe vagy más megoldásaiba a többnyelvű felhasználói élmény biztosítása érdekében. Bármilyen hardverplatformon és operációs rendszeren használhatja nyelvi fordításés más, nyelvekkel kapcsolatos feladatok elvégzésére, például szövegnyelv-felismerésre és szöveg-beszéd nyelven, az iparági szabványoknak megfelelően. További információ: [Translator Text API](../translator-info-overview.md).

## <a name="getting-started"></a>Első lépések
A Translator Text API eléréséhez regisztrálnia kell [a Microsoft Azure szolgáltatásra.](../translator-text-how-to-signup.md)

## <a name="authentication"></a>Hitelesítés 
A Translator Text API minden hívásához előfizetési kulcs szükséges a hitelesítéshez. Az API három hitelesítési módszert támogat:

- Egy hozzáférési jogkivonat. Az előfizetési kulcs használatával hozzon létre egy hozzáférési jogkivonatot postai kérelem a hitelesítési szolgáltatáshoz. A részleteket a tokenszolgáltatás dokumentációjában találja. Adja át a hozzáférési jogkivonatot `Authorization` a `access_token` Fordító szolgáltatásnak a fejléc vagy a lekérdezési paraméter használatával. A hozzáférési jogkivonat 10 percig érvényes. Szerezzen be egy új hozzáférési jogkivonatot 10 percenként, és továbbra is ugyanazt a hozzáférési jogkivonatot használja a 10 perc alatt ismételt kérelmekhez.
- Közvetlenül használt előfizetési kulcs. Adja át az előfizetési `Ocp-Apim-Subscription-Key` kulcsot a kérelemben szereplő fejlécben szereplő értékként a Translator Text API-nak. Ha közvetlenül használja az előfizetési kulcsot, nem kell meghívnia a jogkivonat-hitelesítési szolgáltatást egy hozzáférési jogkivonat létrehozásához.
- [Azure Cognitive Services többszolgáltatásos előfizetése.](https://azure.microsoft.com/pricing/details/cognitive-services/) Ez a módszer lehetővé teszi, hogy egyetlen titkos kulcsot használjon több szolgáltatás ra vonatkozó kérelmek hitelesítéséhez.
Ha többszolgáltatásos titkos kulcsot használ, két hitelesítési fejlécet kell megadnia a kéréshez. Az első fejléc átadja a titkos kulcsot. A második fejléc az előfizetéshez társított régiót határozza meg:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

A régió szükséges a többszolgáltatásos szöveges API-előfizetéshez. A kiválasztott terület az egyetlen olyan terület, amelyet a többszolgáltatásos előfizetési kulcs használatakor használhat a szövegfordításhoz. Ugyanannak a régiónak kell lennie, amelyet akkor választott ki, amikor feliratkozott a többszolgáltatásos előfizetésre az Azure Portalon.

A rendelkezésre `australiaeast` `brazilsouth`álló `canadacentral` `centralindia`régiók `centraluseuap` `eastasia`a `eastus` `eastus2`következők: , , `westcentralus` `westeurope`, `westus` `westus2` `japaneast` `northeurope`, , `southcentralus`, `southeastasia`, `uksouth`, , , , , és .

Az előfizetési kulcs és a hozzáférési jogkivonat titkos kulcsok, amelyeket el kell rejteni a nézetből.

## <a name="profanity-handling"></a>Káromkodás kezelése
Normális esetben a Fordító szolgáltatás megtartja a forrásban jelen lévő káromkodást. A káromkodás mértéke és a kontextus, ami a szavakat a kultúrától függően különbözik. Tehát a káromkodás mértéke a célnyelven növelhető vagy csökkenthető.

Ha meg szeretné akadályozni a káromkodása a fordításban akkor is, ha a forrásszövegben van, használhatja a káromkodásszűrési lehetőséget az azt támogató módszerekhez. A beállítás sal megadhatja, hogy a káromkodást törölni vagy megfelelő címkékkel megjelölni szeretné-e, vagy engedélyezni szeretné-e a káromkodást a célban. Az elfogadott `ProfanityAction` értékek `NoAction` (alapértelmezett), `Marked` `Deleted`és .


|ProfanityAction (Káromkodás– Művelet    |Műveletek |Példa forrás (japán)  |Példa fordításra (magyar)  |
|:--|:--|:--|:--|
|NoAction (Művelet nélkül)   |Default (Alapértelmezett): Ugyanaz, mint nem a beállítás a lehetőséget. A káromkodás a forrástól a célig tart.        |A következő kretén     |Ő egy barom.   |
|Jelölt     |A profán szavakat XML-címkék \<veszik \<körül, amelyek káromkodást> és /profanitás>.       |A következő kretén |Ő egy \<káromkodás>seggfej\</ káromkodás>.  |
|Törölve    |A profán szavak csere nélkül törlődnek a kimenetből.     |A következő kretén |Ő egy.   |

    
## <a name="excluding-content-from-translation"></a>Tartalom kizárása a fordításból
Ha olyan címkékkel , például HTML ( címkével ( írja le a tartalmat),`contentType=text/html`néha hasznos lehet kizárni bizonyos tartalmakat a fordításból. Az attribútum segítségével `class=notranslate` megadhatja azokat a tartalmakat, amelyeknek az eredeti nyelvükön kell maradniuk. A következő példában az első `div` elem tartalma nem lesz lefordítva, `div` de a második elem tartalma le lesz fordítva.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Fordítás

### <a name="implementation-notes"></a>Végrehajtási megjegyzések
Egy szöveges karakterláncot fordít le egyik nyelvről a másikra.

A kérelem `https://api.microsofttranslator.com/V2/Http.svc/Translate`URI.

**Visszatérési érték:** A lefordított szöveget jelölő karakterlánc.

Ha korábban `AddTranslation` `AddTranslationArray` használt, vagy adjon meg egy fordítást, amelynek `Translate` értékelése 5 vagy magasabb ugyanahhoz a forrásmondathoz, csak a rendszer számára elérhető legjobb választást adja vissza. Az "Azonos forrásmondat" pontosan ugyanazt jelenti (100%-os egyezés), kivéve a nagybetűs írásjeleket, a szóközt, a címkeértékeket és az írásjeleket a mondat végén. Ha a rendszer nem tárol 5-ös vagy annál magasabb minősítésű minősítést, a visszaadott eredmény a Microsoft Translator automatikus fordítása lesz.

### <a name="response-class-status-200"></a>Válaszosztály (200-as állapot)

sztring

Választartalom típusa: alkalmazás/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás    |Paraméter típusa|adattípus|
|:--|:--|:--|:--|:--|
|Appid  |(üres)    |Kötelező. Ha `Authorization` a `Ocp-Apim-Subscription-Key` vagy a fejlécet használja, hagyja üresen a `appid` mezőt. Ellenkező esetben adjon meg `"Bearer" + " " + "access_token"`egy karakterláncot, amely tartalmazza a .|lekérdezés|sztring|
|szöveg|(üres)   |Kötelező. A fordítandó szöveget jelölő karakterlánc. A szöveg legfeljebb 10 000 karaktert tartalmazhat.|lekérdezés|sztring|
|honnan|(üres)   |Választható. A lefordított szöveg nyelvkódját jelölő karakterlánc. Például en az angol.|lekérdezés|sztring|
|erre:|(üres) |Kötelező. Olyan karakterlánc, amely annak a nyelvnek a kódját jelöli, amelybe a szöveget le szeretné fordítani.|lekérdezés|sztring|
|contentType típusú|(üres)    |Választható. A lefordított szöveg formátuma. A támogatott `text/plain` formátumok (alapértelmezett) és `text/html`a . Minden HTML elemnek jól formázott, teljes elemeknek kell lennie.|lekérdezés|sztring|
|category|(üres)   |Választható. A fordítás kategóriáját (tartományát) tartalmazó karakterlánc. A mező alapértelmezett értéke: `general`.|lekérdezés|sztring|
|Engedélyezés|(üres)  |Kötelező, ha `appid` a `Ocp-Apim-Subscription-Key` mező és a fej is üresen marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Ocp-Apim-Subscription-Key|(üres)  |Kötelező, ha `appid` a `Authorization` mező és a fej is üresen marad.|header|sztring|


### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Rossz kérés. Ellenőrizze a bemeneti paramétereket és a részletes hibaválaszt.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Kérjük, adja meg & kérelem hozzávetőleges dátumát, valamint `X-MS-Trans-Info`a válaszfejlécben található kérésazonosítót.|
|503    |A szolgáltatás átmenetileg nem érhető el. Kérjük, próbálja meg újra, és tudassa velünk, ha a hiba továbbra is fennáll.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Végrehajtási megjegyzések
Több forrásszöveg fordítását olvassa le.

A kérelem `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`URI.

A kérelem törzsének formátuma:

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

Ezek az `TranslateArrayRequest`elemek a következők:


* `AppId`: Kötelező. Ha `Authorization` a `Ocp-Apim-Subscription-Key` vagy a fejlécet használja, hagyja üresen a `AppId` mezőt. Ellenkező esetben adjon meg `"Bearer" + " " + "access_token"`egy karakterláncot, amely tartalmazza a .
* `From`: Nem kötelező. A lefordított szöveg nyelvkódját jelölő karakterlánc. Ha ez a mező üresen marad, a válasz tartalmazza az automatikus nyelvfelismerés eredményét.
* `Options`: Nem kötelező. A `Options` következő értékeket tartalmazó objektum. Ezek mind választható, és alapértelmezés szerint a leggyakoribb beállításokat. A megadott elemeket betűrendben kell felsorolni.
    - `Category`: A fordítás kategóriáját (tartományát) tartalmazó karakterlánc. A mező alapértelmezett értéke: `general`.
    - `ContentType`: A lefordított szöveg formátuma. A támogatott formátumok `text/plain` (alapértelmezett), `text/xml` `text/html`a , a és a . Minden HTML elemnek jól formázott, teljes elemeknek kell lennie.
    - `ProfanityAction`: Megadja a káromkodások kezelésének módját, ahogy azt korábban kifejtettük. Az elfogadott `NoAction` értékek `Marked`(alapértelmezett), a `Deleted`és a.
    - `State`: A felhasználó állapota a kérés és a válasz korrelációjának elősegítésére. A válaszban ugyanaz a tartalom jelenik meg.
    - `Uri`: Szűrje az eredményeket ezzel az URI-val. Alapértelmezett: `all`.
    - `User`: Szűrje az eredményeket a felhasználó szerint. Alapértelmezett: `all`.
* `Texts`: Kötelező. A fordítás szövegét tartalmazó tömb. Minden karakterláncnak ugyanazon a nyelven kell lennie. A fordítandó szöveg összege nem haladhatja meg a 10 000 karaktert. A tömbelemek maximális száma 2000.
* `To`: Kötelező. Olyan karakterlánc, amely annak a nyelvnek a kódját jelöli, amelybe a szöveget le szeretné fordítani.

A választható elemeket kihagyhatja. Azokat az `TranslateArrayRequest` elemeket, amelyek közvetlen gyermekei, betűrendben kell felsorolni.

A `TranslateArray` metódus `application/xml` elfogadja, vagy `text/xml` a. `Content-Type`

**Visszatérési érték:** Egy `TranslateArrayResponse` tömb. Mindegyiknek `TranslateArrayResponse` vannak ezek az elemei:

* `Error`: Hibát jelez, ha előfordul. Ellenkező esetben null értékre van állítva.
* `OriginalSentenceLengths`: Egész számok ból álló tömb, amely a forrásszöveg ben lévő egyes mondatok hosszát jelzi. A tömb hossza a mondatok számát jelzi.
* `TranslatedText`: A lefordított szöveg.
* `TranslatedSentenceLengths`: Egész számok ból álló tömb, amely a lefordított szöveg minden mondatának hosszát jelzi. A tömb hossza a mondatok számát jelzi.
* `State`: A felhasználó állapota a kérés és a válasz korrelációjának elősegítésére. Ugyanazt a tartalmat adja vissza, mint a kérés.

Itt van a formátum a válasz törzs:

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

### <a name="response-class-status-200"></a>Válaszosztály (200-as állapot)
A sikeres válasz tömbtömbök tömbjeit `TranslateArrayResponse` tartalmazza a korábban leírt formátumban.

sztring

Választartalom típusa: alkalmazás/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Engedélyezés|(üres)  |Kötelező, ha `appid` a `Ocp-Apim-Subscription-Key` mező és a fej is üresen marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Ocp-Apim-Subscription-Key|(üres)|Kötelező, ha `appid` a `Authorization` mező és a fej is üresen marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód   |Ok|
|:--|:--|
|400    |Rossz kérés. Ellenőrizze a bemeneti paramétereket és a részletes hibaválaszt. Gyakori hibák a következők: <ul><li>A tömbelem nem lehet üres.</li><li>Érvénytelen kategória.</li><li>A külföld nyelv érvénytelen.</li><li>A nyelv érvénytelen.</li><li>A kérelem túl sok elemet tartalmaz.</li><li>A From nyelv nem támogatott.</li><li>A To nyelv nem támogatott.</li><li>A Fordítási kérelem túl sok adatot ad meg.</li><li>A HTML formátuma nem megfelelő.</li><li>Túl sok karakterlánc ot adott át a Fordítási kérelemben.</li></ul>|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Kérjük, adja meg & kérelem hozzávetőleges dátumát, valamint `X-MS-Trans-Info`a válaszfejlécben található kérésazonosítót.|
|503    |A szolgáltatás átmenetileg nem érhető el. Kérjük, próbálja meg újra, és tudassa velünk, ha a hiba továbbra is fennáll.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Végrehajtási megjegyzések
Lekéri a paraméterként `languageCodes`megadott nyelvek rövid nevét, az `locale` átadott nyelvre lokalizálva.

A kérelem `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`URI.

A kérelem törzse tartalmaz egy karakterlánc-tömböt, amely az ISO 639-1 nyelvkódokat jelöli, amelyekhez a rövid neveket be kell olvasni. Például:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Visszatérési érték:** A Translator szolgáltatás által támogatott, a kért nyelvre honosított nyelvneveket tartalmazó karakterlánctömb.

### <a name="response-class-status-200"></a>Válaszosztály (200-as állapot)
A Translator szolgáltatás által támogatott, a kért nyelvre honosított nyelveket tartalmazó karakterlánctömb.

sztring

Választartalom típusa: alkalmazás/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Appid|(üres)|Kötelező. Ha `Authorization` a `Ocp-Apim-Subscription-Key` vagy a fejlécet használja, hagyja üresen a `appid` mezőt. Ellenkező esetben adjon meg `"Bearer" + " " + "access_token"`egy karakterláncot, amely tartalmazza a .|lekérdezés|sztring|
|locale|(üres) |Kötelező. A nyelvnevek honosítására használt karakterlánc, amely az alábbiak egyikét képviseli: <ul><li>Egy nyelvhez társított ISO 639 kétbetűs tenyészetkód és egy ISO 3166 kétbetűs kis-szubkultúra kód kombinációja. <li>Egy ISO 639 kiskultakultúra-kód önmagában.|lekérdezés|sztring|
|Engedélyezés|(üres)  |Kötelező, ha `appid` a `Ocp-Apim-Subscription-Key` mező és a fej is üresen marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Ocp-Apim-Subscription-Key|(üres)  |Kötelező, ha `appid` a `Authorization` mező és a fej is üresen marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Rossz kérés. Ellenőrizze a bemeneti paramétereket és a részletes hibaválaszt.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Kérjük, adja meg & kérelem hozzávetőleges dátumát, valamint `X-MS-Trans-Info`a válaszfejlécben található kérésazonosítót.|
|503    |A szolgáltatás átmenetileg nem érhető el. Kérjük, próbálja meg újra, és tudassa velünk, ha a hiba továbbra is fennáll.|

## <a name="get-getlanguagesfortranslate"></a>GET /GETLanguagesForTranslate

### <a name="implementation-notes"></a>Végrehajtási megjegyzések
A fordítási szolgáltatás által támogatott nyelveket jelölő nyelvi kódok listájának leése.  `Translate`és `TranslateArray` lefordíthatja bármelyik két ilyen nyelven.

A kérelem `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`URI.

**Visszatérési érték:** A Translator szolgáltatás által támogatott nyelvkódokat tartalmazó karakterlánctömb.

### <a name="response-class-status-200"></a>Válaszosztály (200-as állapot)
A Translator szolgáltatás által támogatott nyelvkódokat tartalmazó karakterlánctömb.

sztring

Választartalom típusa: alkalmazás/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Appid|(üres)|Kötelező. Ha `Authorization` a `Ocp-Apim-Subscription-Key` vagy a fejlécet használja, hagyja üresen a `appid` mezőt. Ellenkező esetben adjon meg `"Bearer" + " " + "access_token"`egy karakterláncot, amely tartalmazza a .|lekérdezés|sztring|
|Engedélyezés|(üres)  |Kötelező, ha `appid` a `Ocp-Apim-Subscription-Key` mező és a fej is üresen marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Ocp-Apim-Subscription-Key|(üres)|Kötelező, ha `appid` a `Authorization` mező és a fej is üresen marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Rossz kérés. Ellenőrizze a bemeneti paramétereket és a részletes hibaválaszt.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Kérjük, adja meg & kérelem hozzávetőleges dátumát, valamint `X-MS-Trans-Info`a válaszfejlécben található kérésazonosítót.|
|503|A szolgáltatás átmenetileg nem érhető el. Kérjük, próbálja meg újra, és tudassa velünk, ha a hiba továbbra is fennáll.|

## <a name="get-getlanguagesforspeak"></a>GET /GETLanguagesForSpeak

### <a name="implementation-notes"></a>Végrehajtási megjegyzések
Beolvassa a beszédszintézishez rendelkezésre álló nyelveket.

A kérelem `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`URI.

**Visszatérési érték:** A translator szolgáltatás által támogatott beszédszintézishez támogatott nyelvkódokat tartalmazó karakterlánctömb.

### <a name="response-class-status-200"></a>Válaszosztály (200-as állapot)
A translator szolgáltatás által támogatott beszédszintézishez támogatott nyelvkódokat tartalmazó karakterlánctömb.

sztring

Választartalom típusa: alkalmazás/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Appid|(üres)|Kötelező. Ha `Authorization` a `Ocp-Apim-Subscription-Key` vagy a fejlécet használja, hagyja üresen a `appid` mezőt. Ellenkező esetben adjon meg `"Bearer" + " " + "access_token"`egy karakterláncot, amely tartalmazza a .|lekérdezés|sztring|
|Engedélyezés|(üres)|Kötelező, ha `appid` a `Ocp-Apim-Subscription-Key` mező és a fej is üresen marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Ocp-Apim-Subscription-Key|(üres)|Kötelező, ha `appid` a `Authorization` mező és a fej is üresen marad.|header|sztring|
 
### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400|Rossz kérés. Ellenőrizze a bemeneti paramétereket és a részletes hibaválaszt.|
|401|Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Kérjük, adja meg & kérelem hozzávetőleges dátumát, valamint `X-MS-Trans-Info`a válaszfejlécben található kérésazonosítót.|
|503    |A szolgáltatás átmenetileg nem érhető el. Kérjük, próbálja meg újra, és tudassa velünk, ha a hiba továbbra is fennáll.|

## <a name="get-speak"></a>GET /Beszéd

### <a name="implementation-notes"></a>Végrehajtási megjegyzések
Az átadott szöveg WAV vagy MP3 adatfolyamát adja vissza, amelya kívánt nyelven beszél.

A kérelem `https://api.microsofttranslator.com/V2/Http.svc/Speak`URI.

**Visszatérési érték:** Wav vagy MP3 stream az átadott szövegből, a kívánt nyelven.

### <a name="response-class-status-200"></a>Válaszosztály (200-as állapot)

binary

Választartalom típusa: alkalmazás/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Appid|(üres)|Kötelező. Ha `Authorization` a `Ocp-Apim-Subscription-Key` vagy a fejlécet használja, hagyja üresen a `appid` mezőt. Ellenkező esetben adjon meg `"Bearer" + " " + "access_token"`egy karakterláncot, amely tartalmazza a .|lekérdezés|sztring|
|szöveg|(üres)   |Kötelező. Olyan karakterlánc, amely egy vagy több mondatot tartalmaz, amelyet a megadott nyelven kell kimondani az adatfolyamhoz. A szöveg nem haladhatja meg a 2000 karaktert.|lekérdezés|sztring|
|language|(üres)   |Kötelező. Olyan karakterlánc, amely annak a nyelvnek a támogatott nyelvi kódját jelöli, amelyen a szöveget ki kell írni. A kódnak a módszer `GetLanguagesForSpeak`által visszaadott kódok egyikének kell lennie.|lekérdezés|sztring|
|Formátum|(üres)|Választható. A tartalomtípus-azonosítót megíró karakterlánc. Jelenleg, `audio/wav` `audio/mp3` és rendelkezésre állnak. Az alapértelmezett érték `audio/wav`.|lekérdezés|sztring|
|Lehetőségek|(üres)    |Választható. A szintetizált beszéd tulajdonságait megadó karakterlánc:<ul><li>`MaxQuality`és `MinSize` adja meg az audiojel minőségét. `MaxQuality`a legmagasabb minőséget nyújtja. `MinSize`a legkisebb fájlméretet biztosítja. Az alapértelmezett `MinSize`érték a .</li><li>`female`és `male` adja meg a kívánt nemet. A mező alapértelmezett értéke: `female`. A függőleges sáv<code>\|</code>( ) segítségével több beállítást is megad. Például:  `MaxQuality|Male`.</li></li></ul>  |lekérdezés|sztring|
|Engedélyezés|(üres)|Kötelező, ha `appid` a `Ocp-Apim-Subscription-Key` mező és a fej is üresen marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Ocp-Apim-Subscription-Key|(üres)  |Kötelező, ha `appid` a `Authorization` mező és a fej is üresen marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Rossz kérés. Ellenőrizze a bemeneti paramétereket és a részletes hibaválaszt.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Kérjük, adja meg & kérelem hozzávetőleges dátumát, valamint `X-MS-Trans-Info`a válaszfejlécben található kérésazonosítót.|
|503    |A szolgáltatás átmenetileg nem érhető el. Kérjük, próbálja meg újra, és tudassa velünk, ha a hiba továbbra is fennáll.|

## <a name="get-detect"></a>GET /Észlelés

### <a name="implementation-notes"></a>Végrehajtási megjegyzések
A szövegegy részének nyelvét azonosítja.

A kérelem `https://api.microsofttranslator.com/V2/Http.svc/Detect`URI.

**Visszatérési érték:** A szöveg kétkarakteres nyelvkódját tartalmazó karakterlánc.

### <a name="response-class-status-200"></a>Válaszosztály (200-as állapot)

sztring

Választartalom típusa: alkalmazás/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Appid|(üres)  |Kötelező. Ha `Authorization` a `Ocp-Apim-Subscription-Key` vagy a fejlécet használja, hagyja üresen a `appid` mezőt. Ellenkező esetben adjon meg `"Bearer" + " " + "access_token"`egy karakterláncot, amely tartalmazza a .|lekérdezés|sztring|
|szöveg|(üres)|Kötelező. Olyan karakterlánc, amely olyan szöveget tartalmaz, amelynek nyelvét azonosítani kell. A szöveg nem haladhatja meg a 10 000 karaktert.|lekérdezés|  sztring|
|Engedélyezés|(üres)|Kötelező, ha `appid` a `Ocp-Apim-Subscription-Key` mező és a fej is üresen marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Ocp-Apim-Subscription-Key  |(üres)    |Kötelező, ha `appid` a `Authorization` mező és a fej is üresen marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400|Rossz kérés. Ellenőrizze a bemeneti paramétereket és a részletes hibaválaszt.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Kérjük, adja meg & kérelem hozzávetőleges dátumát, valamint `X-MS-Trans-Info`a válaszfejlécben található kérésazonosítót.|
|503    |A szolgáltatás átmenetileg nem érhető el. Kérjük, próbálja meg újra, és tudassa velünk, ha a hiba továbbra is fennáll.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Végrehajtási megjegyzések

Karakterláncok tömbjében lévő nyelveket azonosítja. Függetlenül észleli az egyes tömbelemek nyelvét, és a tömb minden egyes sorához eredményt ad vissza.

A kérelem `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`URI.

A kérelem törzsének formátuma:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

A szöveg nem haladhatja meg a 10 000 karaktert.

**Visszatérési érték:** Karakterlánc-tömb, amely a bemeneti tömb minden egyes sorának kétkarakteres nyelvkódját tartalmazza.

Itt van a formátum a válasz törzs:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Válaszosztály (200-as állapot)
`DetectArray`sikeres volt. Egy karakterlánctömböt ad vissza, amely a bemeneti tömb minden egyes sorához kétkarakteres nyelvkódot tartalmaz.

sztring

Választartalom típusa: alkalmazás/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Appid|(üres)|Kötelező. Ha `Authorization` a `Ocp-Apim-Subscription-Key` vagy a fejlécet használja, hagyja üresen a `appid` mezőt. Ellenkező esetben adjon meg `"Bearer" + " " + "access_token"`egy karakterláncot, amely tartalmazza a .|lekérdezés|sztring|
|Engedélyezés|(üres)|Kötelező, ha `appid` a `Ocp-Apim-Subscription-Key` mező és a fej is üresen marad.  Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Ocp-Apim-Subscription-Key|(üres)|Kötelező, ha `appid` a `Authorization` mező és a fej is üresen marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Rossz kérés. Ellenőrizze a bemeneti paramétereket és a részletes hibaválaszt.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Kérjük, adja meg & kérelem hozzávetőleges dátumát, valamint `X-MS-Trans-Info`a válaszfejlécben található kérésazonosítót.|
|503    |A szolgáltatás átmenetileg nem érhető el. Kérjük, próbálja meg újra, és tudassa velünk, ha a hiba továbbra is fennáll.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Végrehajtási megjegyzések

> [!IMPORTANT]
> **Eprekation megjegyzés:** 2018. január 31-e után ez a módszer nem fogadja el az új mondatokat. Hibaüzenet jelenik meg. Kérjük, olvassa el az együttműködésen alapuló fordítási keretrendszer (CTF) változásairól szóló közleményt.

Fordítást ad a fordítási memóriához.

A kérelem `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`URI.

### <a name="response-class-status-200"></a>Válaszosztály (200-as állapot)

sztring

Választartalom típusa: alkalmazás: xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus   |
|:--|:--|:--|:--|:--|
|Appid|(üres)|Kötelező. Ha `Authorization` a `Ocp-Apim-Subscription-Key` vagy a fejlécet használja, hagyja üresen a `appid` mezőt. Ellenkező esetben adjon meg `"Bearer" + " " + "access_token"`egy karakterláncot, amely tartalmazza a .|lekérdezés|sztring|
|eredeti Szöveg|(üres)|Kötelező. A fordítandó szöveget tartalmazó karakterlánc. A karakterlánc maximális hossza 1000 karakter.|lekérdezés|sztring|
|lefordított szöveg|(üres) |Kötelező. A célnyelvre lefordított szöveget tartalmazó karakterlánc. A karakterlánc maximális hossza 2000 karakter.|lekérdezés|sztring|
|honnan|(üres)   |Kötelező. A szöveg eredeti nyelvének nyelvkódját jelölő karakterlánc. Például en az angol és a de a német.|lekérdezés|sztring|
|erre:|(üres)|Kötelező. Olyan karakterlánc, amely annak a nyelvnek a nyelvkódját jelöli, amelybe a szöveget le szeretné fordítani.|lekérdezés|sztring|
|rating|(üres) |Választható. Egész szám, amely a karakterlánc minőségi besorolását jelöli. Az érték -10 és 10 között van. Az alapértelmezett érték 1.|lekérdezés|egész szám|
|contentType típusú|(üres)    |Választható. A lefordított szöveg formátuma. A támogatott formátumok `text/plain` `text/html`a és a. Minden HTML elemnek jól formázott, teljes elemeknek kell lennie.    |lekérdezés|sztring|
|category|(üres)|Választható. A fordítás kategóriáját (tartományát) tartalmazó karakterlánc. A mező alapértelmezett értéke: `general`.|lekérdezés|sztring|
|felhasználó!|(üres)|Kötelező. A küldés kezdeményezőjének nyomon követésére használt karakterlánc.|lekérdezés|sztring|
|Uri|(üres)|Választható. A fordítás tartalomhelyét tartalmazó karakterlánc.|lekérdezés|sztring|
|Engedélyezés|(üres)|Kötelező, ha `appid` a `Ocp-Apim-Subscription-Key` mező és a fej is üresen marad.  Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.  |header|sztring|
|Ocp-Apim-Subscription-Key|(üres)|Kötelező, ha `appid` a `Authorization` mező és a fej is üresen marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Rossz kérés. Ellenőrizze a bemeneti paramétereket és a részletes hibaválaszt.|
|401    |Érvénytelen hitelesítő adatok.|
|410|`AddTranslation`már nem támogatott.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Kérjük, adja meg & kérelem hozzávetőleges dátumát, valamint `X-MS-Trans-Info`a válaszfejlécben található kérésazonosítót.|
|503    |A szolgáltatás átmenetileg nem érhető el. Kérjük, próbálja meg újra, és tudassa velünk, ha a hiba továbbra is fennáll.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Végrehajtási megjegyzések

> [!IMPORTANT]
> **Eprekation megjegyzés:** 2018. január 31-e után ez a módszer nem fogadja el az új mondatokat. Hibaüzenet jelenik meg. Kérjük, olvassa el az együttműködésen alapuló fordítási keretrendszer (CTF) változásairól szóló közleményt.

Fordítások tömbjét adja hozzá a fordítási memóriához. Ez a módszer a `AddTranslation`tömbverziója.

A kérelem `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`URI.

A kérelem törzsének formátuma:

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

Ezek az `AddtranslationsRequest`elemek a következők:

* `AppId`: Kötelező. Ha `Authorization` a `Ocp-Apim-Subscription-Key` vagy a fejlécet használja, hagyja üresen a `AppId` mezőt. Ellenkező esetben adjon meg `"Bearer" + " " + "access_token"`egy karakterláncot, amely tartalmazza a .
* `From`: Kötelező. A forrásnyelv nyelvkódját tartalmazó karakterlánc. A `GetLanguagesForTranslate` metódus által visszaadott nyelvek egyikének kell lennie.
* `To`: Kötelező. A célnyelv nyelvkódját tartalmazó karakterlánc. A `GetLanguagesForTranslate` metódus által visszaadott nyelvek egyikének kell lennie.
* `Translations`: Kötelező. A fordítási memóriához hozzáadandó fordítások tömbje. Minden fordításnak `OriginalText` `TranslatedText`tartalmaznia `Rating`kell a , és a . Az egyes `OriginalText` és `TranslatedText` 1000 karakter maximális mérete. Az összes `OriginalText` elem `TranslatedText` és elem összege nem haladhatja meg a 10 000 karaktert. A tömbelemek maximális száma 100.
* `Options`: Kötelező. A lehetőségek, beleértve `Category` `ContentType`a `Uri`, `User`, , , és . A `User` használata kötelező. `Category`, `ContentType`, `Uri` és nem kötelező. A megadott elemeket betűrendben kell felsorolni.

### <a name="response-class-status-200"></a>Válaszosztály (200-as állapot)
`AddTranslationArray`módszer sikeres volt. 

2018. január 31-e után a mondatokat nem fogadják el. A szolgáltatás 410-es hibakóddal válaszol.

sztring

Választartalom típusa: alkalmazás/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Engedélyezés|(üres)|Kötelező, ha `appid` a `Ocp-Apim-Subscription-Key` mező és a fej is üresen marad.  Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Ocp-Apim-Subscription-Key|(üres)|Kötelező, ha `appid` a `Authorization` mező és a fej is üresen marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Rossz kérés. Ellenőrizze a bemeneti paramétereket és a részletes hibaválaszt.|
|401    |Érvénytelen hitelesítő adatok.|
|410    |`AddTranslation`már nem támogatott.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Kérjük, adja meg & kérelem hozzávetőleges dátumát, valamint `X-MS-Trans-Info`a válaszfejlécben található kérésazonosítót.|
|503|A szolgáltatás átmenetileg nem érhető el. Kérjük, próbálja meg újra, és tudassa velünk, ha a hiba továbbra is fennáll.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Végrehajtási megjegyzések
A szöveg egy szakaszát mondatokra bontja, és az egyes mondatok hosszát tartalmazó tömböt ad vissza.

A kérelem `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`URI.

**Visszatérési érték:** Egész számokból álló tömb, amely a mondatok hosszát jelöli. A tömb hossza a mondatok számát jelöli. Az értékek az egyes mondatok hosszát jelölik.

### <a name="response-class-status-200"></a>Válaszosztály (200-as állapot)
Egész számokból álló tömb, amely a mondatok hosszát jelöli. A tömb hossza a mondatok számát jelöli. Az értékek az egyes mondatok hosszát jelölik.

egész szám

Választartalom típusa: alkalmazás/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Appid|(üres)  |Kötelező. Ha `Authorization` a `Ocp-Apim-Subscription-Key` vagy a fejlécet használja, hagyja üresen a `appid` mezőt. Ellenkező esetben adjon meg `"Bearer" + " " + "access_token"`egy karakterláncot, amely tartalmazza a .|lekérdezés| sztring|
|szöveg|(üres)   |Kötelező. Mondatokra bontandó szöveget jelölő karakterlánc. A szöveg maximális mérete 10 000 karakter.|lekérdezés|sztring|
|language   |(üres)    |Kötelező. A bemeneti szöveg nyelvkódját jelölő karakterlánc.|lekérdezés|sztring|
|Engedélyezés|(üres)|Kötelező, ha `appid` a `Ocp-Apim-Subscription-Key` mező és a fej is üresen marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.   |header|sztring|
|Ocp-Apim-Subscription-Key|(üres)|Kötelező, ha `appid` a `Authorization` mező és a fej is üresen marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400|Rossz kérés. Ellenőrizze a bemeneti paramétereket és a részletes hibaválaszt.|
|401|Érvénytelen hitelesítő adatok.|
|500|Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Kérjük, adja meg & kérelem hozzávetőleges dátumát, valamint `X-MS-Trans-Info`a válaszfejlécben található kérésazonosítót.|
|503|A szolgáltatás átmenetileg nem érhető el. Kérjük, próbálja meg újra, és tudassa velünk, ha a hiba továbbra is fennáll.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Végrehajtási megjegyzések
Egy adott nyelvpár fordításainak tömbjét olvassa le az üzletből és az MT motorból. `GetTranslations`abban `Translate` különbözik, hogy az összes rendelkezésre álló fordítást visszaadja.

A kérelem `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`URI.

A kérelem törzse tartalmazza `TranslationOptions` a választható objektumot, amely nek ez a formátuma:

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

Az `TranslateOptions` objektum az alábbi lista értékeit tartalmazza. Ezek mind választható, és alapértelmezés szerint a leggyakoribb beállításokat. A megadott elemeket betűrendben kell felsorolni.

* `Category`: A fordítás kategóriáját (tartományát) tartalmazó karakterlánc. A mező alapértelmezett értéke: `general`.
* `ContentType`: Az egyetlen támogatott beállítás és `text/plain`az alapértelmezett beállítás a .
* `IncludeMultipleMTAlternatives`: Logikai jelző, amely meghatározza, hogy egynél több alternatívát kell-e visszaadni az MT motorból. Az érvényes `true` `false` értékek és (kis- és nagybetűk megkülönböztetése). Az alapértelmezett `false`érték a , amely csak egy alternatívát ad vissza. A zászló `true` beállítása lehetővé teszi mesterséges alternatívák létrehozását, amelyek teljes mértékben integrálódnak az együttműködésen alapuló fordítási keretrendszerbe (CTF). A funkció lehetővé teszi a CTF-ben fordítást nem tartalmazó mondatok alternatíváinak visszaadását azáltal, hogy mesterséges alternatívákat ad hozzá a dekóder *n*-legjobb listájából.
    - Értékelés. A minősítések a következőkre vonatkoznak: 
         - A legjobb automatikus fordítás értékelése 5.
       - A CTF alternatívái a bíráló hatóságát tükrözik. -10 és +10 között mozognak.
       - Az automatikusan generált *(n*-legjobb) fordítási alternatívák értékelése 0 és 100 egyezési foka.
    - Alternatívák száma. A visszaadott alternatívák száma lehet olyan magas, `maxTranslations`mint a megadott érték, de alacsonyabb is lehet.
    - Nyelvpárok. Ez a funkció egyik irányban sem érhető el az egyszerűsített kínai és a hagyományos kínai közötti fordításhoz. A Microsoft Translator által támogatott összes többi nyelvpár hoz érhető el.
* `State`: A felhasználó állapota a kérés és a válasz korrelációjának elősegítésére. A válaszban ugyanaz a tartalom jelenik meg.
* `Uri`: Szűrje az eredményeket ezzel az URI-val. Ha nincs beállítva érték, `all`az alapértelmezett érték .
* `User`: Szűrje az eredményeket a felhasználó szerint. Ha nincs beállítva érték, `all`az alapértelmezett érték .

A `Content-Type` kérésnek a legyen `text/xml`.

**Visszatérési érték:** Itt van a formátum a válasz:

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

Ez a `GetTranslationsResponse` válasz olyan elemet tartalmaz, amely a következő értékeket tartalmazza:

* `Translations`: A talált egyezések `TranslationMatch` tömbje, objektumokban tárolva (a következő szakaszban ismertetve). A fordítások tartalmazhatják az eredeti szöveg kisebb változatait (homályos egyezés). A fordítások sorrendje: 100%, a következő a fuzzy.
* `From`: Ha a metódus nem `From` ad meg nyelvet, ez az érték automatikus nyelvfelismerésből származik. Ellenkező esetben ez lesz `From` a megadott nyelv.
* `State`: A felhasználó állapota a kérés és a válasz korrelációjának elősegítésére. A `TranslateOptions` paraméterben megadott értéket tartalmazza.

Az `TranslationMatch` objektum a következő értékekből áll:

* `Error`: A hibakód, ha hiba történik egy adott bemeneti karakterlánc. Ellenkező esetben ez a mező üres.
* `MatchDegree`: Azt jelzi, hogy a bemeneti szöveg milyen szorosan illeszkedik a tárolóban található eredeti szöveghez. A rendszer megfelelteti a bemeneti mondatokat az üzletnek, beleértve a pontatlan egyezéseket is. A visszaadott érték 0 és 100 között van, ahol a 0 nem hasonlóság, a 100 pedig pontos, kis- és nagybetűket megkülönböztető egyezés.
* `MatchedOriginalText`: Az eredményhez igazított eredeti szöveg. Ez az érték csak akkor jelenik meg, ha az egyező eredeti szöveg eltért a bemeneti szövegtől. Arra használják, hogy egy fuzzy egyezés forrásszövegét adják vissza. Ez az érték nem adja vissza a Microsoft Translator eredményeit.
* `Rating`: A minőségi döntést hozó személy jogosultságát jelzi. A gépi fordítás eredményei nek 5-ös minősítése van. Névtelenül biztosított fordítások általában egy értékelés 1-től 4-ig. Mérvadómódon biztosított fordítások általában a minősítés 6-10.
* `Count`: Hányszor lett kiválasztva ezzel a fordítással. Az automatikusan lefordított válasz értéke 0.
* `TranslatedText`: A lefordított szöveg.

### <a name="response-class-status-200"></a>Válaszosztály (200-as állapot)
Egy `GetTranslationsResponse` korábban leírt formátumú objektum.

sztring

Választartalom típusa: alkalmazás/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Appid|(üres)|Kötelező. Ha `Authorization` a `Ocp-Apim-Subscription-Key` vagy a fejlécet használja, hagyja üresen a `appid` mezőt. Ellenkező esetben adjon meg `"Bearer" + " " + "access_token"`egy karakterláncot, amely tartalmazza a .|lekérdezés|sztring|
|szöveg|(üres)|Kötelező. A fordítandó szöveget jelölő karakterlánc. A szöveg maximális mérete 10 000 karakter.|lekérdezés|sztring|
|honnan|(üres)|Kötelező. A lefordított szöveg nyelvkódját jelölő karakterlánc.|lekérdezés|sztring|
|erre: |(üres)    |Kötelező. Olyan karakterlánc, amely annak a nyelvnek a nyelvkódját jelöli, amelybe a szöveget le szeretné fordítani.|lekérdezés|sztring|
|maxFordítások|(üres)|Kötelező. Egész szám, amely a visszaadandó fordítások maximális számát jelöli.|lekérdezés|egész szám|
|Engedélyezés| (üres)|Kötelező, ha `appid` a `Ocp-Apim-Subscription-Key` mező és a fej is üresen marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|sztring|  header|
|Ocp-Apim-Subscription-Key|(üres)  |Kötelező, ha `appid` a `Authorization` mező és a fej is üresen marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Rossz kérés. Ellenőrizze a bemeneti paramétereket és a részletes hibaválaszt.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Kérjük, adja meg & kérelem hozzávetőleges dátumát, valamint `X-MS-Trans-Info`a válaszfejlécben található kérésazonosítót.|
|503|A szolgáltatás átmenetileg nem érhető el. Kérjük, próbálja meg újra, és tudassa velünk, ha a hiba továbbra is fennáll.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Végrehajtási megjegyzések
Több fordítási jelöltet kér le több forrásszöveghez.

A kérelem `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`URI.

A kérelem törzsének formátuma:

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

`GetTranslationsArrayRequest`tartalmazza ezeket az elemeket:

* `AppId`: Kötelező. Ha `Authorization` a fejlécet használja, hagyja üresen a `AppId` mezőt. Ellenkező esetben adjon meg `"Bearer" + " " + "access_token"`egy karakterláncot, amely tartalmazza a .
* `From`: Kötelező. A lefordított szöveg nyelvkódját jelölő karakterlánc.
* `MaxTranslations`: Kötelező. Egész szám, amely a visszaadandó fordítások maximális számát jelöli.
* `Options`: Nem kötelező. A `Options` következő értékeket tartalmazó objektum. Ezek mind választható, és alapértelmezés szerint a leggyakoribb beállításokat. A megadott elemeket betűrendben kell felsorolni.
    - `Category`: A fordítás kategóriáját (tartományát) tartalmazó karakterlánc. A mező alapértelmezett értéke: `general`.
    - `ContentType`: Az egyetlen támogatott beállítás és `text/plain`az alapértelmezett beállítás a .
    - `IncludeMultipleMTAlternatives`: Logikai jelző, amely meghatározza, hogy egynél több alternatívát kell-e visszaadni az MT motorból. Az érvényes `true` `false` értékek és (kis- és nagybetűk megkülönböztetése). Az alapértelmezett `false`érték a , amely csak egy alternatívát ad vissza. Beállítása a `true` zászló, hogy lehetővé teszi a mesterséges alternatívák fordítás, teljes mértékben integrálva az együttműködésen alapuló fordítások keret (CTF). A funkció lehetővé teszi a ctf-ben nincs alternatíva alternatíváinak visszaadását azáltal, hogy mesterséges alternatívákat ad hozzá a dekóder *n*-legjobb listájából.
        - Minősítések A minősítések a következőkre vonatkoznak:
          - A legjobb automatikus fordítás értékelése 5.
          - A CTF alternatívái a bíráló hatóságát tükrözik. -10 és +10 között mozognak.
          - Az automatikusan generált *(n*-legjobb) fordítási alternatívák értékelése 0 és 100 egyezési foka.
        - Alternatívák száma. A visszaadott alternatívák száma lehet olyan magas, `maxTranslations`mint a megadott érték, de alacsonyabb is lehet.
        - Nyelvpárok. Ez a funkció egyik irányban sem érhető el az egyszerűsített kínai és a hagyományos kínai közötti fordításhoz. A Microsoft Translator által támogatott összes többi nyelvpár hoz érhető el.
* `State`: A felhasználó állapota a kérés és a válasz korrelációjának elősegítésére. A válaszban ugyanaz a tartalom jelenik meg.
* `Uri`: Szűrje az eredményeket ezzel az URI-val. Ha nincs beállítva érték, `all`az alapértelmezett érték .
* `User`: Szűrje az eredményeket a felhasználó szerint. Ha nincs beállítva érték, `all`az alapértelmezett érték .
* `Texts`: Kötelező. A fordítás szövegét tartalmazó tömb. Minden karakterláncnak ugyanazon a nyelven kell lennie. A fordítandó szöveg összege nem haladhatja meg a 10 000 karaktert. A tömbelemek maximális száma 10.
* `To`: Kötelező. Olyan karakterlánc, amely annak a nyelvnek a nyelvkódját jelöli, amelybe a szöveget le szeretné fordítani.

A választható elemeket kihagyhatja. Azokat az `GetTranslationsArrayRequest` elemeket, amelyek közvetlen gyermekei, betűrendben kell felsorolni.

A `Content-Type` kérésnek a legyen `text/xml`.

**Visszatérési érték:** Itt van a formátum a válasz:

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

Minden `GetTranslationsResponse` elem a következő értékeket tartalmazza:

* `Translations`: A talált egyezések `TranslationMatch` tömbje, objektumokban tárolva (a következő szakaszban ismertetve). A fordítások tartalmazhatják az eredeti szöveg kisebb változatait (homályos egyezés). A fordítások sorrendje: 100%, a következő a fuzzy.
* `From`: Ha a metódus nem `From` ad meg nyelvet, ez az érték automatikus nyelvfelismerésből származik. Ellenkező esetben ez lesz `From` a megadott nyelv.
* `State`: A felhasználó állapota a kérés és a válasz korrelációjának elősegítésére. A `TranslateOptions` paraméterben megadott értéket tartalmazza.

Az `TranslationMatch` objektum a következő értékeket tartalmazza:
* `Error`: A hibakód, ha hiba történik egy adott bemeneti karakterlánc. Ellenkező esetben ez a mező üres.
* `MatchDegree`: Azt jelzi, hogy a bemeneti szöveg milyen szorosan illeszkedik a tárolóban található eredeti szöveghez. A rendszer megfelelteti a bemeneti mondatokat az üzletnek, beleértve a pontatlan egyezéseket is. A visszaadott érték 0 és 100 között van, ahol a 0 nem hasonlóság, a 100 pedig pontos, kis- és nagybetűket megkülönböztető egyezés.
* `MatchedOriginalText`: Az eredményhez igazított eredeti szöveg. Ez az érték csak akkor jelenik meg, ha az egyező eredeti szöveg eltért a bemeneti szövegtől. Arra használják, hogy egy fuzzy egyezés forrásszövegét adják vissza. Ez az érték nem adja vissza a Microsoft Translator eredményeit.
* `Rating`: A minőségi döntést hozó személy jogosultságát jelzi. A gépi fordítás eredményei nek 5-ös minősítése van. Névtelenül biztosított fordítások általában egy értékelés 1-től 4-ig. Mérvadóan biztosított fordítások általában a minősítés 6-10.
* `Count`: Hányszor lett kiválasztva ezzel a fordítással. Az automatikusan lefordított válasz értéke 0.
* `TranslatedText`: A lefordított szöveg.


### <a name="response-class-status-200"></a>Válaszosztály (200-as állapot)

sztring

Választartalom típusa: alkalmazás/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Engedélyezés  |(üres)    |Kötelező, ha `appid` a `Ocp-Apim-Subscription-Key` mező és a fej is üresen marad.  Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Ocp-Apim-Subscription-Key|(üres)  |Kötelező, ha `appid` a `Authorization` mező és a fej is üresen marad.|header|sztring|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Rossz kérés. Ellenőrizze a bemeneti paramétereket és a részletes hibaválaszt.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, tudassa velünk. Kérjük, adja meg & kérelem hozzávetőleges dátumát, valamint `X-MS-Trans-Info`a válaszfejlécben található kérésazonosítót.|
|503    |A szolgáltatás átmenetileg nem érhető el. Kérjük, próbálja meg újra, és tudassa velünk, ha a hiba továbbra is fennáll.|

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Áttelepítés a fordítószöveg API-ra a 3-as v3-as](../migrate-to-v3.md)


