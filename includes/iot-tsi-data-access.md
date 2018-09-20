---
title: fájl belefoglalása
description: fájl belefoglalása
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: include
ms.date: 08/20/2018
ms.author: anshan
ms.custom: include file
ms.openlocfilehash: fb45ea02f365cf4e7b394e249f9b91a784e5469f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368672"
---
## <a name="grant-data-access"></a>Adathozzáférés biztosítása

Adathozzáférés egy felhasználó rendszerbiztonsági tag az alábbi lépéseket követve:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a Time Series Insights-környezet. Típus **Time Series** a a **keresési** mezőbe. Válassza ki **Time Series Environment** a keresési eredmények között. 

3. Válassza ki az Azure Time Series Insights környezetet a listából.

4. Válassza ki **az adathozzáférési házirendek**, majd **+ Hozzáadás**.
    ![A Time Series Insights-forrás felügyelete – környezet](media/iot-tsi-data-access/getstarted-grant-data-access1.png)

5. Válassza ki **felhasználó kiválasztása**.  Keresse meg a felhasználói név vagy e-mail cím a hozzáadni kívánt felhasználó található. Kattintson a **kiválasztása** a kijelölés megerősítéséhez. 

    ![A Time Series Insights-forrás felügyelete – hozzáadás](media/iot-tsi-data-access/getstarted-grant-data-access2.png)

6. Válassza ki **szerepkör kiválasztása**. Válassza ki a felhasználó a megfelelő hozzáférés-szerepkör:
    - Válassza ki **közreműködői** szeretné-e, hogy a felhasználó módosíthatja a referenciaadatok és megosztás a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival. 
    - Ellenkező esetben válassza **olvasó** lehetővé teszik a felhasználói adatokat lekérdezni a környezetben, és személyes (nem megosztott) lekérdezéseket mentéséhez a környezetben.

    Válassza ki **Ok** szerepkör kiválasztásának megerősítéséhez.

    ![A Time Series Insights-forrás felügyelete – felhasználó kiválasztása](media/iot-tsi-data-access/getstarted-grant-data-access3.png)

7. Válassza ki **Ok** a a **felhasználói szerepkör kiválasztása** lapot.

    ![A Time Series Insights-forrás felügyelete – szerepkör kiválasztása](media/iot-tsi-data-access/getstarted-grant-data-access4.png)

8. A **az adathozzáférési házirendek** lap felsorolja a felhasználók és a szerepkör minden felhasználóhoz.

    ![A Time Series Insights-forrás felügyelete – eredmények](media/iot-tsi-data-access/getstarted-grant-data-access5.png)