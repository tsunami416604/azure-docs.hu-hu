---
title: Oldalelrendezési verziók
titleSuffix: Azure AD B2C
description: Az egyéni házirendek felhasználói felületének testreszabása az oldalelrendezés korábbi verzióiban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 868d99a82009dc8545fc24ad1cfa1da3959da131
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852079"
---
# <a name="page-layout-versions"></a>Oldalelrendezési verziók

A lapelrendezés-csomagok rendszeres időközönként frissülnek, hogy tartalmazzanak javításokat és javításokat a lapok elemeiben. A következő változási napló meghatározza az egyes verziókban bevezetett módosításokat.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="self-asserted-page-selfasserted"></a>Önérvényesített oldal (selfasserted)

**2.1.0**

- Honosítási és kisegítő lehetőségek javításai.

**2.0.0**

- A [megjelenítési vezérlők](display-controls.md) támogatása az egyéni házirendekben.

**1.2.0**

- A Felhasználónév/e-mail és jelszó mezők mostantól a `form` HTML-elemmel lehetővé teszik az Edge és az Internet Explorer (IE) számára az adatok megfelelő mentését.
- Egy konfigurálható felhasználói bemeneti ellenőrzési késleltetés lett hozzáadva a továbbfejlesztett felhasználói élményhez.
- Kisegítő lehetőségek – javítások
- Most hozzáadhatja az `data-preload="true"` attribútumot a [HTML-címkékben](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) a CSS és a JavaScript betöltési sorrendjének szabályozásához.
  - A csatolt CSS-fájlokat a HTML-sablonnal megegyező időben töltse be, így nem "villódzásmentes" a fájlok betöltése között.
  - Szabályozza a `script` címkék beolvasásának és végrehajtásának sorrendjét az oldal betöltése előtt.
- Az e-mail-mező most `type=email` , a mobil billentyűzetek pedig a megfelelő javaslatokat nyújtják
- A Chrome fordításának támogatása

**1.1.0**

- Törlési riasztás eltávolítva
- A hibás elemek CSS-osztálya
- A hiba logikájának megjelenítése/elrejtése Továbbfejlesztve
- Alapértelmezett CSS eltávolítva

**1.0.0**

- Kezdeti kiadás

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>Egyesített bejelentkezési regisztrációs oldal a jelszó-visszaállítási hivatkozással (unifiedssp)

**2.1.0**

- Több regisztrációs hivatkozás támogatása.
- A felhasználói bevitel érvényesítésének támogatása a szabályzatban meghatározott predikátum-szabályoknak megfelelően.

**1.2.0**

- A Felhasználónév/e-mail és jelszó mezők mostantól a `form` HTML-elemmel lehetővé teszik az Edge és az Internet Explorer (IE) számára az adatok megfelelő mentését.
- Kisegítő lehetőségek – javítások
- Most hozzáadhatja az `data-preload="true"` attribútumot a [HTML-címkékben](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) a CSS és a JavaScript betöltési sorrendjének szabályozásához.
  - A csatolt CSS-fájlokat a HTML-sablonnal megegyező időben töltse be, így nem "villódzásmentes" a fájlok betöltése között.
  - Szabályozza a `script` címkék beolvasásának és végrehajtásának sorrendjét az oldal betöltése előtt.
- Az e-mail-mező most `type=email` , a mobil billentyűzetek pedig a megfelelő javaslatokat nyújtják
- A Chrome fordításának támogatása

**1.1.0**

- A bejelentkezett (KMSI) vezérlőelem hozzáadása

**1.0.0**

- Kezdeti kiadás

## <a name="mfa-page-multifactor"></a>MFA-oldal (többtényezős)

**1.2.1**

- Kisegítő lehetőségek javításai az alapértelmezett sablonokban

**1.2.0**

- Kisegítő lehetőségek – javítások
- Most hozzáadhatja az `data-preload="true"` attribútumot a [HTML-címkékben](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) a CSS és a JavaScript betöltési sorrendjének szabályozásához.
  - A csatolt CSS-fájlokat a HTML-sablonnal megegyező időben töltse be, így nem "villódzásmentes" a fájlok betöltése között.
  - Szabályozza a `script` címkék beolvasásának és végrehajtásának sorrendjét az oldal betöltése előtt.
- Az e-mail-mező most `type=email` , a mobil billentyűzetek pedig a megfelelő javaslatokat nyújtják
- A Chrome fordításának támogatása

**1.1.0**

- A "kód megerősítése" gomb el lett távolítva
- A kód bemeneti mezője csak hat (6) karakter bevitelét teszi elérhetővé.
- Az oldal automatikusan megkísérli ellenőrizni a 6 számjegyű kód beírásakor megadott kódot, anélkül, hogy a gombokra rá kellene kattintani
- Ha a kód nem megfelelő, a beviteli mező automatikusan törlődik
- Ha a három (3) helytelen kóddal próbálkozik, a B2C hibát küld vissza a függő entitásnak.
- Kisegítő lehetőségek – javítások
- Alapértelmezett CSS eltávolítva

**1.0.0**

- Kezdeti kiadás

## <a name="exception-page-globalexception"></a>Kivétel lap (globalexception)

**1.2.0**

- Kisegítő lehetőségek – javítások
- Most hozzáadhatja az `data-preload="true"` attribútumot a [HTML-címkékben](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) a CSS és a JavaScript betöltési sorrendjének szabályozásához.
  - A csatolt CSS-fájlokat a HTML-sablonnal megegyező időben töltse be, így nem "villódzásmentes" a fájlok betöltése között.
  - Szabályozza a `script` címkék beolvasásának és végrehajtásának sorrendjét az oldal betöltése előtt.
- Az e-mail-mező most `type=email` , a mobil billentyűzetek pedig a megfelelő javaslatokat nyújtják
- A Chrome fordításának támogatása

**1.1.0**

- Kisegítő lehetőségek javítása
- Az alapértelmezett üzenet el lett távolítva, ha nincs kapcsolat a szabályzatból
- Alapértelmezett CSS eltávolítva

**1.0.0**

- Kezdeti kiadás

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>Egyéb lapok (ProviderSelection, ClaimsConsent, UnifiedSSD)

**1.2.0**

- Kisegítő lehetőségek – javítások
- Most hozzáadhatja az `data-preload="true"` attribútumot a [HTML-címkékben](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) a CSS és a JavaScript betöltési sorrendjének szabályozásához.
  - A csatolt CSS-fájlokat a HTML-sablonnal megegyező időben töltse be, így nem "villódzásmentes" a fájlok betöltése között.
  - Szabályozza a `script` címkék beolvasásának és végrehajtásának sorrendjét az oldal betöltése előtt.
- Az e-mail-mező most `type=email` , a mobil billentyűzetek pedig a megfelelő javaslatokat nyújtják
- A Chrome fordításának támogatása

**1.0.0**

- Kezdeti kiadás

## <a name="next-steps"></a>További lépések

Az alkalmazások felhasználói felületének egyéni házirendekben való testreszabásával kapcsolatos további információkért lásd [az alkalmazás felhasználói felületének egyéni házirenddel való testreszabását](custom-policy-ui-customization.md)ismertető témakört.
