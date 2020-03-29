---
title: Távfigyelési adatelérés-vezérlés - Azure | Microsoft dokumentumok
description: Ez a cikk arról nyújt tájékoztatást, hogyan konfigurálhatja a hozzáférés-vezérlést a Time Series Insights telemetriai explorerhez a Távfigyelési megoldás gyorsítójában
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65827201"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Hozzáférés-vezérlők konfigurálása a Time Series Insights telemetriai kezelőjéhez

Ez a cikk arról nyújt tájékoztatást, hogyan konfigurálhatja a hozzáférés-vezérlést a Time Series Insights-kezelőhöz a Távfigyelési megoldás gyorsítójában. Ahhoz, hogy a megoldásgyorsító felhasználói hozzáférhessenek a Time Series Insights-kezelőhöz, minden felhasználói adathozzáférést meg kell adnia.

Az adathozzáférési házirendek a következőkhöz biztosítanak engedélyeket: adatlekérdezések kiadása, referenciaadatok módosítása a környezetben, valamint a környezethez társított mentett lekérdezések és perspektívák megosztása.

## <a name="grant-data-access"></a>Adathozzáférés biztosítása

Az alábbi lépésekkel biztosíthat adathozzáférést egy egyszerű felhasználó számára:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Keresse meg a Time Series Insights környezetben. Írja be az **Idősorozat kifejezést** a **keresőmezőbe.** A keresési eredmények között válassza a **Time Series Environment lehetőséget.** 

3. Válassza ki az Azure Time Series Insights környezetet a listából.

4. Válassza **az Adatelérési házirendek**lehetőséget, majd a **+ Hozzáadás**lehetőséget.
    ![A Time Series Insights-forrás felügyelete – környezet](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Válassza **a Felhasználó kiválasztása**lehetőséget.  Keresse meg a felhasználónevet vagy az e-mail címet a hozzáadni kívánt felhasználó megkereséséhez. A kijelölés megerősítéséhez kattintson a **Kijelölés** gombra. 

    ![A Time Series Insights-forrás felügyelete – hozzáadás](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Válassza **a Szerepkör kiválasztása lehetőséget.** Válassza ki a felhasználó számára megfelelő hozzáférési szerepkört:
   - Válassza **a Közreműködő** lehetőséget, ha engedélyezni szeretné, hogy a felhasználó módosítsa a referenciaadatokat, és megossza a mentett lekérdezéseket és perspektívákat a környezet többi felhasználójával. 
   - Ellenkező esetben válassza a **Reader** lehetőséget a felhasználói lekérdezési adatok engedélyezéséhez a környezetben, és mentse a személyes (nem megosztott) lekérdezéseket a környezetben.

     **Válassza az Ok gombot** a szerepkör választásának megerősítéséhez.

     ![A Time Series Insights-forrás felügyelete – felhasználó kiválasztása](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. A Felhasználói **szerepkör kiválasztása** lapon válassza az **Ok** gombot.

    ![A Time Series Insights-forrás felügyelete – szerepkör kiválasztása](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. Az **Adatelérési házirendek** lap felsorolja a felhasználókat és az egyes felhasználók szerepköreit.

    ![A Time Series Insights-forrás felügyelete – eredmények](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan hozzáférés-vezérlések vannak megadva a Time Series Insights explorer a távoli figyelési megoldás gyorsító.

A távfigyelési megoldásgyorsítóval kapcsolatos további információkért lásd: [Távoli figyelési architektúra](iot-accelerators-remote-monitoring-sample-walkthrough.md)

A távfigyelési megoldás testreszabásáról a [Mikroszolgáltatás testreszabása és újratelepítése](iot-accelerators-microservices-example.md) című témakörben talál további információt.
<!-- Next tutorials in the sequence -->