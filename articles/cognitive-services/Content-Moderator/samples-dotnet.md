---
title: Kódminták – Content Moderator, .NET
description: A Content Moderator használata az alkalmazásokban
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: sample
ms.date: 01/10/2018
ms.author: sajagtap
ms.openlocfilehash: c5465a2de0924037eb93b9de652e93e85f166737
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223151"
---
# <a name="net-sdk-samples"></a>.NET SDK-minták

A következő felsorolás a .NET-hez készült Azure Content Moderator SDK használatával létrehozott kódmintákra mutató hivatkozásokat tartalmaz.

- **Segédkódtár**: [Content Moderator-ügyfél létrehozása más mintákban való használathoz](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Lásd a [rövid útmutatót](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Moderálás

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

## <a name="review"></a>Áttekintés

- **Képekkel kapcsolatos feladatok**: [Moderálási feladat indítása, amely átvizsgálást végez, és felülvizsgálatokat hoz létre](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Lásd a [rövid útmutatót](moderation-jobs-quickstart-dotnet.md).
- **Képek felülvizsgálata**: [Felülvizsgálatok létrehozása az emberi irányítás számára](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Lásd a [rövid útmutatót](moderation-reviews-quickstart-dotnet.md).
- **Videók felülvizsgálata**: [Videó-felülvizsgálatok létrehozása az emberi irányítás számára](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Lásd a [rövid útmutatót](video-reviews-quickstart-dotnet.md).
- **Videóátiratok felülvizsgálata**: [Videóátirat-felülvizsgálatok létrehozása az emberi irányítás számára](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Lásd a [rövid útmutatót](video-reviews-quickstart-dotnet.md)

Az összes .NET-minta megtekintése: [Content Moderator .NET-példák a GitHubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
