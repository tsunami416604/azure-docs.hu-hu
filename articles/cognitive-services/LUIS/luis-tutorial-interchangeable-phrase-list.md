---
title: Oktatóanyag kifejezés lista használatával javítása a LUIS-előrejelzés – Azure |} A Microsoft Docs
description: Ebben az oktatóanyagban egy kifejezést lista hozzáadása a LUIS-alkalmazásokon, és az eredmény a teljesítménynövekedés.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 9f12d9e8c9ee2038e7841cd05bb438421a5a8984
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345345"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>Oktatóanyag: A kifejezés lista előrejelzéseket javítása hozzáadása
Ebben az oktatóanyagban szándék pontszámok pontosságának javítása, és a szavak jelentése megegyezik (szinonimák) rendelkező hozzáadásával egy cserélhető entitások azonosítása [kifejezés lista szolgáltatás](./luis-concept-feature.md).

> [!div class="checklist"]
* Új alkalmazás importálása  
* Lekérdezés koncového bodu ismert utterance (kifejezés) 
* Lekérdezés koncového bodu _ismeretlen_ utterance (kifejezés)
* Kifejezéslista javítása ismeretlen utterance (kifejezés) pontszám hozzáadása
* Ellenőrizze az entitás található kifejezéslista használatakor

Ehhez a cikkhez egy ingyenes [LUIS][LUIS]-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="import-a-new-app"></a>Új alkalmazás importálása
1. Töltse le a [példa LUIS-alkalmazásokon] [ LuisSampleApp] , amely a jelen oktatóanyag célja. Ez a következő lépésben fogja használni. 

