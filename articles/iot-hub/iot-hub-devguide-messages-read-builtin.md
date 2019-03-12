---
title: Megismerheti az Azure IoT Hub beépített végpont |} A Microsoft Docs
description: Fejlesztői útmutató – útmutató a beépített, Event Hub-kompatibilis végpont használata az eszköz a felhőbe irányuló üzenetek olvasását.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 52f1316b8167d2e1c3e37dbbfc0059b68e832172
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538561"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Az eszközről a felhőbe irányuló üzenetek beolvasása a beépített végpontról

Alapértelmezés szerint üzenetek irányíthatók át a beépített szolgáltatás felé néző végpont (**üzenetek/események**), amely kompatibilis a rendszer [az Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Ez a végpont a jelenleg csak közzétett használja a [AMQP](https://www.amqp.org/) 5671-es port-protokoll. Az IoT hub mutatja meg, hogy szabályozza a beépített üzenetkezelési Event Hub-kompatibilis végpont a következő tulajdonságok **üzenetek/események**.

| Tulajdonság            | Leírás |
| ------------------- | ----------- |
| **Partíciók száma** | Ez a tulajdonság beállítva a létrehozáskor számának meghatározásához [partíciók](../event-hubs/event-hubs-features.md#partitions) az eszközről a felhőbe eseményfeldolgozást. |
| **Megőrzési idő**  | Ez a tulajdonság határozza meg, mennyi ideig maradnak, üzeneteket az IoT Hub napban. Az alapértelmezett érték egy nap, de a hét nap növelhető. |

Az IoT Hub lehetővé teszi, hogy az adatok megőrzése a beépített Event hubs számára legfeljebb 7 napig. Beállíthatja, hogy a megőrzési időtartam, az IoT hub létrehozása során. Adatok megőrzése mérete az IoT Hub az IoT hub-szint és az egység típusa függ. Tekintetében mérete a beépített az Event Hubs üzenetek maximális mérete legfeljebb 24 órával kvóta őrizheti meg. Például az IoT Hub által biztosított elegendő tárhely megőrzése legalább 1 S1 egység 400 ezer üzenetet 4 k méretezés minden. Az eszközök kisebb üzeneteket küldenek, akkor előfordulhat, hogy őrzi meg (akár 7 napig) attól függően, hogy mennyi tárhelyet használja fel. Garantáljuk, hogy az adatok megőrzése legalább a megadott megőrzési ideje.

Az IoT Hub lehetővé teszi olyan kezelése a beépített eszköz-felhő a felhasználói csoportok végpont kap.

Ha használ [üzenet-útválasztása](iot-hub-devguide-messages-d2c.md) és a [tartalék útvonal](iot-hub-devguide-messages-d2c.md#fallback-route) van engedélyezve van, minden üzenetet, amely nem egyezik egy lekérdezést minden olyan útvonal nyissa meg a beépített végpont. Ha letiltja ezt az útvonalat tartalék, üzeneteket, amelyek nem egyeznek a lekérdezés a rendszer elveti.

Módosíthatja a megőrzési időtartam vagy programozott módon használja a [az IoT Hub erőforrás-szolgáltató REST API-k](/rest/api/iothub/iothubresource), vagy a [az Azure portal](https://portal.azure.com).

Az IoT Hub tesz a **üzenetek/események** beépített végpont a háttérbeli szolgáltatásokhoz a hub által fogadott eszköz a felhőbe irányuló üzenetek olvasásához. Ez a végpont az Event Hub-kompatibilis, amely lehetővé teszi, hogy az módszerek bármelyikét használhatja az Event Hubs szolgáltatás támogatja az üzenetek olvasásához.

## <a name="read-from-the-built-in-endpoint"></a>Olvassa el a beépített végpontról

Néhány termékintegrációk és az Event Hubs SDK-k az IoT Hub veszik figyelembe, és a beépített végponthoz csatlakozik az IoT hub szolgáltatás kapcsolati karakterláncának használata.

Amikor az Event Hubs SDK-k vagy termékintegrációk, amelyek nem tudnak az IoT Hub, Event Hub-kompatibilis végponthoz és Event Hub-kompatibilis nevet van szükség. Ezek az értékek módon lehet lekérdezni a portálról:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az IoT hubot.

2. Kattintson a **beépített végpontokról**.

3. A **események** szakasz tartalmazza a következő értékeket: **A partíciók**, **Event Hub-kompatibilis nevet**, **Event Hub-kompatibilis végpont**, **megőrzési időtartama**, és **fogyasztóicsoportok**.

    ![Eszközről-a-felhőbe típusú üzenetek beállításai](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

A portálon az Event Hub-kompatibilis végpont mezőbe, amely a következőhöz hasonló teljes az Event Hubs kapcsolati karakterláncot tartalmazza: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**. Ha az SDK-t használ a többi értéket igényel, majd azokat a következő lesz:

| Name (Név) | Érték |
| ---- | ----- |
| Végpont | sb://abcd1234namespace.servicebus.windows.net/ |
| Gazdanév | abcd1234namespace.servicebus.windows.net |
| Névtér | abcd1234namespace |

Minden megosztott elérési házirendet, amely rendelkezik használhatja a **ServiceConnect** engedélyeket a megadott Event hubhoz való csatlakozáshoz.

Az SDK-kat használhatja a beépített, amely közzéteszi az IoT Hub Event Hub-kompatibilis végponthoz csatlakozik a következők:

| Nyelv | SDK | Példa | Megjegyzések |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Gyors útmutató](quickstart-send-telemetry-dotnet.md) | Event Hubs-kompatibilis információival |
 Java | https://github.com/Azure/azure-event-hubs-java | [Gyors útmutató](quickstart-send-telemetry-java.md) | Event Hubs-kompatibilis információival |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Gyors útmutató](quickstart-send-telemetry-node.md) | Az IoT Hub kapcsolati karakterláncot használja. |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Az IoT Hub kapcsolati karakterláncot használja. |

A termék való integrációt, a beépített Event Hub-kompatibilis végpont, amely közzéteszi az IoT Hub használata a következők:

* [Az Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Lásd: [adatfeldolgozás az Azure Functions az IoT hubról](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/). Lásd: [data Stream bemeneti Stream analyticsbe](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/). Lásd: [IoT hub-eseményforrás hozzáadása a Time Series Insights-környezethez](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Az Apache Storm spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Megtekintheti a [forrás spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) a Githubon.
* [Az Apache Spark-integráció](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Az Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>További lépések

* IoT Hub-végpontok kapcsolatos további információkért lásd: [IoT Hub-végpontok](iot-hub-devguide-endpoints.md).

* A [útmutatóink](quickstart-send-telemetry-node.md) bemutatják, hogyan eszköz – felhő üzeneteket küldhet a szimulált eszközök és a beépített végpont érkező üzenetek olvasásához. 

További részletekért tekintse meg a [folyamat az IoT Hub eszköz – felhő üzenetek útvonalak](tutorial-routing.md) oktatóanyag.

* Ha szeretné az eszköz a felhőbe irányuló üzenetek átirányítása egyéni végpontok, [üzenet útvonalak és az egyedi végpontok használata az eszköz – felhő üzeneteket](iot-hub-devguide-messages-read-custom.md).
