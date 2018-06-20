---
title: Az oktatóanyag kifejezés lista használatával LUIS előrejelzéseket - Azure javítása érdekében |} Microsoft Docs
description: Ebben az oktatóanyagban adhat a LUIS kifejezés listáját, és a teljesítménynövekedés a pontszám.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: feb8acb674fd2dc62b62c26da6a6b42515f30242
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265971"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>Oktatóanyag: Hozzáadása kifejezéslista előrejelzéseket javítása érdekében
Ebben az oktatóanyagban leképezési eredmények pontosságának javítása és entitásokat szavak, amely jelentéssel bír (szinonimák) rendelkezik egy cserélhető hozzáadásával azonosítása [kifejezés lista szolgáltatás](./luis-concept-feature.md).

> [!div class="checklist"]
* Új alkalmazás importálása  
* Ismert utterance lekérdezés végpont 
* Végpont lekérdezése _ismeretlen_ utterance
* Adja hozzá a kifejezéslista ismeretlen utterance pontszám javítása érdekében
* Ellenőrizze a entitás található kifejezéslista használatakor

Ez a cikk van szüksége egy szabad [LUIS] [ LUIS] fiók ahhoz, hogy a LUIS alkalmazás létrehozásához.

## <a name="import-a-new-app"></a>Új alkalmazás importálása
1. Töltse le a [példa LUIS app] [ LuisSampleApp] , amely a jelen oktatóanyag célja. A következő lépésben még szüksége lesz rájuk. 

