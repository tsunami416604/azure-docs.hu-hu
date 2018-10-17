---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: 01a62fe7abb8a79f9afc08c0ff707cdfbb97ddac
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44343170"
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
  * [Linuxra vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  Amikor egy önaláírt főtanúsítványból az előző utasítások segítségével hoz létre ügyféltanúsítványt, akkor az automatikusan a létrehozásához használt számítógépre lesz telepítve. Ha egy ügyféltanúsítványt egy másik ügyfélszámítógépre szeretne telepíteni, akkor exportálnia kell azt egy .pfx-fájlként a teljes tanúsítványlánccal együtt. Ez egy .pfx fájlt hoz létre, amely tartalmazza az ügyfél sikeres hitelesítéséhez szükséges főtanúsítvány-adatokat. A tanúsítvány exportálásának lépéseiért lásd: [Tanúsítványok – ügyféltanúsítvány exportálása](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).