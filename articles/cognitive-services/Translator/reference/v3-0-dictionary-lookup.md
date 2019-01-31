---
title: Translator Text API szótár keresési metódus
titlesuffix: Azure Cognitive Services
description: A Translator Text API szótár keresési módszert használja.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: b6178c4e9c197539359058347b2409210d976569
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458924"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Translator Text API 3.0: Keresés a szótárban

Egy szót, és bármilyen kifejezések kis számú felkínálásával biztosít. Minden egyes fordítási rendelkezik egy rész-az-beszéd- és back-fordítások listáját. A biztonsági fordításokat engedélyezi a felhasználókat a környezetben a fordítás megértéséhez. A [szótár példa](./v3-0-dictionary-examples.md) művelet lehetővé teszi, hogy további részletes elemzési, lásd: Ebben a példában minden egyes fordítási pár képességet.

## <a name="request-url"></a>Kérés URL-címe

Küldjön egy `POST` kérelmet:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
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
    <td>*Kötelező paraméter*.<br/>Meghatározza a bemeneti szöveg nyelvét. A Forrásnyelv egyikének kell lennie a [támogatott nyelvek](./v3-0-languages.md) szerepel a `dictionary` hatókör.</td>
  </tr>
  <tr>
    <td>erre:</td>
    <td>*Kötelező paraméter*.<br/>Meghatározza a kimeneti szöveg nyelvét. A Célnyelv egyikének kell lennie a [támogatott nyelvek](./v3-0-languages.md) szerepel a `dictionary` hatókör.</td>
  </tr>
</table>

Kérelemfejlécek a következők:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>_Egy engedélyezési_<br/>_header_</td>
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

## <a name="request-body"></a>A kérés törzse

A kérelem törzsében egy JSON-tömböt. Egyes tömbelemeken nevű karakterlánc tulajdonsággal rendelkező JSON-objektum `Text`, amelyek keresési kifejezés jelöli.

