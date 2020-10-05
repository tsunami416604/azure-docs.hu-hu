---
title: 'Gyors útmutató: modell lekérése REST API-kkal – LUIS'
titleSuffix: Azure Cognitive Services
description: Ebben a REST API rövid útmutatóban a cURL használatával hosszúságú kimondott szöveg adhat hozzá, és betaníthatja a modelleket.
services: cognitive-services
author: nitinme
manager: diberry
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 06/19/2020
ms.author: nitinme
ms.openlocfilehash: e5cf3160e6592a48e3a81480480ad8f01cc6af3a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "86206261"
---
# <a name="quickstart-use-curl-and-rest-to-change-model"></a>Rövid útmutató: a cURL és a REST használata a modell módosításához

Ez a rövid útmutató bemutatja, hogyan adhat hozzá például hosszúságú kimondott szöveg egy pizza-alkalmazáshoz, és hogyan taníthatja be az alkalmazást. A kimondott példaszövegek szándékhoz rendelt beszélgetésre szánt felhasználói szövegek. A szándékok kimondott példaszövegeinek megadásával megtanítja a LUIS-t arra, hogy milyen típusú felhasználói szöveg mely szándékhoz tartozik.

Ennek a rövid útmutatónak a követésével három REST-hívást hajt végre egymás után.

- Először fel kell töltenie egy példaként szolgáló hosszúságú kimondott szöveg, amely a pizza-alkalmazás modelljének betanítására szolgál majd a [Rest batch címke hozzáadása](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09) hívás használatával.
- Ezután megkezdi a pizza-alkalmazáshoz tartozó betanítási munkamenetet a [Rest Train Application Version](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) hívásának használatával.
- Végül megtekintheti a pizza-alkalmazás betanítási munkamenetének állapotát a [Rest Get Version betanítási állapot](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c46) hívásával.

[Dokumentáció](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)

## <a name="prerequisites"></a>Előfeltételek

