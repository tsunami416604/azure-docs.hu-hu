---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f2d7aba05fc01c5a4dcdb123f25242c4e4a72578
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "43426428"
---
>[!NOTE]
>2018. július 1-től az Azure VPN Gatewayből el lett távolítva a TLS 1.0 és 1.1 támogatása. Ettől kezdve az Azure VPN Gateway csak a TLS 1.2-es verzióját támogatja. Támogatás fenntartásához, tekintse meg a [TLS1.2 támogatásának engedélyezéséhez frissítések](#tls1).

Ezenkívül a következő örökölt algoritmusok is elavulttá válik a TLS 2018. július 1.:

* RC4 (Rivest Cipher 4)
* DES (adattitkosítási algoritmus)
* 3DES (háromszoros adattitkosítási algoritmus)
* MD5 (Message Digest 5)

### <a name="tls1"></a>Hogyan engedélyezhetem a Windows 7 és Windows 8.1 a TLS 1.2 támogatása?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
