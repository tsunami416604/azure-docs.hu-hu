---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.custom: fasttrack-edit
ms.openlocfilehash: 819eed1eb4e32469bf0572a6548f5a2a44091ef0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77279594"
---
Ez a táblázat az Azure Functions futásidejű főverzióiban támogatott kötéseket mutatja be:


| Típus | 1,x | 2.x és magasabb<sup>1</sup> | Eseményindító | Input (Bemenet) | Kimenet |
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Blob-tároló](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|
| [Event Grid](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| |✔|
| [Eseményközpontok](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|
| [HTTP & webhookok](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [IoT Hub](../articles/azure-functions/functions-bindings-event-iot.md)             |✔|✔|✔| |✔|
| [Microsoft<br/>Graph Excel-táblázatok](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Microsoft<br/>Graph OneDrive-fájlok](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Microsoft<br/>Graph Outlook-levelezés](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Microsoft<br/>Graph események](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Microsoft<br/>Graph Hitelesítési tokenek](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [Mobil alkalmazások](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|
| [Értesítési központ](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Várólista-tárolás](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Service Bus](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|
| [SignalR](../articles/azure-functions/functions-bindings-signalr-service.md)             | |✔| |✔|✔|
| [Table Storage](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|
| [Időzítő](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|

<sup>1</sup> A 2.x-es verziójú futásórával kezdve a HTTP és az Időzítő kivételével minden kötést regisztrálni kell. Lásd: [Kötési bővítmények regisztrálása](../articles/azure-functions/functions-bindings-register.md).
