---
title: Az Azure Content moderátor - .NET használatával videó értékelést létrehozása |} Microsoft Docs
description: Videó létrehozása ellenőrzi, hogy Azure tartalom moderátor SDK for .NET használatával
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/18/2018
ms.author: sajagtap
ms.openlocfilehash: cb487314b8695f3676fdb22a9d7e3ec5ca3ed9f2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347202"
---
# <a name="create-video-reviews-using-net"></a>Hozzon létre videó értékelést .NET használatával

Ez a cikk és a segítségével gyorsan Kódminták C# való a tartalom moderátor SDK használatának megkezdése:

- Hozzon létre egy videó tekintse át az emberi moderátorok
- Keretek hozzáadása áttekintése
- A képkockák lekérése a áttekintése 
- Az állapot és a részletek felülvizsgálata
- A felülvizsgálati közzététele

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik [a videó moderált (lásd a gyors üzembe helyezés)](video-moderation-api.md) és válasz az adatokat. A keret-alapú ellenőrzések létrehozásához az emberi moderátorok van szükség.

Ez a cikk is feltételezi, hogy Ön már ismeri a Visual Studio és a C#.

### <a name="sign-up-for-content-moderator-services"></a>Iratkozzon fel a tartalom moderátor szolgáltatások

Tartalom moderátor-szolgáltatások díjairól a REST API-t vagy az SDK használata előtt be kell egy előfizetési kulcsot.

A tartalom moderátor irányítópulton található az Előfizetés kulcs **beállítások** > **hitelesítő adatok** > **API**  >  **Próba Ocp-Apim-előfizetés-kulcs**. További információkért lásd: [áttekintése](overview.md).

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Tekintse át a videó és a videó keretek előkészítése

Tekintse át a video- és minta videó keretek online tehető közzé, mert az URL-címek van szüksége.

