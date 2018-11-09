---
title: Megismerheti az Azure IoT Hub beépített végpont |} A Microsoft Docs
description: Fejlesztői útmutató – útmutató a beépített, Event Hub-kompatibilis végpont használata az eszköz a felhőbe irányuló üzenetek olvasását.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 02ea4b94f8d1442360bebb36fdbba13d973f8555
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242415"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Az eszközről a felhőbe irányuló üzenetek beolvasása a beépített végpontról

Alapértelmezés szerint üzenetek irányíthatók át a beépített szolgáltatás felé néző végpont (**üzenetek/események**), amely kompatibilis a rendszer [az Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/
). Ez a végpont a jelenleg csak közzétett használja a [AMQP](https://www.amqp.org/) 5671-es port-protokoll. Az IoT hub mutatja meg, hogy szabályozza a beépített üzenetkezelési Event Hub-kompatibilis végpont a következő tulajdonságok **üzenetek/események**.

| Tulajdonság            | Leírás |
| ------------------- | ----------- |
| **Partíciók száma** | Ez a tulajdonság beállítva a létrehozáskor számának meghatározásához [partíciók](../event-hubs/event-hubs-features.md#partitions) az eszközről a felhőbe eseményfeldolgozást. |
| **Megőrzési idő**  | Ez a tulajdonság határozza meg, mennyi ideig maradnak, üzeneteket az IoT Hub napban. Az alapértelmezett érték egy nap, de a hét nap növelhető. |

Az IoT Hub lehetővé teszi olyan kezelése a beépített eszköz-felhő a felhasználói csoportok végpont kap.

Ha használ [üzenet-útválasztása](iot-hub-devguide-messages-d2c.md) és a [tartalék útvonal](iot-hub-devguide-messages-d2c.md#fallback-route) van engedélyezve van, egy lekérdezést minden olyan útvonal a nem megfelelő összes üzenetet írja a beépített végpont. Ha letiltja ezt az útvonalat tartalék, a rendszer elveti, amelyek nem egyeznek meg a lekérdezés üzeneteket.

Módosíthatja a megőrzési időtartam vagy programozott módon használja a [az IoT Hub erőforrás-szolgáltató REST API-k](/rest/api/iothub/iothubresource), vagy a [az Azure portal](https://portal.azure.com).

Az IoT Hub tesz a **üzenetek/események** beépített végpont a háttérbeli szolgáltatásokhoz a hub által fogadott eszköz a felhőbe irányuló üzenetek olvasásához. Ez a végpont az Event Hub-kompatibilis, amely lehetővé teszi, hogy az módszerek bármelyikét használhatja az Event Hubs szolgáltatás támogatja az üzenetek olvasásához.

## <a name="read-from-the-built-in-endpoint"></a>Olvassa el a beépített végpontról

Használatakor a [Azure Service Bus SDK for .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) vagy a [az Event Hubs - Event Processor Host](..//event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md), használhatja az IoT Hub kapcsolati karakterláncokat a megfelelő engedélyekkel. Ezután **üzenetek/események** Eseményközpont nevével.

Amikor az SDK-k (vagy termékintegrációk), amely észleli az IoT Hub, le kell kérnie egy Event Hub-kompatibilis végpont és az Event Hub-kompatibilis nevet:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az IoT hubot.

2. Kattintson a **beépített végpontokról**.

3. A **események** szakasz tartalmazza a következő értékeket: **Event Hub-kompatibilis végpont**, **Event Hub-kompatibilis nevet**, **partíciók**, **Megőrzési időtartama**, és **fogyasztói csoportok**.

    ![Eszközről-a-felhőbe típusú üzenetek beállításai](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

Az IoT Hub SDK-t igényel az IoT Hub végpont neve, amely **üzenetek/események** alatt látható módon **végpontok**.

Ha az SDK-t használ van szüksége egy **állomásnév** vagy **Namespace** értéket, távolítsa el a rendszer a a **Event Hub-kompatibilis végpont**. Például, ha az Event Hub-kompatibilis végpont **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, a **állomásnév** lenne  **iothub-ns-myiothub-1234.servicebus.windows.net**. A **Namespace** lenne **iothub-ns-myiothub-1234**.

Minden megosztott elérési házirendet, amely rendelkezik használhatja a **ServiceConnect** engedélyeket a megadott Event hubhoz való csatlakozáshoz.

Ha hozhat létre egy Eseményközpont kapcsolati sztringjének korábbi információk segítségével van szüksége, használja a következő mintának:

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

SDK-k és integrációk használható, amely közzéteszi az IoT Hub Event Hub-kompatibilis végpontokkal rendelkező tartalmazza az elemek a következők:

* [Az Event Hubs Java ügyfél](https://github.com/Azure/azure-event-hubs-java).
* [Az Apache Storm spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Megtekintheti a [forrás spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) a Githubon.
* [Az Apache Spark-integráció](../hdinsight/spark/apache-spark-eventhub-streaming.md).

## <a name="next-steps"></a>További lépések

* IoT Hub-végpontok kapcsolatos további információkért lásd: [IoT Hub-végpontok](iot-hub-devguide-endpoints.md).

* A [útmutatóink](quickstart-send-telemetry-node.md) bemutatják, hogyan eszköz – felhő üzeneteket küldhet a szimulált eszközök és a beépített végpont érkező üzenetek olvasásához. 

További részletekért tekintse meg a [folyamat az IoT Hub eszköz – felhő üzenetek útvonalak](tutorial-routing.md) oktatóanyag.

* Ha szeretné az eszköz a felhőbe irányuló üzenetek átirányítása egyéni végpontok, [üzenet útvonalak és az egyedi végpontok használata az eszköz – felhő üzeneteket](iot-hub-devguide-messages-read-custom.md).