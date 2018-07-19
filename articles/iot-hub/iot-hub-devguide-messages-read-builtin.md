---
title: Megismerheti az Azure IoT Hub beépített végpont |} A Microsoft Docs
description: Fejlesztői útmutató – ismerteti, hogyan használható a beépített, Event Hub-kompatibilis végpont toread eszköz – felhő üzeneteket.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 912bb0dd3e48e53134ad848119ae7428b380b88d
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39124936"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>A beépített végpontról eszköz – felhő üzenetek olvasása

Alapértelmezés szerint üzenetek irányíthatók át a beépített szolgáltatás felé néző végpont (**üzenetek/események**), amely kompatibilis a rendszer [az Event Hubs][lnk-event-hubs]. Ez a végpont a jelenleg csak közzétett használja a [AMQP] [ lnk-amqp] 5671-es port-protokoll. Az IoT hub mutatja meg, hogy szabályozza a beépített üzenetkezelési Event Hub-kompatibilis végpont a következő tulajdonságok **üzenetek/események**.

| Tulajdonság            | Leírás |
| ------------------- | ----------- |
| **Partíciók száma** | Ez a tulajdonság beállítva a létrehozáskor számának meghatározásához [partíciók] [ lnk-event-hub-partitions] az eszközről a felhőbe eseményfeldolgozást. |
| **Megőrzési idő**  | Ez a tulajdonság határozza meg, mennyi ideig maradnak, üzeneteket az IoT Hub napban. Az alapértelmezett érték egy nap, de a hét nap növelhető. |

Az IoT Hub lehetővé teszi olyan kezelése a beépített eszköz-felhő a felhasználói csoportok végpont kap.

Alapértelmezés szerint a beépített végpont írt összes üzenetet, egy üzenet-útválasztási szabály explicit módon nem megfelelő. Ha letiltja ezt az útvonalat tartalék, bármely üzenet-útválasztási szabályok nem kifejezetten megfelelő üzeneteket a rendszer elveti.

Módosíthatja a megőrzési időtartam vagy programozott módon használja a [az IoT Hub erőforrás-szolgáltató REST API-k][lnk-resource-provider-apis], vagy a [az Azure portal] [ lnk-management-portal].

Az IoT Hub tesz a **üzenetek/események** beépített végpont a háttérbeli szolgáltatásokhoz a hub által fogadott eszköz a felhőbe irányuló üzenetek olvasásához. Ez a végpont az Event Hub-kompatibilis, amely lehetővé teszi, hogy az módszerek bármelyikét használhatja az Event Hubs szolgáltatás támogatja az üzenetek olvasásához.

## <a name="read-from-the-built-in-endpoint"></a>Olvassa el a beépített végpontról

Használatakor a [Azure Service Bus SDK for .NET] [ lnk-servicebus-sdk] vagy a [az Event Hubs - Event Processor Host][lnk-eventprocessorhost], használhat bármilyen az IoT Hub-kapcsolat karakterláncok a megfelelő engedélyekkel. Ezután **üzenetek/események** Eseményközpont nevével.

Amikor az SDK-k (vagy termékintegrációk), amely észleli az IoT Hub, le kell kérnie egy Event Hub-kompatibilis végpont és az Event Hub-kompatibilis nevet:

1. Jelentkezzen be a [az Azure portal] [ lnk-management-portal] , és keresse meg az IoT hubot.
1. Kattintson a **Végpontok** elemre.
1. Az a **beépített végpontokról** területén kattintson **események**. 
1. Megnyílik egy tulajdonságok oldal, amely tartalmazza a következő értékeket: **Event Hub-kompatibilis végpont**, **Event Hub-kompatibilis nevet**, **partíciók**,  **Megőrzési idő**, és **fogyasztói csoportok**.

    ![Eszközről-a-felhőbe típusú üzenetek beállításai][img-eventhubcompatible]

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

IoT Hub-végpontok kapcsolatos további információkért lásd: [IoT Hub-végpontok][lnk-endpoints].

A [útmutatóink] [ lnk-get-started] bemutatják, hogyan eszköz – felhő üzeneteket küldhet a szimulált eszközök és a beépített végpont érkező üzenetek olvasásához. További részletekért tekintse meg a [folyamat az IoT Hub eszköz – felhő üzenetek útvonalak] [ lnk-d2c-tutorial] oktatóanyag.

Ha szeretné az eszköz a felhőbe irányuló üzenetek átirányítása egyéni végpontok, [üzenet útvonalak és az egyedi végpontok használata az eszköz – felhő üzeneteket][lnk-custom].

[img-eventhubcompatible]: ./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png

[lnk-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-get-started]: quickstart-send-telemetry-node.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-management-portal]: https://portal.azure.com
[lnk-d2c-tutorial]: tutorial-routing.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-features.md#partitions
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx
[lnk-amqp]: https://www.amqp.org/
