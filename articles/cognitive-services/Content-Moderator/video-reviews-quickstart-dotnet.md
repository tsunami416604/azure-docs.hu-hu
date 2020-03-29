---
title: Videoértékelések létrehozása a .NET használatával – Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: Ez a cikk információkat és kódmintákat tartalmaz, amelyek segítségével gyorsan elkezdheti a C# tartalommoderátor sdk használatával videoértékelések létrehozásához.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 7130ed43183d64b00f8f5ef1697b9a3b456ad396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72931676"
---
# <a name="create-video-reviews-using-net"></a>Videoértékelések létrehozása a .NET használatával

Ez a cikk információkat és kódmintákat tartalmaz, amelyek segítségével gyorsan elkezdheti a [C# tartalommoderátor használatát](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a következőkre:

- Videóáttekintés létrehozása emberi moderátorok számára
- Keretek hozzáadása ellenőrzéshez
- A felülvizsgálat keretei
- Az értékelés állapotának és részleteinek beszerezése
- Az ellenőrzés közzététele

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be vagy hozzon létre fiókot a [Tartalommoderátor-ellenőrzési eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyén.
- Ez a cikk feltételezi, hogy [moderálta a videót (lásd a rövid útmutatót),](video-moderation-api.md) és rendelkezik a válaszadatokkal. Szüksége van rá, hogy keretalapú véleményeket hozzon létre az emberi moderátorok számára.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Arról való gondoskodás, hogy az API-kulcs meg tudja hívni a felülvizsgálati API-t a felülvizsgálat létrehozásához

Az előző lépések végrehajtása után elképzelhető, hogy két Content Moderator-kulcsa is lesz, ha az Azure Portalról indította el a folyamatot.

Ha az Azure által biztosított API-kulcsot tervezi használni az SDK-mintában, kövesse az [Azure-kulcs felülvizsgálati API-val történő használatát](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) ismertető szakaszban szereplő lépéseket annak érdekében, hogy alkalmazása meghívhassa a felülvizsgálati API-t, és felülvizsgálatokat hozhasson létre.

Ha a felülvizsgálati eszköz által létrehozott ingyenes próbakulcsot használja, a felülvizsgálati eszközhöz tartozó fiókja már tud a kulcsról, így nincs szükség további lépésekre.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>A videó és a képkockák előkészítése véleményezésre

A véleményezésre váró videó- és mintaképeket online kell közzétenni, mert szüksége van az URL-címekre.

> [!NOTE]
> A program manuálisan mentett screenshotok a videó véletlenszerű felnőtt / pikáns pontszámok, hogy bemutassa a használatát a felülvizsgálat API. Valós helyzetben a [videó moderálási kimenettel](video-moderation-api.md#run-the-program-and-review-the-output) képeket hozhat létre, és pontszámokat rendelhet hozzá. 

A videóhoz egy streamelési végpontra van szükség, hogy a véleményező eszköz lejátssza a videót a lejátszó nézetben.

![Videobemutató miniatűrje](images/ams-video-demo-view.PNG)

- Másolja az **URL-címet** ezen az [Azure Media Services bemutató](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) oldalon a jegyzékfájl URL-címéhez.

A videoképkockákhoz (képekhez) használja a következő képeket:

![A videokeret miniatűrje 1](images/ams-video-frame-thumbnails-1.PNG) | ![A videokeret miniatűrje 2](images/ams-video-frame-thumbnails-2.PNG) | ![A videokeret miniatűrje 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[1. keret](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [2. keret](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [3. keret](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Adjon hozzá egy új **Konzolalkalmazás (.NET-keretrendszer)** projektet a megoldáshoz.

1. Nevezze el a projektet **VideoReviews**.

1. Válassza ki ezt a projektet a megoldás egyedüli kezdőprojektjeként.

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

Telepítse a következő NuGet csomagokat a TermLists projekthez.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>A program „using” utasításainak frissítése

Módosítsa a program utasításait az alábbiak szerint.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Privát tulajdonságok hozzáadása

Adja hozzá a következő saját tulajdonságokat a **VideoReviews**névtérhez , **osztályprogram**. Frissítse `AzureEndpoint` a `CMSubscriptionKey` és a mezőket a végpont URL-címének és előfizetési kulcsának értékeivel. Ezeket az azure-portálon található erőforrás **Rövid útmutató** lapján találja.


```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
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
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Tartalommoderátor-ügyfélobjektum létrehozása

Adja hozzá a következő metódusdefiníciót a **VideoReviews**névtérhez , **osztályprogram**.

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
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>Videó-ellenőrzés létrehozása

Hozzon létre egy videó felülvizsgálat **ContentModeratorClient.Reviews.CreateVideoReviews**. További információkért lásd az [API-referenciát](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**A CreateVideoReviews** a következő szükséges paraméterekkel rendelkezik:
1. Mime-típust tartalmazó karakterlánc, amelynek "application/json" típusúnak kell lennie. 
1. A tartalommoderátor csapatának neve.
1. Egy **IList\<CreateVideoReviewsBodyItem>** objektumot. Minden **CreateVideoReviewsBodyItem** objektum egy videó-áttekintést jelöl. Ez a rövid útmutató egyszerre csak egy áttekintést hoz létre.

**A CreateVideoReviewsBodyItem számos** tulajdonsággal rendelkezik. Legalább a következő tulajdonságokat kell beállítania:
- **Tartalom**. A felülvizsgálandó videó URL-címe.
- **ContentId**. A videóellenőrzéshez rendelő azonosító.
- **Állapot**. Állítsa az értéket "Közzé nem tett" értékre. Ha nem állítja be, alapértelmezés szerint "Függőben" lesz, ami azt jelenti, hogy a videó-ellenőrzés közzétételre kerül, és emberi ellenőrzésre vár. A videó-ellenőrzés közzététele után már nem adhat hozzá videoképkockákat, átiratot vagy átiratmoderálási eredményt.

> [!NOTE]
> **A CreateVideoReviews** egy\<iList karakterláncot ad vissza>. Ezek a karakterláncok mindegyike tartalmaz egy azonosítót a videó felülvizsgálathoz. Ezek az azonosítók GUID azonosítók, és nem egyeznek meg a **ContentId** tulajdonság értékével. 

Adja hozzá a következő metódusdefiníciót a VideoReviews névtérhez, osztályprogram.

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
> Az ingyenes szint kulcsai egy RPS-korláttal bírnak.

## <a name="add-video-frames-to-the-video-review"></a>Videoképkockák hozzáadása a videóellenőrzéshez

Ön hozzá video keretek egy videó felülvizsgálat **ContentModeratorClient.Reviews.AddVideoFrameUrl** (ha a videó keretek házigazdája online) vagy **ContentModeratorClient.Reviews.AddVideoFrameStream** (ha a videó keretek házigazdája helyben). Ez a rövid útmutató feltételezi, hogy a videoképkockák online vannak tárolva, ezért **az AddVideoFrameUrl -t**használja. További információkért lásd az [API-referenciát](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**Az AddVideoFrameUrl** a következő szükséges paraméterekkel rendelkezik:
1. Mime-típust tartalmazó karakterlánc, amelynek "application/json" típusúnak kell lennie.
1. A tartalommoderátor csapatának neve.
1. A **CreateVideoReviews**által visszaadott videó-ellenőrző azonosító.
1. Az **IList\<VideoFrameBodyItem>** objektumot. Minden **VideoFrameBodyItem** objektum egy videokeretet jelöl.

**A VideoFrameBodyItem** a következő tulajdonságokkal rendelkezik:
- **Időbélyeg**. Olyan karakterlánc, amely másodpercben tartalmazza azt az időt a videóban, amelyből a videoképkocka készült.
- **FrameImage**. A videoképkocka URL-címe.
- **Metaadatok**. Egy IList\<VideoFrameBodyItemMetadataItem>. **A VideoFrameBodyItemMetadataItem** egyszerűen kulcs/érték pár. Az érvényes kulcsok a következők:
- **felülvizsgálatAjánlott**. Igaz, ha a videókép emberi felülvizsgálata ajánlott.
- **adultScore .** 0 és 1 között, amely a videóképben lévő felnőtt tartalom súlyosságát értékeli.
- **a .** Igaz, ha a videó felnőtt tartalmat tartalmaz.
- **racyScore .** 0 és 1 között, amely a videóképben lévő pikáns tartalom súlyosságát értékeli.
- **r.**. Igaz, ha a videokeret pikáns tartalmat tartalmaz.
- **ReviewerResultCímkék**. Egy IList\<VideoFrameBodyItemReviewerResultTagsItem>. **A VideoFrameBodyItemReviewerResultTagsItem** egyszerűen kulcs/érték pár. Egy alkalmazás használhatja ezeket a címkéket a videokeretek rendszerezésére.

> [!NOTE]
> Ez a rövid útmutató véletlenszerű értékeket hoz létre a **adultScore** és **a racyScore** tulajdonságokhoz. Egy éles alkalmazás, ezeket az értékeket a [videó moderálási szolgáltatás,](video-moderation-api.md)az Azure Media Service üzembe helyezése.

Adja hozzá a következő metódusdefiníciókat a VideoReviews névtérhez, osztályprogram.

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

## <a name="get-video-frames-for-video-review"></a>Videóképkockák beszereznie videóellenőrzéshez

A **ContentModeratorClient.Reviews.GetVideoFrames**. **A GetVideoFrames** a következő szükséges paraméterekkel rendelkezik:
1. A tartalommoderátor csapatának neve.
1. A **CreateVideoReviews**által visszaadott videó-ellenőrző azonosító.
1. Az első beszerezhető videoképkocka nulla alapú indexe.
1. A beszerezhető videoképkockák száma.

Adja hozzá a következő metódusdefiníciót a VideoReviews névtérhez, osztályprogram.

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

## <a name="get-video-review-information"></a>Videó-ellenőrzési információk beszerezni

Kapsz információt a videó felülvizsgálat **ContentModeratorClient.Reviews.GetReview**. **A GetReview** a következő szükséges paraméterekkel rendelkezik:
1. A tartalommoderátor csapatának neve.
1. A **CreateVideoReviews**által visszaadott videó-ellenőrző azonosító.

Adja hozzá a következő metódusdefiníciót a VideoReviews névtérhez, osztályprogram.

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

## <a name="publish-video-review"></a>Videó-ellenőrzés közzététele

Ön közzé egy videó felülvizsgálat **ContentModeratorClient.Reviews.PublishVideoReview**. **A PublishVideoReview** a következő szükséges paraméterekkel rendelkezik:
1. A tartalommoderátor csapatának neve.
1. A **CreateVideoReviews**által visszaadott videó-ellenőrző azonosító.

Adja hozzá a következő metódusdefiníciót a VideoReviews névtérhez, osztályprogram.

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

Adja hozzá a **Fő** metódus definícióját a VideoReviews névtérhez, osztályprogram. Végül zárja be a Program osztályt és a VideoReviews névteret.

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
Az alkalmazás futtatásakor a következő sorokban jelenik meg egy kimenet:

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

## <a name="check-out-your-video-review"></a>Nézze meg a videó felülvizsgálat

Végül a Videó **áttekintése**>**képernyőn** láthatja a videóértékelést a Tartalommoderátor-ellenőrző eszközfiókjában.

![Videó felülvizsgálata az emberi moderátorok](images/ams-video-review.PNG)

## <a name="next-steps"></a>További lépések

A [tartalommoderátor .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) és a [Visual Studio megoldás](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) a .NET tartalommoderátor rövid útmutatójának beszerezése.

További információ arról, hogyan adhatsz [átiratmoderálást](video-transcript-moderation-review-tutorial-dotnet.md) a videóellenőrzéshez. 

Nézze meg a részletes bemutató, hogyan kell fejleszteni a [teljes videó moderálás megoldás](video-transcript-moderation-review-tutorial-dotnet.md).
