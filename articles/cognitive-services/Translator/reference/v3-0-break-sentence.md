---
title: Fordító szöveg API BreakSentence metódus
titleSuffix: Azure Cognitive Services
description: A Translator Text API BreakSentence metódus azonosítja a mondathatárok elhelyezését egy szövegdarabban.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: 4c314148b8e1495a8b5a12c42d4989d13cdd6a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548118"
---
# <a name="translator-text-api-30-breaksentence"></a>Fordító szöveg API 3.0: BreakSentence

A mondathatárok elhelyezését azonosítja egy szövegdarabban.

## <a name="request-url"></a>Kérés URL-címe

Kérés `POST` küldése a következő knek:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Kérelem paraméterei

A lekérdezési karakterláncon átadott kérelemparaméterek a következők:

| Lekérdezési paraméter | Leírás |
| -------| ----------- |
| api-verzió <img width=200/>   | **Kötelező lekérdezési paraméter**.<br/>Az ügyfél által kért API verziója. Az értéknek kell lennie. `3.0` |
| language | **Nem kötelező lekérdezési paraméter**.<br/>A beviteli szöveg nyelvét azonosító nyelvi címke. Ha nincs megadva kód, a program automatikus nyelvfelismerést alkalmaz. |
| szkriptet.    | **Nem kötelező lekérdezési paraméter**.<br/>A bemeneti szöveg által használt parancsfájlt azonosító parancsfájlcímke. Ha nincs megadva parancsfájl, a program a nyelv alapértelmezett parancsfájlját veszi alapul.  | 

A kérelemfejlécek a következőket tartalmazzák:

| Fejlécek | Leírás |
| ------- | ----------- |
| Hitelesítési fejléc(ek) <img width=200/>  | **Kötelező kérelemfejléc**.<br/>Lásd: <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">A hitelesítéshez rendelkezésre álló lehetőségek.</a> |
| Content-Type | **Kötelező kérelemfejléc**.<br/>Megadja a hasznos adat tartalom típusát. Lehetséges értékek: `application/json`. . |
| Tartalom hossza    | **Kötelező kérelemfejléc**.<br/>A kérelem törzsének hossza.  | 
| X-ClientTraceId   | **Nem kötelező**.<br/>Az ügyfél által létrehozott GUID a kérelem egyedi azonosítására. Ne feledje, hogy a fejlécet kihagyhatja, ha a nyomkövetési `ClientTraceId`azonosítót a lekérdezési karakterláncba a .  | 

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömbelem egy JSON-objektum, `Text`amelynek karakterlánc-tulajdonsága van. A mondathatárokat a tulajdonság értéke `Text` alapján számítja ki a függvény. Egy egy darab szöveget tartalmazó mintakérelem törzse így néz ki:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Az alábbi korlátozások érvényesek:

* A tömb legbőlegelhető 100 elemet tartalmazhat.
* A tömbelem szöveges értéke szóközökkel együtt nem haladhatja meg a 10 000 karaktert.
* A kérelemben szereplő teljes szöveg nem haladhatja meg az 50 000 karaktert szóközökkel együtt.
* Ha `language` a lekérdezési paraméter meg van adva, akkor minden tömbelemnek ugyanazon a nyelven kell lennie. Ellenkező esetben a nyelvi automatikus észlelés az egyes tömbelemekre egymástól függetlenül vonatkozik.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb, amely a bemeneti tömb minden egyes karakterláncához egy-egy eredményt ad. Az eredményobjektum a következő tulajdonságokat tartalmazza:

  * `sentLen`: Egész számok ból álló tömb, amely a szövegelemmondatainak hosszát jelöli. A tömb hossza a mondatok száma, az értékek pedig az egyes mondatok hossza. 

  * `detectedLanguage`: Az észlelt nyelvet a következő tulajdonságokon keresztül leíró objektum:

     * `language`: Az észlelt nyelv kódja.

     * `score`: Az eredmény megbízhatóságát jelző lebegőérték. A pontszám nulla és egy között van, és az alacsony pontszám alacsony magabiztosságot jelez.
     
    Vegye figyelembe, hogy a `detectedLanguage` tulajdonság csak akkor van jelen az eredményobjektumban, ha a nyelv automatikus észlelését kéri.

Egy példa JSON válasz:

```json
[
  {
    "sentLen": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
  }
]
```

## <a name="response-headers"></a>Válaszfejlécek

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>X-RequestId azonosító</td>
    <td>A szolgáltatás által a kérelem azonosítására létrehozott érték. Hibaelhárítási célokra használják.</td>
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
    <td>429</td>
    <td>A kiszolgáló elutasította a kérést, mert az ügyfél túllépte a kérelemkorlátot.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse azt a következőkkel: `X-RequestId`a hiba dátuma `X-ClientTraceId`és időpontja, a válaszfejlécből származó kérelemazonosító és az ügyfélazonosító a kérelemfejlécből.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló átmenetileg nem érhető el. Próbálkozzon újra a kéréssel. Ha a hiba továbbra is fennáll, jelentse azt a következőkkel: `X-RequestId`a hiba dátuma `X-ClientTraceId`és időpontja, a válaszfejlécből származó kérelemazonosító és az ügyfélazonosító a kérelemfejlécből.</td>
  </tr>
</table> 

Ha hiba történik, a kérelem json-hibaválaszt is ad vissza. A hibakód egy 6 jegyű szám, amely a 3 jegyű HTTP-állapotkódot, majd egy 3 jegyű számot követ a hiba további kategorizálása érdekében. A gyakori hibakódok a [v3 Translator Text API referenciaoldalán](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)találhatók. 

## <a name="examples"></a>Példák

A következő példa bemutatja, hogyan lehet egyetlen mondathoz mondathatárokat szerezni. A szolgáltatás automatikusan felismeri a mondat nyelvét.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

