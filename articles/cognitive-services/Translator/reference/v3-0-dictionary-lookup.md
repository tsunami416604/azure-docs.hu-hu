---
title: A Microsoft Translator szöveg API szótár keresési metódus |} Microsoft Docs
description: A Microsoft Translator szöveg API szótár keresési módszer használatát.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 31435fcfca61517bfc72d534e911a1dcadbee52b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349267"
---
# <a name="text-api-30-dictionary-lookup"></a>Szöveg API 3.0: Szótár keresési

Alternatív fordítások biztosít a word és idiomatikus kifejezések kis számú. Minden egyes fordítási egy része-az-beszéd átalakítás és egy listában vissza rendelkezik. A biztonsági fordításának engedélyezése a felhasználó tudni, hogy a fordítás a környezetben. A [szótár példa](.\v3-0-dictionary-examples.md) művelet lehetővé teszi, hogy további megtekintheti az egyes lásd: a példában minden egyes fordítási pár.

## <a name="request-url"></a>Lekérdezés URL-címe

Küldjön egy `POST` elküldeni a kérelmet:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
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
    <td>*A paraméter kötelező*.<br/>Meghatározza a bemeneti szöveg nyelvét. Az adatforrás nyelvi közül kell a [támogatott nyelv](.\v3-0-languages.md) szerepel a `dictionary` hatókör.</td>
  </tr>
  <tr>
    <td>erre:</td>
    <td>*A paraméter kötelező*.<br/>Meghatározza a kimeneti szöveg. A megadott nyelv közül kell a [támogatott nyelv](.\v3-0-languages.md) szerepel a `dictionary` hatókör.</td>
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

A kérelem törzse egy JSON-tömb. Minden tömbelem egy JSON-objektum nevű karakterlánc tulajdonsággal `Text`, amely keresési kifejezés képviseli.

```json
[
    {"Text":"fly"}
]
```

A következő korlátozások vonatkoznak:

