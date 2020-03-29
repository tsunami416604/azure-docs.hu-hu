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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76694339"
---
## <a name="lifecycle"></a>Életciklus

Az Életciklus lapon **megadhatja,** hogy egy felhasználó hozzárendelése a hozzáférési csomaghoz mikor jár le. Azt is megadhatja, hogy a felhasználók meghosszabbíthatják-e hozzárendeléseiket.

1. A **Lejárat** szakaszban állítsa be az **Access csomag-hozzárendelések dátuma kor** **,** **Napok száma**vagy **Soha**.

    A **Dátum lehetőséget**válassza ki a jövőben egy lejárati dátumot.

    A **Napok száma**területen adjon meg 0 és 3660 nap közötti számot.

    A választás alapján a felhasználó hozzáférési csomaghoz való hozzárendelése egy bizonyos napon, a jóváhagyásuk után bizonyos számú napon lejár, vagy soha.

1. Kattintson **a Speciális elévülési beállítások megjelenítése** gombra a további beállítások megjelenítéséhez.

    ![Access csomag – Életciklus-elévülési beállítások](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Ha engedélyezni szeretné a felhasználóknak a hozzárendelések bővítését, állítsa be a **Hozzáférés engedélyezése** a felhasználók nak az **Igen**beállításra.

    Ha a bővítmények engedélyezettek a házirendben, a felhasználó 14 nappal és 1 nappal a hozzáférési csomag-hozzárendelés lejárta előtt e-mailt kap, amelyben a hozzárendelés meghosszabbítására kéri őket. Ha a felhasználó a hozzáférés kiterjesztésére vonatkozó kérelmet küld, a kiterjesztés dátumának a hozzáférési csomaghoz való hozzáférés biztosításához használt házirendben meghatározott hozzárendelések lejártakor vagy azt megelőzően kell lennie. Ha például a házirend azt jelzi, hogy a hozzárendelések június 30-án lejárnak, a felhasználó által igényelhető maximális bővítmény június 30.

    Ha a felhasználó hozzáférése meghosszabbodik, nem fogja tudni kérni a hozzáférési csomagot a megadott kiterjesztési dátum után (a házirendet létrehozó felhasználó időzónájában beállított dátum).

1. Ha a kiterjesztés megadásához jóváhagyást szeretne igényelni, állítsa be a Jóváhagyás megkövetelése az **Igen** **kiterjesztés megadásához.**

    A kérelmek lapon megadott jóváhagyási beállítások at fogja használni **a(z) .**

1. Kattintson a **Tovább** gombra vagy **a Frissítés gombra.**
