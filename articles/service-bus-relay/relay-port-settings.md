---
title: Az Azure Relay port beállításai | Microsoft dokumentumok
description: Ez a cikk egy táblázatot tartalmaz, amely ismerteti az Azure Relay portértékeinek szükséges konfigurációját.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 55bb6b620c7ba9ee13068213cbc867b165acc185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514884"
---
# <a name="azure-relay-port-settings"></a>Az Azure Relay port beállításai

Az alábbi táblázat ismerteti az Azure Relay portértékeinek szükséges konfigurációját.

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

A hibrid kapcsolatok websocketeket használ a 443-as porton, ahol az SSL az alapul szolgáló átviteli mechanizmus, amely csak **HTTPS-t** használ. 

## <a name="wcf-relays"></a>WCF-továbbítók
  
|Kötés|Közlekedésbiztonság|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (ügyfél)|Igen|HTTPS| 
|" |Nem|HTTP|  
|[BasicHttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (szolgáltatás)|Vagy|9351/HTTP|  
|[NetEventRelayBinding osztály](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (ügyfél)|Igen|9351/HTTPS|  
|" |Nem|9350/HTTP|  
|[NetEventRelayBinding osztály](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (szolgáltatás)|Vagy|9351/HTTP|  
|[NetTcpRelayBinding osztály](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (ügyfél/szolgáltatás)|Vagy|5671/9352/HTTP (9352/9353 hibrid használata esetén)|  
|[NetOnewayRelayBinding osztály](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (ügyfél)|Igen|9351/HTTPS|  
|" |Nem|9350/HTTP|  
|[NetOnewayRelayBinding osztály](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (szolgáltatás)|Vagy|9351/HTTP|  
|[WebHttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (ügyfél)|Igen|HTTPS|  
|" |Nem|HTTP|  
|[WebHttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (szolgáltatás)|Vagy|9351/HTTP|  
|[WS2007HttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (ügyfél)|Igen|HTTPS|  
|" |Nem|HTTP|  
|[WS2007HttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (szolgáltatás)|Vagy|9351/HTTP|

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni az Azure Relayről, látogasson el az alábbi hivatkozásokra:
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Relay – gyakori kérdések](relay-faq.md)