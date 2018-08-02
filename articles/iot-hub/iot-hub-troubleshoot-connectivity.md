---
title: Diagnosztika és hibaelhárítás bontja a kapcsolatot az Azure IoT Hub szolgáltatással
description: Ismertetjük, hogyan diagnosztizálhatja és a gyakori hibák elhárítása az eszköz csatlakoztatása az Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: eb186f4b6e1d742c9cae51e68b3d3dbda1bb751c
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39400419"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Észlelése és elhárítása bontja a kapcsolatot az Azure IoT Hub szolgáltatással

Az IoT-eszközök kapcsolati hibák elhárításához, mivel számos lehetséges meghibásodási pontok nehézkes lehet. Eszközoldali úgy az alkalmazáslogikát, fizikai hálózatokhoz, protokollok, hardver és az Azure IoT Hub összes problémákat okozhat. Ez a dokumentum javaslatok észlelése és a felhő-oldal (szemben eszközoldali) eszköz kapcsolódási problémák elhárításához nyújt.

## <a name="get-alerts-and-error-logs"></a>Riasztások és a hibanaplók

Értesítéseket kaphat, és írjon a naplókat, ha eszközkapcsolatok dobja el az Azure Monitor használatával.

### <a name="turn-on-diagnostic-logs"></a>Kapcsolja be a diagnosztikai naplók 

