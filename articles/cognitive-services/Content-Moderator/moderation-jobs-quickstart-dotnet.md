---
title: .NET - Content Moderator használatával moderálás feladatok használata
titlesuffix: Azure Cognitive Services
description: A tartalom Moderator .NET SDK használatával kezdeményez vagy szöveges tartalom az Azure Content Moderator teljes körű tartalom-jóváhagyás feladatok.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 24d5483cf3b418cada3c5b7f03eedbff13cc36d6
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757045"
---
# <a name="define-and-use-moderation-jobs-net"></a>Definiálja és moderálás feladatok (.NET)

A moderálás feladat egy burkoló a tartalom-jóváhagyás, munkafolyamatok és értékelések funkciójának típusú funkcionál. Ez az útmutató ismerteti, és kódminták segítségével történő használatának első lépései a [Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) való:

- Moderálási feladat indítása, amely lehetővé teszi az emberi moderátorok általi vizsgálatot és felülvizsgálatok létrehozását
- Függőben lévő felülvizsgálat állapotának lekérése
- Felülvizsgálat nyomon követése és a végső eredmény lekérése
- Küldje el a visszahívási URL-címre a felülvizsgálati eredmények

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be, vagy hozzon létre egy fiókot a Content Moderator [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/) hely.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Arról való gondoskodás, hogy az API-kulcs meg tudja hívni a felülvizsgálati API-t a felülvizsgálat létrehozásához

Az előző lépések végrehajtása után elképzelhető, hogy két Content Moderator-kulcsa is lesz, ha az Azure Portalról indította el a folyamatot.

Ha az Azure által biztosított API-kulcsot tervezi használni az SDK-mintában, kövesse az [Azure-kulcs felülvizsgálati API-val történő használatát](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) ismertető szakaszban szereplő lépéseket annak érdekében, hogy alkalmazása meghívhassa a felülvizsgálati API-t, és felülvizsgálatokat hozhasson létre.

Ha a felülvizsgálati eszköz által létrehozott ingyenes próbakulcsot használja, a felülvizsgálati eszközhöz tartozó fiókja már tud a kulcsról, így nincs szükség további lépésekre.

## <a name="define-a-custom-moderation-workflow"></a>Egyéni moderálási munkafolyamat definiálása

A moderálási feladat az API-k használatával veti vizsgálat alá a tartalmakat, és egy **munkafolyamat** segítségével állapítja meg, hogy létre kell-e hoznia felülvizsgálatokat.
Bár a felülvizsgálati eszköz tartalmaz egy alapértelmezett munkafolyamatot, ehhez az útmutatóhoz [definiáljunk egy egyénit](Review-Tool-User-Guide/Workflows.md).

A munkafolyamat nevét használni fogja a moderálási feladatot elindító kódban.

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

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Content Moderator-ügyfél létrehozása

Adja meg a következő kódot, hogy létrehozzon egy Content Moderator-ügyfelet az előfizetéséhez.

> [!IMPORTANT]
> Adja meg a régióazonosító és az előfizetői azonosító értékét az **AzureRegion** és a **CMSubscriptionKey** mezőkben.

```csharp
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
```

### <a name="initialize-application-specific-settings"></a>Alkalmazásra jellemző beállítások inicializálása

Adja hozzá a következő állandókat és statikus mezőket a **Program** osztályhoz a Program.cs-ben.

> [!NOTE]
> Állítsa a TeamName állandót arra a névre, amelyet a Content Moderator-előfizetés létrehozásakor hozott létre. A TeamName értékét a [Content Moderator webhelyén](https://westus.contentmoderator.cognitive.microsoft.com/) ellenőrizheti.
> Jelentkezzen be, majd a **Beállítások** (fogaskerék) menüben válassza a **Hitelesítő adatok** lehetőséget.
>
> A csapatnév az **API** szakasz **Azonosító** mezőjében megadott érték.

```csharp
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
```

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Kód hozzáadása az automatikus moderáláshoz, felülvizsgálat létrehozásához és a feladat részleteinek lekéréséhez

> [!Note]
> A gyakorlatban a **CallbackEndpoint** visszahívási URL-címet arra az URL-címre kell cserélni, amely megkapja a manuális felülvizsgálat eredményeit (egy HTTP POST kérésen keresztül).

Először is adja hozzá a következő kódot a **Main** metódushoz.

```csharp
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
```

> [!NOTE]
> A Content Moderator-szolgáltatáskulcs rendelkezik egy RPS-alapú (kérések másodpercenkénti száma) sebességkorláttal. Ha túllépi ezt a korlátot, az SDK 429-es hibakódú kivételt jelez.
>
> Az ingyenes szint kulcsának a sebességkorlátja egy RPS.

## <a name="run-the-program-and-review-the-output"></a>A program futtatása és a kimenet áttekintése

A konzolon a következő mintakimenetet fogja látni:

```console
Perform manual reviews on the Content Moderator site.
Then, press any key to continue.
```

Jelentkezzen be a Content Moderator felülvizsgálati eszközbe a függőben lévő képfelülvizsgálat megtekintéséhez.

Kattintson a **Next** (Tovább) gombra a küldéshez.

![Képek felülvizsgálata emberi moderátorok által](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Mintakimenet megtekintése a naplófájlban

> [!NOTE]
> A kimeneti fájlban a **Teamname**, **ContentId**, **CallBackEndpoint** és **WorkflowId** sztringekben a korábban használt értékek jelennek meg.

```json
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
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>A visszahívási URL-cím (ha meg van adva) által kapott válasz

A következő példához hasonló választ fog látni:

> [!NOTE]
> A visszahívási válaszban a **ContentId** és a **WorkflowId** sztringben a korábban használt értékek jelennek meg.

```json
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
```

## <a name="next-steps"></a>További lépések

Szerezze be a kapcsolódó [Content Moderator .NET SDK-t](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) és [Visual Studio-megoldást](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) a .NET-es Content Moderator ezen és további rövid útmutatóihoz, hogy nekikezdhessen az integrációnak.
