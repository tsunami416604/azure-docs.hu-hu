---
title: Fordító szöveg API fordítási módszer
titleSuffix: Azure Cognitive Services
description: Ismerje meg az Azure Cognitive Services Translator Text API Translate metódusának paramétereit, fejléceit és törzsüzeneteit a szöveg fordításához.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: 1821623fbe2a22234af649934ac06e72897a19cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052397"
---
# <a name="translator-text-api-30-translate"></a>Fordító szöveg API 3.0: Fordítás

Szöveg fordítása.

## <a name="request-url"></a>Kérés URL-címe

Kérés `POST` küldése a következő knek:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Kérelem paraméterei

A lekérdezési karakterláncon átadott kérelemparaméterek a következők:

### <a name="required-parameters"></a>Kötelező paraméterek

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>api-verzió</td>
    <td><em>Kötelező paraméter</em>.<br/>Az ügyfél által kért API verziója. Az értéknek kell lennie. <code>3.0</code></td>
  </tr>
  <tr>
    <td>erre:</td>
    <td><em>Kötelező paraméter</em>.<br/>A kimeneti szöveg nyelvét adja meg. A célnyelvnek a <a href="./v3-0-languages.md">supported languages</a> <code>translation</code> hatókörben szereplő támogatott nyelvek egyikének kell lennie. Például, <code>to=de</code> használja a fordítást a német.<br/>A lekérdezési karakterlánc ban lévő paraméter ismétlésével egyszerre több nyelvre is lefordíthatók. Például németre <code>to=de&to=it</code> és olaszra fordíthatók.</td>
  </tr>
</table>

### <a name="optional-parameters"></a>Választható paraméterek

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>honnan</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Megadja a bemeneti szöveg nyelvét. A <code>translation</code> hatókör <a href="./v3-0-languages.md">használatával</a> megkeresheti, hogy mely nyelvekből lehet lefordítani. Ha <code>from</code> a paraméter nincs megadva, a program automatikus nyelvfelismerést alkalmaz a forrásnyelv meghatározásához. <br/><br/>A dinamikus <code>from</code> szótári szolgáltatás használatakor az automatikus észlelés helyett a paramétert kell <a href="https://docs.microsoft.com/azure/cognitive-services/translator/dynamic-dictionary">használnia.</a></td>
  </tr>  
  <tr>
    <td>textType (szövegtípusa)</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Azt határozza meg, hogy a lefordított szöveg egyszerű vagy HTML-szöveg-e. Minden HTML kell egy jól formázott, teljes elem. A lehetséges <code>plain</code> értékek a <code>html</code>következők: (alapértelmezett) vagy .</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Nem kötelező paraméter</em>.<br/>A fordítás kategóriáját (tartományát) meghatározó karakterlánc. Ez a paraméter az egyéni fordítóval készült testreszabott rendszer fordításának <a href="../customization.md">lekérni.</a> Adja hozzá a kategóriaazonosítót az egyéni fordító <a href="https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">projekt részleteiből</a> ehhez a paraméterhez az üzembe helyezett testreszabott rendszer használatához. Az alapértelmezett <code>general</code>érték a következő: .</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Megadja, hogyan kell kezelni a káromkodásokat a fordításokban. A lehetséges <code>NoAction</code> értékek a <code>Marked</code> <code>Deleted</code>következők: (alapértelmezett) vagy . A káromkodás kezelésének módjainak megértéséhez olvassa el <a href="#handle-profanity">a Káromkodás kezelése</a>.</td>
  </tr>
  <tr>
    <td>káromkodásMarker</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Itt adható meg, hogy a káromkodások hogyan legyenek megjelölve a fordításokban. A lehetséges <code>Asterisk</code> értékek a <code>Tag</code>következők: (alapértelmezett) vagy . A káromkodás kezelésének módjainak megértéséhez olvassa el <a href="#handle-profanity">a Káromkodás kezelése</a>.</td>
  </tr>
  <tr>
    <td>isAlignment</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Itt adható meg, hogy a program a forrásszövegtől a lefordított szövegig mellékelje-e az igazítási vetületet. A lehetséges <code>true</code> értékek <code>false</code> a következők: vagy (alapértelmezett). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Itt adható meg, hogy a szöveg és a lefordított szöveg szöveghez szerepeljen-e mondathatárok. A lehetséges <code>true</code> értékek <code>false</code> a következők: vagy (alapértelmezett).</td>
  </tr>
  <tr>
    <td>javasoltFrom</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Tartalék nyelvet ad meg, ha a bemeneti szöveg nyelve nem azonosítható. A nyelv automatikus észlelése a <code>from</code> paraméter kihagyása esetén történik. Ha az észlelés sikertelen, a rendszer a <code>suggestedFrom</code> nyelvet veszi alapul.</td>
  </tr>
  <tr>
    <td>inScript</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Megadja a bemeneti szöveg parancsfájlját.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Megadja a lefordított szöveg parancsfájlját.</td>
  </tr>
  <tr>
    <td>allowFallback (engedélyezés)</td>
    <td><em>Nem kötelező paraméter</em>.<br/>Itt adható meg, hogy a szolgáltatás akkor is visszaléphet-e egy általános rendszerre, ha nem létezik egyéni rendszer. A lehetséges <code>true</code> értékek a <code>false</code>következők: (alapértelmezett) vagy .<br/><br/><code>allowFallback=false</code>meghatározza, hogy a fordítás csak a <code>category</code> kérelemben megadott rendszereket használjon. Ha az X nyelv Y nyelvre történő fordítása e pivot nyelven történő láncolást igényel, akkor a lánc összes rendszerének (X->E és E->Y) egyéninek kell lennie, és azonos kategóriával kell rendelkeznie. Ha nem található rendszer az adott kategóriához, a kérelem 400-as állapotkódot ad vissza. <code>allowFallback=true</code>megadja, hogy a szolgáltatás akkor léphet vissza egy általános rendszerre, ha nem létezik egyéni rendszer.
