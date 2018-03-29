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
ms.openlocfilehash: 9fa18b14b82376a25bb434acd770d340b1ef9262
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
Ha problémákat tapasztal a csatlakozás során, ellenőrizze a következőket:

- Győződjön meg arról, hogy az exportált ügyféltanúsítványt .pfx fájlként exportálta az alapértelmezett „Minden tanúsítvány belefoglalása a tanúsítványláncba” beállítással. Ha az exportálást ezzel az értékkel végzi, a rendszer a főtanúsítvány információit is exportálja. Az ügyféltanúsítvány az ügyfélszámítógépre való telepítésekor az alkalmazás a .pfx fájlban lévő főtanúsítványt is telepíti az ügyfélszámítógépre. Az ügyfélszámítógépen telepítve kell lennie a főtanúsítvány adatainak. Ennek ellenőrzéséhez lépjen a **Felhasználói tanúsítványok kezelése** felületre, és navigáljon a **Megbízható legfelső szintű hitelesítésszolgáltatók\Tanúsítványok** részhez. Ellenőrizze, hogy a főtanúsítvány szerepel-e a listában. A főtanúsítvány a hitelesítés működéséhez szükséges.

- Ha vállalati hitelesítésszolgáltatói megoldás használatával kiadott tanúsítványt használ, és problémák merülnek fel a hitelesítés során, ellenőrizze a hitelesítési sorrendet az ügyféltanúsítványon. A hitelesítési lista sorrendjének ellenőrzéséhez kattintson duplán az ügyféltanúsítványra, és lépjen a **Részletek > Kibővített kulcshasználat** részre. Ellenőrizze, hogy a listán az „ügyfél-hitelesítés” jelenik-e meg első helyen. Ha nem, ki kell adnia egy ügyféltanúsítványt, amely az ügyfél-hitelesítést a lista első helyén tartalmazó felhasználói sablonon alapul.

- A pont–hely (P2S) hibaelhárítással kapcsolatos további információkért lásd: [Troubleshoot P2S connections](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) (Pont–hely kapcsolatok hibaelhárítása).