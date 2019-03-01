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
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56988071"
---
## <a name="view-device-telemetry"></a>Eszköztelemetria megtekintése

Az eszközről küldött telemetriát is megtekintheti a **Device Explorer** oldal a megoldásban.

1. Válassza ki az Ön által üzembe helyezett eszközök listájában az eszközön a **Device Explorer** lapot. A panel az eszközről, mint például az eszköz telemetriai rajz információit jeleníti meg:

    ![Tekintse meg az eszköz részletei](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Válasszon **nyomás** telemetriai adatok megjelenítésének módosítása:

    ![Nyomás telemetria megtekintése](media/iot-suite-visualize-connecting/devicespressure.png)

1. Az eszköz diagnosztikai információ megtekintéséhez görgessen le a **diagnosztikai**:

    ![Eszköz diagnosztika megtekintése](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Az eszköz cselekedhet

Az eszközök metódusokat hívhat meg, használja a **Device Explorer** oldal a távoli figyelési megoldásban. Például a távoli figyelési megoldás a **hűtő** eszközök megvalósítani egy **újraindítás** metódust.

1. Válasszon **eszközök** navigálhat a **Device Explorer** oldal a megoldásban.

1. Válassza ki az Ön által üzembe helyezett eszközök listájában az eszközön a **Device Explorer** oldalon:

    ![Válassza ki a valódi eszköz](media/iot-suite-visualize-connecting/devicesselect.png)

1. A metódusok meghívása az eszközön listájának megjelenítéséhez válassza **feladatok**, majd **módszerek**. Ütemezni a feladatot több eszközön futtassa, több eszközt is kijelölhet a listában. A **feladatok** panel megjeleníti a metódus típusú közös kiválasztott összes eszközre.

1. Válassza a **újraindítás**, állítsa a feladat nevet **RebootPhysicalChiller** majd **alkalmaz**:

    ![A belső vezérlőprogram-frissítés ütemezése](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Üzenetek sorozatát eszköz kódja fut, amikor a szimulált eszközt a metódus kezeli a konzolon jeleníti meg.

> [!NOTE]
> Válassza ki a megoldás a feladat állapotának nyomon követéséhez **feladat állapotának megtekintése**.

## <a name="next-steps"></a>További lépések

A cikk [testre szabhatja a távoli figyelési megoldásgyorsító](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) néhány módon szabhatja testre a megoldásgyorsító ismerteti.