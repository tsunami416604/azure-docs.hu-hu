---
title: fájlbefoglalás
description: fájlbefoglalás
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: fe205524c5aef6a43771cc09c810da217678d108
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022140"
---
A AMQP-over-WebSockets protokoll beállítása a 443-as TCP-porton keresztül fut a HTTP-/REST API-hez hasonlóan, de egyébként az egyszerű AMQP eltérően működik. Ez a beállítás valamivel nagyobb kezdeti kapcsolati késést biztosít a további kézfogások miatt, és valamivel nagyobb terhelést jelent, mint a HTTPS-port megosztásának kompromisszuma. Ha ez a mód be van jelölve, a 443-es TCP-port elegendő a kommunikációhoz. A következő lehetőségek lehetővé teszik az egyszerű AMQP vagy a AMQP WebSockets mód kiválasztását:

| Nyelv | Beállítás   |
| -------- | ----- |
| .NET     | [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype?view=azure-dotnet) tulajdonság a [TransportType. Amqp](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) vagy a [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) |
| Java     | [com. microsoft. Azure. servicebus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings?view=azure-java-stable) a [com. microsoft. Azure. servicebus. primitívs. TransportType. AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) vagy [com.microsoft.Azure.servicebus.primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) |
| Csomópont  | A [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions?view=azure-node-latest) `webSocket` konstruktor argumentummal rendelkezik. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) [TransportType. Amqp](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) vagy [TransportType. AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |