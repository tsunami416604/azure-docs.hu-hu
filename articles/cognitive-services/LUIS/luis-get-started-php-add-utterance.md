---
title: 'Oktatóanyag: Beszédmódok hozzáadása LUIS-alkalmazáshoz a PHP használatával | Microsoft Docs'
description: Ez az oktatóanyag bemutatja, hogyan hívhat meg egy LUIS-alkalmazást a PHP használatával.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 59150b7ed6782c28f243041be2ed6aa17e69cc01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263780"
---
# <a name="tutorial-add-utterances-to-app-using-php"></a>Oktatóanyag: Beszédmódok hozzáadása alkalmazáshoz a PHP használatával 
Ebben az oktatóanyagban olyan programot írhat PHP nyelven a szerzői API-k használatával, amely kimondott szöveget ad hozzá egy szándékhoz.

<!-- green checkmark -->
> [!div class="checklist"]
> * Visual Studio-konzolprojekt létrehozása 
> * Metódus hozzáadása a LUIS API meghívására kimondott szöveg hozzáadásához és az alkalmazás betanításához
> * JSON-fájl hozzáadása példa kimondott szövegekkel BookFlight szándékhoz
> * Konzol futtatása és a kimondott szövegek betanítási állapotának megtekintése

További információért tekintse meg a [példa kimondott szöveg hozzáadása szándékhoz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [betanítás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) és [betanítási állapot](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API-k műszaki dokumentációját.

Ehhez a cikkhez egy ingyenes [LUIS][LUIS]-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

* A [**PHP**](http://php.net/) legújabb verziója.
* Az openssl álljon rendelkezésre a PHP függőségeként.  
* A LUIS **[szerzői kulcsa](luis-concept-keys.md#authoring-key)**. A kulcsot a [LUIS](luis-reference-regions.md) webhelyének Account Settings (Fiókbeállítások) részén találhatja meg.
* A LUIS meglévő [**alkalmazásazonosítója**](./luis-get-started-create-app.md). Az alkalmazásazonosító az alkalmazás irányítópultján látható. Az `utterances.json` fájlban használt szándékokat és entitásokat tartalmazó LUIS alkalmazásnak léteznie kell a kód `add-utterances.php` környezetben való futtatása előtt. A jelen cikkben található kód nem hozza létre a szándékokat és az entitásokat. Csak a meglévő szándékokhoz és entitásokhoz adja hozzá a kimondott szövegeket. 
* A kimondott szövegeket fogadó alkalmazáson belüli **verzióazonosító**. Az alapértelmezett azonosító: „0.1”.
* Hozzon létre egy új fájlt `add-utterances.php` néven a VSCode-ban.

> [!NOTE] 
> A teljes `add-utterances.cs` fájl és az `utterances.json` példafájl letölthető a [**LUIS-Samples** GitHub-adattárból](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/php/).


## <a name="write-the-php-code"></a>A PHP-kód írása

Adja hozzá a függőségeket a fájlhoz.

   [!code-php[PHP and LUIS Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=10-29 "PHP and LUIS Dependencies")]

Adja hozzá a betanítási állapot megtekintéséhez használt GET kérést.

   [!code-php[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=31-50 "SendGet")]

Adja hozzá a kimondott szövegek létrehozásához vagy a betanítás indításához használt POST kérést. 

   [!code-php[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=52-72 "SendPost")]

Adja hozzá a `AddUtterances` függvényt.

   [!code-php[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=74-79 "AddUtterances method")]


Adja hozzá a `Train` függvényt. 

   [!code-php[Train](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=81-88 "Train")]

Adja hozzá a `Status` függvényt.

   [!code-php[Status](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=90-94 "Status")]

Adja hozzá a fő kódblokkot a parancssori argumentumok kezeléséhez.

   [!code-php[Main code](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=96-120 "Main code")]

## <a name="specify-utterances-to-add"></a>A hozzáadni kívánt kimondott szövegek megadása
Az `utterances.json` fájl létrehozásával és módosításával adhatja meg a LUIS-alkalmazáshoz hozzáadni kívánt **kimondott szövegek körét**. A szándéknak és az entitásoknak már létezniük **kell** a LUIS-alkalmazásban.

> [!NOTE]
> Az `utterances.json` fájlban használt szándékokat és entitásokat tartalmazó LUIS alkalmazásnak léteznie kell a kód `add-utterances.php` környezetben való futtatása előtt. A jelen cikkben található kód nem hozza létre a szándékokat és az entitásokat. Csak a meglévő szándékokhoz és entitásokhoz adja hozzá a kimondott szövegeket.

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

Futtassa az alkalmazást a parancssorból a PHP használatával.

Ha úgy hívja meg az `add-utterances.php` elemet, hogy csak az utterance.json fájlt használja argumentumként, azzal hozzáadja ugyan az új kimondott szövegeket, de nem tanítja be rájuk a LUIS-t.
````
> php add-utterances.php ./utterances.json
````

A kimondott szövegek hozzáadására irányuló API-hívás az alábbi JSON-t adja vissza. A `response` mező ebben a formátumban a hozzáadott kimondott szövegeket jelzi. A `hasError` értéke false (hamis), ami azt jelzi, hogy a kimondott szöveg hozzáadása megtörtént.  

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
Hívja meg az `add-utterance.php` parancsot a `-train` argumentummal a betanításra vonatkozó kérés elküldéséhez. 

````
> php add-utterances.php ./utterances.json -train
````

> [!NOTE]
> Az ismétlődő kimondott szövegeket a rendszer nem adja újra hozzá, ez azonban nem okoz hibát. A `response` az eredeti kimondott szöveg azonosítóját tartalmazza.

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
A betanítás állapotának ellenőrzéséhez és az állapotinformációk megjelenítéséhez hívja meg az alkalmazást a `-status` argumentummal.

````
> php add-utterances.php -status
````

```
Requested training status.
[
   {
      "modelId": "eb2f117c-e10a-463e-90ea-1a0176660acc",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c1bdfbfc-e110-402e-b0cc-2af4112289fb",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "863023ec-2c96-4d68-9c44-34c1cbde8bc9",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "82702162-73ba-4ae9-a6f6-517b5244c555",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "37121f4c-4853-467f-a9f3-6dfc8cad2763",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "de421482-753e-42f5-a765-ad0a60f50d69",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "80f58a45-86f2-4e18-be3d-b60a2c88312e",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c9eb9772-3b18-4d5f-a1e6-e0c31f91b390",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "2afec2ff-7c01-4423-bb0e-e5f6935afae8",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "95a81c87-0d7b-4251-8e07-f28d180886a1",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   }
]
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az oktatóanyag befejezését követően távolítsa el a Visual Studiót és a konzolalkalmazást, ha már nincs szüksége rájuk. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [LUIS-alkalmazások felépítése programozott módon](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website