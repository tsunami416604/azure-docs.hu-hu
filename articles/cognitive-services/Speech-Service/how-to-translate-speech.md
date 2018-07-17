---
title: Használatával beszédszolgáltatások beszédfelismerési fordítása |} A Microsoft Docs
description: Ismerje meg, hogyan beszédalapú fordítási használja a Speech service-ben.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 6acfcf0ae8ab4c63e6cc943f93da6b947f3d118c
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071096"
---
# <a name="translate-speech-using-speech-service"></a>Beszédfelismerés, beszédfelismerési szolgáltatás használatával fordítása

A [beszéd SDK](speech-sdk.md) beszédalapú fordítási használhatja az alkalmazásban, a legegyszerűbb módja. Az SDK szolgáltatás összes funkcióját biztosítja. Az alapvető folyamat beszédalapú fordítási végrehajtásához a következő lépésekből áll:

1. Egy beszéd-előállítót hoz létre, és adja meg a Speech service előfizetési kulcs és [régió](regions.md) vagy egy engedélyezési jogkivonatot. Is konfigurálnia a forrás és cél szövegfordítási nyelv ezen a ponton, valamint hogy kívánja-e a szöveg és beszéd kimeneti.

2. Egy felismerő lekérheti az előállító. A fordítás válassza ki a fordítási felismerő. (A rendszer a többi felismerő *Speech to Text*.) Nincsenek fordítási felismerő használ hangforrásról alapján különböző változataira jellemző.

4. Események aszinkron művelethez lefoglalhatnak, ha szükséges. A felismerő meghívja az eseménykezelőket, átmeneti és végső eredményt, ha. Ellenkező esetben az alkalmazás fogad a fordítási végső eredményt.

5. Indítsa el a felismerés és a fordítás.

# <a name="sdk-samples"></a>SDK-minták

A legújabb minták, lásd: a [Cognitive Services beszédfelismerő SDK minta GitHub-adattár](https://aka.ms/csspeech/samples).

# <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [A beszédfelismerést a C#-ban](quickstart-csharp-dotnet-windows.md)
