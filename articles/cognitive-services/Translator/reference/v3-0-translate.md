---
title: A Microsoft Translator szöveg API fordítása metódus |} Microsoft Docs
titleSuffix: Cognitive Services
description: A Microsoft Translator szöveg API fordítása módszer használatát.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: d8d5e1e2fac747fa733f1d92c08008b7eac2a1bc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349703"
---
# <a name="text-api-30-translate"></a>Szöveg API 3.0: fordítása

Az eszköz szöveg.

## <a name="request-url"></a>Lekérdezés URL-címe

Küldjön egy `POST` elküldeni a kérelmet:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>A kérés paraméterei

A lekérdezési karakterlánc átadja a kérelemben szereplő paraméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>API-verzió</td>
    <td>*A paraméter kötelező*.<br/>Az API-t, az ügyfél által kért verzióját. Az értéknek kell lennie `3.0`.</td>
  </tr>
  <tr>
    <td>forrás:</td>
    <td>*Nem kötelező paraméter*.<br/>Meghatározza a bemeneti szöveg nyelvét. Milyen nyelveken érhetők el a szervezetifiók fordítani található [támogatott nyelv](.\v3-0-languages.md) használatával a `translation` hatókör. Ha a `from` paraméter nincs megadva, a nyelv automatikus észlelési határozza meg az adatforrás nyelvi alkalmazza.</td>
  </tr>
  <tr>
    <td>erre:</td>
    <td>*A paraméter kötelező*.<br/>Meghatározza a kimeneti szöveg. A megadott nyelv közül kell a [támogatott nyelv](.\v3-0-languages.md) szerepel a `translation` hatókör. Tegyük fel például, `to=de` német lefordítani.<br/>A lekérdezési karakterláncban paraméter többszöri használatával egyszerre több nyelvhez lefordítani lehetőség. Tegyük fel például, `to=de&to=it` német, olasz és lefordítani.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td>*Nem kötelező paraméter*.<br/>Azt határozza meg, hogy a szöveg fordítás alatt álló egyszerű szöveges vagy HTML-szöveg. HTML-kell lennie egy megfelelően formázott, a teljes elemet. Lehetséges értékek a következők: `plain` (alapértelmezés) vagy `html`.</td>
  </tr>
  <tr>
    <td>category</td>
    <td>*Nem kötelező paraméter*.<br/>A kategória (tartomány) a fordítás karakterlánc. Ezzel a paraméterrel fordítások lekérése egy testreszabott rendszer-val készült [egyéni fordító](../customization.md). Alapértelmezett érték: `general`.</td>
  </tr>
  <tr>
    <td>ProfanityAction</td>
    <td>*Nem kötelező paraméter*.<br/>Itt adhatja meg, hogyan profanities a fordítások kell kezelni. Lehetséges értékek a következők: `NoAction` (alapértelmezett), `Marked` vagy `Deleted`. Kezelni Profanitás módjai ismertetése: [Profanitás kezelési](#handle-profanity).</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td>*Nem kötelező paraméter*.<br/>Itt adhatja meg, hogyan profanities a fordítások kell megjelölni. Lehetséges értékek a következők: `Asterisk` (alapértelmezés) vagy `Tag`. Kezelni Profanitás módjai ismertetése: [Profanitás kezelési](#handle-profanity).</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td>*Nem kötelező paraméter*.<br/>Megadja, hogy a forrás szöveg lefordított szöveg igazítási vetítéshez tartalmazza. Lehetséges értékek a következők: `true` vagy `false` (alapértelmezett). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td>*Nem kötelező paraméter*.<br/>Megadja, hogy a mondatok határok a bemeneti szöveg és a lefordított szöveg tartalmazza-e. Lehetséges értékek a következők: `true` vagy `false` (alapértelmezett).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td>*Nem kötelező paraméter*.<br/>Tartalék nyelvet ad meg, ha a bemeneti szöveg nyelvének nem azonosítható. Nyelvi automatikus észlelés vonatkozik. Ha a `from` paraméter nincs megadva. Ha észlelése sikertelen, a `suggestedFrom` nyelvi a rendszer feltételezi.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Nem kötelező paraméter*.<br/>Adja meg a parancsfájlt, amely a bemeneti szöveg.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Nem kötelező paraméter*.<br/>Adja meg a parancsfájl a lefordított szöveg.</td>
  </tr>
</table> 

Kérelemfejléc a következők:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>_Egy engedélyezési_<br/>_Fejléc_</td>
    <td>*Szükséges fejléc*.<br/>Lásd: [elérhető lehetőségek a hitelesítéshez](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Szükséges fejléc*.<br/>Adja meg a tartalom a tartalom. Lehetséges értékek a következők: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Szükséges fejléc*.<br/>A kérelem törzsében hosszát.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Választható*.<br/>Ügyfél által létrehozott GUID-azonosító egyedi módon azonosítja a kérelmet. Kihagyhatja ezt a fejlécet, ha a lekérdezési karakterláncban nevű lekérdezési paraméter használatával adja meg a nyomkövetési azonosító `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Kérelem törzse

A kérelem törzse egy JSON-tömb. Minden tömbelem egy JSON-objektum nevű karakterlánc tulajdonsággal `Text`, amely jelenti, hogy a karakterlánc lefordítani.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

A következő korlátozások vonatkoznak:

* A tömb legfeljebb 25 elemek szerepelhetnek.
* A kérelem tartalmazza a teljes szöveg legfeljebb 5000 karaktereket, szóközöket is beleértve.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb minden egyes karakterláncot kell megadnia a bemeneti tömb egy eredménnyel:. Egy eredményobjektum tartalmazza a következő tulajdonságokkal:

  * `detectedLanguage`: Az objektum leírására, hogy az észlelt a következő tulajdonságok nyelv szerint:

      * `language`: Egy karakterlánc, amely a észlelt nyelvi kódot.

      * `score`: Egy a lebegőpontos érték, amely az eredményben az vetett bizalmat. A pontszám nulla és között és alacsony pontszámmal egy kis abban, hogy jelzi.

    A `detectedLanguage` tulajdonság csak akkor szerepel a eredményobjektum, ha van szükség a nyelvi beállítások automatikus észlelése.

  * `translations`: Fordítási eredmény egy tömbjét. A tömb mérete megadva a cél nyelvek számának egyeznie kell a `to` lekérdezési paraméter. A tömb egyes elemei tartalmazza:

    * `to`: Egy karakterlánc, amely a célként megadott nyelvi nyelvi kódját.

    * `text`: Jogosultságot ad a fordítást egy karakterlánc.

    * `transliteration`: Jogosultságot ad a lefordított szöveget a megadott parancsfájl egy objektum a `toScript` paraméter.

      * `script`: Egy olyan karakterláncot kell a célként megadott parancsfájlt.   

      * `text`: Jogosultságot ad a lefordított szöveget a cél parancsfájlban egy karakterlánc.

    A `transliteration` Ha transliteration nem kerül sor, objektum nincs megadva.

    * `alignment`: Egy karakterlánc típusú tulajdonság nevű objektum `proj`, amely bemeneti szöveg lefordított szöveg. A igazítás csak információ esetén a kérelem paraméter `includeAlignment` van `true`. Igazítás a karakterlánc értéke a következő formátumban adja vissza a rendszer: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  A kettőspont elválasztja start end index, a kötőjel elválasztja a nyelveket és szóköz a szavakat. Egy word előfordulhat, hogy megfelel-e nulla, egy vagy több szavak más nyelven, és lehet, hogy a igazított szavak nem összefüggő. Nincs igazítás információ nem érhető el, a igazítás elem üres lesz. Lásd: [igazítás információkhoz](#obtain-alignment-information) például és korlátozásokat.

    * `sentLen`: Egy objektum mondat határok visszaadni a bemeneti és kimeneti szövegét.

      * `srcSentLen`: Egy a bemeneti szöveg a mondatok a hosszának jelző egész tömb. A tömb hossza mondatok számát, és az értékek a következők minden mondat hosszát.

      * `transSentLen`: Egy a lefordított szöveget a mondatok a hosszának jelző egész tömb. A tömb hossza mondatok számát, és az értékek a következők minden mondat hosszát.

    Mondat határok vannak, csak ha tartalmazza a kérés paraméter `includeSentenceLength` van `true`.

  * `sourceText`: Egy karakterlánc típusú tulajdonság nevű objektum `text`, ennek a bemeneti szöveg révén az alapértelmezett parancsfájlban a forrás nyelv. `sourceText` a tulajdonság jelenik meg, csak akkor, ha a bemeneti egy parancsfájlt, amely nem a szokásos parancsfájl a nyelven van kifejezve. Például, ha a bemeneti volt Latin betűs, majd írt arab `sourceText.text` volna az azonos arab szöveg alakítja át Arab parancsfájl.

Példa a JSON-válaszok szerepelnek a [példák](#examples) szakasz.

## <a name="response-status-codes"></a>Állapotkódok

Az alábbiakban a lehetséges HTTP-állapotkódok, egy kérelem ad vissza. 

<table width="100%">
  <th width="20%">Állapotkód</th>
  <th>Leírás</th>
  <tr>
    <td>200</td>
    <td>Siker.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>A lekérdezés-paraméterek egyike hiányzik vagy érvénytelen. A kérelemben szereplő paraméterek megkísérlése előtt javítsa ki.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Nem sikerült hitelesíteni a kérelmet. Ellenőrizze, hogy hitelesítő adatok megadott és érvényes.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A kérelem nem engedélyezett. Tekintse meg a részleteket hibaüzenetet. Ez gyakran azt jelenti, hogy egy próba-előfizetést megadott összes szabad fordítások használtak.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>A hívó túl sok kérelmet küld.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a probléma továbbra is fennáll, jelentse a: dátum és idő, a hiba, a válaszfejléc azonosítóját `X-RequestId`, és a kérelem fejlécében ügyfél-azonosítókra `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló ideiglenesen nem érhető el. Próbálkozzon újra a kéréssel. Ha a probléma továbbra is fennáll, jelentse a: dátum és idő, a hiba, a válaszfejléc azonosítóját `X-RequestId`, és a kérelem fejlécében ügyfél-azonosítókra `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Példák

### <a name="translate-a-single-input"></a>Egyetlen bevitel fordítása

Ez a példa bemutatja, hogyan egyetlen mondatban az angol az egyszerűsített kínai lefordítani.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

A választörzs mérete:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

A `translations` tömböt tartalmaz több elemmel rendelkezik, amely az egyetlen adat, a bemeneti szöveg fordítása biztosít.

### <a name="translate-a-single-input-with-language-auto-detection"></a>A nyelvi automatikus észlelés egy egyetlen bemeneti fordítása

Ez a példa bemutatja, hogyan egyetlen mondatban az angol az egyszerűsített kínai lefordítani. A kérelem nem határoz meg a beviteli nyelv. Automatikus észlelés a forrás nyelvét használja.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

A választörzs mérete:

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
A válasz hasonlít a válasz az előző példából. Mivel a kért nyelv automatikus észlelést, akkor a válasz a észlelhető a következőnél: a bemeneti szöveg nyelvének kapcsolatos adatokat is tartalmaz. 

### <a name="translate-with-transliteration"></a>A transliteration fordítása

Az előző példában meghosszabbíthatja transliteration hozzáadása. A következő kérés Latin betűs írt kínai fordítás kér.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

A választörzs mérete:

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

A fordítási eredménye most már tartalmaz egy `transliteration` tulajdonság, amely a lefordított szöveg Latin karaktereket használ.

### <a name="translate-multiple-pieces-of-text"></a>Több adatra szöveg fordítása

Több karakterláncok egyszerre fordítása egyszerűen karakterláncok megadása a kérés törzsében.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

---

A választörzs mérete:

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

### <a name="translate-to-multiple-languages"></a>Több nyelv fordítás

Ez a példa bemutatja, hogyan egy kérelem több nyelv ugyanazon bemeneti lefordítani.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

A választörzs mérete:

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

### <a name="handle-profanity"></a>Leíró Profanitás

Általában a fordító szolgáltatás megőrzi az, hogy megtalálható-e a fordítás a forrás Profanitás. Profanitás mennyire és a környezetben, amelynek eredményeképpen a szavakat profán eltérő kulturális környezetek, és ennek eredményeképpen a célként megadott nyelven Profanitás fokának előfordulhat, hogy bővíteni vagy csökkenteni.

Ha el szeretné kerülni, a fordítás, függetlenül a forrás szöveg Profanitás jelenléte Profanitás első a Profanitás szűrés lehetőséget használhatja. A beállítás lehetővé teszi, hogy válassza ki, hogy törölni, hogy profanities megjelölendő megfelelő címkékkel (felkínálva a lehetőséget a saját utófeldolgozás hozzáadása), vagy azt szeretné, hogy nem történt művelet Profanitás megjelenítéséhez. A lehetséges értékek a `ProfanityAction` vannak `Deleted`, `Marked` és `NoAction` (alapértelmezett).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Műveletek</th>
  <tr>
    <td>`NoAction`</td>
    <td>Ez az alapértelmezett viselkedése. Profanitás cél forrásból fogja továbbítani.<br/><br/>
    **Példa forrás (japán)**: 彼はジャッカスです。<br/>
    **Példa fordítási (angol)**: egy Pápaszemes.
    </td>
  </tr>
  <tr>
    <td>`Deleted`</td>
    <td>Profán szavak eltávolítja a kimeneti helyettesítő nélkül.<br/><br/>
    **Példa forrás (japán)**: 彼はジャッカスです。<br/>
    **Példa fordítási (angol)**: van egy.
    </td>
  </tr>
  <tr>
    <td>`Marked`</td>
    <td>Profán helyébe egy jelölő a kimenetben. A jelölő függ a `ProfanityMarker` paraméter.<br/><br/>
A `ProfanityMarker=Asterisk`, profán szavak váltják fel `***`:<br/>
    **Példa forrás (japán)**: 彼はジャッカスです。<br/>
    **Példa fordítási (angol)**: van egy \* \* \*.<br/><br/>
A `ProfanityMarker=Tag`, profán szavak tette XML-címkék &lt;Profanitás&gt; és &lt;/profanity&gt;:<br/>
    **Példa forrás (japán)**: 彼はジャッカスです。<br/>
    **Példa fordítási (angol)**: van egy &lt;Profanitás&gt;Pápaszemes&lt;/profanity&gt;.
  </tr>
</table> 

Példa:

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

Ez visszaad:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Vesse össze:

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

A legutóbbi kérelem adja vissza:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Tartalom található kód fordítása, és döntse el, mi lefordították

Esetében gyakori, lefordítani a tartalmat, beleértve a jelölés során, például HTML-lapon tartalom vagy az XML-dokumentum tartalmát. Tartalmazza a következő lekérdezésparaméter `textType=html` címkékkel tartalom fordítása során. Ezenkívül célszerű néha szeretné kizárni az adott tartalmat a fordítás. Az attribútum használható `class=notranslate` adhatja meg a tartalom maradjon az eredeti nyelven. A következő példában az első belüli tartalom `div` elem nem lesz fordítva, miközben a tartalmat, a második `div` elem fordítja.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Íme egy minta kérelem mutatja be.

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

### <a name="obtain-alignment-information"></a>Igazítás információkhoz

Igazítás információk fogadását, adja meg a `includeAlignment=true` a lekérdezési karakterlánc.

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

Igazítás információk kezdődik-e `0:2-0:1`, ami azt jelenti, hogy a forrás szöveg első három karaktere (`The`) leképezés a lefordított szöveg első két karaktereit (`La`).

Vegye figyelembe a következő korlátozások vonatkoznak:

* Igazítás csak vissza a nyelvi párok egy része számára:
  - az olyan egyéb nyelvek; angol
  - angol, egyszerűsített kínai, hagyományos kínai, és lett angol; kivételével bármely más nyelven történő
  - a japán, koreai, vagy a japán koreai.
* Igazítás Ön nem kap, ha a mondatok előre összeállított fordítását. Példa egy előre összeállított fordítás: "Ez egy tesztművelet", "Gyönyörű meg" és egyéb nagy sűrűségű mondatokat.

### <a name="obtain-sentence-boundaries"></a>Szerezze be néhány mondatot határok

Lefordított és a forrás szöveg mondat hossza információt kap, adja meg a `includeSentenceLength=true` a lekérdezési karakterlánc.

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

### <a name="translate-with-dynamic-dictionary"></a>Dinamikus adatkönyvtárhoz fordítása

Ha már ismeri a fordítás egy szót vagy kifejezést alkalmazni kívánja, a jelölés belül a kérelmet, megadhat. A dinamikus szótár csak nem biztonságos főnevek például a megfelelő és a termék nevét.

Adja meg a kód a következő szintaxist használja.

``` 
<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>
```

Például fontolja meg, az angol mondat "a word wordomatic dictionary-bejegyzés." A word megőrzése érdekében _wordomatic_ a fordítás a kérelem küldése:

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

Ez a szolgáltatás működik együtt a `textType=text` vagy `textType=html`. A szolgáltatás takarékosan. A megfelelő és sokkal hatékonyabb testreszabásának fordítás módja egyéni fordító használatával. Egyéni fordító környezetben és statisztikai valószínűség teljes használata révén. Ha van, vagy is biztosít, amely a munkahelyi vagy kifejezés környezetben betanítási adatok létrehozásához, mennyi jobb eredményeket kap. [További tudnivalók az egyéni fordító](../customization.md).
 





