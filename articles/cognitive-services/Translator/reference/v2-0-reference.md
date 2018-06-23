---
title: A Microsoft Translator szöveg API V2.0 referencia |} Microsoft Docs
titleSuffix: Cognitive Services
description: A V2.0 Microsoft Translator szöveg API referenciadokumentációt tartalmaz.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/15/2018
ms.author: v-jansko
ms.openlocfilehash: e32e28608d2fecf27b61acff74af7eb6849f0ba1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349383"
---
# <a name="translator-text-api-v20"></a>A fordító szöveg API 2.0-s verzió

> [!IMPORTANT]
> Ez a fordító szöveg API verziója elavult. [A v3 a fordító szöveg API dokumentációjában talál](v3-0-reference.md).

A Microsoft Translator V2 szöveg API zökkenőmentesen integrálható az alkalmazások, webhelyek, eszközök, vagy egyéb megoldások többnyelvű felhasználói élmény biztosításához. Az ipari szabványok kihasználva, használat hardver platformok, hogy az operációs rendszer minden nyelvi fordítási és más nyelv kapcsolatos műveletek, például szöveg nyelvi észlelési vagy szöveg-beszéd átalakítás végrehajtásához. A Microsoft Translator API kapcsolatos információkért kattintson ide.

## <a name="getting-started"></a>Első lépések
Szüksége lesz a Microsoft Translator szöveg API eléréséhez [Regisztráljon a Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authorization"></a>Engedélyezés
Az összes Microsoft Translator szöveg API van szükség egy előfizetés kulcs hitelesítéséhez. Az API-t a hitelesítési két módot támogat:

* Access token használatával. A hivatkozott előfizetés kulcs **lépés** 9 egy POST kérést, így a hitelesítési szolgáltatás egy hozzáférési jogkivonat létrehozásához. A jogkivonat-szolgáltatás dokumentációjában talál. A hozzáférési jogkivonat átadása a fordító szolgáltatást az engedélyezési fejléc vagy a access_token lekérdezési paraméter. A hozzáférési jogkivonat érvénytelen 10 percig. Új hozzáférési jogkivonat beszerzése 10 percenként, és tovább használni a ugyanazokkal a hozzáférési jogkivonat ismételt kérelmek ezen 10 percen belül.

* Egy kulccsal előfizetés közvetlenül. Az Előfizetés kulcs átadni értékként `Ocp-Apim-Subscription-Key` a kérelemmel együtt a fordító API fejléc. Ebben a módban nincs hívni a hitelesítési jogkivonat-szolgáltatás egy hozzáférési jogkivonat létrehozásához.

Az Előfizetés kulcs és a hozzáférési jogkivonat tekinti titkokat nézetből el kell rejteni.

## <a name="profanity-handling"></a>Profanitás kezelése
Általában a fordító szolgáltatás megőrzi az, hogy megtalálható-e a fordítás a forrás Profanitás. Profanitás mennyire és a környezetben, amelynek eredményeképpen a szavakat profán eltérő kulturális környezetek, és ennek eredményeképpen a célként megadott nyelven Profanitás fokának előfordulhat, hogy bővíteni vagy csökkenteni.

Ha el szeretné kerülni, a fordítás, függetlenül a forrás szöveg Profanitás jelenléte Profanitás első használhatja a Profanitás szűrése a módszereket, amelyek támogatják ezt a beállítást. A beállítás lehetővé teszi annak meghatározását, hogy meg szeretné tekinteni a Profanitás törölték, vagy a megfelelő címkéket jelölésű, vagy a hajt végre semmilyen műveletet. A lehetséges értékek a `ProfanityAction` vannak `NoAction` (alapértelmezett), Marked és `Deleted`.


|ProfanityAction    |Műveletek |Példa forrás (japán)  |Példa fordítási (angol nyelven)  |
|:--|:--|:--|:--|
|NoAction   |Default (Alapértelmezett): Ugyanaz, mint a beállítás nem található. Profanitás cél forrásból fogja továbbítani.        |彼はジャッカスです。     |Egy Pápaszemes.   |
|Megjelölve     |Profán szavak XML-címkék kell lennie.%n <profanity> és </profanity>.     |彼はジャッカスです。 |Van egy <profanity>Pápaszemes</profanity>.    |
|Törölve    |Profán szavak eltávolítja a kimeneti helyettesítő nélkül.     |彼はジャッカスです。 |Van egy.   |

    
## <a name="excluding-content-from-translation"></a>Tartalom kizárása a fordítás
Ha például HTML címkékkel tartalom fordításával (`contentType=text/html`), egyes esetekben célszerű szeretné kizárni az adott tartalmat a fordítás. Az attribútum segítségével `class=notranslate` adhatja meg a tartalom maradjon az eredeti nyelven. A következő példában az első belüli tartalom `div` elem nem lesz fordítva, miközben a tartalmat, a második `div` elem fordítja.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET / fordítása

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Az eszköz egy szöveges karakterlánc egyik nyelvről a másikra.

A kérelem URI-azonosítója `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Visszatérési érték:** egy karakterlánc, amely a fordítást.

Ha korábban már használt `AddTranslation` vagy `AddTranslationArray` 5-ös vagy újabb egy forrás mondatban egy minősítés fordítását megadását `Translate` csak a legfontosabb döntés érhető el a rendszer adja vissza. A "egy forrás mondatban" azt jelenti, hogy a pontos azonos (a 100 %-os egyeztetéséhez), kivéve a kis-és nagybetűk, illetve szóköz, előfizetéscímkék értékeit és írásjelek mondat végén. Ha a nem minősített tárolt minősítést 5 vagy újabb a visszaadott eredmény szerint a Microsoft Translator automatikus fordítás lesz.

### <a name="response-class-status-200"></a>Válasz osztály (állapot 200)

sztring

Válasz tartalomtípusa: application/xml 

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás    |A paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító  |(üres)    |Kötelező. Az engedélyezési vagy Ocp-Apim-előfizetés-kulcs fejléc használata esetén hagyja üresen a appid mezőt más tartalmaznak egy karakterlánc, amely tartalmazza a "Tulajdonos" + "" + "access_token".|lekérdezés|sztring|
|szöveg|(üres)   |Kötelező. A szöveg lefordítani képviselő karakterláncot. A szöveg mérete legfeljebb 10000 karakterből állhat.|lekérdezés|sztring|
|forrás:|(üres)   |Választható. Egy karakterlánc, amely a fordítás szöveg nyelvi kódját. Ha például az angol nyelvű tájékoztatáshoz en.|lekérdezés|sztring|
|erre:|(üres) |Kötelező. Egy karakterlánc, amely a szöveg lefordítani a nyelvi kódot.|lekérdezés|sztring|
|contentType|(üres)    |Választható. A fordítás alatt álló szöveg formátumban. A támogatott formátumok a következők: egyszerű szöveg (alapértelmezett) és a text/html. HTML-kell lennie egy megfelelően formázott, a teljes elemet.|lekérdezés|sztring|
|category|(üres)   |Választható. A kategória (tartomány) a fordítás tartalmazó karakterlánc. Az alapértelmezett érték "általános".|lekérdezés|sztring|
|Engedélyezés|(üres)  |Szükséges, ha az appid mező vagy a fejléc Ocp-Apim-előfizetés-kulcs nincs megadva. Engedélyezési jogkivonat: "Tulajdonos" + "" + "access_token".|header|sztring|
|Az OCP-Apim-előfizetés-kulcs|(üres)  |Szükséges, ha az appid mező vagy engedélyezési fejléc nincs megadva.|header|sztring|


### <a name="response-messages"></a>A válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek és a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a probléma továbbra is fennáll, ossza meg velünk. Adja meg a hozzávetőleges dátum és idő, a kérelem és a következő a válaszfejlécet X-MS-tranzakció-adatait tartalmazza.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="post-translatearray"></a>/TranslateArray utáni

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Használja a `TranslateArray` metódus több forrás szövegek fordításainak beolvasása.

A kérelem URI-azonosítója `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

A kérelem törzsében formátuma a következő legyen:

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

Elemek belül a `TranslateArrayRequest` vannak:


* `appid`: Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más tartalmazza a karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.
* `from`: Nem kötelező. Egy karakterlánc, amely a szöveg lefordítani a nyelvi kódot. Ha üres a válasz nyelvi automatikus észlelés eredményét tartalmazza.
* `options`: Nem kötelező. Egy `Options` objektumot, amely az alábbi értékeket tartalmazza. Azok az összes megadása nem kötelező, és a leggyakrabban használt beállításait az alapértelmezett. Megadott elemei ábécésorrendben szerepelnie kell.
    - `Category`: A kategória (tartomány) a fordítás tartalmazó karakterlánc. Az alapértelmezett `general`.
    - `ContentType`: A a szöveg alatt lefordítani formátumát. A támogatott formátumok a következők `text/plain` (alapértelmezett), `text/xml` és `text/html`. HTML-kell lennie egy megfelelően formázott, a teljes elemet.
    - `ProfanityAction`: Adja meg, hogyan profanities kezelik a fentiekben leírtak szerint. Az értékek elfogadható `ProfanityAction` vannak `NoAction` (alapértelmezett), `Marked` és `Deleted`.
    - `State`: A felhasználói állapot összefüggésbe kérés és válasz. A válaszban visszaadott ugyanaz a tartalma.
    - `Uri`: Ezt az URI eredményeket szűrheti. Alapértelmezett: `all`.
    - `User`: A felhasználó által eredmény szűrése. Alapértelmezett: `all`.
* `texts`: Kötelező. A fordítás szövegeinek tartalmazó tömb. Minden karakterlánc ugyanazon nyelvet kell lennie. Az összes fordítandó szövegek legfeljebb 10000 karakterből állhat. A tömb elemeinek maximális száma: 2000.
* `to`: Kötelező. Egy karakterlánc, amely a szöveg lefordítani a nyelvi kódot.

Választható elemek elhagyható. Elemek, amelyek közvetlen gyermekei TranslateArrayRequest betűrendben szerepelnie kell.

TranslateArray metódus fogad `application/xml` vagy `text/xml` a `Content-Type`.

**Visszatérési érték:** A `TranslateArrayResponse` tömb. Minden egyes `TranslateArrayResponse` a következő elemeket tartalmazza:

* `Error`: Ha egy történt, a hibát jelző. Nem lehet null értékű.
* `OriginalSentenceLengths`: Minden mondatba az eredeti forrás szöveg hosszát jelző egész számok egy tömbjét. A tömb hosszának mondat számát jelzi.
* `TranslatedText`: A fordítást.
* `TranslatedSentenceLengths`: Minden mondat a lefordított szöveg hosszát jelző egész számok egy tömbjét. A tömb hosszának mondat számát jelzi.
* `State`: A felhasználói állapot összefüggésbe kérés és válasz. Ugyanazt a tartalmat, mint a kérelem adja vissza.

Az adott válasz törzsének formátuma a következő.

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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

### <a name="response-class-status-200"></a>Válasz osztály (állapot 200)
A sikeres válasz tartalmazza a tömbjét `TranslateArrayResponse` formátumban a fent leírt.

sztring

Válasz tartalomtípusa: application/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|A paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Engedélyezés|(üres)) |Szükséges, ha az appid mező vagy a fejléc Ocp-Apim-előfizetés-kulcs nincs megadva. Engedélyezési jogkivonat: "Tulajdonos" + "" + "access_token".|header|sztring|
|Az OCP-Apim-előfizetés-kulcs|(üres)|Szükséges, ha az appid mező vagy engedélyezési fejléc nincs megadva.|header|sztring|

