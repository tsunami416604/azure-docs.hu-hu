---
title: Az Azure Relay portbeállítások |} A Microsoft Docs
description: Az Azure Relay-portok értékei kapcsolatos adatokat.
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
ms.date: 06/26/2018
ms.author: spelluru
ms.openlocfilehash: 9d11179a8518ebf48f68f8607f94e0253d4edb80
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60789926"
---
# <a name="azure-relay-port-settings"></a>Az Azure Relay-port beállítása

A következő táblázat ismerteti az Azure Relay portértékeket a szükséges konfigurációval.

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

A mögöttes átviteli mechanizmusként, használó hibrid kapcsolatok használja a 443-as porton az SSL a websockets protokoll **HTTPS** csak. 

## <a name="wcf-relays"></a>WCF-továbbítók
  
|Kötés|Transport Security|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (ügyfél)|Igen|HTTPS| 
|" |Nem|HTTP|  
|[BasicHttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (szolgáltatás)|Vagy|9351/HTTP|  
|[NetEventRelayBinding osztály](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (ügyfél)|Igen|9351/HTTPS|  
|" |Nem|9350/HTTP|  
|[NetEventRelayBinding osztály](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (szolgáltatás)|Vagy|9351/HTTP|  
|[NetTcpRelayBinding osztály](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (ügyfél vagy-szolgáltatás)|Vagy|5671-es, a 9352-es vagy a HTTP (9352-es/9353 hibrid használatakor)|  
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
Az Azure Relay kapcsolatos további információkért látogasson el ezeket a hivatkozásokat:
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Relay – gyakori kérdések](relay-faq.md)