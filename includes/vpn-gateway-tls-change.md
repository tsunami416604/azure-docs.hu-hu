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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67179010"
---
2018. július 1-től az Azure VPN Gatewayből el lett távolítva a TLS 1.0 és 1.1 támogatása. Ettől kezdve az Azure VPN Gateway csak a TLS 1.2-es verzióját támogatja. Csak a pont – hely kapcsolatok érintettek; a helyek közötti kapcsolatok nem lesznek érintettek. Ha a TLS protokollt használja a pont – hely VPN-hez Windows 10-es ügyfeleken, nem kell semmilyen műveletet végrehajtania. Ha a Windows 7 és a Windows 8 rendszerű ügyfelek esetében TLS-t használ a pont – hely kapcsolatokhoz, tekintse meg a frissítési útmutatót [VPN Gateway gyakori kérdések](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S) című témakört.