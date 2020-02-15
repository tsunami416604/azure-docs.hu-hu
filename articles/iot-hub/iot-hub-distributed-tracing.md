---
title: Korrelációs azonosítók hozzáadása a IoT-üzenetekhez az elosztott nyomkövetés (pre)
description: Ismerje meg, hogyan használhatja az elosztott nyomkövetési képességet a megoldás által használt összes Azure-szolgáltatás IoT-üzeneteinek nyomon követésére.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.openlocfilehash: ed477dddeb499023f4803929d9433ed37c302159
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212487"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Azure IoT-eszközről a felhőbe irányuló üzenetek nyomon követése elosztott nyomkövetéssel (előzetes verzió)

A Microsoft Azure IoT Hub jelenleg [előzetes verziójú szolgáltatásként](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)támogatja az elosztott nyomkövetést.

IoT Hub az elosztott nyomkövetést támogató első Azure-szolgáltatás egyike. Ahogy egyre több Azure-szolgáltatás támogatja az elosztott nyomkövetést, nyomon követheti a IoT üzeneteket a megoldásban résztvevő összes Azure-szolgáltatásban. Az elosztott nyomkövetéssel kapcsolatos háttérért lásd: [elosztott nyomkövetés](../azure-monitor/app/distributed-tracing.md).

A IoT Hub elosztott nyomkövetésének engedélyezése lehetővé teszi a következők lehetőségét:

