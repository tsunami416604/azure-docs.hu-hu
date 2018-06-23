---
title: Egyéni stratégiai szolgáltatás használata a C#-alkalmazás - Azure kognitív szolgáltatások |} Microsoft Docs
description: Fedezze fel egy alapszintű C#-alkalmazást, amely az egyéni Látástechnológiai API kognitív Microsoft-szolgáltatásokban. Projekt létrehozása, címkéket, képek feltöltése, a projekt betanítása és előrejelzéshez ellenőrizze az alapértelmezett végpont használatával.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 80cb022808748ed2c60dff7c363d6020cb4043a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347863"
---
# <a name="use-the-custom-vision-service-from-a-c35-application"></a>Az egyéni stratégiai szolgáltatással a c&#35; alkalmazás

Megtudhatja, hogyan használhatja az egyéni stratégiai szolgáltatást egy C#-alkalmazás. Után létrejön, akkor is címkéket, képek feltöltése, a projekt betanítása, a projekt alapértelmezett előrejelzés végponti URL-Címének beszerzése és a végpont a programozott módon tesztelheti a lemezkép. A nyílt forráskódú példa használata sablonként használatával történő megtervezésével saját alkalmazás a Windows az egyéni stratégiai Service API-t.

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio 2015-öt vagy a Windows 2017 bármely kiadása.

* A [egyéni stratégiai szolgáltatás SDK](http://github.com/Microsoft/Cognitive-CustomVision-Windows/). Ez magában foglalja a minta és az ebben a dokumentumban használt képek.

## <a name="get-the-training-and-prediction-keys"></a>A képzés és előrejelzés kulcsok beszerzése

Ebben a példában használt kulcsok beszerzéséhez látogassa meg a [egyéni stratégiai weblap](https://customvision.ai) válassza ki a __fogaskerék ikonra__ jobb felső részén található. Az a __fiókok__ szakaszban, másolja a __képzési kulcs__ és __előrejelzés kulcs__ mezőket.

![A felhasználói felület kulcsok képe](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>A kód értelmezése

A Visual Studióban nyissa meg a projektet, található, a `Samples/CustomVision.Sample/` könyvtár az SDK projekt.

Ez az alkalmazás korábban nevű új projekt létrehozásához kapott képzési kulcsot használja az __saját új projekt__. Majd feltölti a lemezképeket képzése, és tesztelje a besorolás. A besorolás kiderül, hogy a fa egy __Hemlock__ vagy egy __japán cseresznye__.

Az alábbi kódrészleteket valósítja meg a jelen példában az elsődleges funkciót:

* __Hozzon létre egy új egyéni stratégiai szolgáltatás projektet__:

    ```csharp
     // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project");
    ```

* __Címkék létrehozása projektben__:

    ```csharp
    // Make two tags in the new project
    var hemlockTag = trainingApi.CreateTag(project.Id, "Hemlock");
    var japaneseCherryTag = trainingApi.CreateTag(project.Id, "Japanese Cherry");
    ```

* __Töltse fel, és címkézhetők képek__:

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

* __A osztályozó betanítása__:

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

* __Állítsa be a előrejelzés végpont egy alapértelmezett iterációs__:

    ```csharp
    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
    ```

* __Előrejelzés-végpont létrehozása__:
 
    ```csharp
    // Create a prediction endpoint, passing in obtained prediction key
    PredictionEndpoint endpoint = new PredictionEndpoint() { ApiKey = predictionKey };
    ```
 
* __Kép küldése az előrejelzés végponthoz__:

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

1. A következő módosításokat a képzés és előrejelzés kulcsok hozzáadni az alkalmazáshoz:

    * Adja hozzá a __képzési kulcs__ számára a következő sort:

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * Adja hozzá a __előrejelzés kulcs__ számára a következő sort:

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. Futtassa az alkalmazást. Az alkalmazás futtatása, mert a következő kimeneti írása a konzolhoz:

    ```
    Creating new project:
            Uploading images
            Training
    Done!

    Making a prediction:
            Hemlock: 95.0%
            Japanese Cherry: 0.0%
    ```

3. Nyomja le bármelyik billentyűt, hogy kilép az alkalmazásból.
