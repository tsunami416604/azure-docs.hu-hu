---
title: Ismerje meg az Azure IoT Hub beépített végpontja | Microsoft dokumentumok
description: Fejlesztői útmutató – ismerteti, hogyan használhatja a beépített, Event Hub-kompatibilis végpontot az eszközről a felhőbe irányuló üzenetek olvasására.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: a2674ca0f4808cb6f01781565e57369ca5d3ac37
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478790"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Az eszközről a felhőbe irányuló üzenetek beolvasása a beépített végpontról

Alapértelmezés szerint az üzenetek az [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)rendszerrel kompatibilis, beépített szolgáltatásnéző végpontra **(üzenetek/események)** kerülnek. Ez a végpont jelenleg csak az 5671-es [portAQP](https://www.amqp.org/) protokolljával érhető el. Az IoT-központ a következő tulajdonságokat teszi elérhetővé, hogy a beépített Event Hub-kompatibilis üzenetkezelési **végpontüzenetek/események**vezérléséhez.

| Tulajdonság            | Leírás |
| ------------------- | ----------- |
| **Partíciók száma** | Állítsa be ezt a tulajdonságot a létrehozáskor az eszközről a felhőbe irányuló eseménybetöltés [partícióinak](../event-hubs/event-hubs-features.md#partitions) számának meghatározásához. |
| **Megőrzési idő**  | Ez a tulajdonság határozza meg, hogy az IoT Hub mennyi ideig őrizze meg az üzeneteket napokban. Az alapértelmezett érték egy nap, de hét napra növelhető. |

Az IoT Hub legfeljebb 7 napig engedélyezi az adatmegőrzést a beépített Event Hubs-ban. Beállíthatja a megőrzési idő létrehozása során az IoT Hub létrehozása során. Az IoT Hub adatmegőrzési ideje az IoT hub szintjétől és az egység típusától függ. A méret szempontjából a beépített Event Hubs megtarthatja a maximális üzenetmérettel rendelkező üzeneteket legalább 24 órányi kvótáig. Például 1 S1 egység IoT Hub elegendő tárhelyet biztosít legalább 400K üzenetek egyenként 4k méretű. Ha az eszközök kisebb üzeneteket küldenek, előfordulhat, hogy hosszabb ideig (legfeljebb 7 napig) maradnak meg a ttól függően, hogy mennyi tárhelyet használnak fel. Garantáljuk, hogy az adatok at a megadott megőrzési idő legalább. Az üzenetek lejárnak, és nem lesznek elérhetők a megőrzési idő lejárta után. 

Az IoT Hub azt is lehetővé teszi, hogy a beépített eszközről a felhőbe irányuló fogadási végponton kezelje a fogyasztói csoportokat. Az Egyes IoT Hub-szolgáltatásokhoz legfeljebb 20 fogyasztói csoport lehet.

Ha [üzenet-útválasztást](iot-hub-devguide-messages-d2c.md) használ, és a [tartalék útvonal](iot-hub-devguide-messages-d2c.md#fallback-route) engedélyezve van, minden olyan üzenet, amely nem egyezik meg a lekérdezés bármely útvonalon, a beépített végpontra kerül. Ha letiltja ezt a tartalék útvonalat, a rendszer elejti az olyan üzeneteket, amelyek nem felelnek meg egyetlen lekérdezésnek sem.

Módosíthatja az adatmegőrzési időt, akár programozott módon az [IoT Hub erőforrás-szolgáltató REST API-k](/rest/api/iothub/iothubresource)használatával, vagy az [Azure Portal](https://portal.azure.com)használatával.

Az IoT Hub elérhetővé teszi a háttérszolgáltatások beépített **végpontját,** hogy olvassa el a hub által fogadott eszközről felhőbe érkező üzeneteket. Ez a végpont az Event Hub-kompatibilis, amely lehetővé teszi, hogy az Event Hubs szolgáltatás által az üzenetek olvasásához támogatott mechanizmusok bármelyikét használja.

## <a name="read-from-the-built-in-endpoint"></a>Olvasás a beépített végpontról

Egyes termékintegrációk és Az Event Hubs SDK-k tisztában vannak az IoT Hub, és lehetővé teszi, hogy az IoT hub szolgáltatás kapcsolati karakterlánca a beépített végponthoz való csatlakozáshoz.

Ha Az Event Hubs SDK-k vagy a termék integrációk, amelyek nem ismerik az IoT Hub, szüksége van egy Event Hub-kompatibilis végpont és Event Hub-kompatibilis nevet. Ezeket az értékeket a portálról a következőképpen szerezheti be:

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az IoT-központot.

2. Kattintson **a Beépített végpontok elemre.**

3. Az **Események** szakasz a következő értékeket tartalmazza: **Partíciók**, **Eseményközpont-kompatibilis név**, Event **Hub-kompatibilis végpont**, **Megőrzési idő**és Fogyasztói **csoportok**.

    ![Az eszközök ről a felhőbe való beállítások](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

A portálon az Event Hub-kompatibilis végpontmező egy teljes Event Hubs kapcsolati karakterláncot tartalmaz, amely így néz ki: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/; SharedAccessKeyName=iothubowner; SharedAccessKey=keykeykeykeykey=; EntityPath=iothub-ehub-abcd-1234-123456**. Ha a használt SDK-hoz más értékek szükségesek, akkor a következők lesznek:

| Név | Érték |
| ---- | ----- |
| Végpont | sb://abcd1234namespace.servicebus.windows.net/ |
| Gazdanév | abcd1234namespace.servicebus.windows.net |
| Névtér | abcd1234névtér |

Ezután használhatja a megosztott hozzáférési szabályzatot, amely rendelkezik a **ServiceConnect** engedélyekkel a megadott eseményközponthoz való csatlakozáshoz.

Az SDK-k segítségével csatlakozhat a beépített Event Hub-kompatibilis végpont, amely az IoT Hub elérhetővé teszi a következők:

| Nyelv | SDK | Példa | Megjegyzések |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Első lépések](quickstart-send-telemetry-dotnet.md) | Az Event Hubs-kompatibilis adatokat használja |
 Java | https://github.com/Azure/azure-event-hubs-java | [Első lépések](quickstart-send-telemetry-java.md) | Az Event Hubs-kompatibilis adatokat használja |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Első lépések](quickstart-send-telemetry-node.md) | IoT Hub kapcsolati karakterláncot használ |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | IoT Hub kapcsolati karakterláncot használ |

A beépített Event Hub-kompatibilis végponttal használható termékintegrációk a következők:

* [Az Azure Functions .](https://docs.microsoft.com/azure/azure-functions/) Lásd: [Adatok feldolgozása az IoT Hubból az Azure Functions funkcióval.](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/)
* [Az Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)szolgáltatást. Lásd: [Adatok streamelése a Stream Analytics szolgáltatásba.](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)
* [Idősorozat-elemzési adatok](https://docs.microsoft.com/azure/time-series-insights/). Lásd: [IoT hub-eseményforrás hozzáadása a Time Series Insights-környezethez.](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md)
* [Apache Storm kifolyó](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Megtekintheti a [spout forrás](https://github.com/apache/storm/tree/master/external/storm-eventhubs) a GitHubon.
* [Apache Spark-integráció](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Az Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>További lépések

* Az IoT Hub végpontjairól az [IoT Hub végpontjai](iot-hub-devguide-endpoints.md)című témakörben talál további információt.

* A [rövid útmutatók bemutatják,](quickstart-send-telemetry-node.md) hogyan küldhet eszközről felhőbe üzeneteket a szimulált eszközökről, és hogyan olvashatja el az üzeneteket a beépített végpontról. 

További részletekért tekintse meg az [IoT Hub eszközről felhőbe irányuló üzenetek feldolgozása útvonalak használatával](tutorial-routing.md) oktatóanyag.

* Ha az eszközről a felhőbe irányuló üzeneteket egyéni végpontokra szeretné irányítani, olvassa el [az Üzenetútvonalak és egyéni végpontok használata az eszközről a felhőbe irányuló üzenetekhez című témakört.](iot-hub-devguide-messages-read-custom.md)
