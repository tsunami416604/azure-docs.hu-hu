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
ms.openlocfilehash: af0b819c6c60835089c174a1f9f7c3a6215e362c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956961"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Üzenet útvonalak és az egyedi végpontok használata az eszköz a felhőbe irányuló üzenetek

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az IoT Hub [üzenet-útválasztása](iot-hub-devguide-routing-query-syntax.md) lehetővé teszi a felhasználók eszköz – felhő üzeneteket irányíthatja a szolgáltatás felé néző végpontok. Útválasztás is lehetőséget biztosít egy lekérdezési szűrje az adatokat az Útválasztás, a végpontok előtt. Minden útválasztási lekérdezés konfigurálja a következő tulajdonságokkal rendelkezik:

| Tulajdonság      | Leírás |
| ------------- | ----------- |
| **Name (Név)**      | Az egyedi név, amely azonosítja a lekérdezést. |
| **Forrás**    | Az adatfolyam intézkedni forrása. Ha például eszköz telemetriai adatokat. |
| **a feltétel** | A lekérdezési kifejezésben az útválasztási lekérdezés, amely vonatkozóan üzenettulajdonságok alkalmazás, Rendszertulajdonságok, üzenettörzs, device twin címkék és állapítsa meg, hogy a végpont egyezést eszköz-ikertulajdonságok fut le. Hozhat létre, a lekérdezés kapcsolatos további információkért lásd: a lásd [üzenet útválasztási lekérdezési szintaxis](iot-hub-devguide-routing-query-syntax.md) |
| **Végpont**  | A végpont arról, hogy az IoT Hub hová küldi az üzeneteket, amelyek megfelelnek a lekérdezés neve. Azt javasoljuk, hogy a végpont ugyanabban a régióban, mint az IoT hub kiválasztása. |

Egy üzenet, amelyben az IoT Hub eset kézbesíti az üzenetet a végponthoz társított minden egyező lekérdezés több útválasztási lekérdezés podmínky előfordulhat, hogy egyezik. Az IoT Hub adattömbökbe megfelelően automatikusan is az üzenetek kézbesítését, így ha egy üzenet, amelyek ugyanahhoz a célhoz több lekérdezés megegyezik, csak írás után, hogy a cél.

## <a name="endpoints-and-routing"></a>Végpontok és útválasztási

Az IoT hub tartalmaz egy alapértelmezett [beépített végpont][lnk-built-in]. Az előfizetés többi szolgáltatása a hub kapcsolásával üzeneteknek az egyedi végpontok is létrehozhat. Az IoT Hub jelenleg támogatja az Azure Storage-tárolók, az Event Hubs, Service Bus-üzenetsorok és Service Bus-üzenettémakörök egyéni Végpontokként.

Útválasztási és egyéni végpontok használata esetén üzenetek jelenjenek meg csak a beépített végpont, ha minden lekérdezés nem egyeznek. A beépített végpont, valamint egy egyéni végpont esetleges szabályozási hiányosságok elhárítását kézbesíti az üzeneteket, adjon hozzá egy útvonalat, amely üzeneteket küld a **események** végpont.

> [!NOTE]
> Az IoT Hub csak akkor támogatja a Azure Storage-tárolók, blobok írja az adatokat.

> [!WARNING]
> A Service Bus üzenetsorainak és témáinak **munkamenetek** vagy **duplikáltelem-észlelési** engedélyezve van, az egyedi végpontok nem támogatottak.

Az IoT Hub az egyedi végpontok létrehozásával kapcsolatos további információkért lásd: [IoT Hub-végpontok][lnk-devguide-endpoints].

Egyéni végpontok olvasásakor kapcsolatos további információkért lásd:

* A olvasásakor [Azure Storage-tárolók][lnk-getstarted-storage].
* A olvasásakor [az Event Hubs][lnk-getstarted-eh].
* A olvasásakor [Service Bus-üzenetsorok][lnk-getstarted-queue].
* A olvasásakor [Service Bus-üzenettémakörök][lnk-getstarted-topic].

### <a name="next-steps"></a>További lépések

* IoT Hub-végpontok kapcsolatos további információkért lásd: [IoT Hub-végpontok][lnk-devguide-endpoints].
* Útválasztási lekérdezések meghatározásához használhatja a lekérdezési nyelv kapcsolatos további információkért lásd: [üzenet útválasztási lekérdezési szintaxis](iot-hub-devguide-routing-query-syntax.md).
* A [folyamat az IoT Hub eszköz – felhő üzenetek útvonalak] [ lnk-d2c-tutorial] oktatóanyag bemutatja, hogyan használhatja az útválasztási lekérdezések és az egyedi végpontok.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: tutorial-routing.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
