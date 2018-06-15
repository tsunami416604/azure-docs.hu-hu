---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8a49653b4083cbfd17656d701225dcb14f91f637
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197101"
---
Minden, a virtuális hálózathoz pont–hely kapcsolattal csatlakozó ügyfélszámítógépnek rendelkeznie kell telepített ügyféltanúsítvánnyal. Az ügyféltanúsítványokat a főtanúsítványból hozzák létre, majd telepítik az egyes ügyfélszámítógépekre. Ha nincs telepítve érvényes ügyféltanúsítvány, és az ügyfél megpróbál csatlakozni a virtuális hálózathoz, akkor a hitelesítés meghiúsul.

Létrehozhat egy egyedi tanúsítványt minden ügyfél számára, vagy használhatja ugyanazt a tanúsítványt több ügyfélhez is. Az egyedi ügyféltanúsítványok előállításának előnye az, hogy visszavonhat egyetlen tanúsítványt is. Ha azonban több ügyfél is ugyanazt az ügyféltanúsítványt használja, és Önnek vissza kell vonnia a tanúsítványt, az összes olyan ügyfél számára elő kell állítania és telepítenie kell új tanúsítványokat, amelyek az adott tanúsítványt használják a hitelesítéshez.

Ügyféltanúsítványokat a következő módszerekkel hozhat létre:

- **Vállalati tanúsítvány:**

  - Ha vállalati tanúsítványmegoldást használ, az általános „name@yourdomain.com” formátumban hozza létre az ügyféltanúsítványokat a „tartománynév\felhasználónév” formátum helyett.
  - Ellenőrizze, hogy az ügyféltanúsítvány azon a „felhasználói” tanúsítványsablonon alapul-e, amely használati listájának első helyén az „ügyfél-hitelesítés” áll, nem pedig az intelligens kártyás bejelentkezés vagy egyebek. A tanúsítvány ellenőrzéséhez kattintson duplán az ügyféltanúsítványra, és tekintse meg a **Részletek > Kibővített kulcshasználat** részt.

- **Önaláírt főtanúsítvány:** Fontos, hogy lépésről lépésre betartsa a P2S-tanúsítványokkal foglalkozó valamelyik alábbi cikk utasításait. Ellenkező esetben a létrehozott ügyféltanúsítványok nem lesznek kompatibilisek a P2S-kapcsolatokkal, és az ügyfelek hibákat kapnak majd a kapcsolódás során. Az alábbi cikkekben ismertetett lépésekkel kompatibilis ügyféltanúsítványok hozhatóak létre: 

  * [A Windows 10 PowerShellre vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): Ezekhez az utasításokhoz Windows 10 és PowerShell szükséges a tanúsítványok létrehozásához. A létrehozott tanúsítványok bármely támogatott P2S-ügyfélen telepíthetőek.
  * [A MakeCertre vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Ha nem rendelkezik hozzáféréssel Windows 10 rendszerű számítógéphez, a Makecert használatával is létrehozhat tanúsítványokat. A MakeCert ugyan elavult, azonban továbbra is hozhat létre vele tanúsítványokat. A létrehozott tanúsítványok bármely támogatott P2S-ügyfélen telepíthetőek.

  Amikor egy önaláírt főtanúsítványból az előző utasítások segítségével hoz létre ügyféltanúsítványt, akkor az automatikusan a létrehozásához használt számítógépre lesz telepítve. Ha egy ügyféltanúsítványt egy másik ügyfélszámítógépre szeretne telepíteni, akkor exportálnia kell azt egy .pfx-fájlként a teljes tanúsítványlánccal együtt. Ez egy .pfx fájlt hoz létre, amely tartalmazza az ügyfél sikeres hitelesítéséhez szükséges főtanúsítvány-adatokat. A tanúsítvány exportálásának lépéseiért lásd: [Tanúsítványok – ügyféltanúsítvány exportálása](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).