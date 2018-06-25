---
title: Az Azure Content moderátor - videó Beszélgetés szövegének értékelést .NET használatával hozzon létre |} Microsoft Docs
description: Videó Beszélgetés szövegének létrehozása ellenőrzi, hogy Azure tartalom moderátor SDK for .NET használatával
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/19/2018
ms.author: sajagtap
ms.openlocfilehash: 3286da6e38f0fba02386d877a835fb694ed0fdec
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "35347583"
---
# <a name="create-video-transcript-reviews-using-net"></a>Hozzon létre videó Beszélgetés szövegének értékelést .NET használatával

Ez a cikk és a segítségével gyorsan Kódminták C# való a tartalom moderátor SDK használatának megkezdése:

- Hozzon létre egy videó tekintse át az emberi moderátorok
- Az ellenőrzéshez a moderált Beszélgetés szövegének hozzáadása
- A felülvizsgálati közzététele

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik [a videó moderált](video-moderation-api.md) és [a videó felülvizsgálati létrehozott](video-reviews-quickstart-dotnet.md) a felülvizsgálati eszközben az emberi révén. Most hozzáadandó moderált videó ki a felülvizsgálati eszközben.

Ez a cikk is feltételezi, hogy Ön már ismeri a Visual Studio és a C#.

### <a name="sign-up-for-content-moderator-services"></a>Iratkozzon fel a tartalom moderátor szolgáltatások

Tartalom moderátor-szolgáltatások díjairól a REST API-t vagy az SDK használata előtt be kell egy előfizetési kulcsot.

A tartalom moderátor irányítópulton található az Előfizetés kulcs **beállítások** > **hitelesítő adatok** > **API**  >   **Az Ocp-Apim-előfizetés-Próbakulcs**. További információkért lásd: [áttekintése](overview.md).

### <a name="prepare-your-video-for-review"></a>Tekintse át a videó előkészítése

Adja hozzá a Beszélgetés szövegének videó áttekintése. A videó online közzé kell tenni. A streamvégpont van szüksége. A streamvégpont lehetővé teszi, hogy a felülvizsgálati eszköz videólejátszó a videó lejátszása.

![Bemutató videó miniatűr](images/ams-video-demo-view.PNG)

- Másolás a **URL-cím** ezen [Azure Media Services bemutató](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) lap a jegyzék URL-címhez.

## <a name="create-your-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. Adjon hozzá egy új **Konzolalkalmazás (.NET-keretrendszer)** -projektet a megoldásban.

1. Nevet a projektnek **VideoTranscriptReviews**.

1. Jelölje ki a projektet a megoldásban egyetlen kiindulási projektként.

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

A következő NuGet-csomagok a TermLists projekt telepítése.

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


### <a name="add-private-properties"></a>Adja hozzá a saját tulajdonságait

A következő személyes tulajdonságok hozzáadása a névtérhez VideoTranscriptReviews, osztály Program.

A jelzett, cserélje le a példában szereplő ezekhez a tulajdonságokhoz tartozó értékeket.


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
            /// the Conent Moderator web site. Your team name is the Id associated 
            /// with your subscription.</remarks>
            public static readonly string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

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


### <a name="create-content-moderator-client-object"></a>Tartalom moderátor ügyfél objektum létrehozása

A következő metódusdefiníciót hozzáadása a névtérhez VideoTranscriptReviews, osztály Program.

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
            BaseUrl = AzureBaseURL
        };
    }

## <a name="create-a-video-review"></a>Hozzon létre egy videó áttekintése

