---
title: Az Azure Content Moderator – létrehozása .NET használatával videóátiratot felülvizsgálatok |} A Microsoft Docs
description: Videóátiratot létrehozása áttekinti az Azure Content Moderator SDK használatával a .NET-hez
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/19/2018
ms.author: sajagtap
ms.openlocfilehash: 1bb1a61f8ee4cd1cffcd7ce0b466947bbb0cbeaa
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182059"
---
# <a name="create-video-transcript-reviews-using-net"></a>Hozzon létre videóátiratot felülvizsgálatok .NET használatával

Ez a cikk nyújt információt, és kódminták segítségével gyorsan használatának első lépései a [Content Moderator SDK a C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) való:

- Létrehoz egy videó az emberi moderátorok
- A felülvizsgálat moderált átiratok hozzáadása
- A felülvizsgálat közzététele

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy [a videó Metz](video-moderation-api.md) és [a videó felülvizsgálat létrehozott](video-reviews-quickstart-dotnet.md) döntéshozatalt az emberi felülvizsgálati eszközben. Most szeretné moderált videó szövegekben hozzáadása a felülvizsgálati eszközben.

Ez a cikk azt is feltételezi, hogy már ismeri a Visual Studio és C#.

## <a name="sign-up-for-content-moderator"></a>Iratkozzon fel a Content Moderator

A REST API-t vagy az SDK-t a Content Moderator szolgáltatások használata előtt szüksége van egy előfizetési kulcsot.
Tekintse meg a [rövid](quick-start.md) megtudhatja, hogyan szerezheti be a kulcsot.

## <a name="sign-up-for-a-review-tool-account-if-not-completed-in-the-previous-step"></a>Ha nem végzi el az előző lépésben felülvizsgálati eszköz fiókot regisztráljon

