---
title: '9. oktatóanyag: Hangulatelemzés – pozitív, negatív vagy semleges érzelmek azonosítása a LUIS-ban'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban létrehozunk egy alkalmazást, amely bemutatja, hogyan nyerhető ki pozitív, negatív vagy semleges érzelem a kimondott szövegekből. A hangulat meghatározása a teljes kimondott szövegből történik.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 60c4b280033e110f6b8b2a3ce720934e118c8479
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424850"
---
# <a name="tutorial-9--extract-sentiment-of-overall-utterance"></a>9. oktatóanyag: Kimondott szöveg általános hangulatának kinyerése
Ebben az oktatóanyagban létrehozunk egy alkalmazást, amely bemutatja, hogyan nyerhető ki pozitív, negatív vagy semleges érzelem a kimondott szövegekből. A hangulat meghatározása a teljes kimondott szövegből történik.

A hangulatelemzés az a képesség, amellyel megállapítható, hogy egy felhasználó által kimondott szöveg pozitív, negatív, vagy semleges. 

Az alábbi kimondott szövegek a különböző hangulatokat példázzák:

|Hangulat|Pontszám|Kimondott szöveg|
|:--|:--|:--|
|pozitív|0,91 |John W. Smith remek prezentációt tartott Párizsban.|
|pozitív|0,84 |jill-jones@mycompany.com nagyszerű munkát végzett a Parker befektetői bemutatón.|

A hangulatelemzés egy olyan közzétételi beállítás, amely minden kimondott szövegre vonatkozik. Önnek nem kell megkeresnie és felcímkéznie a hangulatot kifejező szavakat a kimondott szövegekben, mert a hangulatelemzés a teljes kimondott szövegre vonatkozik. 

Mivel ez közzétételi beállítás, a szándékok vagy az entitások oldalán nem jelenik meg; az [interaktív teszt](luis-interactive-test.md#view-sentiment-results) panelen vagy a végpont URL-címén való teszteléskor látható. 

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Meglévő oktatóalkalmazás használata 
> * Hangulatelemzés hozzáadása közzétételi beállításként
> * Betanítás
> * Közzététel
> * Kimondott szöveg hangulatának lekérése végpontból

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Meglévő alkalmazás használata

Folytassa az előző oktatóanyagban létrehozott **EmberiErőforrások** nevű alkalmazással. 

Amennyiben nem rendelkezik az előző oktatóanyagból származó EmberiErőforrások alkalmazással, kövesse a következő lépéseket:

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-keyphrase-HumanResources.json).

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `sentiment` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

## <a name="employeefeedback-intent"></a>Az EmployeeFeedback szándék 
Adjon hozzá egy új szándékot a vállalat tagjaitól származó alkalmazotti visszajelzések rögzítéséhez. 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget.

3. Adja meg az új szándék nevét: `EmployeeFeedback`.

    ![Hozzon lére egy új, EmployeeFeedback nevű szándék párbeszédpanelt](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Adja hozzá több kimondott szöveget, amelyek azt jelzik, hogy az alkalmazott valamit jól végzett el, vagy amelyek olyan területet jelölnek, ahol fejlődni kell:

    Ne feledje, az Emberi erőforrások alkalmazásban az alkalmazottak az `Employee` listaentitásban vannak meghatározva, név, e-mail-cím, telefonmellék, mobiltelefonszám, és az USA-beli szövetségi társadalombiztosítási szám alapján. 

    |Beszédmódok|
    |--|
    |425-555-1212 szépen üdvözölte a szülési szabadságról visszatérő munkatársat|
    |234-56-7891 vigaszt nyújtott gyászoló munkatársa számára.|
    |jill-jones@mycompany.com nem rendelkezik a papírmunkához szükséges számlákkal.|
    |john.w.smith@mycompany.com egy hónap késéssel adta le a szükséges nyomtatványokat, aláírások nélkül|
    |x23456 nem ért oda a fontos, külső helyszínen tartott marketing értekezletre.|
    |x12345 nem jelent meg a júniusi felülvizsgálatokat ismertető értekezleten.|
    |Jill Jones nagyszerű befektetési bemutatót tartott a Harvardon|
    |John W. Smith remek prezentációt tartott a Stanfordon|

    [ ![A LUIS-alkalmazás képernyőképe: kimondott példaszövegek az EmployeeFeedback szándékban](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train"></a>Betanítás

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Alkalmazás konfigurálása hangulatelemzés belefoglalásához
1. Válassza a **Manage** (Kezelés) lehetőséget a jobb felső navigációs területen, majd a **Publish settings** (Közzétételi beállítások) elemet a bal oldali menüben.

2. A beállítás engedélyezéséhez kapcsolja be a **hangulatelemzést**. 

    ![](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish"></a>Közzététel

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-sentiment-of-utterance-from-endpoint"></a>Kimondott szöveg hangulatának lekérése végpontból

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Jill Jones work with the media team on the public portal was amazing`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a következő szándékot kell visszaadnia kinyert hangulatelemzéssel: `EmployeeFeedback`.
    
    ```JSON
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.4983256
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.4983256
        },
        {
          "intent": "MoveEmployee",
          "score": 0.06617523
        },
        {
          "intent": "GetJobInformation",
          "score": 0.04631853
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0103248553
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.007531875
        },
        {
          "intent": "FindForm",
          "score": 0.00344597152
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00337914471
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0026357458
        },
        {
          "intent": "None",
          "score": 0.00214573368
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00157622492
        },
        {
          "intent": "Utilities.Confirm",
          "score": 7.379545E-05
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 0,
          "endIndex": 9,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "media team",
          "type": "builtin.keyPhrase",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "public portal",
          "type": "builtin.keyPhrase",
          "startIndex": 43,
          "endIndex": 55
        },
        {
          "entity": "jill jones",
          "type": "builtin.keyPhrase",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    A SentimentAnalysis pozitív, 0,86 értékű pontszámmal. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag közzétételi beállításként hozzáadja a hangulatelemzést, amellyel átfogó hangulatértékek nyerhetők ki a kimondott szövegből.

> [!div class="nextstepaction"] 
> [Végponti kimondott szövegek áttekintése az Emberi erőforrás alkalmazásban](luis-tutorial-review-endpoint-utterances.md) 

