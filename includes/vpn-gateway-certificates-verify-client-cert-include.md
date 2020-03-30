---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67178978"
---
Ha problémái vannak a csatlakozáskor, ellenőrizze az alábbi elemeket:

- Ha a **Tanúsítványexportáló varázslóval**exportált egy ügyféltanúsítványt, győződjön meg arról, hogy .pfx fájlként exportálta, és lehetőség szerint **jelölje be az Összes tanúsítvány felvétele a tanúsítványláncba**. Ha ezzel az értékkel exportálja, a főtanúsítvány-adatok is exportálásra kerülnek. Miután telepítette a tanúsítványt az ügyfélszámítógépre, a .pfx fájlban lévő főtanúsítvány is telepítve lesz. A főtanúsítvány telepítésének ellenőrzéséhez nyissa meg a **Felhasználói tanúsítványok kezelése lehetőséget,** és válassza **a Megbízható legfelső szintű hitelesítésszolgáltatók\Tanúsítványok**lehetőséget. Ellenőrizze, hogy a főtanúsítvány szerepel-e a listában, amelynek jelen kell lennie ahhoz, hogy a hitelesítés működjön.

- Ha olyan tanúsítványt használt, amelyet vállalati hitelesítésszolgáltatói megoldás bocsátott ki, és nem tudja hitelesíteni magát, ellenőrizze az ügyféltanúsítvány hitelesítési sorrendjét. Ellenőrizze a hitelesítési lista sorrendjét úgy, hogy duplán kattint az ügyféltanúsítványra, a **Részletek** fülre, majd a **Bővített kulcshasználat parancsra.** Győződjön meg arról, hogy az *ügyfélhitelesítés* a lista első eleme. Ha nem, adjon ki egy ügyféltanúsítványt a lista első elemeként az *ügyfélhitelesítést* tartalmazó felhasználói sablon alapján.

- A pont–hely (P2S) hibaelhárítással kapcsolatos további információkért lásd: [Troubleshoot P2S connections](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) (Pont–hely kapcsolatok hibaelhárítása).