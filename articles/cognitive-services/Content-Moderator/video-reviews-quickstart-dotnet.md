---
title: .NET - Content Moderator használatával videót felülvizsgálatok létrehozása
titlesuffix: Azure Cognitive Services
description: Ez a cikk nyújt információt, és kódminták segítségével gyorsan a Content Moderator SDK-val használatának első lépései C# videót felülvizsgálatok létrehozásához.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/19/2019
ms.author: sajagtap
ms.openlocfilehash: e4dd7299907168bb50ac8ebdf90b381c0bac01f2
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527370"
---
# <a name="create-video-reviews-using-net"></a>Hozzon létre videót felülvizsgálatok .NET használatával

Ez a cikk nyújt információt, és kódminták segítségével gyorsan használatának első lépései a [Content Moderator SDK a C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) való:

- Létrehoz egy videó az emberi moderátorok
- Felülvizsgálat keretek hozzáadása
- A keretek a felülvizsgálat lekérése
- Az állapot és a részletek felülvizsgálata
- A felülvizsgálat közzététele

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be, vagy hozzon létre egy fiókot a Content Moderator [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/) hely.
- Ez a cikk feltételezi, hogy [Metz a videó (lásd a rövid útmutató)](video-moderation-api.md) és a válasz az adatokat. A keret-alapú ellenőrzések hoz létre az emberi moderátorok van szükség.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Arról való gondoskodás, hogy az API-kulcs meg tudja hívni a felülvizsgálati API-t a felülvizsgálat létrehozásához

Az előző lépések végrehajtása után elképzelhető, hogy két Content Moderator-kulcsa is lesz, ha az Azure Portalról indította el a folyamatot.

Ha az Azure által biztosított API-kulcsot tervezi használni az SDK-mintában, kövesse az [Azure-kulcs felülvizsgálati API-val történő használatát](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) ismertető szakaszban szereplő lépéseket annak érdekében, hogy alkalmazása meghívhassa a felülvizsgálati API-t, és felülvizsgálatokat hozhasson létre.

Ha a felülvizsgálati eszköz által létrehozott ingyenes próbakulcsot használja, a felülvizsgálati eszközhöz tartozó fiókja már tud a kulcsról, így nincs szükség további lépésekre.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Tekintse át a videót, és a videókban előkészítése

A videó és a mintaadatokat videókban is, tekintse át online tehetők közzé, mert szüksége lesz az URL-címeket.

