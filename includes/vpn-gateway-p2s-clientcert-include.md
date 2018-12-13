---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 7ae3886db6391836cd8d281e44c95c5253cc8dd5
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323804"
---
Minden virtuális hálózathoz pont – hely kapcsolattal csatlakozó ügyfélszámítógépen rendelkeznie kell telepített ügyféltanúsítvánnyal. Hozza létre a főtanúsítvány az, és telepítse az egyes ügyfélszámítógépekre. Ha nem telepít egy érvényes ügyféltanúsítványt, hitelesítés sikertelen lesz, amikor az ügyfél megpróbál csatlakozni a virtuális hálózathoz.

Létrehozhat egy egyedi tanúsítványt minden ügyfél számára, vagy használhatja ugyanazt a tanúsítványt több ügyfélhez is. Az egyedi ügyféltanúsítványok előállításának előnye az, hogy visszavonhat egyetlen tanúsítványt is. Ellenkező esetben ha több ügyfél használja ugyanazt az ügyféltanúsítványt hitelesítésre, és Ön visszavonáshoz, szüksége létrehozni, és telepítenie kell új tanúsítványokat, amelyek a tanúsítványt használja minden ügyfélhez.

Ügyfél-tanúsítványok az alábbi módszerek használatával is létrehozhat:

- **Vállalati tanúsítvány:**

  - Ha vállalati tanúsítványmegoldást használ, az ügyféltanúsítványokat a közös formátumban *name@yourdomain.com*. Ez a formátum helyett használja a *tartománynév\felhasználónév* formátumban.
  - Ellenőrizze, hogy egy felhasználó tanúsítványsablont, amelynek alapján az ügyféltanúsítvány *ügyfél-hitelesítés* az első elem, a felhasználók listájában szerepel. Ellenőrizze a tanúsítvány kattint, és megtekintés **kibővített kulcshasználat** a a **részletek** fülre.

- **Önaláírt főtanúsítvány:** Lépésről lépésre betartsa a P2S-tanúsítványokkal alábbi cikk hajtsa végre az, hogy a létrehozott ügyféltanúsítványok lesznek kompatibilisek a P2S-kapcsolatokkal. Ezekben a cikkekben leírt lépéseket egy kompatibilis ügyféltanúsítvány létrehozása: 

  * [A Windows 10 Powershellre vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): Ezekhez az utasításokhoz Windows 10-es és a PowerShell is létrehozhat tanúsítványokat. A létrehozott tanúsítványok bármely támogatott P2S-ügyfélen is telepíthető.
  * [Útmutatás a MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Ha nem rendelkezik hozzáféréssel Windows 10 rendszerű számítógéphez a tanúsítványok létrehozásához használja a Makecertet. Bár a MakeCert elavult, továbbra is használhatja azt is létrehozhat tanúsítványokat. Telepítheti a létrehozott tanúsítványok bármely támogatott P2S-ügyfélen.
  * [Linuxra vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  Egy önaláírt főtanúsítványból létre ügyféltanúsítványt, ha automatikusan települ a létrehozáshoz használt számítógépen. Telepíthet ügyféltanúsítványt egy másik ügyfélszámítógépre szeretne, ha exportálja .pfx-fájlként, a teljes tanúsítványlánccal együtt. Ez létrehoz egy .pfx-fájlt, amely tartalmazza a főtanúsítvány információit az ügyfél hitelesítéséhez szükséges. Tanúsítvány exportálása lépéseiért lásd: [tanúsítványok létrehozása és exportálása pont – hely PowerShell-lel](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).