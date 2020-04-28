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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67179553"
---
## <a name="view-device-telemetry"></a>Eszköztelemetria megtekintése

A megoldás **Device Explorer** lapján megtekintheti az eszközről eljuttatott telemetria.

1. Válassza ki az eszközök listájában kiépített eszközt a **Device Explorer** oldalon. A panel az eszközre vonatkozó információkat jeleníti meg, beleértve az eszköz telemetria:

    ![Az eszköz részleteinek megjelenítése](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Válassza a **nyomást** a telemetria megjelenítésének módosításához:

    ![Nyomás telemetria megtekintése](media/iot-suite-visualize-connecting/devicespressure.png)

1. Az eszközre vonatkozó diagnosztikai információk megtekintéséhez görgessen le a **diagnosztika**:

    ![Eszközdiagnosztika megtekintése](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Az eszközön való műveletek

Ha metódusokat szeretne meghívni az eszközökön, használja a távoli figyelési megoldás **Device Explorer** lapját. A távoli figyelési megoldásban például a **Chiller** -eszközök **Újraindítási** módszert alkalmaznak.

1. Válassza az **eszközök** lehetőséget, hogy a megoldás **Device Explorer** lapjára navigáljon.

1. Válassza ki az eszközök listájában kiépített eszközt a **Device Explorer** oldalon:

    ![Válassza ki a valódi eszközét](media/iot-suite-visualize-connecting/devicesselect.png)

1. Az eszközön hívható metódusok listájának megjelenítéséhez válassza a **feladatok**, majd a **metódusok**lehetőséget. Ha több eszközön szeretne futtatni egy feladatot, több eszközt is kijelölhet a listában. A **feladatok** panel megjeleníti az összes kiválasztott eszköz közös metódusának típusait.

1. Válassza az **Újraindítás**lehetőséget, állítsa be a **RebootPhysicalChiller** nevet, majd válassza az **alkalmaz**lehetőséget:

    ![A belső vezérlőprogram frissítésének ütemezett időpontja](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Az üzenetek egy sora az eszköz kódját futtató konzolon jelenik meg, miközben a szimulált eszköz kezeli a metódust.

> [!NOTE]
> A megoldásban a feladatok állapotának nyomon követéséhez válassza a **feladatok állapotának megtekintése**lehetőséget.

## <a name="next-steps"></a>További lépések

A [távoli figyelési megoldáshoz tartozó gyorsító](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) cikk a megoldás-gyorsító testreszabásának néhány módját ismerteti.