```json
[
    {"Text":"fly"}
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 10 elemet is rendelkezhet.
* Egy tömbelem szöveg értéke nem lehet 100 karakternél, beleértve a szóközöket.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömböt az egyes sztringek a bemeneti számtömbből egy eredmény. Egy objektumra a következő tulajdonságokat tartalmazza:

  * `normalizedSource`: Egy karakterlánc, így a forrás kifejezés normalizált formájában. Például ha a kérés "JÁNOS", a normalizált képernyő lesz "János". Ez a mező tartalma válik a bemeneti [keresési példák](./v3-0-dictionary-examples.md).
    
  * `displaySource`: Egy karakterlánc leginkább az űrlapon jogosultságot ad a forrás kifejezés olyan végfelhasználói megjelenített. Például ha a bemeneti "JÁNOS", a megjelenítendő képernyő nevét szokásos helyesírás-fogja tartalmazni: "János". 

  * `translations`: A forrás időszakra fordítások listája. A lista minden eleme egy olyan objektum, a következő tulajdonságokkal:

    * `normalizedTarget`: Egy karakterlánc, így a normalizált kifejezés formájában a célként megadott nyelven. Ezt az értéket kell használni bemeneteként [keresési példák](./v3-0-dictionary-examples.md).

    * `displayTarget`: Egy karakterlánc jogosultságot ad a kifejezés a Célnyelv és a egy űrlap leginkább az olyan végfelhasználói megjelenített. Általában ez csak különbözni fog a `normalizedTarget` nagybetűs tekintetében. Ha például a tulajdonneveknél "Juan" fog rendelkezni, `normalizedTarget = "juan"` és `displayTarget = "Juan"`.

    * `posTag`: Ez a kifejezés társít egy része a beszéd, a címke karakterlánc.

        | Címke neve | Leírás  |
        |----------|--------------|
        | BEÁLLÍTÁS      | Melléknevek   |
        | ADV      | Módosítószavak      |
        | CONJ     | Kötőszavak |
        | DET      | Determiners  |
        | MODÁLIS    | Műveletek        |
        | FŐNÉV     | Főneveket        |
        | ELŐKÉSZÍTŐ     | Elöljárószavakat |
        | PRON     | Névmások     |
        | VERB     | Műveletek        |
        | EGYÉB    | Egyéb        |

        Egy megvalósítási Megjegyzés ezekkel a címkékkel is határozza meg. rész – az-speech címkéket az angol nyelvű oldalon és a leggyakoribb címke majd véve az egyes forrás/cél párhoz. Tehát ha emberek gyakran lefordítja spanyol szó különböző rész-, beszéd címkére angol nyelven, címkék előfordulhat, hogy végül van (garanciát a spanyol szó).

    * `confidence`: 0,0 és 1,0 képviselő "magabiztosan" közötti értéket (vagy akár több pontosan, "valószínűség a betanítási adatok") a fordítási pár. Megbízhatósági pontszámokat több forrás Word összegét is, vagy előfordulhat, hogy nem összeg 1.0. 

    * `prefixWord`: Jogosultságot ad a word, a fordítás előtagjaként megjelenítendő karakterlánc. Ez jelenleg az gendered determiner a főnevek rendelkezik gendered determiners nyelveken. Például az előtag, a spanyol szó "mosca", "la", mivel a "mosca" nőnemű főnév spanyol nyelven. Ez a tulajdonság csak a függő a fordítás, és nem az a forrás. Ha nincs előtag, az üres karakterlánc lesz.
    
    * `backTranslations`: A cél "vissza fordítások" listáját. Például a forrás, amely lefordítja a cél a szavakat. A lista tartalmazza a lekért forrás szó garantáltan (pl., ha a forrás word folyamatban kulcskeresési "repülési", akkor azt garantálja, hogy a "repülési" lesz a `backTranslations` lista). Azonban nem garantált, hogy az első helyen kell, és gyakran nem lesz. Egyes elemeinek a `backTranslations` lista egy olyan objektum leírtak szerint a következő tulajdonságokkal:

        * `normalizedText`: Egy karakterlánc, így a forrás kifejezés, amely a cél vissza fordítási normalizált formájában. Ezt az értéket kell használni bemeneteként [keresési példák](./v3-0-dictionary-examples.md).        

        * `displayText`: Egy karakterlánc, így a forrás kifejezés, amely a cél egy űrlapon, ajánlott biztonsági fordítási olyan végfelhasználói megjelenítése.

        * `numExamples`: Egy példa, amelyek érhetők el a fordítási pár számát jelző egész számot. Tényleges példák külön hívásával kell beolvasni [keresési példák](./v3-0-dictionary-examples.md). A szám többnyire célja, hogy egy UX megjelenítéshez megkönnyítése Felhasználói felület például hivatkozás hozzáadása szövegmezőhöz a biztonsági fordítás Ha példák száma csak nullánál nagyobb és megjelenítése a biztonsági fordítás pedig egyszerű szövegként, esetén nem nincs példákat. Vegye figyelembe, hogy a példák tényleges száma hívása által visszaadott [keresési példák](./v3-0-dictionary-examples.md) lehet, hogy kevesebb mint `numExamples`, mert a további szűréshez menet közben is távolítsa el a "rossz" példák alkalmazhatók.
        
        * `frequencyCount`: Az adatok a fordítási pár gyakorisága jelölő egész szám. Ez a mező fő célja, hogy a vissza-fordítások rendezni, így a leggyakoribb használati első azt jelenti, hogy egy felhasználói felületet kínálnak.

    > [!NOTE]
    > Ha a kifejezés kikeresi mentése folyamatban van a szótár nem létezik, a válasz-e a 200 (OK), de a `translations` listája üres lista lesz.

## <a name="examples"></a>Példák

Ez a példa bemutatja, hogyan felkínálásával spanyol nyelven az angol nyelvű kifejezést talált `fly` .

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

---

A válasz törzsében (rövidítése az átláthatóság érdekében) a következő:

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

Ez a példa bemutatja, mi történik, ha az előfizetési időszak alatt kulcskeresési a érvényes szótár pár nem létezik.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

---

Mivel az előfizetési időszak nem található a szótárban, a válasz törzse tartalmazza-e egy üres `translations` listája.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
