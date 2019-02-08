---
title: A Custom Speech Service át beszédszolgáltatások
titlesuffix: Azure Cognitive Services
description: A Custom Speech Service most már a beszédfelismerési szolgáltatás részét képezi. Váltson a beszédfelismerési szolgáltatás számára, hogy a legújabb minőségi és frissítéseket.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 698962aa0e3d72b204c4e990aa1384b44bf3896f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55856892"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>A Custom Speech Service áttelepít a beszédszolgáltatás

Ez a cikk segítségével az alkalmazások áttelepíthetők a Custom Speech Service a Speech Service.

A Custom Speech Service most már a beszédfelismerési szolgáltatás részét képezi. Váltson a beszédfelismerési szolgáltatás számára, hogy a legújabb minőségi és frissítéseket.

## <a name="migration-for-new-customers"></a>Az új ügyfelek áttelepítése

A díjszabási modell az egyszerűbb, a beszédfelismerési szolgáltatás egy óra-alapú díjszabási modell használatával.  

1. Hol érhető el az alkalmazás minden régióban hozzon létre egy Azure-erőforrás. Az Azure-erőforrás neve **Speech**. Egyetlen Azure-erőforrás a következő szolgáltatásokat ugyanabban a régióban, ahelyett, hogy létrehozná a különálló erőforrásokat is használhatja:

    * Speech-to-text
    * Egyéni hang-szöveg
    * Szövegfelolvasás
    * Tolmácsolás

2. Töltse le a [beszéd SDK](speech-sdk.md).

3. Kövesse a gyors útmutatók és az SDK-minták a megfelelő API-k használata. A REST API-k használatakor is szeretné használni a megfelelő végpontokra és erőforrás kulcsainak.

4. A Speech Service és az API-k használata az ügyfélalkalmazás frissítése.

## <a name="migration-for-existing-customers"></a>Meglévő ügyfelek migrálása

A meglévő erőforrás kulcsainak áttelepítése a Speech Service, a beszéd portálon. Ehhez a következő lépések szükségesek:

> [!NOTE]
> Erőforrás kulcsainak csak áttelepíthetők ugyanazon a régión belül.

1. Jelentkezzen be a [cris.ai](http://www.cris.ai) portálon, és válassza a jobb felső menüjében az előfizetéshez.

2. Válassza ki **kiválasztott előfizetés áttelepítése**.

3. A mezőben adja meg az előfizetési kulcsot, és válassza ki **áttelepítése**.

## <a name="next-steps"></a>További lépések

* [Speech Service ingyenes kipróbálása](get-started.md).
* Ismerje meg, [beszédfelismerés](./speech-to-text.md) fogalmakat.

## <a name="see-also"></a>Lásd még

* [Mi az a Speech Service](overview.md)
* [Beszédszolgáltatás és az SDK-dokumentáció](speech-sdk.md#get-the-sdk)
