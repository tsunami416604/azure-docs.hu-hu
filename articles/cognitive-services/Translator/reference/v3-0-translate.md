---
title: Translator Text API fordítására metódust
titleSuffix: Azure Cognitive Services
description: Használja a Translator Text API fordítására metódust.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 1841730a39d29c5fe1f3451b7614818e924b339f
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128757"
---
# <a name="translator-text-api-30-translate"></a>Translator Text API 3.0: fordítása

Szöveg lefordítása.

## <a name="request-url"></a>Lekérdezés URL-címe

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
    <td>*Kötelező paraméter*.<br/>Az ügyfél által kért API-verzió. Az érték lehet `3.0`.</td>
  </tr>
  <tr>
    <td>forrás:</td>
    <td>*Nem kötelező paraméter*.<br/>Meghatározza a bemeneti szöveg nyelvét. Milyen nyelveken érhetők el lefordítani a szavazatait található [támogatott nyelvek](.\v3-0-languages.md) használatával a `translation` hatókör. Ha a `from` paraméter nincs megadva, az automatikus nyelvfelismerést a Forrásnyelv alkalmazásával.</td>
  </tr>
  <tr>
    <td>erre:</td>
    <td>*Kötelező paraméter*.<br/>Meghatározza a kimeneti szöveg nyelvét. A Célnyelv egyikének kell lennie a [támogatott nyelvek](.\v3-0-languages.md) szerepel a `translation` hatókör. Például `to=de` német fordításához.<br/>Újraindításához ismételje meg a paraméter a lekérdezési karakterláncban egyszerre több nyelvhez lefordítani lehetőség. Például `to=de&to=it` , német, olasz lefordítja.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td>*Nem kötelező paraméter*.<br/>Határozza meg, hogy a szöveg áll fordítás alatt-e egyszerű szöveges vagy HTML-szöveg. HTML kell lennie egy megfelelően formázott, teljes elemhez. Lehetséges értékek a következők: `plain` (alapértelmezés) vagy `html`.</td>
  </tr>
  <tr>
    <td>category</td>
    <td>*Nem kötelező paraméter*.<br/>A kategória (tartomány), a fordítás megadó karakterlánc. Ezzel a paraméterrel fordítások beszerezni egy testre szabott rendszer beépített [egyéni a fordítót](../customization.md). Alapértelmezett érték: `general`.</td>
  </tr>
  <tr>
    <td>ProfanityAction</td>
    <td>*Nem kötelező paraméter*.<br/>Itt adhatja meg, hogyan kell kezelni profanities fordításokat. Lehetséges értékek a következők: `NoAction` (alapértelmezett), `Marked` vagy `Deleted`. Módon kezelni a vulgáris ismertetése: [Cenzúrázása kezelési](#handle-profanity).</td>
  </tr>
  <tr>
    <td>ProfanityMarker</td>
    <td>*Nem kötelező paraméter*.<br/>Itt adhatja meg, hogyan profanities kell megjelölni a fordításokat. Lehetséges értékek a következők: `Asterisk` (alapértelmezés) vagy `Tag`. Módon kezelni a vulgáris ismertetése: [Cenzúrázása kezelési](#handle-profanity).</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td>*Nem kötelező paraméter*.<br/>Megadja, hogy a igazítás kivetítés forrás szöveget a lefordított szöveg tartalmazza-e. Lehetséges értékek a következők: `true` vagy `false` (alapértelmezett). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td>*Nem kötelező paraméter*.<br/>Megadja, hogy a mondat határait a bemeneti szöveg és a lefordított szöveg tartalmazza-e. Lehetséges értékek a következők: `true` vagy `false` (alapértelmezett).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td>*Nem kötelező paraméter*.<br/>Tartalék nyelvet ad meg, ha a bemeneti szöveg nyelvét nem lehet azonosítani. Automatikus észlelés nyelv alkalmazása során a `from` paraméter nincs megadva. Ha észlelése sikertelen, a `suggestedFrom` nyelvi lesz.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Nem kötelező paraméter*.<br/>Megadja a parancsprogramot a bemeneti szöveg.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Nem kötelező paraméter*.<br/>Adja meg a szkript a lefordított szöveg.</td>
  </tr>
</table> 

Kérelemfejlécek a következők:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>_Egy engedélyezési_<br/>_Fejléc_</td>
    <td>*Szükséges kérelem fejléce*.<br/>Lásd: [elérhető lehetőségek a hitelesítéshez](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Szükséges kérelem fejléce*.<br/>Megadja a hasznos tartalom típusát. Lehetséges értékek a következők: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Szükséges kérelem fejléce*.<br/>A kérelem törzsében mennyi.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Választható*.<br/>Egy ügyfél által létrehozott GUID egyedi azonosítására szolgál a kérelmet. Kihagyhatja ezt a fejlécet, ha a lekérdezési karakterláncban nevű lekérdezési paraméter használatával adja meg a nyomkövetési azonosító `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Kérelem törzse

A kérelem törzsében egy JSON-tömböt. Egyes tömbelemeken nevű karakterlánc tulajdonsággal rendelkező JSON-objektum `Text`, amely lefordítja a karakterláncot jelöli.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 25 elemeket is rendelkezhet.
* A kérelem tartalmazza a teljes szöveg nem lehet hosszabb 5000 karakternél, beleértve a szóközöket.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömböt az egyes sztringek a bemeneti számtömbből egy eredmény. Egy objektumra a következő tulajdonságokat tartalmazza:

  * `detectedLanguage`: Egy objektum leíró nyelvben keresztül az alábbi tulajdonságokat:

      * `language`: Egy karakterlánc, amely a felismert nyelv kódja.

      * `score`: Egy jelző a bizalom, az eredmény a lebegőpontos értéket. A pontszám, nulla és a egy között, és a egy alacsony pontszámmal azt jelzi, hogy egy alacsony megbízhatósági.

    A `detectedLanguage` tulajdonság csak akkor szerepel az eredményobjektum, nyelvi automatikus észlelés igénylésekor.

  * `translations`: Fordítási eredmény egy tömbje. A tömb mérete megadva a cél nyelvek száma megegyezik a `to` lekérdezési paraméter. A tömb egyes elemei a következőket tartalmazza:

    * `to`: Egy karakterlánc, amely a célként megadott nyelv nyelvi kódját.

    * `text`: Egy karakterlánc, így a fordítást.

    * `transliteration`: A parancsfájl által megadott jogosultságot ad a lefordított szöveg egy objektum a `toScript` paraméter.

      * `script`: Egy karakterlánc, a cél-szkript megadása.   

      * `text`: A célként megadott parancsfájl jogosultságot ad a lefordított szöveg egy karakterlánc.

    A `transliteration` objektum lehetőség nem része, ha átbetűzésű nem kerül sor.

    * `alignment`: Egy objektum nevű egyetlen karakterlánc tulajdonsággal rendelkező `proj`, amely adjon meg szöveget a lefordított szöveg. Az igazítás csak információ Ha a kérés paramétereihez `includeAlignment` van `true`. Igazítás, karakterlánc-érték a következő formátumban adja vissza: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  A kettőspont elválasztja a kezdő és záró index, a dash elkülöníti a nyelveket, és terület a szavakat osztja szét. Több szóból előfordulhat, hogy összhangba nulla, egy vagy több szóból más nyelven, és lehet, hogy a igazított szavak nem összefüggő. Zarovnání információ nem érhető el, ha az igazítási elem üres lesz. Lásd: [igazítás megszerzése](#obtain-alignment-information) például és korlátozások.

    * `sentLen`: Egy objektum mondat határok visszaadása a bemeneti és kimeneti szövegét.

      * `srcSentLen`: Egy a mondatok a bemeneti szöveg hosszának jelölő egész számok tömbje. A hossz a tömb mondatok számát, és az értékek a következők minden mondat hosszát.

      * `transSentLen`: Egy a mondatok a lefordított szöveg hosszának jelölő egész számok tömbje. A hossz a tömb mondatok számát, és az értékek a következők minden mondat hosszát.

    Határok mondat csak tartalmazza, ha a kérés paramétereihez `includeSentenceLength` van `true`.

  * `sourceText`: Egy objektum nevű egyetlen karakterlánc tulajdonsággal rendelkező `text`, a forrás nyelv alapértelmezett szkriptben révén a bemeneti szöveg. `sourceText` a tulajdonság jelenik meg, csak akkor, ha a bemenet egy parancsfájlt, amely nem a szokásos parancsfájl nyelvének van kifejezve. Például, ha a bemeneti adatok lettek Latin parancsfájlokat, majd írt arab `sourceText.text` volna az azonos arab szöveg alakítva Arab parancsfájlt.

Példa a JSON-válaszok szerepelnek a [példák](#examples) szakaszban.

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
    <td>429</td>
    <td>A hívó túl sok kérelmet küld.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse be a: dátum és idő a hiba, a válaszfejlécet a kérelem azonosító `X-RequestId`, és az ügyfél-azonosítója a fejléc `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló átmenetileg nem érhető el. Ismételje meg a kérelmet. Ha a hiba továbbra is fennáll, jelentse be a: dátum és idő a hiba, a válaszfejlécet a kérelem azonosító `X-RequestId`, és az ügyfél-azonosítója a fejléc `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Példák

### <a name="translate-a-single-input"></a>Egyetlen bevitel fordítása

Ez a példa bemutatja, hogyan kell az angol, egyszerűsített kínai egyetlen mondatok fordítása.

# <a name="curltabcurl"></a>[A curl](#tab/curl)

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

# <a name="curltabcurl"></a>[A curl](#tab/curl)

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

# <a name="curltabcurl"></a>[A curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
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
                "transliteration":{"text":"nǐ hǎo , nǐ jiào shén me míng zì ？","script":"Latn"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

A fordítási eredménye most már tartalmaz egy `transliteration` tulajdonság, amely lehetővé teszi a lefordított szöveg Latin karaktereket használ.

### <a name="translate-multiple-pieces-of-text"></a>Több darabból szöveg lefordítása

A kérelem törzsében szereplő karakterláncok a lekérésükhöz több karakterlánc egyszerre fordítása.

# <a name="curltabcurl"></a>[A curl](#tab/curl)

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

# <a name="curltabcurl"></a>[A curl](#tab/curl)

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
    <td>`NoAction`</td>
    <td>Ez az az alapértelmezett viselkedést. Káromkodás cél forrásból fogja továbbítani.<br/><br/>
    **Példa forrás (japán)**: 彼はジャッカスです。<br/>
    **Példa fordítási (angol nyelven)**: egy Pápaszemes.
    </td>
  </tr>
  <tr>
    <td>`Deleted`</td>
    <td>Profán szavakat a kimenet nélküli helyettesítő törlődni fog.<br/><br/>
    **Példa forrás (japán)**: 彼はジャッカスです。<br/>
    **Példa fordítási (angol nyelven)**:, ő egy.
    </td>
  </tr>
  <tr>
    <td>`Marked`</td>
    <td>Profán helyébe egy jelölő a kimenetben. A jelölő függ a `ProfanityMarker` paraméter.<br/><br/>
A `ProfanityMarker=Asterisk`, profán szavak helyén `***`:<br/>
    **Példa forrás (japán)**: 彼はジャッカスです。<br/>
    **Példa fordítási (angol nyelven)**:, ő egy \* \* \*.<br/><br/>
A `ProfanityMarker=Tag`, profán szavak XML-címkék veszi körül &lt;cenzúrázása&gt; és &lt;/profanity&gt;:<br/>
    **Példa forrás (japán)**: 彼はジャッカスです。<br/>
    **Példa fordítási (angol nyelven)**:, ő egy &lt;cenzúrázása&gt;Pápaszemes&lt;/profanity&gt;.
  </tr>
</table> 

Példa:

# <a name="curltabcurl"></a>[A curl](#tab/curl)

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

# <a name="curltabcurl"></a>[A curl](#tab/curl)

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

# <a name="curltabcurl"></a>[A curl](#tab/curl)

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

# <a name="curltabcurl"></a>[A curl](#tab/curl)

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

# <a name="curltabcurl"></a>[A curl](#tab/curl)

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
 





