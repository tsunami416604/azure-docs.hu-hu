---
title: 'Oktatóanyag: Összetett entitás oktatóanyaga – LUIS'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban adjon hozzá egy összetett entitást a kinyert adatok kötegeléséhez különböző típusú egyetlen entitásba. Az adatok kötegelésével az ügyfélalkalmazás könnyen kinyerheti a kapcsolódó adatokat a különböző adattípusokban.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: f2b2c3f52610cd9fae0845b15aebf032a088000b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75447949"
---
# <a name="tutorial-group-and-extract-related-data"></a>Oktatóanyag: Kapcsolódó adatok csoportosítása és kinyerése
Ebben az oktatóanyagban adjon hozzá egy összetett entitást a kinyert adatok kötegeléséhez különböző típusú egyetlen entitásba. Az adatok kötegelésével az ügyfélalkalmazás könnyen kinyerheti a kapcsolódó adatokat a különböző adattípusokban.

Az összetett entitás célja, hogy a kapcsolódó entitásokat egy fölérendelt kategória entitásba csoportosítsa. Az információ különálló entitásokként létezik az összetett elem létrehozása előtt.

Az összetett entitás jól illeszkedik az ilyen típusú adatokhoz, mivel az adatok:

* Kapcsolódnak egymáshoz.
* Használjon különböző entitástípusokat.
* Csoportosítását és feldolgozását az ügyfélalkalmazásoknak egy információegységként kell végezniük.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Példaalkalmazás importálása
> * Szándék létrehozása
> * Összetett entitás hozzáadása
> * Betanítás
> * Közzététel
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Példaalkalmazás importálása

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](
https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json?raw=true) a List entitás oktatóanyagából.

2. Importálja a JSON-t egy új alkalmazásba a [LUIS portál](https://www.luis.ai)használatával.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `composite` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

## <a name="composite-entity"></a>Összetett entitás

Ebben az alkalmazásban a részleg neve a **Részleg** lista entitásban van definiálva, és szinonimákat tartalmaz.

A **TransferEmployeeToDepartment** szándék példakimondott szöveggel rendelkezik, amely egy alkalmazott áthelyezését kéri egy új részlegbe.

Példa utterances for this intent include:

|Példák kimondott szövegekre|
|--|
|John W. Smith áthelyezése a számviteli osztályra|
|át Jill Jones-tól az R&D|

Az áthelyezési kérelemnek tartalmaznia kell a részleg nevét és az alkalmazott nevét.

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>A PersonName előre összeállított entitás hozzáadása a gyakori adattípus-kinyeréshez

A LUIS számos előre összeállított entitást biztosít a gyakori adatok kinyeréséhez.

1. Válassza a legfelső navigációs sáv **Build parancsát,** majd a bal oldali navigációs menü **Entitások** parancsát.

1. Válassza a **Manage prebuilt entity** (Előre összeállított entitás kezelése) gombot.

1. Válassza a **[Személynév](luis-reference-prebuilt-person.md)** elemet az előre összeállított entitások listájában, majd válassza a **Kész**lehetőséget.

    ![Képernyőkép: számválasztó az előre összeállított entitások párbeszédpanelen](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Ez az entitás segít a névfelismerés hozzáadásában az ügyfélalkalmazáshoz.

## <a name="create-composite-entity-from-example-utterances"></a>Összetett entitás létrehozása példakimondottszövegből

1. A bal oldali menüben válassza az **Intents** (Szándékok) lehetőséget.

1. Válassza a **TransferEmployeeToDepartment** elemet a szándékok listájából.

1. Az utterance `place John Jackson in engineering`(kifejezés) menüben `John Jackson`válassza ki a personName entitást, majd válassza a **Wrap in composite entity (Összetett entitás) elemet** a következő kimondott szöveg legördülő listában.

    ![Képernyőkép: a körbefolyatási kompozit kijelölése a legördülő párbeszédpanelen](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Ezután azonnal válassza ki `engineering` az utolsó entitás, az utterance (kifejezés). A kijelölt szavak alatt egy zöld sáv rajzolódik, amely összetett entitást jelöl. A legördülő menüben írja be `TransferEmployeeInfo` az összetett nevet, majd válassza a Belépés lehetőséget.

    ![Képernyőkép: összetett név beírása a legördülő menüből](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. A **Milyen típusú entitást szeretne létrehozni?** `personName` `Department` Válassza a **Done** (Kész) lehetőséget. Figyelje meg, hogy az előre összeállított entitás, personName, hozzá lett adva az összetett entitáshoz. Ha egy összetett entitás kezdő és záró jogkivonatai között egy előre összeállított entitás jelenhet meg, az összetett entitásnak tartalmaznia kell ezeket az előre összeállított entitásokat. Ha az előre összeállított entitások nem szerepelnek, az összetett entitás nem megfelelően előre jelzett, de minden egyes elem.

    ![Képernyőkép: összetett név beírása a legördülő menüből](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Címkepélda kimondott szöveg kompozit entitással

1. Minden példa utterance (kifejezés) válassza ki a bal szélső entitás, amely az összetettben kell lennie. Ezután válassza **a Körbefolyatás elemet összetett entitásban**.

1. Jelölje ki az utolsó szót az összetett entitásban, majd válassza a **TransferEmployeeInfo** lehetőséget a legördülő menüből.

1. Ellenőrizze, hogy a szándék összes utterances a szándék van címkézve az összetett entitás.

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanítása a szándék módosításai tesztelése érdekében

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Az alkalmazás közzététele, hogy a betanított modell lekérdezhető a végpontról

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Leképezés és entitás-előrejelzés beszerezni a végponttól

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Move Jill Jones to DevOps`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a query.

    Mivel ez a teszt a kompozit megfelelő kibontásának ellenőrzése, a teszt tartalmazhat egy meglévő minta utterance (kifejezés) vagy egy új utterance (kifejezés) is. Egy jó teszt, hogy tartalmazza az összes gyermek entitások az összetett entitás.

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   Ez az utterance (kifejezés) egy összetett entitástömböt ad vissza. Minden entitás kap egy típust és értéket. Ha minden egyes alárendelt entitáshoz pontosabb anamforitást szeretne találni, az összetett tömbelem típus- és értékkombinációjával keresse meg a megfelelő elemet az entitástömbben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Entitás oktatóanyagának listázása](luis-quickstart-intents-only.md)
* [Összetett entitás](luis-concept-entity-types.md) koncepcionális információi
* [Hogyan kell a vonat](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag létrehozott egy összetett entitást a meglévő entitások beágyazására. Ez lehetővé teszi, hogy az ügyfélalkalmazás megtalálja a különböző adattípusokkapcsolódó adatainak egy csoportját a beszélgetés folytatásához. Az emberi erőforrások alkalmazás ügyfélalkalmazása megkérdezheti, hogy az áthelyezés nek mikor és mikor kell elkezdenie és le kell tennie. Azt is kérdezni más logisztikai a lépés, mint például a fizikai telefon.

> [!div class="nextstepaction"]
> [Bizonytalan előrejelzések javítása a végpontkimondott szöveg áttekintésével](luis-tutorial-review-endpoint-utterances.md)