### <a name="response-messages"></a>A válaszüzenetek

|HTTP-állapotkód   |Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek és a részletes hibaüzenetet. Gyakori hibák a következők: <ul><li>A tömb elem nem lehet üres</li><li>Érvénytelen kategórianév</li><li>A nyelvi érvénytelen</li><li>A nyelvi érvénytelen</li><li>A kérelem túl sok elemet tartalmaz.</li><li>A From nyelv nem támogatott.</li><li>A címzett nyelv nem támogatott</li><li>Fordítása kérelem túl sok adatot tartalmaz</li><li>HTML nem a megfelelő formátumban van.</li><li>Túl sok karakterlánc fordítása Request lett átadva.</li></ul>|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a probléma továbbra is fennáll, ossza meg velünk. Adja meg a hozzávetőleges dátum és idő, a kérelem és a következő a válaszfejlécet X-MS-tranzakció-adatait tartalmazza.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
A paraméterként átadott lekéri a nyelvek rövid nevének `languageCodes`, és honosítás a átadott területibeállítás-nyelv használatával.

A kérelem URI-azonosítója `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

A kérelem törzsében magában foglalja a kódokat 639-1-nyelv rövid nevének beolvasása jelölő karakterlánc-tömbben. Példa:

```
<ArrayOfstring xmlns:i="http://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Visszatérési érték:** a fordító a kért nyelv honosítva-szolgáltatás által támogatott nyelvek nevét tartalmazó karakterlánc-tömbben.

