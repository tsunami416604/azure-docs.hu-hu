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
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "67178981"
---
Minden olyan ügyfélszámítógépnek, amelyhez pont – hely kapcsolattal rendelkező VNet kapcsolódik, telepítenie kell egy ügyféltanúsítványt. A rendszer létrehozza a főtanúsítványból, és telepíti az egyes ügyfélszámítógépekre. Ha nem telepít érvényes ügyféltanúsítványt, a hitelesítés sikertelen lesz, ha az ügyfél megpróbál csatlakozni a VNet.

Létrehozhat egy egyedi tanúsítványt minden ügyfél számára, vagy használhatja ugyanazt a tanúsítványt több ügyfélhez is. Az egyedi ügyféltanúsítványok előállításának előnye az, hogy visszavonhat egyetlen tanúsítványt is. Ellenkező esetben, ha több ügyfél ugyanazt az ügyféltanúsítványt használja a hitelesítéshez és a visszavonásához, új tanúsítványokat kell létrehoznia és telepítenie minden olyan ügyfélnél, amely ezt a tanúsítványt használja.

Az ügyféltanúsítványok a következő módszerekkel hozhatók elő:

- **Vállalati tanúsítvány:**

  - Ha vállalati Tanúsítványos megoldást használ, a common name Value Format nevű ügyféltanúsítványt adja meg *\@yourdomain. com*néven. Ezt a formátumot a *tartomány \ Felhasználónév* formátuma helyett használja.
  - Győződjön meg arról, hogy az ügyféltanúsítvány olyan felhasználói tanúsítványsablon alapján van megadva, amely a felhasználók listájának első elemeként szerepel az *ügyfél-hitelesítésben* . Ellenőrizze a tanúsítványt úgy, hogy duplán kattint rá, és megtekinti a **Kibővített kulcshasználat** lehetőséget a **részletek** lapon.

- **Önaláírt főtanúsítvány:** Kövesse az alábbi P2S-tanúsítványok egyikének lépéseit, hogy a létrehozott Ügyféltanúsítványok kompatibilisek legyenek a P2S-kapcsolatokkal. A cikkben ismertetett lépések egy kompatibilis ügyféltanúsítványt hoznak: 

  * [A Windows 10 PowerShellre vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): Ezekhez az utasításokhoz Windows 10 és PowerShell szükséges a tanúsítványok létrehozásához. A generált tanúsítványok telepíthetők bármely támogatott P2S-ügyfélre.
  * [MakeCert utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): a MakeCert használata, ha nem rendelkezik hozzáféréssel a Windows 10 rendszerű számítógépekhez a tanúsítványok létrehozásához. Bár a MakeCert elavult, továbbra is használhatja tanúsítvány létrehozásához. A generált tanúsítványokat bármely támogatott P2S-ügyfélre telepítheti.
  * [Linuxra vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  Ha önaláírt főtanúsítványból állít elő ügyféltanúsítványt, azt a rendszer automatikusan telepíti a létrehozásához használt számítógépre. Ha egy másik ügyfélszámítógépen szeretné telepíteni az ügyféltanúsítványt, exportálja. pfx-fájlként, valamint a teljes tanúsítványláncot. Ekkor létrejön egy. pfx-fájl, amely tartalmazza az ügyfél hitelesítéséhez szükséges főtanúsítvány-információkat. 

**A tanúsítvány exportálása**

A tanúsítványok exportálásának lépéseiért lásd: [tanúsítványok létrehozása és exportálása pont – hely kapcsolatokhoz a PowerShell használatával](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).
