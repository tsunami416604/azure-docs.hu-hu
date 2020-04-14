---
title: Űrlapfelismerő egyéni képzettsége (C#)
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan hozhat létre egyéni űrlapfelismerő képességet a C# és a Visual Studio használatával.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: article
ms.date: 01/21/2020
ms.openlocfilehash: 713b790c432f0e416392243262aed4b0fcda8892
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274574"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Példa: Űrlapfelismerő egyéni szakértelem létrehozása

Ebben az Azure Cognitive Search skillset példában megtudhatja, hogyan hozhat létre egy űrlapfelismerő egyéni szakértelem a C# és a Visual Studio használatával. A Formafelismerő elemzi a dokumentumokat, és kinyeri a kulcs/érték párokat és a táblaadatokat. Ha a Form Recognizer-t az [egyéni szakértelem-felületre](cognitive-search-custom-skill-interface.md)csomagolja, ezt a képességet hozzáadhatja egy teljes körű dúsítási folyamat lépéseként. A folyamat ezután betöltheti a dokumentumokat, és egyéb átalakításokat hajthat végre.

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (bármely kiadás).
- Legalább öt azonos típusú forma. Az útmutatóban található mintaadatokat használhatja.

## <a name="create-a-form-recognizer-resource"></a>Űrlapfelismerő erőforrás létrehozása

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>A modell betanítása

A szakértelem használata előtt be kell tanítania egy űrlapfelismerő modellt a beviteli űrlapokkal. Kövesse a [cURL rövid útmutató,](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/curl-train-extract) hogyan kell betanítani egy modellt. Használhatja a rövid útmutatóban biztosított mintaűrlapokat, vagy használhatja a saját adatait. A modell betanítása után másolja az azonosító értékét egy biztonságos helyre.

## <a name="set-up-the-custom-skill"></a>Az egyéni szakértelem beállítása

Ez az oktatóanyag az [AnalyzeForm projektet](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) használja az [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) GitHub-tárházban. Klónozza ezt a tárházat a helyi gépre, és keresse meg a **Vision/AnalyzeForm/** webhelyet a projekt eléréséhez. Ezután nyissa meg _az AnalyzeForm.csproj alkalmazást_ a Visual Studióban. Ez a projekt létrehoz egy Azure Function erőforrást, amely megfelel az [egyéni szakértelem-felületnek,](cognitive-search-custom-skill-interface.md) és használható az Azure Cognitive Search bővítéséhez. Az űrlapdokumentumokat bemenetként veszi fel, és a megadott kulcs-érték párokat adja ki (szövegként).

Először adja hozzá a projektszintű környezeti változókat. Keresse meg az **AnalyzeForm** projektet a bal oldali ablaktáblában, kattintson rá a jobb gombbal, és válassza **a Tulajdonságok parancsot.** A **Tulajdonságok** ablakban kattintson a **Hibakeresés** fülre, és keresse meg a **Környezeti változók mezőt.** A következő változók hozzáadásához kattintson a **Hozzáadás** gombra:
* `FORMS_RECOGNIZER_ENDPOINT_URL`a végpont URL-címére beállított értékkel.
* `FORMS_RECOGNIZER_API_KEY`az előfizetési kulcsra beállított értékkel.
* `FORMS_RECOGNIZER_MODEL_ID`a betanított modell azonosítójára beállított értékkel.
* `FORMS_RECOGNIZER_RETRY_DELAY`1000-re beállított értékkel. Ez az érték az az idő ezredmásodpercben, amelyet a program várni fog a lekérdezés újbóli megkísérlése előtt.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS`100-ra beállított értékkel. Ez az érték az a szám, ahányszor a program lekérdezi a szolgáltatást, miközben sikeres választ próbál kapni.

Ezután nyissa meg `fieldMappings` _AnalyzeForm.cs,_ és keresse meg a *mező-mappings.json* fájlra hivatkozó változót. Ez a fájl (és az arra hivatkozó változó) határozza meg az űrlapokból kinyerni kívánt kulcsok listáját, valamint az egyes kulcsok egyéni címkéjét. Például egy `{ "Address:", "address" }, { "Invoice For:", "recipient" }` érték azt jelenti, hogy a parancsfájl `Address:` `Invoice For:` csak az észlelt és a `"address"` `"recipient"`mezők értékeit menti, és ezeket az értékeket a és a, címkével címkézi.

Végül vegye `contentType` figyelembe a változót. Ez a parancsfájl az adott Űrlapfelismerő modellt futtatja az URL-cím által hivatkozott távoli dokumentumokon, így a tartalomtípus `application/json`. Ha a helyi fájlokat úgy szeretné elemezni, hogy a bájtadatfolyamokat `contentType` beleépíti a HTTP-kérelmekbe, akkor a fájlmegfelelő [MIME-típusára](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) kell módosítania.

## <a name="test-the-function-from-visual-studio"></a>A funkció tesztelése a Visual Studio-ból

Miután szerkesztette a projektet, mentse, és állítsa be az **AnalyzeForm** projektet indítási projektként a Visual Studióban (ha még nincs beállítva). Ezután nyomja le az **F5 billentyűt** a funkció helyi környezetben való futtatásához. Használjon REST-szolgáltatást, például [postást](https://www.postman.com/) a függvény hívásához.

### <a name="http-request"></a>HTTP-kérelem

A következő kéréssel hívja meg a függvényt.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>A kérés törzse

Kezdje az alábbi kérelemtörzssablonnal.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

Itt meg kell adnia egy olyan űrlap URL-címét, amelynek típusa megegyezik a betanított űrlapokkal. Tesztelési célokra használhatja az egyik képzési űrlapot. Ha követte a cURL-rövid útmutatót, az űrlapok egy Azure blob storage-fiókban lesznek. Nyissa meg az Azure Storage Explorert, keressen meg egy űrlapfájlt, kattintson rá a jobb gombbal, és válassza **a Megosztott hozzáférésű aláírás beszereznie parancsot**. A következő párbeszédpanel ablak ban egy URL-címet és egy SAS-jogkivonatot biztosít. Adja meg ezeket `"formUrl"` `"formSasToken"` a karakterláncokat a kérelem törzsének és mezőinek.

> [!div class="mx-imgBorder"]
> ![Azure storage-kezelő; pdf dokumentum van kiválasztva](media/cognitive-search-skill-form/form-sas.png)

Ha olyan távoli dokumentumot szeretne elemezni, amely nem az Azure blob `"formUrl"` storage-ban `"formSasToken"` található, illessze be az URL-címét a mezőbe, és hagyja üresen a mezőt.

> [!NOTE]
> Ha a szakértelem integrálva van egy skillset, az URL-t és a jogkivonatot a Cognitive Search biztosítja.

### <a name="response"></a>Válasz

A következő példához hasonló választ kell látnia:

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>A függvény közzététele az Azure-ban

Ha elégedett a funkció viselkedésével, közzéteheti azt.

1. A Visual Studio **Megoldáskezelőjében** kattintson a jobb gombbal a projektre, és válassza a **Közzététel parancsot.** Válassza **az Új** > **közzététel**létrehozása lehetőséget.
1. Ha még nem kapcsolta össze a Visual Studio alkalmazást az Azure-fiókjával, válassza **a Fiók hozzáadása lehetőséget....**
1. Kövesse a képernyőn megjelenő utasításokat. Adja meg az alkalmazásszolgáltatás, az Azure-előfizetés, az erőforráscsoport, az üzemeltetési csomag és a használni kívánt tárfiók egyedi nevét. Létrehozhat egy új erőforráscsoportot, egy új üzemeltetési csomagot és egy új tárfiókot, ha még nem rendelkezik ezekkel. Ha végzett, válassza a **Létrehozás gombot.**
1. A központi telepítés befejezése után figyelje meg a webhely URL-címét. Ez az URL-cím a függvényalkalmazás címe az Azure-ban. Mentse egy ideiglenes helyre.
1. Az [Azure Portalon](https://portal.azure.com)keresse meg az erőforráscsoportot, és keresse meg a `AnalyzeForm` közzétett függvényt. A **Kezelés** szakaszban a gazdakulcsok nak kell megjelennie. Másolja az *alapértelmezett* állomáskulcsot, és mentse ideiglenes helyre.

## <a name="connect-to-your-pipeline"></a>Csatlakozás a folyamathoz

Ahhoz, hogy ezt a képességet egy kognitív keresési folyamat, hozzá kell adnia egy készségdefiníciót a skillset. A következő JSON-blokk egy minta szakértelem-definíció (frissítenie kell a bemenetek és kimenetek, hogy tükrözze az adott forgatókönyv és a skillset környezet). Cserélje `AzureFunctionEndpointUrl` le a függvény `AzureFunctionDefaultHostKey` URL-címét, és cserélje le a gazdakulcsot.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban létrehozott egy egyéni szakértelem az Azure Form Recognizer szolgáltatásból. Ha többet szeretne megtudni az egyéni képességekről, olvassa el az alábbi forrásokat. 

* [Azure Search Power Skills: egyéni képességek tárháza](https://github.com/*zure-Samples/azure-search-power-skills)
* [Egyéni szakértelem hozzáadása a ai-dúsítási folyamathoz](cognitive-search-custom-skill-interface.md)
* [Képességcsoport megadása](cognitive-search-defining-skillset.md)
* [Szakértelemkészlet létrehozása (REST)](https://docs.microsoft.com/rest/api/*earchservice/create-skillset)
* [Bővített mezők leképezése](cognitive-search-output-field-mapping.md)