### <a name="response-class-status-200"></a>Válasz osztály (állapot 200)
A kért nyelv honosítva fordító szolgáltatás által támogatott nyelvek nevét tartalmazó karakterlánc-tömbben.

sztring

Válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|A paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más tartalmazza a karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|Területi beállítás|(üres) |Kötelező. Egy karakterlánc, amely egy ISO 639 kétbetűs kis kulturális környezetet a nyelvet és a nyelv nevének localize egy ISO 3166 kétbetűs nagybetűs szubkultúrákhoz kód társított vagy a egy ISO 639 kis kulturális környezet kombinációja önmagában.|lekérdezés|sztring|
|Engedélyezés|(üres)  |Kötelező, ha az appid mező vagy `Ocp-Apim-Subscription-Key` fejléc nem lett megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Az OCP-Apim-előfizetés-kulcs|(üres)  |Kötelező, ha az appid mező vagy `Authorization` fejléc nem lett megadva.|header|sztring|

### <a name="response-messages"></a>A válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek és a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a probléma továbbra is fennáll, ossza meg velünk. Adja meg a hozzávetőleges dátum és idő, a kérelem és a következő a válaszfejlécet X-MS-tranzakció-adatait tartalmazza.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-getlanguagesfortranslate"></a>/GetLanguagesForTranslate beolvasása

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
A fordítás szolgáltatás által támogatott nyelvek képviselő nyelvkódjait listájának beszerzése.  `Translate` és `TranslateArray` lefordíthatja bármely két nyelvek között.

A kérelem URI-azonosítója `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Visszatérési érték:** a fordító Services által támogatott nyelvkódok tartalmazó karakterlánc-tömbben.

### <a name="response-class-status-200"></a>Válasz osztály (állapot 200)
A fordító Services által támogatott nyelvkódok tartalmazó karakterlánc-tömbben.

sztring

Válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|A paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más tartalmazza a karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|Engedélyezés|(üres)  |Kötelező, ha a `appid` mező vagy `Ocp-Apim-Subscription-Key` fejléc nem lett megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Az OCP-Apim-előfizetés-kulcs|(üres)|Kötelező, ha a `appid` mező vagy `Authorization` fejléc nem lett megadva.|header|sztring|

### <a name="response-messages"></a>A válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek és a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a probléma továbbra is fennáll, ossza meg velünk. Adja meg a hozzávetőleges dátum és idő, a kérelem és a következő a válaszfejlécet X-MS-tranzakció-adatait tartalmazza.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-getlanguagesforspeak"></a>/GetLanguagesForSpeak beolvasása

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
A beszédszintézishez elérhető nyelvek beolvasása.

A kérelem URI-azonosítója `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Visszatérési érték:** beszéd összefoglaló a fordító szolgáltatás által támogatott nyelvkódok tartalmazó karakterlánc-tömbben.

