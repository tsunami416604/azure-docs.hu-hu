---
title: Az Azure Content Moderator – létrehozása .NET használatával felülvizsgálatok |} A Microsoft Docs
description: Hogyan lehet létrehozni a felülvizsgálatok az Azure Content Moderator SDK használatával a .NET-hez
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/10/2018
ms.author: sajagtap
ms.openlocfilehash: 32e18273ad92f6b415b2a0219fd8b0520fe707f3
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716145"
---
# <a name="create-reviews-using-net"></a>Hozzon létre értékelések .NET használatával

Ez a cikk nyújt információt, és kódminták segítségével történő használatának első lépései a [Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) való:
 
- Az emberi moderátorok felülvizsgálatok készletének létrehozása
- Az emberi moderátorok meglévő felülvizsgálatok állapotának lekérése

Általában a tartalom halad végig az emberi ellenőrző ütemezett előtt bizonyos automatizált moderálás előnyeit. Ez a cikk csak az emberi moderálás vonatkozó felülvizsgálat létrehozása ismerteti. Egy teljes, olvassa az [Facebook tartalom-jóváhagyás](facebook-post-moderation.md) és [e-kereskedelmi katalógusok moderálása](ecommerce-retail-catalog-moderation.md) oktatóanyagok.

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

            client.BaseUrl = AzureBaseURL;
            return client;
        }
    }

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>Hozzon létre egy osztályt belső tartalom adatai társítása egy felülvizsgálat azonosítója

Adja hozzá a következő osztály a **Program** osztály.
Ez az osztály használatával társítja a belső tartalom ID, az elem a felülvizsgálat azonosítója.

    /// <summary>
    /// Associates the review ID (assigned by the service) to the internal
    /// content ID of the item.
    /// </summary>
    public class ReviewItem
    {
        /// <summary>
        /// The media type for the item to review.
        /// </summary>
        public string Type;

        /// <summary>
        /// The URL of the item to review.
        /// </summary>
        public string Url;

        /// <summary>
        /// The internal content ID for the item to review.
        /// </summary>
        public string ContentId;

        /// <summary>
        /// The ID that the service assigned to the review.
        /// </summary>
        public string ReviewId;
    }

### <a name="initialize-application-specific-settings"></a>Alkalmazás-specifikus beállítások inicializálása

> [!NOTE]
> A Content Moderator Szolgáltatáskulcs rendelkezik egy második (RPS) sávszélesség-korlátjának kérelemre, és ha túllépi a korlátot, az SDK kivételt 429 hibakód. 
>
> Ingyenes szint kulcs esetében egy függő Entitás sebessége.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Adja hozzá a következő állandókat a **Program** osztály a program.cs fájlban.
    
    /// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Image List API.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const double latencyDelay = 45;

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Adja hozzá a következő állandókat és a statikus mezők a **Program** osztály a program.cs fájlban.

Frissítse előfizetését és csapat-specifikus adatait tartalmazza ezeket az értékeket.

