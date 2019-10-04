---
title: Kódminták – Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: Az SDK-n keresztül használhatja a .NET-alkalmazások Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 0c7db3c48f1ff7a141fda84caaad84ac8ff7f85d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564432"
---
# <a name="content-moderator-net-sdk-samples"></a>.NET SDK-minták Content Moderator

A következő felsorolás a .NET-hez készült Azure Content Moderator SDK használatával létrehozott kódmintákra mutató hivatkozásokat tartalmaz.

## <a name="moderation"></a>Moderálás

- **Képek moderálása**: [Kiértékelheti a felnőtt és a zamatos tartalom, szöveg és arcok képét](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Lásd a [rövid útmutatót](image-moderation-quickstart-dotnet.md).
- **Egyéni lemezképek**: [Mérsékelt egyéni rendszerkép](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs)-listaokkal. Lásd a [rövid útmutatót](image-lists-quickstart-dotnet.md).

> [!NOTE]
> A maximális korlát **5 képlista**, amelyek egyenként **nem haladhatják meg a 10 000 képet**.
>

- **Szöveg moderálása**: [A trágár és a személyes](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs)adatmegjelenítést jelző szöveg. Lásd a [rövid útmutatót](text-moderation-quickstart-dotnet.md).
- **Egyéni feltételek**: [Mérsékelt, egyéni feltételekkel rendelkező listával](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Lásd a [rövid útmutatót](term-lists-quickstart-dotnet.md).

> [!NOTE]
> A maximális korlát **5 kifejezéslista**, amelyek egyenként **nem haladhatják meg a 10 000 kifejezést**.
>

- **Videó moderálása**: [Videó beolvasása a felnőtteknek és a zamatos tartalmaknak, és az eredmények](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs)beszerzése. Lásd a [rövid útmutatót](video-moderation-api.md).

## <a name="review"></a>Áttekintés

- **Rendszerkép-feladatok**: [Hozzon létre egy moderálási feladatot, amely értékeléseket vizsgál és hoz létre](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Lásd a [rövid útmutatót](moderation-jobs-quickstart-dotnet.md).
- **Rendszerkép**-felülvizsgálatok: [Hozzon létre a Human-in-the-loop értékeléseket](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Lásd a [rövid útmutatót](moderation-reviews-quickstart-dotnet.md).
- **Videós felülvizsgálatok**: [A Human-in-the-loop videós felülvizsgálatok létrehozása](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Lásd a [rövid útmutatót](video-reviews-quickstart-dotnet.md).
- **Videó átiratának felülvizsgálatai**: [A Human-in-the-loop videó-átirati felülvizsgálatok létrehozása](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) Útmutató [](video-reviews-quickstart-dotnet.md)

Az összes .NET-minta megtekintése: [Content Moderator .NET-példák a GitHubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