### <a name="response-class-status-200"></a>Válasz osztály (állapot 200)
A beszédfelismerés összefoglaló a fordító szolgáltatás által támogatott nyelvkódok tartalmazó karakterlánc-tömbben.

sztring

Válasz tartalomtípusa: application/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|A paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más tartalmazza a karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|Engedélyezés|(üres)|Kötelező, ha a `appid` mező vagy `Ocp-Apim-Subscription-Key` fejléc nem lett megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Az OCP-Apim-előfizetés-kulcs|(üres)|Kötelező, ha a `appid` mező vagy `Authorization` fejléc nem lett megadva.|header|sztring|
 
### <a name="response-messages"></a>A válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400|Hibás kérés. Ellenőrizze a bemeneti paraméterek és a részletes hibaüzenetet.|
|401|Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a probléma továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérelem és a válaszfejlécet szereplő Kérelemazonosító `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-speak"></a>GET / beszéd

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
A megfelelő nyelvű felolvasását átadott szöveg wave vagy mp3 adatfolyam adja vissza.

A kérelem URI-azonosítója `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Visszatérési érték:** wave vagy mp3 adatfolyam felolvasását a megfelelő nyelvű átadott a szöveg.

### <a name="response-class-status-200"></a>Válasz osztály (állapot 200)

Bináris

Válasz tartalomtípusa: application/xml 

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|A paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más tartalmazza a karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|szöveg|(üres)   |Kötelező. A mondatok vagy a hanghullám-adatfolyamok elhangzani megadott nyelv mondatokat tartalmazó karakterlánc. Beszéd szöveg mérete nem lehet hosszabb 2000 karakternél.|lekérdezés|sztring|
|nyelv|(üres)   |Kötelező. Egy karakterlánc, amely képes legyen alkalmazni a szöveget a támogatott nyelvi kódot. A kód szerepelnie kell a metódus által visszaadott kódok listája `GetLanguagesForSpeak`.|lekérdezés|sztring|
|Formátumban|(üres)|Választható. Egy olyan karakterláncot a tartalomtípus-azonosítóját. Jelenleg `audio/wav` és `audio/mp3` érhetők el. Az alapértelmezett érték `audio/wav`.|lekérdezés|sztring|
|beállítások|(üres)    |<ul><li>Választható. A karakterlánc a szintetizált tulajdonságainak megadása:<li>`MaxQuality` és `MinSize` érhetők el a hang jelek minőségének megadásához. A `MaxQuality`, kaphat a legmagasabb minőségét, valamint a hangok `MinSize`, a legkisebb méretű hangok kaphat. Alapértelmezett érték a `MinSize`.</li><li>`female` és `male` érhetők el a kívánt nemét, a hang megadásához. Az alapértelmezett szint a `female`. Használja a függőleges vonal "|` to include multiple options. For example  `MaxQuality|Hímivarú ".</li></li></ul> |lekérdezés|sztring|
|Engedélyezés|(üres)|Kötelező, ha a `appid` mező vagy `Ocp-Apim-Subscription-Key` fejléc nem lett megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Az OCP-Apim-előfizetés-kulcs|(üres)  |Kötelező, ha a `appid` mező vagy `Authorization` fejléc nem lett megadva.|header|sztring|

### <a name="response-messages"></a>A válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek és a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a probléma továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérelem és a válaszfejlécet szereplő Kérelemazonosító `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-detect"></a>GET / észlelése

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Használja a `Detect` metódus egy kijelölt szöveg adat nyelv azonosítására.

A kérelem URI-azonosítója `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Visszatérési érték:** a megadott szöveg két karakter nyelvi kódját tartalmazó karakterláncot. .

### <a name="response-class-status-200"></a>Válasz osztály (állapot 200)

sztring

Válasz tartalomtípusa: application/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|A paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)  |Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más tartalmazza a karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|szöveg|(üres)|Kötelező. Néhány szöveg, amelynek nyelv azonosítsa a tartalmazó karakterlánc. A szöveg mérete legfeljebb 10000 karakterből állhat.|lekérdezés| sztring|
|Engedélyezés|(üres)|Kötelező, ha a `appid` mező vagy `Ocp-Apim-Subscription-Key` fejléc nem lett megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Az OCP-Apim-előfizetés-kulcs  |(üres)    |Kötelező, ha a `appid` mező vagy `Authorization` fejléc nem lett megadva.|header|sztring|

### <a name="response-messages"></a>A válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400|Hibás kérés. Ellenőrizze a bemeneti paraméterek és a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a probléma továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérelem és a válaszfejlécet szereplő Kérelemazonosító `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|


## <a name="post-detectarray"></a>/DetectArray utáni

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Használja a `DetectArray` módszer karakterlánc tömbje nyelvének egyszerre azonosításához. Minden egyes tömbelem független észlelési hajt végre, és a tömb minden egyes sorára eredményt adja vissza.

