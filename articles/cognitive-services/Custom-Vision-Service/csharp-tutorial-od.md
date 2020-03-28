---
title: QuickStart Objektum C# -észlelési projekt létrehozása a-Custom Vision SDK-val
titleSuffix: Azure Cognitive Services
description: Projekt létrehozása, Címkék hozzáadása, képek feltöltése, a projekt betanítása és az objektumok észlelése a .NET SDK C#-val a használatával.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: c6aaf69ba3ed682a00a203079b024a47121334e3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170074"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>QuickStart Objektum-észlelési projekt létrehozása a Custom Vision .NET SDK-val

Ebből a cikkből megtudhatja, hogyan kezdheti el az Custom Vision C# SDK-t egy objektum-észlelési modell létrehozásához. A létrehozást követően címkézett régiókat adhat hozzá, képeket tölthet fel, betaníthatja a projektet, beolvashatja a projekt alapértelmezett előrejelzési végpontjának URL-címét, és a végpont használatával programozott módon tesztelheti a lemezképeket. Ez a példa sablonként használható saját .NET-alkalmazás létrehozásához. 

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Custom Vision SDK és mintakód beszerzése

Custom Visiont használó .NET-alkalmazás írásához szüksége lesz a Custom Vision NuGet-csomagokra. Ezek a csomagok a letöltött minta projekt részét képezik, de ezeket külön-külön is elérheti.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Klónozza vagy töltse le a [Cognitive Services .NET-mintákat](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) tartalmazó projektet. Nyissa meg a **CustomVision/ObjectDetection mappát,** és nyissa meg _az ObjectDetection.csproj fájlt_ a Visual Studióban.

Ez a Visual Studio-projekt létrehoz egy új, __My New Project__ nevű Custom Vision-projektet, amely a [Custom Vision webhelyén](https://customvision.ai/) keresztül érhető el. Utána képeket tölt fel az objektumészlelési modell betanítására és tesztelésére. Ebben a projektben a modell betanítása a képeken látható villák és ollók észlelésére irányul.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>A kód értelmezése

Nyissa meg a _Program.cs_ fájlt, és tekintse át a kódot. [Hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) `CUSTOM_VISION_TRAINING_KEY` a `CUSTOM_VISION_PREDICTION_KEY`betanítási és előrejelzési kulcsok neve, és, illetve. A parancsfájl megkeresi ezeket a változókat.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

A végpont URL-címét a Custom Vision webhely beállítások lapján szerezheti be. Mentse egy `CUSTOM_VISION_ENDPOINT`nevű környezeti változóba. A parancsfájl egy hivatkozást ment az osztály gyökerére.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>Új Custom Vision Service projekt létrehozása

A következő kódrészlet létrehoz egy objektumészlelési projektet. A létrehozott projekt a [Custom Vision webhelyén](https://customvision.ai/) jelenik meg, amelyet korábban felkeresett. A [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) metódusból további beállításokat adhat meg a projekt létrehozásakor (ezt a forrás az [Érzékelő](get-started-build-detector.md) webportál-útmutatójában ismerteti).  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>Címkék hozzáadása a projekthez

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ha képeket címkéz az objektum-észlelési projektekben, az egyes címkézett objektumok régióját normalizált koordináták használatával kell megadnia. A következő kód a mintául szolgáló rendszerképeket társítja a címkézett régiójával.

> [!NOTE]
> Ha nem rendelkezik kattintással és húzással a régiók koordinátáinak megjelöléséhez, használhatja a webes felhasználói felületet [a Customvision.ai.](https://www.customvision.ai/) Ebben a példában a koordináták már meg vannak adva.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Az egyes mintaképek és régiókoordinátáik ezután ezzel a társítási térképpel tölthetők fel. Egy kötegben legfeljebb 64 képet tölthet fel.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

Ekkor feltöltötte az összes minta lemezképet, és címkézi őket (**elágazás** vagy **olló**) egy társított pixel téglalapmal.

### <a name="train-the-project"></a>A projekt betanítása

Ez a kód létrehozza az első betanítási iterációt a projektben.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>Az aktuális iteráció közzététele

A közzétett iterációhoz megadott név felhasználható az előrejelzési kérelmek küldésére. Egy iteráció nem érhető el az előrejelzési végponton, amíg közzé nem teszi.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

### <a name="create-a-prediction-endpoint"></a>Előrejelzési végpont létrehozása

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

### <a name="use-the-prediction-endpoint"></a>Az előrejelzési végpont használata

A szkript ezen része betölti a teszt képét, lekérdezi a modell végpontját, és kiírja az előrejelzési adatokat a konzolra.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásakor meg kell nyitnia egy konzolablak ablakát, és meg kell írnia a következő kimenetet:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Ezt követően ellenőrizheti, hogy a rendszerkép ( **images/test/** ) címkéje megfelelően van-e megjelölve, és hogy az észlelési régió helyes-e. Ezen a ponton bármelyik billentyű lenyomásával kiléphet az alkalmazásból.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>További lépések

Most már látta, hogyan kell csinálni minden lépését az objektum észlelési folyamat kódot. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran többször is be kell képeznie és tesztelnie kell a modellt annak érdekében, hogy pontosabb legyen. Az alábbi útmutató a képosztályozással foglalkozik, az alapelvei azonban hasonlóak az objektumészlelés alapelveihez.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](test-your-model.md)
