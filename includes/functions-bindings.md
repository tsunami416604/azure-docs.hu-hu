---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 17e17b9847306de1971ebcfbb79f1d95516cba57
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134348"
---
Az alábbi táblázat a kötések az Azure Functions runtime két fő verziói által támogatott.

| Típus | 1.x | 2.x<sup>1</sup> | Eseményindító | Input (Bemenet) | Kimenet |  
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|  
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|  
| [Event Grid](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |  
| [Event Hubs](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|  
| [Külső fájl](../articles/azure-functions/functions-bindings-external-file.md)<sup>2</sup>    |✔|| |✔|✔|  
| [Külső tábla](../articles/azure-functions/functions-bindings-external-table.md)<sup>2</sup>  |✔|| |✔|✔|  
| [HTTP](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [A Microsoft Graph<br/>Excel-táblák](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [A Microsoft Graph<br/>OneDrive-fájlok](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [A Microsoft Graph<br/>Outlookos e-mail cím](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [A Microsoft Graph<br/>események](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [A Microsoft Graph<br/>hitelesítési jogkivonatok](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [Mobile Apps](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|  
| [Értesítési központ](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|  
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Szolgáltatásbusz](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|  
| [Table Storage](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|  
| [Időzítő](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|
| [Webhooks](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔||✔| |✔|
  
<sup>1</sup> 2.x, HTTP- és időzítő kivételével az összes kötések kell regisztrálni. Lásd: [regisztrálja a kötési bővítményeket](../articles/azure-functions/functions-triggers-bindings.md#register-binding-extensions).

<sup>2</sup> kísérleti &mdash; nem támogatott, és előfordulhat, hogy a jövőben hagyva.
