---
title: Reguláris kifejezést entitás
titleSuffix: Azure Cognitive Services
description: Konzisztensen formázott adatok kinyerése kimondott szövegből a Reguláris kifejezés entitással.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 654d3c4e7dd7ec8916b785f52a78388ec04b3cc9
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53712780"
---
# <a name="tutorial-3-extract-well-formatted-data"></a>3. oktatóanyag: Helyesen formázott adatok kinyerése
Ebben az oktatóanyagban módosítja az Emberi erőforrások alkalmazást, hogy konzisztensen formázott adatokat nyerjen ki egy kimondott szövegből a **Reguláris kifejezés** entitás használatával.

Az entitások célja a kimondott szövegekben található fontos adatok kinyerése. Az alkalmazás arra használja a reguláriskifejezés-entitást, hogy formázott Emberierőforrások- (HR-) űrlapszámokat nyerjen ki egy kimondott szövegből. Bár a kimondott szöveg szándékát mindig gépi tanulás határozza meg, ez az entitástípus nem gép által tanult. 

**Példák kimondott szövegekre:**

|Példák kimondott szövegekre|
|--|
|Hol található HRF-123456?|
|Ki lett létrehozva HRF-123234?|
|HRF-456098 francia nyelvű közzé lett téve?|
|HRF-456098|
|HRF-456098 dátum?|
 
A reguláris kifejezés megfelelő választás az ilyen típusú adatok esetén, ha

* az adatok helyesen formázottak.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Meglévő oktatóalkalmazás használata
> * FindForm szándék hozzáadása
> * Reguláriskifejezés-entitás hozzáadása 
> * Betanítás
> * Közzététel
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Meglévő alkalmazás használata
Folytassa az előző oktatóanyagban létrehozott **EmberiErőforrások** nevű alkalmazással. 

Amennyiben nem rendelkezik az előző oktatóanyagból származó EmberiErőforrások alkalmazással, kövesse a következő lépéseket:

1. Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-prebuilts-HumanResources.json).

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `regex` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes. 

## <a name="findform-intent"></a>FindForm szándék

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

3. Az előugró párbeszédpanelen írja be a `FindForm` karakterláncot, majd válassza a **Kész** elemet. 

    ![Képernyőkép a Create new intent (Új szándék létrehozása) párbeszédpanelről, a keresőmezőben a Utilities (Segédprogramok) karaktersorral](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. Adjon hozzá kimondott példaszövegeket a szándékhoz.

    |Példák kimondott szövegekre|
    |--|
    |Mi a hrf-123456 URL-címe?|
    |Hol van a hrf-345678?|
    |Mikor frissült a hrf-456098?|
    |Frissítette Kovács János múlt héten a hrf-234639 űrlapot?|
    |Hány verziója van a hrf-345123 űrlapnak?|
    |Kinek kell jóváhagynia a hrf-123456 űrlapot?|
    |Hány személynek kell jóváhagynia a hrf-345678 űrlapot?|
    |A hrf-234123 dátuma?|
    |A hrf-546234 szerzője?|
    |A hrf-456234 címe?|

    [ ![Képernyőkép a Szándék lapról, az új kimondott szövegek kiemelésével](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    Az alkalmazásban fel lettek véve előre összeállított számentitások az előző oktatóanyagban, ezért minden űrlapszám meg van címkézve. Ez elég lehet az ügyfélalkalmazásához, de a szám nem lesz megcímkézve a szám típusával. Egy új entitás létrehozása a megfelelő névvel lehetővé teszi, hogy az ügyfélalkalmazás megfelelően dolgozza fel az entitást, amikor visszakapja a LUIS-ból.

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="regular-expression-entity"></a>Reguláriskifejezés-entitás 
A reguláriskifejezés-entitás, amelyet meg kell feleltetni az űrlapszámnak: `hrf-[0-9]{6}`. Ez a reguláriskifejezés-entitás megfelelteti egymásnak a `hrf-` karaktereket, de figyelmen kívül hagyja, hogy kis- vagy nagybetűvel szerepelnek-e, illetve a kulturális változatokat. Megfelelteti egymásnak a számjegyeket 0-tól 9-ig, pontosan 6 számjegyet.

A HRF a következőt jelöli: `human resources form`.

A LUIS elvégzi a kimondott szöveg lexikális elemzését, ha az hozzá van adva egy szándékhoz. Ezen kimondott szövegek lexikális elemzése szóközöket ad hozzá a kötőjel előtt és után, `Where is HRF - 123456?` A reguláris kifejezést a rendszer nyers formájában alkalmazza a kimondott szövegre, még a lexikális elemzés előtt. Mivel még a _nyers_ űrlapra van alkalmazva, a reguláris kifejezésnek nem kell foglalkoznia a szóhatárokkal. 

A következő lépésekkel hozzon létre egy reguláriskifejezés-entitást, amely elmagyarázza a LUIS-nak, mi az a HRF-számformátum:

1. Válassza az **Entities** (Entitások) elemet a bal oldali ablaktáblán.

2. Az Entities (Entitások) lapon válassza a **Create new entity** (Új entitás létrehozása) gombot. 

3. A felugró párbeszédpanelen az új entitásnak adja a `HRF-number` nevet, válassza a **RegEx** entitástípust, a **Regex** értéke legyen `hrf-[0-9]{6}`, majd válassza a **Done** (Kész) lehetőséget.

    ![Képernyőkép az új entitás tulajdonságainak beállításakor felugró párbeszédpanelről](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. A bal oldali menüben válassza az **Intents** (Szándékok) elemet, majd a **FindForm** szándékot kiválasztva megtekintheti a kimondott szövegekben a megcímkézett reguláris kifejezést. 

    [![Képernyőkép a Label (Címke) kimondott szövegről létező entitás és regex mintával](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Mivel az entitás nem gépi tanulással létrejött entitás, a címkét a rendszer a létrehozása után azonnal alkalmazza a kimondott szövegre és megjeleníti a LUIS webhelyén.

## <a name="train"></a>Betanítás

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Közzététel

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Szándék és entitások lekérése a végpontból

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Lépjen az URL-cím végéhez, és írja be a következőt: `When were HRF-123456 and hrf-234567 published in the last year?`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek pont megfelelő, és `FindForm` szándékot kell visszaadnia `HRF-123456` és `hrf-234567` űrlapszámmal.

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    A kimondott szövegben szereplő számokat a rendszer kétszer adja vissza, egyszer az új `hrf-number` entitásként, egyszer pedig az előre létrehozott `number` entitásként. Egy kimondott szöveghez tartozhat több entitás, és több azonos típusú entitás is, ahogy az ebből a példából is látszik. Ha reguláriskifejezés-entitást használ, a LUIS kinyeri a megnevezett adatokat, ami programozás szempontjából nagyobb segítséget nyújt a JSON-választ fogadó ügyfélalkalmazásnak.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag létrehozott egy új szándékot, példaszándékokat adott hozzá, majd létrehozott egy reguláriskifejezés-entitást a helyesen formázott adatok kimondott szövegből történő kinyerésének céljából. Az alkalmazás betanítása és közzététele után egy végpontlekérdezés azonosította a szándékot, és visszaadta a kinyert adatokat.

> [!div class="nextstepaction"]
> [Tudnivalók a listaentitásról](luis-quickstart-intent-and-list-entity.md)

