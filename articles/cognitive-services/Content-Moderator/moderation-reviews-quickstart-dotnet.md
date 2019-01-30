---
title: 'Gyors útmutató: .NET - Content Moderator használatával felülvizsgálatok létrehozása'
titlesuffix: Azure Cognitive Services
description: Felülvizsgálatok létrehozása a .NET-hez készült Azure Content Moderator SDK-val.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 318d80049caf1328aac1cc4423b0de10df7a8c58
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211508"
---
# <a name="quickstart-create-reviews-using-net"></a>Gyors útmutató: Hozzon létre értékelések .NET használatával

Ez a cikk ahhoz biztosít információt és kódmintákat, hogy elvégezhesse a következő műveleteket a [Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) segítségével:
 
- Felülvizsgálati készlet létrehozása emberi moderátorok számára
- Meglévő felülvizsgálatok állapotának lekérése emberi moderátorok számára

A tartalmak általában némi automatizált moderáláson mennek keresztül, mielőtt be lennének ütemezve az ember általi felülvizsgálatra. Ez a cikk csak azt tárgyalja, hogyan hozhat létre felülvizsgálatot ember által végzett moderáláshoz. Egy teljes, olvassa az [Facebook tartalom-jóváhagyás](facebook-post-moderation.md) és [e-kereskedelmi Termékképek mérsékelt](ecommerce-retail-catalog-moderation.md) oktatóanyagok.

Ez a cikk feltételezi, hogy már ismeri a Visual Studiót és a C# nyelvet.

## <a name="sign-up-for-content-moderator"></a>Regisztráció a Content Moderatorba

Ahhoz, hogy a REST API-n vagy az SDK-n keresztül használhassa a Content Moderator szolgáltatásait, előbb be kell szereznie egy előfizetői azonosítót. A Content Moderatorra történő előfizetéshez és az előfizetői azonosító beszerzéséhez kövesse a [Cognitive Services-fiók létrehozásával](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) kapcsolatos szakaszban található utasításokat.

## <a name="sign-up-for-a-review-tool-account-if-not-completed-in-the-previous-step"></a>Fiók létrehozása a felülvizsgálati eszközhöz, ha az előző lépésben erre nem került sor

