---
title: '8. oktatóanyag: kulcskifejezések kinyerése a LUIS-ban'
titleSuffix: Azure Cognitive Services
description: A kimondott szöveg kulcstémájának kinyeréséhez használja az előre összeállított keyPhrase entitást. A kimondott szövegeket nem szükséges megcímkézni előre összeállított entitásokkal. Az entitást a rendszer automatikusan észleli.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: d6feb24b1e59aee70204d8438f1a4c51f71d1835
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281812"
---
# <a name="tutorial-8-extract-key-phrases-of-utterance"></a>8. oktatóanyag: Kimondott szövegek kulcsszavainak kinyerése
Ebben az oktatóanyagban az előre összeállított keyPhrase entitást használhatja kulcstémák a kimondott szövegekből történő kinyeréséhez. A kimondott szövegeket nem szükséges megcímkézni előre összeállított entitásokkal. Az entitást a rendszer automatikusan észleli.

Az alábbi kimondott szövegek például szolgáló kulcskifejezései:

|Kimondott szöveg|keyPhrase entitásértékek|
|--|--|
|Lesz jövőre olyan egészségbiztosítási csomag, amelynek alacsonyabb lesz az önrésze?|„alacsonyabb önrész”<br>„új egészségbiztosítási csomag”<br>„jövőre”|
|A nagyobb önrészű egészségbiztosítási csomag kiterjed a látáskorrekciós vizsgálatokra?|„nagyobb önrészű egészségbiztosítási csomag”<br>„látáskorrekciós vizsgálat”|

Az ügyfélalkalmazás használhatja ezeket az értékeket csakúgy, mint más kinyert entitásokat a beszélgetés következő lépésének megállapításához.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Meglévő oktatóalkalmazás használata
> * keyPhrase entitás hozzáadása 
> * Betanítás
> * Közzététel
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Meglévő alkalmazás használata

Folytassa az előző oktatóanyagban létrehozott **EmberiErőforrások** nevű alkalmazással. 

Amennyiben nem rendelkezik az előző oktatóanyagból származó EmberiErőforrások alkalmazással, kövesse a következő lépéseket:

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-simple-HumanResources.json).

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `keyphrase` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

## <a name="add-keyphrase-entity"></a>keyPhrase entitás hozzáadása 
A kimondott szöveg témájának kinyeréséhez adja hozzá az előre összeállított keyPhrase entitást.

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Válassza az **Entities** (Entitások) elemet a bal oldali menüben.

3. Válassza az **Add prebuilt entity** (Előre összeállított entitás hozzáadása) lehetőséget.

4. Az előugró párbeszédpanelen válassza ki a **keyPhrase** elemet, majd a **Done** (Kész) lehetőséget. 

    [ ![Az entitáslistát tartalmazó előugró párbeszédpanel képernyőképe](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

5. A bal oldali menüben válassza az **Intents** (Szándékok) lehetőséget, majd a **Utilities.Confirm** szándékot. A keyPhrase-entitás számos kimondott szövegben meg van címkézve. 

    [ ![Képernyőkép a Utilities.Confirm szándékról, a keyPhrases megcímkézve a kimondott szövegben](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train"></a>Betanítás

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Közzététel

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Szándék és entitások lekérése a végpontból

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Lépjen az URL-cím végéhez, és írja be a következőt: `does form hrf-123456 cover the new dental benefits and medical plan`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. 
    
    ```JSON
    {
      "query": "does form hrf-123456 cover the new dental benefits and medical plan",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9300641
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9300641
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0359598845
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0141798034
        },
        {
          "intent": "MoveEmployee",
          "score": 0.0112197418
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00507669244
        },
        {
          "intent": "None",
          "score": 0.00238501839
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00202810857
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00102957746
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0008688423
        },
        {
          "intent": "Utilities.Confirm",
          "score": 3.557994E-05
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",git 
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "new dental benefits",
          "type": "builtin.keyPhrase",
          "startIndex": 31,
          "endIndex": 49
        },
        {
          "entity": "medical plan",
          "type": "builtin.keyPhrase",
          "startIndex": 55,
          "endIndex": 66
        },
        {
          "entity": "hrf",
          "type": "builtin.keyPhrase",
          "startIndex": 10,
          "endIndex": 12
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        }
      ]
    }
    ```

    Az űrlap keresése közben a felhasználó több információt adott meg, mint amennyi az űrlap megtalálásához szükséges. A további információkat **builtin.keyPhrase** elemként adja vissza a rendszer. Az ügyfélalkalmazás használhatja ezeket a további információkat egy következő kérdéshez, például „Szeretne egy emberierőforrások-képviselővel beszélni az új fogászati juttatásokról?”, vagy egy több lehetőséget tartalmazó menüt adhat meg, amely például tartalmazza a következőt: „További információk az új fogászati juttatásokról vagy orvosi tervről”.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag hozzáadta az előre összeállított keyPhrase entitást, amely gyorsan megadja a kimondott szövegek kulcskifejezéseit a kimondott szövegek megcímkézésének igénye nélkül. 

> [!div class="nextstepaction"]
> [Hangulatelemzés hozzáadása az alkalmazáshoz](luis-quickstart-intent-and-sentiment-analysis.md)