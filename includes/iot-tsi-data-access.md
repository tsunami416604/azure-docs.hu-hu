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
ms.openlocfilehash: c9daa86bf36b260001d9969385b9e8a98a8ac0cf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125083"
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