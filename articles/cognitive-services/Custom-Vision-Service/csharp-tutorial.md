---
title: A Custom Vision Service használata a C#-alkalmazás – az Azure Cognitive Services |} A Microsoft Docs
description: Egy egyszerű C# alkalmazást, amely a Custom Vision API a Microsoft Cognitive Services bemutatása. Hozzon létre egy projektet, adja hozzá a címkéket, tölthet fel képeket, a projekt betanítását és az alapértelmezett végpont az előrejelzést.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: d3c2ffb0fd9578458bd07241eed4a87cf70d3c3c
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617434"
---
# <a name="use-the-custom-vision-service-from-a-c35-application"></a>Használja a-C, a Custom Vision Service&#35; alkalmazás

Ismerje meg, hogyan használhatja a Custom Vision Service, a C#-alkalmazás. A létrehozást követően, is címkéket adhat hozzá, tölthet fel képeket, betanítását a projektet, a projekt alapértelmezett előrejelzési végpont URL-cím és ezt a végpont programozott módon képet. A nyílt forráskódú példa sablonként használni a saját alkalmazás létrehozásához a Windows a Custom Vision Service API használatával.

## <a name="prerequisites"></a>Előfeltételek

* A Visual Studio 2017, a Windows valamelyik kiadása.

## <a name="get-the-custom-vision-sdk-and-samples"></a>Az egyéni Látástechnológiai SDK és minták
Ebben a példában hozhat létre, az egyéni Látástechnológiai SDK NuGet-csomagok szükségesek:

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

A képek, valamint letöltheti a [C# minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/CustomVision).

## <a name="get-the-training-and-prediction-keys"></a>A betanítási és Predikciós kulcsok beolvasása

Ebben a példában használt kulcsok beszerzéséhez látogasson el a [Custom Vision weblap](https://customvision.ai) , és válassza ki a __fogaskerék ikont__ kattintson a jobb felső sarokban. Az a __fiókok__ területén másolja a __képzési kulcs__ és __előrejelzési kulcs__ mezőket.

![A felhasználói felület kulcsok képe](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>A kód értelmezése

A Visual Studióban nyissa meg a projekt található a `Samples/CustomVision.Sample/` az SDK projekt könyvtárába.

Ez az alkalmazás használ a betanítási kulcs nevű új projekt létrehozásához korábban lekért __saját új projekt__. Ezután feltölti a lemezképeket taníthat vagy tesztelhet egy osztályozó által igénybe vett. Az osztályozó által igénybe vett azonosítja egy fa-e egy __Hemlock__ vagy egy __japán cseresznye__.

Az alábbi kódrészleteket megvalósítása ebben a példában az elsődleges funkciója:

* __Hozzon létre egy új Custom Vision Service-projektet__:

    ```csharp
     // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project");
    ```

* __Címkék létrehozása a projektben__:

    ```csharp
    // Make two tags in the new project
    var hemlockTag = trainingApi.CreateTag(project.Id, "Hemlock");
    var japaneseCherryTag = trainingApi.CreateTag(project.Id, "Japanese Cherry");
    ```

* __Töltse fel és címkézhet meg képeket__:

    ```csharp
    // Add some images to the tags
    Console.WriteLine("\tUploading images");
    LoadImagesFromDisk();

    // Images can be uploaded one at a time
    foreach (var image in hemlockImages)
    {
        using (var stream = new MemoryStream(File.ReadAllBytes(image)))
        {
            trainingApi.CreateImagesFromData(project.Id, stream, new List<string>() { hemlockTag.Id.ToString() });
        }
    }

    // Or uploaded in a single batch 
    var imageFiles = japaneseCherryImages.Select(img => new ImageFileCreateEntry(Path.GetFileName(img), File.ReadAllBytes(img))).ToList();
    trainingApi.CreateImagesFromFiles(project.Id, new ImageFileCreateBatch(imageFiles, new List<Guid>() { japaneseCherryTag.Id }));
    ```

* __Az osztályozó által igénybe vett betanításához__:

    ```csharp
    // Now there are images with tags start training the project
    Console.WriteLine("\tTraining");
    var iteration = trainingApi.TrainProject(project.Id);

    // The returned iteration will be in progress, and can be queried periodically to see when it has completed
    while (iteration.Status == "Completed")
    {
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
    }
    ```

* __Állítsa be az előrejelzési végpont egy alapértelmezett iteráció__:

    ```csharp
    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
    ```

* __Hozzon létre egy előrejelzési végpontot__:
 
    ```csharp
    // Create a prediction endpoint, passing in obtained prediction key
    PredictionEndpoint endpoint = new PredictionEndpoint() { ApiKey = predictionKey };
    ```
 
* __Kép küldése az előrejelzési végpontra__:

    ```csharp
    // Make a prediction against the new project
    Console.WriteLine("Making a prediction:");
    var result = endpoint.PredictImage(project.Id, testImage);

    // Loop over each prediction and write out the results
    foreach (var c in result.Predictions)
    {
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
    }
    ```

## <a name="run-the-application"></a>Az alkalmazás futtatása

1. A következő módosításokat a betanítási és Predikciós kulcsok hozzáadni az alkalmazáshoz:

    * Adja hozzá a __képzési kulcs__ a következő sort:

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * Adja hozzá a __előrejelzési kulcs__ a következő sort:

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. Futtassa az alkalmazást. Az alkalmazás futása íródik a következő kimenetet a konzolon:

    ```
    Creating new project:
            Uploading images
            Training
    Done!

    Making a prediction:
            Hemlock: 95.0%
            Japanese Cherry: 0.0%
    ```

3. Nyomja le bármelyik billentyűt Kilépés az alkalmazásból.