2. A [hozzon létre egy alkalmazást](Create-new-app.md#import-new-app), importálja a fájlt a letöltött a [LUIS] [ LUIS] webhely új alkalmazásként. Az alkalmazás nevére az "A kifejezéslista oktatóanyaga." Leképezések, entitások és utterances rendelkezik. 

3. [Vonat](luis-how-to-train.md) az alkalmazást. Be van tanítva, amíg nem [párbeszédes formában történő teszteléséhez](interactive-test.md#interactive-testing) legyen a [LUIS] [ LUIS] webhelyet. 

4. A a [közzététel](PublishApp.md) lapon jelölje be a **Include minden előre jelezni leképezési pontszámok** jelölőnégyzetet. Ha a jelölőnégyzet be van jelölve, adja vissza az összes leképezések. Ha a jelölőnégyzet nincs bejelölve, a felső célt adja vissza. 

5. [Közzététel](PublishApp.md) az alkalmazást. Az alkalmazás közzététele lehetővé teszi a HTTPS-végpont segítségével tesztelheti. 

## <a name="test-a-trained-utterance"></a>Betanított utterance tesztelése
Használja a közzétett végpontot, hogy már ismeri az alkalmazás egy utterance lekérdezni. LUIS már ismeri a utterance, mert a pontszám magas és észleli az entitás.

1. Az a [nyelvi ismertetése (LUIS)] [ LUIS] webhely, az a **közzététel** az új alkalmazás lapján válassza ki a végpont URL-címet a **erőforrások és a kulcsok**szakasz. 

    ![A végponti URL-cím közzététele](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. A böngészőben az URL-cím végén hozzáadása után a következő lekérdezés a `q=`.

    `I want a computer replacement`

    A végpont válaszol, a következő JSON:
    
    ```JSON
    {
      "query": "I want a computer replacement",
      "topScoringIntent": {
        "intent": "GetHardware",
        "score": 0.9735458
      },
      "intents": [
        {
          "intent": "GetHardware",
          "score": 0.9735458
        },
        {
          "intent": "None",
          "score": 0.07053033
        },
        {
          "intent": "Whois",
          "score": 0.03760778
        },
        {
          "intent": "CheckCalendar",
          "score": 0.02285902
        },
        {
          "intent": "CheckInventory",
          "score": 0.0110072717
        }
      ],
      "entities": [
        {
          "entity": "computer",
          "type": "Hardware",
          "startIndex": 9,
          "endIndex": 16,
          "score": 0.8465915
        }
      ]
    }
    ```

    A leképezési pontszám 0.973 és az entitás észlelési pontszám 0.846 nincsenek magas, mert az alkalmazás a utterance rendelkező lett képezni. A leképezési oldal LUIS alkalmazásban van a utterance **GetHardware**. Szöveg, amelyet a utterance `computer`, van-e címkézve a **hardver** entitás. 
    
    |status|Word| Leképezési pontszám | Entitás pontszám |
    |--|--|--|--|
    |Betanítása| szeretné, hogy | 0.973 | 0.846 |
    
    
## <a name="test-an-untrained-utterance"></a>Egy kellő utterance tesztelése
A böngészőben egy utterance, amely az alkalmazás már nem ismeri a azonos közzétett végpontot a lekérdezés használata:

`I require a computer replacement`

A utterance használja az előző utterance szinonimát:

| Betanított word | Kellő szinonimát |
|--|--|
| szeretné, hogy | Megkövetelése |

A rendszer a végpont választ:

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.840912163
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.840912163
    },
    {
      "intent": "None",
      "score": 0.0972757638
    },
    {
      "intent": "Whois",
      "score": 0.0448251367
    },
    {
      "intent": "CheckCalendar",
      "score": 0.0291390456
    },
    {
      "intent": "CheckInventory",
      "score": 0.0137837809
    }
  ],
  "entities": []
}
```

| status | Word | Leképezési pontszám | Entitás pontszám |
|--|--|--|--|
| Betanítása| szeretné, hogy | 0.973 | 0.846 |
| Kellő| Megkövetelése | 0.840 | - |

A kellő utterance leképezési pontszám LUIS tudja, hogy a mondatok megegyezik nyelvtanilag oka alacsonyabb, mint a címkézett utterance. De LUIS nem ismeri, a utterances rendelkezik-e a jelentéssel bír. A kifejezéslista nélkül is, a **hardver** entitás nem található.

Amely LUIS kell tanítása *szeretné* és *szükséges* az alkalmazástartományban ugyanazt jelenti, mert egy word rendelkezhet egynél több jelentése. 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>A kifejezéslista kellő utterance pontszám javítása 
1. Adja hozzá egy [kifejezéslista](luis-how-to-add-features.md) nevű funkció **szeretné** értékét `want`, majd válassza ki **Enter**.

    > [!TIP]
    > Minden szó vagy kifejezés után válassza ki a **Enter** kulcs. A szó vagy kifejezés hozzáadódik a **listaértékek kifejezés** mezőben, miközben a kurzor marad a a **érték** mezőbe. Ezzel a szolgáltatással gyorsan sok értékeket adhat meg.

2. Válassza ki a szöveget, LUIS azt javasolja, hogy ha **javasoljuk**. 

    ![Javasolt értékek](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. Adja hozzá a szavakat. Ha `require` van nem a javasolt listában, adja hozzá a szükséges értéknek. 

4. Mivel ezeknek a szavaknak szinonimák, tartsa a *cserélhető* beállításával, és válassza ki **mentése**.

    ![Kifejezés lista értékeire](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. Válassza ki a felső navigációs sáv **betanítása** betanítása az alkalmazást, de nem közzéteendő. Most már rendelkezik két modell. A két modell értékek összehasonlításával.

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>A kifejezés lista modellt a közzétett modell összehasonlítása
Az alkalmazásban a közzétett modell nem betanítása a szinonimák a. Csak a jelenleg szerkesztett modellt szinonimák kifejezés listáját tartalmazza. A modellek összehasonlítása, használja a [interaktív tesztelés](interactive-test.md#interactive-testing). 

1. Nyissa meg a **teszt** ablaktáblán, és írja be a következő utterance:

    `I require a computer replacement`

2. A hálózatfelügyeleti panel megnyitásához, válassza ki a **vizsgálat**. 

    ![Válassza ki vizsgálata](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. Válassza ki az új kifejezés lista modellre a közzétett modell összehasonlítására, **hasonlítsa össze a közzétett**.

    ![Vizsgálja meg és a jelenlegi közzétett](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

Miután hozzáadta a kifejezéslista, a utterance nagyobb pontosságát, és a **hardver** entitás található. 

|status | Kifejezéslista| Leképezési pontszám | Entitás pontszám |
|--|--|--|--|
| Közzétéve | - | 0.84 | - |
| A jelenleg szerkesztett |✔| 0.92 | Hardver entitás azonosított |

> [!TIP]
> * A [interaktív tesztelés](interactive-test.md#interactive-testing), összehasonlíthatja a közzétett modell bármely képzett közzététele után végzett módosításokat. 
> * A [végpont tesztelés](PublishApp.md#test-your-published-endpoint-in-a-browser), megtekintheti a tényleges LUIS válasz JSON. 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>Az entitás pontszám lekérdezni a végpont teszt
Az entitás pontszám megtekintéséhez [közzététele a modell](PublishApp.md) és lekérdezni a végpont. 

`I require a computer replacement`

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.916503668
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.916503668
    },
    {
      "intent": "None",
      "score": 0.136505231
    },
    {
      "intent": "Whois",
      "score": 0.02778677
    },
    {
      "intent": "CheckInventory",
      "score": 0.0144592477
    },
    {
      "intent": "CheckCalendar",
      "score": 0.01401332
    }
  ],
  "entities": [
    {
      "entity": "computer",
      "type": "Hardware",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.5959917
    }
  ]
}
```

A **hardver** entitás jeleníti meg a kifejezés listájával 0.595 pontszámot. A kifejezéslista létezett, mielőtt az entitás nem található. 

|status | Kifejezéslista| Leképezési pontszám | Entitás pontszám |
|--|--|--|--|
| Közzétéve | - | 0.84 | - |
| A jelenleg szerkesztett |✔| 0.92 | 0.595 |


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szükség, a LUIS alkalmazás törlése. Ehhez az szükséges, válassza a három pont menü (...) az alkalmazás nevétől jobbra alkalmazáslistájában, jelölje be **törlése**. Az előugró párbeszédpanelen **Delete app?**, jelölje be **Ok**.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Végpont lekérdezéssel utterance előrejelzés beolvasása](luis-get-started-cs-get-intent.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[LuisFeatures]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[LuisSampleApp]: https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
