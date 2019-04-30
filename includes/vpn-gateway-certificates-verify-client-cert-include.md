---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 12/11/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320102"
---
Ha ütközik a csatlakozás, ellenőrizze a következőket:

- Ha az ügyféltanúsítványokat exportált **Tanúsítványexportáló varázsló**, ellenőrizze, hogy exportálta a .pfx-fájlként, és kiválasztott **minden tanúsítvány belefoglalása a tanúsítványláncba, ha lehetséges**. Ha az ezt az értéket az exportálást, a főtanúsítvány információit is exportálja. Miután telepítette a tanúsítványt az ügyfélszámítógépen, a .pfx fájlban a legfelső szintű tanúsítvány is telepítve van. Győződjön meg arról, hogy telepítve van-e a legfelső szintű tanúsítványt, nyissa meg a **felhasználói tanúsítványok kezelése** válassza **megbízható legfelső szintű hitelesítésszolgáltatók\Tanúsítványok**. Győződjön meg arról, hogy a legfelső szintű tanúsítvány szerepel a listán, amelynek hitelesítés működéséhez jelen kell lennie.

- Ha vállalati Hitelesítésszolgáltatói megoldás által kiállított tanúsítványt használt, és nem tudják hitelesíteni magukat, ellenőrizze a hitelesítési sorrendet az ügyféltanúsítványon. A hitelesítési lista sorrendjének ellenőrzéséhez kattintson duplán az ügyféltanúsítvány kiválasztása a **részletek** fülre, majd kiválasztja az **kibővített kulcshasználat**. Győződjön meg arról, hogy *ügyfél-hitelesítés* az első elem a listában. Ellenkező esetben adja ki a felhasználó sablont, amelynek alapján ügyféltanúsítvány *ügyfél-hitelesítés* a lista első helyén.

- A pont–hely (P2S) hibaelhárítással kapcsolatos további információkért lásd: [Troubleshoot P2S connections](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) (Pont–hely kapcsolatok hibaelhárítása).
