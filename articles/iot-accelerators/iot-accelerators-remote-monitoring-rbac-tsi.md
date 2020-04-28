---
title: Távoli figyelés – adathozzáférés-vezérlés – Azure | Microsoft Docs
description: Ez a cikk arról nyújt tájékoztatást, hogyan konfigurálhatja a Time Series Insights telemetria Explorer hozzáférés-vezérlését a távoli figyelési megoldás gyorsító felületén
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "65827201"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>A Time Series Insights telemetria Explorer hozzáférés-vezérlésének konfigurálása

Ez a cikk azt ismerteti, hogyan konfigurálható a Time Series Insights Explorer hozzáférés-vezérlése a távoli figyelési megoldás-gyorsító szolgáltatásban. Annak engedélyezéséhez, hogy a megoldás-gyorsító felhasználók hozzáférhessenek a Time Series Insights Explorerhez, meg kell adnia az egyes felhasználói adathozzáféréseket.

Az adathozzáférési házirendek a következőkhöz biztosítanak engedélyeket: adatlekérdezések kiadása, referenciaadatok módosítása a környezetben, valamint a környezethez társított mentett lekérdezések és perspektívák megosztása.

## <a name="grant-data-access"></a>Adathozzáférés biztosítása

Az alábbi lépéseket követve biztosíthatja az adathozzáférést egy felhasználói tag számára:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg Time Series Insights-környezetét. Írja be a **keresőmezőbe** a **Time Series** kifejezést. Válassza az **idősorozat-környezet** lehetőséget a keresési eredmények között. 

3. Válassza ki az Azure Time Series Insights környezetet a listából.

4. Válassza **az adatelérési házirendek**, majd a **+ Hozzáadás**lehetőséget.
    ![A Time Series Insights-forrás felügyelete – környezet](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Válassza a **felhasználó kiválasztása**lehetőséget.  Keresse meg a felhasználónevet vagy az e-mail-címet, hogy megkeresse a hozzáadni kívánt felhasználót. Kattintson **a kijelölés** elemre a kijelölés megerősítéséhez. 

    ![A Time Series Insights-forrás felügyelete – hozzáadás](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Válassza a **szerepkör kiválasztása**lehetőséget. Válassza ki a megfelelő hozzáférési szerepkört a felhasználó számára:
   - Válassza a **közreműködő** lehetőséget, ha engedélyezni szeretné a felhasználó számára a hivatkozási adatmódosítást és a mentett lekérdezések és perspektívák megosztását a környezet más felhasználóival. 
   - Ellenkező esetben válassza az **olvasó** lehetőséget, hogy engedélyezze a felhasználói lekérdezési és a személyes (nem megosztott) lekérdezések mentését a környezetben.

     A szerepkör választásának megerősítéséhez kattintson **az OK gombra** .

     ![A Time Series Insights-forrás felügyelete – felhasználó kiválasztása](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. A **felhasználói szerepkör kiválasztása** lapon kattintson az **OK gombra** .

    ![A Time Series Insights-forrás felügyelete – szerepkör kiválasztása](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. Az **adatelérési házirendek** lap felsorolja a felhasználókat és az egyes felhasználók szerepkörét.

    ![A Time Series Insights-forrás felügyelete – eredmények](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan adható meg a hozzáférés-vezérlés a távoli figyelési megoldás-gyorsító Time Series Insights Explorer számára.

A távoli figyelési megoldás-gyorsító részletes ismertetését lásd: [távoli figyelési architektúra](iot-accelerators-remote-monitoring-sample-walkthrough.md)

A távoli figyelési megoldás testreszabásával kapcsolatos további információkért lásd: [a szolgáltatás testreszabása és újbóli üzembe helyezése](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->