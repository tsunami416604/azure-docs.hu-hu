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
ms.openlocfilehash: 383c4a53913333a3e25006980dd7533b9e243a4a
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236680"
---
2018. július 1., kezdési támogatja a TLS 1.0 és 1.1 az Azure VPN Gateway távolít el. Csak a TLS 1.2-es VPN-átjáró támogatja. A TLS-támogatás és a kapcsolat fenntartása a Windows 7 és Windows 8 pont-pont használó ügyfelek számára a TLS azt javasoljuk, hogy telepítse a következő frissítéseket:

• [Frissítés a Microsoft EAP megvalósítása, amely lehetővé teszi, hogy a TLS használata](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

• [Engedélyezték a TLS 1.1 és TLS 1.2-es alapértelmezett biztonságos protokollok, a WinHTTP frissítése](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

A következő örökölt algoritmusok is elavulttá válik a TLS a 2018. július 1:

* RC4 (Rivest titkosítási 4)
* DES (adattitkosító algoritmus)
* 3DES (háromszoros adattitkosító algoritmus)
* MD5 (Message Digest 5)
* Az SHA-1 (biztonságos kivonatoló algoritmus: 1)