* Egy ingyenes [Luis](https://www.luis.ai) -fiók.
* Egy szövegszerkesztő, például a [Visual Studio Code](https://code.visualstudio.com/).
* A parancssori program cURL. A cURL program már telepítve van a macOS, a Linux-disztribúciók és a Windows 10 1803-es és újabb verzióiban.

    Ha telepítenie kell a cURLot, letöltheti a curlot a [curl letöltési oldaláról](https://curl.haxx.se/download.html).

## <a name="create-pizza-app"></a>Pizza-alkalmazás létrehozása

[!INCLUDE [Create pizza app](includes/get-started-get-model-create-pizza-app.md)]

## <a name="create-a-json-file-to-train-the-pizza-app"></a>JSON-fájl létrehozása a pizza-alkalmazás betanításához

Három példát tartalmazó JSON-fájl létrehozásához mentse a következő JSON-hosszúságú kimondott szöveg egy nevű fájlba `ExampleUtterances.JSON` :

```JSON
[
  {
    "text": "order a pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "order a large pepperoni pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 14,
        "endCharIndex": 28
      },
      {
        "entityName": "Size",
        "startCharIndex": 8,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "I want two large pepperoni pizzas on thin crust",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 17,
        "endCharIndex": 32
      },
      {
        "entityName": "Size",
        "startCharIndex": 11,
        "endCharIndex": 15
      },
      {
        "entityName": "Quantity",
        "startCharIndex": 7,
        "endCharIndex": 9
      },
      {
        "entityName": "Crust",
        "startCharIndex": 37,
        "endCharIndex": 46
      }
    ]
  }
]`
```

A hosszúságú kimondott szöveg JSON-példa egy adott formátumot követ.

A `text` mező a kimondott példaszövegeket tartalmazza. Az `intentName` mezőnek a LUIS-alkalmazásban található létező szándék nevének kell megfelelnie. Az `entityLabels` mező kötelező. Ha nem szeretne entitásokat megcímkézni, adjon meg egy üres tömböt.

Ha az entityLabels tömb nem üres, a `startCharIndex` és az `endCharIndex` indexnek az `entityName` mezőben hivatkozott entitást kell jelölnie. Az index nulla-alapú. Ha a feliratot a szöveg egyik területén kezdi meg vagy fejezi be, akkor a hosszúságú kimondott szöveg hozzáadásához szükséges API-hívás sikertelen lesz.

## <a name="add-example-utterances"></a>Példa kimondott szövegek hozzáadása

1. A következő hosszúságú kimondott szöveg-köteg feltöltéséhez másolja a parancsot a szövegszerkesztőbe:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" ^
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" \
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" \
          --data "@ExampleUtterances.JSON"
    ```

    ***

1. Cserélje le az értékeket a `***YOUR-` saját értékeivel kezdődő értékekre.

    |Tájékoztatás|Rendeltetés|
    |--|--|
    |`***YOUR-AUTHORING-ENDPOINT***`| Az authoring URL-végpontja. Például: „https://REPLACE-WITH-YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/”. Az erőforrás neve az erőforrás létrehozásakor állítható be.|
    |`***YOUR-APP-ID***`| A LUIS-alkalmazás azonosítója. |
    |`***YOUR-APP-VERSION***`| A LUIS-alkalmazás verziószáma. A pizza alkalmazás esetében a verziószám "0,1", idézőjelek nélkül.|
    |`***YOUR-AUTHORING-KEY***`|Az 32 karakteres szerzői kulcs.|

    A hozzárendelt kulcsok és erőforrások a LUIS portálon láthatók a kezelés szakasz Azure- **erőforrások** lapján. Az alkalmazás-azonosító az **Alkalmazásbeállítások** lapon, ugyanazon kezelés szakaszban érhető el.

1. Indítsa el a parancssort (Windows) vagy a terminált (macOS és Linux), és módosítsa a címtárakat arra a könyvtárba, ahová a fájlt mentette `ExampleUtterances.JSON` .

1. Másolja a cURL parancsot a szerkesztőből, és illessze be a parancssorba (Windows) vagy a terminálba (macOS és Linux). Nyomja le az ENTER billentyűt a parancs végrehajtásához.

    A következő válasznak kell megjelennie:

    ```json
    [{"value":{"ExampleId":1255129706,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1255129707,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1255129708,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    ```

    Itt látható az olvashatóságra formázott kimenet:

    ```json
    [
      {
        "value": {
          "ExampleId": 1255129706,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129707,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129708,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    ```

## <a name="train-the-pizza-app-model"></a>A pizza-alkalmazás modelljének betanítása

1. A pizza-alkalmazás betanítási munkamenetének megkezdéséhez másolja ezt a parancsot a szövegszerkesztőbe:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" ^
          --data "" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    ***

1. Ahogy korábban tette, cserélje le az értékeket a `***YOUR-` saját értékeivel kezdődően.

1. Másolja a cURL parancsot a szerkesztőből, és illessze be a parancssorba (Windows) vagy a terminálba (macOS és Linux). Nyomja le az ENTER billentyűt a parancs végrehajtásához.

    A következő válasznak kell megjelennie:

    ```json
    {"statusId":2,"status":"UpToDate"}
    ```

    Itt látható az olvashatóságra formázott kimenet:

    ```json
    {
      "statusId": 2,
      "status": "UpToDate"
    }
    ```

## <a name="get-the-status-of-training"></a>Képzés állapotának beolvasása

1. A betanítási munkamenet betanítási állapotának lekéréséhez másolja a parancsot a szövegszerkesztőbe:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" ^
            --request GET ^
            --header "Content-Type:application/json" ^
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    ***

1. Ahogy korábban tette, cserélje le az értékeket a `***YOUR-` saját értékeivel kezdődően.

1. Másolja a cURL parancsot a szerkesztőből, és illessze be a parancssorba (Windows) vagy a terminálba (macOS és Linux). Nyomja le az ENTER billentyűt a parancs végrehajtásához.

    A következő válasznak kell megjelennie:

    ```json
    [{"modelId":"8eb7ad8f-5db5-4c28-819b-ca3905fffd80","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6f53bc92-ae54-44ce-bc4e-010d1f8cfda0","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6cb17888-ad6e-464c-82c0-d37fd1f2c4f8","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"a16fc4fd-1949-4e77-9de3-30369f16c3a5","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6bacdb75-1889-4f93-8971-8c8995ff8960","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"be963f4c-4898-48d7-9eba-3c6af0867b9d","details":{"statusId":2,"status":"UpToDate","exampleCount":171}}]
    ```

    Itt látható az olvashatóságra formázott kimenet:

    ```json
    [
      {
        "modelId": "8eb7ad8f-5db5-4c28-819b-ca3905fffd80",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6f53bc92-ae54-44ce-bc4e-010d1f8cfda0",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6cb17888-ad6e-464c-82c0-d37fd1f2c4f8",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "a16fc4fd-1949-4e77-9de3-30369f16c3a5",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6bacdb75-1889-4f93-8971-8c8995ff8960",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "be963f4c-4898-48d7-9eba-3c6af0867b9d",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a rövid útmutatóval, törölje a `ExampleUtterances.JSON` fájlt a fájlrendszerből.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ajánlott eljárások az alkalmazásokhoz](luis-concept-best-practices.md)
