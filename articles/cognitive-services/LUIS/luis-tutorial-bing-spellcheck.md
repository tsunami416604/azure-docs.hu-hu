---
title: Helytelenül beírt szavak – LUIS
titleSuffix: Azure Cognitive Services
description: Javítsa a hibásan írt szavakat a hosszúságú kimondott szöveg-ben a Bing Spell Check API v7 és LUIS végponti lekérdezések hozzáadásával.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: f416fe8ef4f6e89d07e6065d4c9435642d9bacb9
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179639"
---
# <a name="correct-misspelled-words-with-bing-search-resource"></a>Hibásan írt szavak kijavítása Bing Search erőforrással

A LUIS-alkalmazást [Bing Search](https://ms.portal.azure.com/#create/Microsoft.BingSearch) használatával integrálhatja a hosszúságú kimondott szöveg hibásan írt szavainak kijavításához, mielőtt a Luis megjósolja a pontszámot és az entitásokat.

## <a name="create-endpoint-key"></a>Végponti kulcs létrehozása

Ha Bing Search erőforrást szeretne létrehozni a Azure Portalban, kövesse az alábbi utasításokat:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).

2. Válassza az **erőforrás létrehozása** lehetőséget a bal felső sarokban.

3. A keresőmezőbe írja be `Bing Search V7` és válassza ki a szolgáltatást.

4. Egy információs panel jelenik meg a jogot tartalmazó információkkal, beleértve a jogi értesítést is. Válassza a **Létrehozás** lehetőséget az előfizetés-létrehozási folyamat megkezdéséhez.

    :::image type="content" source="./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png" alt-text="Bing Spell Check API v7-erőforrás":::

5. A következő panelen adja meg a szolgáltatás beállításait. Várjon, amíg a szolgáltatás-létrehozási folyamat befejeződik.

6. Az erőforrás létrehozása után nyissa meg a bal oldali **kulcsok és végpont** panelt. 

7. Másolja be az egyik kulcsot, amelyet fel szeretne venni az előrejelzési kérelem fejlécébe. Csak a két kulcs egyikére lesz szüksége.

8. Adja hozzá a kulcsot az `mkt-bing-spell-check-key` előrejelzési kérelem fejlécéhez.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>A kulcs hozzáadása a végpont URL-címéhez
Minden olyan lekérdezésnél, amelynél helyesírás-módosítást kíván alkalmazni, a végponti lekérdezéshez a lekérdezési fejléc paraméterében átadott Bing helyesírás-ellenőrzési erőforrás kulcsa szükséges. Lehet, hogy van egy Csevegőrobot, amely meghívja a LUIS-t, vagy meghívhatja közvetlenül a LUIS Endpoint API-t. Függetlenül attól, hogy a végpont hogyan legyen meghívva, minden egyes hívásnak tartalmaznia kell a szükséges információkat a fejléc kérésében a helyesírás-helyesbítések megfelelő működéséhez. A **MKT-Bing-Spell-Check-Key** értékkel kell megadnia a kulcs értékét.


## <a name="send-misspelled-utterance-to-luis"></a>Hibásan írt kifejezés elküldése LUIS-re
1. Adjon hozzá egy hibásan írt kiírást az előrejelzési lekérdezésben, amely a következőt küldi el: "milyen messze van a mountainn?". Az angol nyelvben `mountain` a, az egyik `n` , a helyes helyesírás.

2. LUIS egy JSON-eredménnyel válaszol `How far is the mountain?` . Ha Bing Spell Check API v7 hibát észlel, akkor a `query` Luis alkalmazás JSON-válaszában szereplő mező tartalmazza az eredeti lekérdezést, és a `alteredQuery` mező tartalmazza a Luis-nak küldött javított lekérdezést.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Helyesírási hibák figyelmen kívül hagyása

Ha nem szeretné használni a Bing Search API v7 szolgáltatást, hozzá kell adnia a helyes és helytelen helyesírást.

Két megoldás a következők:

* Címkézheti például az összes különböző helyesírású hosszúságú kimondott szöveg, hogy LUIS el tudja sajátítani a megfelelő helyesírást és elírásokat. Ez a beállítás több címkézési erőfeszítést igényel, mint a spell-ellenőrző használata.
* Hozzon létre egy kifejezés listát a szó összes változatával. Ezzel a megoldással nem kell megcímkézni a példában szereplő hosszúságú kimondott szöveg.


> [!div class="nextstepaction"]
> [További információ a példa hosszúságú kimondott szöveg](./luis-how-to-add-entities.md)
