---
title: 'Rövid útmutató: Képosztályozási projekt létrehozása a C#-hoz készült Custom Vision SDK-val'
titleSuffix: Azure Cognitive Services
description: Projekt létrehozása, címkék hozzáadása, képek feltöltése, projekt betanítása és előrejelzés létrehozása a .NET SDK és a C# együttes használatával.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: anroth
ms.openlocfilehash: db98464ecefaaf177161a1e417496ee7c994cff0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978645"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Rövid útmutató: Képosztályozási projekt létrehozása a Custom Vision .NET SDK-val

Ez a cikk ahhoz biztosít információt és mintakódot, hogy megismerkedhessen a Custom Vision SDK és a C# együttes használatával egy képosztályozási modell létrehozása céljából. Miután elkészült, adhat hozzá címkéket, tölthet fel képeket, betaníthatja a projektet, megkaphatja a projekt alapértelmezett előrejelzési végpont URL-címét és ezt a végpontot felhasználhatja kép programozott tesztelésére. Használja sablonként a példát a saját .NET-alkalmazása létrehozásához. Ha a besorolási modellt kód _nélkül_ szeretné felépíteni és használni, tekintse meg a [böngészőalapú útmutatást](getting-started-build-a-classifier.md) .

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>A Custom Vision SDK és a mintakód letöltése

A Custom Visiont használó .NET-alkalmazás megírásához a Custom Vision NuGet-csomagokra lesz szüksége. Ezek a csomagok a letöltött minta projekt részét képezik, de ezeket külön-külön is elérheti.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Klónozza vagy töltse le a [Cognitive Services .NET-mintákat](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) tartalmazó projektet. Lépjen a **CustomVision/ImageClassification** mappába, és nyissa meg az _ImageClassification.csproj_ fájlt a Visual Studióban.

Ez a Visual Studio-projekt létrehoz egy új, __My New Project__ nevű Custom Vision-projektet, amely a [Custom Vision webhelyén](https://customvision.ai/) keresztül érhető el. Utána feltölti a képeket az osztályozó tanítására és kipróbálására. Ebben a projektben az osztályozónak meg kell határoznia, hogy a fa egy __hemlokfenyő__ vagy pedig egy __japáncseresznye__.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>A kód értelmezése

Nyissa meg a _Program.cs_ fájlt, és tekintse át a kódot. [Környezeti változókat hozhat létre](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) az `CUSTOM_VISION_TRAINING_KEY` és `CUSTOM_VISION_PREDICTION_KEY`nevű képzési és előrejelzési kulcsokhoz. A szkript ezeket a változókat fogja keresni.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

A végpont URL-címét a Custom Vision webhely beállítások lapján szerezheti be. Mentse egy `CUSTOM_VISION_ENDPOINT`nevű környezeti változóba. A parancsfájl egy hivatkozást ment az osztály gyökerére.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

Az alábbi kódsorok a projekt elsődleges funkcióját hajtják végre.

### <a name="create-a-new-custom-vision-service-project"></a>Új Custom Vision Service-projekt létrehozása

A létrehozott projekt a [Custom Vision webhelyén](https://customvision.ai/) jelenik meg, amelyet korábban felkeresett. Tekintse meg a [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) metódust a projekt létrehozásakor a többi beállítás megadásához (az [osztályozó webportál összeállításával](getting-started-build-a-classifier.md) foglalkozó útmutatóban).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Címkék létrehozása a projektben

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

A projekt képei megtalálhatók a letöltött fájlban. A _Program.cs_ **LoadImagesFromDisk** metódusa tartalmazza a rájuk mutató hivatkozást. Egyetlen kötegben akár 64 képet is feltölthet.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>Az osztályozó és a közzététel betanítása

Ez a kód létrehozza az első iterációt a projektben, majd közzéteszi az iterációt az előrejelzési végponton. Az iteráció nevét használhatja az előrejelzési kérelmek küldéséhez. Egy iteráció nem érhető el az előrejelzési végponton, amíg közzé nem teszi.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>Az előrejelzési végpont beállítása

Az előrejelzési végpont az a hivatkozás, amellyel egy képet küldhet el az aktuális modellnek, és osztályozási előrejelzést kérhet le.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Kép elküldése az alapértelmezett előrejelzési végpontnak

Ebben a szkriptben a tesztkép betöltése a **LoadImagesFromDisk** metódusban történik, a modell előrejelzési kimenetének pedig a konzolon kell megjelennie. A `publishedModelName` változó értékének meg kell egyeznie a Custom Vision portál **teljesítmény** lapján található "közzétett" értékkel. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

A futtatása során az alkalmazásnak meg kell nyitnia egy konzolablakot, és az alábbi kimenetet kell megjelenítenie:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Ezután ellenőrizheti, hogy a tesztkép (az **Images/Test/** mappában található) megfelelően lett-e megcímkézve. Az alkalmazásból történő kilépéshez nyomjon meg egy billentyűt. Vissza is léphet a [Custom Vision webhelyére](https://customvision.ai), és megtekintheti az újonnan létrehozott projekt aktuális állapotát.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan végezheti el a képosztályozási folyamat minden lépését a kódban. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran előfordulhat, hogy a nagyobb pontosság érdekében többször is be kell tanítania és tesztelnie kell a modellt.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](test-your-model.md)
