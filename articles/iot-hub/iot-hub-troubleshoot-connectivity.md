---
title: A kapcsolatbontások figyelése és hibaelhárítása az Azure IoT Hubbal
description: Ismerje meg az Azure IoT Hub eszközkapcsolatával kapcsolatos gyakori hibák figyelését és elhárítását
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 82139eef9708ff8d76e1087c71aa5445ba898385
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759613"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Az Azure IoT Hubbal való kapcsolatbontások figyelése, diagnosztizálása és hibaelhárítása

Az IoT-eszközök kapcsolódási problémáit nehéz lehet elhárítani, mivel sok lehetséges hibapont van. Az alkalmazáslogika, a fizikai hálózatok, a protokollok, a hardverek, az IoT Hub és más felhőszolgáltatások mind problémákat okozhatnak. A probléma forrásának észlelése és pontosítása kritikus fontosságú. Egy nagy méretű IoT-megoldás azonban több ezer eszközzel rendelkezhet, ezért nem célszerű manuálisan ellenőrizni az egyes eszközöket. A problémák nagy méretekben történő észleléséhez, diagnosztizálásához és elhárításához használja az IoT Hub által az Azure Monitoron keresztül biztosított figyelési képességeket. Ezek a képességek csak arra korlátozódnak, amit az IoT Hub megfigyelhet, ezért azt is javasoljuk, hogy kövesse az eszközök és más Azure-szolgáltatások ajánlott eljárások figyelését.

## <a name="get-alerts-and-error-logs"></a>Értesítések és hibanaplók beszerezni

Az Azure Monitor használatával értesítéseket kaphat, és naplókat írhat, amikor az eszközök bontják a kapcsolatot.

### <a name="turn-on-diagnostic-logs"></a>Diagnosztikai naplók bekapcsolása

Eszközkapcsolati események és -hibák naplózásához kapcsolja be az IoT Hub diagnosztikájának bekapcsolását. Javasoljuk, hogy a lehető leghamarabb kapcsolja be ezeket a naplókat, mert ha a diagnosztikai naplók nincsenek engedélyezve, amikor az eszköz bontja a kapcsolatot, akkor nem lesz semmilyen információ a probléma elhárításához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Tallózással az IoT-központ.

3. Válassza **a Diagnosztikai beállítások lehetőséget**.

4. Válassza **a Diagnosztika bekapcsolása**lehetőséget.

5. A **kapcsolatok** naplóinak gyűjtését engedélyezze.

6. A könnyebb elemzés érdekében kapcsolja be a **Küldés a Log Analytics szolgáltatásba** [(lásd: díjszabás](https://azure.microsoft.com/pricing/details/log-analytics/)). Lásd a példát a [Kapcsolódási hibák megoldása csoportban.](#resolve-connectivity-errors)

   ![Ajánlott beállítások](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

További információ: [Az Azure IoT Hub állapotának figyelése és a problémák gyors diagnosztizálása](iot-hub-monitor-resource-health.md)című témakörben olvashat.

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Riasztások beállítása az eszköz bontásához nagy méretekben

Ha riasztásokat szeretne kapni, amikor az eszközök bontják a kapcsolatot, konfigurálja a riasztásokat a **Csatlakoztatott eszközök (előzetes verzió)** mérőszámon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Tallózással az IoT-központ.

3. Válassza **a Riasztások lehetőséget.**

4. Válassza az **Új riasztási szabály** lehetőséget.

5. Válassza **a Feltétel hozzáadása**lehetőséget, majd válassza a "Csatlakoztatott eszközök (előnézet)" lehetőséget.

6. Állítsa be a küszöbértéket és a riasztást a következő utasításokat követve.

További információ: [Mik a riasztások a Microsoft Azure-ban?](../azure-monitor/platform/alerts-overview.md)

#### <a name="detecting-individual-device-disconnects"></a>Az egyes eszközök leválasztásának észlelése

Az *eszközönkénti* kapcsolatbontások észleléséhez, például ha tudnia kell, hogy egy gyár offline állapotba került, [konfigurálja az eszközleválasztó eseményeket az Event Grid segítségével.](iot-hub-event-grid.md)

## <a name="resolve-connectivity-errors"></a>Kapcsolódási hibák megoldása

Amikor bekapcsolja a csatlakoztatott eszközök diagnosztikai naplóit és riasztásait, hibák esetén riasztásokjelennek meg. Ez a szakasz azt ismerteti, hogyan keresheti meg a gyakori problémákat, amikor riasztást kap. Az alábbi lépések feltételezik, hogy beállította az Azure Monitor naplók at a diagnosztikai naplók.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Tallózással az IoT-központ.

1. Válassza **a Naplók lehetőséget.**

1. Az IoT Hub kapcsolódási hibanaplóinak elkülönítéséhez írja be a következő lekérdezést, majd válassza a **Futtatás lehetőséget:**

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Ha vannak eredmények, `OperationName`keresse `ResultType` meg a `ResultDescription` , (hibakód) és (hibaüzenet), hogy részletesebben a hibát.

   ![Példa hibanaplóra](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. A leggyakoribb hibákat a problémamegoldási útmutatókban kövesse:

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Próbáltam a lép, de ők didnt' dolgozik

Ha az előző lépések nem segítettek, próbálkozzon a következőkkal:

* Ha fizikailag vagy távolról is hozzáfér a problémás eszközökhöz (például az SSH-hoz), kövesse az [eszközoldali hibaelhárítási útmutatót](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) a hibaelhárítás folytatásához.

* Ellenőrizze, hogy az eszközök **engedélyezve** vannak-e az Azure Portalon, > az IoT-központ > IoT-eszközök.

* Ha a készülék MQTT protokollt használ, ellenőrizze, hogy a 8883-as port nyitva van-e. További információ: [Csatlakozás az IoT Hubhoz (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

* Segítséget kaphat az [Azure IoT Hub fórumáról,](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub) [a Veremtúlcsordulásról](https://stackoverflow.com/questions/tagged/azure-iot-hub)vagy az [Azure-támogatásról.](https://azure.microsoft.com/support/options/)

A dokumentáció mindenki számára elérhető javításához hagyjon megjegyzést az alábbi visszajelzési szakaszban, ha ez az útmutató nem segít.

## <a name="next-steps"></a>További lépések

* Az átmeneti problémák megoldásáról az [Átmeneti hibakezelés](/azure/architecture/best-practices/transient-faults)című témakörben olvashat bővebben.

* Ha többet szeretne megtudni az Azure IoT SDK-ról és az újrapróbálkozások kezeléséről, olvassa el [a kapcsolat és a megbízható üzenetküldés kezelése az Azure IoT Hub sdk-k használatával.](iot-hub-reliability-features-in-sdks.md#connection-and-retry)