2. Leírtak szerint [hozzon létre egy alkalmazást](Create-new-app.md#import-new-app), importálni a letöltött fájlt a [LUIS] [ LUIS] webhely egy új alkalmazásként. Az alkalmazás neve az "A kifejezés lista oktatóanyaga." Rendelkezik szándék fog vonatkozni, az entitások és a kimondott szöveg. 

3. [Train](luis-how-to-train.md) az alkalmazást. Mindaddig, amíg be van tanítva, nem [interaktív vizsgálati](interactive-test.md#interactive-testing) azt a [LUIS] [ LUIS] webhelyén. 

4. Az a [közzététel](luis-how-to-publish-app.md) lapon válassza ki a **minden beágyazott előre jelzett szándék pontszámok** jelölőnégyzetet. Ha a jelölőnégyzet be van jelölve, a rendszer az összes leképezések adja vissza. Ha a jelölőnégyzet nincs bejelölve, a felső célt adja vissza. 

5. [Közzététel](luis-how-to-publish-app.md) az alkalmazást. Közzététele az alkalmazás lehetővé teszi, hogy kipróbáljuk a HTTPS-végpont használatával. 

## <a name="test-a-trained-utterance"></a>Tesztelje a betanított utterance (kifejezés)
A közzétett végpont használatával lekérdezheti az utterance (kifejezés), hogy már ismeri az alkalmazást. A LUIS már tudja, hogy az utterance (kifejezés), mert a pontszám, magas, és az entitás észlel.

1. Az a [Language Understanding (LUIS)] [ LUIS] webhelyén, a a **közzététel** az új alkalmazások lapon, válassza ki a végpont URL-cím a **erőforrások és a kulcsok**szakaszban. 

    ![A végponti URL-cím közzététele](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. A böngészőben, az URL-cím végén található hozzáadása után a következő lekérdezést a `q=`.

    `I want a computer replacement`

    A végpont válaszol a következő JSON-ra:
    
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

    A leképezési pontszámát 0.973 és az entitás észlelési pontszámát 0.846 nincsenek magas, mert az alkalmazás lett képzett az utterance (kifejezés). A LUIS alkalmazás szándék oldalán van az utterance (kifejezés) **GetHardware**. Az utterance (kifejezés) szöveg, `computer`, van-e címkével a **hardver** entitás. 
    
    |status|Word| Leképezési pontszám | Entitás pontszám |
    |--|--|--|--|
    |Betanított| szeretné | 0.973 | 0.846 |
    
    
## <a name="test-an-untrained-utterance"></a>Egy kellő utterance (kifejezés) tesztelése
A böngészőben az utterance (kifejezés), amely az alkalmazás már nem tudja lekérdezni a közzétett egyazon végpont használata:

`I require a computer replacement`

Az utterance (kifejezés) használ, az előző utterance (kifejezés) szinonima:

| Betanított word | Kellő szinonimát |
|--|--|
| szeretné | megkövetelése |

A végpont válasz a következő:

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
| Betanított| szeretné | 0.973 | 0.846 |
| Kellő| megkövetelése | 0.840 | - |

A kellő utterance (kifejezés) szándék pontszám, alacsonyabb, mint a címkézett utterance (kifejezés), mert a LUIS, tudni fogja, hogy a mondat megegyezik nyelvtanilag. De a LUIS nem ismert, hogy megcímkézzen szavak jelentése megegyezik. A kifejezéslista nélkül is, a **hardver** entitás nem található.

LUIS, amely kell tanít *szeretné* és *megkövetelése* az alkalmazástartományban ugyanazt jelenti, mivel egy szót egynél több jelentése. 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>A pontszám a kifejezéslista kellő utterance (kifejezés) javítása 
1. Adjon hozzá egy [kifejezéslista](luis-how-to-add-features.md) nevű funkció **szeretné** értékét `want`, majd válassza ki **Enter**.

    > [!TIP]
    > Minden egyes szó vagy kifejezés után válassza ki a **Enter** kulcsot. Szó vagy kifejezés adnak hozzá a **listaértékek kifejezés** mezőbe, amíg a kurzor marad a **érték** mezőbe. Ezzel a funkcióval gyorsan számos értékeket adhat meg.

2. A szavak, amelyek a LUIS javasolja megtekintéséhez jelölje ki **javasoljuk**. 

    ![Javasolt értékek](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. Adja hozzá az összes szó. Ha `require` van a javasolt listán nem szereplő adja hozzá a szükséges érték. 

4. Mivel ezeknek a szavaknak szinonimák, tartsa a *felcserélhetők* beállítást, és válassza ki **mentése**.

    ![Listabeli értékek kifejezés](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. A felső navigációs sávon válassza **betanításához** betanításához az alkalmazást, de nem közzéteendő. Most már két modell. A két modell szereplő értékek összehasonlításával.

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>A kifejezés lista modellt a közzétett modell összehasonlítása
Ebben az alkalmazásban a közzétett modell nem képzett az a szinonimák. Csak a jelenleg szerkesztett modellt a szinonimák kifejezés listáját tartalmazza. A modell összehasonlítására, használja a [interaktív vizsgálati](interactive-test.md#interactive-testing). 

1. Nyissa meg a **teszt** ablaktáblán, és adja meg a következő utterance (kifejezés):

    `I require a computer replacement`

2. A hálózatvizsgáló panel megnyitásához válassza **vizsgálat**. 

    ![Válassza ki a vizsgálata](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. Válassza ki az új kifejezés lista modell a közzétett modell összehasonlítására, **hasonlítsa össze a közzétett**.

    ![Vizsgálja meg és a jelenlegi közzétett](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

Miután hozzáadta a kifejezéslista, a nagyobb pontosságát az utterance (kifejezés) és a **hardver** megtalálható entitás. 

|status | Kifejezéslista| Leképezési pontszám | Entitás pontszám |
|--|--|--|--|
| Közzétéve | - | 0.84 | - |
| A jelenleg szerkesztett |✔| 0.92 | Azonosított hardver entitás |

> [!TIP]
> * Használatával [interaktív vizsgálati](interactive-test.md#interactive-testing), összehasonlíthatja a közzétett modell betanított módosításokat végzett, a közzététel után. 
> * Használatával [végpont tesztelése](luis-how-to-publish-app.md#test-your-published-endpoint-in-a-browser), LUIS válasz JSON is megtekintheti. 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>Az entitás pontszám lekérése a megadott végpont
Az entitás pontszám megtekintéséhez [közzétesszük a modellt](luis-how-to-publish-app.md) és lekérdezni a végpont. 

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

A **hardver** entitás egy kifejezést listáját tartalmazó 0.595 pontszámát jeleníti meg. A kifejezéslista előtt az entitás nem található. 

|status | Kifejezéslista| Leképezési pontszám | Entitás pontszám |
|--|--|--|--|
| Közzétéve | - | 0.84 | - |
| A jelenleg szerkesztett |✔| 0.92 | 0.595 |


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Ehhez válassza az alkalmazáslistában az alkalmazás neve mellett jobbra található három pontot (...), majd a **Delete** (Törlés) lehetőséget. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Végpont lekérdezés utterance (kifejezés)-előrejelzés beolvasása](luis-get-started-cs-get-intent.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[LuisFeatures]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[LuisSampleApp]: https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
