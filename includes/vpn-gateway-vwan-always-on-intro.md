---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4ea97e2dbee87f7ab129c4295276c9024c0212c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117075"
---
A Windows 10 VPN-ügyfél új funkciója, az Always On, a VPN-kapcsolat fenntartásának képessége. Az Always On beállítással az aktív VPN-profil automatikusan csatlakozhat, és az eseményindítók , például a felhasználói bejelentkezés, a hálózati állapot módosítása vagy az eszköz képernyője aktív marad.

A Windows 10 Always On rendszerrel rendelkező átjárók használatával állandó felhasználói alagutakat és eszközalagutakat hozhat létre az Azure-ba. Ez a cikk segít a mindig bekapcsolt VPN felhasználói alagút konfigurálása.

A Mindig bekapcsolt VPN-kapcsolatok kétféle alagúttípust tartalmaznak:

* **Eszközalagút**: A felhasználók bejelentkezése előtt csatlakozik a megadott VPN-kiszolgálókhoz. Bejelentkezés előtti kapcsolódási forgatókönyvek és az eszközkezelés eszközbújtatvezetést használ.

* **Felhasználói alagút**: Csak a felhasználók eszközre való bejelentkezése után csatlakozik. A felhasználói alagutak használatával vpn-kiszolgálókon keresztül érheti el a szervezeti erőforrásokat.

Az eszközalagutak és a felhasználói alagutak a VPN-profiloktól függetlenül működnek. Egyszerre is csatlakoztathatók, és szükség szerint különböző hitelesítési módszereket és más VPN-konfigurációs beállításokat is használhatnak.