> [!NOTE]
> A TeamName konstans értékre a létrehozásakor használt nevet a [Content Moderator felülvizsgálati eszközben](https://contentmoderator.cognitive.microsoft.com/) előfizetés. Kérheti le a TeamName származó a **hitelesítő adatok** című rész a **beállítások** (fogaskerék) menüben.
>
> A csapat nevét az az érték a **azonosító** mezőbe a **API** szakaszban.

    /// <summary>
    /// The name of the team to assign the review to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Conent Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "YOUR REVIEW TEAM ID";

    /// <summary>
    /// The optional name of the subteam to assign the review to.
    /// </summary>
    private const string Subteam = null;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Revies show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "YOUR API ENDPOINT";

    /// <summary>
    /// The media type for the item to review.
    /// </summary>
    /// <remarks>Valid values are "image", "text", and "video".</remarks>
    private const string MediaType = "image";

    /// <summary>
    /// The URLs of the images to create review jobs for.
    /// </summary>
    private static readonly string[] ImageUrls = new string[] {
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg"
        // add more if you want
    };

    /// <summary>
    /// The metadata key to initially add to each review item.
    /// </summary>
    private const string MetadataKey = "sc";

    /// <summary>
    /// The metadata value to initially add to each review item.
    /// </summary>
    private const string MetadataValue = "true";

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>Adja hozzá a következő statikus mezőket a **Program** osztály a program.cs fájlban.

Ezek a mezők segítségével nyomon követheti az alkalmazás állapotát.

    /// <summary>
    /// A static reference to the text writer to use for logging.
    /// </summary>
    private static TextWriter writer;

    /// <summary>
    /// The cached review information, associating a local content ID
    /// to the created review ID for each item.
    /// </summary>
    private static List<ReviewItem> reviewItems =
        new List<ReviewItem>();

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>A naplófájl üzeneteket írni metódus létrehozása

Adja hozzá a **Program** osztályhoz a következő metódust. 

    /// <summary>
    /// Writes a message to the log file, and optionally to the console.
    /// </summary>
    /// <param name="message">The message.</param>
    /// <param name="echo">if set to <c>true</c>, write the message to the console.</param>
    private static void WriteLine(string message = null, bool echo = false)
    {
        writer.WriteLine(message ?? String.Empty);

        if (echo)
        {
            Console.WriteLine(message ?? String.Empty);
        }
    }

## <a name="create-a-method-to-create-a-set-of-reviews"></a>Létrehozhat egy csoportot felülvizsgálatokról azoknak metódus létrehozása

Normális esetben azonosításához bejövő képek, szöveg-, egy üzleti logikát rendelkezik, vagy a videó, meg kell vizsgálni. Itt ugyanúgy használják, rögzített rendszerképek listáját.

Adja hozzá a **Program** osztályhoz a következő metódust.

    /// <summary>
    /// Create the reviews using the fixed list of images.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void CreateReviews(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Creating reviews for the following images:", true);

        // Create the structure to hold the request body information.
        List<CreateReviewBodyItem> requestInfo =
            new List<CreateReviewBodyItem>();

        // Create some standard metadata to add to each item.
        List<CreateReviewBodyItemMetadataItem> metadata =
            new List<CreateReviewBodyItemMetadataItem>(
            new CreateReviewBodyItemMetadataItem[] {
                new CreateReviewBodyItemMetadataItem(
                    MetadataKey, MetadataValue)
            });

        // Populate the request body information and the initial cached review information.
        for (int i = 0; i < ImageUrls.Length; i++)
        {
            // Cache the local information with which to create the review.
            var itemInfo = new ReviewItem()
            {
                Type = MediaType,
                ContentId = i.ToString(),
                Url = ImageUrls[i],
                ReviewId = null
            };

            WriteLine($" - {itemInfo.Url}; with id = {itemInfo.ContentId}.", true);

            // Add the item informaton to the request information.
            requestInfo.Add(new CreateReviewBodyItem(
                itemInfo.Type, itemInfo.Url, itemInfo.ContentId,
                CallbackEndpoint, metadata));

            // Cache the review creation information.
            reviewItems.Add(itemInfo);
        }

        var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync(
            "application/json", TeamName, requestInfo);

        // Update the local cache to associate the created review IDs with
        // the associated content.
        var reviewIds = reviewResponse.Result.Body;
        for (int i = 0; i < reviewIds.Count; i++)
        {
            Program.reviewItems[i].ReviewId = reviewIds[i];
        }

        WriteLine(JsonConvert.SerializeObject(
        reviewIds, Formatting.Indented));

        Thread.Sleep(throttleRate);
    }

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>A meglévő felülvizsgálatok állapotának lekéréséhez metódus létrehozása

Adja hozzá a **Program** osztályhoz a következő metódust. 

