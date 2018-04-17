---
title: Azure IoT Hub egyéni végpontokat megértése |} Microsoft Docs
description: Fejlesztői útmutató - útválasztási szabályok használatával egyéni végpontokkal való eszközről a felhőbe üzenetek továbbításához.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2018
ms.author: dobett
ms.openlocfilehash: 3d54da43141dc2bdf34c9f71adc41dc7cf24ff10
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Üzenet útvonalak és egyéni végpontokat használja az eszköz a felhőbe küldött üzeneteket

Az IoT-központ lehetővé teszi a továbbításához [eszköz a felhőbe küldött üzeneteket] [ lnk-device-to-cloud] az IoT-központ szolgáltatás felé néző végpontok üzenet tulajdonságai alapján. Útválasztási szabályokat a munkakörnyezetnek üzenetek küldése, ha szükségük nyissa meg a további szolgáltatásainak vagy egyéni kód nélkül. Egyes útválasztási szabályokat konfigurál a következő tulajdonságokkal rendelkeznek:

| Tulajdonság      | Leírás |
| ------------- | ----------- |
| **Name (Név)**      | A szabály azonosító egyedi név. |
| **Forrás**    | Az adatfolyamot kell bírálni a forrása. Például telemetriát. |
| **Az állapot** | Az útválasztási szabály, amely az üzenet fejlécek és body futtatni, és határozza meg, ha a végpont egyezés lekérdezési kifejezésben. Útvonal feltétel létrehozásával kapcsolatos további információkért tekintse meg a [referencia - lekérdezési nyelv eszköz twins és feladatok][lnk-devguide-query-language]. |
| **végpont**  | A végpont, ahol az IoT-központ elküldi a feltételnek megfelelő üzenetek neve. Végpontok az IoT hub ugyanabban a régióban kell lennie, ellenkező esetben meg kell felszámítani kereszt-régió írási műveleteket. |

Egyetlen üzenet előfordulhat, hogy felel meg a feltétel több útválasztási szabályokat, amelyben eset az IoT-központ kézbesíti az üzenetet minden egyező szabályt társított végpont. Az IoT-központ is automatikusan deduplicates üzenetkézbesítést, így ha egy üzenet rendelkezhet azonos céllal több szabály megfelel, csak írás egyszer célhoz.

## <a name="endpoints-and-routing"></a>Végpontok és az Útválasztás

Az IoT-központ rendelkezik egy alapértelmezett [beépített végpont][lnk-built-in]. Egyéni végpontokat üzeneteknek az előfizetésében szereplő más szolgáltatások létrehozhatja, ha szeretne az elosztóhoz hozhat létre. Az IoT-központ jelenleg támogatja az Azure Storage tárolók, az Event Hubs, a Service Bus-üzenetsorok és a Service Bus-üzenettémakörök egyéni végpontként.

Útválasztási és egyéni végpontok használatakor üzenetek csak kézbesítési a beépített végpont, ha a szabályok nem egyeznek. Üzenetek kézbesítése a beépített végpont, valamint egy egyéni végpont számára, adja hozzá egy olyan útvonalat, amely üzeneteket küld a **események** végpont.

> [!NOTE]
> Az IoT-központ csak írás a Azure Storage tárolók blobként támogatja.

> [!WARNING]
> Service Bus-üzenetsorok és témakörök a **munkamenetek** vagy **ismétlődő észlelési** engedélyezve van az egyéni végpontok nem támogatottak.

Egyéni végpontokat az IoT hubon létrehozásával kapcsolatos további információkért lásd: [IoT-központok végpontjai][lnk-devguide-endpoints].

A egyéni végpontok olvasását kapcsolatos további információkért lásd:

* A olvasásakor [Azure Storage tárolók][lnk-getstarted-storage].
* A olvasásakor [az Event Hubs][lnk-getstarted-eh].
* A olvasásakor [Service Bus-üzenetsorok][lnk-getstarted-queue].
* A olvasásakor [Service Bus-üzenettémakörök][lnk-getstarted-topic].

## <a name="latency"></a>Késés

Útvonal eszközről a felhőbe telemetriai üzenetek beépített végpont, nincs a a végpontok közötti késés megnövekedett az első útvonal létrehozása után.

A legtöbb esetben a átlagos késés növekedése legalább egy másodperc. A késés használatával figyelheti **d2c.endpoints.latency.builtIn.events** [IoT-központ metrika](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). Létrehozott, és egyetlen útvonalnak törlése után az elsőt nincs hatással a végpontok közötti késés.

### <a name="next-steps"></a>További lépések

IoT-központok végpontjai kapcsolatos további információkért lásd: [IoT-központok végpontjai][lnk-devguide-endpoints].

Útválasztási szabályok meghatározásához használja a lekérdezési nyelv kapcsolatos további információkért lásd: [IoT-központ lekérdezési nyelv eszköz twins, feladatok és üzenet útválasztási][lnk-devguide-query-language].

A [folyamat IoT Hub eszközről a felhőbe üzenetek útvonalak] [ lnk-d2c-tutorial] oktatóanyag bemutatja, hogyan használható az útválasztási szabályokat és az egyéni végpontokat.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
