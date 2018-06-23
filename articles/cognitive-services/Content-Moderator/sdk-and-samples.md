---
title: Az Azure Content moderátor moderálás SDK-k és minták tartalom |} Microsoft Docs
description: SDK-k és minták lekérése tartalom moderátor
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/27/2018
ms.author: sajagtap
ms.openlocfilehash: 40b8fc0f63383e837f0813f876f20806e6e8c6eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347110"
---
# <a name="content-moderator-sdks-and-samples"></a>Tartalom moderátor SDK-k és minták

## <a name="sdks-for-python-java-nodejs-and-net"></a>A Python, Java, Node.js és a .NET SDK-k

- [Tartalom moderátor Python SDK](https://pypi.python.org/pypi/azure-cognitiveservices-vision-contentmoderator)
- [Tartalom moderátor Java SDK](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-cognitiveservices-contentmoderator%22)
- [Tartalom moderátor a Node.js SDK](https://www.npmjs.com/package/azure-cognitiveservices-contentmoderator)
- [Tartalom moderátor a .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="net-sdk-samples"></a>.NET SDK-minták

Az alábbi lista tartalmazza az Azure tartalom moderátor SDK for .NET használatával beépített mintakódok mutató hivatkozásokat tartalmaz.

- **Segédkódtárba helyezni**: [használata tartalom moderátor ügyfelet létrehozni a más mintában](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Lásd: [gyors üzembe helyezés](content-moderator-helper-quickstart-dotnet.md).

### <a name="moderation"></a>Moderálás 
- **Kép moderálás**: [felnőtt és ellopható tartalmát, a szöveg és a lapok kép kiértékelése](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Lásd: [gyors üzembe helyezés](image-moderation-quickstart-dotnet.md).
- **Egyéni lemezképek**: [mérsékelt egyéni lemezkép listákkal](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Lásd: [gyors üzembe helyezés](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Nincs a jelenlegi maximális műveletszámot **5 kép listák** minden listájával, hogy **legfeljebb 10 000 képek**.
>

- **Szöveg moderálás**: [szöveg Profanitás és a személyes azonosításra alkalmas adatokat (PII) képernyőn](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Lásd: [gyors üzembe helyezés](text-moderation-quickstart-dotnet.md).
- **Egyéni kifejezések**: [mérsékelt egyéni kifejezés listákkal](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Lásd: [gyors üzembe helyezés](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Nincs a jelenlegi maximális műveletszámot **5 kifejezés listája** minden listájával, hogy **legfeljebb 10 000 feltételek**.
>

- **Videó moderálás**: [felnőtt és ellopható tartalom videó vizsgálja, és az eredményt](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Lásd: [gyors üzembe helyezés](video-moderation-api.md).

### <a name="review"></a>Áttekintés
- **Feladatok kép**: [vizsgálja, és értékelést létrehozó moderálás feladat indítása](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Lásd: [gyors üzembe helyezés](moderation-jobs-quickstart-dotnet.md).
- **Kép értékelést**: [emberi hurok értékelést létrehozása](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Lásd: [gyors üzembe helyezés](moderation-reviews-quickstart-dotnet.md).
- **Ellenőrzi, hogy a videó**: [létrehozása videó ellenőrzi, hogy az emberi hurok](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Lásd: [gyors üzembe helyezés](video-reviews-quickstart-dotnet.md)
- **Ellenőrzi, hogy videó Beszélgetés szövegének**: [létrehozása videó Beszélgetés szövegének ellenőrzi, hogy az emberi hurok](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) lásd: [gyors üzembe helyezés](video-reviews-quickstart-dotnet.md)

Tekintse meg az összes .NET minták a [tartalom moderátor .NET minták a Githubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).

## <a name="rest-api-samples-in-c"></a>A C# REST API-minták

- [Képmoderálás](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageModeration)
- [Szövegmoderálás](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/TextModeration)
- [Videomoderálás](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/VideoModeration)
- [Képekre vonatkozó vélemények](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageReviews)
- [Kép feladatok](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageJob)

Ezeket a mintákat a forgatókönyvek, tekintse meg a [igény szerinti webinar](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html).

## <a name="tutorials"></a>Oktatóanyagok
- [Kereskedelmi katalógus moderálás](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration). Lásd: [oktatóanyag](ecommerce-retail-catalog-moderation.md).
- [Tartalom Facebook-moderálás](https://github.com/MicrosoftContentModerator/samples-fbPageModeration). Lásd: [oktatóanyag](facebook-post-moderation.md).
- [Videó és Beszélgetés szövegének moderálás, és nézze meg megoldás](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) lásd [oktatóanyag](video-transcript-moderation-review-tutorial-dotnet.md)

## <a name="on-demand-webinars"></a>Igény szerinti előadások
- [A tartalom moderátor léptékű tartalom moderálás gép támogatású](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html)
