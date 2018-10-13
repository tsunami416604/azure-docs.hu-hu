---
title: 'Gyors útmutató: Ellenőrizze a C# - Content Moderator szöveges tartalom'
titlesuffix: Azure Cognitive Services
description: Hogyan ellenőrizheti a szöveges tartalom Moderator tartalom SDK használatának a C#
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: sajagtap
ms.openlocfilehash: ae795ad823c32bc83669d5e98e3fd922500741d4
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309212"
---
# <a name="quickstart-check-text-content-in-c"></a>Gyors útmutató: Ellenőrizze a szöveges tartalom C#-ban 

Ez a cikk ahhoz biztosít információt és kódmintákat, hogy elvégezhesse a következő műveleteket a [Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) segítségével:

- Vulgáris SMS-t kifejezés szerinti szűrés
- A machine learning-alapú modellek használata [besorolása a szöveg](text-moderation-api.md#classification) három kategóriába sorolhatók.
- Például Egyesült Államok és UK telefonszámok, e-mail-címeket és postai címéhez USA észleli a személyes azonosításra alkalmas adatokat (PII).
- Szöveg- és az automatikus javítás elgépelések normalizálása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="sign-up-for-content-moderator-services"></a>Regisztráció a Content Moderator szolgáltatásaiba

A Content Moderator szolgáltatások REST API vagy az SDK használatához szüksége lesz egy előfizetési kulcsot. A Content Moderator szolgáltatás található a [az Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) beszerzése utólag.

## <a name="create-your-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Adjon hozzá egy új **Konzolalkalmazás (.NET-keretrendszer)** projektet a megoldáshoz.

   A mintakód adja a projektnek **TextModeration**.

1. Válassza ki ezt a projektet a megoldás egyedüli kezdőprojektjeként.

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

Telepítse az alábbi NuGet-csomagokat:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>A program „using” utasításainak frissítése

Adja hozzá a következő `using` utasításokat. 

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

Adja hozzá a következő statikus mezőket a **Program** osztályhoz a Program.cs-ben.

```csharp
/// <summary>
/// The name of the file that contains the text to evaluate.
/// </summary>
/// <remarks>You will need to create an input file and update this path
/// accordingly. Relative paths are relative to the execution directory.</remarks>
private static string TextFile = "TextFile.txt";

/// <summary>
/// The name of the file to contain the output from the evaluation.
/// </summary>
/// <remarks>Relative paths are relative to the execution directory.</remarks>
private static string OutputFile = "TextModerationOutput.txt";
```

A következő szöveg bemenetként, ebben a rövid útmutatóban használtuk.

> [!NOTE]
> Az érvénytelen társadalombiztosítási szám a következő minta szöveg szándékosan így. A célja, hogy továbbítja a minta bemeneti és kimeneti formátumot.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="add-code-to-load-and-evaluate-the-input-text"></a>Adja hozzá a kódot betölteni és kiértékelheti a bemeneti szöveg

Adja hozzá az alábbi kódot a **Main** metódushoz.

```csharp
// Load the input text.
string text = File.ReadAllText(TextFile);
Console.WriteLine("Screening {0}", TextFile);

text = text.Replace(System.Environment.NewLine, " ");

// Save the moderation results to a file.
using (StreamWriter outputWriter = new StreamWriter(OutputFile, false))
{
    // Create a Content Moderator client and evaluate the text.
    using (var client = Clients.NewClient())
    {
        // Screen the input text: check for profanity, classify the text into three categories,
        // do autocorrect text, and check for personally identifying
        // information (PII)
        outputWriter.WriteLine("Autocorrect typos, check for matching terms, PII, and classify.");
        var screenResult =
        client.TextModeration.ScreenText("eng", "text/plain", text, true, true, null, true);
        outputWriter.WriteLine(
                JsonConvert.SerializeObject(screenResult, Formatting.Indented));
    }
    outputWriter.Flush();
    outputWriter.Close();
}
```

> [!NOTE]
> A Content Moderator-szolgáltatáskulcs rendelkezik egy RPS-alapú (kérések másodpercenkénti száma) sebességkorláttal, amelyet ha túllép, az SDK egy 429-es hibakódú kivételt jelez. Ingyenes szint kulcsot használ, a kérések aránya korlátozódik egy kérés / másodperc.

## <a name="run-the-program-and-review-the-output"></a>A program futtatása és a kimenet áttekintése

A minta a program kimenetét, a naplófájlba írt van:

```json
Autocorrect typos, check for matching terms, PII, and classify.
{
"OriginalText": "\"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).\"",
"NormalizedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"AutoCorrectedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"Misrepresentation": null,
    
"Classification": {
        "Category1": {
        "Score": 1.5113095059859916E-06
        },
        "Category2": {
        "Score": 0.12747249007225037
        },
        "Category3": {
        "Score": 0.98799997568130493
        },
        "ReviewRecommended": true
  },
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "PII": {
        "Email": [
            {
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 33
            }
            ],
        "IPA": [
            {
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 73
            }
            ],
        "Phone": [
            {
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 57
            },
            {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 211
            },
            {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 207
            },
            {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 227
            },
            {
        "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 244
            }
            ],
         "Address": [{
             "Text": "1 Microsoft Way, Redmond, WA 98052",
             "Index": 89
                }]
    },
  "Language": "eng",
  "Terms": [
    {
        "Index": 22,
        "OriginalIndex": 22,
        "ListId": 0,
        "Term": "crap"
    }
  ],
  "TrackingId": "9392c53c-d11a-441d-a874-eb2b93d978d3"
}
```

## <a name="next-steps"></a>További lépések

Szerezze be a kapcsolódó [Content Moderator .NET SDK-t](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) és [Visual Studio-megoldást](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) a .NET-es Content Moderator ezen és további rövid útmutatóihoz, hogy nekikezdhessen az integrációnak.