Eszköz kapcsolat események és hibák jelentkeznek, engedélyezze a diagnosztikát az IoT Hub. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg az IoT-központot.
1. Válassza ki **diagnosztikai beállítások**.
1. Válassza ki **diagnosztika bekapcsolása**.
1. Győződjön meg arról, hogy engedélyezi **kapcsolatok** gyűjtendő naplók. 
1. Elemzés megkönnyítése érdekében kapcsolja **Küldés a Log Analyticsnek** ([díjszabása](https://azure.microsoft.com/pricing/details/log-analytics/)). A cikk későbbi részében egy példa a Log Analytics.

   ![Ajánlott beállítások][2]

További tudnivalókért lásd: [Azure IoT Hub állapotának Monitorozásához és a problémák gyorsan diagnosztizálása](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>A csatlakoztatott eszközök száma metrikus riasztásainak beállítása

Értesítéseket kaphat, amikor az eszköz leválasztása, a riasztások beállítása a *csatlakoztatott eszközök* metrikát. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg az IoT hubnak.
1. Válassza ki **riasztások (klasszikus)**.
1. Kattintson a **metrikariasztás hozzáadása (klasszikus)**.
1. Töltse ki az űrlapot, és válassza ki a **OK**. 

   ![Metrikariasztás ajánlott][3]

További tudnivalókért lásd: [Mik azok a Microsoft Azure klasszikus riasztások?](../monitoring-and-diagnostics/monitoring-overview-alerts.md).

## <a name="resolve-common-connectivity-errors"></a>Gyakori csatlakozási hibák megoldása

Diagnosztikai naplók és riasztások csatlakoztatott eszközök vannak kapcsolva, kap riasztások történnek hibák. Ez a szakasz ismerteti a leggyakoribb hibák elhárításához, ha egy riasztás. Az alábbi lépések azt feltételezik, hogy beállította a Log Analytics a diagnosztikai naplók. 

1. Nyissa meg a munkaterület **Log Analytics** az Azure Portalon.
1. Kattintson a **naplóbeli keresés**.
1. Csatlakozási hiba naplók elkülönítése az IoT Hub, adja meg a lekérdezés a mezőbe, majd nyomja le az **futtatása**.

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Ha nincsenek eredmények, keresse meg a `OperationName`, `ResultType` (hibakód:), és `ResultDescription` (hibaüzenet) a hibát a részletekért.

   ![Példa a hibanapló][4]

1. Ez a táblázat segítségével gyakori hibák megértése és megoldása.

    | Hiba | Gyökérok | Megoldás: |
    |---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 404104 DeviceConnectionClosedRemotely | Az eszköz által a kapcsolat bezárult, de az IoT Hub miért nem ismert. Gyakori okai az AMQP és MQTT időtúllépési és az internetes kapcsolat megszakadását. | Győződjön meg arról, hogy az eszköz csatlakozhat az IoT Hub által [csatlakozási kísérlet](tutorial-connectivity.md). Ha a kapcsolat megfelelően működik, de az eszköz időnként megszakad, ügyeljen arra, hogy megfelelő életben tartási eszköz logikát az Ön által választott protokoll (mqtt-ről/AMPQ). |
    | 401003 IoTHubUnauthorized | Az IoT Hub nem sikerült hitelesíteni a kapcsolatot. | Győződjön meg arról, hogy a SAS- vagy más biztonsági jogkivonat használata nem járt le. [Az Azure IoT SDK-k](iot-hub-devguide-sdks.md) automatikusan jogkivonatokat hoz létre, anélkül, hogy a speciális konfigurációt. |
    | 409002 LinkCreationConflict | Nincsenek ugyanazon az eszközön egynél több kapcsolatot. Új kapcsolat kérelem érkezik egy eszközhöz, az IoT Hub bezárja az előzőre hiba. | Ellenőrizze, hogy ki egy új kapcsolódási kérelmet csak akkor, ha csökken a kapcsolat. |
    | 500001 Kiszolgálóhibái | Az IoT Hub hibába ütközött egy kiszolgálóoldali problémát. Az a legvalószínűbb a probléma nem átmeneti. Az IoT Hub csapat működik nehezen karbantartása során [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/), az IoT Hub csomópontok kis részhalmazainak alkalmanként tapasztalhatnak az átmeneti hibák. Ha az eszköz próbál csatlakozni a csomóponthoz, amely esetén problémák adódnak, ezt a hibaüzenetet kapja. | Az átmeneti hibák elhárításához, adjon ki egy újra az eszközről. A [automatikusan kezelheti az újrapróbálkozásokat](iot-hub-reliability-features-in-sdks.md), győződjön meg arról, hogy a legújabb verzióját használja, a [Azure IoT SDK-k](iot-hub-devguide-sdks.md).<br><br>A legjobb az átmeneti hibák kezelése és az újrapróbálkozások, lásd: [átmeneti hibák kezelése](/azure/architecture/best-practices/transient-faults.md).  <br><br>Ha az újrapróbálkozás után a probléma továbbra is fennáll, ellenőrizze [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) és [Azure állapotlapján](https://azure.microsoft.com/status/history/) az IoT Hub tartalmaz olyan ismert probléma. Ha nincs az ismert problémák, és a probléma továbbra is fennáll, [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/support/options/) további vizsgálat. |
    | 500008 GenericTimeout | Az IoT Hub időkorlátját a kapcsolódási kérelmet nem sikerült befejezni. Például 500001 Kiszolgálóhibái Ez a hiba akkor valószínűleg átmeneti. | Alapvető ok, és ez a hiba megoldásához 500001 Kiszolgálóhibái hibaelhárítási lépésekkel.|

## <a name="other-steps-to-try"></a>Egyéb lépések

Ha a fenti lépések nem oldották, Íme néhány további az alábbiakkal próbálkozhat:

* Ha rendelkezik hozzáféréssel a hibás eszközökre fizikailag vagy távoli (például SSH-), kövesse a [eszközoldali – hibaelhárítási útmutató](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) folytatja a hibaelhárítás.
* Ellenőrizze, hogy vannak-e az eszközök **engedélyezve** az Azure Portal > az IoT Hub > IoT-eszközök.
* Segítség kérése a [Azure IoT Hub fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub), vagy [az Azure-támogatás](https://azure.microsoft.com/support/options/).

Mindenki a dokumentációban javítható, hagyja az alábbi megjegyzést, ha ez az útmutató nem oldották meg.

<!-- Images -->
[1]: ../../includes/media/iot-hub-diagnostics-settings/turnondiagnostics.png
[2]: ./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png
[3]: ./media/iot-hub-troubleshoot-connectivity/metric-alert.png
[4]: ./media/iot-hub-troubleshoot-connectivity/diag-logs.png
