---
title: Kódminták – Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható az Azure Cognitive Services Content Moderator a .NET-alkalmazásokban az SDK-n keresztül.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 930571c841146e0b12efbf7325915ba2b23a7efa
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "73744330"
---
# <a name="content-moderator-net-sdk-samples"></a>.NET SDK-minták Content Moderator

A következő felsorolás a .NET-hez készült Azure Content Moderator SDK használatával létrehozott kódmintákra mutató hivatkozásokat tartalmaz.

## <a name="moderation"></a>Moderálás

- **Képmoderálás**: [Felnőtteknek szóló és kényes tartalom, szöveg és arcok észlelése a képeken](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Tekintse meg a [.net SDK](dotnet-sdk-quickstart.md)gyors útmutatóját.
- **Egyéni képek**: [Moderálás egyéni képlistákkal](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Tekintse meg a[.net SDK](dotnet-sdk-quickstart.md)gyors útmutatóját.

> [!NOTE]
> A maximális korlát **5 képlista**, amelyek egyenként **nem haladhatják meg a 10 000 képet**.
>

- **Szöveges moderálás**: [képernyő szövege a káromkodáshoz és a személyes adatértékekhez](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Tekintse meg a[.net SDK](dotnet-sdk-quickstart.md)gyors útmutatóját.
- **Egyéni kifejezések**: [Moderálás egyéni kifejezéslistákkal](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Tekintse meg a [.net SDK](dotnet-sdk-quickstart.md)gyors útmutatóját.

> [!NOTE]
> A maximális korlát **5 kifejezéslista**, amelyek egyenként **nem haladhatják meg a 10 000 kifejezést**.
>

- **Videómoderálás**: [Felnőtteknek szóló és kényes tartalom észlelése a videókban, és az eredmények lekérése](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Lásd [:](video-moderation-api.md)gyors útmutató.

## <a name="review"></a>Áttekintés

- **Képekkel kapcsolatos feladatok**: [Moderálási feladat indítása, amely átvizsgálást végez, és felülvizsgálatokat hoz létre](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Lásd [:](moderation-jobs-quickstart-dotnet.md)gyors útmutató.
- **Képek felülvizsgálata**: [Felülvizsgálatok létrehozása az emberi irányítás számára](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Lásd [:](dotnet-sdk-quickstart.md)gyors útmutató.
- **Videók felülvizsgálata**: [Videó-felülvizsgálatok létrehozása az emberi irányítás számára](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Lásd a [rövid útmutatót](video-reviews-quickstart-dotnet.md).
- **Videóátiratok felülvizsgálata**: [Videóátirat-felülvizsgálatok létrehozása az emberi irányítás számára](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Lásd a [rövid útmutatót](video-reviews-quickstart-dotnet.md)

Az összes .NET-minta megtekintése: [Content Moderator .NET-példák a GitHubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