> [!Note]
> A gyakorlatban, így állíthatja be a visszahívási URL-Címének `CallbackEndpoint` szeretne kapni a manuális ellenőrzést (keresztül egy HTTP POST-kérés) URL-címre.
> Ez a módszer a függőben lévő értékelések állapotát ellenőrizni, módosításával.

    /// <summary>
    /// Gets the review details from the server.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void GetReviewDetails(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Getting review details:", true);
        foreach (var item in reviewItems)
        {
            var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(
                TeamName, item.ReviewId);

            WriteLine(
                $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                $"{reviewDetail.Result.Body.Status}.", true);
            WriteLine(JsonConvert.SerializeObject(
                reviewDetail.Result.Body, Formatting.Indented));

            Thread.Sleep(throttleRate);
        }
    }

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>Adja hozzá a kódot felülvizsgálatok csoportját hozhatja létre, és ellenőrizze a állapotát

Adja hozzá a következő kódot a **fő** metódust.

Ez a kód szimulálja számos, a művelet, amely definiálása és kezelése a listában, valamint listájának képernyőképek a használatával hajt végre. A naplózási szolgáltatások lehetővé teszi a válaszobjektumok hozta a Content Moderator Service SDK-hívásokat.

    using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
    {
        writer = outputWriter;
        using (var client = Clients.NewClient())
        {
            CreateReviews(client);
            GetReviewDetails(client);

            Console.WriteLine();
            Console.WriteLine("Perform manual reviews on the Content Moderator site.");
            Console.WriteLine("Then, press any key to continue.");
            Console.ReadKey();

            Console.WriteLine();
            Console.WriteLine($"Waiting {latencyDelay} seconds for results to propigate.");
            Thread.Sleep(latencyDelay * 1000);

            GetReviewDetails(client);
        }

        writer = null;
        outputWriter.Flush();
        outputWriter.Close();
    }

    Console.WriteLine();
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();

## <a name="run-the-program-and-review-the-output"></a>Futtassa a programot, és tekintse át a kimenetet

Az alábbi kimeneti példa láthatja:

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.

Jelentkezzen be a Content Moderator felülvizsgálati eszközében megtekintheti a függőben lévő rendszerképet, tekintse át a a **sc** címke beállítása **igaz**. Is megjelenik az alapértelmezett **egy** és **r** címkéket és a felülvizsgálati eszköz belül definiált egyéni címkéket. 

Használja a **tovább** gombra kattintva küldje el.

![Az emberi moderátorok kép áttekintése](images/moderation-reviews-quickstart-dotnet.PNG)

Ezután nyomja le bármelyik billentyűt a folytatáshoz.

    Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...

## <a name="check-out-the-following-output-in-the-log-file"></a>Tekintse meg a következő kimenetet a naplófájlban.

> [!NOTE]
> A kimeneti fájl, a karakterláncok "\{teamname}" és "\{callbackUrl}" értékeit tükrözze a `TeamName` és `CallbackEndpoint` mezőket, illetve.

A felülvizsgálat azonosítók és a tartalom URL-címek különböznek minden alkalommal, amikor a lemezkép futtassa az alkalmazást, és ha felülvizsgálat befejezése, a `reviewerResultTags` a mező jelzi, hogy hogyan a felülvizsgáló címkével ellátott az elemet.

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.
    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Pending",
        "reviewerResultTags": [],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
            "key": "a",
            "value": "False"
        },
        {
            "key": "r",
            "value": "True"
        },
        {
            "key": "sc",
            "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

## <a name="your-callback-url-if-provided-receives-this-response"></a>Ha meg van adva, megkapja ezt a választ a visszahívási URL-címét

A válasz az alábbi példához hasonlóan jelenik meg:

    {
        "ReviewId": "201801i48a2937e679a41c7966e838c92f5e649",
        "ModifiedOn": "2018-01-06T05:04:40.5525865Z",
        "ModifiedBy": "yourusername",
        "CallBackType": "Review",
        "ContentId": "0",
        "ContentType": "Image",
        "Metadata": {
            "sc": "true"
            },
        "ReviewerResultTags": {
            "a": "False",
            "r": "False",
        }
    }


## <a name="next-steps"></a>További lépések

Első a [Content Moderator .NET SDK-val](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) és a [Visual Studio-megoldás](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) ennél és a többi a Content Moderator rövid útmutató a .NET-hez, és az integrációval kapcsolatos első lépések.
