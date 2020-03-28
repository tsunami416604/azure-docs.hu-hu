---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: 58122066e65fbcb02b6b4333985785b219d3dbbd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900308"
---
A szándékfelismerés iszatának befejezéséhez létre kell hoznia egy LUIS-fiókot és egy projektet a LUIS előzetes verzióportál használatával. Ez a rövid útmutató csak LUIS-előfizetést igényel. A beszédfelismerési szolgáltatás *előfizetése nem* szükséges.

Az első dolog, amit meg kell tennie, hogy hozzon létre egy LUIS-fiókot és alkalmazást a LUIS előzetes verzióportál használatával. A létrehozott LUIS-alkalmazás egy előre összeállított tartományt fog használni az otthoni automatizáláshoz, amely leképezéseket, entitásokat és példakimondott szövegeket biztosít. Ha elkészült, egy LUIS-végpont fut a felhőben, amely a beszédska használatával hívható. 

A LUIS-alkalmazás létrehozásához kövesse az alábbi utasításokat:

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Rövid útmutató: Előre összeállított tartományi alkalmazás létrehozása<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Ha végeztél, négy dologra lesz szükséged:

* Közzététel újra a **Beszéd alapozásával**
* A LUIS **elsődleges kulcsa**
* Az Ön **LUIS-helye**
* A **LUIS-alkalmazásazonosító**

Itt találja ezeket az információkat a [LUIS előnézeti portálján:](https://preview.luis.ai/)

1. A LUIS előnézeti portálján jelölje ki az alkalmazást, majd válassza a **Közzététel** gombot.

2. Válassza ki a **Termelés** helyet, `en-US` ha a **Beszédalapozás** beállítást a **Be** állásra kapcsolja. Ezután kattintson a **Közzététel** gombra.

    > [!IMPORTANT]
    > **A beszédfelismerés használata** erősen ajánlott, mivel javítja a beszédfelismerés pontosságát.

    > [!div class="mx-imgBorder"]
    > ![Luis közzététele a végponton](../../../media/luis/publish-app-popup.png)

3. A LUIS előzetes portálján válassza a **Kezelés**lehetőséget, majd válassza az **Azure Resources lehetőséget.** Ezen az oldalon megtalálja a LUIS-kulcsot és a helyet (más néven _régiót)._

   > [!div class="mx-imgBorder"]
   > ![LUIS-kulcs és hely](../../../media/luis/luis-key-region.png)

4. Miután megszerezte a kulcsot és a tartózkodási helyet, szüksége lesz az alkalmazásazonosítóra. Válassza **az Alkalmazásbeállítások** lehetőséget – az alkalmazásazonosító jave érhető el ezen az oldalon.

   > [!div class="mx-imgBorder"]
   > ![LUIS-alkalmazásazonosító](../../../media/luis/luis-app-id.png)
