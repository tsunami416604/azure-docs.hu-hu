---
title: SDK-k és minták – Content Moderator, Python, Java, Node.js és .NET
titlesuffix: Azure Cognitive Services
description: SDK-k és minták beszerzése a Content Moderatorhoz
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: sample
ms.date: 02/27/2018
ms.author: sajagtap
ms.openlocfilehash: a57f6a312b00d7ec3d927c6fda319f1de8663c9c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220448"
---
# <a name="content-moderator-sdks-and-samples"></a>Content Moderator SDK-k és minták

## <a name="sdks-for-python-java-nodejs-and-net"></a>Python, Java, Node.js és .NET SDK-k

- [Content Moderator SDK Pythonhoz](https://pypi.python.org/pypi/azure-cognitiveservices-vision-contentmoderator)
- [Content Moderator SDK Javához](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-cognitiveservices-contentmoderator%22)
- [Content Moderator SDK Node.js-hez](https://www.npmjs.com/package/azure-cognitiveservices-contentmoderator)
- [Content Moderator SDK .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="net-sdk-samples"></a>.NET SDK-minták

A következő felsorolás a .NET-hez készült Azure Content Moderator SDK használatával létrehozott kódmintákra mutató hivatkozásokat tartalmaz.

- **Segédkódtár**: [Content Moderator-ügyfél létrehozása más mintákban való használathoz](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Lásd a [rövid útmutatót](content-moderator-helper-quickstart-dotnet.md).

### <a name="moderation"></a>Moderálás 
- **Képmoderálás**: [Felnőtteknek szóló és kényes tartalom, szöveg és arcok észlelése a képeken](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Lásd a [rövid útmutatót](image-moderation-quickstart-dotnet.md).
- **Egyéni képek**: [Moderálás egyéni képlistákkal](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Lásd a [rövid útmutatót](image-lists-quickstart-dotnet.md).

> [!NOTE]
> A maximális korlát **5 képlista**, amelyek egyenként **nem haladhatják meg a 10 000 képet**.
>

- **Szövegmoderálás**: [Profanitás és személyazonosításra alkalmas adatok kiszűrése a szövegekben](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Lásd a [rövid útmutatót](text-moderation-quickstart-dotnet.md).
- **Egyéni kifejezések**: [Moderálás egyéni kifejezéslistákkal](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Lásd a [rövid útmutatót](term-lists-quickstart-dotnet.md).

> [!NOTE]
> A maximális korlát **5 kifejezéslista**, amelyek egyenként **nem haladhatják meg a 10 000 kifejezést**.
>

- **Videómoderálás**: [Felnőtteknek szóló és kényes tartalom észlelése a videókban, és az eredmények lekérése](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Lásd a [rövid útmutatót](video-moderation-api.md).

### <a name="review"></a>Áttekintés
- **Képekkel kapcsolatos feladatok**: [Moderálási feladat indítása, amely átvizsgálást végez, és felülvizsgálatokat hoz létre](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Lásd a [rövid útmutatót](moderation-jobs-quickstart-dotnet.md).
- **Képek felülvizsgálata**: [Felülvizsgálatok létrehozása az emberi irányítás számára](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Lásd a [rövid útmutatót](moderation-reviews-quickstart-dotnet.md).
- **Videók felülvizsgálata**: [Videó-felülvizsgálatok létrehozása az emberi irányítás számára](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Lásd a [rövid útmutatót](video-reviews-quickstart-dotnet.md).
- **Videóátiratok felülvizsgálata**: [Videóátirat-felülvizsgálatok létrehozása az emberi irányítás számára](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Lásd a [rövid útmutatót](video-reviews-quickstart-dotnet.md)

Az összes .NET-minta megtekintése: [Content Moderator .NET-példák a GitHubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).

## <a name="rest-api-samples-in-c"></a>REST API-minták C# nyelven

- [Képmoderálás](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageModeration)
- [Szövegmoderálás](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/TextModeration)
- [Videomoderálás](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/VideoModeration)
- [Képekre vonatkozó vélemények](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageReviews)
- [Képekkel kapcsolatos feladatok](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageJob)

A minták útmutatóinak eléréséhez tekintse meg az [igény szerinti webináriumot](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html).

## <a name="tutorials"></a>Oktatóanyagok
- [E-kereskedelmi katalógusok moderálása](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration). Lásd az [oktatóanyagot](ecommerce-retail-catalog-moderation.md).
- [Facebook-tartalom moderálása](https://github.com/MicrosoftContentModerator/samples-fbPageModeration). Lásd az [oktatóanyagot](facebook-post-moderation.md).
- [Videó és átirat moderálása, valamint a megoldás áttekintése](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) Lásd az [oktatóanyagot](video-transcript-moderation-review-tutorial-dotnet.md)

## <a name="on-demand-webinars"></a>Igény szerinti webináriumok
- [Gépi támogatású nagy léptékű tartalommoderálás a Content Moderatorral](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html)
