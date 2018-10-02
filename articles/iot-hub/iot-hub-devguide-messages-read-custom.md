---
title: Megismerheti az Azure IoT Hub az egyedi végpontok |} A Microsoft Docs
description: Fejlesztői útmutató – az egyedi végpontok juthatnak eszköz – felhő üzenetek útválasztási lekérdezések segítségével.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.author: dobett
ms.openlocfilehash: bbd5058be502839f83db484136d1c97bac4a3d79
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585951"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Üzenet útvonalak és az egyedi végpontok használata az eszköz a felhőbe irányuló üzenetek

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az IoT Hub [üzenet-útválasztása](iot-hub-devguide-routing-query-syntax.md) lehetővé teszi a felhasználók eszköz – felhő üzeneteket irányíthatja a szolgáltatás felé néző végpontok. Útválasztás is lehetőséget biztosít egy lekérdezési szűrje az adatokat az Útválasztás, a végpontok előtt. Minden útválasztási lekérdezés konfigurálja a következő tulajdonságokkal rendelkezik:

| Tulajdonság      | Leírás |
| ------------- | ----------- |
| **Name (Név)**      | Az egyedi név, amely azonosítja a lekérdezést. |
| **Forrás**    | Az adatfolyam intézkedni forrása. Ha például eszköz telemetriai adatokat. |
| **A feltétel** | A lekérdezési kifejezésben az útválasztási lekérdezés, amely az üzenet alkalmazás tulajdonságai, Rendszertulajdonságok, üzenet szövegét, device twin címkék és állapítsa meg, hogy a végpont egyezést eszköz-ikertulajdonságok vonatkozóan fut le. Hozhat létre, a lekérdezés kapcsolatos további információkért lásd: a lásd [üzenet útválasztási lekérdezési szintaxis](iot-hub-devguide-routing-query-syntax.md) |
| **Végpont**  | A végpont arról, hogy az IoT Hub hová küldi az üzeneteket, amelyek megfelelnek a lekérdezés neve. Azt javasoljuk, hogy a végpont ugyanabban a régióban, mint az IoT hub kiválasztása. |

Egy üzenet, amelyben az IoT Hub eset kézbesíti az üzenetet a végponthoz társított minden egyező lekérdezés több útválasztási lekérdezés podmínky előfordulhat, hogy egyezik. Az IoT Hub adattömbökbe megfelelően automatikusan is az üzenetek kézbesítését, így ha egy üzenet, amelyek ugyanahhoz a célhoz több lekérdezés megegyezik, csak írás után, hogy a cél.

## <a name="endpoints-and-routing"></a>Végpontok és útválasztási

Az IoT hub tartalmaz egy alapértelmezett [beépített végpont](iot-hub-devguide-messages-read-builtin.md). Az előfizetés többi szolgáltatása a hub kapcsolásával üzeneteknek az egyedi végpontok is létrehozhat. Az IoT Hub jelenleg támogatja az Azure Storage-tárolók, az Event Hubs, Service Bus-üzenetsorok és Service Bus-üzenettémakörök egyéni Végpontokként.

Útválasztási és egyéni végpontok használata esetén üzenetek jelenjenek meg csak a beépített végpont, ha minden lekérdezés nem egyeznek. A beépített végpont, valamint egy egyéni végpont esetleges szabályozási hiányosságok elhárítását kézbesíti az üzeneteket, adjon hozzá egy útvonalat, amely üzeneteket küld a **események** végpont.

> [!NOTE]
> * Az IoT Hub csak akkor támogatja a Azure Storage-tárolók, blobok írja az adatokat.
> * A Service Bus üzenetsorainak és témáinak **munkamenetek** vagy **duplikáltelem-észlelési** engedélyezve van, az egyedi végpontok nem támogatottak.

Az IoT Hub az egyedi végpontok létrehozásával kapcsolatos további információkért lásd: [IoT Hub-végpontok](iot-hub-devguide-endpoints.md).

Egyéni végpontok olvasásakor kapcsolatos további információkért lásd:

* A olvasásakor [Azure Storage-tárolók](../storage/blobs/storage-blobs-introduction.md).

* A olvasásakor [az Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* A olvasásakor [Service Bus-üzenetsorok](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* A olvasásakor [Service Bus-üzenettémakörök](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>További lépések

* IoT Hub-végpontok kapcsolatos további információkért lásd: [IoT Hub-végpontok](iot-hub-devguide-endpoints.md).

* Útválasztási lekérdezések meghatározásához használhatja a lekérdezési nyelv kapcsolatos további információkért lásd: [üzenet útválasztási lekérdezési szintaxis](iot-hub-devguide-routing-query-syntax.md).

* A [folyamat az IoT Hub eszköz – felhő üzenetek útvonalak](tutorial-routing.md) oktatóanyag bemutatja, hogyan használhatja az útválasztási lekérdezések és az egyedi végpontok.