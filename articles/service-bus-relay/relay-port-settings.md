---
title: "Az Azure továbbítási portbeállítások |} Microsoft Docs"
description: "Azure-továbbítási portértékeket adatait."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 055f04d496b56a5e8542911aa78292d7746ae80b
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="azure-relay-port-settings"></a>Az Azure továbbítási port beállításai

A következő táblázat ismerteti az Azure-továbbítási értékeket a kötelező beállítani.

## <a name="hybrid-connections"></a>Hibrid kapcsolatok
Hibrid kapcsolatok websocket elemek használja a mögöttes átviteli mechanizmusként, amely használja **HTTPS** csak. 

## <a name="wcf-relays"></a>WCF-továbbítók
  
|Kötelező|A Transport Security|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (ügyfél)|Igen|HTTPS| 
| |" |Nem|HTTP|  
|[BasicHttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (szolgáltatás)|Vagy|9351/HTTP|  
|[NetEventRelayBinding osztály](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (ügyfél)|Igen|9351/HTTPS|  
||" |Nem|9350/HTTP|  
|[NetEventRelayBinding osztály](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (szolgáltatás)|Vagy|9351/HTTP|  
|[NetTcpRelayBinding osztály](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (ügyfél vagy szolgáltatás)|Vagy|A HTTP/5671/9352 (9352/9353 hibrid használata)|  
|[NetOnewayRelayBinding osztály](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (ügyfél)|Igen|9351/HTTPS|  
||" |Nem|9350/HTTP|  
|[NetOnewayRelayBinding osztály](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (szolgáltatás)|Vagy|9351/HTTP|  
|[WebHttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (ügyfél)|Igen|HTTPS|  
||" |Nem|HTTP|  
|[WebHttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (szolgáltatás)|Vagy|9351/HTTP|  
|[WS2007HttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (ügyfél)|Igen|HTTPS|  
||" |Nem|HTTP|  
|[WS2007HttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (szolgáltatás)|Vagy|9351/HTTP|

## <a name="next-steps"></a>További lépések
Azure-továbbítási kapcsolatos további információkért látogasson el ezeket a hivatkozásokat:
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Relay – gyakori kérdések](relay-faq.md)