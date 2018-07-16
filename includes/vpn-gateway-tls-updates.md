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
ms.openlocfilehash: 9a17f34333503436d3da340670abdde154e45ef6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38727530"
---
2018. július 1-től az Azure VPN Gatewayből el lett távolítva a TLS 1.0 és 1.1 támogatása. Ettől kezdve az Azure VPN Gateway csak a TLS 1.2-es verzióját támogatja. A TLS támogatásának, valamint a TLS-t használó, Windows 7 és Windows 8 rendszerű, pont–hely típusú ügyfelek kapcsolatainak fenntartása érdekében javasoljuk, hogy telepítse az alábbi frissítéseket:

•   [A Microsoft EAP-implementáció frissítése, amely lehetővé teszi a TLS használatát](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

•   [Frissítés, amely lehetővé teszi a TLS 1.1 és a TLS 1.2 alapértelmezett biztonságos protokollként történő használatát a WinHTTP-ben](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

2018. július 1-től a következő örökölt algoritmusok szintén elavulttá válnak a TLS vonatkozásában:

* RC4 (Rivest Cipher 4)
* DES (adattitkosítási algoritmus)
* 3DES (háromszoros adattitkosítási algoritmus)
* MD5 (Message Digest 5)
