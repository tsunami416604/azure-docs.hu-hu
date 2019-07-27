---
title: Hencegő dokumentáció – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A kivágási dokumentáció használatával számos programozási nyelvhez automatikusan létrehozhat SDK-kat. A szolgáltatás minden műveletét a hencegés támogatja
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 232435a424d2461bce4598356a986473cb1d3644
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552576"
---
# <a name="swagger-documentation"></a>A Swagger dokumentációja

A Speech Services olyan hencegő specifikációt kínál, amely az adatok importálásához, a modellek létrehozásához, a modell pontosságához, az egyéni végpontok létrehozásához, a kötegelt átírások végrehajtásához és az előfizetések kezeléséhez használt REST API-k használatával kommunikál. A Custom Speech-portálon keresztül elérhető legtöbb művelet programozott módon is elvégezhető az API-k használatával.

> [!NOTE]
> A beszéd-szöveg és a szövegről beszédre irányuló műveletek egyaránt elérhetők REST API-kként, amelyek viszont a hencegés specifikációjában vannak dokumentálva.

## <a name="generating-code-from-the-swagger-specification"></a>Kód generálása a hencegés specifikációból

A [hencegés specifikációja](https://cris.ai/swagger/ui/index) olyan lehetőségeket kínál, amelyekkel gyorsan tesztelheti a különböző útvonalakat. Előfordulhat azonban, hogy minden elérési útra vonatkozóan létre kell hoznia egy kódot, így egyetlen, a jövőbeli megoldások alapjául szolgáló hívásokat hozhat létre. Vessünk egy pillantást a Python-könyvtár létrehozási folyamatára.

Be kell állítania a hencegő szolgáltatást ugyanahhoz a régióhoz, mint a beszédfelismerési szolgáltatás előfizetése. A régiót a Speech Services-erőforrás alatt lévő Azure Portal ellenőrizheti. A támogatott régiók teljes listájáért lásd: [régiók](regions.md).

1. Nyissa meg a következőt: https://editor.swagger.io
2. Kattintson a **fájl**, majd az **Importálás** elemre.
3. Adja meg a felvágási URL-címet, beleértve a Speech Services-előfizetés régióját`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Kattintson az **ügyfél** előállítása és a Python elemre.
5. Az ügyféloldali kódtár mentése

Használhatja a GitHubon a [Speech Services](https://aka.ms/csspeech/samples)-mintákkal generált Python-könyvtárat.

## <a name="reference-docs"></a>Segédanyagok

* [REST (hencegés): Kötegelt átírás és testreszabás](https://westus.cris.ai/swagger/ui/index)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Szöveg – beszéd](rest-text-to-speech.md)

## <a name="next-steps"></a>További lépések

* [Speech Services-minták a githubon](https://aka.ms/csspeech/samples).
* [Ingyenes Speech Services-előfizetési kulcs beszerzése](get-started.md)
