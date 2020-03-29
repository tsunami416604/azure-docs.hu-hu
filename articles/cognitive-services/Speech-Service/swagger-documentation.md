---
title: Swagger dokumentáció - Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A Swagger dokumentáció használható sdk-k automatikus létrehozásához számos programozási nyelvhez. A swagger minden műveletünket támogatja.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fcc43caf895dadfaf832a47c3254f9b828bcb71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77430805"
---
# <a name="swagger-documentation"></a>A Swagger dokumentációja

A beszédfelismerési szolgáltatás egy Swagger specifikációt kínál az adatok importálásához, modellek létrehozásához, a modell pontosságának teszteléséhez, az egyéni végpontok létrehozásához, a kötegelt átírások várólistára helyezéséhez és az előfizetések kezeléséhez használt néhány REST API-k kezelésére. Az egyéni beszédportálon keresztül elérhető legtöbb művelet programozott módon hajtható végre ezekkel az API-kkal.

> [!NOTE]
> Mind a beszéd-szöveg, mind a szövegfelolvasó műveletek REST API-ként érhetők el, amelyek viszont dokumentálva vannak a Swagger specifikációban.

## <a name="generating-code-from-the-swagger-specification"></a>Kód generálása a Swagger specifikációból

A [Swagger specifikáció](https://cris.ai/swagger/ui/index) olyan lehetőségeket tartalmaz, amelyek lehetővé teszik a különböző útvonalak gyors tesztelését. Néha azonban kívánatos, hogy kódot hozzon létre az összes elérési úthoz, és hozzon létre egy híváskönyvtárat, amelya jövőbeli megoldásokat alapulhatja. Vessünk egy pillantást a folyamat egy Python-könyvtár létrehozásához.

A Swagger-t a beszédfelismerési szolgáltatás-előfizetéssel azonos régióra kell beállítania. Megerősítheti a régiót az Azure Portalon a beszédszolgáltatás-erőforrás alatt. A támogatott régiók teljes listáját a régiók című [témakörben található.](regions.md)

1. Nyissa meg a következőt: https://editor.swagger.io
2. Kattintson **a Fájl**menü **Importálás parancsára.**
3. Adja meg a swagger URL-címét, beleértve a beszédfelismerési szolgáltatás-előfizetés régióját`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Kattintson **az Ügyfél létrehozása gombra,** és válassza a Python lehetőséget.
5. Az ügyféltár mentése

Használhatja a Python-kódtár, amely a [GitHubon a beszédszolgáltatás-minták](https://aka.ms/csspeech/samples)használatával létrehozott.

## <a name="reference-docs"></a>Referenciadokumentumok

* [REST (Swagger): Kötegátírás és testreszabás](https://westus.cris.ai/swagger/ui/index)
* [REST API: Beszéd-szöveg](rest-speech-to-text.md)
* [REST API: Szövegfelolvasás](rest-text-to-speech.md)

## <a name="next-steps"></a>További lépések

* [Beszédszolgáltatás-minták a GitHubon.](https://aka.ms/csspeech/samples)
* [Ingyenes beszédfelismerési szolgáltatás-előfizetési kulcs beszerezni](get-started.md)