> [!NOTE]
> A program a videóból manuálisan mentett képernyőképek véletlenszerű felnőtt ellopható pontszámok a felülvizsgálati API használatát mutatja be. A valós esetben használja a [videó moderálás kimeneti](video-moderation-api.md#run-the-program-and-review-the-output) képek létrehozására és hozzárendelésére pontszámait. 

A videó van szüksége a streamvégpontján, hogy a felülvizsgálati eszköz a videó játszik a player nézetet.

![Bemutató videó miniatűr](images/ams-video-demo-view.PNG)

- Másolás a **URL-cím** ezen [Azure Media Services bemutató](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) lap a jegyzék URL-címhez.

A videó keretek (lemezképek) használja a következő lemezképek:

![Videó keret miniatűr 1](images/ams-video-frame-thumbnails-1.PNG) | ![Videó keret miniatűr 2](images/ams-video-frame-thumbnails-2.PNG) | ![Videó keret miniatűr 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Keret 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Keret 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Keret 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. Adjon hozzá egy új **Konzolalkalmazás (.NET-keretrendszer)** -projektet a megoldásban.

1. Nevet a projektnek **VideoReviews**.

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

A következő személyes tulajdonságok hozzáadása a névtérhez VideoReviews, osztály Program.

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

A következő metódusdefiníciót hozzáadása a névtérhez VideoReviews, osztály Program.

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
> A tartalom moderátor szolgáltatási kulcs egy kérelmek / második (RPS) sávszélesség-korlátjának rendelkezik, és ha meghaladja a korlátot, az SDK 429-es jelű hibakód kivételt jelez. 
>
> Ingyenes szint kulcs egy RPS sávszélesség-korlátjának rendelkezik.

## <a name="add-video-frames-to-the-video-review"></a>Videó keretek hozzáadása a videó áttekintése

Videó keretek ad hozzá a videó áttekintése **ContentModeratorClient.Reviews.AddVideoFrameUrl** (Ha a videó keretek online üzemeltetett) vagy **ContentModeratorClient.Reviews.AddVideoFrameStream** () Ha a videó keretek üzemeltetett helyileg). A gyors üzembe helyezés azt feltételezi, hogy a videó keretek online üzemeltetett, és így használja a **AddVideoFrameUrl**. További információkért lásd: a [API-referencia](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** rendelkezik a következő kötelező paraméterek:
1. Karakterlánc, amely tartalmazza a MIME-típust, amely kell lennie az "application/json."
1. A tartalom moderátor csoport neve.
1. A videó felülvizsgálati azonosító által visszaadott **CreateVideoReviews**.
1. Egy **IList<VideoFrameBodyItem>**  objektum. Minden egyes **VideoFrameBodyItem** objektum által jelképezett videó keret.

**VideoFrameBodyItem** tulajdonságai a következők:
- **Timestamp típusú**. Egy karakterlánc, amely tartalmazza a videót, amelyből a képkockák kerül az idő másodpercben.
- **FrameImage**. A képkockák URL-CÍMÉT.
- **Metaadatok**. IList<VideoFrameBodyItemMetadataItem>. **VideoFrameBodyItemMetadataItem** egyszerűen kulcs-érték párból áll. Érvényes kulcsok a következők:
- **reviewRecommended**. Értéke TRUE, ha a képkockák emberi áttekintése ajánlott.
- **adultScore**. Egy értéket 0 és 1, felnőtteknek szóló tartalmak súlyossága minősíti a video-keretében.
- **egy**. Értéke TRUE, ha a videó felnőtt tartalom szerepel.
- **racyScore**. Egy értéket 0 és 1, amely ellopható tartalom súlyossága minősíti a video-keretében.
- **r**. Értéke TRUE, ha a képkockák ellopható tartalom szerepel.
- **ReviewerResultTags**. IList<VideoFrameBodyItemReviewerResultTagsItem>. **VideoFrameBodyItemReviewerResultTagsItem** egyszerűen kulcs-érték párból áll. Egy alkalmazás ezekkel a címkékkel segítségével videó keretek rendezheti.

> [!NOTE]
> A gyors üzembe helyezés véletlenszerű értékek hoz létre a **adultScore** és **racyScore** tulajdonságait. Egy éles alkalmazásban volna ezeket az értékeket az beszerzése a [videó moderálás szolgáltatás](video-moderation-api.md), az Azure Media szolgáltatásként telepített.

Adja hozzá a következő metódus definíciók névtér VideoReviews, osztály Program.

    <summary>
    /// Create a video frame to add to a video review after the video review is created.
    /// </summary>
    /// <param name="url">The URL of the video frame image.</param>
    /// <returns>The video frame.</returns>
    private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
    {
        // We generate random "adult" and "racy" scores for the video frame.
        Random rand = new Random();

        var frame = new VideoFrameBodyItem
        {
            // The timestamp is measured in milliseconds. Convert from seconds.
            Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
            FrameImage = url,

            Metadata = new List<VideoFrameBodyItemMetadataItem>
            {
                new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
                new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
                new VideoFrameBodyItemMetadataItem("a", "false"),
                new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
                new VideoFrameBodyItemMetadataItem("r", "false")
            },

            ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
            {
                new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
            }
        };

        return frame;
    }

    /// <summary>
    /// Add a video frame to the indicated video review. For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="url">The URL of the video frame image.</param>
    static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
    {
        Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

        var frames = new List<VideoFrameBodyItem>()
        {
            CreateFrameToAddToReview(url, timestamp_seconds)
        };
            
        client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

        Thread.Sleep(throttleRate);
    

## <a name="get-video-frames-for-video-review"></a>Videó keretek lekérése videó áttekintése

A videó keretek kaphat a videó áttekintése **ContentModeratorClient.Reviews.GetVideoFrames**. **GetVideoFrames** rendelkezik a következő kötelező paraméterek:
1. A tartalom moderátor csoport neve.
1. A videó felülvizsgálati azonosító által visszaadott **CreateVideoReviews**.
1. Az első képkockát beolvasása nulla alapú indexét.
1. A beolvasandó videó keretek száma.

A következő metódusdefiníciót hozzáadása a névtérhez VideoReviews, osztály Program.

    /// <summary>
    /// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    static void GetFrames(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

        Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0, Int32.MaxValue);
        Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

        Thread.Sleep(throttleRate);
    }

## <a name="get-video-review-information"></a>Videó felülvizsgálati információ

A videó véleményezésre információkat elérhetővé **ContentModeratorClient.Reviews.GetReview**. **GetReview** rendelkezik a következő kötelező paraméterek:
1. A tartalom moderátor csoport neve.
1. A videó felülvizsgálati azonosító által visszaadott **CreateVideoReviews**.

A következő metódusdefiníciót hozzáadása a névtérhez VideoReviews, osztály Program.

    /// <summary>
    /// Get the information for the indicated video review. For more information, see the reference API:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    private static void GetReview(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

        var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
        Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

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

Adja hozzá a **fő** metódusdefiníciót is névtér VideoReviews, a Program osztályban. Végül zárja be a Program és a VideoReviews névtér.

    static void Main(string[] args)
    {
        using (ContentModeratorClient client = NewClient())
        {
            // Create a review with the content pointing to a streaming endpoint (manifest)
            var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
            string review_id = CreateReview(client, "review1", streamingcontent);

            var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
            var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
            var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

            // Add the frames from 17, 64, and 144 seconds.
            AddFrame(client, review_id, frame1_url, "17");
            AddFrame(client, review_id, frame2_url, "64");
            AddFrame(client, review_id, frame3_url, "144");

            // Get frames information and show
            GetFrames(client, review_id);
            GetReview(client, review_id);

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
    Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    {
        "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
        "VideoFrames": [
        {
            "Timestamp": "17000",
            "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
            "Metadata": [
            {
                "Key": "reviewRecommended",
                "Value": "true"
            },
            {
                "Key": "adultScore",
                "Value": "0.808312381528463"
            },
            {
                "Key": "a",
                "Value": "false"
            },
            {
                "Key": "racyScore",
                "Value": "0.846378884206702"
            },
            {
                "Key": "r",
                "Value": "false"
            }
            ],
            "ReviewerResultTags": [
            {
                "Key": "tag1",
                "Value": "value1"
            }
        ]
        },
        {
            "Timestamp": "64000",
            "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
            "Metadata": [
            {
                "Key": "reviewRecommended",
                "Value": "true"
            },
            {
                "Key": "adultScore",
                "Value": "0.576078300166912"
            },
            {
                "Key": "a",
                "Value": "false"
            },
            {
                "Key": "racyScore",
                "Value": "0.244768953064815"
            },
            {
                "Key": "r",
                "Value": "false"
            }
            ],
            "ReviewerResultTags": [
            {
                "Key": "tag1",
                "Value": "value1"
            }
        ]
        },
        {
            "Timestamp": "144000",
            "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
            "Metadata": [
            {
                "Key": "reviewRecommended",
                "Value": "true"
            },
            {
                "Key": "adultScore",
                "Value": "0.664480847150311"
            },
            {
                "Key": "a",
                "Value": "false"
            },
            {
                "Key": "racyScore",
                "Value": "0.933817870418456"
            },
            {
                "Key": "r",
                "Value": "false"
            }
            ],
            "ReviewerResultTags": [
            {
                "Key": "tag1",
                "Value": "value1"
            }
            ]
        }
        ]
    }
    
    Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    {
        "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
        "SubTeam": "public",
        "Status": "UnPublished",
        "ReviewerResultTags": [],
        "CreatedBy": "testreview6",
        "Metadata": [
        {
            "Key": "FrameCount",
            "Value": "3"
        }
        ],
        "Type": "Video",
        "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        "ContentId": "review1",
        "CallbackEndpoint": null
    }

    Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Open your Content Moderator Dashboard and select Review > Video to see the review.
    Press any key to close the application.

## <a name="check-out-your-video-review"></a>Tekintse meg a videót áttekintése

Végül megjelenik a videó tekintse át a tartalom moderátor tekintse át az eszköz fiók a a **tekintse át**>**videó** képernyő.

![Az emberi moderátorok videó áttekintése](images/ams-video-review.PNG)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan adhat [Beszélgetés szövegének moderálás](video-transcript-moderation-review-tutorial-dotnet.md) az videó ellenőrzéshez. 

Tekintse meg a részletes útmutatót a fejlesztéséhez egy [végezze el a videó moderálás megoldás](video-transcript-moderation-review-tutorial-dotnet.md).

[Töltse le a Visual Studio megoldás](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) ennél és egyéb tartalom moderátor quickstarts a .NET-hez.
