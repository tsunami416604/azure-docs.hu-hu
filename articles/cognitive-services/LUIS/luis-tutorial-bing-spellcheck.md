---
title: Hibásan írt szavak javítása - LUIS
titleSuffix: Azure Cognitive Services
description: Javítsa ki a hibásan írt szavakat a kimondott szövegekben a Bing Helyesírás-ellenőrző API V7 hozzáadása a LUIS végpontlekérdezésekhez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74225453"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Hibásan írt szavak javítása a Bing helyesírás-ellenőrzésével

Integrálhatja a LUIS-alkalmazást a [Bing Helyesírás-ellenőrző API V7-tel,](https://azure.microsoft.com/services/cognitive-services/spell-check/) hogy kijavítsa a hibásan írt szavakat az utterances-ben, mielőtt a LUIS előre jelzi az utterance (kifejezés) pontszámát és entitásait. 

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Első kulcs létrehozása a Bing Helyesírás-ellenőrzés V7-hez

Az [első Bing Helyesírás-ellenőrző API v7 kulcs](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) ingyenes. 

![Szabad kulcs létrehozása](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name="create-subscription-key"></a>

## <a name="create-endpoint-key"></a>Végpontkulcs létrehozása
Ha az ingyenes kulcs lejárt, hozzon létre egy végpontkulcsot.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 

2. Válassza **az Erőforrás létrehozása lehetőséget** a bal felső sarokban.

3. A keresőmezőbe írja be a `Bing Spell Check API V7` kifejezést.

    ![Bing helyesírás-ellenőrző API V7 keresése](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Válassza ki a szolgáltatást. 

5. A jobb oldalon megjelenik egy információs panel, amely információkat tartalmaz, beleértve a jogi közleményt is. Válassza a **Létrehozás gombot** az előfizetés-létrehozási folyamat megkezdéséhez. 

6. A következő panelen adja meg a szolgáltatás beállításait. Várja meg, amíg befejeződik a szolgáltatáslétrehozási folyamat.

    ![Szolgáltatásbeállítások megadása](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. A bal oldali navigációs **sávon** a Kedvencek cím alatt válassza a **Minden erőforrás** lehetőséget.

8. Válassza ki az új szolgáltatást. Típusa a **Cognitive Services,** a helyszín pedig **globális.** 

9. A főpanelen válassza a **Billentyűk** lehetőséget az új billentyűk megtekintéséhez.

    ![Fogd gombok](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Másolja az első kulcsot. Csak egy a két kulcs. 

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>A kulcs hozzáadása a végpont URL-címéhez
A végpontlekérdezéshez a lekérdezési karakterlánc paramétereiben átadott kulcsra van szükség minden olyan lekérdezésnél, amelyet helyesírás-javítást kíván alkalmazni. Lehet, hogy egy chatbot, amely felhívja a LUIS, vagy hívhatja a LUIS végpont API-t közvetlenül. A végpont hívásától függetlenül minden egyes hívásnak tartalmaznia kell a helyesírás-korrekciók megfelelő működéséhez szükséges információkat.

A végpont URL-címe több olyan értéket tartalmaz, amelyeket megfelelően kell átadni. A Bing Helyesírás-ellenőrző API v7 kulcs csak egy ilyen. A **spellCheck** paramétert igaz értékre kell állítania, és a **bing-spell-check-subscription-key** értékét a kulcsértékre kell állítania:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Hibásan írt kimondott szöveg küldése a LUIS-nak
1. Webböngészőben másolja az előző karakterláncot, `luisKey`és `bingKey` cserélje le a `region`, `appId`, és a saját értékeit. Győződjön meg arról, hogy a végpontrégiót használja, ha az eltér a közzétételi [régiótól.](luis-reference-regions.md)

2. Adjon hozzá egy hibásan írt utterance (kifejezés) például "Milyen messze van a hegy?". Az angol `mountain`nyelvben `n`a ( angol nyelven ) a helyes helyesírás. 

3. Válassza az Enter lehetőséget, ha el szeretné küldeni a lekérdezést a LUIS-nak.

4. A LUIS JSON-eredménnyel `How far is the mountain?`válaszol. Ha a Bing Helyesírás-ellenőrző API v7 `query` hibás helyesírást észlel, a LUIS alkalmazás JSON-válaszának mezője tartalmazza az eredeti lekérdezést, és a `alteredQuery` mező a LUIS-nak küldött javított lekérdezést tartalmazza.

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

Ha nem szeretné használni a Bing Helyesírás-ellenőrző API v7 szolgáltatás, meg kell adnia a megfelelő és helytelen helyesírási. 

Két megoldás:

* Címke példa utterances, amelyek rendelkeznek a különböző helyesírási, hogy a LUIS megtudja tanulni a megfelelő helyesírást, valamint az elírások. Ez a beállítás több címkézési erőfeszítést igényel, mint a helyesírás-ellenőrző használata.
* Hozzon létre egy kifejezéslistát a szó összes változatával. Ezzel a megoldással nem kell címkézni a szó változatok a példa utterances. 

## <a name="publishing-page"></a>Közzététellap
A [közzétételi](luis-how-to-publish-app.md) lapon a **Bing helyesírás-ellenőrzőjének engedélyezése** jelölőnégyzet található. Ez a kulcs létrehozásához és a végpont URL-címének változásaihoz szükséges kényelmi szempontból. Továbbra is a megfelelő végpontparamétereket kell használnia ahhoz, hogy a helyesírást minden egyes utterance (kifejezés) korrigálja. 

> [!div class="nextstepaction"]
> [További információ a példakimondott szövegekről](luis-how-to-add-example-utterances.md)
