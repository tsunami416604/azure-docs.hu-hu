---
title: Hangulatelemzés
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban létrehoz egy alkalmazást, amely a pozitív, negatív és semleges vélemények lekérését a kimondott szöveg. A hangulat meghatározása a teljes kimondott szövegből történik.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 0e61f6a914c33842f4f42b2e1e4206b370a11dd4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099035"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>Oktatóanyag:  Vélemények az utterance (kifejezés) beolvasása

Ebben az oktatóanyagban hozzon létre egy alkalmazást, amely bemutatja, hogyan pozitív, negatív és semleges kimondott szöveg hangulatának megállapításához. A hangulat meghatározása a teljes kimondott szövegből történik.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Új alkalmazás létrehozása
> * Hangulatelemzés hozzáadása közzétételi beállításként
> * Alkalmazás betanítása
> * Alkalmazás közzététele
> * Kimondott szöveg hangulatának lekérése végpontból

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Hangulatelemzés a publish beállítást

Az alábbi kimondott szövegek a különböző hangulatokat példázzák:

|Hangulat|Pontszám|Kimondott szöveg|
|:--|:--|:--|
|pozitív|0,91 |John W. Smith remek prezentációt tartott Párizsban.|
|pozitív|0,84 |A Seattle mérnökök a Parker felébresztve ünnepi munka volt.|

A hangulatelemzés egy olyan közzétételi beállítás, amely minden kimondott szövegre vonatkozik. Nem rendelkezik az utterance (kifejezés) a vélemények jelző szó, és jelölje meg őket. 

Mivel ez közzétételi beállítás, a szándékok vagy az entitások oldalán nem jelenik meg; az [interaktív teszt](luis-interactive-test.md#view-sentiment-results) panelen vagy a végpont URL-címén való teszteléskor látható. 


## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>Adja hozzá a PersonName előre összeállított entitások 


1. Válassza az **Entities** (Entitások) elemet a bal oldali navigációs menüben.

1. Válassza az **Add prebuilt entity** (Előre összeállított entitás hozzáadása) gombot.

1. Adja meg a következő entitást előre összeállított entitások listájából, majd válassza ki **kész**:

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![Képernyőkép: számválasztó az előre összeállított entitások párbeszédpanelen](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>Egy alkalmazott visszajelzés meghatározásához leképezésének létrehozása

Adjon hozzá egy új szándékot a vállalat tagjaitól származó alkalmazotti visszajelzések rögzítéséhez. 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget.

3. Adja meg az új szándék nevét: `EmployeeFeedback`.

    ![Hozzon lére egy új, EmployeeFeedback nevű szándék párbeszédpanelt](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Adja hozzá több kimondott szöveget, amelyek azt jelzik, hogy az alkalmazott valamit jól végzett el, vagy amelyek olyan területet jelölnek, ahol fejlődni kell:

    |Beszédmódok|
    |--|
    |John Smith fejeződött üdvözlő vissza nagyszerű feladat egy munkatársnak a szülési|
    |Jill Jones egy remek, egy munkatársnak tőkeösszegű szeretteink az időt a volt.|
    |Bob Barnes nem kell minden szükséges számláit a dokumentumokat.|
    |A szükséges nyomtatványokat aláírás nélküli késői havonta Todd Thomas kapcsolva|
    |Katherine Tibor kezdeményezte, a fontos marketing külső helyszínen lévő értekezletre.|
    |Farkas Dillard az értekezlet június felülvizsgálatok nem talált.|
    |Megjelölés Mathews rocked a felébresztve Harvard:|
    |Walter Williams volt egy remek, Stanford bemutatása|

    [![Példa utterances EmployeeFeedback leképezés a képernyőkép a LUIS-alkalmazás](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>A none szándék példa beszédmódok hozzáadása 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanításához, így a módosítások a leképezés tesztelhető legyen 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Alkalmazás konfigurálása hangulatelemzés belefoglalásához

1. Válassza a **Manage** (Kezelés) lehetőséget a jobb felső navigációs területen, majd a **Publish settings** (Közzétételi beállítások) elemet a bal oldali menüben.

1. Válassza ki **Hangulatelemzés** a beállítás engedélyezése. 

    ![Hangulatelemzés bekapcsolása közzétételi beállításai](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Tegye közzé az alkalmazást, így a betanított modell lekérdezhető a végpontról

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>A végpont jelöli az utterance (kifejezés) beolvasása

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Lépjen az URL-cím végéhez, és írja be a következőt: `Jill Jones work with the media team on the public portal was amazing`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a következő szándékot kell visszaadnia kinyert hangulatelemzéssel: `EmployeeFeedback`.
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
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

    A sentimentAnalysis-egy pontszám 86 %-os pozitívnak kell lennie. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* Hangulatelemzés a Cognitive Services-szolgáltatás által biztosított [Szövegelemzés](../Text-Analytics/index.yml). A szolgáltatás korlátozódik, Szövegelemzés [támogatott nyelvek](luis-language-support.md##languages-supported).
* [Hogyan betanítása](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [A LUIS-portál tesztelése](luis-interactive-test.md)


## <a name="next-steps"></a>További lépések
Ez az oktatóanyag közzétételi beállításként hozzáadja a hangulatelemzést, amellyel átfogó hangulatértékek nyerhetők ki a kimondott szövegből.

> [!div class="nextstepaction"] 
> [Végponti kimondott szövegek áttekintése az Emberi erőforrás alkalmazásban](luis-tutorial-review-endpoint-utterances.md) 

