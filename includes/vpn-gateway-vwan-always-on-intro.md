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
ms.openlocfilehash: 2783c828f96eeb3539e2266eaf1d6d590a6af4c4
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370979"
---
A Windows 10 VPN-ügyfél új funkciója, a always on, a VPN-kapcsolat fenntartásának lehetősége. A always on használatával az aktív VPN-profil automatikusan csatlakozhat, és az eseményindítók alapján csatlakoztatva maradhat, például a felhasználói bejelentkezés, a hálózati állapot módosítása vagy az eszközbeállítások aktív állapota.

Az Azure-beli virtuális hálózati átjárók és a Windows 10 always on használatával állandó felhasználói alagutak és eszköz-alagutak hozhatók létre az Azure-ba. Ez a cikk segítséget nyújt az Always On VPN felhasználói alagút konfigurálásához.

Az Always On VPN-kapcsolatok a következő két típusú alagutat tartalmazzák:

* **Eszköz-alagút**: csatlakozás a megadott VPN-kiszolgálókhoz, mielőtt a felhasználók bejelentkeznek az eszközre. A Bejelentkezés előtti kapcsolódási forgatókönyvek és az eszközkezelés egy eszköz-alagutat használnak.

* **Felhasználói alagút**: csak azt követően csatlakozik, hogy a felhasználók bejelentkeznek az eszközre. A felhasználói alagutak használatával VPN-kiszolgálókon keresztül érheti el a szervezeti erőforrásokat.

Az eszköz-alagutak és a felhasználói alagutak a VPN-profiljaik függetlenül működnek. Egyszerre csatlakozhatnak egymáshoz, és szükség szerint különböző hitelesítési módszereket és egyéb VPN-konfigurációs beállításokat használhatnak.
