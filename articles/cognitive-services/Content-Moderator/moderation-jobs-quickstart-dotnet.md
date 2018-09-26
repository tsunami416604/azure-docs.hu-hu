---
title: Az Azure Content Moderator – kezdő moderálás feladatok .NET használatával |} A Microsoft Docs
description: .NET-hez készült Azure Content Moderator SDK használatával moderálás feladatok indítása
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/10/2018
ms.author: sajagtap
ms.openlocfilehash: 3761552f81bd733f9c93fab40db07ef6f5a6a7f6
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181600"
---
# <a name="start-moderation-jobs-using-net"></a>.NET-tel moderálás feladatok indításához

Ez a cikk nyújt információt, és kódminták segítségével történő használatának első lépései a [Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) való:
 
- Vizsgálat, és hozzon létre értékelések emberi moderátorok moderálás feladat indítása
- A függőben lévő felülvizsgálati állapotának lekérése
- Nyomon követheti, és a felülvizsgálat végső állapotának lekérése
- Küldje el az eredmény a visszahívási URL-cím

Ez a cikk azt feltételezi, hogy már ismeri a Visual Studio és C#.

## <a name="sign-up-for-content-moderator"></a>Iratkozzon fel a Content Moderator

A REST API-t vagy az SDK-t a Content Moderator szolgáltatások használata előtt szüksége van egy előfizetési kulcsot.
Tekintse meg a [rövid](quick-start.md) megtudhatja, hogyan szerezheti be a kulcsot.

## <a name="sign-up-for-a-review-tool-account-if-not-completed-in-the-previous-step"></a>Ha nem végzi el az előző lépésben felülvizsgálati eszköz fiókot regisztráljon

