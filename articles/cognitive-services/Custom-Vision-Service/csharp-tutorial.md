---
title: 'Oktatóanyag: Windows-alkalmazás készítése Custom Vision szolgáltatáshoz C# nyelven'
titlesuffix: Azure Cognitive Services
description: Hozzon létre projektet, adjon hozzá címkéket, töltsön fel képeket, tanítsa be a projektet és adjon előrejelzést az alapértelmezett végpont használatával.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 9e5ed71d4620f7ffeac8acb15f90d67964a86870
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366641"
---
# <a name="tutorial-use-the-custom-vision-service-from-a-c-application"></a>Oktatóanyag: Custom Vision Service használata C# alkalmazásból

Ismerje meg hogyan történhet a Custom Vision Service használata C# alkalmazásból. Miután elkészült, adhat hozzá címkéket, tölthet fel képeket, betaníthatja a projektet, megkaphatja a projekt alapértelmezett előrejelzési végpont URL-címét és ezt a végpontot felhasználhatja kép programozott tesztelésére. Ez a nyílt forráskódú példa sablonként használható saját, Custom Vision Service API használatával készülő Windows alkalmazásaihoz.

## <a name="prerequisites"></a>Előfeltételek

* A Visual Studio 2017 bármely kiadása Windowsra.

## <a name="get-the-custom-vision-sdk-and-samples"></a>A Custom Vision SDK és példák letöltése
A példa elkészítéséhez szüksége lesz a Custom Vision SDK NuGet-csomagokra:

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

A képeket letöltheti a [C# példákkal](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/CustomVision) együtt.

## <a name="get-the-training-and-prediction-keys"></a>A betanítási és előrejelzési kulcsok letöltése

Az ebben a példában használt kulcsok megszerzéséhez látogasson el a [Custom Vision weboldalra](https://customvision.ai), válassza ki a __fogaskerék ikont__ a jobb felső sarokban. A __Fiókok__ területen másolja ki a __Betanítási kulcs__ és __Előrejelzési kulcs__ mezők értékeit.

![A kulcsok felhasználói felület képe](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>A kód értelmezése

A Visual Studióban nyissa meg az SDK projekt `Samples/CustomVision.Sample/` könyvtárában található projektet.

Ez az alkalmazás a korábban lekért betanítási kulcsot használva létrehozza a __My New Projekt__ nevű új projektet. Utána feltölti a képeket az osztályozó tanítására és kipróbálására. Az osztályozó azonosítja, hogy a fa __kanadai fenyő__ vagy __japán cseresznye__-e.

A példa fő funkcióit az alábbi kódrészlet valósítja meg:

* __Új Custom Vision Service-projekt létrehozása__:

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

* __Képek feltöltése és címkézése__:

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

* __Az osztályozó betanítása__:

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

* __Alapértelmezett iteráció beállítása az előrejelzési végpontra__:

    ```csharp
    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
    ```

* __Előrejelzési végpont létrehozása__:
 
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

1. A következő módosításokkal adja hozzá a betanítási és előrejelzési kulcsokat az alkalmazáshoz:

    * Adja hozzá a __betanítási kulcsot__ a következő sorba:

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * Adja hozzá az __előrejelzési kulcsot__ a következő sorba:

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. Futtassa az alkalmazást. Az alkalmazás futása közben a következő kimenet írja ki a konzolra:

    ```
    Creating new project:
            Uploading images
            Training
    Done!

    Making a prediction:
            Hemlock: 95.0%
            Japanese Cherry: 0.0%
    ```

3. Az alkalmazásból történő kilépéshez nyomjon meg egy billentyűt.
