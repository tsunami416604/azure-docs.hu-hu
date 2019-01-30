---
title: 'Gyors útmutató: A szöveg elemzése C# -Content Moderator'
titlesuffix: Azure Cognitive Services
description: Szöveg tartalmának elemzése különböző kifogásolható tartalmak felismeréséhez a Content Moderator SDK for .NET használatával
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: fbbb58d4dc3774e3142787894ab6dfe0580c25b7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224615"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>Gyors útmutató: A nem kívánt tartalom szöveges tartalmakat elemezhetC# 

Ez a cikk ahhoz biztosít információt és kódmintákat, hogy megismerkedhessen a [Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) használatával. Megtanulhatja, hogyan végezhet szűrést kifejezések alapján, és hogyan osztályozhatja a szöveges tartalmakat az esetlegesen kifogásolható tartalmak moderálása céljából.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek
- A Content Moderator előfizetői azonosítója. A Content Moderatorra történő előfizetéshez és az előfizetői azonosító beszerzéséhez kövesse a [Cognitive Services-fiók létrehozásával](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) kapcsolatos szakaszban található utasításokat.
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.

> [!NOTE]
> Ez az útmutató ingyenes szintű Content Moderator-előfizetést használ. Az egyes előfizetési szintek tartalmáról a [díjszabást és a korlátozásokat](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/) ismertető oldalon talál információkat.

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. Hozzon létre egy új **Console app (.NET Framework)** (Konzolalkalmazás- (.NET-keretrendszer)) projektet a Visual Studióban és adja neki a **TextModeration** nevet. 
1. Ha más projektek is vannak a megoldásban, válassza ki ezt a projektet az egyedüli kezdőprojektként.
1. Szerezze be a szükséges NuGet-csomagokat. A Solution Explorer (Megoldáskezelő) felületén kattintson a jobb gombbal a projektre és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget, majd keresse meg és telepítse a következő csomagokat:
    - Microsoft.Azure.CognitiveServices.ContentModerator
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json

## <a name="add-text-moderation-code"></a>Szövegmoderálási kód hozzáadása

Következő lépésként másolja és illessze be az ebben az útmutatóban található kódot a projektjébe egy alapszintű tartalommoderálási forgatókönyv megvalósításához.

### <a name="include-namespaces"></a>Névterek belefoglalása

Adja hozzá az alábbi `using` utasításokat a *Program.cs* fájl elejéhez.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Content Moderator-ügyfél létrehozása

Adja hozzá a következő kódot a *Program.cs* fájlhoz, hogy létrehozzon egy Content Moderator-ügyfélszolgáltatót az előfizetéséhez. Adja hozzá a kódot a **Program** osztály mellett, ugyanabban a névtérben. Frissítenie kell a régióazonosító és az előfizetői azonosító értékét az **AzureRegion** és a **CMSubscriptionKey** mezőkben.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=54-77)]

### <a name="set-up-input-and-output-targets"></a>Bemeneti és kimeneti célok beállítása

Adja hozzá a következő statikus mezőket a **Program** osztályhoz a _Program.cs_-ben. Ezek adják meg a bemeneti szöveg tartalmának és a kimeneti JSON tartalmának fájljait.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=15-19)]

Létre kell hoznia a *TextFile.txt* bemeneti fájlt, és ennek megfelelően frissítenie kell az elérési útját (a relatív elérési utak a végrehajtási könyvtárhoz képest relatívak). Nyissa meg a _TextFile.txt_ fájlt, és adja hozzá a moderálandó szöveget. Ez a rövid útmutató a következő mintaszöveget használja:

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>A bemeneti szöveg betöltése

Adja hozzá az alábbi kódot a **Main** metódushoz. A **ScreenText** metódus a legfontosabb művelet. Ennek a metódusnak a paraméterei adják meg, melyik tartalommoderálási művelet megy végbe. Ebben a példában a metódus a következőkre van konfigurálva:
- Potenciális profanitás észlelése a szövegben.
- A szöveg normalizálása és a gépelési hibák automatikus javítása.
- A személyazonosításra alkalmas adatok (IIP), például USA-beli és egyesült királyságbeli telefonszámok, e-mail-címek, USA-beli postacímek észlelése.
- Gépi tanuláson alapuló modellek használata a szöveg három kategóriába való besorolásához.

Ha többet szeretne megtudni arról, mire szolgálnak ezek a műveletek, kattintson a [További lépések](#next-steps) szakaszban található hivatkozásra.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=23-48)]

## <a name="run-the-program"></a>A program futtatása

A program a JSON-sztringadatokat a _TextModerationOutput.txt_ fájlba írja. Az ebben a rövid útmutatóban használt mintaszöveg a következő kimenetet adja:

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

Ebben a rövid útmutatóban létrehozott egy egyszerű .NET-alkalmazást, amely a Content Moderator szolgáltatás segítségével releváns információkat ad vissza egy adott szövegmintáról. Következő lépésként többet tudhat meg arról, mit jelentenek a különböző jelzők és besorolások, így eldöntheti, milyen adatokra van szüksége, és hogyan kezelje ezeket az alkalmazása.

> [!div class="nextstepaction"]
> [Szövegmoderálási útmutató](text-moderation-api.md)
