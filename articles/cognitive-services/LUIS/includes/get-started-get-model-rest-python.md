---
title: 'Modell beszereznie A REST-hívással c-ben #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/14/2020
ms.author: diberry
ms.openlocfilehash: 4d8da7d2bc51c4fc4ebc8d71f230f24f20b3aa24
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368481"
---
## <a name="prerequisites"></a>Előfeltételek

* Azure Language Understanding – Authoring resource 32 karakterkulcs és szerzői végpont URL-címe. Hozzon létre az [Azure Portalon](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) vagy az [Azure CLI-n](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli)keresztül.
* Importálja a [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) alkalmazást a cognitive-services-language-understanding GitHub-tárházból.
* Az importált TravelAgent alkalmazás LUIS-alkalmazásazonosítója. Az alkalmazásazonosító az alkalmazás irányítópultján látható.
* A kimondott szövegeket fogadó alkalmazáson belüli verzióazonosító. Az alapértelmezett azonosító a „0.1”.
* [Python 3.6-os](https://www.python.org/downloads/) vagy újabb.
* [Visual Studio kód](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Kimondott példaszövegek JSON-fájlja

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Modell módosítása programozott módon

1. Hozzon létre egy új fájlt `model.py` néven. Adja hozzá a következő kódot:

    [!code-python[Add example utterances to Language Understanding in python](~/samples-luis/documentation-samples/quickstarts/change-model/python/3.x/add-utterances-3-6.py)]

1. Cserélje le a `YOUR-` saját értékeivel kezdődő értékeket.

    |Információ|Cél|
    |--|--|
    |`YOUR-KEY`|A 32 karakteres szerzői kulcs.|
    |`YOUR-ENDPOINT`| A szerzői URL-végpont. Például: `replace-with-your-resource-name.api.cognitive.microsoft.com`. Az erőforrás nevét az erőforrás létrehozásakor állította be.|
    |`YOUR-APP-ID`| A LUIS-alkalmazásazonosító. |

    A hozzárendelt kulcsok és erőforrások a LUIS-portálon, a Kezelés szakaszban, az **Azure-erőforrások** lapon láthatók. Az alkalmazásazonosító ugyanabban a Kezelés szakaszban, az **Alkalmazás beállítások** lapján érhető el.

1. Ha a parancssorugyanabban a könyvtárban található, ahol a fájlt létrehozta, írja be a következő parancsot a fájl futtatásához:

    ```console
    python model.py
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte ezt a rövid útmutatót, törölje a fájlt a fájlrendszerből.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyakorlati tanácsok egy alkalmazáshoz](../luis-concept-best-practices.md)
