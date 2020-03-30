---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179553"
---
## <a name="view-device-telemetry"></a>Eszköztelemetria megtekintése

Megtekintheti az eszközről küldött telemetriai adatokat a megoldás **Eszközkezelő** lapján.

1. Válassza ki a kiépített eszközt az **Eszközkezelő** lapon található eszközök listájában. A panel megjeleníti az eszközadatait, beleértve az eszköz telemetriai adatait is:

    ![Az eszköz részleteinek megtekintése](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Válassza a **Nyomás** lehetőséget a telemetriai megjelenítés módosításához:

    ![Nyomástelemetria megtekintése](media/iot-suite-visualize-connecting/devicespressure.png)

1. Az eszközdiagnosztikai információk megtekintéséhez görgessen le a **Diagnosztika lapra:**

    ![Eszközdiagnosztika megtekintése](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Cselekedj a készüléken

Metódusok meghívásához használja az **Eszközkezelő** lapot a Távoli figyelési megoldásban. Például a távoli figyelési megoldás **hűtő eszközök** végre egy **újraindítási** módszer.

1. Válassza **az Eszközök** lehetőséget a megoldás **Eszközkezelő** lapjára való navigáláshoz.

1. Válassza ki a kiépített eszközt az **Eszközkezelő** lapon található eszközök listájában:

    ![Válassza ki a valódi eszközét](media/iot-suite-visualize-connecting/devicesselect.png)

1. Az eszközön hívható módszerek listájának megjelenítéséhez válassza a **Feladatok**, majd **a Metódusok lehetőséget.** Ha egy feladatot több eszközön szeretne futtatni, több eszközt is kijelölhet a listában. A **Feladatok** panel en látható, hogy milyen típusú módszerek közösek az összes kiválasztott eszközön.

1. Válassza **az Újraindítás**lehetőséget, állítsa a feladat nevét **RebootPhysicalChiller-re,** majd válassza **az Alkalmaz**lehetőséget:

    ![A belső vezérlőprogram frissítésének ütemezése](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Az eszközkódot futtató konzolon üzenetsorozat jelenik meg, miközben a szimulált eszköz kezeli a metódust.

> [!NOTE]
> A feladat állapotának nyomon követéséhez a megoldásban válassza a **Feladat állapotának megtekintése**lehetőséget.

## <a name="next-steps"></a>További lépések

A [Távoli figyelési megoldás gyorsítójának testreszabása](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) című cikk a megoldásgyorsító testreszabásának néhány módját ismerteti.