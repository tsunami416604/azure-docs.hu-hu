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
ms.openlocfilehash: a746a4baf172bcb6728c06f2a31b460139377e46
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912922"
---
# <a name="content-moderator-net-sdk-samples"></a>.NET SDK-minták Content Moderator

A következő felsorolás a .NET-hez készült Azure Content Moderator SDK használatával létrehozott kódmintákra mutató hivatkozásokat tartalmaz.

## <a name="moderation"></a>Moderálás

- **Képmoderálás** : [Felnőtteknek szóló és kényes tartalom, szöveg és arcok észlelése a képeken](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Tekintse meg a [.net SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)gyors útmutatóját.
- **Egyéni képek** : [Moderálás egyéni képlistákkal](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Tekintse meg a[.net SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)gyors útmutatóját.

> [!NOTE]
> A maximális korlát **5 képlista** , amelyek egyenként **nem haladhatják meg a 10 000 képet** .
>

- **Szöveges moderálás** : [képernyő szövege a káromkodáshoz és a személyes adatértékekhez](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Tekintse meg a[.net SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)gyors útmutatóját.
- **Egyéni kifejezések** : [Moderálás egyéni kifejezéslistákkal](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Tekintse meg a [.net SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)gyors útmutatóját.

> [!NOTE]
> A maximális korlát **5 kifejezéslista** , amelyek egyenként **nem haladhatják meg a 10 000 kifejezést** .
>

- **Videómoderálás** : [Felnőtteknek szóló és kényes tartalom észlelése a videókban, és az eredmények lekérése](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Lásd [:](video-moderation-api.md)gyors útmutató.

## <a name="review"></a>Áttekintés

- **Képekkel kapcsolatos feladatok** : [Moderálási feladat indítása, amely átvizsgálást végez, és felülvizsgálatokat hoz létre](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Lásd [:](moderation-jobs-quickstart-dotnet.md)gyors útmutató.
- **Képek felülvizsgálata** : [Felülvizsgálatok létrehozása az emberi irányítás számára](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Lásd [:](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)gyors útmutató.
- **Videók felülvizsgálata** : [Videó-felülvizsgálatok létrehozása az emberi irányítás számára](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Lásd a [rövid útmutatót](video-reviews-quickstart-dotnet.md).
- **Videóátiratok felülvizsgálata** : [Videóátirat-felülvizsgálatok létrehozása az emberi irányítás számára](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Lásd a [rövid útmutatót](video-reviews-quickstart-dotnet.md)

Az összes .NET-minta megtekintése: [Content Moderator .NET-példák a GitHubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).