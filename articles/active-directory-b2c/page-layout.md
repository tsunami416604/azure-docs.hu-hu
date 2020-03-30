---
title: Oldalelrendezési verziók
titleSuffix: Azure AD B2C
description: Lapelrendezés verzióelőzményei a felhasználói felület testreszabásához az egyéni házirendekben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3d0cb06f84fdd96d099e05f55ba62c37cb1192c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183975"
---
# <a name="page-layout-versions"></a>Oldalelrendezési verziók

A lapelrendezési csomagok rendszeres időközönként frissülnek, hogy javításokkal és fejlesztésekkel egészítsék ki az oldalelemeiket. A következő változásnapló az egyes verziókban bevezetett módosításokat határozza meg.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- Saját érvényesített oldal`selfasserted`( )
  - Az egyéni házirendekben a [megjelenítési vezérlők](display-controls.md) támogatása hozzáadva.

## <a name="120"></a>1.2.0

- Minden oldal
  - Kisegítő lehetőségek javítása
  - Most már hozzáadhatja az attribútumot `data-preload="true"` [a HTML-címkékhez](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) a CSS és a JavaScript betöltési sorrendjének szabályozásához.
    - A csatolt CSS-fájlokat a HTML-sablonnal egy időben töltse be, így nem "villog" a fájlok betöltése között.
    - Az oldal betöltése előtt szabályozhatja a `script` címkék beolvasásának és végrehajtásának sorrendjét.
  - Az e-mail mező most, `type=email` és a mobil billentyűzetek a megfelelő javaslatokat
  - A Chrome-fordítás támogatása
- Egyesített és önérvényesítő oldalak
  - A felhasználónév/e-mail és `form` jelszó mezők mostantól a HTML-elemet használják, hogy az Edge és az Internet Explorer (IE) megfelelően menthesse ezt az információt.

## <a name="110"></a>1.1.0

- Kivétellap (globalexception)
  - Kisegítő lehetőségek javítása
  - Az alapértelmezett üzenet eltávolítása, ha nincs kapcsolat a házirendből
  - Alapértelmezett CSS eltávolítva
- MFA-oldal (többtényezős)
  - "Kód megerősítése" gomb eltávolítva
  - A kód beviteli mezője most antól csak hat (6) karaktert vesz fel
  - Az oldal automatikusan megpróbálja ellenőrizni a 6 jegyű kód beírásakor megadott kódot, anélkül, hogy bármilyen gombra rá kellene kattintania.
  - Ha a kód hibás, a beviteli mező automatikusan törlődik
  - Három (3) helytelen kóddal tett kísérlet után a B2C hibát küld vissza a függő entitásnak
  - Kisegítő lehetőségek javítása
  - Alapértelmezett CSS eltávolítva
- Önérvényesítő oldal (önérvényesítő)
  - Törölt megszakítási riasztás
  - CSS-osztály hibaelemekhez
  - A hibalogika megjelenítése/elrejtése javítva
  - Alapértelmezett CSS eltávolítva
- Egyesített SSP (unifiedssp)
  - Hozzáadva, hogy bejelentkezve maradjak (KMSI) vezérlő

## <a name="100"></a>1.0.0

- Kezdeti kiadás

## <a name="next-steps"></a>További lépések

Az alkalmazások felhasználói felületének egyéni házirendekben történő testreszabásáról [az Alkalmazás felhasználói felületének testreszabása egyéni házirend használatával](custom-policy-ui-customization.md)című témakörben talál további tudnivalókat.
