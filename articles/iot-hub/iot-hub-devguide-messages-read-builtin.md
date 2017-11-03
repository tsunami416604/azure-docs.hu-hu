---
title: "Az Azure IoT Hub beépített végpont megértése |} Microsoft Docs"
description: "Fejlesztői útmutató - a beépített, Event Hub-kompatibilis végpont toread eszközről a felhőbe üzenetek használatát ismerteti."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: dobett
ms.openlocfilehash: f864ca586e8e607168ae7b46a1eaa297eca1cfb8
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Eszköz a felhőbe küldött üzeneteket beolvasni a beépített végpont

Alapértelmezés szerint üzenetek átirányítja a beépített service felé néző végpont (**üzenetek/események**), amely kompatibilis a rendszer [Event Hubs][lnk-event-hubs]. Ehhez a végponthoz jelenleg csak kitett használja a [AMQP] [ lnk-amqp] protokoll az 5671-es port. Az IoT-központ mutatja meg a következő tulajdonságok lehetővé teszik a beépített Event Hub-kompatibilis üzenetkezelési végpont **üzenetek/események**.

| Tulajdonság            | Leírás |
| ------------------- | ----------- |
| **Partíciók száma** | Ez a tulajdonság beállítása a létrehozásakor meghatározhatja a számát [partíciók] [ lnk-event-hub-partitions] eszközről a felhőbe esemény szempontjából. |
| **Megőrzési idő**  | Ez a tulajdonság meghatározza, hogy az üzenetek megmaradnak az IoT-központ által nap. Az alapértelmezett érték egy nap, de hét napos növelhető. |

Az IoT-központ is lehetővé teszi kezelését a beépített eszközről-a-felhőbe a fogyasztói csoportok végpont fogadására.

Alapértelmezés szerint minden üzenetet, amely explicit módon a szabálynak üzenet útválasztási a beépített végpont kerülnek. Ha letiltja ezt a tartalék útvonalat, eldobott üzenetek, amely explicit módon nem egyezik a minden üzenet útválasztási szabályokat.

Módosíthatja a megőrzési időtartam, vagy programozott módon a a [IoT-központ erőforrás-szolgáltató REST API-k][lnk-resource-provider-apis], vagy a [Azure-portálon][lnk-management-portal].

Az IoT-központ elérhetővé teszi a **üzenetek/események** olvashatja az eszközről a felhőbe a központ fogadja a háttér-szolgáltatások beépített végpont. Ezt a végpontot az esemény lehetővé teszi, hogy mechanizmusok az Event Hubs szolgáltatás Hub-kompatibilis támogatja az üzenetek olvasásához.

## <a name="read-from-the-built-in-endpoint"></a>A beépített végpont olvasásakor

Használatakor a [Azure Service Bus SDK for .NET] [ lnk-servicebus-sdk] vagy a [Event Hubs - Event Processor Host][lnk-eventprocessorhost], minden IoT-központ kapcsolati karakterláncok használatával a megfelelő engedélyekkel. Ezután **üzenetek/események** az Eseményközpont neveként.

Amikor az SDK-k (vagy a termék integrációja), amely nem tudnak a IoT-központ, be kell olvasni az Event Hub-kompatibilis végpont és Event Hub-kompatibilis nevét a IoT hub beállításai közül:

1. Jelentkezzen be a [Azure-portálon] [ lnk-management-portal] , és keresse meg az IoT hub.
1. Kattintson a **Végpontok** elemre.
1. Az a **beépített végpontok** kattintson **események**. 
1. Megnyílik egy tulajdonságok oldal, amely tartalmazza a következő értékek: **Event Hub-kompatibilis végpont**, **Event Hub-kompatibilis neve**, **partíciók**,  **Megőrzési idő**, és **fogyasztói csoportok**.

    ![Eszköz-felhő beállításai][img-eventhubcompatible]

Az IoT Hub SDK igényel az IoT-központ végpont neve, amely **üzenetek/események** alatt látható **végpontok**.

Ha az SDK-t használ a **állomásnév** vagy **Namespace** értékét, akkor távolítsa el a rendszer a **Event Hub-kompatibilis végpont**. Például, ha az Event Hub-kompatibilis végpont **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, a **állomásnév** lenne  **IOT hubbal-ns-myiothub-1234.servicebus.windows.net**. A **Namespace** lenne **IOT hubbal-ns-myiothub-1234**.

Ezután használhatja a minden megosztott elérési házirendet, amely rendelkezik a **ServiceConnect** engedéllyel a megadott Eseményközpontba való kapcsolódáshoz.

Ha egy Eseményközpontba kapcsolati karakterlánc összeállítása a korábbi információk segítségével van szüksége, használja a következő mintát:

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

Az SDK-k és az IoT-központ elérhetővé tévő Event Hub-kompatibilis végpontokon használható Integrációk tartalmazza az alábbi listában szereplő elemeket:

* [Az Event Hubs Java ügyfél](https://github.com/Azure/azure-event-hubs-java).
* [Apache Storm spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Megtekintheti a [forrás spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) a Githubon.
* [Apache Spark-integráció](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md).

## <a name="next-steps"></a>Következő lépések

IoT-központok végpontjai kapcsolatos további információkért lásd: [IoT-központok végpontjai][lnk-endpoints].

A [Ismerkedés] [ lnk-get-started] oktatóprogramok bemutatják a eszközről a felhőbe üzeneteket küldhet a szimulált eszköz és az üzenetek olvasásakor a beépített végpont. További részletekért tekintse meg a [folyamat IoT Hub eszközről a felhőbe üzenetek útvonalak] [ lnk-d2c-tutorial] oktatóanyag.

Ha szeretné továbbítani az eszközről a felhőbe üzenetek egyéni végpontokat, lásd: [üzenet útvonalak és egyéni végpontokat eszközről a felhőbe üzeneteiben használt][lnk-custom].

[img-eventhubcompatible]: ./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png

[lnk-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-management-portal]: https://portal.azure.com
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-features.md#partitions
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx
[lnk-amqp]: https://www.amqp.org/
