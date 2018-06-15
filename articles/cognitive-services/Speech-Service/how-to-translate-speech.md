---
title: Beszéd szolgáltatásokkal beszéd fordítása |} Microsoft Docs
description: Megtudhatja, hogyan beszéd fordítás használatára a beszédfelismerés szolgáltatásban.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 7f39f284998489574049d82c44b3d3a0a3797adb
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "35350010"
---
# <a name="translate-speech-using-speech-service"></a>Beszéd szolgáltatással beszéd fordítása

A [beszéd SDK](speech-sdk.md) a legegyszerűbb módja a beszédfelismerés fordítás használatára az alkalmazásban. Az SDK szolgáltatás összes funkcióját biztosítja. A folyamat alapvetően a beszédfelismerés fordítási végrehajtásához a következő lépésekből áll:

1. A beszédfelismerés létrehozni, és adja meg a beszédfelismerés szolgáltatás előfizetés kulcs vagy egy engedélyezési jogkivonatot. Is konfigurálnia a forrás és cél fordítási nyelvek ezen a ponton, valamint adja meg, hogy kívánja-e szöveg vagy beszéd kimeneti.

2. A felismerő a gyári beolvasása sikertelen. Fordítási válassza ki a fordítási felismerő. (A rendszer a többi felismerő *szöveg beszédfelismerés*.) Nincsenek fordítási felismerő használ hang adatforráson alapul, különböző változataira jellemző.

4. Lefoglalják az események aszinkron művelet, ha szükséges. A felismerő meghívja a eseménykezelők, ha átmeneti és végleges eredmények. Ellenkező esetben az alkalmazás fogad végleges fordítását eredményt.

5. Indítsa el a felismerési és a fordítás.

# <a name="sdk-samples"></a>SDK-minták

A minták legújabb készletét, tekintse meg a [kognitív szolgáltatások beszéd SDK minta GitHub-tárházban](https://aka.ms/csspeech/samples).

# <a name="next-steps"></a>További lépések

- [Beszéd próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [A C# beszéd felismerésére](quickstart-csharp-windows.md)