Ha kapott a Content Moderator az Azure Portalon is [a felülvizsgálati eszköz fiók](https://contentmoderator.cognitive.microsoft.com/) , és tekintse át a csoport létrehozása. A csoport azonosítója, és indítsa el a feladatot, és tekintse meg az értékelések a vizsgálóeszközt, a felülvizsgálati API hívása a felülvizsgálati eszköz szükséges.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Győződjön meg arról, az API-kulcs segítségével meghívhatja a felülvizsgálati API (projekt létrehozása)

Az előző lépések végrehajtását követően, előfordulhat, hogy végül két a Content Moderator kulcs Ha használatba az Azure Portalról. 

Ha azt tervezi, használja az Azure által biztosított API-kulcsot az SDK-minta, hajtsa végre a szereplő lépéseket a [a felülvizsgálati API-val az Azure key](review-tool-user-guide/credentials.md#use-the-azure-account-with-the-review-tool-and-review-api) szakaszban, hogy az alkalmazása a felülvizsgálati API-t, és létrehozni a felülvizsgálatok.

Ingyenes próba hozza létre a kulcsot a felülvizsgálati eszköz használatakor a felülvizsgálati eszköz fiók már ismer a kulcsot, és ezért semmilyen további lépésekre szükség.

## <a name="prepare-your-video-for-review"></a>Tekintse át a videó előkészítése

Adja hozzá az átiratot videót felülvizsgálatok. A videó online közzé kell tenni. A streamvégpont van szüksége. A streamvégpont lehetővé teszi, hogy a felülvizsgálati eszköz videólejátszó lejátszani a videót.

![Bemutató videó miniatűrje](images/ams-video-demo-view.PNG)

- Másolás a **URL-cím** ezen [bemutató az Azure Media Services](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) lapját a jegyzékfájl URL-CÍMÉT.

## <a name="create-your-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. Vegyen fel egy új **Console app (.NET Framework)** projektet a megoldáshoz.

1. Adja a projektnek **VideoTranscriptReviews**.

1. Jelölje ki a projektet a megoldáshoz egyetlen indítási projektként.

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

A következő, a TermLists projekt NuGet-csomagok telepítése.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Frissítés a program által utasítások segítségével.

Módosítsa a program a következő using utasításokat.

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;
    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;


### <a name="add-private-properties"></a>Adja hozzá a saját tulajdonságai

Adja hozzá a következő privát tulajdonságok névtérhez VideoTranscriptReviews, osztály Program.

A felsoroltak, cserélje le a példában szereplő értékeket ezekhez a tulajdonságokhoz.


    namespace VideoReviews
    {
        class Program
        {
            // NOTE: Replace this example location with the location for your Content Moderator account.
            /// <summary>
            /// The region/location for your Content Moderator account, 
            /// for example, westus.
            /// </summary>
            private static readonly string AzureRegion = "YOUR CONTENT MODERATOR REGION";

            // NOTE: Replace this example key with a valid subscription key.
            /// <summary>
            /// Your Content Moderator subscription key.
            /// </summary>
            private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

            // NOTE: Replace this example team name with your Content Moderator team name.
            /// <summary>
            /// The name of the team to assign the job to.
            /// </summary>
            /// <remarks>This must be the team name you used to create your 
            /// Content Moderator account. You can retrieve your team name from
            /// the Content Moderator web site. Your team name is the Id associated 
            /// with your subscription.</remarks>
            private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

            /// <summary>
            /// The base URL fragment for Content Moderator calls.
            /// </summary>
            private static readonly string AzureBaseURL =
                $"{AzureRegion}.api.cognitive.microsoft.com";

            /// <summary>
            /// The minimum amount of time, in milliseconds, to wait between calls
            /// to the Content Moderator APIs.
            /// </summary>
            private const int throttleRate = 2000;


### <a name="create-content-moderator-client-object"></a>Content Moderator ügyfélobjektum létrehozása

Adja hozzá a következő definice metody névtér VideoTranscriptReviews, osztály Program.

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
        {
            Endpoint = AzureBaseURL
        };
    }

## <a name="create-a-video-review"></a>Létrehoz egy videó

Létrehoz egy videó- **ContentModeratorClient.Reviews.CreateVideoReviews**. További információkért lásd: a [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** a következő szükséges paraméterek:
1. Egy karakterlánc, amely tartalmazza a MIME-típust, amely elvileg "application/json." 
1. A Content Moderator csoport neve.
1. Egy **IList<CreateVideoReviewsBodyItem>**  objektum. Minden egyes **CreateVideoReviewsBodyItem** objektum videót felülvizsgálatok jelöli. Ebben a rövid útmutatóban létrehoz egy felülvizsgálati egyszerre.

**CreateVideoReviewsBodyItem** néhány olyan tulajdonság tartozik. Minimális állítsa be a következő tulajdonságokat:
- **Tartalom**. A Videó URL-címét a programtulajdonos.
- **ContentId**. A videó felülvizsgálat rendel azonosító.
- **Állapot**. Állítsa be az értéket "Unpublished." Ha nincs beállítva, a rendszer alapértelmezés szerint, "Függő", ami azt jelenti, hogy a videó felülvizsgálat közzé van téve, és emberi folyamatban. A videó felülvizsgálat közzététele után már nem adhat videókban, a szöveges vagy szöveges moderálás eredményt azt.

> [!NOTE]
> **CreateVideoReviews** adja vissza az IList<string>. Ezek a karakterláncok mindegyike tartalmaz egy videót felülvizsgálatok Azonosítót. Azonosítóit a részletekben GUID és nem ugyanaz, mint az értékét a **ContentId** tulajdonság. 

Adja hozzá a következő definice metody névtér VideoReviews, osztály Program.

    /// <summary>
    /// Create a video review. For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="id">The ID to assign to the video review.</param>
    /// <param name="content">The URL of the video to review.</param>
    /// <returns>The ID of the video review.</returns>
    private static string CreateReview(ContentModeratorClient client, string id, string content)
    {
        Console.WriteLine("Creating a video review.");

        List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
            new CreateVideoReviewsBodyItem
            {
                Content = content,
                ContentId = id,
                /* Note: to create a published review, set the Status to "Pending".
                However, you cannot add video frames or a transcript to a published review. */
                Status = "Unpublished",
            }
        };

        var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

        Thread.Sleep(throttleRate);

        // We created only one review.
        return result[0];
    }

> [!NOTE]
> A Content Moderator Szolgáltatáskulcs rendelkezik egy második (RPS) sávszélesség-korlátjának kérelemből. Ha túllépi a korlátot, akkor az SDK-t egy 429 hibakód kivételt jelez. 
>
> Ingyenes szint kulcs esetében egy függő Entitás sebessége.

## <a name="add-transcript-to-video-review"></a>Átirat hozzá Videós áttekintése

Ad hozzá egy átiratok a videó felülvizsgálat **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** a következő szükséges paraméterek:
1. A Content Moderator csapat azonosítóját.
1. A videó felülvizsgálat azonosítója által visszaadott **CreateVideoReviews**.
1. A **Stream** objektum, amely tartalmazza a szövegben.

Az átirat WebVTT formátumban kell lennie. További információkért lásd: [WebVTT: A webes videó nyomon követi szövegformátum](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> A program egy minta szöveges VTT formátumban. A való életből vett megoldás használata az Azure Media Indexer szolgáltatás [hozzon létre egy szöveges](https://docs.microsoft.com/azure/media-services/media-services-index-content) egy videó hangjának.

Adja hozzá a következő definice metody névtér VideotranscriptReviews, osztály Program.

    /// <summary>
    /// Add a transcript to the indicated video review.
    /// The transcript must be in the WebVTT format.
    /// For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="transcript">The video transcript.</param>
    static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
    {
        Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
        client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
        Thread.Sleep(throttleRate);
    }

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Egy szöveges moderálás eredményt ad hozzá Videós áttekintése

Egy szöveges videót felülvizsgálatok hozzáadásán is hozzáadhat moderálása, átirat eredményét. Ekkor a **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. További információkért lásd: a [API-referencia](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** a következő szükséges paraméterek:
1. Egy karakterlánc, amely tartalmazza a MIME-típust, amely elvileg "application/json." 
1. A Content Moderator csoport neve.
1. A videó felülvizsgálat azonosítója által visszaadott **CreateVideoReviews**.
1. IList<TranscriptModerationBodyItem>. A **TranscriptModerationBodyItem** tulajdonságai a következők:
- **Feltételek**. IList<TranscriptModerationBodyItemTermsItem>. A **TranscriptModerationBodyItemTermsItem** tulajdonságai a következők:
- **Index**. A kifejezés a nulla alapú indexét.
- **Kifejezés**. Az előfizetési időszak tartalmazó karakterlánc.
- **Időbélyeg**. Tartalmazó, (másodpercben), az idő, amikor a használati találhatók a szöveges karakterláncot.

Az átirat WebVTT formátumban kell lennie. További információkért lásd: [WebVTT: A webes videó nyomon követi szövegformátum](https://www.w3.org/TR/webvtt1/).

Adja hozzá a következő definice metody névtér VideoTranscriptReviews, osztály Program. Ez a módszer egy, átirat elküldi a **ContentModeratorClient.TextModeration.ScreenText** metódust. Azt is fordítja le az eredmény IList<TranscriptModerationBodyItem>, és a helyrendszerekre **AddVideoTranscriptModerationResult**.

    /// <summary>
    /// Add the results of moderating a video transcript to the indicated video review.
    /// For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="transcript">The video transcript.</param>
    static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
    {
        Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

        // Screen the transcript using the Text Moderation API. For more information, see:
        // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
        Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

        // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
        List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
        if (null != screen.Terms)
        {
            foreach (var term in screen.Terms)
            {
                if (term.Index.HasValue)
                {
                    terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
                }
            }
        }

        List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
        {
            new TranscriptModerationBodyItem()
            {
                Timestamp = "0",
                Terms = terms
            }
        };

        client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

        Thread.Sleep(throttleRate);
    }


## <a name="publish-video-review"></a>Közzététel a videós áttekintése

A videó értékelést tesz közzé **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** a következő szükséges paraméterek:
1. A Content Moderator csoport neve.
1. A videó felülvizsgálat azonosítója által visszaadott **CreateVideoReviews**.

Adja hozzá a következő definice metody névtér VideoReviews, osztály Program.

    /// <summary>
    /// Publish the indicated video review. For more information, see the reference API:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    private static void PublishReview(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Publishing the review with ID {0}.", review_id);
        client.Reviews.PublishVideoReview(TeamName, review_id);
        Thread.Sleep(throttleRate);
    }

## <a name="putting-it-all-together"></a>A teljes kép

Adja hozzá a **fő** metódus VideoTranscriptReviews, névtér-definíciót a Program osztályhoz. Végül zárja be a Program osztályt, valamint a VideoTranscriptReviews névteret.

> [!NOTE]
> A program egy minta szöveges VTT formátumban. A való életből vett megoldás használata az Azure Media Indexer szolgáltatás [hozzon létre egy szöveges](https://docs.microsoft.com/azure/media-services/media-services-index-content) egy videó hangjának. 

    static void Main(string[] args)
    {
        using (ContentModeratorClient client = NewClient())
        {
            // Create a review with the content pointing to a streaming endpoint (manifest)
            var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
            string review_id = CreateReview(client, "review1", streamingcontent);

            var transcript = @"WEBVTT

            01:01.000 --> 02:02.000
            First line with a crap word in a transcript.

            02:03.000 --> 02:25.000
            This is another line in the transcript.
            ";

            AddTranscript(client, review_id, transcript);

            AddTranscriptModerationResult(client, review_id, transcript);

            // Publish the review
            PublishReview(client, review_id);

            Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
            Console.WriteLine("Press any key to close the application.");
            Console.ReadKey();
        }
    }

## <a name="run-the-program-and-review-the-output"></a>Futtassa a programot, és tekintse át a kimenetet

Az alkalmazás futtatásakor a látható kimenet a következő sorokat:

    Creating a video review.
    Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Open your Content Moderator Dashboard and select Review > Video to see the review.
    Press any key to close the application.

## <a name="navigate-to-your-video-transcript-review"></a>Keresse meg a videóátiratot áttekintése

A videóátiratot felülvizsgálatot a Content Moderator felülvizsgálati eszközben lépjen a **áttekintése**>**videó**>**átirat** képernyő.

Megjelenik a következő funkciókat:
- A hozzáadott szöveges két sornyi
- Káromkodás kifejezés található, és a szöveg moderálása szolgáltatás kiemelésével
- A videó egy beszédátírási szöveg kijelölése indul az időbélyeg

![Az emberi moderátorok videóátiratot áttekintése](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>További lépések

Első a [Content Moderator .NET SDK-val](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) és a [Visual Studio-megoldás](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) ennél és a többi a Content Moderator rövid útmutató a .NET-hez.

Ismerje meg, hogyan hozhat létre [videót felülvizsgálatok](video-reviews-quickstart-dotnet.md) a felülvizsgálati eszközben.

Tekintse meg a részletes oktatóanyag, hogyan hozhat létre egy [videomoderálás megoldás befejezéséhez](video-transcript-moderation-review-tutorial-dotnet.md).
