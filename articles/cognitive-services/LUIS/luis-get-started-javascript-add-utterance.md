---
title: 'Oktatóanyag: Kimondott szövegek hozzáadása LUIS-alkalmazáshoz a JavaScript használatával | Microsoft Docs'
description: Ez az oktatóanyag bemutatja, hogyan hívhat meg egy LUIS-alkalmazást a JavaScript használatával.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: v-geberr
ms.openlocfilehash: b6d021dcfdddb5449aa989c6aa06d7faf326befb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265459"
---
# <a name="tutorial-add-utterances-to-app-using-javascript"></a>Oktatóanyag: Kimondott szövegek hozzáadása alkalmazáshoz a JavaScript használatával
Ebben az oktatóanyagban olyan programot fog írni JavaScript nyelven a szerzői API-kkal, amely kimondott szöveget ad hozzá egy szándékhoz.

<!-- green checkmark -->
> [!div class="checklist"]
> * Visual Studio-konzolprojekt létrehozása 
> * Metódus hozzáadása a LUIS API meghívására kimondott szöveg hozzáadásához és az alkalmazás betanításához
> * JSON-fájl hozzáadása példa kimondott szövegekkel BookFlight szándékhoz
> * Konzol futtatása és a kimondott szövegek betanítási állapotának megtekintése

További információért tekintse meg a [példa kimondott szöveg hozzáadása szándékhoz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [betanítás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) és [betanítási állapot](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API-k műszaki dokumentációját.

Ehhez a cikkhez egy ingyenes [LUIS][LUIS]-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="prerequisites"></a>Előfeltételek
* A LUIS [**szerzői kulcsa**](luis-concept-keys.md#authoring-key). 
* A meglévő LUIS-**alkalmazásazonosító** és **-verzióazonosító**. 
* Egy új fájl `add-utterances.html` projekt néven a VSCode-ban.

> [!NOTE] 
> A teljes `add-utterances.html` fájl letölthető a [**LUIS-Samples** GitHub-adattárból](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/javascript/add-utterance.html).


## <a name="write-the-code"></a>A kód írása
Hozza létre a(z) `add-utterances.html` fájlt, és adja hozzá a következő kódot:

   [!code-javascript[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/javascript/add-utterance.html "Java Dependencies")]

## <a name="view-in-browser"></a>Megtekintés böngészőben
1. Nyissa meg a fájlt egy böngészőben.

2. Adja hozzá a LUIS-beli szerzői azonosítóját, a LUIS-alkalmazásazonosítóját, és módosítsa a verziót, ha az értéke nem `0.1`

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
Az oktatóanyag befejezését követően távolítsa el a Visual Studiót és a konzolalkalmazást, ha már nincs szüksége rájuk. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A LUIS integrálása robotokkal](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website