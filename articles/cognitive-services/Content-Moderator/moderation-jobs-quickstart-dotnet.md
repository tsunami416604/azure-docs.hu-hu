---
title: Az Azure Content moderátor - indítási moderálás feladatok .NET használatával |} Microsoft Docs
description: Hogyan moderálás feladatokat Azure tartalom moderátor SDK for .NET használatával
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/06/2018
ms.author: sajagtap
ms.openlocfilehash: a103875607355993e216ce1ddea02009fc8fa1c4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347079"
---
# <a name="start-moderation-jobs-using-net"></a>Indítsa el a moderálás feladatok .NET használatával

Ez a cikk információkat tartalmazza, és mintakódok segítséget első lépései a .NET-hez, hogy a tartalom moderátor SDK használatával:
 
- Vizsgálati, és hozzon létre értékelést emberi moderátorok moderálás feladat indítása
- A függőben lévő felülvizsgálat állapotának beolvasása
- Követésének és a nézze át a végső állapot
- Küldje el az eredmény a visszahívási URL-cím

Ez a cikk feltételezi, hogy Ön már ismeri a Visual Studio és a C#.

## <a name="sign-up-for-content-moderator-services"></a>Iratkozzon fel a tartalom moderátor szolgáltatások

Tartalom moderátor-szolgáltatások díjairól a REST API-t vagy az SDK használata előtt be kell egy előfizetési kulcsot.
Tekintse meg a [gyors üzembe helyezés](quick-start.md) megtudhatja, hogyan szerezhet a kulcsot.

## <a name="define-a-custom-moderation-workflow"></a>Adja meg egy egyéni moderálás munkafolyamat

Egy moderálás feladat megvizsgálja az API-k és használ a tartalom egy **munkafolyamat** annak megállapításához, hogy értékelést létrehozásához, vagy nem.
Amíg a felülvizsgálati eszköz tartalmaz egy alapértelmezett munkafolyamat most [adja meg egy egyéni munkafolyamat](Review-Tool-User-Guide/Workflows.md) a gyors üzembe helyezés a.

A munkafolyamat nevét a kódban, amely elindítja a moderálás feladatot használja.

## <a name="create-your-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. Adjon hozzá egy új **Konzolalkalmazás (.NET-keretrendszer)** -projektet a megoldásban.

   A mintakód a nevet a projektnek **CreateReviews**.

1. Jelölje ki a projektet a megoldásban egyetlen kiindulási projektként.

1. Adjon hozzá egy hivatkozást, a **ModeratorHelper** szerelvény a projektre a [tartalom moderátor ügyfél segítő gyors üzembe helyezés](content-moderator-helper-quickstart-dotnet.md).

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

A következő NuGet-csomagok telepítése:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Frissítés a program által utasítások segítségével.

Módosítsa a program által using utasításokat.

    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="initialize-application-specific-settings"></a>Az alkalmazás-specifikus beállításokat inicializálása

Adja hozzá a következő állandók és a statikus mezők a **Program** osztály a program.cs fájlban.

> [!NOTE]
> A TeamName konstans értékre a tartalom moderátor előfizetés létrehozásakor használt név. A TeamName lekérése a [tartalom moderátor webhely](https://westus.contentmoderator.cognitive.microsoft.com/).
> Után jelentkezzen be, válassza a **hitelesítő adatok** a a **beállítások** (fogaskerék) menü.
>
> A csoport nevét az az érték a **azonosító** mező mellett a **API** szakasz.


    /// <summary>
    /// The moderation job will use this workflow that you defined earlier.
    /// See the quickstart article to learn how to setup custom workflows.
    /// </summary>
    private const string WorkflowName = "OCR";
    
    /// <summary>
    /// The name of the team to assign the job to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Conent Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "***";

    /// <summary>
    /// The URL of the image to create a review job for.
    /// </summary>
    private const string ImageUrl =
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const int latencyDelay = 45;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Revies show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "";

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Automatikus Közepes hozzá a kódot, hozzon létre egy áttekintése és a feladat részletes

> [!Note]
> A gyakorlatban a visszahívási URL-Címének beállítása **CallbackEndpoint** URL-címet, amely megkapja a (via HTTP POST-kérelmet) manuális felülvizsgálati eredményeit.

Először vegyen fel a következő kódot a **fő** metódust.

    using (TextWriter writer = new StreamWriter(OutputFile, false))
    {
        using (var client = Clients.NewClient())
        {
            writer.WriteLine("Create review job for an image.");
            var content = new Content(ImageUrl);
        
            // The WorkflowName contains the nameof the workflow defined in the online review tool.
            // See the quickstart article to learn more.
            var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                    TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

            // Record the job ID.
            var jobId = jobResult.Result.Body.JobIdProperty;

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                jobResult.Result.Body, Formatting.Indented));

            Thread.Sleep(2000);
            writer.WriteLine();

            writer.WriteLine("Get review job status.");
            var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                    TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                    jobDetails.Result.Body, Formatting.Indented));

            Console.WriteLine();
            Console.WriteLine("Perform manual reviews on the Content Moderator site.");
            Console.WriteLine("Then, press any key to continue.");
            Console.ReadKey();

            Console.WriteLine();
            Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
            Thread.Sleep(latencyDelay * 1000);

            writer.WriteLine("Get review details.");
            jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
            TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
            jobDetails.Result.Body, Formatting.Indented));
        }
        writer.Flush();
        writer.Close();
    }

> [!NOTE]
> A tartalom moderátor szolgáltatás kulcs egy kérelmek / második (RPS) sávszélesség-korlátjának rendelkezik. Ha meghaladja a korlátot, az SDK kivételt 429-es jelű hibakóddal. 
>
> Ingyenes szint kulcs egy RPS sávszélesség-korlátjának rendelkezik.

## <a name="run-the-program-and-review-the-output"></a>Futtassa a programot, és tekintse át a kimenetet

A konzolon a következő minta kimenet jelenik meg:

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

Jelentkezzen be a tartalom moderátor tekintse át az eszköz tekintse át a függőben lévő kép megtekintéséhez.

Használja a **következő** gombra kattintva küldje el.

![Az emberi moderátorok kép áttekintése](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Lásd: a minta kimenet a naplófájlban

> [!NOTE]
> A kimeneti fájl, a karakterláncok **Teamname**, **ContentId**, **CallBackEndpoint**, és **WorkflowId** használt értékeknek felelnek korábban.

    Create moderation job for an image.
    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

    Get review details.
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": [
        {
            "Ts": "2018-01-07T00:38:26.7714671",
            "Msg": "Successfully got hasText response from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:26.4181346",
            "Msg": "Getting hasText from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:25.5122828",
            "Msg": "Starting Execution - Try 1"
        }
        ]
    }


## <a name="your-callback-url-if-provided-receives-this-response"></a>A visszahívási URL-címet ad meg, ha a válasz fogadása.

Az alábbi példához hasonló válasz jelenik meg:

> [!NOTE]
> A visszahívási válaszként, a karakterláncok **ContentId** és **WorkflowId** tükrözik a korábban használt értékek.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
        "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
        "WorkFlowId": "OCR",
        "Status": "Complete",
        "ContentType": "Image",
        "CallBackType": "Job",
        "ContentId": "contentID",
        "Metadata": {
            "hastext": "True",
            "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
            "imagename": "contentID"
        }
    }


## <a name="next-steps"></a>További lépések

[Töltse le a Visual Studio megoldás](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) ennél és egyéb tartalom moderátor quickstarts a .NET-hez, és az integráció a kezdéshez.
