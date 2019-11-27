---
title: Helytelenül beírt szavak – LUIS
titleSuffix: Azure Cognitive Services
description: Beszédmódok LUIS végpont lekérdezések ad hozzá a Bing Spell ellenőrzés API 7-es megfelelő kijavítsa a hibásan leírt szavakat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 51b0d02443df872a7fae13116ea77b13d05055fa
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225453"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>A Bing Spell Check megfelelő kijavítsa a hibásan leírt szavakat

A Luis-alkalmazást [Bing Spell Check API v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) -vel integrálhatja, hogy javítsa a hibásan írt szavakat a hosszúságú kimondott szöveg, mielőtt Luis megjósolja a pontszámot és az entitásokat. 

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>A Bing-helyesírás-ellenőrzés 7-es első kulcs létrehozása

Az [első Bing Spell Check API v7-kulcs](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) ingyenes. 

![Ingyenes kulcs létrehozása](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name="create-subscription-key"></a>

## <a name="create-endpoint-key"></a>Végponti kulcs létrehozása
Ha az ingyenes kulcs lejárt, a végpont kulcs létrehozása.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 

2. Válassza az **erőforrás létrehozása** lehetőséget a bal felső sarokban.

3. A keresőmezőbe írja be a `Bing Spell Check API V7` kifejezést.

    ![Keresés a Bing helyesírás-ellenőrzése API 7-es verziója](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Válassza ki a szolgáltatást. 

5. Az információk panel a jobb oldalon, beleértve a jogi nyilatkozat adatokat tartalmazó jelenik meg. Válassza a **Létrehozás** lehetőséget az előfizetés-létrehozási folyamat megkezdéséhez. 

6. A következő panelen adja meg a szolgáltatás beállításait. Várjon, amíg a szolgáltatás létrehozásának folyamatát a befejezéshez.

    ![Adja meg a szolgáltatás beállításai](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. A bal oldali navigációs sávon a **Kedvencek** cím alatt válassza az **összes erőforrás** lehetőséget.

8. Válassza ki az új szolgáltatást. A típusa **Cognitive Services** , és a hely **globális**. 

9. A fő panelen a **kulcsok** elemre kattintva megtekintheti az új kulcsokat.

    ![Kulcsok megnyitása](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Másolja ki az első kulcsot. Csak akkor kell egyik két kulcsot. 

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>A kulcs hozzáadása a végpont URL-címe
A végpont lekérdezés minden egyes lekérdezés javításra a alkalmazni szeretné a lekérdezési karakterlánc paraméterei átadott kulcs szükséges. Előfordulhat, hogy egy csevegőrobot, amely meghívja a LUIS, vagy közvetlenül a LUIS-végpont API-t hívhatja. Függetlenül attól, milyen a végpont neve minden hívás tartalmaznia kell a helyesírási javításokkal megfelelő működéséhez szükséges információkat.

A végpont URL-címnek megfelelően átadandó igénylő több értéket. A Bing Spell Check API 7-es verziója kulcs, ezek közül csak egy másikat. A **helyesírási** paramétert True értékre kell állítani, és a **Bing-Spell-Check-előfizetés-Key** értéket kell beállítania a kulcs értékére:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>A LUIS felbontása utterance (kifejezés) küldése
1. Egy webböngészőben másolja az előző karakterláncot, és cserélje le a `region`, `appId`, `luisKey`és `bingKey` a saját értékeire. Ügyeljen arra, hogy a végpont régióját használja, ha az eltér a közzétételi [régiótól](luis-reference-regions.md).

2. Adja hozzá a hibásan írt utterance (kifejezés) például a "milyen is a mountainn?". Az angol nyelvű, `mountain`egy `n`, a helyes helyesírás. 

3. Válassza ki a lekérdezés küldendő LUIS adja meg.

4. LUIS a `How far is the mountain?`JSON-eredménnyel válaszol. Ha Bing Spell Check API v7 hibát észlel, akkor a LUIS alkalmazás JSON-válaszában szereplő `query` mező tartalmazza az eredeti lekérdezést, és a `alteredQuery` mező tartalmazza a LUIS-nak küldött javított lekérdezést.

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

## <a name="ignore-spelling-mistakes"></a>Hagyja figyelmen kívül a helyesírási hibákat

Ha nem szeretné használni a Bing Spell Check API v7 szolgáltatást, hozzá kell adnia a helyes és helytelen helyesírást. 

Két megoldás a következők:

* Címkézheti például az összes különböző helyesírású hosszúságú kimondott szöveg, hogy LUIS el tudja sajátítani a megfelelő helyesírást és elírásokat. Ez a beállítás nagyobb címkézési munkát, mint a helyesírás-ellenőrzés használata szükséges.
* Hozzon létre egy kifejezés listát a szó összes változatával. Ezzel a megoldással nem kell megcímkézni a példában szereplő hosszúságú kimondott szöveg. 

## <a name="publishing-page"></a>Közzétételi lap
A [közzétételi](luis-how-to-publish-app.md) lapon engedélyezve van a **Bing helyesírás-ellenőrző engedélyezése** jelölőnégyzet. Ez az a kényelmi célokat szolgál, a kulcs létrehozásához, és megismerheti, hogyan változik a végpont URL-CÍMÉT. Továbbra is meg kell ahhoz, hogy minden kimondásakor korrigálni helyesírási rendelkezik a megfelelő végpontra paraméterek használatával. 

> [!div class="nextstepaction"]
> [További információ a példa hosszúságú kimondott szöveg](luis-how-to-add-example-utterances.md)
