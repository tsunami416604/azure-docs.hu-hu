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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679382"
---
2018. július 1-től az Azure VPN Gatewayből el lett távolítva a TLS 1.0 és 1.1 támogatása. Ettől kezdve az Azure VPN Gateway csak a TLS 1.2-es verzióját támogatja. Csak a pont – hely kapcsolatok érintett; hely – hely kapcsolatok érintetlenek maradnak. Ha TLS a Windows 10-ügyfelek a pont – hely VPN-ek használata esetén nem kell semmit sem. Ha pont – hely kapcsolatokhoz a Windows 7 és Windows 8 rendszerű ügyfelek TLS használ, tekintse meg a [VPN Gateway – gyakori kérdések](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S) update utasítások.