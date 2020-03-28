---
title: Kódminták – Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja az Azure Cognitive Services tartalommoderátorát a .NET-alkalmazásokban az SDK-n keresztül.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 930571c841146e0b12efbf7325915ba2b23a7efa
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73744330"
---
# <a name="content-moderator-net-sdk-samples"></a>Tartalommoderátor .NET SDK-mintái

A következő felsorolás a .NET-hez készült Azure Content Moderator SDK használatával létrehozott kódmintákra mutató hivatkozásokat tartalmaz.

## <a name="moderation"></a>Moderálás

- **Képmoderálás**: [Felnőtteknek szóló és kényes tartalom, szöveg és arcok észlelése a képeken](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Tekintse meg a [.NET SDK rövid útmutatót.](dotnet-sdk-quickstart.md)
- **Egyéni képek**: [Moderálás egyéni képlistákkal](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Tekintse meg a[.NET SDK rövid útmutatót.](dotnet-sdk-quickstart.md)

> [!NOTE]
> A maximális korlát **5 képlista**, amelyek egyenként **nem haladhatják meg a 10 000 képet**.
>

- **Szövegmoderálás**: [A káromkodás és a személyes adatok képernyőszövege](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Tekintse meg a[.NET SDK rövid útmutatót.](dotnet-sdk-quickstart.md)
- **Egyéni kifejezések**: [Moderálás egyéni kifejezéslistákkal](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Tekintse meg a [.NET SDK rövid útmutatót.](dotnet-sdk-quickstart.md)

> [!NOTE]
> A maximális korlát **5 kifejezéslista**, amelyek egyenként **nem haladhatják meg a 10 000 kifejezést**.
>

- **Videómoderálás**: [Felnőtteknek szóló és kényes tartalom észlelése a videókban, és az eredmények lekérése](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Lásd [a rövid útmutatót.](video-moderation-api.md)

## <a name="review"></a>Áttekintés

- **Képekkel kapcsolatos feladatok**: [Moderálási feladat indítása, amely átvizsgálást végez, és felülvizsgálatokat hoz létre](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Lásd [a rövid útmutatót.](moderation-jobs-quickstart-dotnet.md)
- **Képek felülvizsgálata**: [Felülvizsgálatok létrehozása az emberi irányítás számára](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Lásd [a rövid útmutatót.](dotnet-sdk-quickstart.md)
- **Videók felülvizsgálata**: [Videó-felülvizsgálatok létrehozása az emberi irányítás számára](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Lásd a [rövid útmutatót](video-reviews-quickstart-dotnet.md).
- **Videóátiratok felülvizsgálata**: [Videóátirat-felülvizsgálatok létrehozása az emberi irányítás számára](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Lásd a [rövid útmutatót](video-reviews-quickstart-dotnet.md)

Az összes .NET-minta megtekintése: [Content Moderator .NET-példák a GitHubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
