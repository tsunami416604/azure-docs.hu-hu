---
title: 'Rövid útmutató: Kimondott szövegek hozzáadása LUIS-alkalmazáshoz a JavaScript használatával – Azure Cognitive Services | Microsoft Docs'
description: Ez a rövid útmutató bemutatja, hogyan hívhat meg egy LUIS-alkalmazást a JavaScript használatával.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: 0920a194d3e9c93883b88b7131f7e81dc8fb3302
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159690"
---
# <a name="quickstart-change-model-using-javascript"></a>Rövid útmutató: Modell módosítása a JavaScript használatával

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Visual Studio Code](https://code.visualstudio.com/).

[!INCLUDE [Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Kimondott példaszövegek JSON-fájlja

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]


## <a name="create-quickstart-code"></a>A rövid útmutató kódjának létrehozása

Hozza létre a(z) `add-utterances.html` fájlt, és adja hozzá a következő kódot:

   [!code-html[Html code](~/samples-luis/documentation-samples/quickstarts/change-model/javascript/add-utterance.html "Javascript code")]

## <a name="run-code"></a>Kód futtatása

1. Nyissa meg a fájlt egy böngészőben.

2. Adja hozzá a LUIS-beli szerzői azonosítóját és a LUIS-alkalmazásazonosítóját.

3. Módosítsa az alkalmazáshoz hozzáadni kívánt **kimondott szövegek körét**. Ezeket az utteranceJSON változó tárolja. Módosítsa ezeket az értékeket a tartományokkal és kimondott szövegekkel kapcsolatos igényeinek megfelelően. 

    ```json
    // example batch utterances
    var utteranceJSON = [
        {
            "text": "go to Seattle",
            "intentName": "BookFlight",
            "entityLabels": [
                {
                    "entityName": "Location::LocationTo",
                    "startCharIndex": 6,
                    "endCharIndex": 12
                }
            ]
        }
    ,
        {
            "text": "book a flight",
            "intentName": "BookFlight",
            "entityLabels": []
        }
    ];
    ```

4. Kattintson a(z) `Upload utterance` gombra. A gombok alatt megjelennek a LUIS-eredmények.

5. Kattintson a(z) `Train model` gombra, hogy megtanítsa az alkalmazásnak az új kimondott szövegeket.

6. Kattintson a(z) `Train Status` gombra a betanítás állapotának megtekintéséhez. 

    ![Add-utterances.html](./media/luis-quickstart-javascript-add-utterance/add-utterance.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Miután végzett a rövid útmutatóval, távolítsa el a rövid útmutatóban létrehozott összes fájlt. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A LUIS integrálása robotokkal](luis-csharp-tutorial-build-bot-framework-sample.md)
