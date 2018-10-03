---
title: A Custom Speech Service át beszédszolgáltatás
titlesuffix: Azure Cognitive Services
description: Ismerje meg a Custom Speech szolgáltatásból a Speech Service-be való migrálás hogyan.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 10/01/2018
ms.author: panosper
ms.openlocfilehash: b5f23cf5a9dc3a675d9103bb32f686d387fda837
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239939"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>A Custom Speech service áttelepít a beszédszolgáltatás

Ez a cikk segítségével az alkalmazások áttelepítése a Custom Speech service-ből a Speech Service.

A Custom Speech Service most már a beszédfelismerési szolgáltatás részét képezi. Váltson a beszédfelismerési szolgáltatás számára, hogy a legújabb minőségi és frissítéseket.
 
## <a name="migration-for-new-customers"></a>Az új ügyfelek áttelepítése

A díjszabási modell az egyszerűbb, áthelyezése egy óra-alapú díjszabási modellre a beszédfelismerési szolgáltatás.   

1. Hol érhető el az alkalmazás minden régióban hozzon létre egy Azure-erőforrás. Az Azure-erőforrás neve **Speech**. Egyetlen Azure-erőforrás a következő szolgáltatásokat ugyanabban a régióban, ahelyett, hogy létrehozná a különálló erőforrásokat is használhatja:

    * Hang-szöveg
    * Egyéni hang-szöveg
    * Szöveg-hang transzformációs
    * Tolmácsolás

2. Töltse le a [beszéd SDK](speech-sdk.md). 

3. Kövesse a gyors útmutatók és az SDK-minták a megfelelő API-k használata. A REST API-k használatakor is szeretné használni a megfelelő végpontokra és erőforrás kulcsainak. 

4. A Speech service és az API-k használata az ügyfélalkalmazás frissítése. 

> [!NOTE]
> * A LUIS - Ha engedélyezte a beszéd, a Language Understanding (LUIS), ugyanabban a régióban egy LUIS erőforrást fog működni a LUIS, valamint a beszédszolgáltatások. Lásd: [ismeri fel a speech leképezések](how-to-recognize-intents-from-speech-csharp.md) dokumentációját.
> * Szöveg szövegfordítás nem szerepel a Speech service. A saját Azure-erőforrás-előfizetés szükséges.
  


## <a name="migration-for-existing-customers"></a>Meglévő ügyfelek migrálása

Meglévő ügyfeleink van szükség a meglévő erőforrás kulcsainak áttelepítése az új szolgáltatást, a beszéd portálon. Ehhez a következő lépések szükségesek: 

> [!NOTE] 
> Erőforrás kulcsainak csak áttelepíthetők ugyanazon a régión belül. 

1. Jelentkezzen be a [cris.ai](http://www.cris.ai) portálon, és válassza ki az előfizetést, a jobb felső menüben. 

2. Válassza ki **kiválasztott előfizetés áttelepítése**.

3. A mezőben adja meg az előfizetési kulcsot, és válassza ki **áttelepítése**.

    ![Adja meg az előfizetés áttelepítése](./media/migrate-from-custom-speech-service/migrate-dialog.png)

## <a name="next-steps"></a>További lépések

* [Speech Service ingyenes kipróbálása](get-started.md)
* Ismerje meg, [beszédfelismerés](./speech-to-text.md) fogalmak

## <a name="see-also"></a>Lásd még

* [Mi az a Speech service](overview.md)
* [Beszédszolgáltatás és az SDK-dokumentáció](speech-sdk.md#get-the-sdk)