* A tömb lehet legfeljebb 10 elemet.
* A szöveges érték, egy tömbelem legfeljebb 100 karakter lehet, beleértve a szóközöket.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb minden egyes karakterláncot kell megadnia a bemeneti tömb egy eredménnyel:. Egy eredményobjektum tartalmazza a következő tulajdonságokkal:

  * `normalizedSource`: Jogosultságot ad a forrás kifejezés normalizált formája egy karakterlánc. Például ha a kérelem "JOHN", a normalizált formája lesz "john". Ez a mező tartalmának válik a bemeneti [keresési példák](.\v3-0-dictionary-examples.md).
    
  * `displaySource`: Végfelhasználói megjelenítési megfelelő a legjobb űrlapon jogosultságot ad a forrás kifejezés karakterlánc. Például, ha a bemeneti érték a "JOHN", az űrlap megjelenítése változik meg a szokásos helyesen adta-e a név: "John". 

  * `translations`: A forrás időszakra fordítások listája. A lista minden eleme egy objektum a következő tulajdonságokkal:

    * `normalizedTarget`: Egy karakterlánc, ez a kifejezés normalizált formája megadva a cél nyelven. Ezt az értéket kell használni a bemeneti [keresési példák](.\v3-0-dictionary-examples.md).

    * `displayTarget`: A karakterlánc megadva a kifejezés a célként megadott nyelven, valamint egy űrlapon legjobban megfelelő végfelhasználói megjelenítése. Általában, a rendszer csak abban tér el a `normalizedTarget` nagybetűs tekintetében. Például egy megfelelő főnév like "Juan" lesz `normalizedTarget = "juan"` és `displayTarget = "Juan"`.

    * `posTag`: Ez a kifejezés társít egy része a beszéd címke egy karakterlánc.

        | A címke neve | Leírás  |
        |----------|--------------|
        | BEÁLLÍTÁS      | Melléknevek   |
        | ADV      | Módosítószavak      |
        | CONJ     | Kötőszavak |
        | DET      | Determiners  |
        | MODÁLIS    | Műveletek        |
        | FŐNÉV     | Főnevek        |
        | ELŐKÉSZÍTŐ     | Elöljárószók |
        | PRON     | Névmásokat     |
        | MŰVELET     | Műveletek        |
        | MÁS    | Egyéb        |

        Egy megvalósítási Megjegyzés ezekkel a címkékkel rész-a-beszéd átalakítás címkézése a angol nyelvű oldalon, és majd véve a leggyakoribb címke minden forrás/cél pár volt alapján. Ezért ha személyek gyakran fordítására egy másik része a beszédfelismerés címkére angolul spanyol szót, címkék is szükségessé tehet helytelen (tekintetében a spanyol word) alatt.

    * `confidence`: Egy értéknek 0,0 és 1,0, amely a "megbízhatósági" között (vagy akár több pontosan, "probability a betanítási adatok"), hogy fordítási pár. Egy forrás Word abban, hogy pontszámok összege is, vagy előfordulhat, hogy nem összeg 1.0. 

    * `prefixWord`: A karakterlánc megadva a a word, a fordítás előtagjaként megjelenítéséhez. Ez jelenleg a főnevek, hogy rendelkezik gendered determiners nyelven gendered determiner. Például az előtag, spanyol Word "mosca" az "la", mivel "mosca" nőnemű főnév spanyol nyelven. Ez a tulajdonság csak a függő fordítása, és nem a forrás. Ha nincs előtagja, az üres lesz.
    
    * `backTranslations`: A listában "vissza" a cél. Például a forrás, amely a célként megadott lefordítani is szó. A lista tartalmazza a kért forrás word garantáltan (például akkor, ha a forrás word alatt keresni "keresnie", akkor válik biztossá, hogy a "keresnie" lesz a `backTranslations` lista). Azonban ez nem biztos, hogy az első helyen kell, és gyakran nem lesz. Egyes elemeinek a `backTranslations` lista egy objektum a következő tulajdonságok szerint:

        * `normalizedText`: Jogosultságot ad a forrás-kifejezés, amely a célként megadott vissza fordítás normalizált formája egy karakterlánc. Ezt az értéket kell használni a bemeneti [keresési példák](.\v3-0-dictionary-examples.md).        

        * `displayText`: A karakterlánc megadva a forrás-kifejezés, amely a cél az űrlapon a legjobb biztonsági-fordítás megfelelő végfelhasználói megjelenítése.

        * `numExamples`: Egy példák, amelyek elérhetők a fordítási pár számát jelző egész számot. Tényleges példák külön hívja kell beolvasni [keresési példák](.\v3-0-dictionary-examples.md). A szám többnyire célja, hogy egy UX megjelenítéshez elősegítése A felhasználói felület például hivatkozás hozzáadása a biztonsági-fordítási, ha a példák száma nagyobb, mint nulla és vissza-fordítása megjelenítése egyszerű szövegként, ha nincs példák. Vegye figyelembe, hogy a tényleges száma példák hívásával vissza [keresési példák](.\v3-0-dictionary-examples.md) lehet kisebb, mint `numExamples`, mert további szűrés is alkalmazható parancsprogramok eltávolítása a "hibás" példák.
        
        * `frequencyCount`: Jelző egész számot az adatok a fordítási pár gyakoriságát. Ez a mező fő célja, hogy módszereket biztosítsanak a felhasználói felület segítségével vissza-fordítások rendezheti úgy, hogy a leggyakoribb feltételek első.

    > [!NOTE]
    > Ha éppen kikeresi fel kifejezés nem létezik a szótárban, a rendszer a választ 200 (OK), de a `translations` lista üres lista lesz.

## <a name="examples"></a>Példák

A példa bemutatja, hogyan kell megkeresni az angol nyelvű kifejezés spanyol alternatív fordításokat `fly` .

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

---

Az adott válasz törzsének (jobb érthetőség kedvéért bizonyos rövidítése) a következő:

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

Ez a példa bemutatja, mi történik, ha éppen keresett kifejezés a érvényes szótárat pár nem létezik.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

---

Mivel a kifejezés nem található a szótárban, a választörzs tartalmaz-e egy üres `translations` listája.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
