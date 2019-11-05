---
title: Helytelenül beírt szavak – LUIS
titleSuffix: Azure Cognitive Services
description: Javítsa a hibásan írt szavakat a hosszúságú kimondott szöveg-ben a Bing Spell Check API v7 és LUIS végponti lekérdezések hozzáadásával.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9c8babac8450bdfd170d3d18b338ba3d64383a67
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499024"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Helytelenül írt szavak kijavítása Bing Spell Check

A Luis-alkalmazást [Bing Spell Check API v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) -vel integrálhatja, hogy javítsa a hibásan írt szavakat a hosszúságú kimondott szöveg, mielőtt Luis megjósolja a pontszámot és az entitásokat. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Bing Spell Check v7 első kulcsának létrehozása
Az [első Bing Spell Check API v7-kulcs](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) ingyenes. 

![Szabad kulcs létrehozása](./media/luis-tutorial-bing-spellcheck/free-key.png)

< nevet a "Create-Subscription-Key" ></a>
## <a name="create-endpoint-key"></a>Végponti kulcs létrehozása
Ha az ingyenes kulcs lejárt, hozzon létre egy végponti kulcsot.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 

2. Válassza az **erőforrás létrehozása** lehetőséget a bal felső sarokban.

3. A keresőmezőbe írja be a `Bing Spell Check API V7` kifejezést.

    ![Bing Spell Check API v7 keresése](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Válassza ki a szolgáltatást. 

5. Egy információs panel jelenik meg a jogot tartalmazó információkkal, beleértve a jogi értesítést is. Válassza a **Létrehozás** lehetőséget az előfizetés-létrehozási folyamat megkezdéséhez. 

6. A következő panelen adja meg a szolgáltatás beállításait. Várjon, amíg a szolgáltatás-létrehozási folyamat befejeződik.

    ![Adja meg a szolgáltatás beállításait](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. A bal oldali navigációs sávon a **Kedvencek** cím alatt válassza az **összes erőforrás** lehetőséget.

8. Válassza ki az új szolgáltatást. A típusa **Cognitive Services** , és a hely **globális**. 

9. A fő panelen a **kulcsok** elemre kattintva megtekintheti az új kulcsokat.

    ![Kulcsok megragadása](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Másolja ki az első kulcsot. Csak a két kulcs egyikét kell megadnia. 

## <a name="using-the-key-in-luis-test-panel"></a>A kulcs használata a LUIS test panelen
A kulcs használatához két helyen kell lennie a LUIS-ben. Az első a [teszt panelen](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel)található. A kulcs nem kerül a LUIS-ba, hanem egy munkamenet-változó. Minden alkalommal be kell állítania a kulcsot, amikor azt szeretné, hogy a tesztelési panel alkalmazza a Bing Spell Check API v7 szolgáltatást a teljes verzióra. A kulcs beállításához tekintse meg a teszt panel [utasításait](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) .

## <a name="adding-the-key-to-the-endpoint-url"></a>A kulcs hozzáadása a végpont URL-címéhez
A végponti lekérdezésnek a lekérdezési karakterlánc paraméterei között kell átadni a kulcsot a helyesírási korrekciót alkalmazni kívánt összes lekérdezéshez. Lehet, hogy van egy Csevegőrobot, amely meghívja a LUIS-t, vagy meghívhatja közvetlenül a LUIS Endpoint API-t. Függetlenül attól, hogy a végpont hogyan legyen meghívva, minden egyes hívásnak tartalmaznia kell a szükséges információkat a helyesírás-javítások megfelelő működéséhez.

A végpont URL-címe több olyan értéket tartalmaz, amelyeket helyesen kell átadni. A Bing Spell Check API v7-kulcs csupán egy másik. A **helyesírási** paramétert True értékre kell állítani, és a **Bing-Spell-Check-előfizetés-Key** értéket kell beállítania a kulcs értékére:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Hibásan írt kifejezés elküldése LUIS-re
1. Egy webböngészőben másolja az előző karakterláncot, és cserélje le a `region`, `appId`, `luisKey`és `bingKey` a saját értékeire. Ügyeljen arra, hogy a végpont régióját használja, ha az eltér a közzétételi [régiótól](luis-reference-regions.md).

2. Adjon hozzá egy hibásan írt kiírást, például "milyen messzire kerül a mountainn?". Az angol nyelvű, `mountain`egy `n`, a helyes helyesírás. 

3. Válassza az ENTER billentyűt a lekérdezés LUIS-nek való elküldéséhez.

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

## <a name="ignore-spelling-mistakes"></a>Helyesírási hibák figyelmen kívül hagyása

Ha nem szeretné használni a Bing Spell Check API v7 szolgáltatást, hozzá kell adnia a helyes és helytelen helyesírást. 

Két megoldás a következők:

* Címkézheti például az összes különböző helyesírású hosszúságú kimondott szöveg, hogy LUIS el tudja sajátítani a megfelelő helyesírást és elírásokat. Ez a beállítás több címkézési erőfeszítést igényel, mint a spell-ellenőrző használata.
* Hozzon létre egy kifejezés listát a szó összes változatával. Ezzel a megoldással nem kell megcímkézni a példában szereplő hosszúságú kimondott szöveg. 

## <a name="publishing-page"></a>Közzétételi oldal
A [közzétételi](luis-how-to-publish-app.md) lapon engedélyezve van a **Bing helyesírás-ellenőrző engedélyezése** jelölőnégyzet. Ez a kulcs létrehozása és a végpont URL-címének megváltozása. Továbbra is a megfelelő végponti paramétereket kell használnia ahhoz, hogy a helyesírás kijavítva legyen az egyes részleteknél. 

> [!div class="nextstepaction"]
> [További információ a példa hosszúságú kimondott szöveg](luis-how-to-add-example-utterances.md)
