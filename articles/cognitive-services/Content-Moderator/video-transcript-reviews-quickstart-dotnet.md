---
title: Videoátirat-vélemények létrehozása a .NET használatával – Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre videóátirat-értékeléseket az Azure Cognitive Services tartalommoderátorának SDK-val.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: b2d763454b86570b57a16fb9ae2107a2a2bcd23d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73744390"
---
# <a name="create-video-transcript-reviews-using-net"></a>Videoátirat-vélemények létrehozása a .NET használatával

Ez a cikk információkat és kódmintákat tartalmaz, amelyek segítségével gyorsan elkezdheti a [C# tartalommoderátor használatát](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a következőkre:

- Videóáttekintés létrehozása emberi moderátorok számára
- Moderált átirat hozzáadása az értékeléshez
- Az ellenőrzés közzététele

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be, vagy hozzon létre fiókot a [Tartalommoderátor-ellenőrzési eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyén, ha még nem tette meg.
- Ez a cikk feltételezi, hogy [moderálta a videót,](video-moderation-api.md) és [létrehozta a videó áttekintést](video-reviews-quickstart-dotnet.md) az emberi döntéshozatal felülvizsgálati eszközében. Most moderált videóátiratokat szeretne hozzáadni a felülvizsgálati eszközhöz.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Győződjön meg arról, hogy az API-kulcs meg tudja hívni a felülvizsgálati API-t (Munkahelyteremtés)

Az előző lépések végrehajtása után elképzelhető, hogy két Content Moderator-kulcsa is lesz, ha az Azure Portalról indította el a folyamatot.

Ha az Azure által biztosított API-kulcsot tervezi használni az SDK-mintában, kövesse az [Azure-kulcs felülvizsgálati API-val történő használatát](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) ismertető szakaszban szereplő lépéseket annak érdekében, hogy alkalmazása meghívhassa a felülvizsgálati API-t, és felülvizsgálatokat hozhasson létre.

Ha a felülvizsgálati eszköz által létrehozott ingyenes próbakulcsot használja, a felülvizsgálati eszközhöz tartozó fiókja már tud a kulcsról, így nincs szükség további lépésekre.

## <a name="prepare-your-video-for-review"></a>A videó előkészítése felülvizsgálatra

Adja hozzá az átiratot egy videóellenőrzéshez. A videót közzé kell tenni az interneten. Szüksége van a streamelési végpontra. A streamelési végpont lehetővé teszi, hogy a véleményező eszköz videolejátszója lejátssza a videót.

![Videobemutató miniatűrje](images/ams-video-demo-view.PNG)

- Másolja az **URL-címet** ezen az [Azure Media Services bemutató](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) oldalon a jegyzékfájl URL-címéhez.

## <a name="create-your-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Adjon hozzá egy új **Konzolalkalmazás (.NET-keretrendszer)** projektet a megoldáshoz.

1. Nevezze el a projektet **VideoTranscriptReviews**.

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

Adja hozzá a következő saját tulajdonságokat a **VideoTranscriptReviews**névtérhez , **osztályprogram**. Frissítse `AzureEndpoint` a `CMSubscriptionKey` és a mezőket a végpont URL-címének és előfizetési kulcsának értékeivel. Ezeket az azure-portálon található erőforrás **Rövid útmutató** lapján találja.

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

Adja hozzá a következő metódusdefiníciót a VideoTranscriptReviews névtérhez, osztályprogram.

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
> A Content Moderator szolgáltatáskulcs egy másodpercenkénti kérelmekre (RPS-re) vonatkozó korláttal rendelkezik. Ha túllépi ezt a korlátot, az SDK 429-es hibakódú kivételt jelez.
>
> Az ingyenes szint kulcsai egy RPS-korláttal bírnak.

## <a name="add-transcript-to-video-review"></a>Átirat hozzáadása a videóellenőrzéshez

Hozzáad egy átiratot egy videó felülvizsgálat **ContentModeratorClient.Reviews.AddVideoTranscript**. **Az AddVideoTranscript** a következő szükséges paraméterekkel rendelkezik:
1. A tartalommoderátor csapatának azonosítója.
1. A **CreateVideoReviews**által visszaadott videó-ellenőrző azonosító.
1. Az átiratot tartalmazó **adatfolyam-objektum.**

Az átiratnak WebVTT formátumúnak kell lennie. További információt a [WebVTT: The Web Video Text Tracks Format című témakörben talál.](https://www.w3.org/TR/webvtt1/)

> [!NOTE]
> A program egy minta átirata a VTT formátumban. Egy valós megoldás, az Azure Media Indexer szolgáltatás használatával [egy videó átiratát.](https://docs.microsoft.com/azure/media-services/media-services-index-content)

Adja hozzá a következő metódusdefiníciót a VideotranscriptReviews névtérhez, osztályprogram.

```csharp
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
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Átiratmoderálási eredmény hozzáadása a videóellenőrzéshez

Az átirat hozzáadása a videóellenőrzéshez mellett az átirat moderálásának eredményét is hozzáadja. Ezt a **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult .** További információkért lásd az [API-referenciát](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**Az AddVideoTranscriptModerationResult** a következő szükséges paraméterekkel rendelkezik:
1. Mime-típust tartalmazó karakterlánc, amelynek "application/json" típusúnak kell lennie. 
1. A tartalommoderátor csapatának neve.
1. A **CreateVideoReviews**által visszaadott videó-ellenőrző azonosító.
1. Egy IList\<TranscriptModerationBodyItem>. A **TranscriptModerationBodyItem** a következő tulajdonságokkal rendelkezik:
1. **Feltételek .** Egy IList\<TranscriptModerationBodyItemTermsItem>. A **TranscriptModerationBodyItemTermsItem** a következő tulajdonságokkal rendelkezik:
1. **Index**. A kifejezés nulla alapú indexe.
1. **Kifejezés**. A kifejezést tartalmazó karakterlánc.
1. **Időbélyeg**. Olyan karakterlánc, amely másodpercben tartalmazza azt az időt, amelyen a feltételek találhatók.

Az átiratnak WebVTT formátumúnak kell lennie. További információt a [WebVTT: The Web Video Text Tracks Format című témakörben talál.](https://www.w3.org/TR/webvtt1/)

Adja hozzá a következő metódusdefiníciót a VideoTranscriptReviews névtérhez, osztályprogram. Ez a módszer átiratot küld a **ContentModeratorClient.TextModeration.ScreenText** metódusnak. Azt is lefordítja az\<eredményt egy IList TranscriptModerationBodyItem>, és elküldi **addVideoTranscriptModerationResult**.

```csharp
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

Adja hozzá a **Fő** metódus definícióját a VideoTranscriptReviews névtérhez, osztályprogram. Végül zárja be a Program osztályt és a VideoTranscriptReviews névteret.

> [!NOTE]
> A program egy minta átirata a VTT formátumban. Egy valós megoldás, az Azure Media Indexer szolgáltatás használatával [egy videó átiratát.](https://docs.microsoft.com/azure/media-services/media-services-index-content)

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

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
```

## <a name="run-the-program-and-review-the-output"></a>A program futtatása és a kimenet áttekintése

Az alkalmazás futtatásakor a következő sorokban jelenik meg egy kimenet:

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Keresse meg a videóátirat-áttekintést

Nyissa meg a videóátirat-áttekintést a Tartalommoderátor felülvizsgálati eszközében a>**Videóátirat**>**Transcript** **áttekintése**képernyőn.

A következő funkciók jelennek meg:
- A két sor átirat a hozzáadott
- A szövegmoderálási szolgáltatás által talált és kiemelt káromkodási kifejezés
- Az átírási szöveg kiválasztása elindítja a videót abból az időbélyegből

![Videóátirat felülvizsgálata az emberi moderátorok](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>További lépések

A [tartalommoderátor .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) és a [Visual Studio megoldás](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) a .NET tartalommoderátor rövid útmutatójának beszerezése.

További információ [avideó-értékelések](video-reviews-quickstart-dotnet.md) létrehozásáról az ellenőrző eszközben.

Nézze meg a részletes bemutató, hogyan kell fejleszteni a [teljes videó moderálás megoldás](video-transcript-moderation-review-tutorial-dotnet.md).
