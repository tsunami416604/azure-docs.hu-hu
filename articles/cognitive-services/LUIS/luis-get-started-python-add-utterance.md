---
title: 'Oktatóanyag: Beszédmódok hozzáadása LUIS-alkalmazáshoz a Python használatával | Microsoft Docs'
description: Ez az oktatóanyag bemutatja, hogyan hívhat meg egy LUIS-alkalmazást a Python használatával.
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 3e1ca2ea874b6b39ac8a1b1eaa94fe9ff1894ea3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264301"
---
# <a name="tutorial-add-utterances-to-app-using-python"></a>Oktatóanyag: Beszédmódok hozzáadása alkalmazáshoz a Python használatával
Ebben az oktatóanyagban olyan programot írhat Python nyelven a szerzői API-k használatával, amely kimondott szöveget ad hozzá egy szándékhoz.

<!-- green checkmark -->
> [!div class="checklist"]
> * Visual Studio-konzolprojekt létrehozása 
> * Metódus hozzáadása a LUIS API meghívására kimondott szöveg hozzáadásához és az alkalmazás betanításához
> * JSON-fájl hozzáadása példa kimondott szövegekkel BookFlight szándékhoz
> * Konzol futtatása és a kimondott szövegek betanítási állapotának megtekintése

További információért tekintse meg a [példaként használt kimondott szöveg hozzáadása szándékhoz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45), [betanítás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) és [betanítási állapot](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API-k műszaki dokumentációját.

Ehhez a cikkhez egy ingyenes [LUIS][LUIS]-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.6](https://www.python.org/downloads/) vagy újabb.
* **[Ajánlott]** [Visual Studio Code](https://code.visualstudio.com/) az IntelliSense és a hibakeresés használatához.
* A LUIS **[szerzői kulcsa](luis-concept-keys.md#authoring-key)**. A kulcsot a [LUIS](luis-reference-regions.md) webhelyének Account Settings (Fiókbeállítások) részén találhatja meg.
* A LUIS meglévő [**alkalmazásazonosítója**](./luis-get-started-create-app.md). Az alkalmazásazonosító az alkalmazás irányítópultján látható. Az `utterances.json` fájlban használt szándékokat és entitásokat tartalmazó LUIS alkalmazásnak léteznie kell a kód `add-utterances.js` környezetben való futtatása előtt. A jelen cikkben található kód nem hozza létre a szándékokat és az entitásokat. Csak a meglévő szándékokhoz és entitásokhoz adja hozzá a kimondott szövegeket. 
* A kimondott szövegeket fogadó alkalmazáson belüli **verzióazonosító**. Az alapértelmezett azonosító: „0.1”.
* Hozzon létre egy új fájlt `add-utterances-3-6.py` néven a VSCode-ban.

> [!NOTE] 
> A teljes `add-utterances-3-6.py` fájl letölthető a [**LUIS-Samples** GitHub-adattárból](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/authoring-api-samples/python).


## <a name="write-the-python-code"></a>A Python-kód megírása

1. Másolja az alábbi kódrészleteket:

   [!code-python[Console app code that adds an utterance Python 3.6](~/samples-luis/documentation-samples/authoring-api-samples/python/add-utterances-3-6.py)]

## <a name="specify-utterances-to-add"></a>A hozzáadni kívánt kimondott szövegek megadása
Az `utterances.json` fájl létrehozásával és módosításával adhatja meg a LUIS-alkalmazáshoz hozzáadni kívánt **kimondott szövegek körét**. A szándéknak és az entitásoknak már létezniük **kell** a LUIS-alkalmazásban.

> [!NOTE]
> Az `utterances.json` fájlban használt szándékokat és entitásokat tartalmazó LUIS alkalmazásnak léteznie kell a kód `add-utterances.js` környezetben való futtatása előtt. A jelen cikkben található kód nem hozza létre a szándékokat és az entitásokat. Csak a meglévő szándékokhoz és entitásokhoz adja hozzá a kimondott szövegeket.

A `text` mező a kimondott szöveget tartalmazza. Az `intentName` mezőnek a LUIS-alkalmazásban található szándék nevének kell megfelelnie. Az `entityLabels` mező kötelező. Ha nem szeretne entitásokat megcímkézni, adjon meg egy üres listát az alábbi példában látható módon:

Ha az entityLabels lista nem üres, a `startCharIndex` és az `endCharIndex` indexnek az `entityName` mezőben hivatkozott entitást kell jelölnie. Mindkét index nullaalapú darabszám, így a fenti példában a 6 a Seattle szó „S” betűjére hivatkozik, nem pedig a nagy S előtt található szóközre.

```json
[
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
    },
    {
        "text": "book a flight",
        "intentName": "BookFlight",
        "entityLabels": []
    }
]
```

## <a name="add-an-utterance-from-the-command-line"></a>Beszédmód hozzáadása a parancssorból

Futtassa az alkalmazást egy parancssorból a Python 3.6 segítségével.

Az add-utterance argumentumok nélkül történő meghívása betanítás nélkül ad hozzá kimondott szöveget az alkalmazáshoz.

````
> python add-utterances-3-6.py
````

A minta létrehozza a kimondott szövegek hozzáadására szolgáló API meghívásának az eredményét tartalmazó fájlt a `results.json` használatával. A `response` mező ebben a formátumban a hozzáadott kimondott szövegeket jelzi. A `hasError` értéke false (hamis), ami azt jelzi, hogy a kimondott szöveg hozzáadása megtörtént.  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

## <a name="add-an-utterance-and-train-from-the-command-line"></a>Beszédmód hozzáadása és betanítás a parancssorból
Hívja meg az add-utterance parancsot a `-train` argumentummal a betanításra, majd a betanítás állapotának a lekérése vonatkozó kérés elküldéséhez. A betanítás kezdetét követően a rendszer azonnal várólistára helyezi a kérést. Az állapotinformációkat egy fájlba írja.

````
> python add-utterances-3-6.py -train
````

> [!NOTE]
> Az ismétlődő kimondott szövegeket a rendszer nem adja újra hozzá, ez azonban nem okoz hibát. A `response` az eredeti kimondott szöveg azonosítóját tartalmazza.

Amikor a mintát a `-train` argumentummal hívja meg, a minta egy `training-results.json` fájlt hoz létre, amely jelzi, hogy a LUIS-alkalmazás betanítására irányuló kérés várólistára helyezése sikeres volt. 

Az alábbiakban a betanításra irányuló sikeres kérés eredménye látható:
```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

A betanításra irányuló kérés üzenetsorba való helyezése után kis időbe telhet a betanítás végrehajtása.

## <a name="get-training-status-from-the-command-line"></a>A betanítás állapotának lekérése a parancssorból
A betanítás állapotának ellenőrzéséhez és az állapotinformációk fájlba írásához hívja meg a mintát a `-status` argumentummal.

````
> python add-utterances-3-6.py -status
````
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az oktatóanyag befejezését követően távolítsa el a Visual Studiót és a konzolalkalmazást, ha már nincs szüksége rájuk. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [LUIS-alkalmazások felépítése programozott módon](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website