A kérelem URI-azonosítója `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

A kérelem törzsében formátuma a következő legyen.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

A szöveg mérete legfeljebb 10000 karakterből állhat.

**Visszatérési érték:** a bemeneti tömb minden egyes sorára kódok két karakter nyelvet tartalmazó karakterlánc-tömbben.

Az adott válasz törzsének formátuma a következő.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Válasz osztály (állapot 200)
DetectArray sikeres volt. Adja vissza a két karakter nyelvkódjait a bemeneti tömb minden egyes sorára tartalmazó karakterlánc-tömbben.

sztring

Válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|A paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más tartalmazza a karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|Engedélyezés|(üres)|Kötelező, ha a `appid` mező vagy `Ocp-Apim-Subscription-Key` fejléc nem lett megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Az OCP-Apim-előfizetés-kulcs|(üres)|Kötelező, ha a `appid` nincs megadva mező vagy Authorization fejlécet.|header|sztring|

### <a name="response-messages"></a>A válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek és a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a probléma továbbra is fennáll, ossza meg velünk. Adja meg a hozzávetőleges dátum és idő, a kérelem és a következő a válaszfejlécet X-MS-tranzakció-adatait tartalmazza.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-addtranslation"></a>/AddTranslation beolvasása

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések

> [!IMPORTANT]
> **ÉRVÉNYTELENÍTÉSE Megjegyzés:** 2018. január 31. után ez a módszer nem fogad el új mondat jelentések, és egy hibaüzenetet fog kapni. Tekintse meg ezt az értesítést a közös fordítási funkciók változásokról.

A fordítás memória ad hozzá a fordítást.

A kérelem URI-azonosítója `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Válasz osztály (állapot 200)

sztring

Válasz tartalomtípusa: alkalmazás: xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|A paraméter típusa|Adattípus   |
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más tartalmazza a karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|OriginalText|(üres)|Kötelező. A lefordítani szöveget tartalmazó karakterlánc. A karakterlánc legfeljebb 1000 karakterből rendelkezik.|lekérdezés|sztring|
|TranslatedText|(üres) |Kötelező. Karakterlánc, amely tartalmazza a lefordított cél nyelven. A karakterlánc hossza maximális 2000 karakternél.|lekérdezés|sztring|
|forrás:|(üres)   |Kötelező. Egy karakterlánc, amely a fordítás szöveg nyelvi kódját. en = angol, de német stb =...|lekérdezés|sztring|
|erre:|(üres)|Kötelező. Egy karakterlánc, amely a szöveg lefordítani a nyelvi kódot.|lekérdezés|sztring|
|rating|(üres) |Választható. Ez a karakterlánc a minőségi besorolását jelölő egész. -10 és 10 közötti értéket. Az alapértelmezett érték 1.|lekérdezés|egész szám|
|contentType|(üres)    |Választható. A fordítás alatt álló szöveg formátumban. A támogatott formátumok a következők: "text/plain" és "text/html". HTML-kell lennie egy megfelelően formázott, a teljes elemet.   |lekérdezés|sztring|
|category|(üres)|Választható. A kategória (tartomány) a fordítás tartalmazó karakterlánc. Az alapértelmezett érték "általános".|lekérdezés|sztring|
|Felhasználó|(üres)|Kötelező. Segítségével nyomon követhető a küldése a kezdeményezője karakterlánc.|lekérdezés|sztring|
|uri azonosító|(üres)|Választható. A tartalom helyéhez a fordítás tartalmazó karakterlánc.|lekérdezés|sztring|
|Engedélyezés|(üres)|Kötelező, ha az appid mező vagy `Ocp-Apim-Subscription-Key` fejléc nem lett megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.    |header|sztring|
|Az OCP-Apim-előfizetés-kulcs|(üres)|Kötelező, ha a `appid` mező vagy `Authorization` fejléc nem lett megadva.|header|sztring|

### <a name="response-messages"></a>A válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek és a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|410|AddTranslation már nem támogatott.|
|500    |Kiszolgálóhiba. Ha a probléma továbbra is fennáll, ossza meg velünk. Adja meg a hozzávetőleges dátum és idő, a kérelem és a következő a válaszfejlécet X-MS-tranzakció-adatait tartalmazza.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="post-addtranslationarray"></a>/AddTranslationArray utáni

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések

> [!IMPORTANT]
> **ÉRVÉNYTELENÍTÉSE Megjegyzés:** 2018. január 31. után ez a módszer nem fogad el új mondat jelentések, és egy hibaüzenetet fog kapni. Tekintse meg ezt az értesítést a közös fordítási funkciók változásokról.

Hozzáadja a fordítások fordítási memóriáját tömbjét. Egy tömb verziója `AddTranslation`.

