---
title: Az Azure IoT Hub-kapcsolatának diagnosztizálása és megoldása
description: Megtudhatja, hogyan diagnosztizálhatja és elháríthatja az Azure-IoT Hub eszköz-kapcsolattal kapcsolatos gyakori hibákat
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: 3904c6390cfe8de197bae470c4ae32d22605ae6a
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801427"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Az Azure IoT Hub-vel való leválasztások észlelése és megoldása

A IoT-eszközök kapcsolódási problémái nehézkesek lehetnek a hibaelhárításhoz, mert számos lehetséges meghibásodási pont áll rendelkezésre. Az eszközökön belüli alkalmazás-logika, a fizikai hálózatok, a protokollok, a hardverek és az Azure-IoT Hub problémákhoz vezethetnek. Ez a cikk a felhőből származó eszközök csatlakozási problémáinak észlelésével és hibaelhárításával kapcsolatos javaslatokat tartalmaz (az eszköz oldalára nem vonatkozik).

## <a name="get-alerts-and-error-logs"></a>Riasztások és hibanapló beolvasása

A Azure Monitor használatával riasztásokat és írási naplókat kaphat, amikor az eszközök kapcsolatai csökkennek.

### <a name="turn-on-diagnostic-logs"></a>Kapcsolja be a diagnosztikai naplók

Az eszköz kapcsolódási eseményeinek és hibáinak naplózásához kapcsolja be a diagnosztikát a IoT Hubhoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg az IoT hubot.

3. Válassza a **diagnosztikai beállítások**lehetőséget.

4. Válassza ki **diagnosztika bekapcsolása**.

5. A **kapcsolatok** naplóinak begyűjtésének engedélyezése.

