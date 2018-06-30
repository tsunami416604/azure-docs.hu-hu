---
title: Bing helyesírás ellenőrizze API v7 hozzáadása LUIS lekérdezések |} Microsoft Docs
titleSuffix: Azure
description: Bing helyesírás ellenőrizze API V7 felvételével LUIS végpont lekérdezések utterances a megfelelő helyesírási.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/27/2018
ms.author: v-geberr
ms.openlocfilehash: 96b23146e726b7fee86b7e449c81d7efc0073e8d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127669"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Bing helyesírás-ellenőrzés a megfelelő helyesírási

A LUIS alkalmazások integrálva [Bing helyesírás ellenőrizze API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) előtt LUIS előrejelzi a pontszám és entitásai számára a utterance helyesírási utterances a megoldására. 

## <a name="create-first-key-for-bing-spell-check-v7"></a>Bing helyesírás ellenőrizze V7 első kulcs létrehozása
A [első Bing helyesírás ellenőrizze API v7 kulcs](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) ingyenes. 

![Szabad kulcs létrehozása](./media/luis-tutorial-bing-spellcheck/free-key.png)

< a name "hozzon létre-előfizetés-kulcsot" ></a>
## <a name="create-endpoint-key"></a>Végpont-kulcs létrehozása
Ha a szabad kulcs érvényes, a végpont-kulcs létrehozása.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 

2. Válassza ki **hozzon létre egy erőforrást** a bal felső sarokban.

3. A keresőmezőbe írja be a `Bing Spell Check API V7` kifejezést.

    ![Bing helyesírás-keresés API V7 ellenőrzése](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Válassza ki a szolgáltatást. 

5. Az adatai információkat, beleértve a jogi nyilatkozat tartalmazó jobb jelenik meg. Válassza ki **létrehozása** az előfizetés létrehozásának megkezdéséhez. 

6. A következő panelen adja meg a szolgáltatás beállításait. Várja meg a szolgáltatás-létrehozási folyamat befejezéséhez.

    ![Adja meg a szolgáltatás beállításai](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Válassza ki **összes erőforrás** alatt a **Kedvencek** a bal oldali navigációs cím.

8. Válassza ki az új szolgáltatást. A típus **kognitív szolgáltatások** és a hely **globális**. 

9. Jelölje ki a fő panelen **kulcsok** az új kulcsok megtekintéséhez.

    ![Kulcsok adása](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Másolja át az első kulcsát. Csak akkor kell a két kulcsok egyikét. 

## <a name="using-the-key-in-luis-test-panel"></a>A kulcs használatával LUIS teszt panelen
A kulcs használatához LUIS két helyen van. Az egyik a [teszt panel](interactive-test.md#view-bing-spell-check-corrections-in-test-panel). A kulcs nem menti azokat LUIS de ehelyett egy munkamenet-változó. Állítsa a kulcsot minden alkalommal, amikor azt szeretné, hogy a teszt panelen, a Bing helyesírás ellenőrizze API v7 szolgáltatás alkalmazása a utterance kell. Lásd: [utasításokat](interactive-test.md#view-bing-spell-check-corrections-in-test-panel) beállításához a kulcsot a teszt panelen.

## <a name="adding-the-key-to-the-endpoint-url"></a>A kulcs hozzáadása a végpont URL-címe
A végpont lekérdezés minden lekérdezéshez javításra alkalmazni kívánja a lekérdezési karakterlánc paraméterek átadott kulcs szükséges. Előfordulhat, hogy a hívó LUIS chatbot, vagy közvetlenül a LUIS végpont API kérhet. Függetlenül attól, hogy a végpont neve minden egyes hívásakor tartalmaznia kell javításokat helyesen adta-e a megfelelő működéséhez szükséges adatokat.

A végpont URL-címnek több értéket kell átadni megfelelően. Bing helyesírás ellenőrizze API v7 fontos, ezek csak egy másikat. Meg kell adni a **a helyesírás-ellenőrzési** paramétert true, és az értékre kell állítani a **bing helyesírás-ellenőrzés-előfizetés-kulcs** kulcs értékre:

https://{Region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appID}?Subscription-Key={luisKey}&spellCheck=**igaz**& bing helyesírás-ellenőrzés-előfizetés-kulcs =**{bingKey}**& részletes = true & timezoneOffset = 0 & q = {utterance}

## <a name="send-misspelled-utterance-to-luis"></a>Hibásan utterance küldeni LUIS
1. Egy webböngészőben, másolja a fenti karakterláncot, és cserélje le a `region`, `appId`, `luisKey`, és `bingKey` saját értékekkel. Ügyeljen arra, hogy a végpont régiót kell használnia, ha eltér a közzététel [régió](luis-reference-regions.md).

2. Hozzáadása egy hibásan utterance például a "milyen távolságban a mountainn?". Angol `mountain`, egy `n`, helyesen van. 

3. Válassza ki a lekérdezés küldendő LUIS adja meg.

4. LUIS válaszol, egy JSON eredménye `How far is the mountain?`. Bing helyesírás ellenőrizze API v7 észleli a helyesírási hiba, ha a `query` mező a LUIS alkalmazás JSON-NÁ válaszul tartalmazza az eredeti lekérdezés és a `alteredQuery` mezőben a javított lekérdezés LUIS küldött.

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

## <a name="ignore-spelling-mistakes"></a>Hagyja figyelmen kívül helyesen írta-e
Ha nem szeretné használni a Bing helyesírás ellenőrizze API v7 szolgáltatás, a címkézés utterances rendelkező helyesen írta-e, hogy LUIS megtanulhassa megfelelő helyesen adta-e, valamint a elírás eredménye. Ezt a beállítást a helyesírás-ellenőrző használnak, mint további szalagcímkézési beavatkozást igényel.

## <a name="publishing-page"></a>Közzétételi lap
A [közzétételi](publishapp.md) lapon egy **engedélyezése Bing helyesírás-ellenőrző** jelölőnégyzetet. Ez egy, a kulcs létrehozásához, és megismerheti, hogyan a végponti URL-Címének módosítása a könnyebb elérhetőség érdekében. Továbbra is meg kell ahhoz, hogy helyesen adta-e az egyes utterance javítani kell a megfelelő végpont paraméterek használatával. 

> [!div class="nextstepaction"]
> [További tudnivalók példa utterances](luis-how-to-add-example-utterances.md)