A kérelem URI-azonosítója `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

A kérelem törzsében formátuma a következő.

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

A AddtranslationsRequest elemen belül elemei a következők:

* `AppId`: Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más tartalmazza a karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.
* `From`: Kötelező. Az adatforrás nyelvi nyelvi kódját tartalmazó karakterlánc. A nyelv által visszaadott egyikének kell lennie a `GetLanguagesForTranslate` metódust.
* `To`: Kötelező. A cél nyelv nyelvi kódot tartalmazó karakterlánc. A nyelv által visszaadott egyikének kell lennie a `GetLanguagesForTranslate` metódust.
* `Translations`: Kötelező. Fordítások fordítási memória hozzáadása tömbjét. Minden egyes fordítási kell tartalmaznia: originalText, translatedText és minősítése. Minden egyes originalText és translatedText mérete legfeljebb 1000 karakter. Az összes a originalText(s) és translatedText(s) legfeljebb 10000 karakterből állhat. A tömb elemeinek maximális száma: 100.
* `Options`: Kötelező. Olyan beállításokat, beleértve a kategóriát, ContentType, Uri és felhasználói készlete. Felhasználó megadása kötelező. Kategória, ContentType és Uri megadása nem kötelező. Megadott elemei ábécésorrendben szerepelnie kell.

### <a name="response-class-status-200"></a>Válasz osztály (állapot 200)
AddTranslationArray metódus sikeres volt. 2018. január 31. után mondat jelentések nem fogja elfogadni. A szolgáltatás válaszol, és 410 hibakód.

sztring

Válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|A paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Engedélyezés|(üres)|Szükséges, ha az appid mező vagy a fejléc Ocp-Apim-előfizetés-kulcs nincs megadva. Engedélyezési jogkivonat: "Tulajdonos" + "" + "access_token".|header|sztring|
|Az OCP-Apim-előfizetés-kulcs|(üres)|Szükséges, ha az appid mező vagy engedélyezési fejléc nincs megadva.|header|sztring|

### <a name="response-messages"></a>A válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek és a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|410    |AddTranslation már nem támogatott.|
|500    |Kiszolgálóhiba. Ha a probléma továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérelem és a válaszfejlécet szereplő Kérelemazonosító `X-MS-Trans-Info`.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-breaksentences"></a>/BreakSentences beolvasása

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Szöveg bontja a mondatok, és minden mondat hosszának tartalmazó tömböt ad vissza.

A kérelem URI-azonosítója `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Visszatérési érték:** a mondatok a hosszának jelző egész számok tömbjét. A tömb hossza mondatok számát, és az értékek a következők minden mondat hosszát.

### <a name="response-class-status-200"></a>Válasz osztály (állapot 200)
A mondatok a hosszának jelző egész számok tömbje. A tömb hossza mondatok számát, és az értékek a következők minden mondat hosszát.

egész szám

Válasz tartalomtípusa: application/xml 

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|A paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)  |Kötelező. Az engedélyezési vagy Ocp-Apim-előfizetés-kulcs fejléc használata esetén hagyja üresen a appid mezőt más tartalmaznak egy karakterlánc, amely tartalmazza a "Tulajdonos" + "" + "access_token".|lekérdezés| sztring|
|szöveg|(üres)   |Kötelező. A szöveg felosztása a mondatok képviselő karakterláncot. A szöveg mérete legfeljebb 10000 karakterből állhat.|lekérdezés|sztring|
|nyelv   |(üres)    |Kötelező. Egy karakterlánc, amely a bemeneti szöveg nyelvi kódot.|lekérdezés|sztring|
|Engedélyezés|(üres)|Szükséges, ha az appid mező vagy a fejléc Ocp-Apim-előfizetés-kulcs nincs megadva. Engedélyezési jogkivonat: "Tulajdonos" + "" + "access_token".    |header|sztring|
|Az OCP-Apim-előfizetés-kulcs|(üres)|Szükséges, ha az appid mező vagy engedélyezési fejléc nincs megadva.|header|sztring|

### <a name="response-messages"></a>A válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400|Hibás kérés. Ellenőrizze a bemeneti paraméterek és a részletes hibaüzenetet.|
|401|Érvénytelen hitelesítő adatok|
|500|Kiszolgálóhiba. Ha a probléma továbbra is fennáll, ossza meg velünk. Adja meg a hozzávetőleges dátum és idő, a kérelem és a következő a válaszfejlécet X-MS-tranzakció-adatait tartalmazza.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Egy adott nyelvre párhoz fordításai tömbje lekéri a tároló és a fő Célkiszolgáló motor. GetTranslations eltér a fordítás, az összes rendelkezésre álló fordítások adja vissza.

