---
title: Távoli figyelési adatok hozzáférés-vezérlés – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan konfigurálhatja a Time Series Insights-telemetria explorer a távoli figyelési megoldásgyorsító a hozzáférés-vezérlés
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827201"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>A Time Series Insights-telemetria Explorer hozzáférés-vezérlés konfigurálása

Ez a cikk ismerteti a Time Series Insights explorer, a távoli figyelési megoldásgyorsító a hozzáférés-vezérlés konfigurálása. Ahhoz, hogy az a megoldásgyorsító felhasználóit, hogy nyissa meg a Time Series Insights Explorert, kell minden egyes felhasználó adathozzáférés biztosítása.

Az adathozzáférési házirendek a következőkhöz biztosítanak engedélyeket: adatlekérdezések kiadása, referenciaadatok módosítása a környezetben, valamint a környezethez társított mentett lekérdezések és perspektívák megosztása.

## <a name="grant-data-access"></a>Adathozzáférés biztosítása

Adathozzáférés egy felhasználó rendszerbiztonsági tag az alábbi lépéseket követve:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a Time Series Insights-környezet. Típus **Time Series** a a **keresési** mezőbe. Válassza ki **Time Series Environment** a keresési eredmények között. 

3. Válassza ki az Azure Time Series Insights környezetet a listából.

4. Válassza ki **az adathozzáférési házirendek**, majd **+ Hozzáadás**.
    ![A Time Series Insights-forrás felügyelete – környezet](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Válassza ki **felhasználó kiválasztása**.  Keresse meg a felhasználói név vagy e-mail cím a hozzáadni kívánt felhasználó található. Kattintson a **kiválasztása** a kijelölés megerősítéséhez. 

    ![A Time Series Insights-forrás felügyelete – hozzáadás](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Válassza ki **szerepkör kiválasztása**. Válassza ki a felhasználó a megfelelő hozzáférés-szerepkör:
   - Válassza ki **közreműködői** szeretné-e, hogy a felhasználó módosíthatja a referenciaadatok és megosztás a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival. 
   - Ellenkező esetben válassza **olvasó** lehetővé teszik a felhasználói adatokat lekérdezni a környezetben, és személyes (nem megosztott) lekérdezéseket mentéséhez a környezetben.

     Válassza ki **Ok** szerepkör kiválasztásának megerősítéséhez.

     ![A Time Series Insights-forrás felügyelete – felhasználó kiválasztása](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Válassza ki **Ok** a a **felhasználói szerepkör kiválasztása** lapot.

    ![A Time Series Insights-forrás felügyelete – szerepkör kiválasztása](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. A **az adathozzáférési házirendek** lap felsorolja a felhasználók és a szerepkör minden felhasználóhoz.

    ![A Time Series Insights-forrás felügyelete – eredmények](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan a Time Series Insights Explorer a távoli figyelési megoldásgyorsító a hozzáférés-vezérlést kapnak.

További elméleti kapcsolatos további információkért a távoli figyelési megoldásgyorsító: [távoli figyelési architektúrával](iot-accelerators-remote-monitoring-sample-walkthrough.md)

A távoli figyelési megoldás testreszabásával kapcsolatos további információkért lásd: [testreszabása és ismételt mikroszolgáltatások üzembe helyezése](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->