</td>
  </tr>
</table> 

A kérelemfejlécek a következőket tartalmazzák:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>Hitelesítési fejléc(ek)</td>
    <td><em>Kötelező kérelemfejléc</em>.<br/>Lásd: <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">A hitelesítéshez rendelkezésre álló lehetőségek.</a></td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Kötelező kérelemfejléc</em>.<br/>Megadja a hasznos adat tartalom típusát.<br/> Az elfogadott <code>application/json; charset=UTF-8</code>érték a .</td>
  </tr>
  <tr>
    <td>Tartalom hossza</td>
    <td><em>Kötelező kérelemfejléc</em>.<br/>A kérelem törzsének hossza.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Nem kötelező</em>.<br/>Az ügyfél által létrehozott GUID a kérelem egyedi azonosítására. Ezt a fejlécet kihagyhatja, ha a nyomkövetési azonosítót <code>ClientTraceId</code>a lekérdezési karakterláncba a .</td>
  </tr>
</table> 

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömbelem egy JSON-objektum, `Text`amelynek neve karakterlánc-tulajdonság, amely a lefordítandó karakterláncot jelöli.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Az alábbi korlátozások érvényesek:

* A tömb legbőlegelhető 100 elemet tartalmazhat.
* A kérelemben szereplő teljes szöveg nem haladhatja meg az 5000 karaktert szóközökkel együtt.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb, amely a bemeneti tömb minden egyes karakterláncához egy-egy eredményt ad. Az eredményobjektum a következő tulajdonságokat tartalmazza:

  * `detectedLanguage`: Az észlelt nyelvet a következő tulajdonságokon keresztül leíró objektum:

      * `language`: Az észlelt nyelv kódját jelölő karakterlánc.

      * `score`: Az eredmény megbízhatóságát jelző lebegőérték. A pontszám nulla és egy között van, és az alacsony pontszám alacsony magabiztosságot jelez.

    A `detectedLanguage` tulajdonság csak akkor jelenik meg az eredményobjektumban, ha automatikus nyelvi észlelést kér.

  * `translations`: Fordítási eredmények tömbje. A tömb mérete megegyezik a `to` lekérdezési paraméteren megadott célnyelvek számával. A tömb minden eleme a következőket tartalmazza:

    * `to`: A célnyelv nyelvkódját jelölő karakterlánc.

    * `text`: A lefordított szöveget adó karakterlánc.

    * `transliteration`: Olyan objektum, amely a `toScript` paraméter által megadott parancsfájlban a lefordított szöveget adja meg.

      * `script`: A célparancsfájlt meghatározó karakterlánc.   

      * `text`: A lefordított szöveget a célparancsfájlban ad meg.

    Az `transliteration` objektum nem szerepel, ha az átírás nem történik meg.

    * `alignment`: Egyetlen karakterlánctulajdonsággal rendelkező `proj`objektum, amely a szöveget lefordított szöveggé képezi. Az igazítási információ csak akkor `includeAlignment` `true`szolgál meg, ha a kérelem paramétere . Az igazítás a következő formátumú karakterlánc-értékként jelenik meg: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  A kettőspont elválasztja a kezdő és záró indexet, a kötőjel választja el a nyelveket, a szóköz pedig elválasztja a szavakat. Az egyik szó a másik nyelv ben a nullához, egy vagy több szóhoz igazodhat, és az igazított szavak nem lehetnek összefüggőek. Ha nem áll rendelkezésre igazítási információ, a nyomvonalelem üres lesz. Lásd: [Igazítási információk beszerzése](#obtain-alignment-information) egy példához és korlátozáshoz.

    * `sentLen`: A bemeneti és kimeneti szövegekben mondathatárokat visszaadó objektum.

      * `srcSentLen`: Egész tömb, amely a szövegben lévő mondatok hosszát jelöli. A tömb hossza a mondatok száma, az értékek pedig az egyes mondatok hossza.

      * `transSentLen`: Egész tömb, amely a lefordított szöveg mondatainak hosszát jelöli. A tömb hossza a mondatok száma, az értékek pedig az egyes mondatok hossza.

    A mondathatárok csak akkor szerepelnek, ha a kérelem paramétere `includeSentenceLength` . `true`

  * `sourceText`: Egyetlen karakterlánc tulajdonsággal `text`rendelkező objektum, amely a forrásnyelv alapértelmezett parancsfájljában adja meg a bemeneti szöveget. `sourceText`tulajdonság csak akkor van jelen, ha a bemenet olyan parancsfájlban van kifejezve, amely nem a nyelv szokásos parancsfájlja. Ha például a bevitel latin írással `sourceText.text` íródott arab, akkor ugyanaz az arab szöveg lenne, amelyet arab írássá konvertálnak.

A JSON-válaszok példa a [példák](#examples) szakaszban találhatók.

## <a name="response-headers"></a>Válaszfejlécek

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
    <tr>
    <td>X-RequestId azonosító</td>
    <td>A szolgáltatás által a kérelem azonosítására létrehozott érték. Hibaelhárítási célokra használják.</td>
  </tr>
  <tr>
    <td>X-MT rendszer</td>
    <td>Megadja azt a rendszertípust, amelyet a fordításhoz kért minden egyes "a" nyelvfordításhoz használt. Az érték a karakterláncok vesszővel tagolt listája. Minden karakterlánc egy típust jelöl:<br/><ul><li>Egyéni – A kérelem tartalmaz egy egyéni rendszert, és legalább egy egyéni rendszert használtak a fordítás során.</li><li>Csapat - Minden más kérés</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Válasz állapotkódjai

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők. 

<table width="100%">
  <th width="20%">Állapotkód</th>
  <th>Leírás</th>
  <tr>
    <td>200</td>
    <td>Siker.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>A lekérdezési paraméterek egyike hiányzik vagy érvénytelen. Az újrapróbálkozás előtt javítsa ki a kérelem paramétereit.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>A kérés t nem lehetett hitelesíteni. Ellenőrizze, hogy a hitelesítő adatok meg vannak-e adva és érvényesek-e.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A kérés nem engedélyezett. Ellenőrizze a részleteket hibaüzenetet. Ez gyakran azt jelzi, hogy a próba-előfizetéssel rendelkező összes ingyenes fordítást felhasználták.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>A kérés nem teljesíthető, mert egy erőforrás hiányzik. Ellenőrizze a részleteket hibaüzenetet. Egyéni <code>category</code>használata esetén ez gyakran azt jelzi, hogy az egyéni fordítási rendszer még nem áll rendelkezésre a kérések kiszolgálására. A kérelmet várakozási idő után (pl. 1 perc) újra meg kell próbálni.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>A kiszolgáló elutasította a kérést, mert az ügyfél túllépte a kérelemkorlátot.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse azt a következőkkel: <code>X-RequestId</code>a hiba dátuma <code>X-ClientTraceId</code>és időpontja, a válaszfejlécből származó kérelemazonosító és az ügyfélazonosító a kérelemfejlécből.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló átmenetileg nem érhető el. Próbálkozzon újra a kéréssel. Ha a hiba továbbra is fennáll, jelentse azt a következőkkel: <code>X-RequestId</code>a hiba dátuma <code>X-ClientTraceId</code>és időpontja, a válaszfejlécből származó kérelemazonosító és az ügyfélazonosító a kérelemfejlécből.</td>
  </tr>
</table> 

Ha hiba történik, a kérelem json-hibaválaszt is ad vissza. A hibakód egy 6 jegyű szám, amely a 3 jegyű HTTP-állapotkódot, majd egy 3 jegyű számot követ a hiba további kategorizálása érdekében. A gyakori hibakódok a [v3 Translator Text API referenciaoldalán](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)találhatók. 

## <a name="examples"></a>Példák

### <a name="translate-a-single-input"></a>Egyetlen bevitel fordítása

Ez a példa bemutatja, hogyan lehet lefordítani egy mondatot angolról egyszerűsített kínaira.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

A válaszszerv:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

A `translations` tömb tartalmaz egy elemet, amely biztosítja a fordítást az egyetlen szövegdarab a bemeneti.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Egyetlen bevitel fordítása a nyelvi automatikus észleléssel

Ez a példa bemutatja, hogyan lehet lefordítani egy mondatot angolról egyszerűsített kínaira. A kérelem nem adja meg a beviteli nyelvet. Ehelyett a forrásnyelv automatikus felismerése használatos.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

A válaszszerv:

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
A válasz hasonló az előző példa válaszához. Mivel a rendszer automatikusnyelvi észlelést kért, a válasz a bemeneti szöveg hez észlelt nyelvre vonatkozó információkat is tartalmazza. 

### <a name="translate-with-transliteration"></a>Fordítás átírással

Terjeszjük ki az előző példát átírás hozzáadásával. A következő kérelem egy latin nyelvű kínai fordítást kér.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

A válaszszerv:

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

A fordítás eredménye `transliteration` most már tartalmaz egy tulajdonságot, amely latin betűkkel adja meg a lefordított szöveget.

### <a name="translate-multiple-pieces-of-text"></a>Több szövegfordítása

Több karakterlánc egyszerre történő fordítása egyszerűen csak karakterláncok tömbjének megadása a kérelem törzsében.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

A válaszszerv:

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

### <a name="translate-to-multiple-languages"></a>Fordítás több nyelvre

Ez a példa bemutatja, hogyan lehet lefordítani ugyanazt a bevitelt több nyelvre egy kérelemben.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

A válaszszerv:

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

### <a name="handle-profanity"></a>Káromkodás kezelése

Normális esetben a fordító szolgáltatás megtartja káromkodás, amely jelen van a forrás a fordításban. A káromkodás mértéke és a szavakat a kultúrák között eltérő kontextus, és ennek következtében a célnyelven a káromkodás mértéke felerősödhet vagy csökkenhet.

Ha el szeretné kerülni, hogy káromkodás tanusítsa a fordítást, függetlenül attól, hogy a forrásszövegben jelen van-e a káromkodás, használhatja a káromkodásszűrési lehetőséget. A beállítás sal megadhatja, hogy a káromkodást törölni szeretné-e, hogy a káromkodásokat megfelelő címkékkel szeretné-e megjelölni (lehetőséget adva a saját utófeldolgozás hozzáadására), vagy nem szeretne műveletet végrehajtani. Az elfogadott `ProfanityAction` értékek `Deleted` `Marked` a `NoAction` és a (default).

<table width="100%">
  <th width="20%">ProfanityAction (Káromkodás– Művelet</th>
  <th>Műveletek</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Ez az alapértelmezett viselkedés. A káromkodás a forrástól a célig tart.<br/><br/>
    <strong>Példa forrás (japán)</strong>:<br/>
    <strong>Példa Fordítás (angol)</strong>: Ő egy seggfej.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>A profán szavak csere nélkül törlődnek a kimenetből.<br/><br/>
    <strong>Példa forrás (japán)</strong>:<br/>
    <strong>Példa Fordítás (angol)</strong>: Ő egy.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>A profán szavakat a kimenetben egy jelölő váltja fel. A jelölő <code>ProfanityMarker</code> a paramétertől függ.<br/><br/>
A <code>ProfanityMarker=Asterisk</code>profán szavak helyébe <code>***</code>a következő lép:<br/>
    <strong>Példa forrás (japán)</strong>:<br/>
    <strong>Példa Fordítás (angol)</strong>: \* \* \*Ő egy .<br/><br/>
A <code>ProfanityMarker=Tag</code>profán szavakat XML-címkék &lt;veszik&gt; &lt;körül, profanitás és /profanitás:&gt;<br/>
    <strong>Példa forrás (japán)</strong>:<br/>
    <strong>Példa Fordítás (angol)</strong>: &lt;Ő&gt;egy káromkodás&lt;jackass&gt;/ káromkodás .
  </tr>
</table> 

Példa:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```
Ez a következőt adja vissza:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Összehasonlítás a következőkkel:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

Az utolsó kérés:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Tartalom fordítása jelöléssel, és a fordítás eldöntése

Gyakori, hogy olyan tartalmat fordít le, amely olyan megtekintéseket tartalmaz, mint például egy HTML-lapból származó tartalom vagy xml-dokumentumból származó tartalom. Lekérdezési `textType=html` paraméter belefoglalása címkékkel rendelkező tartalom fordításakor. Ezenkívül néha hasznos lehet kizárni bizonyos tartalmakat a fordításból. Az attribútum segítségével `class=notranslate` megadhatja azokat a tartalmakat, amelyeknek az eredeti nyelvükön kell maradniuk. A következő példában az első `div` elemen belüli tartalom nem lesz lefordítva, míg a második `div` elem tartalma lelesz fordítva.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Itt van egy példa kérelmet illusztrálni.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

A válasz:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Igazítási információk beszerzése

Az igazítási információk `includeAlignment=true` fogadásához adja meg a lekérdezési karakterláncon.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation.'}]"
```

A válasz:

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

Az igazítási `0:2-0:1`információ a ( programmal kezdődik, ami`The`azt jelenti, hogy a forrásszöveg`La`( ) első három karaktere a lefordított szöveg első két karakterére ( ) van leképezve.

#### <a name="limitations"></a>Korlátozások
Az összehangolási információk megszerzése egy kísérleti funkció, amelyet engedélyeztünk a kutatás és a lehetséges kifejezésleképezésekkel kapcsolatos tapasztalatok prototípusára. Dönthetünk úgy, hogy a jövőben nem támogatjuk ezt tovább. Íme néhány említésre méltó korlátozás, ahol a nyomvonalak nem támogatottak:

* Az igazítás nem érhető el HTML formátumú szöveghez, azaz textType=html
* A program csak a nyelvpárok egy részhalmazához adja vissza az igazítást:
  - az angoltól bármely más nyelvig;
  - bármely más nyelvről angolra, kivéve a kínai egyszerűsített, a hagyományos kínait és a lettet angolra;
  - japánról koreaira vagy koreairól japánra.
* Nem kap igazítást, ha a mondat konzervfordítás. Példa a konzerv fordítás "Ez egy teszt", "Szeretlek" és más nagy frekvenciájú mondatokat.
* Az igazítás nem érhető el, ha az itt leírt fordítási módszerek bármelyikét [alkalmazza.](../prevent-translation.md)

### <a name="obtain-sentence-boundaries"></a>Mondathatárok beszerzése

Ha információt szeretne kapni a szövegés a lefordított szöveg mondathosszáról, adja meg `includeSentenceLength=true` a lekérdezési karakterláncban.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

A válasz:

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

### <a name="translate-with-dynamic-dictionary"></a>Fordítás dinamikus szótárral

Ha már ismeri azt a fordítást, amelyet alkalmazni szeretne egy szóra vagy kifejezésre, akkor a kérelemben megjelölésként is megadhatod. A dinamikus szótár csak összetett főnevek, például a megfelelő nevek és terméknevek esetén biztonságos.

A szállítójelet a következő szintaxishasználja.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Vegyük például a "A szószöveg szó szót szótárbejegyzésként" című angol mondatot. A _wordomatic_ szó fordításban való megőrzéséhez küldje el a kérést:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Az eredmény:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Ez a funkció ugyanúgy működik a programmal `textType=text` vagy a programmal. `textType=html` A funkciót takarékosan kell használni. A fordítás testreszabásának megfelelő és sokkal jobb módja az egyéni fordító használata. Az egyéni fordító teljes mértékben kihasználja a környezetet és a statisztikai valószínűségeket. Ha rendelkezik vagy megengedheti magának, hogy olyan betanítási adatokat hozzon létre, amelyek a munkáját vagy kifejezését kontextusban jelenítik meg, sokkal jobb eredményeket érhet el. [További információ az egyéni fordítóról.](../customization.md)