A kérelem URI-azonosítója `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

A kérelem törzse formátuma a következő opcionális TranslationOptions objektumot tartalmaz.

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

A `TranslateOptions` objektum alábbi értékeket tartalmazza. Azok az összes megadása nem kötelező, és a leggyakrabban használt beállításait az alapértelmezett. Megadott elemei ábécésorrendben szerepelnie kell.

* `Category`: A kategória (tartomány) a fordítás tartalmazó karakterlánc. Az alapértelmezett érték "általános".
* `ContentType`: Az egyetlen támogatott, és az alapértelmezett beállítás "text/plain".
* `IncludeMultipleMTAlternatives`: annak meghatározásához, hogy a több alternatívák vissza kell-e a fő Célkiszolgáló motor logikai jelző. Érvényes értékek: true és false (kis-és nagybetűket). Alapértelmezett értéke false, és tartalmazza a csak az 1 helyett. A jelző IGAZ értékű lehetővé teszi, hogy teljesen integrálva van a közös fordítások keretrendszer (CTF) fordításban mesterséges alternatívák generálásához. A funkció lehetővé teszi, hogy a mondatok, amelyeknek nincs alternatíva CTF, a dekóder n-legjobb közül mesterséges alternatívák hozzáadásával alternatívák visszaküldésére használatos.
    - Minősítések a minősítések alkalmazza az alábbiak szerint: 1) a legjobb automatikus fordítás, 5 besorolású. 2.) a alternatívák CTF a tükrözik a felülvizsgáló, hogy -10 + 10 felügyelete. 3.) az automatikusan generált (n-legjobb) fordítási alternatívák 0 minősítést van, és 100 egyezés fokú.
    - Alternatívák visszaadott alternatívák száma legfeljebb maxTranslations, de kisebb lehet száma.
    - Nyelvi párok Ez a funkció nem érhető el fordítások egyszerűsített és hagyományos kínai, mindkét irányban között. Érhető el az összes többi támogatott Microsoft Translator nyelvi párokat.
* `State`: A felhasználói állapot összefüggésbe kérés és válasz. A válaszban visszaadott ugyanaz a tartalma.
* `Uri`: Ezt az URI eredményeket szűrheti. Ha nincs érték megadva, az alapértelmezett érték az összes.
* `User`: A felhasználó által eredmény szűrése. Ha nincs érték megadva, az alapértelmezett érték az összes.

Kérelem `Content-Type` kell `text/xml`.

**Visszatérési érték:** a válasz formátuma a következő.

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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

Ez magában foglalja a `GetTranslationsResponse` elem a következő értékeket tartalmazó:

* `Translations`: Tömbje megfelel talált, a tárolt TranslationMatch (lásd alább) objektumok. A fordítások is tartalmazhat az eredeti szöveg (intelligens egyező) enyhe változatát. A fordítás a rendezendő: 100 %-os először intelligens megfelel az alábbi megegyezik.
* `From`: Ha a metódus nem adta meg a From nyelvi, ez lesz az automatikus nyelvi észlelés eredményét. Ellenkező esetben, akkor a megadott nyelv.
* `State`: A felhasználói állapot összefüggésbe kérés és válasz. A TranslateOptions paraméterben megadott értékét tartalmazza.

TranslationMatch objektum a következőkből áll:

* `Error`: Ha hiba történt egy adott bemeneti karakterláncot, a rendszer tárolja a hibakód. Ellenkező esetben a mező nem üres.
* `MatchDegree`: A rendszer megfelel a tárolóban, beleértve a pontatlan megegyezik a bemeneti mondatok.  MatchDegree azt jelzi, hogy milyen közeli a bemeneti szöveg megegyezik az eredeti található a tárban. 100 által visszaadott érték tartományok 0 és 100, ahol 0-t nem hasonlóság és kis-és nagybetűket pontosan egyeznie.
MatchedOriginalText: Eredeti szöveg, amely megfelelt az eredmény. Csak adott vissza, ha a megfelelő eredeti szöveg különbözött a bemeneti szöveg. Ad vissza egy egyeztetésű forrásszövege. A Microsoft Translator eredmény nem adott vissza.
* `Rating`: A jogosultságokat a minőségi döntési tevő személy jelzi. Gépi fordítás eredmények 5 minősítést fog rendelkezni. Névtelenül megadott fordítások általában olyan minősítést az 1 – 4, miközben mérvadó megadott fordítások általában olyan 6 – 10 minősítést.
* `Count`: A száma a fordítási, amelyek minősítése van beállítva. Az érték a automatikusan lefordított válasz 0 lesz.
* `TranslatedText`: A fordítást.

### <a name="response-class-status-200"></a>Válasz osztály (állapot 200)
A `GetTranslationsResponse` formátumban a fent leírt objektum.

sztring

Válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|A paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más tartalmazza a karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|szöveg|(üres)|Kötelező. A szöveg lefordítani képviselő karakterláncot. A szöveg mérete legfeljebb 10000 karakterből állhat.|lekérdezés|sztring|
|forrás:|(üres)|Kötelező. Egy karakterlánc, amely a fordítás szöveg nyelvi kódját.|lekérdezés|sztring|
|erre: |(üres)    |Kötelező. Egy karakterlánc, amely a szöveg lefordítani a nyelvi kódot.|lekérdezés|sztring|
|maxTranslations|(üres)|Kötelező. Jelző egész számot fordítások maximális számának való visszatéréshez.|lekérdezés|egész szám|
|Engedélyezés| (üres)|Kötelező, ha a `appid` mező vagy `Ocp-Apim-Subscription-Key` fejléc nem lett megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|sztring| header|
|Az OCP-Apim-előfizetés-kulcs|(üres)  |Kötelező, ha a `appid` mező vagy `Authorization` fejléc nem lett megadva.|header|sztring|

### <a name="response-messages"></a>A válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek és a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a probléma továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérelem és a válaszfejlécet szereplő Kérelemazonosító `X-MS-Trans-Info`.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="post-gettranslationsarray"></a>/GetTranslationsArray utáni

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Használja a `GetTranslationsArray` metódusának segítéségével lekérheti több forrás szövegek több fordítási vár.

A kérelem URI-azonosítója `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

A kérelem törzsében formátuma a következő.

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

