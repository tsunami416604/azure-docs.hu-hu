---
title: A Custom Speech Service át beszédszolgáltatások
titlesuffix: Azure Cognitive Services
description: A Custom Speech Service most már a beszédszolgáltatások részét képezi. Váltson a Speech Services számára, hogy a legújabb minőségi és frissítéseket.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 8a2c149faa0ec9d135713a123a33d7c220522496
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58496003"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>A Custom Speech Service áttelepít a beszédszolgáltatás

Ez a cikk segítségével az alkalmazások áttelepíthetők a Custom Speech Service a Speech Service.

A Custom Speech Service most már a beszédfelismerési szolgáltatás részét képezi. Váltson a Speech Services számára, hogy a legújabb minőségi és frissítéseket.

## <a name="migration-for-new-customers"></a>Az új ügyfelek áttelepítése

A díjszabási modell az egyszerűbb, a beszédfelismerési szolgáltatás egy óra-alapú díjszabási modell használatával.  

1. Hol érhető el az alkalmazás minden régióban hozzon létre egy Azure-erőforrás. Az Azure-erőforrás neve **Speech**. Egyetlen Azure-erőforrás a következő szolgáltatásokat ugyanabban a régióban, ahelyett, hogy létrehozná a különálló erőforrásokat is használhatja:

    * Speech-to-text
    * Egyéni hang-szöveg
    * Szövegfelolvasás
    * Tolmácsolás

2. Töltse le a [beszéd SDK](speech-sdk.md).

3. Kövesse a gyors útmutatók és az SDK-minták a megfelelő API-k használata. A REST API-k használatakor is szeretné használni a megfelelő végpontokra és erőforrás kulcsainak.

4. A Speech Services és API-k használata az ügyfélalkalmazás frissítése.

## <a name="migration-for-existing-customers"></a>Meglévő ügyfelek migrálása

A meglévő erőforrás kulcsainak áttelepítése a beszédszolgáltatások a Speech Services portálon. Ehhez a következő lépések szükségesek:

> [!NOTE]
> Erőforrás kulcsainak csak áttelepíthetők ugyanazon a régión belül.

1. Jelentkezzen be a [cris.ai](https://cris.ai/Home/CustomSpeech) portálon, és válassza a jobb felső menüjében az előfizetéshez.

2. Válassza ki **kiválasztott előfizetés áttelepítése**.

3. A mezőben adja meg az előfizetési kulcsot, és válassza ki **áttelepítése**.

## <a name="next-steps"></a>További lépések

* [Ingyenes kipróbálása beszédszolgáltatások](get-started.md).
* Ismerje meg, [beszédfelismerés](./speech-to-text.md) fogalmakat.

## <a name="see-also"></a>Lásd még

* [Mi az a Speech Service](overview.md)
* [Beszédszolgáltatások és a Speech SDK dokumentációja](speech-sdk.md#get-the-sdk)
