---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d8091fdade9cd417af58755d8245c2fb091b86b3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
A következő táblázat PolicyBased és RouteBased VPN-átjáró rendszerkövetelményeit sorolja fel. Ez a tábla a Resource Managerre és a klasszikus üzembe helyezési modellre is érvényes. A Klasszikus modell PolicyBased VPN gatewayek ugyanazok, mint statikus átjárók, és útválasztó-alapú gatewayek ugyanazok, mint a dinamikus átjárók.

|  | **PolicyBased alapszintű VPN Gateway** | **RouteBased alapszintű VPN Gateway** | **RouteBased Standard VPN Gateway** | **RouteBased nagy teljesítményű VPN Gateway** |
| --- | --- | --- | --- | --- |
| **Pont-pont kapcsolat (S2S)** |PolicyBased VPN-konfiguráció |RouteBased VPN-konfiguráció |RouteBased VPN-konfiguráció |RouteBased VPN-konfiguráció |
| **Pont–hely típusú kapcsolat (P2S**) |Nem támogatott |Támogatott (párhuzamosan használható az S2S mellett) |Támogatott (párhuzamosan használható az S2S mellett) |Támogatott (párhuzamosan használható az S2S mellett) |
| **Hitelesítési módszer** |Előre megosztott kulcs |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |
| **S2S-kapcsolatok maximális száma** |1 |10 |10 |30 |
| **P2S-kapcsolatok maximális száma** |Nem támogatott |128 |128 |128 |
| **Aktív útválasztás-támogatás (BGP)** |Nem támogatott |Nem támogatott |Támogatott |Támogatott |
