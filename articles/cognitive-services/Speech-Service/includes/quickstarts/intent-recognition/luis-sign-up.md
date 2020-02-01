---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: 58122066e65fbcb02b6b4333985785b219d3dbbd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900308"
---
A szándék-felismerési útmutató befejezéséhez létre kell hoznia egy LUIS-fiókot és egy projektet a LUIS betekintő portál használatával. Ehhez a rövid útmutatóhoz csak LUIS-előfizetés szükséges. *Nincs* szükség beszédfelismerési szolgáltatás előfizetésére.

Első lépésként létre kell hoznia egy LUIS-fiókot és-alkalmazást a LUIS betekintő portál használatával. A létrehozott LUIS-alkalmazás egy előre elkészített tartományt fog használni a Home Automation számára, amely a leképezéseket, az entitásokat és a példa hosszúságú kimondott szöveg biztosítja. Ha elkészült, egy, a felhőben futó LUIS-végponttal fog rendelkezni, amely a Speech SDK használatával hívható meg. 

A LUIS-alkalmazás létrehozásához kövesse az alábbi utasításokat:

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Gyors útmutató: előre elkészített tartományi alkalmazás készítése<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Ha elkészült, négy dolgot kell megadnia:

* Újbóli közzététel a **beszédfelismerési alapozóval**
* A LUIS **elsődleges kulcsa**
* A LUIS **helye**
* A LUIS- **alkalmazás azonosítója**

Itt találja ezt az információt a [Luis betekintő portálon](https://preview.luis.ai/):

1. A LUIS betekintő portálon válassza ki az alkalmazást, majd kattintson a **Közzététel** gombra.

2. Válassza ki az **üzemi** tárolóhelyet, ha `en-US` használja a **beszédfelismerési** alapbeállítást a **be** állásba. Ezután kattintson a **Közzététel** gombra.

    > [!IMPORTANT]
    > A **beszédfelismerési** alapértékek erősen ajánlottak, mivel ez javítja a beszédfelismerés pontosságát.

    > [!div class="mx-imgBorder"]
    > ![LUIS közzététele a végponton](../../../media/luis/publish-app-popup.png)

3. A LUIS betekintő portálon válassza a **kezelés**, majd az **Azure-erőforrások**lehetőséget. Ezen az oldalon megtalálja a LUIS-kulcsot és-helyet (más néven _régiót_).

   > [!div class="mx-imgBorder"]
   > ![LUIS-kulcs és-hely](../../../media/luis/luis-key-region.png)

4. Miután megkapta a kulcsot és a helyet, szüksége lesz az alkalmazás AZONOSÍTÓJÁRA. **Alkalmazás-beállítások** kiválasztása – az alkalmazás azonosítója elérhető ezen az oldalon.

   > [!div class="mx-imgBorder"]
   > ![LUIS-alkalmazás azonosítója](../../../media/luis/luis-app-id.png)
