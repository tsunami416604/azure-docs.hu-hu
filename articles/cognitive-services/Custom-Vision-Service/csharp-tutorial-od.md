---
title: 'Gyors útmutató: objektum C# -észlelési projekt létrehozása a-Custom Vision SDK-val'
titleSuffix: Azure Cognitive Services
description: Projekt létrehozása, címkék hozzáadása, képek feltöltése, projekt betanítása és objektumok észlelése a .NET SDK és a C# együttes használatával.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: areddish
ms.openlocfilehash: 7baa3e2de00997496edb5b445f8426d899ac65a4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383724"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Rövid útmutató: Objektumészlelési projekt létrehozása a Custom Vision .NET SDK-val

Ez a cikk ahhoz biztosít információt és mintakódot, hogy megismerkedhessen a Custom Vision SDK és a C# együttes használatával egy objektumészlelési modell létrehozása céljából. Miután elkészült, adhat hozzá címkézett régiókat, tölthet fel képeket, betaníthatja a projektet, megkaphatja a projekt alapértelmezett előrejelzési végpont URL-címét és ezt a végpontot felhasználhatja kép programozott tesztelésére. Használja sablonként a példát a saját .NET-alkalmazása létrehozásához. 

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>A Custom Vision SDK és a mintakód letöltése

A Custom Visiont használó .NET-alkalmazás megírásához a Custom Vision NuGet-csomagokra lesz szüksége. Ezek a csomagok a letöltött minta projekt részét képezik, de ezeket külön-külön is elérheti.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Klónozza vagy töltse le a [Cognitive Services .NET-mintákat](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) tartalmazó projektet. Navigáljon a **CustomVision/ObjectDetection** mappára, és nyissa meg a _ObjectDetection. Csproj_ mappát a Visual Studióban.

Ez a Visual Studio-projekt létrehoz egy új, __My New Project__ nevű Custom Vision-projektet, amely a [Custom Vision webhelyén](https://customvision.ai/) keresztül érhető el. Utána képeket tölt fel az objektumészlelési modell betanítására és tesztelésére. Ebben a projektben a modell betanítása a képeken látható villák és ollók észlelésére irányul.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>A kód értelmezése

Nyissa meg a _Program.cs_ fájlt, és tekintse át a kódot. [Környezeti változókat hozhat létre](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) az `CUSTOM_VISION_TRAINING_KEY` és `CUSTOM_VISION_PREDICTION_KEY`nevű képzési és előrejelzési kulcsokhoz. A szkript ezeket fogja keresni.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

A végpont URL-címét a Custom Vision webhely beállítások lapján szerezheti be. Mentse egy `CUSTOM_VISION_ENDPOINT`nevű környezeti változóba. A parancsfájl egy hivatkozást ment az osztály gyökerére.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>Új Custom Vision Service-projekt létrehozása

A következő kódrészlet létrehoz egy objektumészlelési projektet. A létrehozott projekt a [Custom Vision webhelyén](https://customvision.ai/) jelenik meg, amelyet korábban felkeresett. Tekintse meg a [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) metódust a projekt létrehozásakor a további beállítások megadásához (lásd: a detektor webportáljának [összeállítása](get-started-build-detector.md) útmutató).  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>Címkék hozzáadása a projekthez

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ha képeket címkéz meg az objektumészlelési projektekben, meg kell adnia a címkével ellátott objektumok régióját a normalizált koordináták használatával. Az alábbi kód a címkével ellátott régiójához társítja mindegyik mintaképet.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Az egyes mintaképek és régiókoordinátáik ezután ezzel a társítási térképpel tölthetők fel. Egyetlen kötegben akár 64 képet is feltölthet.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

Megtörtént a mintaképek mindegyikének feltöltése, és mindegyik kép rendelkezik egy címkével (**villa** vagy **olló**), illetve a címkéhez társított képponttéglalappal.

### <a name="train-the-project"></a>A projekt tanítása

Ez a kód létrehozza az első betanítási iterációt a projektben.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>Az aktuális iteráció közzététele

A közzétett iterációhoz megadott név felhasználható az előrejelzési kérelmek küldésére. Egy iteráció nem érhető el az előrejelzési végponton, amíg közzé nem teszi.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

### <a name="create-a-prediction-endpoint"></a>Előrejelzési végpont létrehozása

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

### <a name="use-the-prediction-endpoint"></a>Az előrejelzési végpont használata

A szkript ezen része betölti a képet, lekérdezi a modell végpontját, majd megjeleníti az előrejelzési adatokat a konzolon.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

A futtatása során az alkalmazásnak meg kell nyitnia egy konzolablakot, és az alábbi kimenetet kell megjelenítenie:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Ezután ellenőrizheti, hogy a tesztkép (az **Images/Test/** mappában található) megfelelően lett-e megcímkézve, és helyes-e az észlelési régió. Az alkalmazásból való kilépéshez nyomja le bármelyik billentyűt.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>További lépések

Láthatta, hogyan hajthatók végre az objektumészlelési folyamat lépései kódok használatával. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran előfordulhat, hogy a nagyobb pontosság érdekében többször is be kell tanítania és tesztelnie kell a modellt. Az alábbi útmutató a képosztályozással foglalkozik, az alapelvei azonban hasonlóak az objektumészlelés alapelveihez.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](test-your-model.md)