Ha kapott a Content Moderator az Azure Portalon is [a felülvizsgálati eszköz fiók](https://contentmoderator.cognitive.microsoft.com/) , és tekintse át a csoport létrehozása. A csoport azonosítója, és indítsa el a feladatot, és tekintse meg az értékelések a vizsgálóeszközt, a felülvizsgálati API hívása a felülvizsgálati eszköz szükséges.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Győződjön meg arról, az API-kulcs segítségével meghívhatja a felülvizsgálati API felülvizsgálat létrehozása

Az előző lépések végrehajtását követően, előfordulhat, hogy végül két a Content Moderator kulcs Ha használatba az Azure Portalról. 

Ha azt tervezi, használja az Azure által biztosított API-kulcsot az SDK-minta, hajtsa végre a szereplő lépéseket a [a felülvizsgálati API-val az Azure key](review-tool-user-guide/credentials.md#use-the-azure-account-with-the-review-tool-and-review-api) szakaszban, hogy az alkalmazása a felülvizsgálati API-t, és létrehozni a felülvizsgálatok.

Ingyenes próba hozza létre a kulcsot a felülvizsgálati eszköz használatakor a felülvizsgálati eszköz fiók már ismer a kulcsot, és ezért semmilyen további lépésekre szükség.

## <a name="define-a-custom-moderation-workflow"></a>Egy egyéni moderálás munkafolyamatokat

A moderálás feladat megvizsgálja az API-kkal és használ a tartalom egy **munkafolyamat** annak megállapításához, hogy hozhat létre értékelések, vagy sem.
Bár a vizsgálóeszközt tartalmaz egy alapértelmezett munkafolyamat hozzunk [egy egyéni munkafolyamatokat](Review-Tool-User-Guide/Workflows.md) ebben a rövid útmutatóban.

A munkafolyamat neve a kódban, amely elindítja a moderálás feladatot használhatja.

## <a name="create-your-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. Vegyen fel egy új **Console app (.NET Framework)** projektet a megoldáshoz.

   A mintakód adja a projektnek **CreateReviews**.

1. Jelölje ki a projektet a megoldáshoz egyetlen indítási projektként.

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

A következő NuGet-csomagok telepítéséhez:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Frissítés a program által utasítások segítségével.

Módosítsa a program által utasítások segítségével.

    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="create-the-content-moderator-client"></a>A Content Moderator ügyfél létrehozása

Adja hozzá a következő kódot a Content Moderator ügyfélbeállítások az előfizetéshez.

> [!IMPORTANT]
> Frissítés a **AzureRegion** és **CMSubscriptionKey** mezőket a régió azonosítója és az Előfizetés kulcs értékét.


    /// <summary>
    /// Wraps the creation and configuration of a Content Moderator client.
    /// </summary>
    /// <remarks>This class library contains insecure code. If you adapt this 
    /// code for use in production, use a secure method of storing and using
    /// your Content Moderator subscription key.</remarks>
    public static class Clients
    {
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "YOUR API REGION";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"https://{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR API KEY";

        /// <summary>
        /// Returns a new Content Moderator client for your subscription.
        /// </summary>
        /// <returns>The new client.</returns>
        /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
        /// When you have finished using the client,
        /// you should dispose of it either directly or indirectly. </remarks>
        public static ContentModeratorClient NewClient()
        {
            // Create and initialize an instance of the Content Moderator API wrapper.
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

            client.Endpoint = AzureBaseURL;
            return client;
        }
    }

### <a name="initialize-application-specific-settings"></a>Alkalmazás-specifikus beállítások inicializálása

Adja hozzá a következő állandókat és a statikus mezők a **Program** osztály a program.cs fájlban.

> [!NOTE]
> A TeamName konstans értékre állítjuk, a Content Moderator előfizetés létrehozásakor használt név. Kérheti le a TeamName a [a Content Moderator webhely](https://westus.contentmoderator.cognitive.microsoft.com/).
> Miután bejelentkezett, válassza ki a **hitelesítő adatok** származó a **beállítások** (fogaskerék) menüben.
>
> A csapat nevét az az érték a **azonosító** mezőbe a **API** szakaszban.


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
    /// the Content Moderator web site. Your team name is the Id associated 
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
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const int latencyDelay = 45;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Reviews show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "";

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Adja hozzá a kódot az automatikus Közepes, létrehoz egy és a feladat részleteinek beolvasása

> [!Note]
> A gyakorlatban a visszahívási URL-Címének beállítása **CallbackEndpoint** az URL-címet, amely megkapja a manuális ellenőrzést (keresztül egy HTTP POST-kérés) eredményét.

Először adja hozzá a következő kódot a **fő** metódust.

    using (TextWriter writer = new StreamWriter(OutputFile, false))
    {
        using (var client = Clients.NewClient())
        {
            writer.WriteLine("Create review job for an image.");
            var content = new Content(ImageUrl);
        
            // The WorkflowName contains the name of the workflow defined in the online review tool.
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
> A Content Moderator Szolgáltatáskulcs rendelkezik egy második (RPS) sávszélesség-korlátjának kérelemből. Ha túllépi a korlátot, akkor az SDK-t egy 429 hibakód kivételt jelez. 
>
> Ingyenes szint kulcs esetében egy függő Entitás sebessége.

## <a name="run-the-program-and-review-the-output"></a>Futtassa a programot, és tekintse át a kimenetet

Az alábbi kimeneti példa a konzolon láthatja:

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

Jelentkezzen be a Content Moderator felülvizsgálati eszközében megtekintheti a függőben lévő rendszerképet, tekintse át.

Használja a **tovább** gombra kattintva küldje el.

![Az emberi moderátorok kép áttekintése](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>A naplófájl a minta kimenet

> [!NOTE]
> A kimeneti fájl, a karakterláncok **Teamname**, **ContentId**, **CallBackEndpoint**, és **WorkflowId** tükrözze ezeket az értékeket használta korábban.

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


## <a name="your-callback-url-if-provided-receives-this-response"></a>A visszahívási URL-címét. Ha meg van adva, a válasz fogadása.

A válasz az alábbi példához hasonlóan jelenik meg:

> [!NOTE]
> A visszahívási válaszként, a karakterláncok **ContentId** és **WorkflowId** tükrözik a korábban használt értékeket.

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

Első a [Content Moderator .NET SDK-val](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) és a [Visual Studio-megoldás](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) ennél és a többi a Content Moderator rövid útmutató a .NET-hez, és az integrációval kapcsolatos első lépések.
