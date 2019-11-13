---
title: 'Oktatóanyag: pontos szöveges egyezés – LUIS'
titleSuffix: Azure Cognitive Services
description: Elemek előre meghatározott listájával egyező adatok lekérése. A lista minden elemének lehetnek pontosan megegyező szinonimái
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: a8021885bf51ab6d44bc8576b9fdd69f1bdd270a
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953702"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>Oktatóanyag: pontos szöveggel egyező adatok beolvasása a teljes használatból

Ebből az oktatóanyagból megtudhatja, hogyan kérhet le olyan entitás-adatokat, amelyek megfelelnek az előre meghatározott elemek listájának. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Alkalmazás létrehozása
> * Szándék hozzáadása
> * Listaentitás hozzáadása 
> * Betanítás 
> * Közzététel
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Mi a lista entitás?

A lista entitások pontos szövegnek felelnek meg a kifejezésben szereplő szavaknak. 

A lista minden eleme tartalmazhatja szinonimák egy listáját. A Human Resources alkalmazás esetében a vállalati részlegek több fontos információval azonosíthatók, például a hivatalos név, a közös betűszók és a számlázási részleg kódjai. 

Az emberi erőforrások alkalmazásnak meg kell határoznia azt az osztályt, amelyre az alkalmazott át lett ruházva. 

A listaentitás megfelelő választás az ilyen típusú adatok esetén, amikor:

* Az adatok értékei egy ismert készletbe tartoznak.
* A készlet nem haladja meg a LUIS maximális [határait](luis-boundaries.md) ezen entitástípus esetében.
* A kimondott szöveg egy része pontosan megegyezik egy szinonimával vagy a kanonikus névvel. LUIS nem használja a listát a pontos szöveges egyezéseken felül. A kivezetés, a többes szám és más változat nem oldható fel egyetlen listával rendelkező entitással. A változatok kezeléséhez érdemes [mintát](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) használni a választható szöveges szintaxissal. 

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>Hozzon létre egy szándékot, hogy az alkalmazottakat egy másik részlegbe vigye át

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

3. Az előugró párbeszédpanelen írja be a `TransferEmployeeToDepartment` karakterláncot, majd válassza a **Kész** elemet. 

    ![Új szándék létrehozása párbeszédpanel képernyőképe](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Adjon hozzá kimondott példaszövegeket a szándékhoz.

    |Példák kimondott szövegekre|
    |--|
    |John W. Smith áthelyezése a nyilvántartási részlegbe|
    |Jill Jones átvitele az R & D-re|
    |A dept 1234 nevű új tag a Bill Bradstreettől|
    |John Jackson elhelyezése a mérnöki tudományok területén |
    |Debra Doughtery áthelyezése a belső értékesítésbe|
    |MV Jill Jones|
    |Alice Anderson áthelyezése a DevOps|
    |Carl Chamerlin – Pénzügy|
    |Steve Standish – 1234|
    |Tanner Thompson – 3456|

    [![Képernyőkép – példa hosszúságú kimondott szöveg](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "Képernyőkép – példa hosszúságú kimondott szöveg")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>Részlegek listája entitás

Most, hogy a **TransferEmployeeToDepartment** szándéka például a hosszúságú kimondott szöveg, Luis meg kell ismernie, mi az a részleg. 

Az egyes elemek elsődleges, _kanonikus_és neve a részleg neve. Az egyes kanonikus nevek szinonimái például a következők: 

|Kanonikus név|Szinonimák|
|--|--|
|Könyvelés|Acct<br>Acct<br>3456|
|Fejlesztési műveletek|Fejlesztés és üzemeltetés<br>4949|
|Mérnöki tevékenységek|ENG<br>naplófeladat<br>4567|
|Pénzügy|Pénzügy<br>2020|
|Információs technológia|IT<br>2323|
|Értékesítésen belül|isale<br>nem értékesíthető<br>1414|
|Kutatás és fejlesztés|R & D<br>1234|

1. Válassza az **Entities** (Entitások) elemet a bal oldali ablaktáblán.

1. Válassza a **Create new entity** (Új entitás létrehozása) lehetőséget.

1. A felugró párbeszédpanelen adja meg az `Department` értéket az entitás neveként, és a **List** (Lista) értéket az entitás típusaként. Válassza a **Done** (Kész) lehetőséget.  

    [![Képernyőfelvétel: új entitás előugró ablakának létrehozása párbeszédpanel](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "Képernyőfelvétel: új entitás előugró ablakának létrehozása párbeszédpanel")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. Az részleg entitása lapon adja meg a `Accounting` értéket az új értékként.

1. Szinonimák esetében adja hozzá az előző táblázat szinonimáit.

1. Folytassa az összes kanonikus név és a hozzá tartozó szinonimák hozzáadásával. 

## <a name="add-example-utterances-to-the-none-intent"></a>Példa hosszúságú kimondott szöveg hozzáadása a none szándékhoz 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanítása, hogy tesztelni lehessen a szándék változásait 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Tegye közzé az alkalmazást, hogy a betanított modell lekérdezhető legyen a végpontról.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Cél-és entitás-előrejelzés beolvasása a végpontról

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Lépjen az URL-cím végéhez, és írja be a következőt: `shift Joe Smith to IT`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **q**uery. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a `TransferEmployeeToDepartment` szándékot kell visszaadnia, kinyerve a következőt: `Department`.

   ```json
    {
      "query": "shift Joe Smith to IT",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9775754
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9775754
        },
        {
          "intent": "None",
          "score": 0.0154493852
        }
      ],
      "entities": [
        {
          "entity": "it",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 20,
          "resolution": {
            "values": [
              "Information Technology"
            ]
          }
        }
      ]
    }
   ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* Entitás fogalmi információinak [listázása](luis-concept-entity-types.md#list-entity)
* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)


## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag létrehozott egy új szándékot, kimondottszöveg-példákat adott hozzá, majd létrehozott egy listaentitást a pontos szövegegyezések kimondott szövegből történő kinyerése céljából. Az alkalmazás betanítása és közzététele után egy végpontlekérdezés azonosította a szándékot, és visszaadta a kinyert adatokat.

Folytassa az alkalmazással, és [adjon hozzá egy összetett entitást](luis-tutorial-composite-entity.md).

> [!div class="nextstepaction"]
> [Előre összeépített entitás hozzáadása az alkalmazáshoz tartozó szerepkörrel](tutorial-entity-roles.md)

