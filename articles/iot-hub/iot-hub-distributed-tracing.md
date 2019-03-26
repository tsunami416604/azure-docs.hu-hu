---
title: Korrelációs azonosítók hozzáadása IoT-üzenetek az elosztott nyomkövetést (előzetes verzió)
description: ''
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.openlocfilehash: fc6db4d02898ea0e8eed3cdf3d0b1a9788d943e9
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439296"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Elosztott nyomkövetést (előzetes verzió) az Azure IoT eszköz – felhő üzenetek nyomon követése

A Microsoft Azure IoT Hub jelenleg támogatja az elosztott nyomkövetést, mint egy [előzetes verziójú funkció](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az IoT Hub az elosztott nyomkövetést támogatásához az első Azure-szolgáltatások egyik. További Azure-szolgáltatások támogatja az elosztott nyomkövetést, mert fogja tudni nyomkövetési IoT üzenetek teljes részt vesz a megoldás az Azure-szolgáltatások. Elosztott nyomkövetést a háttérbeli, lásd: [elosztott nyomkövetést](../azure-monitor/app/distributed-tracing.md).

Elosztott nyomkövetést engedélyezése az IoT Hub lehetővé teszi a:

- Pontosan figyelheti az egyes üzenetek IoT hubon keresztül a flow [nyomkövetési környezet](https://github.com/w3c/trace-context). Ebben a környezetben a nyomkövetési magában foglalja a korrelációs azonosítók, amelyek lehetővé teszik egy másik összetevő származó eseményekkel rendelkező események az egyes összetevők korrelációját. Egy részhalmazát vagy IoT-eszköz összes üzenet használatával alkalmazhatók [ikereszköz](iot-hub-devguide-device-twins.md).
- Automatikusan naplózhatja a nyomkövetési környezet [diagnosztikai naplók az Azure Monitor](iot-hub-monitor-resource-health.md).
- Mérje meg, és üzenet a folyamat és a késés eszközről az IoT Hub és az útválasztási végpontok.
- Indítsa el a mérlegeli, hogyan szeretné implementáljon elosztott nyomkövetést, az-Azure szolgáltatások a saját IoT-megoldás.

Ebben a cikkben fogja használni a [Azure IoT eszközoldali SDK-t a c nyelvhez készült](./iot-hub-device-sdk-c-intro.md) az elosztott nyomkövetést. Elosztott nyomkövetést támogatása továbbra is más SDK-k folyamatban van.

## <a name="prerequisites"></a>Előfeltételek

- Az előzetes verziója elosztott nyomkövetést jelenleg csak a az IoT-központok létrehozni a következő régiókban támogatott:

  - **Észak-Európa**
  - **Délkelet-Ázsia**
  - **2. nyugati RÉGIÓJA**

- Ez a cikk feltételezi, hogy ismeri a telemetriai üzeneteket küld az IoT hubnak. Győződjön meg arról, hogy befejezte a [Telemetriát C rövid](./quickstart-send-telemetry-c.md).

- Eszköz regisztrálása az IoT hub (lépéseket minden egyes a rövid útmutatóban elérhető) és jegyezze fel a kapcsolati karakterláncot.

- Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

## <a name="configure-iot-hub"></a>IoT-központ konfigurálása

Ebben a szakaszban egy IoT hubra való bejelentkezéshez elosztott nyomkövetést attribútumok (korrelációs azonosítók és időbélyegek) konfigurálása.

1. Keresse meg az IoT hub a [az Azure portal](https://portal.azure.com/).

1. Az IoT hub, a bal oldali panelen görgessen le a **figyelés** szakaszt, és kattintson a **diagnosztikai beállítások**.

1. Ha a diagnosztikai beállítások már nem aktív, kattintson a **diagnosztika bekapcsolása**. Ha már engedélyezte a diagnosztikai beállítások, kattintson a **diagnosztikai beállítás hozzáadása**.

1. Az a **neve** mezőben adjon meg egy új diagnosztikai beállítás nevét. Ha például **DistributedTracingSettings**.

1. Válasszon egy vagy több, a következő beállításokat, amelyek meghatározzák, ahol a naplózási küld:

    - **Archiválás tárfiókba**: Konfigurálja storage-fiók, a naplózási információkat tartalmazzák.
    - **Az eseményközpontok felé Stream**: Állítsa be egy eseményközpontot, a naplózási információkat tartalmazzák.
    - **Küldés a Log Analyticsnek**: Konfigurálja a log analytics-munkaterületet a naplózási információkat tartalmazzák.

1. Az a **Log** területen válassza ki a műveleteket, amelyeket szeretne naplózását.

    Győződjön meg arról, hogy tartalmazzák **DistributedTracing**, és konfigurálja a **megőrzési** , hány napig őrzi meg a naplózást szeretne. Napló megőrzése a tárolási költségeket befolyásolja.

    ![Képernyőfelvétel: hol DistributedTracing kategória van-e az IoT-diagnosztikai beállítások](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Kattintson a **mentése** az új beállítások.

1. (Nem kötelező) A különböző helyeken áramló, és állítsa be az üzenetek megtekintéséhez [legalább két különböző végpontok útválasztási szabályokat](iot-hub-devguide-messages-d2c.md).

Után a naplózás be van kapcsolva, az IoT Hub egy naplóban rögzíti a egy érvényes nyomkövetési tulajdonságait tartalmazó üzenetet észlelt a következő helyzetek egyikében:

- Az üzeneteket az IoT Hub-átjáró a érkezik.
- Az üzenetet az IoT Hub dolgoz fel.
- Az üzenet az egyedi végpontok irányítja a rendszer. Útválasztás engedélyezve kell lennie.

Ezek a naplók és a sémáikat kapcsolatos további tudnivalókért lásd: [elosztott nyomkövetést a diagnosztikai naplókban az IoT Hub](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Eszköz beállítása

Ebben a szakaszban egy fejlesztési környezetben való használatra előkészíteni a [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Ezután módosítja ahhoz, hogy az eszköz telemetriai üzeneteket az elosztott nyomkövetést minták egyikét.

Ezeket az utasításokat, amellyel a minta a Windows rendszer. Más környezetek esetén lásd: [fordítsa le az C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) vagy [előrecsomagolt C SDK-t az egyes fejlesztői Platform](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development).

### <a name="clone-the-source-code-and-initialize"></a>Klónozza a forráskódot, és inicializálása

1. Telepítés ["Asztali fejlesztés C++ használatával" számítási feladat](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2017) Visual Studio 2015 vagy 2017.

1. Telepítés [CMake](https://cmake.org/). Ellenőrizze, hogy van a `PATH` beírásával `cmake -version` parancsot a parancssorba.

1. Nyisson meg egy parancssort vagy a Git Bash-felületet. A következő parancs végrehajtásával klónozza az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattárat:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Az adattár mérete jelenleg körülbelül 220 MB. Ez a művelet várhatóan több percig is eltarthat.

1. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára.

    ```cmd
    cd azure-iot-sdk-c    
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Ha `cmake` nem találja a C++ fordítóprogram kaphat fordítási hibákat a fenti parancs futtatása közben. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>A Küldés telemetriai minta ahhoz, hogy elosztott nyomkövetést szerkesztése

1. Szerkesztő segítségével nyissa meg a `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` forrásfájl.

1. Keresse meg a `connectionString` konstans deklarációját:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Értékét cserélje le a `connectionString` jegyezze fel a végzett folyamatos, az eszköz kapcsolati karakterlánccal a [eszköz regisztrálása](./quickstart-send-telemetry-c.md#register-a-device) szakaszában a [Telemetriát C rövid](./quickstart-send-telemetry-c.md).

1. Módosítsa a `MESSAGE_COUNT` meghatározzák az `5000`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Keresse meg a kódot, amely meghívja ezt a sort `IoTHubDeviceClient_LL_SetConnectionStatusCallback` regisztrálása előtt a küldési üzenetciklusa kapcsolat állapota visszahívási függvény. Adja hozzá a kódot a sor alatt hívja az alább látható módon `IoTHubDeviceClient_LL_EnablePolicyConfiguration` elosztott nyomkövetést az eszköz engedélyezése:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    A `IoTHubDeviceClient_LL_EnablePolicyConfiguration` funkció lehetővé teszi, hogy a házirendek adott IoTHub szolgáltatások használatával konfigurált [ikereszközök](./iot-hub-devguide-device-twins.md). Egyszer `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` engedélyezve van a fenti kód vonal, a nyomkövetés viselkedés az eszköz az ikereszköz módosítások elosztott nyomkövetést fogja tartalmazni.

1. Az összes kvótájának nélkül futó mintaalkalmazás megőrzéséhez a küldési üzenetciklusa végén egy másodperces késleltetés hozzáadása:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Fordítás és futtatás

1. Keresse meg a *iothub_ll_telemetry_sample* projektkönyvtár a CMake könyvtárból (`azure-iot-sdk-c/cmake`) korábban létrehozott, és fordítsa le a mintát:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Futtassa az alkalmazást. Az eszköz elosztott nyomkövetést támogató telemetriát küld.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Tartsa meg az alkalmazással. Igény szerint vizsgálja meg az üzenetet küld az IoT Hub megnézzük a konzolablakban.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Megkerülő megoldás a külső ügyfelek számára

Rendelkezik **nem triviális** az előzetes verzióra az elosztott nyomkövetést szolgáltatás C SDK használata nélkül. Ez a megközelítés ezért nem ajánlott.

Először meg kell valósítani az IoT Hub protokoll primitívek az üzenetek a fejlesztői útmutatót [létrehozása és olvas az IoT Hub-üzenetek](iot-hub-devguide-messages-construct.md). Ezt követően az üzenetek hozzáadása az AMQP és MQTT protokoll tulajdonságainak szerkesztése `tracestate` , **rendszertulajdonság**. Ezek:

* Az MQTT, adja hozzá `%24.tracestate=timestamp%3d1539243209` üzenet témakörbe, ahol `1539243209` le kell cserélni a unix-időbélyegző formátumban lévő üzenet létrehozásának idejét. Tegyük fel, tekintse meg a megvalósítás [C SDK-ban](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761)
* Az AMQP és adja hozzá `key("tracestate")` és `value("timestamp=1539243209")` , üzenet jegyzet. Egy referenciaimplementációt, lásd: [Itt](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527).

Ezt a tulajdonságot tartalmazó üzenetek aránya szabályozhatja, mint például az ikereszköz-frissítések felhő által kezdeményezett eseményeinek figyelésére, a logikát alkalmazzák.

## <a name="update-sampling-options"></a>Mintavételi beállítások frissítése 

Módosítja a kulcsát, az üzenetek a felhőből nyomon követését, frissítenie kell az ikereszközben. Ez többek között a JSON-szerkesztő portálon és az IoT Hub szolgáltatási SDK-val többféle végezheti el. A következőkben példákat biztosítanak.

### <a name="update-using-the-portal"></a>Frissítse a portál használatával

1. Keresse meg az IoT hubot a [az Azure portal](https://portal.azure.com/), majd kattintson a **IoT-eszközök**.

1. Kattintson az eszközre.

1. Keressen **engedélyezése elosztott nyomkövetést (előzetes verzió)**, majd **engedélyezése**.

    ![Az Azure Portalon elosztott nyomkövetést engedélyezése](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Válasszon egy **mintavételi ráta** 0 és 100 % között.

1. Kattintson a **Save** (Mentés) gombra.

1. Várjon néhány másodpercet, és kattintson a **frissítése**, majd ha az eszköz sikeresen nyugtázta egy szinkronizálási egy pipa ikon jelenik meg.

1. Lépjen vissza a konzolablakban a telemetriai üzenetet alkalmazáshoz. Látni fogja az elküldött üzenetek `tracestate` alkalmazás tulajdonságai.

    ![A nyomkövetési állapot](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Nem kötelező) A mintavételi ráta módosítása egy másik értékre, és figyelje meg a módosítása, beleértve az üzenetek gyakorisága elemnél `tracestate` alkalmazás tulajdonságai.

### <a name="update-using-azure-iot-hub-toolkit-for-vs-code"></a>Frissítés a VS Code Azure IoT Hub-eszközkészlet használatával

1. A VS Code telepítése, majd telepítse az Azure IoT Hub-eszközkészlet legújabb verzióját a VS Code a [Itt](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

1. Nyissa meg a VS Code és [az IoT Hub kapcsolati karakterlánc beállítása](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Bontsa ki az eszközt, és keressen **elosztott nyomkövetési beállítás (előzetes verzió)**. Részre, kattintson a **frissítés elosztott nyomkövetési beállítás (előzetes verzió)** alárendelt csomópont.

    ![Az Azure IoT Hub-eszközkészlet elosztott nyomkövetést engedélyezése](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. Az előugró ablakban válassza ki a **engedélyezése**, nyomja le az Enter billentyűt a mintavételi ráta 100 megerősítése.

    ![Frissítés mintavételezési mód](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Mintavételi ráta módosítása](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Tömeges frissítés több eszközön

Több eszköz elosztott nyomkövetést mintavételi konfigurációjának frissítéséhez használja [automatikus eszközkonfiguráció](iot-hub-auto-device-config.md). Győződjön meg arról, hogy az ikereszköz sémát követi:

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

| Elem neve | Szükséges | Típus | Leírás |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Igen | Egész szám | Két üzemmód értékéhez a jelenleg támogatott a mintavételi kapcsolhatja be és ki. `1` a rendszer, és `2` ki van kapcsolva. |
| `sampling_rate` | Igen | Egész szám | Százalékos értéke. Csak értékeket `0` való `100` (inkluzív) használata engedélyezett.  |

## <a name="query-and-visualize"></a>Lekérdezése és megjelenítése

Az IoT Hub által naplózott összes nyomkövetések megtekintéséhez lekérdezheti a diagnosztikai beállításokat a kiválasztott tároló. Ez a szakasz néhány eltérő lehetőségeket ismerteti.

### <a name="query-using-log-analytics"></a>Lekérdezés a Log Analytics használatával

Ha már beállította [Log Analytics-diagnosztikai naplók](../azure-monitor/platform/diagnostic-logs-stream-log-store.md), a naplók keresése a lekérdezésre a `DistributedTracing` kategória. Ez a lekérdezés például naplózott összes nyomkövetési látható:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Példa naplók Log Analytics által látható módon:

| TimeGenerated | OperationName | Kategória | Szint | CorrelationId | DurationMs | Tulajdonságok |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | Tájékoztató | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | Tájékoztató | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | Tájékoztató | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

A naplók különböző típusú ismertetése: [Azure IoT Hub-diagnosztikai naplók](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

### <a name="application-map"></a>Alkalmazástérkép

A folyamat az IoT-üzenetek megjelenítése, állítsa be az alkalmazás-hozzárendelés mintaalkalmazást. A mintaalkalmazás küldi az elosztott nyomkövetést bejegyzéseit, amelyek [Alkalmazástérkép](../application-insights/app-insights-app-map.md) használatával egy Azure-függvényt, és a egy Eseményközpontba.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">GET, a minta a Githubon</a>

Ez az alábbi képen látható elosztott nyomkövetést az Alkalmazástérkép három útválasztási végpontok:

![IoT elosztott nyomkövetést az Alkalmazástérkép](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Azure IoT elosztott nyomkövetést ismertetése

### <a name="context"></a>Környezet

Számos IoT-megoldások, beleértve a saját [referenciaarchitektúra](https://aka.ms/iotrefarchitecture) (csak angol nyelven), általában valamely változatában hajtsa végre a [mikroszolgáltatási architektúra](https://docs.microsoft.com/azure/architecture/microservices/). IoT-megoldás növekedésével összetettebb, végül egy tucatnyi vagy több mikroszolgáltatások használatával. Előfordulhat, hogy ilyen mikroszolgáltatásokból, vagy nem lehet az Azure-ból. Felügyelő, ahol az IoT-üzenetekhez eldobása, vagy lelassítanunk kihívást válhat. Például hogy IoT-megoldás, amely 5 különböző Azure-szolgáltatásokat és 1500 aktív eszközt használ. Minden egyes eszköz küld 10 eszköz – felhő üzenetek/másodperc (az összesen 15 000 üzenetek/másodperc), de azt észleli, hogy a webalkalmazás látja csak 10 000 üzenetek/másodperc. Hol található a probléma? Hogyan meg a probléma oka?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>A mikroszolgáltatási architektúra elosztott nyomkövetést minta

Helyreállítani egy IoT-üzenet a folyamat között a különböző szolgáltatásokkal, minden egyes szolgáltatás propagálni kell egy *korrelációs azonosító* , amely egyedileg azonosítja az üzenetet. Után egy központosított rendszerben a korrelációs azonosítók lehetővé teszi üzenet folyamat megtekintéséhez. Ezt a metódust meghívják a [elosztott nyomkövetést minta](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing).

A szélesebb körű bevezetési támogatja az elosztott nyomkövetést, a Microsoft is hozzájárul [W3C szabvány javaslatot elosztott nyomkövetést](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Az IoT Hub-támogatás

Ha engedélyezve van, az IoT Hub elosztott nyomkövetést támogatása kövesse az ezt a folyamatot:

1. Egy üzenetet az IoT-eszköz jön létre.
1. Az IoT eszköz úgy dönt, hogy (a felhő segítségével), hogy ez az üzenet hozzá kell rendelni egy nyomkövetési környezettel.
1. Az SDK-t ad hozzá egy `tracestate` alkalmazás üzenettulajdonság tartalmazó üzenet létrehozásának történő küldés időbélyegzője legyen.
1. Az IoT-eszköz az üzenetet küld az IoT hubnak.
1. Az üzenet megérkezik az IoT hub-átjáró.
1. Az IoT Hub keres a `tracestate` a üzenet alkalmazás tulajdonságait, és ellenőrzi, hogy a megfelelő formátumban.
1. Ha tehát az IoT Hub állít elő, és naplózza az `trace-id` és `span-id` az Azure Monitor diagnosztikai naplók a kategóriához tartozó `DiagnosticIoTHubD2C`.
1. Ha az üzenet feldolgozása befejeződött, az IoT Hub állít elő egy másik `span-id` és naplózza a együtt a meglévő `trace-id` kategóriában `DiagnosticIoTHubIngress`.
1. Útválasztás engedélyezve van az üzenetet, ha az IoT Hub írja azokat az egyéni végpont, és a naplók egy másik `span-id` azonos `trace-id` kategóriában `DiagnosticIoTHubEgress`.
1. A fenti lépéseket minden egyes létrehozott üzenet ismétlődik.

## <a name="public-preview-limits-and-considerations"></a>Nyilvános előzetes verzióban korlátozások és szempontok

- A W3C nyomkövetési környezet standard javaslat jelenleg egy működő draft.
- Az ügyfél-SDK által támogatott egyetlen fejlesztői nyelvek jelenleg c-hez
- Felhőalapú ikereszköz funkció nem érhető el a [alapszintű IoT Hub-csomag](iot-hub-scaling.md#basic-and-standard-tiers). Azonban az IoT Hub továbbra is naplózza az Azure Monitor, ha azt látja, hogy megfelelően szintből nyomkövetési környezet fejléc.
- Hatékony működés biztosítása érdekében az IoT Hub a szabályozás mértéke a naplózás, amely akkor fordulhat elő, elosztott nyomkövetést részeként a rendszer kivetett.

## <a name="next-steps"></a>További lépések

- Az általános elosztott nyomkövetést minta a mikroszolgáltatások kapcsolatos további információkért lásd: [Mikroszolgáltatási architektúra a minta: elosztott nyomkövetési](https://microservices.io/patterns/observability/distributed-tracing.html).
- Konfigurációs beállításával elosztott nyomkövetést beállításokat alkalmazni egy nagy számú, tekintse meg [konfigurálása és figyelése a nagy mennyiségű IoT-eszközök](iot-hub-auto-device-config.md).
- Az Azure Monitor kapcsolatos további információkért lásd: [Mi az Azure Monitor?](../azure-monitor/overview.md).
