---
title: Az Azure IoT Hub egyéni végpontjainak megismerése | Microsoft dokumentumok
description: Fejlesztői útmutató – útválasztási lekérdezések használata az eszközről a felhőbe irányuló üzenetek egyéni végpontokhoz történő továbbításához.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: e5e92c40cef15e99431dc9652820c71e87935f67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61244344"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Üzenetútvonalak és egyéni végpontok használata az eszközről a felhőbe irányuló üzenetekhez

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az IoT Hub [üzenet-útválasztáslehetővé](iot-hub-devguide-routing-query-syntax.md) teszi a felhasználók számára, hogy az eszköz-felhő üzeneteket a szolgáltatás felé néző végpontok. Az Útválasztás lekérdezési lehetőséget is biztosít az adatok szűrésére, mielőtt a végpontokhoz irányítanák. Minden konfigurált útválasztási lekérdezés a következő tulajdonságokkal rendelkezik:

| Tulajdonság      | Leírás |
| ------------- | ----------- |
| **Név**      | A lekérdezést azonosító egyedi név. |
| **Forrás**    | A reagáló adatfolyam eredete. Például az eszköz telemetriai adatok. |
| **Állapot** | Az útválasztási lekérdezés, amely az üzenetalkalmazás tulajdonságain, a rendszertulajdonságain, az üzenettörzsén, az ikereszköz-címkéken és az ikereszköz tulajdonságain fut, és megállapítja, hogy megegyezik-e a végpont. A lekérdezések létrehozásáról további információt az [üzenet-útválasztási lekérdezés szintaxisa című](iot-hub-devguide-routing-query-syntax.md) témakörben talál. |
| **Végpont**  | Annak a végpontnak a neve, ahol az IoT Hub a lekérdezésnek megfelelő üzeneteket küld. Azt javasoljuk, hogy válasszon egy végpontot ugyanabban a régióban, mint az IoT hub. |

Egyetlen üzenet több útválasztási lekérdezés esetén is megfelelhet a feltételnek, amely esetben az IoT Hub az üzenetet az egyes egyező lekérdezésekhez társított végpontnak kézbesíti. Az IoT Hub is automatikusan deduplicates üzenet kézbesítése, így ha egy üzenet megfelel több lekérdezések, amelyek ugyanazt a célt, csak egyszer írt, hogy a cél.

## <a name="endpoints-and-routing"></a>Végpontok és útválasztás

Az IoT-központ alapértelmezett [beépített végponttal rendelkezik.](iot-hub-devguide-messages-read-builtin.md) Egyéni végpontokat hozhat létre az üzenetek továbbításához az előfizetésben lévő más szolgáltatások hubhoz csatolásával. Az IoT Hub jelenleg egyéni végpontként támogatja az Azure Storage-tárolókat, az Event Hubs-t, a Service Bus-várólistákat és a Service Bus-témaköröket.

Útválasztási és egyéni végpontok használata esetén az üzenetek csak akkor kerülnek kézbesítésre a beépített végponthoz, ha azok nem egyeznek meg egyetlen lekérdezéssel sem. Üzenetek kézbesítése a beépített végpont, valamint egy egyéni végpont, adjon hozzá egy útvonalat, amely üzeneteket küld a beépített **események** végpont.

> [!NOTE]
> * Az IoT Hub csak az Azure Storage-tárolókba blobként való adatok írását támogatja.
> * A Service Bus-várólisták és a **munkamenetek** vagy **a duplikáltelem-észlelés** engedélyezésével rendelkező témakörök egyéni végpontként nem támogatottak.

Az egyéni végpontok IoT Hubban való létrehozásáról az [IoT Hub végpontjai](iot-hub-devguide-endpoints.md)című témakörben talál további információt.

Az egyéni végpontok olvasásáról a következő témakörben talál további információt:

* Olvasás [az Azure Storage-tárolókból.](../storage/blobs/storage-blobs-introduction.md)

* Olvasás az [Event Hubs-ból](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* Olvasás a [Service Bus várólistáiból](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Olvasás a [Service Bus témaköreiből](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>További lépések

* Az IoT Hub végpontjairól az [IoT Hub végpontjai](iot-hub-devguide-endpoints.md)című témakörben talál további információt.

* Az útválasztási lekérdezések definiálásához használt lekérdezési nyelvről az [Üzenetküldés lekérdezésszintaxiscímű témakörben](iot-hub-devguide-routing-query-syntax.md)talál további információt.

* Az [IoT Hub-eszközök közötti eszközök](tutorial-routing.md) közötti üzenetek feldolgozása útvonalak használatával oktatóanyag bemutatja, hogyan használhatja útválasztási lekérdezések és egyéni végpontok használatával.