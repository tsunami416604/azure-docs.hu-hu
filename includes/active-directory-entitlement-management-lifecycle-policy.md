---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: e971af5d415ea788f97ab11b58541e629c7eb0ed
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694339"
---
## <a name="lifecycle"></a>Életciklus

Az **életciklus** lapon megadhatja, hogy mikor járjon le egy felhasználó a hozzáférési csomaghoz. Megadhatja azt is, hogy a felhasználók ki tudják-e terjeszteni a hozzárendeléseiket.

1. A **lejárat** szakaszban adja meg a **hozzáférési csomag hozzárendeléseinek érvényességét** **dátum**, **nap**vagy **soha**értékre.

    A **on Date (dátum**) beállításnál válassza ki a lejárati dátumot a jövőben.

    A **napok számának**megadásához 0 és 3660 nap közötti számot kell megadni.

    A kiválasztás alapján a felhasználó a hozzáférési csomaghoz való hozzárendelése egy adott napon lejár, bizonyos számú nappal a jóváhagyás után, vagy soha nem.

1. További beállítások megjelenítéséhez kattintson a **speciális lejárati beállítások megjelenítése** lehetőségre.

    ![Hozzáférési csomag – életciklus-lejárati beállítások](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Annak engedélyezéséhez, hogy a felhasználó kiterjessze a hozzárendeléseket, állítsa a **felhasználók számára az** **Igen**értékre való kiterjesztést.

    Ha a kiterjesztések engedélyezve vannak a szabályzatban, a felhasználó 14 napig, a hozzáférési csomag hozzárendelésének megkezdése előtt pedig 1 nappal e-mailt kap, hogy lejárja a hozzárendelés kiterjesztését. Ha a felhasználó a hozzáférés kiterjesztésére vonatkozó kérést küld, a bővítmény dátumának vagy annál korábbinak kell lennie, mint a hozzáférési csomaghoz a felhasználói hozzáférés biztosításához használt Szabályzatban meghatározottak szerint. Ha például a házirend azt jelzi, hogy a hozzárendelések június 30-án lejárnak, a felhasználó által igényelhető maximális kiterjesztés június 30-ig tart.

    Ha a felhasználó hozzáférése ki van bővítve, a megadott kiterjesztési dátum (a szabályzatot létrehozó felhasználó időzónájában beállított dátum) után nem fogja tudni kérni a hozzáférési csomagot.

1. Ha jóváhagyásra van szüksége a bővítmény megadásához, állítsa a **jóváhagyás megkövetelése lehetőséget az** **Igen**értékre való kiterjesztés megadásához.

    A rendszer a **kérelmek** lapon megadott jóváhagyási beállításokat fogja használni.

1. Kattintson a **tovább** vagy a **frissítés**gombra.