Ha a Content Moderatort az Azure Portalon szerezte be, [hozzon létre egy fiókot a felülvizsgálati eszközhöz](https://contentmoderator.cognitive.microsoft.com/) is, és hozzon létre egy felügyeleti csapatot. Szüksége lesz a csapatazonosítóra és a felülvizsgálati eszközre, ha egy feladat elkezdéséhez meg szeretné hívni a felülvizsgálati API-t, illetve ha meg szeretné tekinteni a felülvizsgálatokat a felülvizsgálati eszközben.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Arról való gondoskodás, hogy az API-kulcs meg tudja hívni a felülvizsgálati API-t a felülvizsgálat létrehozásához

Az előző lépések végrehajtása után elképzelhető, hogy két Content Moderator-kulcsa is lesz, ha az Azure Portalról indította el a folyamatot. 

Ha az Azure által biztosított API-kulcsot tervezi használni az SDK-mintában, kövesse az [Azure-kulcs felülvizsgálati API-val történő használatát](review-tool-user-guide/credentials.md#use-the-azure-account-with-the-review-tool-and-review-api) ismertető szakaszban szereplő lépéseket annak érdekében, hogy alkalmazása meghívhassa a felülvizsgálati API-t, és felülvizsgálatokat hozhasson létre.

Ha a felülvizsgálati eszköz által létrehozott ingyenes próbakulcsot használja, a felülvizsgálati eszközhöz tartozó fiókja már tud a kulcsról, így nincs szükség további lépésekre.

## <a name="create-your-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Adjon hozzá egy új **Konzolalkalmazás (.NET-keretrendszer)** projektet a megoldáshoz.

   A mintakódban adja a **CreateReviews** nevet a projektnek.

1. Válassza ki ezt a projektet a megoldás egyedüli kezdőprojektjeként.

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

Telepítse az alábbi NuGet-csomagokat:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>A program „using” utasításainak frissítése

Módosítsa a program „using” utasításait.

    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="create-the-content-moderator-client"></a>Content Moderator-ügyfél létrehozása

Adja meg a következő kódot, hogy létrehozzon egy Content Moderator-ügyfelet az előfizetéséhez.

> [!IMPORTANT]
> Adja meg a régióazonosító és az előfizetői azonosító értékét az **AzureRegion** és a **CMSubscriptionKey** mezőkben.


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

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>Osztály létrehozása belső tartalomadatok és egy felülvizsgálati azonosító társításához

Adja hozzá a következő osztályt a **Program** osztályhoz.
Az osztály használatával társítsa a felülvizsgálati azonosítót az elem belső tartalomazonosítójához.

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

### <a name="initialize-application-specific-settings"></a>Alkalmazásra jellemző beállítások inicializálása

> [!NOTE]
> A Content Moderator-szolgáltatáskulcs rendelkezik egy RPS-alapú (kérések másodpercenkénti száma) sebességkorláttal, amelyet ha túllép, az SDK egy 429-es hibakódú kivételt jelez. 
>
> Az ingyenes szint kulcsának a sebességkorlátja egy RPS.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Adja hozzá a következő állandókat a **Program** osztályhoz a Program.cs-ben.
    
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
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Adja hozzá a következő állandókat és statikus mezőket a **Program** osztályhoz a Program.cs-ben.

Frissítse ezeket az értékeket, hogy tartalmazzák az előfizetés és a csapat adatait.

> [!NOTE]
> Állítsa a TeamName állandót arra a névre, amelyet a [Content Moderator felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) előfizetésének létrehozásakor használt. A TeamName értékét a **Beállítások** (fogaskerék) menü **Hitelesítő adatok** szakaszában találja meg.
>
> A csapatnév az **API** szakasz **Azonosító** mezőjében megadott érték.

    /// <summary>
    /// The name of the team to assign the review to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Content Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "YOUR REVIEW TEAM ID";

    /// <summary>
    /// The optional name of the subteam to assign the review to.
    /// </summary>
    private const string Subteam = null;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Reviews show up for reviewers on your team. 
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

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>Adja hozzá a következő statikus mezőket a **Program** osztályhoz a Program.cs-ben.

Ezeket a mezőket az alkalmazás állapotának nyomon követésére használhatja.

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

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>Metódus létrehozása üzenetek naplófájlba történő írására

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

## <a name="create-a-method-to-create-a-set-of-reviews"></a>Metódus létrehozása felülvizsgálati készlet létrehozására

Rendszerint a rendelkezésére áll valamilyen üzleti logika azon bejövő képek, szövegek vagy videók azonosítására, amelyek felülvizsgálatra szorulnak. Itt azonban a képeknek csak egy rögzített listáját fogjuk használni.

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

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>Metódus létrehozása a meglévő felülvizsgálatok állapotának lekérésére

Adja hozzá a **Program** osztályhoz a következő metódust. 

> [!Note]
> A gyakorlatban a `CallbackEndpoint` visszahívási URL-címet rendszerint arra az URL-re állítjuk be, amely megkapja a manuális felülvizsgálat eredményeit (egy HTTP POST kérésen keresztül).
> Ezt a metódust úgy is módosíthatja, hogy a függőben lévő felülvizsgálatok állapotát ellenőrizze.

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

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>Kód hozzáadása felülvizsgálati készlet létrehozásához és állapotának ellenőrzéséhez

Adja hozzá az alábbi kódot a **Main** metódushoz.

Ez a kód sok olyan műveletet szimulál, amelyet a lista definiálásakor és kezelésekor, illetve a lista képek vizsgálatához történő használatakor hajt végre. A naplózási szolgáltatásokkal megtekintheti azokat a válaszobjektumokat, amelyeket a Content Moderator szolgáltatásnak intézett SDK-hívások hoztak létre.

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

## <a name="run-the-program-and-review-the-output"></a>A program futtatása és a kimenet áttekintése

A következő mintakimenetet fogja látni:

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.

Jelentkezzen be a Content Moderator felülvizsgálati eszközbe a függőben lévő képfelülvizsgálat megtekintéséhez, amelynek **sc** címkéje **true** értékre van állítva. Láthatja az alapértelmezett **a** és **r** címkéket, illetve egyéb olyan egyéni címkéket, amelyeket definiált a felülvizsgálati eszközben. 

Kattintson a **Next** (Tovább) gombra a küldéshez.

![Képek felülvizsgálata emberi moderátorok által](images/moderation-reviews-quickstart-dotnet.PNG)

A folytatáshoz nyomjon le egy billentyűt.

    Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...

## <a name="check-out-the-following-output-in-the-log-file"></a>Az alábbi kimenet áttekintése a naplófájlban

> [!NOTE]
> A kimeneti fájl \{teamname} és \{callbackUrl} sztringjei a `TeamName` és `CallbackEndpoint` mezők értékeit mutatják.

A felülvizsgálati azonosítók és a képi tartalmak URL-címei az alkalmazás minden egyes futtatásakor különböznek, és amikor a felülvizsgálat befejeződik, a `reviewerResultTags` mező mutatja meg, hogy a felülvizsgáló milyen címkével látta el az elemet.

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

## <a name="your-callback-url-if-provided-receives-this-response"></a>A visszahívási URL-cím (ha meg van adva) által kapott válasz

A következő példához hasonló választ fog látni:

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

Szerezze be a kapcsolódó [Content Moderator .NET SDK-t](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) és [Visual Studio-megoldást](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) a .NET-es Content Moderator ezen és további rövid útmutatóihoz, hogy nekikezdhessen az integrációnak.
