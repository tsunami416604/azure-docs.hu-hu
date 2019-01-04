---
title: Összetett entitást"
titleSuffix: Azure Cognitive Services
description: Adjon hozzá egy összetett entitást szeretné a különböző típusú kinyert adatok egyetlen tartalmazó entitásba. A kötegelés az adatokat, az ügyfélalkalmazás is könnyen kinyerheti az különböző adattípusok kapcsolódó adatokat.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 18a32f5e07470f71ba276fbe3a2633150b1bf188
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754664"
---
# <a name="tutorial-group-and-extract-related-data"></a>Oktatóanyag: Kapcsolódó adatok csoportosítása és kinyerése
Ebben az oktatóanyagban egy összetett entitás szeretné a kinyert adatokkal történő tartalmazó egyetlen entitás hozzáadása. A kötegelés az adatokat, az ügyfélalkalmazás is könnyen kinyerheti az különböző adattípusok kapcsolódó adatokat.

Az összetett entitás az a célja, hogy egy szülőentitás kategória kapcsolódó entitások csoportosíthatja. Az információk létezik külön entitásokként összetett létrehozása előtt. Ez hasonlít a hierarchikus entitás, de eltérő típusú entitásokat is tartalmazhat. 

Az összetett entitás ideális ehhez az adattípushoz, mert az adatok:

* Kapcsolódnak egymáshoz. 
* Többféle típusú entitás használja.
* Csoportosítását és feldolgozását az ügyfélalkalmazásoknak egy információegységként kell végezniük.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Példa-alkalmazás importálása
> * Szándék létrehozása
> * Összetett entitás hozzáadása 
> * Betanítás
> * Közzététel
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Példa-alkalmazás importálása

1.  Töltse le és mentse a [alkalmazás JSON-fájlt](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json) a lista entitás oktatóanyagot.

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `composite` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

## <a name="composite-entity"></a>Összetett entitást

Ebben az alkalmazásban a részleg neve van megadva a **részleg** entitás listában, és a szinonimák tartalmazza. 

A **TransferEmployeeToDepartment** szándékot rendelkezik egy alkalmazott áthelyezni egy új szervezeti kérelem például kimondott szöveg. 

Példa utterances ezt szándék a következők:

|Példák kimondott szövegekre|
|--|
|Helyezze át a könyvelési részleg W. Kovács János|
|R & D Jill Jones, a átvitele|
 
Az áthelyezési kérelemnek tartalmaznia kell a részleg nevét és az alkalmazott nevét. 

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Adja hozzá a PersonName közös adatkinyerés írja be annak előre összeállított entitások

A LUIS számos előre összeállított entitást biztosít a gyakori adatok kinyeréséhez. 

1. Válassza ki **összeállítása** a felső navigációs sávon, majd válassza ki **entitások** a bal oldali navigációs menüjében.

1. Válassza a **Manage prebuilt entity** (Előre összeállított entitás kezelése) gombot.

1. Válassza ki **[PersonName](luis-reference-prebuilt-person.md)** előre összeállított entitások listájából válassza ki **kész**.

    ![Képernyőkép: számválasztó az előre összeállított entitások párbeszédpanelen](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Ehhez az entitáshoz segítségével adhat hozzá az ügyfélalkalmazás neve felismerése.

## <a name="create-composite-entity-from-example-utterances"></a>Létre összetett entitást példa kimondott szöveg

1. A bal oldali menüben válassza az **Intents** (Szándékok) lehetőséget.

1. Válassza ki **TransferEmployeeToDepartment** leképezések listájából.

1. Az első utterance (kifejezés), válassza ki a personName entitást `John Jackson`, majd **Start összetett entitást alkalmazásburkoló** a legördülő menü listája a következő utterance (kifejezés):

    `place John Jackson in engineering`

1. Azonnal válassza ki az utolsó entitás `engineering` az utterance (kifejezés) található. Zöld sáv megjelenítése a kiválasztott szavakat jelző egy összetett entitás alapján. Az előugró menüben adja meg az összetett név `TransferEmployeeInfo` majd válassza ki az Entert. 

1. A **milyen típusú entitást szeretne létrehozni?**, minden kötelező mezőt a listában szerepelnek: `personName` és `Department`. Válassza a **Done** (Kész) lehetőséget. 

    Figyelje meg, az összetett entitás hozzáadásának personName, előre létrehozott entitást. Ha egy előre összeállított entitások jelennek meg a kezdő és záró jogkivonatok összetett entitás között lehetnek, az összetett entitás ezeket előre összeállított entitások kell tartalmaznia. Ha az előre összeállított entitások nem szerepelnek, az összetett entitás nem jelzett megfelelően, de az egyes elemeket.

## <a name="label-example-utterances-with-composite-entity"></a>Az összetett entitás címke példa kimondott szöveg


1. Valamennyi példa utterance (kifejezés) válassza ki a bal szélső entitást, amely az összetett kell lennie. Válassza ki **Zabalit do összetett entitást**.

1. Válassza ki az utolsó szót az összetett entitásban, majd válassza ki **TransferEmployeeInfo** az előugró menüben. 

1. Ellenőrizze, hogy a célt az összes utterances vannak ellátva, az összetett entitáshoz. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanításához, így a módosítások a leképezés tesztelhető legyen 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Tegye közzé az alkalmazást, így a betanított modell lekérdezhető a végpontról

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Leképezés és egyéb entitások előrejelzés beolvasása végpont 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Move Jill Jones to DevOps`. Az utolsó lekérdezési karakterlánc paraméter `q`, az utterance (kifejezés) lekérdezést. 

    Mivel ez a teszt ellenőrzése az összetett ki kell olvasni megfelelően, egy tesztet vagy tartalmazhat egy meglévő minta utterance (kifejezés) vagy egy új utterance (kifejezés). Ellenőrzi, hogy a gyermekentitások tartalmazza az összetett entitásban.

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

  Az utterance (kifejezés) egy összetett entitások tömböt ad vissza. Minden entitás egy típusa és értéke van megadva. Minden gyermek entitásnak pontossággal megkereséséhez használja a megfelelő elem található entitások tömbben típusa és értéke összetett tömb elemének kombinációja.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Lista entitás oktatóanyag](luis-quickstart-intents-only.md)
* [Összetett entitást](luis-concept-entity-types.md) elméleti információk
* [Hogyan betanítása](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [A LUIS-portál tesztelése](luis-interactive-test.md)


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy összetett entitást, hogy magába foglalja a meglévő entitásokat. Ez lehetővé teszi, hogy az ügyfélalkalmazás található kapcsolódó adatok csoportja folytatja a beszélgetés különböző adattípusokat. Egy ügyfélalkalmazás az emberi erőforrások alkalmazás megkérheti, mely napokon és időpontban az áthelyezés kell kezdődik és ér véget. Azt is kérheti kapcsolatos egyéb logisztikai az áthelyezési például fizikai telefonon. 

> [!div class="nextstepaction"] 
> [Egy kifejezés listát egy egyszerű entitás hozzáadása](luis-quickstart-primary-and-secondary-data.md)  