* `AppId`: Kötelező. Engedélyezési fejléc használata esetén hagyja üresen a appid mezőt más tartalmazza a karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.
* `From`: Kötelező. Egy karakterlánc, amely a fordítás szöveg nyelvi kódját.
* `MaxTranslations`: Kötelező. Jelző egész számot fordítások maximális számának való visszatéréshez.
* `Options`: Nem kötelező. Az alábbi értékeket tartalmazó beállítások objektum. Azok az összes megadása nem kötelező, és a leggyakrabban használt beállításait az alapértelmezett. Megadott elemei ábécésorrendben szerepelnie kell.
    - Kategória ": a kategória (tartomány) a fordítás tartalmazó karakterláncot. Az alapértelmezett érték az általános.
    - `ContentType`: Az egyetlen támogatott, és az alapértelmezett beállítás egyszerű szöveg.
    - `IncludeMultipleMTAlternatives`: annak meghatározásához, hogy a több alternatívák vissza kell-e a fő Célkiszolgáló motor logikai jelző. Érvényes értékek: true és false (kis-és nagybetűket). Alapértelmezett értéke false, és tartalmazza a csak az 1 helyett. A jelző IGAZ értékű lehetővé teszi, hogy teljesen integrálva van a közös fordítások keretrendszer (CTF) fordításban mesterséges alternatívák generálásához. A funkció lehetővé teszi, hogy a mondatok, amelyeknek nincs alternatíva CTF, a dekóder n-legjobb közül mesterséges alternatívák hozzáadásával alternatívák visszaküldésére használatos.
        - Minősítések a minősítések alkalmazza az alábbiak szerint: 1) a legjobb automatikus fordítás, 5 besorolású. 2.) a alternatívák CTF a tükrözik a felülvizsgáló, hogy -10 + 10 felügyelete. 3.) az automatikusan generált (n-legjobb) fordítási alternatívák 0 minősítést van, és 100 egyezés fokú.
        - Alternatívák visszaadott alternatívák száma legfeljebb maxTranslations, de kisebb lehet száma.
        - Nyelvi párok Ez a funkció nem érhető el fordítások egyszerűsített és hagyományos kínai, mindkét irányban között. Érhető el az összes többi támogatott Microsoft Translator nyelvi párokat.
* `State`: A felhasználói állapot összefüggésbe kérés és válasz. A válaszban visszaadott ugyanaz a tartalma.
* `Uri`: Ezt az URI eredményeket szűrheti. Ha nincs érték megadva, az alapértelmezett érték az összes.
* `User`: A felhasználó által eredmény szűrése. Ha nincs érték megadva, az alapértelmezett érték az összes.
* `Texts`: Kötelező. A fordítás szövegeinek tartalmazó tömb. Minden karakterlánc ugyanazon nyelvet kell lennie. Az összes fordítandó szövegek legfeljebb 10000 karakterből állhat. Tömb elemeinek maximális száma érték a 10.
* `To`: Kötelező. Egy karakterlánc, amely a szöveg lefordítani a nyelvi kódot.

Választható elemek elhagyható. Elemek, amelyek közvetlen gyermekei `GetTranslationsArrayRequest` betűrendben szerepelnie kell.

Kérelem `Content-Type` kell `text/xml`.

**Visszatérési érték:** a válasz formátuma a következő.

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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

Minden egyes `GetTranslationsResponse` elem tartalmazza-e a következő értékeket:

* `Translations`: Tömbje megfelel található tárolt `TranslationMatch` objektumok (lásd alább). A fordítások is tartalmazhat az eredeti szöveg (intelligens egyező) enyhe változatát. A fordítás a rendezendő: 100 %-os először intelligens megfelel az alábbi megegyezik.
* `From`: Ha a metódus nem adta meg a `From` nyelvi, ez lesz az automatikus nyelvi észlelés eredményét. Ellenkező esetben, akkor a megadott nyelv.
* `State`: A felhasználói állapot összefüggésbe kérés és válasz. Az azonos értéket tartalmaz a `TranslateOptions` paraméter.

`TranslationMatch` objektum a következőkből áll:
* `Error`: Ha hiba történt egy adott bemeneti karakterláncot, a rendszer tárolja a hibakód. Ellenkező esetben a mező nem üres.
* `MatchDegree`: A rendszer megfelel a tárolóban, beleértve a pontatlan megegyezik a bemeneti mondatok.  `MatchDegree` azt jelzi, hogy milyen közeli a bemeneti szöveg megegyezik az eredeti található a tárban. 100 által visszaadott érték tartományok 0 és 100, ahol 0-t nem hasonlóság és kis-és nagybetűket pontosan egyeznie.
* `MatchedOriginalText`: Az eredmény megfelelt eredeti szöveg. Csak adott vissza, ha a megfelelő eredeti szöveg különbözött a bemeneti szöveg. Ad vissza egy egyeztetésű forrásszövege. A Microsoft Translator eredmény nem adott vissza.
* `Rating`: A jogosultságokat a minőségi döntési tevő személy jelzi. Gépi fordítás eredmények 5 minősítést fog rendelkezni. Névtelenül megadott fordítások általában olyan minősítést az 1 – 4, miközben mérvadó megadott fordítások általában olyan 6 – 10 minősítést.
* `Count`: A száma a fordítási, amelyek minősítése van beállítva. Az érték a automatikusan lefordított válasz 0 lesz.
* `TranslatedText`: A fordítást.


### <a name="response-class-status-200"></a>Válasz osztály (állapot 200)

sztring

Válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|A paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Engedélyezés  |(üres)    |Kötelező, ha a `appid` mező vagy `Ocp-Apim-Subscription-Key` fejléc nem lett megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|Az OCP-Apim-előfizetés-kulcs|(üres)  |Kötelező, ha a `appid` mező vagy `Authorization` fejléc nem lett megadva.|header|sztring|

### <a name="response-messages"></a>A válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek és a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a probléma továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérelem és a válaszfejlécet szereplő Kérelemazonosító `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [V3 fordító szöveg API áttelepítése](../migrate-to-v3.md)










