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
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66146991"
---
>[!NOTE]
>2018\. július 1-től az Azure VPN Gatewayből el lett távolítva a TLS 1.0 és 1.1 támogatása. Ettől kezdve az Azure VPN Gateway csak a TLS 1.2-es verzióját támogatja. Támogatás fenntartásához, tekintse meg a [TLS1.2 támogatásának engedélyezéséhez frissítések](#tls1).

Ezenkívül a következő örökölt algoritmusok is elavulttá válik a TLS 2018. július 1.:

* RC4 (Rivest Cipher 4)
* DES (adattitkosítási algoritmus)
* 3DES (háromszoros adattitkosítási algoritmus)
* MD5 (Message Digest 5)

### <a name="tls1"></a>Hogyan engedélyezhetem a Windows 7 és Windows 8.1 a TLS 1.2 támogatása?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
