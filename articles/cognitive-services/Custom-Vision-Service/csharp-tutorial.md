---
title: 'Gyors útmutató: Kép besorolási projekt létrehozása a Custom Vision készült SDK-valC#'
titlesuffix: Azure Cognitive Services
description: Projekt létrehozása, címkék hozzáadása, képek feltöltése, projekt betanítása és előrejelzés létrehozása a .NET SDK és a C# együttes használatával.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 9b6d01908265791a83ee311375fa50fcca995f79
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472726"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Gyors útmutató: A Custom Vision .NET SDK-val kép besorolási projekt létrehozása

Ez a cikk ahhoz biztosít információt és mintakódot, hogy megismerkedhessen a Custom Vision SDK és a C# együttes használatával egy képosztályozási modell létrehozása céljából. Miután elkészült, adhat hozzá címkéket, tölthet fel képeket, betaníthatja a projektet, megkaphatja a projekt alapértelmezett előrejelzési végpont URL-címét és ezt a végpontot felhasználhatja kép programozott tesztelésére. Használja sablonként a példát a saját .NET-alkalmazása létrehozásához. Ha az osztályozási modell létrehozásának és használatának folyamatán kód használata _nélkül_ szeretne végighaladni, tekintse meg a [böngészőalapú módszer útmutatóját](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>A Custom Vision SDK és a mintakód letöltése

A Custom Visiont használó .NET-alkalmazás megírásához a Custom Vision NuGet-csomagokra lesz szüksége. A csomagokat tartalmazza a mintaprojekt, amelyet le fog tölteni, azonban külön is elérheti őket itt.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Klónozza vagy töltse le a [Cognitive Services .NET-mintákat](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) tartalmazó projektet. Lépjen a **CustomVision/ImageClassification** mappába, és nyissa meg az _ImageClassification.csproj_ fájlt a Visual Studióban.

Ez a Visual Studio-projekt létrehoz egy új, __My New Project__ nevű Custom Vision-projektet, amely a [Custom Vision webhelyén](https://customvision.ai/) keresztül érhető el. Utána feltölti a képeket az osztályozó tanítására és kipróbálására. Ebben a projektben az osztályozónak meg kell határoznia, hogy a fa egy __hemlokfenyő__ vagy pedig egy __japáncseresznye__.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>A kód értelmezése

Nyissa meg a _Program.cs_ fájlt, és tekintse át a kódot. Szúrja be az előfizetési kulcsokat a megfelelő definíciókba a **Main** metódusban.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=21-30)]

A végpont paramétert kell mutatnia a régió, ahol a Custom Vision erőforrásokat tartalmazó Azure-erőforráscsoportot létrehozták. Ebben a példában azt, hogy az USA déli középső régiójában pedig használja:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

Az alábbi kódsorok a projekt elsődleges funkcióját hajtják végre.

### <a name="create-a-new-custom-vision-service-project"></a>Új Custom Vision Service-projekt létrehozása

A létrehozott projekt a [Custom Vision webhelyén](https://customvision.ai/) jelenik meg, amelyet korábban felkeresett. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=32-34)]

### <a name="create-tags-in-the-project"></a>Címkék létrehozása a projektben

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=36-38)]

### <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

A projekt képei megtalálhatók a letöltött fájlban. A _Program.cs_ **LoadImagesFromDisk** metódusa tartalmazza a rájuk mutató hivatkozást.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=40-55)]

### <a name="train-the-classifier-and-publish"></a>Az osztályozó által igénybe vett betanítás, közzététel

Ez a kód a projektet hoz létre az első példányát, és majd az előrejelzési végpontot tesz közzé, hogy az iteráció. Név, a közzétett iteráció előrejelzési kérelmek küldésére használható. Egy iteráció nem áll rendelkezésre előrejelzési végpontját, amíg közzé van téve.

```csharp
// The returned iteration will be in progress, and can be queried periodically to see when it has completed
while (iteration.Status == "Training")
{
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
}

// The iteration is now trained. Publish it to the prediction end point.
var publishedModelName = "treeClassModel";
var predictionResourceId = "<target prediction resource ID>";
trainingApi.PublishIteration(project.Id, iteration.Id, publishedModelName, predictionResourceId);
Console.WriteLine("Done!\n");
```

### <a name="set-the-prediction-endpoint"></a>Az előrejelzési végpont beállítása

Az előrejelzési végpont az a hivatkozás, amellyel egy képet küldhet el az aktuális modellnek, és osztályozási előrejelzést kérhet le.

```csharp
// Create a prediction endpoint, passing in obtained prediction key
CustomVisionPredictionClient endpoint = new CustomVisionPredictionClient()
{
        ApiKey = predictionKey,
        Endpoint = SouthCentralUsEndpoint
};
```

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Kép elküldése az alapértelmezett előrejelzési végpontnak

Ebben a szkriptben a tesztkép betöltése a **LoadImagesFromDisk** metódusban történik, a modell előrejelzési kimenetének pedig a konzolon kell megjelennie.

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var result = endpoint.ClassifyImage(project.Id, publishedModelName, testImage);

// Loop over each prediction and write out the results
foreach (var c in result.Predictions)
{
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A futtatása során az alkalmazásnak meg kell nyitnia egy konzolablakot, és az alábbi kimenetet kell megjelenítenie:

```
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

Láthatta, hogyan hajthatók végre a képosztályozási folyamat lépései kód használatával. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran előfordulhat, hogy a nagyobb pontosság érdekében többször is be kell tanítania és tesztelnie kell a modellt.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](test-your-model.md)
