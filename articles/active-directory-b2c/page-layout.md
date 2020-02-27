---
title: Oldalelrendezési verziók
titleSuffix: Azure AD B2C
description: Az egyéni házirendek felhasználói felületének testreszabása az oldalelrendezés korábbi verzióiban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b24e8cc5f6bb6213bf021362c5654afd4d5266fb
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620614"
---
# <a name="page-layout-versions"></a>Oldalelrendezési verziók

A lapelrendezés-csomagok rendszeres időközönként frissülnek, hogy tartalmazzanak javításokat és javításokat a lapok elemeiben. A következő változási napló meghatározza az egyes verziókban bevezetett módosításokat.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- Önérvényesített oldal (`selfasserted`)
  - A [megjelenítési vezérlők](display-controls.md) támogatása az egyéni házirendekben.

## <a name="120"></a>1.2.0

- Minden oldal
  - Kisegítő lehetőségek – javítások
  - Most hozzáadhatja a `data-preload="true"` attribútumot a [HTML-címkékben](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) a CSS és a JavaScript betöltési sorrendjének szabályozásához.
    - A csatolt CSS-fájlokat a HTML-sablonnal megegyező időben töltse be, így nem "villódzásmentes" a fájlok betöltése között.
    - Szabályozhatja, hogy a rendszer hogyan olvassa be és hajtsa végre a `script` címkék sorrendjét az oldal betöltése előtt.
  - Az e-mail-mező mostantól `type=email` és a mobil billentyűzetek biztosítják a megfelelő javaslatokat
  - A Chrome fordításának támogatása
- Egyesített és önérvényesített lapok
  - A Felhasználónév/e-mail és jelszó mezők mostantól a `form` HTML-elemet használva lehetővé teszik az Edge és az Internet Explorer (IE) számára az adatok megfelelő mentését.

## <a name="110"></a>1.1.0

- Kivétel lap (globalexception)
  - Kisegítő lehetőségek javítása
  - Az alapértelmezett üzenet el lett távolítva, ha nincs kapcsolat a szabályzatból
  - Alapértelmezett CSS eltávolítva
- MFA-oldal (többtényezős)
  - A "kód megerősítése" gomb el lett távolítva
  - A kód bemeneti mezője csak hat (6) karakter bevitelét teszi elérhetővé.
  - Az oldal automatikusan megkísérli ellenőrizni a 6 számjegyű kód beírásakor megadott kódot, anélkül, hogy a gombokra rá kellene kattintani
  - Ha a kód nem megfelelő, a beviteli mező automatikusan törlődik
  - Ha a három (3) helytelen kóddal próbálkozik, a B2C hibát küld vissza a függő entitásnak.
  - Kisegítő lehetőségek – javítások
  - Alapértelmezett CSS eltávolítva
- Önérvényesített oldal (selfasserted)
  - Törlési riasztás eltávolítva
  - A hibás elemek CSS-osztálya
  - A hiba logikájának megjelenítése/elrejtése Továbbfejlesztve
  - Alapértelmezett CSS eltávolítva
- Unified SSP (unifiedssp)
  - A bejelentkezett (KMSI) vezérlőelem hozzáadása

## <a name="100"></a>1.0.0

- Kezdeti kiadás

## <a name="next-steps"></a>Következő lépések

Az alkalmazások felhasználói felületének egyéni házirendekben való testreszabásával kapcsolatos további információkért lásd [az alkalmazás felhasználói felületének egyéni házirenddel való testreszabását](custom-policy-ui-customization.md)ismertető témakört.