> [!NOTE]
> A program a videó manuálisan mentett képernyőképek a felnőtt/pikáns pontszámok véletlenszerű mutatja be a felülvizsgálati API használatát. Egy való életből vett helyzet fogja használni a [videomoderálás kimeneti](video-moderation-api.md#run-the-program-and-review-the-output) lemezképek létrehozása és hozzárendelése a pontszámokat. 

A videó kell egy streamvégponton, hogy a vizsgálóeszköz a videó játszik a lejátszó nézetet.

![Bemutató videó miniatűrje](images/ams-video-demo-view.PNG)

- Másolás a **URL-cím** ezen [bemutató az Azure Media Services](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) lapját a jegyzékfájl URL-CÍMÉT.

A videókban (képek) használja az alábbi lemezképek:

![1. képkocka miniatűrje](images/ams-video-frame-thumbnails-1.PNG) | ![2. keret videó miniatűrje](images/ams-video-frame-thumbnails-2.PNG) | ![3. keret videó miniatűrje](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[1. képkocka](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [2. keret](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [3. keret](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Adjon hozzá egy új **Konzolalkalmazás (.NET-keretrendszer)** projektet a megoldáshoz.

1. Adja a projektnek **VideoReviews**.

1. Válassza ki ezt a projektet a megoldás egyedüli kezdőprojektjeként.

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

A következő, a TermLists projekt NuGet-csomagok telepítése.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>A program „using” utasításainak frissítése

Módosítsa a program a következő using utasításokat.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Privát tulajdonságok hozzáadása

Adja hozzá a következő privát tulajdonságok névtérhez VideoReviews, osztály Program.

A felsoroltak, cserélje le a példában szereplő értékeket ezekhez a tulajdonságokhoz.

```csharp
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
```

### <a name="create-content-moderator-client-object"></a>Content Moderator ügyfélobjektum létrehozása

Adja hozzá a következő definice metody névtér VideoReviews, osztály Program.

```csharp
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
```

## <a name="create-a-video-review"></a>Létrehoz egy videó

Létrehoz egy videó- **ContentModeratorClient.Reviews.CreateVideoReviews**. További információkért lásd az [API-referenciát](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** a következő szükséges paraméterek:
1. Egy karakterlánc, amely tartalmazza a MIME-típust, amely elvileg "application/json." 
1. A Content Moderator csoport neve.
1. Egy **IList\<CreateVideoReviewsBodyItem >** objektum. Minden egyes **CreateVideoReviewsBodyItem** objektum videót felülvizsgálatok jelöli. Ebben a rövid útmutatóban létrehoz egy felülvizsgálati egyszerre.

**CreateVideoReviewsBodyItem** néhány olyan tulajdonság tartozik. Minimális állítsa be a következő tulajdonságokat:
- **Tartalom**. A Videó URL-címét a programtulajdonos.
- **ContentId**. A videó felülvizsgálat rendel azonosító.
- **Állapot**. Állítsa be az értéket "Unpublished." Ha nincs beállítva, a rendszer alapértelmezés szerint, "Függő", ami azt jelenti, hogy a videó felülvizsgálat közzé van téve, és emberi folyamatban. A videó felülvizsgálat közzététele után már nem adhat videókban, a szöveges vagy szöveges moderálás eredményt azt.

> [!NOTE]
> **CreateVideoReviews** adja vissza az IList<string>. Ezek a karakterláncok mindegyike tartalmaz egy videót felülvizsgálatok Azonosítót. Azonosítóit a részletekben GUID és nem ugyanaz, mint az értékét a **ContentId** tulajdonság. 

Adja hozzá a következő definice metody névtér VideoReviews, osztály Program.

```csharp
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
```

> [!NOTE]
> A Content Moderator-szolgáltatáskulcs rendelkezik egy RPS-alapú (kérések másodpercenkénti száma) sebességkorláttal, amelyet ha túllép, az SDK egy 429-es hibakódú kivételt jelez.
>
> Az ingyenes szint kulcsának a sebességkorlátja egy RPS.

## <a name="add-video-frames-to-the-video-review"></a>A videó felülvizsgálat videókban hozzáadása

Hozzáadhat egy videó tekintse át a videókban **ContentModeratorClient.Reviews.AddVideoFrameUrl** (Ha a videókban online) vagy **ContentModeratorClient.Reviews.AddVideoFrameStream** () Ha a videókban helyileg). Ez a rövid útmutató feltételezi, hogy a videókban online üzemelnek, és ezért használja **AddVideoFrameUrl**. További információkért lásd az [API-referenciát](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** a következő szükséges paraméterek:
1. Egy karakterlánc, amely tartalmazza a MIME-típust, amely elvileg "application/json."
1. A Content Moderator csoport neve.
1. A videó felülvizsgálat azonosítója által visszaadott **CreateVideoReviews**.
1. Egy **IList\<VideoFrameBodyItem >** objektum. Minden egyes **VideoFrameBodyItem** objektum képviseli egy videó keretet.

**VideoFrameBodyItem** tulajdonságai a következők:
- **Időbélyeg**. Egy karakterlánc, amely tartalmaz, a videót, amelyből a képkocka kerül az idő másodpercben.
- **FrameImage**. A videó keret URL-címe
- **Metaadatok**. IList\<VideoFrameBodyItemMetadataItem >. **VideoFrameBodyItemMetadataItem** egyszerűen egy kulcs/érték pár. Érvényes kulcsok a következők:
- **reviewRecommended**. IGAZ, ha a videó keret elbírálni használata javasolt.
- **adultScore**. A 0 érték 1, felnőtt tartalom súlyosságát értékeli a videó keretbe.
- **a**. IGAZ, ha a videó felnőtt tartalom található.
- **racyScore**. A 0 érték 1, amely a képkocka fajgyűlölő tartalom súlyosságát aránya.
- **r**. IGAZ, ha a képkocka fajgyűlölő tartalom található.
- **ReviewerResultTags**. An IList\<VideoFrameBodyItemReviewerResultTagsItem>. **VideoFrameBodyItemReviewerResultTagsItem** egyszerűen egy kulcs/érték pár. Egy alkalmazás használhatja a címkéket videókban rendszerezéséhez.

> [!NOTE]
> Ebben a rövid útmutatóban a véletlenszerű értéket állít elő a **adultScore** és **racyScore** tulajdonságait. Egy éles alkalmazásban, akkor szerezze be ezeket az értékeket a a [videomoderálás szolgáltatás](video-moderation-api.md)üzembe helyezett, mint az Azure Media Services.

Adja hozzá a következő metódus definíciókat névtér VideoReviews, osztály Program.

```csharp
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
```

```csharp
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
```

## <a name="get-video-frames-for-video-review"></a>Videó felülvizsgálatra videókban beolvasása

A videó értékelést is igénybe a videókban **ContentModeratorClient.Reviews.GetVideoFrames**. **GetVideoFrames** a következő szükséges paraméterek:
1. A Content Moderator csoport neve.
1. A videó felülvizsgálat azonosítója által visszaadott **CreateVideoReviews**.
1. Az első videó keret beolvasni a nulla alapú indexét.
1. Videókban is első száma.

Adja hozzá a következő definice metody névtér VideoReviews, osztály Program.

```csharp
/// <summary>
/// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
static void GetFrames(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

    Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="get-video-review-information"></a>Első információk Videós áttekintése

Egy videó tekintse át az információhoz **ContentModeratorClient.Reviews.GetReview**. **GetReview** a következő szükséges paraméterek:
1. A Content Moderator csoport neve.
1. A videó felülvizsgálat azonosítója által visszaadott **CreateVideoReviews**.

Adja hozzá a következő definice metody névtér VideoReviews, osztály Program.

```csharp
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
```

## <a name="publish-video-review"></a>Közzététel a videós áttekintése

A videó értékelést tesz közzé **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** a következő szükséges paraméterek:
1. A Content Moderator csoport neve.
1. A videó felülvizsgálat azonosítója által visszaadott **CreateVideoReviews**.

Adja hozzá a következő definice metody névtér VideoReviews, osztály Program.

```csharp
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
```

## <a name="putting-it-all-together"></a>Végső összeállítás

Adja hozzá a **fő** metódus VideoReviews, névtér-definíciót a Program osztályhoz. Végül zárja be a Program osztályt, valamint a VideoReviews névteret.

```csharp
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
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>A program futtatása és a kimenet áttekintése
Az alkalmazás futtatásakor a látható kimenet a következő sorokat:

```json
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
```

## <a name="check-out-your-video-review"></a>Tekintse meg a videós áttekintése

Végül tekintse meg a videó felülvizsgálatot a Content Moderator a tekintse át az eszköz fiók az a **áttekintése**>**videó** képernyő.

![Az emberi moderátorok Videós áttekintése](images/ams-video-review.PNG)

## <a name="next-steps"></a>További lépések

Első a [Content Moderator .NET SDK-val](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) és a [Visual Studio-megoldás](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) ennél és a többi a Content Moderator rövid útmutató a .NET-hez.

Ismerje meg, hogyan adhat hozzá [átiratok moderálás](video-transcript-moderation-review-tutorial-dotnet.md) a videó felülvizsgálatra. 

Tekintse meg a részletes oktatóanyag, hogyan hozhat létre egy [videomoderálás megoldás befejezéséhez](video-transcript-moderation-review-tutorial-dotnet.md).
