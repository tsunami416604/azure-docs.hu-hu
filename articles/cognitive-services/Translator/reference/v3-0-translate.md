---
title: Translator Text API fordítására metódust
titleSuffix: Azure Cognitive Services
description: Használja a Translator Text API fordítására metódust.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: 4f6c420ab76462818fb17308d062cc9d881af7df
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091035"
---
# <a name="translator-text-api-30-translate"></a>Translator Text API 3.0: Translate

Szöveg lefordítása.

## <a name="request-url"></a>Kérés URL-címe

Küldjön egy `POST` kérelmet:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>A kérés paraméterei

A kérelem lekérdezési karakterláncot az átadott paraméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>API-verzió</td>
    <td><em>Kötelező paraméter</em>.<br/>Az ügyfél által kért API-verzió. Az érték lehet <code>3.0</code>.</td>
  </tr>
  <tr>
    <td>forrás:</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Meghatározza a bemeneti szöveg nyelvét. Milyen nyelveken érhetők el lefordítani a szavazatait található [támogatott nyelvek](./v3-0-languages.md) használatával a <code>translation</code> hatókör. Ha a <code>from</code> paraméter nincs megadva, az automatikus nyelvfelismerést a Forrásnyelv alkalmazásával.</td>
  </tr>
  <tr>
    <td>erre:</td>
    <td><em>Kötelező paraméter</em>.<br/>Meghatározza a kimeneti szöveg nyelvét. A Célnyelv egyikének kell lennie a [támogatott nyelvek](./v3-0-languages.md) szerepel a <code>translation</code> hatókör. Például <code>to=de</code> német fordításához.<br/>Újraindításához ismételje meg a paraméter a lekérdezési karakterláncban egyszerre több nyelvhez lefordítani lehetőség. Például <code>to=de&to=it</code> , német, olasz lefordítja.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Határozza meg, hogy a szöveg áll fordítás alatt-e egyszerű szöveges vagy HTML-szöveg. HTML kell lennie egy megfelelően formázott, teljes elemhez. Lehetséges értékek a következők: <code>plain</code> (alapértelmezés) vagy <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Nem kötelező paraméter</em>.<br/>A kategória (tartomány), a fordítás megadó karakterlánc. Ezzel a paraméterrel fordítások beszerezni egy testre szabott rendszer beépített [egyéni a fordítót](../customization.md). Kategóriaazonosító egyéni a fordítót projektjének hozzá ezt a paramétert használni a telepített testre szabott rendszert. Alapértelmezett érték: <code>general</code>.</td>
  </tr>
  <tr>
    <td>ProfanityAction</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Itt adhatja meg, hogyan kell kezelni profanities fordításokat. Lehetséges értékek a következők: <code>NoAction</code> (alapértelmezett), <code>Marked</code> vagy <code>Deleted</code>. Módon kezelni a vulgáris ismertetése: [Cenzúrázása kezelési](#handle-profanity).</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Itt adhatja meg, hogyan profanities kell megjelölni a fordításokat. Lehetséges értékek a következők: <code>Asterisk</code> (alapértelmezés) vagy <code>Tag</code>. Módon kezelni a vulgáris ismertetése: [Cenzúrázása kezelési](#handle-profanity).</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Megadja, hogy a igazítás kivetítés forrás szöveget a lefordított szöveg tartalmazza-e. Lehetséges értékek a következők: <code>true</code> vagy <code>false</code> (alapértelmezett). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Megadja, hogy a mondat határait a bemeneti szöveg és a lefordított szöveg tartalmazza-e. Lehetséges értékek a következők: <code>true</code> vagy <code>false</code> (alapértelmezett).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Tartalék nyelvet ad meg, ha a bemeneti szöveg nyelvét nem lehet azonosítani. Automatikus észlelés nyelv alkalmazása során a <code>from</code> paraméter nincs megadva. Ha észlelése sikertelen, a <code>suggestedFrom</code> nyelvi lesz.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Megadja a parancsprogramot a bemeneti szöveg.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Adja meg a szkript a lefordított szöveg.</td>
  </tr>
  <tr>
    <td>AllowFallback</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Itt adhatja meg, hogy a szolgáltatás engedélyezett egy általános rendszer álljon, ha egy egyéni rendszer nem létezik. Lehetséges értékek a következők: <code>true</code> (alapértelmezés) vagy <code>false</code>.<br/><br/><code>allowFallback=false</code> Megadja, hogy a fordítás csak tanított rendszerek kell használnia a <code>category</code> a kérés által megadott. Ha nyelvet X és Y nyelv fordítását igényel láncolási keresztül pivot nyelv E, majd minden a rendszer a lánc (X -> E és E -> Y) kell lennie az egyéni és azonos kategóriába tartoznak. Ha a rendszer nem található az adott kategóriával rendelkezik, a kérelem 400 állapotkódot ad vissza. <code>allowFallback=true</code> Itt adhatja meg, hogy a szolgáltatás engedélyezett egy általános rendszer álljon, ha egy egyéni rendszer nem létezik.
</td>
  </tr>
</table> 

Kérelemfejlécek a következők:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>_Egy engedélyezési_<br/>_header_</td>
    <td><em>Szükséges kérelem fejléce</em>.<br/>Lásd: [elérhető lehetőségek a hitelesítéshez](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Szükséges kérelem fejléce</em>.<br/>Megadja a hasznos tartalom típusát. Lehetséges értékek a következők: <code>application/json</code>.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td><em>Szükséges kérelem fejléce</em>.<br/>A kérelem törzsében mennyi.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Választható</em>.<br/>Egy ügyfél által létrehozott GUID egyedi azonosítására szolgál a kérelmet. Kihagyhatja ezt a fejlécet, ha a lekérdezési karakterláncban nevű lekérdezési paraméter használatával adja meg a nyomkövetési azonosító <code>ClientTraceId</code>.</td>
  </tr>
</table> 

## <a name="request-body"></a>A kérés törzse

A kérelem törzsében egy JSON-tömböt. Egyes tömbelemeken nevű karakterlánc tulajdonsággal rendelkező JSON-objektum `Text`, amely lefordítja a karakterláncot jelöli.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 100 elemeket is rendelkezhet.
* A kérelem tartalmazza a teljes szöveg nem lehet hosszabb 5000 karakternél, beleértve a szóközöket.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömböt az egyes sztringek a bemeneti számtömbből egy eredmény. Egy objektumra a következő tulajdonságokat tartalmazza:

  * `detectedLanguage`: Egy objektum leíró nyelvben keresztül az alábbi tulajdonságokat:

      * `language`: Egy karakterlánc, amely a felismert nyelv kódja.

      * `score`: Az eredmény magabiztosan jelző float érték. A pontszám, nulla és a egy között, és a egy alacsony pontszámmal azt jelzi, hogy egy alacsony megbízhatósági.

    A `detectedLanguage` tulajdonság csak akkor szerepel az eredményobjektum, nyelvi automatikus észlelés igénylésekor.

  * `translations`: Fordítási eredmények tömbje. A tömb mérete megadva a cél nyelvek száma megegyezik a `to` lekérdezési paraméter. A tömb egyes elemei a következőket tartalmazza:

    * `to`: A Célnyelv nyelvkódja képviselő karakterláncot.

    * `text`: Egy karakterlánc, így a lefordított szöveg.

    * `transliteration`: Jogosultságot ad a lefordított szöveg a parancsfájl által meghatározott objektum a `toScript` paraméter.

      * `script`: A célként megadott parancsfájl megadó karakterlánc.   

      * `text`: A cél-szkriptben a lefordított szöveg így karakterlánc.

    A `transliteration` objektum lehetőség nem része, ha átbetűzésű nem kerül sor.

    * `alignment`: Egy nevű egyetlen karakterlánc tulajdonsággal rendelkező objektumot `proj`, amely adjon meg szöveget a lefordított szöveg. Az igazítás csak információ Ha a kérés paramétereihez `includeAlignment` van `true`. Igazítás, karakterlánc-érték a következő formátumban adja vissza: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  A kettőspont elválasztja a kezdő és záró index, a dash elkülöníti a nyelveket, és terület a szavakat osztja szét. Több szóból előfordulhat, hogy összhangba nulla, egy vagy több szóból más nyelven, és lehet, hogy a igazított szavak nem összefüggő. Zarovnání információ nem érhető el, ha az igazítási elem üres lesz. Lásd: [igazítás megszerzése](#obtain-alignment-information) például és korlátozások.

    * `sentLen`: Mondat határok visszaadása a bemeneti és kimeneti szövegét az objektum.

      * `srcSentLen`: A bemeneti szövegben a mondatok hosszának jelölő egész számok tömbje. A hossz a tömb mondatok számát, és az értékek a következők minden mondat hosszát.

      * `transSentLen`:  A mondatok a lefordított szöveg hosszának jelölő egész számok tömbje. A hossz a tömb mondatok számát, és az értékek a következők minden mondat hosszát.

    Határok mondat csak tartalmazza, ha a kérés paramétereihez `includeSentenceLength` van `true`.

  * `sourceText`: Egy nevű egyetlen karakterlánc tulajdonsággal rendelkező objektumot `text`, a forrás nyelv alapértelmezett szkriptben révén a bemeneti szöveg. `sourceText` a tulajdonság jelenik meg, csak akkor, ha a bemenet egy parancsfájlt, amely nem a szokásos parancsfájl nyelvének van kifejezve. Például, ha a bemeneti adatok lettek Latin parancsfájlokat, majd írt arab `sourceText.text` volna az azonos arab szöveg alakítva Arab parancsfájlt.

Példa a JSON-válaszok szerepelnek a [példák](#examples) szakaszban.

## <a name="response-headers"></a>Válaszfejlécek

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
    <tr>
    <td>X-RequestId</td>
    <td>A kérelem azonosíthatja a szolgáltatás által létrehozott értéket. Hibaelhárítási célokra szolgál.</td>
  </tr>
  <tr>
    <td>X-MT-System</td>
    <td>Meghatározza a rendszer minden kért fordításhoz "to" nyelv fordítását használt. Az érték karakterláncok vesszővel tagolt listája. Mindegyik sztring egy típusát jelzi:<br/><ul><li>Egyéni - kérés tartalmaz egy egyéni rendszert, és legalább egy egyéni rendszer fordítási során lett megadva.</li><li>Csapat – minden más kérelemhez</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Állapotkódok

Az alábbi táblázat a lehetséges HTTP-állapotkódok, amely egy kérés adja vissza. 

<table width="100%">
  <th width="20%">Állapotkód</th>
  <th>Leírás</th>
  <tr>
    <td>200</td>
    <td>Siker.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>A lekérdezési paraméterek egyike hiányzik vagy érvénytelen. Javítsa ki a kérelem paramétereinek újrapróbálkozás előtt.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Nem sikerült hitelesíteni a kérelmet. Ellenőrizze a használt hitelesítő adatok-e a megadott, és érvényes.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A kérelem nem engedélyezett. Tekintse meg a részletes hibaüzenetet. Ez gyakran azt jelenti, hogy a próba-előfizetéshez biztosított összes ingyenes fordítások használtak.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>A kérelem nem teljesíthető, mert egy erőforrás hiányzik. Tekintse meg a részletes hibaüzenetet. Egyéni használatakor <code>category</code>, ez gyakran azt jelenti, hogy az egyéni fordítási rendszer még nem érhető el a kérelmek kiszolgálását. A kérelem (pl. 1 perces) várakozási idő elteltével meg kell ismételni.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>A hívó túl sok kérelmet küld.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse be a: dátum és idő a hiba, a válaszfejlécet a kérelem azonosító <code>X-RequestId</code>, és az ügyfél-azonosítója a fejléc <code>X-ClientTraceId</code>.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló átmenetileg nem érhető el. Ismételje meg a kérelmet. Ha a hiba továbbra is fennáll, jelentse be a: dátum és idő a hiba, a válaszfejlécet a kérelem azonosító <code>X-RequestId</code>, és az ügyfél-azonosítója a fejléc <code>X-ClientTraceId</code>.</td>
  </tr>
</table> 

Ha hiba történik, a kérelem is hiba JSON-választ adja vissza. A hibakód egy 6 jegyű számot csoportba foglalása 3 számjegyből HTTP-állapotkód további követ és 3-jegyű szám kategorizálása a hibát. Gyakori hibakódok találhatók a [v3 a Translator Text API referencialapja](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Példák

### <a name="translate-a-single-input"></a>Egyetlen bevitel fordítása

Ez a példa bemutatja, hogyan kell az angol, egyszerűsített kínai egyetlen mondatok fordítása.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

A válasz törzse a következő:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

A `translations` tömböt tartalmaz egy elemet, amely biztosítja a legegyszerűbb a bemeneti szöveg fordítását.

### <a name="translate-a-single-input-with-language-auto-detection"></a>A nyelvi automatikus észlelés egyetlen bemeneti fordítása

Ez a példa bemutatja, hogyan kell az angol, egyszerűsített kínai egyetlen mondatok fordítása. A kérelem nem határoz meg a beviteli nyelv. Automatikus észlelését, a forrás nyelvet használja.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

A válasz törzse a következő:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
A válasz a hasonló az előző példában a választ. Mivel a kért nyelv automatikus észlelését, akkor a válasz a észlelt a bemeneti szöveg nyelvét kapcsolatos adatokat is tartalmaz. 

### <a name="translate-with-transliteration"></a>A átbetűzésű fordítása

Az előző példában meghosszabbíthatja átbetűzésű hozzáadása. A következő kérelmet az egy kínai fordítás Latin betűs nyelven írt kéri.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

A válasz törzse a következő:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

A fordítási eredménye most már tartalmaz egy `transliteration` tulajdonság, amely lehetővé teszi a lefordított szöveg Latin karaktereket használ.

### <a name="translate-multiple-pieces-of-text"></a>Több darabból szöveg lefordítása

A kérelem törzsében szereplő karakterláncok a lekérésükhöz több karakterlánc egyszerre fordítása.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

---

A válasz törzse a következő:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Többnyelvű fordítás

Ez a példa bemutatja, hogyan lefordítani a ugyanazon bemeneti egyetlen kérelem több nyelven.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

A válasz törzse a következő:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Leíró cenzúrázása

Általában a Translator szolgáltatás megőrzi a vulgáris, amely a forrásból a fordítás található. Kulturális környezetek közötti különbségeit cenzúrázása mértékét, és a környezetet, amely a szavakat profán lehetővé teszi, és ennek eredményeképpen a párhuzamossági cenzúrázása a célként megadott nyelven lehet, hogy bővíteni vagy csökkenteni.

Ha el szeretné kerülni, a fordítás, függetlenül a forrás szövegben cenzúrázása jelenléte cenzúrázása első a vulgáris szűrés lehetőséget használhatja. A beállítás lehetővé teszi, hogy válassza ki, hogy törölni kíván megjelölni profanities megfelelő címkékkel (felkínálva a lehetőséget a saját utófeldolgozás hozzáadása), vagy azt szeretné, hogy hajt végre semmilyen műveletet cenzúrázása megtekintéséhez. Az elfogadott értékek a `ProfanityAction` vannak `Deleted`, `Marked` és `NoAction` (alapértelmezett).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Műveletek</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Ez az az alapértelmezett viselkedést. Káromkodás cél forrásból fogja továbbítani.<br/><br/>
    <strong>Példa forrás (japán)</strong>: 彼はジャッカスです。<br/>
    <strong>Példa fordítási (angol nyelven)</strong>: Egy Pápaszemes áll.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Profán szavakat a kimenet nélküli helyettesítő törlődni fog.<br/><br/>
    <strong>Példa forrás (japán)</strong>: 彼はジャッカスです。<br/>
    <strong>Példa fordítási (angol nyelven)</strong>: Ő egy.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Profán helyébe egy jelölő a kimenetben. A jelölő függ a <code>ProfanityMarker</code> paraméter.<br/><br/>
A <code>ProfanityMarker=Asterisk</code>, profán szavak helyén <code>***</code>:<br/>
    <strong>Példa forrás (japán)</strong>: 彼はジャッカスです。<br/>
    <strong>Példa fordítási (angol nyelven)</strong>: Ő egy \* \* \*.<br/><br/>
A <code>ProfanityMarker=Tag</code>, profán szavak XML-címkék veszi körül &lt;cenzúrázása&gt; és &lt;/profanity&gt;:<br/>
    <strong>Példa forrás (japán)</strong>: 彼はジャッカスです。<br/>
    <strong>Példa fordítási (angol nyelven)</strong>: Ő egy &lt;cenzúrázása&gt;Pápaszemes&lt;/profanity&gt;.
  </tr>
</table> 

Példa:

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

Ez adja vissza:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Összehasonlítás:

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

A legutóbbi kérés adja vissza:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Jelölőnyelvi tartalmak lefordítása, és döntse el, hogy lefordított elemek

Szokás lefordítja a tartalmat, például egy HTML-oldalt a tartalom jelölőnyelvi tartalmazó vagy XML-dokumentum tartalmát. Lekérdezési paramétert `textType=html` amikor fordítása a címkékkel rendelkező tartalmát. Emellett hasznos néha fordítási kizárandó konkrét tartalmakat. Az attribútum is használhat `class=notranslate` , adja meg a tartalom maradjon az eredeti nyelven. A következő példában az első belüli tartalom `div` elem nem fordítja, miközben a tartalmat, a második `div` elem lesznek fordítva.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Íme egy mintakérelmet mutatja be.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

---

A válasz a következő:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Zarovnání megszerzése

Zarovnání információk fogadását, adja meg a `includeAlignment=true` a lekérdezési karakterláncot.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation.'}]"
```

---

A válasz a következő:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

Zarovnání információk kezdődik `0:2-0:1`, ami azt jelenti, hogy a forrás szöveg első három karaktere (`The`) térkép a lefordított szöveget az első két karakter lehet (`La`).

Vegye figyelembe a következő korlátozásokkal:

* Zarovnání csak a nyelvi párok részéhez adja vissza:
  - a más nyelvre; angol nyelven
  - az angol, egyszerűsített kínai, hagyományos kínai és; az angol nyelvű lett bármilyen más nyelven
  - a japán, koreai és japán, koreai.
* Igazítás nem fog kapni, ha a mondat jelzésének fordítását. Jelzésének fordítását. például a "Ez a tesztüzenet", "Szeretek," és egyéb nagy gyakoriságú mondatokat.

### <a name="obtain-sentence-boundaries"></a>Szerezze be a mondat határok

Mondat hossza információt kap a forrás szöveg és a fordítást, adja meg a `includeSentenceLength=true` a lekérdezési karakterláncot.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

---

A válasz a következő:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>A dinamikus szótárral fordítása

Ha már ismeri a fordítást egy szót vagy kifejezést a alkalmazni szeretné, adja meg, mint belül a kérelem markup. A dinamikus szótár csak eszköze biztonságosan főnevek, mint például a megfelelő és a termék nevét.

A címkék adja meg a következő szintaxist használja.

``` 
<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>
```

Vegyük példaként az angol nyelvű mondat "a word wordomatic dictionary-bejegyzés is." A word megőrzéséhez _wordomatic_ a fordítás, a kérelem küldése:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Az eredmény a következő:

```
[
    {
        "translations":[
            {"text":"Das Wort "wordomatic" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Ez a funkció működik együtt a `textType=text` vagy `textType=html`. A szolgáltatás takarékosan. A megfelelő, és sokkal jobb megoldás a fordítás testreszabása módja egyéni Translator használatával. Egyéni a fordítót lehetővé teszi a környezetet és statisztikai valószínűségek teljes használatát. Ha van, vagy hozhat létre, amely megjeleníti a munkahelyi vagy kifejezés környezetben betanítási adatok megengedhet, sokkal jobb eredményeket kap. [További tudnivalók a Translator egyéni](../customization.md).
 





