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
ms.openlocfilehash: dc018b5d09c9b33c10cd2d54ac6572537e05ed25
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64859044"
---
A következő táblázat felsorolja a házirendalapú és Útvonalalapú VPN-átjárók követelményei. Ez a tábla a Resource Managerre és a klasszikus üzembe helyezési modellre is érvényes. A klasszikus modellt a házirendalapú VPN gatewayek ugyanazok, mint statikus átjárókhoz, és az útvonalalapú átjárók ugyanazok, mint a dinamikus átjárók.

|  | **Házirendalapú alapszintű VPN Gateway** | **Alapszintű Útvonalalapú VPN-átjáró** | **Standard Útvonalalapú VPN-átjáró** | **Útvonalalapú nagy teljesítményű VPN Gateway** |
| --- | --- | --- | --- | --- |
| **Helyek közötti kapcsolat (S2S)** |Házirendalapú VPN-konfiguráció |Útvonalalapú VPN-konfiguráció |Útvonalalapú VPN-konfiguráció |Útvonalalapú VPN-konfiguráció |
| **Pont–hely típusú kapcsolat (P2S**) |Nem támogatott |Támogatott (párhuzamosan használható az S2S mellett) |Támogatott (párhuzamosan használható az S2S mellett) |Támogatott (párhuzamosan használható az S2S mellett) |
| **Hitelesítési módszer** |Előre megosztott kulcs |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |
| **S2S-kapcsolatok maximális száma** |1 |10 |10 |30 |
| **P2S-kapcsolatok maximális száma** |Nem támogatott |128 |128 |128 |
| **Aktív útválasztás-támogatás (BGP)** (*) |Nem támogatott |Nem támogatott |Támogatott |Támogatott |

  (*) A klasszikus üzemi modell esetében nem támogatott a BGP.
