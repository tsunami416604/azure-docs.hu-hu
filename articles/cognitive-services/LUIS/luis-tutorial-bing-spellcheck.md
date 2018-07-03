---
title: Adja hozzá a Bing Spell Check API 7-es verziója a LUIS-lekérdezésekre |} A Microsoft Docs
titleSuffix: Azure
description: Beszédmódok LUIS végpont lekérdezések ad hozzá a Bing Spell ellenőrzés API 7-es megfelelő kijavítsa a hibásan leírt szavakat.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/27/2018
ms.author: v-geberr
ms.openlocfilehash: e7bf447dafecf090f610f670539ca4673827953a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37343978"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>A Bing Spell Check megfelelő kijavítsa a hibásan leírt szavakat

A LUIS alkalmazásait is integrálhatja [a Bing Spell ellenőrzés API 7-es](https://azure.microsoft.com/services/cognitive-services/spell-check/) a LUIS előrejelzi a pontszám és entitásai az utterance (kifejezés) előtt javítsa ki a kimondott szöveg kijavítsa a hibásan leírt szavakat. 

## <a name="create-first-key-for-bing-spell-check-v7"></a>A Bing-helyesírás-ellenőrzés 7-es első kulcs létrehozása
A [első Bing Spell Check API 7-es verziója kulcsot](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) ingyenes. 

![Ingyenes kulcs létrehozása](./media/luis-tutorial-bing-spellcheck/free-key.png)

< a name "létrehozása-subscription-key" ></a>
## <a name="create-endpoint-key"></a>Végponti kulcs létrehozása
Ha az ingyenes kulcs lejárt, a végpont kulcs létrehozása.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 

2. Válassza ki **erőforrás létrehozása** bal felső sarokban.

3. A keresőmezőbe írja be a `Bing Spell Check API V7` kifejezést.

    ![Keresés a Bing helyesírás-ellenőrzése API 7-es verziója](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Válassza ki a szolgáltatást. 

5. Az információk panel a jobb oldalon, beleértve a jogi nyilatkozat adatokat tartalmazó jelenik meg. Válassza ki **létrehozás** az előfizetés létrehozásának megkezdéséhez. 

6. A következő panelen adja meg a szolgáltatás beállításait. Várjon, amíg a szolgáltatás létrehozásának folyamatát a befejezéshez.

    ![Adja meg a szolgáltatás beállításai](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Válassza ki **összes erőforrás** alatt a **Kedvencek** a bal oldali navigációs címe.

8. Válassza ki az új szolgáltatást. A típusa **Cognitive Services** és a hely **globális**. 

9. Jelölje ki a fő panelen **kulcsok** az új kulcsok megtekintéséhez.

    ![Kulcsok megnyitása](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Másolja ki az első kulcsot. Csak akkor kell egyik két kulcsot. 

## <a name="using-the-key-in-luis-test-panel"></a>A kulcs használatával LUIS teszt panel
Nincsenek a kulcs használatához a LUIS két helyén. Az első szerepel a [teszt panel](interactive-test.md#view-bing-spell-check-corrections-in-test-panel). A kulcs nem menti a LUIS, de ehelyett egy munkamenet-változó. Minden alkalommal, amikor azt szeretné, hogy a teszt panel az utterance (kifejezés) vonatkoznak a Bing Spell Check API 7-es verziója szolgáltatást, állítsa be a kulcsot kell. Lásd: [utasításokat](interactive-test.md#view-bing-spell-check-corrections-in-test-panel) állítja a kulcsot a teszt panel.

## <a name="adding-the-key-to-the-endpoint-url"></a>A kulcs hozzáadása a végpont URL-címe
A végpont lekérdezés minden egyes lekérdezés javításra a alkalmazni szeretné a lekérdezési karakterlánc paraméterei átadott kulcs szükséges. Előfordulhat, hogy egy csevegőrobot, amely meghívja a LUIS, vagy közvetlenül a LUIS-végpont API-t hívhatja. Függetlenül attól, milyen a végpont neve minden hívás tartalmaznia kell a helyesírási javításokkal megfelelő működéséhez szükséges információkat.

A végpont URL-címnek megfelelően átadandó igénylő több értéket. A Bing Spell Check API 7-es verziója kulcs, ezek közül csak egy másikat. Be kell állítani a **helyesírás-ellenőrzés** paraméter igaz, és állítsa az **bing – helyesírás-ellenőrzés-subscription-key** , a kulcs értéke:

https://{Region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appID}?Subscription-Key={luisKey}&spellCheck=**igaz**& bing – helyesírás-ellenőrzés-subscription-key =**{bingKey}**& részletes = true & timezoneOffset = 0 & q = {utterance (kifejezés)}

## <a name="send-misspelled-utterance-to-luis"></a>A LUIS felbontása utterance (kifejezés) küldése
1. Másolja a fenti karakterláncot egy webböngészőt, és cserélje le a `region`, `appId`, `luisKey`, és `bingKey` a saját értékeire. Ügyeljen arra, hogy a végpont régiót használni, ha eltér a közzététel [régió](luis-reference-regions.md).

2. Adja hozzá a hibásan írt utterance (kifejezés) például a "milyen is a mountainn?". Angol nyelvű `mountain`, egy `n`, helyesen van. 

3. Válassza ki a lekérdezés küldendő LUIS adja meg.

4. A LUIS fűzi hozzá egy JSON-eredményt a `How far is the mountain?`. Ha a Bing Spell Check API 7-es verzióját észleli a helyesírási, a `query` a LUIS-alkalmazás JSON-válasz mező tartalmazza az eredeti lekérdezés és a `alteredQuery` mező tartalmazza a LUIS küldött javított lekérdezés.

```
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
Ha nem szeretné a Bing Spell Check API 7-es verziója szolgáltatást használja, a címkézés, amelyek rendelkeznek a helyesírási hibákat, hogy a megfelelő helyesírás-ellenőrzés, valamint a benne elgépelések tudhat meg a LUIS kimondott szöveg. Ez a beállítás nagyobb címkézési munkát, mint a helyesírás-ellenőrzés használata szükséges.

## <a name="publishing-page"></a>Közzétételi lap
A [közzétételi](luis-how-to-publish-app.md) oldal rendelkezik egy **engedélyezése a Bing helyesírás-ellenőrzővel** jelölőnégyzetet. Ez az a kényelmi célokat szolgál, a kulcs létrehozásához, és megismerheti, hogyan változik a végpont URL-CÍMÉT. Továbbra is meg kell ahhoz, hogy minden kimondásakor korrigálni helyesírási rendelkezik a megfelelő végpontra paraméterek használatával. 

> [!div class="nextstepaction"]
> [További tudnivalók a példa kimondott szöveg](luis-how-to-add-example-utterances.md)