- Pontosan figyelje az egyes üzenetek áramlását a [nyomkövetési környezet](https://github.com/w3c/trace-context)használatával IoT hub. Ez a nyomkövetési környezet olyan korrelációs azonosítókat tartalmaz, amelyek lehetővé teszik az események egy másik összetevőből származó eseményekkel való összekapcsolását. Egy részhalmazra vagy az összes [IoT eszközre alkalmazható.](iot-hub-devguide-device-twins.md)
- A nyomkövetési környezet automatikus naplózása [Azure monitor diagnosztikai naplókba](iot-hub-monitor-resource-health.md).
- Az eszközökről a IoT Hub és az útválasztási végpontokra irányuló üzenetek folyamatának és késésének mérése és értelmezése.
- Megkezdheti a IoT-megoldásban lévő nem Azure-szolgáltatások elosztott nyomkövetésének megvalósítását.

Ebben a cikkben a [C Azure IoT Device SDK](iot-hub-device-sdk-c-intro.md) -t használja elosztott nyomkövetéssel. Az elosztott nyomkövetés támogatása még folyamatban van a többi SDK esetében.

## <a name="prerequisites"></a>Előfeltételek

- Az elosztott nyomkövetés előzetes verziója jelenleg csak a következő régiókban létrehozott IoT hubok esetében támogatott:

  - **Észak-Európa**
  - **Délkelet-Ázsia**
  - **USA 2. nyugati régiója**

- Ez a cikk azt feltételezi, hogy már ismeri a telemetria-üzenetek küldését az IoT hub-ra. Győződjön meg arról, hogy végrehajtotta a [Send telemetria C](quickstart-send-telemetry-c.md)rövid útmutatót.

- Regisztrálja az eszközt az IoT hub-ban (az egyes rövid útmutatókban elérhető lépések), és jegyezze fel a kapcsolatok karakterláncát.

- Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

## <a name="configure-iot-hub"></a>IoT Hub konfigurálása

Ebben a szakaszban egy IoT Hub konfigurál az elosztott nyomkövetési attribútumok (korrelációs azonosítók és időbélyegek) naplózásához.

1. Navigáljon az IoT hubhoz a [Azure Portal](https://portal.azure.com/).

1. Az IoT hub bal oldali paneljén görgessen le a **figyelés** szakaszhoz, és kattintson a **diagnosztikai beállítások**elemre.

1. Ha a diagnosztikai beállítások még nincsenek bekapcsolva, kattintson **a diagnosztika bekapcsolása**elemre. Ha már engedélyezte a diagnosztikai beállításokat, kattintson a **diagnosztikai beállítás hozzáadása**elemre.

1. A **név** mezőbe írja be az új diagnosztikai beállítás nevét. Például: **DistributedTracingSettings**.

1. Válasszon egyet vagy többet a következő lehetőségek közül, amelyek meghatározzák, hogy a naplózás hol lesz elküldve:

    - **Archiválás egy Storage-fiókba**: a naplózási adatokat tartalmazó Storage-fiók konfigurálása.
    - **Stream az Event hub**-ba: az Event hub konfigurálása, hogy tartalmazza a naplózási adatokat.
    - **Küldés log Analyticsba**: konfigurálja a log Analytics-munkaterületet, hogy tartalmazza a naplózási adatokat.

1. A **napló** szakaszban válassza ki azokat a műveleteket, amelyeknek a naplózási adatait meg szeretné jeleníteni.

    Ügyeljen arra, hogy **DistributedTracing**tartalmazzon, és konfigurálja a **megőrzést** , hogy hány napig tart a naplózás. A naplózási megőrzés a tárolási költségeket is befolyásolja.

    ![Képernyőfelvétel: a DistributedTracing kategóriájának helye a IoT diagnosztikai beállításaihoz](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Kattintson a **Save (Mentés** ) gombra az új beállításhoz.

1. Választható Ha szeretné megtekinteni az üzenetek különböző helyekre való áramlását, állítsa be az [útválasztási szabályokat legalább két különböző végpontra](iot-hub-devguide-messages-d2c.md).

Miután bekapcsolta a naplózást, IoT Hub rögzíti a naplót, ha az alábbi helyzetekben a rendszer az érvényes nyomkövetési tulajdonságokat tartalmazó üzenetet észlel:

- Az üzenetek a IoT Hub átjáróján érkeznek.
- Az üzenetet a IoT Hub dolgozza fel.
- Az üzenet átirányítva az egyéni végpontokra. Az útválasztást engedélyezni kell.

Ha többet szeretne megtudni ezekről a naplókról és azok sémáról, tekintse meg [az elosztott nyomkövetés IoT hub diagnosztikai naplókban](iot-hub-monitor-resource-health.md#distributed-tracing-preview)című témakört.

## <a name="set-up-device"></a>Eszköz beállítása

Ebben a szakaszban egy fejlesztési környezetet készít elő az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-val való használatra. Ezután az egyik minta módosításával engedélyezheti az elosztott nyomkövetést az eszköz telemetria üzenetein.

Ezek az utasítások a minta Windows rendszeren történő létrehozásához szükségesek. Más környezetekben lásd: [a c SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) vagy [előre csomagolt c SDK fordítása platform-specifikus fejlesztéshez](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development).

### <a name="clone-the-source-code-and-initialize"></a>A forráskód klónozása és az inicializálás

1. Telepítse az ["asztali fejlesztés C++a következővel" munkaterhelést](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) a Visual Studio 2019-es verzióra. A Visual Studio 2017 és a 2015 is támogatott.

1. Telepítse a [cmakt](https://cmake.org/). Győződjön meg arról, hogy a `PATH` a parancssorból `cmake -version` beírásával.

1. Nyisson meg egy parancssort vagy a Git Bash-felületet. Futtassa az alábbi parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárház legújabb kiadásának klónozásához:

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

1. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. Futtassa az alábbi parancsokat a `azure-iot-sdk-c` könyvtárából:

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Ha `cmake` nem találja a C++ fordítót, előfordulhat, hogy a fenti parancs futtatásakor hibákat fog kiépíteni. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>A Send telemetria minta szerkesztése az elosztott nyomkövetés engedélyezéséhez

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">A minta beolvasása a githubon</a>

1. A `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` forrásfájl megnyitásához használja a szerkesztőt.

1. Keresse meg a `connectionString` konstans deklarációját:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Cserélje le a `connectionString` konstans értékét az eszköz csatlakoztatási karakterláncára, és jegyezze fel az [eszköz regisztrálása](./quickstart-send-telemetry-c.md#register-a-device) című szakaszban, a [Send telemetria C](./quickstart-send-telemetry-c.md)rövid útmutatóban.

1. Módosítsa a `MESSAGE_COUNT` határozza meg a `5000`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Keresse meg a kód azon sorát, amely meghívja a `IoTHubDeviceClient_LL_SetConnectionStatusCallback`t a kapcsolatok állapotának visszahívása funkció regisztrálásához az üzenet küldése hurok előtt. Adja hozzá az alább látható kódot az alábbi módon az eszköz elosztott nyomkövetésének engedélyezéséhez `IoTHubDeviceClient_LL_EnablePolicyConfiguration` meghívásához:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    A `IoTHubDeviceClient_LL_EnablePolicyConfiguration` függvény lehetővé teszi, hogy a szabályzatok meghatározott IoTHub-funkciókhoz legyenek [konfigurálva.](./iot-hub-devguide-device-twins.md) Ha `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` engedélyezve van a fenti kóddal, az eszköz nyomkövetési viselkedése az eszköz Twin-beli elosztott nyomkövetési módosításaival fog rendelkezni.

1. Ha a minta alkalmazást az összes kvóta használata nélkül szeretné megőrizni, adjon hozzá egy másodperces késleltetést az üzenet küldése hurok végén:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Fordítás és Futtatás

1. Navigáljon a korábban létrehozott CMak-könyvtárból (`azure-iot-sdk-c/cmake`) *iothub_ll_telemetry_sample* projekt könyvtárába, és fordítsa le a mintát:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Futtassa az alkalmazást. Az eszköz az elosztott nyomkövetést támogató telemetria küld.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Az alkalmazás futásának megtartása. A konzol ablakának megtekintésével megtekintheti, hogy a rendszer elküldte-e az üzenetet IoT Hub.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Megkerülő megoldás harmadik féltől származó ügyfelek számára

Az elosztott nyomkövetési funkció **nem triviális** a C SDK használata nélkül. Ezért ez a megközelítés nem ajánlott.

Először is meg kell valósítania az üzenetekben az összes IoT Hub-protokoll primitívjét, a fejlesztői útmutató [IoT hub üzenetek létrehozása és olvasása](iot-hub-devguide-messages-construct.md)című témakörben leírtak szerint. Ezután szerkessze a protokoll tulajdonságait a MQTT/AMQP üzenetekben `tracestate` **rendszertulajdonságként**való hozzáadásához. Ezek:

* A MQTT esetében vegyen fel `%24.tracestate=timestamp%3d1539243209`t az üzenet témakörbe, ahol a `1539243209`t az üzenet létrehozási idejével kell helyettesíteni a Unix timestamp formátumban. Példaként tekintse át a [C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761) implementációját.
* AMQP esetén adja hozzá a `key("tracestate")` és az `value("timestamp=1539243209")` üzenetet. A referenciák megvalósításához [tekintse](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527)meg a következő témakört:.

A tulajdonságot tartalmazó üzenetek százalékos arányának szabályozása érdekében a felhő által kezdeményezett események, például a kettős frissítések figyeléséhez logikát kell végrehajtani.

## <a name="update-sampling-options"></a>Mintavételi beállítások frissítése 

A felhőből nyomon követett üzenetek százalékos arányának módosításához frissítenie kell az eszközt. Ezt többféleképpen is elvégezheti a Portálon és a IoT Hub Service SDK-ban található JSON-szerkesztővel. A következő alszakaszok példákat tartalmaznak.

### <a name="update-using-the-portal"></a>Frissítés a portál használatával

1. Navigáljon a IoT hubhoz [Azure Portal](https://portal.azure.com/), majd kattintson a **IoT-eszközök**elemre.

1. Kattintson az eszközre.

1. Keresse meg az **elosztott nyomkövetés engedélyezése (előzetes verzió)** lehetőséget, majd kattintson az **Engedélyezés**gombra.

    ![Elosztott nyomkövetés engedélyezése a Azure Portalban](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Válasszon ki egy 0 és 100% közötti **mintavételi sebességet** .

1. Kattintson a **Save** (Mentés) gombra.

1. Várjon néhány másodpercet, és kattintson a **frissítés**gombra, majd ha az eszköz sikeresen visszaigazolja az eszközt, a szinkronizálás ikon látható.

1. Lépjen vissza a telemetria-üzenethez tartozó konzolablak ablakához. A rendszer az alkalmazás tulajdonságai között `tracestate`ként küldött üzeneteket fogja látni.

    ![Nyomkövetés állapota](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. Választható Módosítsa a mintavételezési sebességet egy másik értékre, és figyelje meg, hogy az üzenetek milyen gyakorisággal rendelkeznek `tracestate` az alkalmazás tulajdonságai között.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Frissítés az Azure IoT Hub a VS Code-hoz

1. Telepítse a VS [Code-ot](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools), majd telepítse az Azure IoT hub legújabb verzióját a vs Code-hoz.

1. Nyissa meg a VS Code-ot, és [állítsa be IoT hub a kapcsolatok karakterláncát](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Bontsa ki az eszközt, és keresse meg az **elosztott nyomkövetési beállítást (előzetes verzió)** . Alatta kattintson az alcsomópontok **elosztott nyomkövetési beállításainak frissítése (előzetes verzió)** elemre.

    ![Elosztott nyomkövetés engedélyezése az Azure IoT Hub bővítményben](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. Az előugró ablakban válassza az **Engedélyezés**lehetőséget, majd az ENTER billentyű lenyomásával erősítse meg az 100-as mintavételezési arányt.

    ![Mintavételi mód frissítése](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Mintavételi sebesség frissítése](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Több eszköz tömeges frissítése

Az elosztott nyomkövetési mintavételi konfiguráció több eszközhöz való frissítéséhez használja az [automatikus eszköz konfigurációját](iot-hub-auto-device-config.md). Győződjön meg arról, hogy ezt a kettős sémát követi:

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| Elem neve | Kötelező | Típus | Leírás |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Igen | Egész szám | A mintavétel be-és kikapcsolása jelenleg két mód értékkel lehetséges. `1` be van kapcsolva, és `2` ki van kapcsolva. |
| `sampling_rate` | Igen | Egész szám | Ez az érték százalék. Csak `0` és `100` (beleértve a) értékek megengedettek.  |

## <a name="query-and-visualize"></a>Lekérdezés és megjelenítés

Ha meg szeretné tekinteni a IoT Hub által naplózott összes nyomkövetést, kérdezze le a diagnosztikai beállításokban kiválasztott napló-tárolót. Ez a szakasz néhány különböző lehetőséget mutat be.

### <a name="query-using-log-analytics"></a>Lekérdezés Log Analytics használatával

Ha a [log Analyticst diagnosztikai naplókkal](../azure-monitor/platform/resource-logs-collect-storage.md)állította be, a lekérdezés a naplók keresése a `DistributedTracing` kategóriában. Ez a lekérdezés például az összes naplózott nyomkövetést megjeleníti:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Példák a Log Analytics által megjelenített naplókra:

| TimeGenerated | OperationName | Kategória | Szint | CorrelationId | Átl | Tulajdonságok |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | Tájékoztató | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | Tájékoztató | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled": "false", "parentSpanId": "0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | Tájékoztató | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

A különböző típusú naplók megismeréséhez tekintse meg az [Azure IoT hub diagnosztikai naplók](iot-hub-monitor-resource-health.md#distributed-tracing-preview)című témakört.

### <a name="application-map"></a>Alkalmazástérkép

A IoT-üzenetek folyamatának megjelenítéséhez állítsa be az alkalmazás-hozzárendelési minta alkalmazást. A minta alkalmazás egy Azure-függvény és egy Event hub használatával küldi el az elosztott nyomkövetési naplókat az [Application Map](../application-insights/app-insights-app-map.md) szolgáltatásnak.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">A minta beolvasása a githubon</a>

Az alábbi képen az App Map elosztott nyomkövetése látható három útválasztási végponttal:

![IoT elosztott nyomkövetése az App Map-ben](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Az Azure IoT elosztott nyomkövetésének megismerése

### <a name="context"></a>Környezet

Számos IoT-megoldás, beleértve a saját [hivatkozási architektúrát](https://aka.ms/iotrefarchitecture) (csak angol nyelven), általában a [Service architektúrájának](https://docs.microsoft.com/azure/architecture/microservices/)egy változatát követi. Mivel a IoT-megoldás összetettebb, a végén egy tucat vagy több szolgáltatást használ. Előfordulhat, hogy ezek a szolgáltatások nem az Azure-ból származnak. Kihívást jelenthet a IoT üzenetek eldobásának és lelassulásának a kimutatása. Például egy IoT-megoldással rendelkezik, amely 5 különböző Azure-szolgáltatást és 1500 aktív eszközt használ. Minden eszköz 10 eszközről a felhőbe irányuló üzenetet küld/másodpercet (összesen 15 000 üzenet/másodperc), de észreveheti, hogy a webalkalmazás csak 10 000 üzenetet lát/másodpercet. Hol található a probléma? Hogyan találják meg a bűnöst?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Elosztott nyomkövetési minta a Service Architecture architektúrában

Ahhoz, hogy egy IoT-üzenet folyamatát újra létre lehessen alakítani a különböző szolgáltatások között, minden szolgáltatásnak egy *korrelációs azonosítót* kell propagálnia, amely egyedileg azonosítja az üzenetet. A központi rendszerbe való begyűjtést követően a korrelációs azonosítók lehetővé teszik az üzenetek folyamatának megtekintését. Ezt a metódust [elosztott nyomkövetési mintának](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing)nevezzük.

Az elosztott nyomkövetés szélesebb körű bevezetésének támogatásához a Microsoft hozzájárul az [elosztott nyomkövetéshez készült W3C standard javaslathoz](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>IoT Hub támogatás

Ha engedélyezve van, az elosztott nyomkövetés támogatása IoT Hub a következő folyamatot fogja követni:

1. A rendszer létrehoz egy üzenetet a IoT-eszközön.
1. A IoT-eszköz (a felhő segítségével) úgy dönt, hogy ezt az üzenetet nyomkövetési környezettel kell hozzárendelni.
1. Az SDK hozzáadja az üzenet-létrehozási időbélyeget tartalmazó `tracestate` a Message Application tulajdonsághoz.
1. A IoT-eszköz elküldi az üzenetet a IoT Hubnak.
1. Az üzenet megérkezik az IoT hub-átjáróra.
1. IoT Hub megkeresi a `tracestate` az üzenet alkalmazás tulajdonságaiban, és ellenőrzi, hogy a formátuma megfelelő-e.
1. Ha igen, IoT Hub hozza létre és naplózza a `trace-id` és a `span-id` a `DiagnosticIoTHubD2C`kategóriába tartozó diagnosztikai naplók Azure Monitoréhez.
1. Az üzenetek feldolgozásának befejeződése után IoT Hub egy másik `span-id` hoz létre, és a `DiagnosticIoTHubIngress`kategóriába tartozó meglévő `trace-id`t naplózza.
1. Ha az útválasztás engedélyezve van az üzenethez, IoT Hub írja azt az egyéni végpontba, és egy másik `span-id` naplózza ugyanazzal a `trace-id` a `DiagnosticIoTHubEgress`kategóriába.
1. A fenti lépéseket minden generált üzenet esetében meg kell ismételni.

## <a name="public-preview-limits-and-considerations"></a>Nyilvános előzetes verziókra vonatkozó korlátozások és megfontolások

- A W3C Trace Context standard csomagra vonatkozó javaslat jelenleg egy munkaterv.
- Jelenleg az ügyféloldali SDK által támogatott egyetlen fejlesztési nyelv a C.
- A felhőből az eszközre történő kettős képesség nem érhető el [IoT hub alapszintű csomaghoz](iot-hub-scaling.md#basic-and-standard-tiers). A IoT Hub azonban továbbra is Azure Monitor, ha a megfelelő számú nyomkövetési környezeti fejlécet lát.
- A hatékony működés biztosítása érdekében a IoT Hub a naplózás mértékét fogja alkalmazni, amely az elosztott nyomkövetés részeként fordulhat elő.

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni az általános elosztott nyomkövetési mintával kapcsolatban, tekintse meg a következő témakört [: a Service architektúrájának mintája: elosztott nyomkövetés](https://microservices.io/patterns/observability/distributed-tracing.html).
- Ha úgy szeretné beállítani a konfigurációt, hogy az elosztott nyomkövetési beállításokat nagy számú eszközre alkalmazza, tekintse meg a [IoT-eszközök konfigurálása és figyelése skálán](iot-hub-auto-device-config.md)című témakört
- Ha többet szeretne megtudni a Azure Monitorről, tekintse meg a [Mi az a Azure monitor?](../azure-monitor/overview.md)című témakört.
