---
title: Korrelációs azonosítók hozzáadása az IoT-üzenetekhez elosztott nyomkövetés (előzetes)
description: Ismerje meg, hogyan használhatja az elosztott nyomkövetési képességet az IoT-üzenetek nyomon követésére a megoldás által használt Azure-szolgáltatásokban.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 2b1dc7873140f885ec3efac11dec5fbf6aab7aa9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732577"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Az Azure IoT-eszközökről a felhőbe irányuló üzenetek nyomon követése (előzetes verzió)

A Microsoft Azure IoT Hub jelenleg támogatja az elosztott nyomkövetést [előnézeti funkcióként.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Az IoT Hub az első Azure-szolgáltatások egyike, amely támogatja az elosztott nyomkövetést. Ahogy egyre több Azure-szolgáltatás támogatja az elosztott nyomkövetést, nyomon követheti az IoT-üzeneteket a megoldásban részt vevő Azure-szolgáltatásokban. Az elosztott nyomkövetés hátterét az [Elosztott nyomkövetés témakörben tetszés ben ta-](../azure-monitor/app/distributed-tracing.md)

Az Elosztott nyomkövetés engedélyezése az IoT Hub számára lehetővé teszi a következőket:

- Pontosan figyelheti az egyes üzenetek áramlását az IoT Hubon keresztül [nyomkövetési környezet használatával.](https://github.com/w3c/trace-context) Ez a nyomkövetési környezet olyan korrelációs azonosítókat tartalmaz, amelyek lehetővé teszik az egyik összetevő eseményeinek egy másik összetevőből származó eseményekkorrel való összeírását. Az [ikereszköz](iot-hub-devguide-device-twins.md)használatával egy részhalmazra vagy az összes IoT-eszközüzenetre alkalmazható.
- A nyomkövetési környezet automatikus naplózása az [Azure Monitor diagnosztikai naplóiba.](iot-hub-monitor-resource-health.md)
- Az eszközöktől az IoT Hubig és az útválasztási végpontokig történő üzenetfolyam és késés mérése és megértése.
- Kezdje el megfontolni, hogyan szeretné megvalósítani a nem Azure-szolgáltatások elosztott nyomkövetését az IoT-megoldásában.

Ebben a cikkben az [Azure IoT-eszköz SDK C-hez](iot-hub-device-sdk-c-intro.md) terjesztett nyomkövetés. Az elosztott nyomkövetési támogatás még folyamatban van a többi SDK esetében.

## <a name="prerequisites"></a>Előfeltételek

- Az elosztott nyomkövetés előzetes verziója jelenleg csak a következő régiókban létrehozott IoT Hubok számára támogatott:

  - **Észak-Európa**
  - **Délkelet-Ázsia**
  - **USA nyugati régiója, 2.**

- Ez a cikk feltételezi, hogy ismeri a telemetriai üzenetek küldését az IoT hub. Győződjön meg arról, hogy befejezte a [C telemetriai adatok küldése rövid útmutatót.](quickstart-send-telemetry-c.md)

- Regisztráljon egy eszközt az IoT hub (az egyes rövid útmutatókban elérhető lépések) és jegyezze fel a kapcsolati karakterláncot.

- Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

## <a name="configure-iot-hub"></a>Az IoT hub konfigurálása

Ebben a szakaszban konfigurálja az IoT Hub ot az elosztott nyomkövetési attribútumok (korrelációs azonosítók és időbélyegek) naplózására.

1. Keresse meg az IoT-központot az [Azure Portalon.](https://portal.azure.com/)

1. Az IoT-központ bal oldali ablaktáblájában görgessen le a **Figyelés** szakaszig, és kattintson a **Diagnosztikai beállítások parancsra.**

1. Ha a diagnosztikai beállítások még nincsenek bekapcsolva, kattintson **a Diagnosztika bekapcsolása gombra.** Ha már engedélyezte a diagnosztikai beállításokat, kattintson **a Diagnosztikai beállítások hozzáadása gombra.**

1. A **Név** mezőbe írja be egy új diagnosztikai beállítás nevét. Például **DistributedTracingSettings**.

1. Válasszon az alábbi lehetőségek közül, amelyek meghatározzák, hogy a rendszer hová küldje a naplózást:

    - **Archiválás tárfiókba:** Állítson be egy tárfiókot a naplózási adatok tárolására.
    - **Adatfolyam egy eseményközpontba:** Állítson be egy eseményközpontot a naplózási adatok tárolására.
    - **Küldés a Log Analytics szolgáltatásba:** Konfiguráljon egy naplóelemzési munkaterületet a naplózási adatok tárolására.

1. A **Napló csoportban** jelölje ki azokat a műveleteket, amelyeknaplózási adatait naplózni szeretné.

    Győződjön meg arról, hogy tartalmazza **a DistributedTracing**, és konfigurálja a **megőrzési** hány napig szeretné megőrizni a naplózást. A naplómegőrzés hatással van a tárolási költségekre.

    ![Képernyőkép, amely bemutatja, hogy hol található az Elosztottta-követés kategória az IoT diagnosztikai beállításaihoz](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Kattintson a **Mentés** gombra az új beállításhoz.

1. (Nem kötelező) Ha azt szeretné, hogy az üzenetek különböző helyekre érkeznek, állítson be [útválasztási szabályokat legalább két különböző végpontra.](iot-hub-devguide-messages-d2c.md)

A naplózás bekapcsolása után az IoT Hub naplót rögzít, ha érvényes nyomkövetési tulajdonságokat tartalmazó üzenet jelenik meg az alábbi helyzetek bármelyikében:

- Az üzenetek az IoT Hub átjárójára érkeznek.
- Az üzenetet az IoT Hub dolgozza fel.
- Az üzenet egyéni végpontokhoz van irányítva. Az útválasztást engedélyezni kell.

Ezekről a naplókról és sémáikról az [Elosztott nyomkövetés az IoT Hub diagnosztikai naplóiban](iot-hub-monitor-resource-health.md#distributed-tracing-preview)című témakörben olvashat bővebben.

## <a name="set-up-device"></a>Eszköz beállítása

Ebben a szakaszban előkészíti a fejlesztői környezetet az [Azure IoT C SDK-val való használatra.](https://github.com/Azure/azure-iot-sdk-c) Ezután módosítja az egyik mintát, hogy lehetővé tegye az elosztott nyomkövetést az eszköz telemetriai üzeneteiben.

Ezek az utasítások a minta Windows rendszeren történő létrehozásához. Más környezetek ről a [C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) vagy [az Előre csomagolt C SDK platformspecifikus fejlesztéshez című](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development)témakörben található.

### <a name="clone-the-source-code-and-initialize"></a>Klónozza a forráskódot és inicializálja

1. Telepítse az ["Asztali fejlesztés C++"-os munkaterhelést](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) a Visual Studio 2019-hez. A Visual Studio 2017 és 2015 is támogatott.

1. Telepítse [a CMake -et.](https://cmake.org/) Győződjön meg róla, hogy a `PATH` parancssorból beírja `cmake -version` a készüléket.

1. Nyisson meg egy parancssort vagy a Git Bash-felületet. Futtassa a következő parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárház legújabb kiadásának klónozásához:

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

1. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. Futtassa a `azure-iot-sdk-c` következő parancsokat a könyvtárból:

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Ha `cmake` nem találja a C++ fordítót, a fenti parancs futtatása közben építési hibákat kaphat. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>A telemetriai minta küldése az elosztott nyomkövetés engedélyezéséhez

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">A minta beszereznie a GitHubon</a>

1. A `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` forrásfájl megnyitásához használjon szerkesztőt.

1. Keresse meg a `connectionString` konstans deklarációját:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Cserélje le az `connectionString` állandó értékét arra az eszközkapcsolati karakterláncra, amelyet a [C telemetriai adatok küldése rövid útmutató](./quickstart-send-telemetry-c.md) [eszközszakaszában](./quickstart-send-telemetry-c.md#register-a-device) feljegyezte.

1. Módosítsa `MESSAGE_COUNT` a `5000`definiálás t:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Keresse meg azt a `IoTHubDeviceClient_LL_SetConnectionStatusCallback` kódsort, amely a kapcsolatállapot-visszahívási funkció nak az üzenetküldési hurok előtti regisztrálásához hív. Adjon kódot a sor alá `IoTHubDeviceClient_LL_EnablePolicyConfiguration` az alábbi módon az eszköz elosztott nyomkövetésének engedélyezéséhez:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    A `IoTHubDeviceClient_LL_EnablePolicyConfiguration` függvény lehetővé teszi, hogy az [eszközök twins](./iot-hub-devguide-device-twins.md)keresztül konfigurált adott IoTHub-funkciók házirendjei. Ha `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` engedélyezve van a fenti kódsor, az eszköz nyomkövetési viselkedése tükrözi az ikereszközön végrehajtott elosztott nyomkövetési módosításokat.

1. Ha azt szeretné, hogy a mintaalkalmazás az összes kvóta használata nélkül fusson, adjon hozzá egy másodperces késleltetést a küldési üzenet hurok végén:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Fordítás és futtatás

1. Keresse meg a *iothub_ll_telemetry_sample* projektkönyvtárat`azure-iot-sdk-c/cmake`a korábban létrehozott CMake könyvtárból ( ) , és fordítsa le a mintát:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Futtassa az alkalmazást. Az eszköz telemetriát küld, amely támogatja az elosztott nyomkövetést.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Tartsa futtassa az alkalmazást. Ha megfigyelheti az IoT Hubnak küldött üzenetet a konzolablakból.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Megoldás külső ügyfelek számára

Nem **triviális** az elosztott nyomkövetési funkció előnézete a C SDK használata nélkül. Így ez a megközelítés nem ajánlott.

Először is az összes IoT Hub-protokoll primitívek az üzenetekben a fejlesztői útmutató [létrehozása és olvasása IoT Hub-üzenetek.](iot-hub-devguide-messages-construct.md) Ezután szerkesztsd a protokoll tulajdonságait az MQTT/AMQP üzenetekben, amelyeket `tracestate` **rendszertulajdonságként**ad hozzá. Ezek:

* Az MQTT `%24.tracestate=timestamp%3d1539243209` esetében adja hozzá `1539243209` az üzenettémakört, ahol az üzenet létrehozási idejét unix időbélyeg formátumban kell helyettesíteni. Példaként lásd a [C SDK-ban](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761)
* AMQP esetén `key("tracestate")` adja `value("timestamp=1539243209")` hozzá és üzenetként jegyzetként. A referencia-megvalósítást [itt](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527)olvashatja.

A tulajdonságot tartalmazó üzenetek százalékos arányának szabályozásához valósítsa meg a felhőáltal kezdeményezett események, például a ikerfrissítések figyeléséhez szükséges logikát.

## <a name="update-sampling-options"></a>Mintavételi beállítások frissítése 

A felhőből nyomon követhető üzenetek százalékos arányának módosításához frissítenie kell az ikereszközt. Ezt többféleképpen is elvégezheti, beleértve a JSON-szerkesztőt a portálon és az IoT Hub szolgáltatás SDK-t. A következő alfejezetek példákat mutatnak be.

### <a name="update-using-the-portal"></a>Frissítés a portál használatával

1. Keresse meg az IoT-központot az [Azure Portalon,](https://portal.azure.com/)majd kattintson **az IoT-eszközök**elemre.

1. Kattintson az eszközre.

1. Az **Elosztott nyomkövetés engedélyezése (előzetes verzió)** lehetőséget, majd válassza **az Engedélyezés lehetőséget.**

    ![Elosztott nyomkövetés engedélyezése az Azure Portalon](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Válasszon egy 0% és 100% közötti **mintavételi arányt.**

1. Kattintson a **Save** (Mentés) gombra.

1. Várjon néhány másodpercet, és nyomja le a **Frissítés**gombot, majd ha az eszköz sikeresen nyugtázza, megjelenik egy ikon egy pipával.

1. Lépjen vissza a telemetriai üzenetalkalmazás konzolablakába. Az alkalmazás tulajdonságai `tracestate` között megjelennek az üzenetek küldése.

    ![Nyomkövetési állapot](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Nem kötelező) Módosítsa a mintavételi arányt egy másik értékre, `tracestate` és figyelje meg az üzenetek által az alkalmazás tulajdonságaiban feltüntetett gyakoriságváltozásváltozását.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Frissítés az Azure IoT Hub for VS Code használatával

1. Telepítse a VS Code-ot, majd telepítse az Azure IoT Hub for VS Code legújabb verzióját [innen.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

1. Nyissa meg a VS-kódot, és [állítsa be az IoT Hub kapcsolati karakterláncát.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites)

1. Bontsa ki az eszközt, és keresse meg az **Elosztott nyomkövetési beállítást (előnézet)**. Alatta kattintson az **Elosztott nyomkövetési beállítás (előnézet) frissítése (előnézet)** elemre.

    ![Elosztott nyomkövetés engedélyezése az Azure IoT Hub-bővítményben](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. Az előugró ablakban válassza az **Engedélyezés**lehetőséget, majd nyomja le az Enter billentyűt a 100 mintavételi arány megerősítéséhez.

    ![Mintavételi mód frissítése](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Mintavételi arány frissítése](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Tömeges frissítés több eszközhöz

Több eszköz elosztott nyomkövetési mintavételezési konfigurációjának frissítéséhez használja az [automatikus eszközkonfigurációt.](iot-hub-auto-device-config.md) Győződjön meg róla, hogy követi ezt az ikersémát:

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
| `sampling_mode` | Igen | Egész szám | Jelenleg két módérték támogatott a mintavételezés be- és kikapcsolására. `1`be van `2` kapcsolva, és ki van kapcsolva. |
| `sampling_rate` | Igen | Egész szám | Ez az érték százalék. Csak a `0` `100` (beleértve) értékek megengedettek.  |

## <a name="query-and-visualize"></a>Lekérdezés és megjelenítés

Az IoT Hub által naplózott összes nyomkövetés megtekintéséhez kérdezze le a diagnosztikai beállításokban kiválasztott naplótárolót. Ez a rész néhány különböző lehetőségen vezet át.

### <a name="query-using-log-analytics"></a>Lekérdezés a Log Analytics használatával

Ha beállította a [Log Analytics diagnosztikai naplók,](../azure-monitor/platform/resource-logs-collect-storage.md)lekérdezés a kategória `DistributedTracing` naplóinak keresésével. Ez a lekérdezés például az összes naplózott nyomkövetést megjeleníti:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Példa naplók által megjelenített Log Analytics:

| TimeGenerated | OperationName | Kategória | Szint | CorrelationId | Időtartamok | Tulajdonságok |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | Tájékoztató | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngresss | DistributedTracing | Tájékoztató | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | Tájékoztató | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590acd909"} |

A különböző típusú naplók megismerése: [Azure IoT Hub diagnosztikai naplók.](iot-hub-monitor-resource-health.md#distributed-tracing-preview)

### <a name="application-map"></a>Alkalmazástérkép

Az IoT-üzenetek folyamatának megjelenítéséhez állítsa be az Alkalmazástérkép mintaalkalmazást. A mintaalkalmazás elküldi az elosztott nyomkövetési naplók [at Application Map](../application-insights/app-insights-app-map.md) egy Azure-függvény és egy Event Hub használatával.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">A minta beszereznie a GitHubon</a>

Az alábbi képen az elosztott nyomkövetés látható az App Mapben három útválasztási végponttal:

![IoT-alapú nyomkövetés az App Mapben](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Az Azure IoT által terjesztett nyomkövetés megismerése

### <a name="context"></a>Környezet

Számos IoT-megoldás, köztük saját [referenciaarchitektúránk](https://aka.ms/iotrefarchitecture) (csak angol nyelven) általában a [mikroszolgáltatási architektúra](https://docs.microsoft.com/azure/architecture/microservices/)egy változatát követi. Az IoT-megoldás összetettebbé válik, a végén egy tucat vagy több mikroszolgáltatások használatával. Ezek a mikroszolgáltatások lehet, hogy nem az Azure-ból. Az IoT-üzenetek leejtése vagy lassulása kihívást jelenthet. Például van egy IoT-megoldás, amely 5 különböző Azure-szolgáltatásokat és 1500 aktív eszközt használ. Minden eszköz 10 eszközről felhőbe irányuló üzenetet küld/másodperc (összesen 15 000 üzenet/másodperc), de azt veszi észre, hogy a webalkalmazás csak 10 000 üzenetet lát másodpercenként. Hol van a probléma? Hogy találja meg a tettest?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Elosztott nyomkövetési minta a mikroszolgáltatás-architektúrában

Az IoT-üzenetek különböző szolgáltatások közötti áramlásának rekonstruálása érdekében minden szolgáltatásnak olyan *korrelációs azonosítót* kell terjesztenie, amely egyedileg azonosítja az üzenetet. A központosított rendszerben gyűjtött korrelációs azonosítók lehetővé teszik az üzenetfolyam megtekintését. Ezt a módszert [elosztott nyomkövetési mintának nevezzük.](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing)

Az elosztott nyomkövetés szélesebb körű bevezetésének támogatása érdekében a Microsoft hozzájárul a [W3C megosztott nyomkövetésre vonatkozó szabványos javaslatához.](https://w3c.github.io/trace-context/)

### <a name="iot-hub-support"></a>Az IoT Hub támogatása

Ha engedélyezve van, az IoT Hub elosztott nyomkövetési támogatása ezt a folyamatot követi:

1. Egy üzenet jön létre az IoT-eszközön.
1. Az IoT-eszköz úgy dönt (a felhősegítségével), hogy ezt az üzenetet kell hozzárendelni egy nyomkövetési környezetben.
1. Az SDK `tracestate` hozzáad egy az üzenetalkalmazás-tulajdonsághoz, amely tartalmazza az üzenet létrehozási időbélyegét.
1. Az IoT-eszköz elküldi az üzenetet az IoT Hubnak.
1. Az üzenet megérkezik az IoT hub átjáró.
1. Az IoT Hub `tracestate` megkeresi az üzenetalkalmazás tulajdonságait, és ellenőrzi, hogy a megfelelő formátumú-e.
1. Ha igen, az IoT Hub `trace-id` globálisan egyedi `span-id` t, a az "ugrás" és naplózza azokat `DiagnosticIoTHubD2C`az Azure Monitor diagnosztikai naplók a művelet alatt.
1. Miután az üzenet feldolgozása befejeződött, `span-id` az IoT Hub `trace-id` létrehoz egy `DiagnosticIoTHubIngress`másikat, és naplózza azt a művelet alatt meglévővel együtt.
1. Ha az üzenet útválasztása engedélyezve van, az IoT Hub az `span-id` egyéni `trace-id` végpontra `DiagnosticIoTHubEgress`írja azt, és egy másikat naplóz a kategóriában.
1. A fenti lépések minden létrehozott üzenetnél megismétlődnek.

## <a name="public-preview-limits-and-considerations"></a>Nyilvános előzetes verzió korlátai és szempontjai

- A W3C Trace Context szabványra vonatkozó javaslat jelenleg egy működő tervezet.
- Jelenleg az ügyfél SDK által támogatott egyetlen fejlesztési nyelv a C.
- A felhőből az eszközre irányuló ikerhálózat nem érhető el az [IoT Hub alapszintű szintje in.](iot-hub-scaling.md#basic-and-standard-tiers) Az IoT Hub azonban továbbra is bejelentkezik az Azure Monitorra, ha megfelelően összeállított nyomkövetési környezetfejlécet lát.
- A hatékony működés biztosítása érdekében az IoT Hub egy fojtószelepet ír elő az elosztott nyomkövetés részeként előforduló naplózási sebességre.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni az általános elosztott nyomkövetési minta a mikroszolgáltatások, olvassa el a [Mikroszolgáltatás architektúra minta: elosztott nyomkövetés.](https://microservices.io/patterns/observability/distributed-tracing.html)
- Ha azt szeretné, hogy a konfiguráció nagyszámú eszközre alkalmazzon elosztott nyomkövetési beállításokat, olvassa el az [IoT-eszközök nagy méretekben történő konfigurálása és figyelése című témakört.](iot-hub-auto-device-config.md)
- Ha többet szeretne megtudni az Azure Monitorról, olvassa el [a Mi az Azure Monitor?](../azure-monitor/overview.md).
