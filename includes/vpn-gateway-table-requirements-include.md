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
ms.openlocfilehash: 5e7e365a73c9ba9cfd91d4f93759e7af55a0ad48
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
ms.locfileid: "30929325"
---
A következő táblázat PolicyBased és RouteBased VPN-átjáró rendszerkövetelményeit sorolja fel. Ez a tábla a Resource Managerre és a klasszikus üzembe helyezési modellre is érvényes. A Klasszikus modell PolicyBased VPN gatewayek ugyanazok, mint statikus átjárók, és útválasztó-alapú gatewayek ugyanazok, mint a dinamikus átjárók.

|  | **PolicyBased alapszintű VPN Gateway** | **RouteBased alapszintű VPN Gateway** | **RouteBased Standard VPN Gateway** | **RouteBased nagy teljesítményű VPN Gateway** |
| --- | --- | --- | --- | --- |
| **Pont-pont kapcsolat (S2S)** |PolicyBased VPN-konfiguráció |RouteBased VPN-konfiguráció |RouteBased VPN-konfiguráció |RouteBased VPN-konfiguráció |
| **Pont–hely típusú kapcsolat (P2S**) |Nem támogatott |Támogatott (párhuzamosan használható az S2S mellett) |Támogatott (párhuzamosan használható az S2S mellett) |Támogatott (párhuzamosan használható az S2S mellett) |
| **Hitelesítési módszer** |Előre megosztott kulcs |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |
| **S2S-kapcsolatok maximális száma** |1 |10 |10 |30 |
| **P2S-kapcsolatok maximális száma** |Nem támogatott |128 |128 |128 |
| **Aktív útválasztás-támogatás (BGP)** (*) |Nem támogatott |Nem támogatott |Támogatott |Támogatott |

  (*) A klasszikus üzembe helyezési modell esetén nem támogatottak a BGP.
