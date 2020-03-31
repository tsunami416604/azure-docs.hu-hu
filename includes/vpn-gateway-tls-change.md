---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2ed141847f923a847443d2293e850519357cdae2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179010"
---
2018. július 1-től az Azure VPN Gatewayből el lett távolítva a TLS 1.0 és 1.1 támogatása. Ettől kezdve az Azure VPN Gateway csak a TLS 1.2-es verzióját támogatja. Csak a pont-hely kapcsolatok érintettek; a helyek közötti kapcsolatokat ez nem érinti. Ha a TLS-t pont-hely VPN-ekhez használja Windows 10-ügyfeleken, nem kell semmilyen műveletet végrehajtania. Ha a TLS-t pont-hely kapcsolatokhoz használja Windows 7 és Windows 8 ügyfeleken, olvassa el a [VPN-átjáró gyIK-et](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S) a frissítéssel kapcsolatos utasításokért.