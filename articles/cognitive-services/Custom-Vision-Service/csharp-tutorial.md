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
ms.openlocfilehash: 158e4dcd07f6ba31ad0efdd88f030f8db99fcfdd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170033"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Rövid útmutató: Képosztályozási projekt létrehozása a Custom Vision .NET SDK-val

Ez a cikk ahhoz biztosít információt és mintakódot, hogy megismerkedhessen a Custom Vision SDK és a C# együttes használatával egy képosztályozási modell létrehozása céljából. Miután elkészült, adhat hozzá címkéket, tölthet fel képeket, betaníthatja a projektet, megkaphatja a projekt alapértelmezett előrejelzési végpont URL-címét és ezt a végpontot felhasználhatja kép programozott tesztelésére. Ez a példa sablonként használható saját .NET-alkalmazás létrehozásához. Ha azt szeretné, hogy menjen át a folyamat létrehozása és használata besorolási modell kód _nélkül,_ tekintse meg a [böngésző-alapú útmutató](getting-started-build-a-classifier.md) helyett.

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Custom Vision SDK és mintakód beszerzése

Custom Visiont használó .NET-alkalmazás írásához szüksége lesz a Custom Vision NuGet-csomagokra. Ezek a csomagok a letöltött minta projekt részét képezik, de ezeket külön-külön is elérheti.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Klónozza vagy töltse le a [Cognitive Services .NET-mintákat](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) tartalmazó projektet. Lépjen a **CustomVision/ImageClassification** mappába, és nyissa meg az _ImageClassification.csproj_ fájlt a Visual Studióban.

Ez a Visual Studio-projekt létrehoz egy új, __My New Project__ nevű Custom Vision-projektet, amely a [Custom Vision webhelyén](https://customvision.ai/) keresztül érhető el. Utána feltölti a képeket az osztályozó tanítására és kipróbálására. Ebben a projektben az osztályozónak meg kell határoznia, hogy a fa egy __hemlokfenyő__ vagy pedig egy __japáncseresznye__.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>A kód értelmezése

Nyissa meg a _Program.cs_ fájlt, és tekintse át a kódot. [Hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) `CUSTOM_VISION_TRAINING_KEY` a `CUSTOM_VISION_PREDICTION_KEY`betanítási és előrejelzési kulcsok neve, és, illetve. A parancsfájl megkeresi ezeket a változókat.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

A végpont URL-címét a Custom Vision webhely beállítások lapján szerezheti be. Mentse egy `CUSTOM_VISION_ENDPOINT`nevű környezeti változóba. A parancsfájl egy hivatkozást ment az osztály gyökerére.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

Az alábbi kódsorok a projekt elsődleges funkcióját hajtják végre.

### <a name="create-a-new-custom-vision-service-project"></a>Új Custom Vision Service-projekt létrehozása

A létrehozott projekt a [Custom Vision webhelyén](https://customvision.ai/) jelenik meg, amelyet korábban felkeresett. A [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) metódusból további beállításokat adhat meg a projekt létrehozásakor (ez a magyarázat az [Osztályozó webportál készítése](getting-started-build-a-classifier.md) útmutatóban található).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Címkék létrehozása a projektben

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

A projekt képei megtalálhatók a letöltött fájlban. A _Program.cs_**LoadImagesFromDisk** metódusa tartalmazza a rájuk mutató hivatkozást. Egy kötegben legfeljebb 64 képet tölthet fel.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>Az osztályozó betanítása és közzététele

Ez a kód létrehozza az előrejelzési modell első iterációját, majd közzéteszi az előrejelzési végpontra vonatkozó iterációt. Az iteráció nevével előrejelzési kérelmeket küldhet. Egy iteráció nem érhető el az előrejelzési végponton, amíg közzé nem teszi.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>Az előrejelzési végpont beállítása

Az előrejelzési végpont az a hivatkozás, amellyel egy képet küldhet el az aktuális modellnek, és osztályozási előrejelzést kérhet le.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Kép elküldése az alapértelmezett előrejelzési végpontnak

Ebben a szkriptben a tesztkép betöltése a **LoadImagesFromDisk** metódusban történik, a modell előrejelzési kimenetének pedig a konzolon kell megjelennie. A `publishedModelName` változó értékének meg kell felelnie az egyéni látásportál **Teljesítmény** lapján található "Published as" értéknek. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásakor meg kell nyitnia egy konzolablak ablakát, és meg kell írnia a következő kimenetet:

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

## <a name="next-steps"></a>További lépések

Most már látta, hogyan kell csinálni minden lépését a kép besorolási folyamat kódot. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran előfordulhat, hogy a nagyobb pontosság érdekében többször is be kell tanítania és tesztelnie kell a modellt.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](test-your-model.md)
