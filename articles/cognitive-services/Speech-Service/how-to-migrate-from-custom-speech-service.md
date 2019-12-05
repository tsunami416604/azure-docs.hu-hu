---
title: Áttelepítés Custom Speech Serviceról a Speech Service-be
titleSuffix: Azure Cognitive Services
description: A Custom Speech Service mostantól a Speech Service részét képezi. Váltson a beszédfelismerési szolgáltatásra a legújabb minőségi és szolgáltatás-frissítések kihasználása érdekében.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 9c93286329316d081f8fd99ebd360195931b7b09
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805926"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrálás a Custom Speech Serviceról a beszédfelismerési szolgáltatásba

Ebből a cikkből megtudhatja, hogyan telepíthet alkalmazásokat a Custom Speech Serviceról a Speech szolgáltatásba.

A Custom Speech Service mostantól a Speech Service részét képezi. Váltson a beszédfelismerési szolgáltatásra a legújabb minőségi és szolgáltatás-frissítések kihasználása érdekében.

## <a name="migration-for-new-customers"></a>Áttelepítés új ügyfelek számára

A díjszabási modell egyszerűbb, a beszédfelismerési szolgáltatás óradíjas díjszabási modelljét használva.  

1. Hozzon létre egy Azure-erőforrást minden olyan régióban, ahol az alkalmazás elérhető. Az Azure-Erőforrás neve **beszéd**. Különálló erőforrások létrehozása helyett egyetlen Azure-erőforrást is használhat az adott régióban lévő következő szolgáltatásokhoz:

    * Diktálás
    * Egyéni beszéd – szöveg
    * Szövegfelolvasás
    * Beszédfordítás

2. Töltse le a [SPEECH SDK](speech-sdk.md)-t.

3. A megfelelő API-k használatához kövesse a rövid útmutatók és az SDK-minták című témakört. Ha a REST API-kat használja, a megfelelő végpontokat és erőforrás-kulcsokat is használni kell.

4. Az ügyfélalkalmazás frissítése a beszédfelismerési szolgáltatás és az API-k használatára.

## <a name="migration-for-existing-customers"></a>Migrálás meglévő ügyfelek számára

Migrálja meglévő erőforrás-kulcsait a Speech szolgáltatás portálon. Ehhez a következő lépések szükségesek:

> [!NOTE]
> Az erőforrás-kulcsok csak ugyanazon a régión belül helyezhetők át.

1. Jelentkezzen be a [Cris.ai](https://cris.ai/Home/CustomSpeech) -portálra, és válassza ki az előfizetést a jobb felső menüben.

2. Válassza a **kiválasztott előfizetés áttelepíteni**lehetőséget.

3. Írja be az előfizetési kulcsot a szövegmezőbe, majd kattintson az **áttelepítés**elemre.

## <a name="next-steps"></a>Következő lépések

* [Próbálja ki ingyenesen a Speech szolgáltatást](get-started.md).
* Megtudhatja [a szöveg](./speech-to-text.md) fogalmait.

## <a name="see-also"></a>Lásd még:

* [Mi a beszédfelismerési szolgáltatás?](overview.md)
* [A Speech Service és a Speech SDK dokumentációja](speech-sdk.md#get-the-sdk)