6. A könnyebb elemzés érdekében kapcsolja be a **küldés log Analytics** ([lásd a díjszabást](https://azure.microsoft.com/pricing/details/log-analytics/)). A [kapcsolódási hibák elhárítása](#resolve-connectivity-errors)című témakörben talál példát.

   ![Ajánlott beállítások](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

További információ: [Az Azure IoT hub állapotának monitorozása és a problémák gyors diagnosztizálása](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-_connected-devices_-count-metric"></a>Riasztások beállítása a _csatlakoztatott eszközök_ száma metrika

Ha riasztásokat szeretne kapni az eszközök leválasztásakor, konfigurálja a riasztásokat a **csatlakoztatott eszközök (előzetes verzió)** metrikában.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg az IoT hubot.

3. Válassza a **riasztások**lehetőséget.

4. Válassza az **új riasztási szabály**lehetőséget.

5. Válassza a **feltétel hozzáadása**, majd a csatlakoztatott eszközök (előzetes verzió) lehetőséget.

6. Az utasításokat követve fejezze be a kívánt küszöbértékek és riasztási beállítások beállítását.

További információ: [Mi a klasszikus riasztás a Microsoft Azureban?](../azure-monitor/platform/alerts-overview.md).

## <a name="resolve-connectivity-errors"></a>Csatlakozási hibák elhárítása

Ha bekapcsolja a diagnosztikai naplókat és riasztásokat a csatlakoztatott eszközökhöz, riasztást kap, ha hiba történik. Ez a szakasz azt ismerteti, Hogyan oldhatók fel a riasztások a gyakori problémák. Az alábbi lépések feltételezik, hogy beállította Azure Monitor naplókat a diagnosztikai naplókhoz.

1. Nyissa meg a munkaterületet a Azure Portal **log Analyticséhez** .

2. Válassza a **naplóbeli keresés**lehetőséget.

3. Ha el szeretné különíteni a IoT Hub csatlakozási naplófájljait, adja meg a következő lekérdezést, majd válassza a **Futtatás**lehetőséget:

    ```kusto
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Ha vannak találatok, keresse `OperationName` `ResultType` meg a (hibakód) és `ResultDescription` a (hibaüzenet), hogy további részleteket kapjon a hibáról.

   ![Hibanapló – példa](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

2. Ez a táblázat a gyakori hibák megismerésére és megoldására használható.

    | Hiba | Gyökérok | Megoldás: |
    |-------|------------|------------|
    | 404104 DeviceConnectionClosedRemotely | Az eszköz lezárta a kapcsolatokat, de IoT Hub nem tudja, miért. Gyakori okok például a MQTT/AMQP időtúllépés és az internetkapcsolat elvesztése. | Ellenőrizze, hogy az eszköz tud-e csatlakozni a IoT Hubhoz [a kapcsolat tesztelésével](tutorial-connectivity.md). Ha a kapcsolat rendben van, de az eszköz időnként megszakítja a kapcsolódást, győződjön meg arról, hogy a választott protokoll (MQTT/AMPQ) megfelelő Keep Alive-eszköz logikáját alkalmazza. |
    | 401003 IoTHubUnauthorized | A IoT Hub nem tudta hitelesíteni a kapcsolatokat. | Győződjön meg arról, hogy a használt SAS vagy más biztonsági jogkivonat nem járt le. Az [Azure IoT SDK](iot-hub-devguide-sdks.md) -k automatikusan állítanak elő tokeneket speciális konfiguráció nélkül. |
    | 409002 LinkCreationConflict | Az eszközök több kapcsolatban állnak egymással. Ha egy eszközhöz új kapcsolódási kérelem érkezik, IoT Hub bezárja az előzőt ezzel a hibával. | A leggyakoribb esetben az eszköz leválasztást észlel, és megpróbálja helyreállítani a kapcsolatot, de IoT Hub továbbra is figyelembe veszi az eszköz csatlakoztatását. IoT Hub bezárja az előző kapcsolatokat, és naplózza ezt a hibát. Ez a hiba általában egy másik átmeneti probléma mellékhatása jelenik meg, ezért a naplók további hibáit a további hibaelhárítás érdekében keresse. Ellenkező esetben ne felejtsen el új kapcsolódási kérelmet kiadni, ha a kapcsolódás elesik. |
    | 500001 ServerError | A IoT Hub egy kiszolgálóoldali hibába ütközött. Legvalószínűbb, hogy a probléma átmeneti jellegű. Habár a IoT Hub csapat keményen dolgozik [Az SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/)fenntartásában, IoT hub csomópontok kis részhalmaza időnként átmeneti hibákat tapasztalhat. Ha az eszköz egy problémával rendelkező csomóponthoz próbál csatlakozni, ezt a hibaüzenetet kapja. | Az átmeneti hibák enyhítéséhez adjon meg egy újrapróbálkozást az eszközről. Az [újrapróbálkozások automatikus kezeléséhez](iot-hub-reliability-features-in-sdks.md#connection-and-retry)győződjön meg arról, hogy az [Azure IoT SDK](iot-hub-devguide-sdks.md)-k legújabb verzióját használja.<br><br>Az átmeneti hibák kezelésével és az újrapróbálkozásokkal kapcsolatos ajánlott eljárásokért lásd: [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults).  <br><br>Ha a probléma az újrapróbálkozások után sem szűnik meg, tekintse meg [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) és az [Azure állapotát](https://azure.microsoft.com/status/history/) , és ellenőrizze, hogy az IoT hub ismert problémával rendelkezik-e. Ha nincs ismert probléma, és a probléma továbbra is fennáll, [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/support/options/) a további vizsgálathoz. |
    | 500008 GenericTimeout | A IoT Hub időtúllépés előtt nem tudta befejezni a kapcsolatkérelem-kérést. A 500001 ServerError hasonlóan ez a hiba valószínűleg átmeneti. | Kövesse a 500001 ServerError kapcsolatos hibaelhárítási lépéseket a probléma okának elhárítása érdekében, és oldja meg a hibát.|

## <a name="other-steps-to-try"></a>Egyéb lépések a kipróbáláshoz

Ha az előző lépések nem segítettek, a következőket teheti:

* Ha rendelkezik hozzáféréssel a problémás eszközökhöz fizikailag vagy távolról (például SSH), kövesse az [eszközre vonatkozó hibaelhárítási útmutatót](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) a hibaelhárítás folytatásához.

* Ellenőrizze, hogy az eszközök **engedélyezve** vannak-e a Azure Portal > az IoT hub > IoT eszközöket.

* Segítség kérése az [azure IoT hub fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [stack overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)vagy [Azure támogatásáról](https://azure.microsoft.com/support/options/).

Ha segítségre van szüksége mindenki számára, az alábbi visszajelzések szakaszban hagyja meg a megjegyzéseit, ha az útmutató nem segít Önnek.

## <a name="next-steps"></a>További lépések

* Az átmeneti problémák megoldásával kapcsolatos további tudnivalókért lásd: az [átmeneti hibák kezelésének](/azure/architecture/best-practices/transient-faults)ismertetése.

* Ha többet szeretne megtudni az Azure IoT SDK-ról és az újrapróbálkozások kezeléséről, olvassa el a [kapcsolat és a megbízható üzenetkezelés kezelése az azure IoT hub Device SDK](iot-hub-reliability-features-in-sdks.md#connection-and-retry)-k használatával című témakört
