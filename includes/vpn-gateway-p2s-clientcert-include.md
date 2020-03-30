---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 31ccf14c82f6248c74d6af932fe9e338d26d2747
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67178981"
---
Minden olyan ügyfélszámítógépen, amelyet egy pont-hely kapcsolattal rendelkező virtuális hálózathoz csatlakozik, telepítve kell lennie egy ügyféltanúsítvánnyal. A főtanúsítványból hozza létre, és minden ügyfélszámítógépre telepíti. Ha nem telepít érvényes ügyféltanúsítványt, a hitelesítés sikertelen lesz, amikor az ügyfél megpróbál csatlakozni a virtuális hálózathoz.

Létrehozhat egy egyedi tanúsítványt minden ügyfél számára, vagy használhatja ugyanazt a tanúsítványt több ügyfélhez is. Az egyedi ügyféltanúsítványok előállításának előnye az, hogy visszavonhat egyetlen tanúsítványt is. Ellenkező esetben, ha több ügyfél ugyanazt az ügyféltanúsítványt használja a hitelesítéshez, és ön visszavonja azt, akkor új tanúsítványokat kell létrehoznia és telepítenie minden olyan ügyfélhez, amely ezt a tanúsítványt használja.

Az ügyféltanúsítványokat a következő módszerekkel hozhatja létre:

- **Vállalati tanúsítvány:**

  - Vállalati tanúsítványmegoldás használata esetén hozzon létre egy ügyféltanúsítványt a köznapi névérték-formátum *\@névvel yourdomain.com.* Ezt a formátumot használja a *tartománynév\felhasználónév* formátum helyett.
  - Győződjön meg arról, hogy az ügyféltanúsítvány olyan felhasználói tanúsítványsablonon alapul, *amelynek ügyfélhitelesítése* szerepel a felhasználói lista első elemeként. Ellenőrizze a tanúsítványt úgy, hogy duplán kattint rá, és a **Részletek** lapon megtekinti a **bővített kulcshasználatot.**

- **Önaláírt főtanúsítvány:** Kövesse az alábbi P2S-tanúsítványcikkek egyikének lépéseit, hogy a létrehozott ügyféltanúsítványok kompatibilisek legyenek a P2S-kapcsolatokkal. Az ezekben a cikkekben ismertetett lépések kompatibilis ügyféltanúsítványt hoznak létre: 

  * [A Windows 10 PowerShellre vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): Ezekhez az utasításokhoz Windows 10 és PowerShell szükséges a tanúsítványok létrehozásához. A létrehozott tanúsítványok bármely támogatott P2S-ügyfélre telepíthetők.
  * [MakeCert utasítások:](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)Használja a MakeCert-et, ha nem rendelkezik hozzáféréssel a Windows 10-es számítógéphez a tanúsítványok létrehozásához. Bár a MakeCert elavult, továbbra is használhatja tanúsítványok létrehozásához. A létrehozott tanúsítványok bármely támogatott P2S-ügyfélre telepíthetők.
  * [Linuxra vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  Ha önaláírt főtanúsítványból hoz létre ügyféltanúsítványt, az automatikusan települ azon a számítógépen, amelyen létrehozta azt. Ha egy ügyféltanúsítványt egy másik ügyfélszámítógépre szeretne telepíteni, exportálja .pfx fájlként, a teljes tanúsítványlánccal együtt. Ezzel létrehoz egy .pfx fájlt, amely tartalmazza az ügyfél hitelesítéséhez szükséges főtanúsítvány-adatokat. 

**A tanúsítvány exportálása**

A tanúsítványok exportálásának lépéseit a [PowerShell használatával a Pont–webhely tanúsítványok létrehozása és exportálása témakörben található.](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)