Hozzon létre egy videó tekintse át a **ContentModeratorClient.Reviews.CreateVideoReviews**. További információkért lásd: a [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** rendelkezik a következő kötelező paraméterek:
1. Karakterlánc, amely tartalmazza a MIME-típust, amely kell lennie az "application/json." 
1. A tartalom moderátor csoport neve.
1. Egy **IList<CreateVideoReviewsBodyItem>**  objektum. Minden egyes **CreateVideoReviewsBodyItem** objektum által jelképezett videó áttekintése. A gyors üzembe helyezés létrehoz egy felülvizsgálati egyszerre.

**CreateVideoReviewsBodyItem** több tulajdonságokkal rendelkezik. Minimális állítsa be a következő tulajdonságokkal:
- **Tartalom**. Javasoljuk, hogy a Videó URL-CÍMÉT.
- **ContentId**. A videó felülvizsgálati hozzárendelése azonosító.
- **Állapot**. Állítsa az értéket "Unpublished." Ha nincs megadva, alapértelmezett "függő"állapotba, ami azt jelenti, hogy a videó felülvizsgálati közzé van téve, emberi felülvizsgálati vár. Miután közzétette videó áttekintése, már nem adhat videó keretek, a Beszélgetés szövegének vagy a Beszélgetés szövegének moderálás eredmény azt.

> [!NOTE]
> **CreateVideoReviews** adja vissza az IList<string>. Ezek a karakterláncok mindegyikének a videó felülvizsgálati-Azonosítót tartalmaz. Ezek az azonosítók GUID-EK és nincs értéke megegyezik a **ContentId** tulajdonság. 

A következő metódusdefiníciót hozzáadása a névtérhez VideoReviews, osztály Program.

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
> A tartalom moderátor szolgáltatás kulcs egy kérelmek / második (RPS) sávszélesség-korlátjának rendelkezik. Ha meghaladja a korlátot, az SDK kivételt 429-es jelű hibakóddal. 
>
> Ingyenes szint kulcs egy RPS sávszélesség-korlátjának rendelkezik.

## <a name="add-transcript-to-video-review"></a>Adja hozzá Beszélgetés szövegének videó áttekintése

A Beszélgetés szövegének ad hozzá a videó áttekintése **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** rendelkezik a következő kötelező paraméterek:
1. A tartalom moderátor csapat azonosítóját.
1. A videó felülvizsgálati azonosító által visszaadott **CreateVideoReviews**.
1. A **adatfolyam** szövegének tartalmazó objektum.

A Beszélgetés szövegének a WebVTT formátumúnak kell lennie. További információkért lásd: [WebVTT: A webes videó számok szövegformátum](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> A program egy minta Beszélgetés szövegének VTT formátumban. Az Azure Media Indexer szolgáltatást használja a valós megoldás [készítése a Beszélgetés szövegének](https://docs.microsoft.com/azure/media-services/media-services-index-content) a videó.

A következő metódusdefiníciót hozzáadása a névtérhez VideotranscriptReviews, osztály Program.

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

## <a name="add-a-transcript-moderation-result-to-video-review"></a>A Beszélgetés szövegének moderálás eredmény hozzáadása videó áttekintése

A Beszélgetés szövegének videó áttekintése hozzáadásán is hozzáadhat az eredménye, hogy a Beszélgetés szövegének moderálás. Ezt a teszi **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. További információkért lásd: a [API-referencia](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** rendelkezik a következő kötelező paraméterek:
1. Karakterlánc, amely tartalmazza a MIME-típust, amely kell lennie az "application/json." 
1. A tartalom moderátor csoport neve.
1. A videó felülvizsgálati azonosító által visszaadott **CreateVideoReviews**.
1. IList<TranscriptModerationBodyItem>. A **TranscriptModerationBodyItem** tulajdonságai a következők:
- **Feltételek**. IList<TranscriptModerationBodyItemTermsItem>. A **TranscriptModerationBodyItemTermsItem** tulajdonságai a következők:
- **Index**. A kifejezés nulla alapú indexét.
- **Kifejezés**. A kifejezés tartalmazó karakterlánc.
- **Timestamp típusú**. Egy karakterlánc, amely tartalmazza, amelynél a feltételek találhatók szövegének az idő másodpercben.

A Beszélgetés szövegének a WebVTT formátumúnak kell lennie. További információkért lásd: [WebVTT: A webes videó számok szövegformátum](https://www.w3.org/TR/webvtt1/).

A következő metódusdefiníciót hozzáadása a névtérhez VideoTranscriptReviews, osztály Program. Ez a módszer elküldi a Beszélgetés szövegének az a **ContentModeratorClient.TextModeration.ScreenText** metódust. Azt is fordítja le az eredmény IList<TranscriptModerationBodyItem>, és a helyrendszerekre **AddVideoTranscriptModerationResult**.

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


## <a name="publish-video-review"></a>Videó felülvizsgálati közzététele

Beállíthatja a videó áttekintése **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** rendelkezik a következő kötelező paraméterek:
1. A tartalom moderátor csoport neve.
1. A videó felülvizsgálati azonosító által visszaadott **CreateVideoReviews**.

A következő metódusdefiníciót hozzáadása a névtérhez VideoReviews, osztály Program.

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

Adja hozzá a **fő** metódusdefiníciót is névtér VideoTranscriptReviews, a Program osztályban. Végül zárja be a Program és a VideoTranscriptReviews névtér.

> [!NOTE]
> A program egy minta Beszélgetés szövegének VTT formátumban. Az Azure Media Indexer szolgáltatást használja a valós megoldás [készítése a Beszélgetés szövegének](https://docs.microsoft.com/azure/media-services/media-services-index-content) a videó. 

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
            Console.Read();
        }
    }

## <a name="run-the-program-and-review-the-output"></a>Futtassa a programot, és tekintse át a kimenetet

Az alkalmazás futtatásakor egy kimenet jelenik meg a következő sorokat:

    Creating a video review.
    Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Open your Content Moderator Dashboard and select Review > Video to see the review.
    Press any key to close the application.

## <a name="navigate-to-your-video-transcript-review"></a>Keresse meg a videót Beszélgetés szövegének áttekintése

Nyissa meg a tartalom moderátor felülvizsgálati eszközben a videó Beszélgetés szövegének tekintse át a **tekintse át**>**videó**>**Beszélgetés szövegének** képernyő.

Megjelenik a következő szolgáltatásokat:
- A Beszélgetés szövegének hozzáadott két sornyi
- Profanitás kifejezés található, és a szöveg moderálás szolgáltatás kiemelve
- Az időbélyeg a videó elindul egy írjanak elő szöveg kijelölése

![Az emberi moderátorok videó Beszélgetés szövegének áttekintése](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan készítése [ellenőrzi, hogy a videó](video-reviews-quickstart-dotnet.md) a felülvizsgálati eszközben.

Tekintse meg a részletes útmutatót a fejlesztéséhez egy [végezze el a videó moderálás megoldás](video-transcript-moderation-review-tutorial-dotnet.md).

[Töltse le a Visual Studio megoldás](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) ennél és egyéb tartalom moderátor quickstarts a .NET-hez.
