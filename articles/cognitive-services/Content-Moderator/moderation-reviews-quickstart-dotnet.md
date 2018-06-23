---
title: Az Azure Content moderátor - értékelést .NET használatával hozzon létre |} Microsoft Docs
description: Létrehozása ellenőrzi, hogy Azure tartalom moderátor SDK for .NET használatával
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/04/2018
ms.author: sajagtap
ms.openlocfilehash: 6a0ff48f4ea17f9c800f3e6c096df2492699f87a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347178"
---
# <a name="create-reviews-using-net"></a>Hozzon létre értékelést .NET használatával

Ez a cikk információkat tartalmazza, és mintakódok segítséget első lépései a .NET-hez, hogy a tartalom moderátor SDK használatával:
 
- Létrehozhat egy értékelést az emberi moderátorok
- Emberi moderátorok meglévő értékelést állapotának lekérése

Általában tartalom végighalad az egyes automatizált moderálás előtt emberi felülvizsgálati lett ütemezve. Ez a cikk csak bemutatja, hogyan adhat emberi moderálás a felülvizsgálati létrehozása. A jóval összetettebb, olvassa el a [tartalmat Facebook-moderálás](facebook-post-moderation.md) és [kereskedelmi katalógus moderálás](ecommerce-retail-catalog-moderation.md) oktatóanyagok.

Ez a cikk feltételezi, hogy Ön már ismeri a Visual Studio és a C#.

## <a name="sign-up-for-content-moderator-services"></a>Iratkozzon fel a tartalom moderátor szolgáltatások

Tartalom moderátor-szolgáltatások díjairól a REST API-t vagy az SDK használata előtt be kell egy előfizetési kulcsot.
Tekintse meg a [gyors üzembe helyezés](quick-start.md) megtudhatja, hogyan szerezhet a kulcsot.

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

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>Hozzon létre egy osztályt, rendelje hozzá a belső tartalom adatai egy felülvizsgálati azonosítója

Adja hozzá a következő osztályt a **Program** osztály.
Ez az osztály az a cikk a belső Tartalomazonosítót felülvizsgálati Azonosítót használ.

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

### <a name="initialize-application-specific-settings"></a>Az alkalmazás-specifikus beállításokat inicializálása

> [!NOTE]
> A tartalom moderátor szolgáltatási kulcs egy kérelmek / második (RPS) sávszélesség-korlátjának rendelkezik, és ha meghaladja a korlátot, az SDK 429-es jelű hibakód kivételt jelez. 
>
> Ingyenes szint kulcs egy RPS sávszélesség-korlátjának rendelkezik.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Adja hozzá a következő állandókat, hogy a **Program** osztály a program.cs fájlban.
    
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

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Adja hozzá a következő állandók és a statikus mezők a **Program** osztály a program.cs fájlban.

Frissítheti ezeket az értékeket előfizetését és csapata vonatkozó információkat tartalmazzák.

> [!NOTE]
> A TeamName konstans értékre létrehozásakor használt nevét a [tartalom moderátor felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) előfizetés. A TeamName lekérése a **hitelesítő adatok** szakasz a **beállítások** (fogaskerék) menü.
>
> A csoport nevét az az érték a **azonosító** mező mellett a **API** szakasz.

    /// <summary>
    /// The name of the team to assign the review to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Conent Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "{teamname}";

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
    private const string CallbackEndpoint = "{callbackUrl}";

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

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>A következő statikus mezők hozzáadása a **Program** osztály a program.cs fájlban.

Ezek a mezők segítségével nyomon követheti az alkalmazás állapotára.

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

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>Hozzon létre egy metódust az üzenetek ír a naplófájlba

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

## <a name="create-a-method-to-create-a-set-of-reviews"></a>A létrehozott értékelést metódus létrehozása

Általában van néhány üzleti logikát, azonosítsa a bejövő lemezképek, a szöveg, vagy videót, amely meg kell vizsgálni. Azonban itt használja a képek rögzített listáját.

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

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>A meglévő felülvizsgálat állapotának beolvasása metódus létrehozása

Adja hozzá a **Program** osztályhoz a következő metódust. 

> [!Note]
> A gyakorlatban, akkor a visszahívási URL-cím állítania `CallbackEndpoint` (via HTTP POST-kérelmet) manuális felülvizsgálati eredményeinek kapja URL-címre.
> Ez a módszer a függőben lévő értékelést állapotának ellenőrzésével módosíthatják.

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

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>Adja hozzá a kódot a létrehozott értékelést, és állapota

Adja hozzá a következő kódot a **fő** metódust.

Ez a kód a művelet, amelyet a definiálása és kezelése a listában, valamint képernyő lemezképekhez lista segítségével számos szimulálja. A naplózási szolgáltatások lehetővé teszik az SDK-hívásokat a tartalom moderátor szolgáltatás által létrehozott válasz objektumok megjelenítéséhez.

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

A következő minta kimenet jelenik meg:

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.

Jelentkezzen be a tartalom moderátor eszköz a függőben lévő, tekintse át a kép megtekintéséhez tekintse át a **sc** címke beállítása **igaz**. Emellett tekintse meg az alapértelmezett **egy** és **r** címkék és a felülvizsgálati eszköz belül definiált egyéni címkéket. 

Használja a **következő** gombra kattintva küldje el.

![Az emberi moderátorok kép áttekintése](images/moderation-reviews-quickstart-dotnet.PNG)

Ezután nyomja le bármelyik billentyűt, hogy továbbra is.

    Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...

## <a name="check-out-the-following-output-in-the-log-file"></a>Tekintse meg a következő kimeneti a naplófájlban.

> [!NOTE]
> A kimeneti fájl, a karakterlánc a "\{teamname}" és "\{callbackUrl}" az értékeknek felelnek meg a `TeamName` és `CallbackEndpoint` mezők, illetve.

A felülvizsgálati azonosítók és a kép URL-címei különböző minden alkalommal, amikor a tartalom futtassa az alkalmazást, és egy felülvizsgálat befejeződik, a `reviewerResultTags` mező tükrözi, hogy a felülvizsgáló címkézett a cikk.

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

## <a name="your-callback-url-if-provided-receives-this-response"></a>A visszahívási URL-címet ad meg, ha a válasz fogadása

Az alábbi példához hasonló válasz jelenik meg:

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

[Töltse le a Visual Studio megoldás](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) ennél és egyéb tartalom moderátor quickstarts a .NET-hez, és az integráció a kezdéshez.
