---
title: fájlbefoglalás
description: fájlbefoglalás
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 01796985a05f88b59786be7bbe0b06907cf0bc25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88854112"
---
| Erőforrás                                | Korlát        |
|-----------------------------------------|------------------------------|
| VNet-címek előtagjai                   | 600/VPN-átjáró          |
| Összesített BGP-útvonalak                    | 4 000/VPN-átjáró        |
| Helyi hálózati átjáró címeinek előtagjai  | 1000/helyi hálózati átjáró               |
| S2S-kapcsolatok                         | [Az átjáró SKU-jának függ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)|
| Pont–hely kapcsolatok                         | [Az átjáró SKU-jának függ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) |
| P2S-útvonal korlátja – IKEv2                 | 256 nem Windows **/** 25 Windows rendszerhez           |
| P2S-útvonal korlátja – OpenVPN               | 1000                         |
| Legfeljebb folyamatok                              | 100K a VpnGw1/AZ  **/**  512K for VpnGw2-4/az|