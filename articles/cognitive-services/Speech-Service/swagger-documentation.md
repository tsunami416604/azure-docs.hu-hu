---
title: Swagger-dokumentáció – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: A Swagger-dokumentáció használható SDK-k számos programozási nyelvet a automatikus létrehozása. A Swagger által támogatott összes műveletet a szolgáltatásban
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 04/12/2019
ms.author: erhopf
ms.openlocfilehash: 29fcbd058651c428b488f5ce1c767105cb7921a6
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59580407"
---
# <a name="swagger-documentation"></a>Swagger-dokumentáció

A beszédfelismerési szolgáltatás olyan való kommunikációhoz használt adatok importálása, modelleket hozhat létre, tesztelheti a pontosság, egyéni végpontok létrehozása, várólistára helyezését batch beszédátírás és -előfizetések kezelése a REST API-k és a Swagger-specifikációra. A Custom Speech-portál elérhető a legtöbb műveletek programozott módon az API-k használatával elvégezhető. 

> [!NOTE]
> Hang-szöveg és a szöveg-hang transzformációs műveletek támogatottak elérhető REST API-k, amelyek viszont a Swagger-specifikációra vannak dokumentálva.

## <a name="generating-code-from-the-swagger-specification"></a>A Swagger-specifikációra származó kód generálása

A [Swager specifikáció](https://cris.ai/swagger/ui/index) lehetőséget is kínál, amelyek lehetővé teszik, hogy gyorsan tesztelheti az különböző elérési utak. Azonban néha kívánatos görbékhez létrehozása egy egyetlen kódtár, hogy alapján szeretne létrehozni a jövőbeli megoldások a kódot. Vessünk egy pillantást egy Python-kódtár létrehozni a folyamatot.

Swagger beállítása és a beszédfelismerési szolgáltatás előfizetésében ugyanabban a régióban kell. Az Azure Portalon a régió alatt az beszédszolgáltatások erőforrás ellenőrizheti. Támogatott régiók teljes listáját lásd: [régiók](regions.md).

1. Nyissa meg a következőt: https://editor.swagger.io
2. Kattintson a **fájl**, majd kattintson a **importálása**
3. Adja meg a swagger URL-címet, a régió beszédszolgáltatások előfizetése `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Kattintson a **készítése ügyfél** , és válassza ki a Python
5. Az ügyféloldali kódtár mentése

Használhatja a Python-kódtár, amely akkor hozza létre a [beszédszolgáltatások példák a Githubon](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Segédanyagok

* [REST (Swagger): A Batch beszédátírási és testreszabás](https://westus.cris.ai/swagger/ui/index)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Szöveg-hang transzformációs](rest-text-to-speech.md)

## <a name="next-steps"></a>További lépések

* [Speech Services-minták a Githubon](https://aka.ms/csspeech/samples).
* [Ingyenes beszédszolgáltatások előfizetési kulcs lekérése](get-started.md)
