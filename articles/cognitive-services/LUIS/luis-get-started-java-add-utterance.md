---
title: 'Oktatóanyag: Kimondott szövegek hozzáadása LUIS-alkalmazáshoz a Java használatával | Microsoft Docs'
description: Ez az oktatóanyag bemutatja, hogyan hívhat meg egy LUIS-alkalmazást a Java használatával.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 5b9c7b90ca96b23fbabfeb1e1d06e4124e65a0dc
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266039"
---
# <a name="tutorial-add-utterances-to-app-using-java"></a>Oktatóanyag: Kimondott szövegek hozzáadása alkalmazáshoz a Java használatával 
Ebben az oktatóanyagban olyan programot fog írni Java nyelven a szerzői API-kkal, amely kimondott szövegeket ad hozzá egy szándékhoz.

<!-- green checkmark -->
> [!div class="checklist"]
> * Visual Studio-konzolprojekt létrehozása 
> * Metódus hozzáadása a LUIS API meghívására kimondott szöveg hozzáadásához és az alkalmazás betanításához
> * JSON-fájl hozzáadása példa kimondott szövegekkel BookFlight szándékhoz
> * Konzol futtatása és a kimondott szövegek betanítási állapotának megtekintése

További információért tekintse meg a [példa kimondott szöveg hozzáadása szándékhoz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [betanítás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) és [betanítási állapot](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API-k műszaki dokumentációját.

Ehhez a cikkhez egy ingyenes [LUIS][LUIS]-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

* Az Oracle [**Java/JDK**](http://www.oracle.com/technetwork/java/javase/downloads/index.html) legújabb verziója.
* [A Google GSON JSON-kódtára](https://github.com/google/gson).
* A LUIS **[szerzői kulcsa](luis-concept-keys.md#authoring-key)**. A kulcsot a [LUIS](luis-reference-regions.md) webhelyének Account Settings (Fiókbeállítások) részén találhatja meg.
* A LUIS meglévő [**alkalmazásazonosítója**](./luis-get-started-create-app.md). Az alkalmazásazonosító az alkalmazás irányítópultján látható. Az `utterances.json` fájlban használt szándékokat és entitásokat tartalmazó LUIS alkalmazásnak léteznie kell a kód `AddUtterances.java` környezetben való futtatása előtt. A jelen cikkben található kód nem hozza létre a szándékokat és az entitásokat. Mindössze a kimondott szövegeket adja hozzá a meglévő szándékokhoz és entitásokhoz. 
* A kimondott szövegeket fogadó alkalmazáson belüli **verzióazonosító**. Az alapértelmezett azonosító: „0.1”.
* Hozzon létre egy új szöveges fájlt `AddUtterances.java` néven.

> [!NOTE] 
> A teljes `add-utterances.cs` fájl és az `utterances.json` példafájl letölthető a [**LUIS-Samples** GitHub-adattárból](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/authoring-api-samples/java).


## <a name="write-the-java-code"></a>A Java-kód megírása

Adja hozzá a Java-függőségeket a fájlhoz.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=31-34 "Java Dependencies")]

`AddUtterances` osztály létrehozása

```Java
public class AddUtterances {
    // Insert code here
}
```

Ehhez az osztály tartalmazza az összes következő kódrészletet.

Adja hozzá a LUIS-állandókat az osztályhoz. Másolja ki az alábbi kódot, és módosítsa a szerzői kulcsnak, az alkalmazásazonosítónak és a verzióazonosítónak megfelelően.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=41-53 "LUIS-based IDs")]

Adja hozzá a LUIS API-ba meghívásokat intéző metódust. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=55-178 "HTTP request to LUIS")]

Adja hozzá a LUIS API HTTP-válaszait intéző metódust.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=180-226 "HTTP response from LUIS")]


Adja hozzá a kivételkezelést. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=228-256 "Exception Handling")]

Adja hozzá a kimenetkezelést és a nyomtatáskezelést.

   [!code-java[Add output handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=258-267 "Add configuration information for adding utterance")]

Adja hozzá a fő függvényt.

   [!code-java[Add main function](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=269-345 "Add main function")]


## <a name="specify-utterances-to-add"></a>A hozzáadni kívánt kimondott szövegek megadása
Az `utterances.json` fájl létrehozásával és módosításával adhatja meg a LUIS-alkalmazáshoz hozzáadni kívánt **kimondott szövegek körét**. A szándéknak és az entitásoknak már létezniük **kell** a LUIS-alkalmazásban.

> [!NOTE]
> Az `utterances.json` fájlban használt szándékokat és entitásokat tartalmazó LUIS alkalmazásnak léteznie kell a kód `AddUtterances.java` környezetben való futtatása előtt. A jelen cikkben található kód nem hozza létre a szándékokat és az entitásokat. Mindössze a kimondott szövegeket adja hozzá a meglévő szándékokhoz és entitásokhoz.

A `text` mező a kimondott szöveget tartalmazza. Az `intentName` mezőnek a LUIS-alkalmazásban található szándék nevének kell megfelelnie. Az `entityLabels` mező kötelező. Ha nem szeretne entitásokat megcímkézni, adjon meg egy üres listát az alábbi példában látható módon.

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

A függőségeket tartalmazó AddUtterance fordítása

```
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

Az `AddUtterance` argumentumok nélkül történő meghívása betanítás nélkül adja hozzá a LUIS-beli kimondott szövegeket az alkalmazáshoz.
````
> java -classpath .;gson-2.8.2.jar AddUtterances
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
Hívja meg az `add-utterance` parancsot a `-train` argumentummal a betanításra vonatkozó kérés elküldéséhez. 

````
> java -classpath .;gson-2.8.2.jar AddUtterances -train
````

> [!NOTE]
> Az ismétlődő kimondott szövegeket a rendszer nem adja újra hozzá, ez azonban nem okoz hibát. A `response` az eredeti kimondott szöveg azonosítóját tartalmazza.

Amikor a mintát a `-train` argumentummal hívja meg, a minta egy `training-results.json` fájlt hoz létre, amely jelzi, hogy a LUIS-alkalmazás betanítására irányuló kérés üzenetsorba helyezése sikeres volt. 

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
A betanítás állapotának ellenőrzéséhez és az állapotinformációk fájlba írásához hívja meg az alkalmazást a `-status` argumentummal.

````
> java -classpath .;gson-2.8.2.jar AddUtterances -status
````

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az oktatóanyag befejezését követően távolítsa el a Visual Studiót és a konzolalkalmazást, ha már nincs szüksége rájuk. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [LUIS-alkalmazások felépítése programozott módon](luis-tutorial-node-import-utterances-csv.md) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website