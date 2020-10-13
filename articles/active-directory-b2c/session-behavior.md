---
title: Munkamenet-viselkedés konfigurálása – Azure Active Directory B2C | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a munkamenet viselkedését Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 091704fabb7b50a0c83625c6ae46d9a807f01ffc
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961034"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Munkamenet-viselkedés konfigurálása Azure Active Directory B2Cban

Az [egyszeri bejelentkezés (SSO) munkamenet](session-overview.md) -kezelője Azure Active Directory B2C (Azure ad B2C) lehetővé teszi a rendszergazda számára, hogy a felhasználó már hitelesített hitelesítése után vezérelje a felhasználókkal való interakciót. A rendszergazda például megadhatja, hogy megjelenjen-e az identitás-szolgáltatók kiválasztása, vagy hogy meg kell-e adni a fiók adatait. Ez a cikk a Azure AD B2C egyszeri bejelentkezéses beállításainak konfigurálását ismerteti.

## <a name="session-behavior-properties"></a>Munkamenet viselkedési tulajdonságai

A webalkalmazás-munkamenetek kezeléséhez a következő tulajdonságokat használhatja:

- **Webalkalmazás-munkamenet élettartama (perc)** – sikeres hitelesítés után a felhasználó böngészőjében tárolt Azure ad B2C's-munkamenet-cookie élettartama.
    - Alapértelmezett = 1440 perc.
    - Minimum (inkluzív) = 15 perc.
    - Maximum (inkluzív) = 1440 perc.
- **Webalkalmazás-munkamenet időtúllépése** – a [munkamenet lejárati típusa](session-overview.md#session-expiry-type), *működés közbeni*vagy *abszolút*értéke. 
- **Egyszeri bejelentkezés konfigurálása** – az egyszeri bejelentkezés (SSO) viselkedési [hatóköre](session-overview.md#session-scope) több alkalmazás és felhasználói folyamat között a Azure ad B2C-bérlőben.


## <a name="configure-the-properties"></a>A tulajdonságok konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőt tartalmazza.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza a **felhasználói folyamatok**lehetőséget.
5. Nyissa meg a korábban létrehozott felhasználói folyamatot.
6. Válassza ki a **Tulajdonságok** elemet.
7. Konfigurálja a **webalkalmazás-munkamenet élettartamát (perc)**, a **webalkalmazás-munkamenet időkorlátját**, az **egyszeri bejelentkezés konfigurációját**, és szükség szerint adja **meg az azonosító tokent a kijelentkezési kérésekben** .

    ![A munkamenet viselkedési tulajdonságának beállításai a Azure Portalban](./media/session-behavior/session-behavior.png)

8. Kattintson a **Mentés** gombra.

## <a name="configure-sign-out-behavior"></a>Kijelentkezési viselkedés konfigurálása

### <a name="secure-your-logout-redirect"></a>A kijelentkezési átirányítás biztonságossá tétele

A kijelentkezés után a rendszer átirányítja a felhasználót a paraméterben megadott URI-ra `post_logout_redirect_uri` , az alkalmazáshoz megadott válasz URL-címektől függetlenül. Ha azonban érvényes `id_token_hint` értéket ad át, és a **kijelentkezési kérelmekhez szükséges azonosító jogkivonat** be van kapcsolva, Azure ad B2C ellenőrzi, hogy az adott `post_logout_redirect_uri` alkalmazás beállított átirányítási URI azonosítóinak egyike megfelel-e az átirányítás végrehajtása előtt. Ha nincs beállítva egyező válasz URL-cím az alkalmazáshoz, a rendszer hibaüzenetet jelenít meg, és a felhasználót nem irányítja át. AZONOSÍTÓ jogkivonat megkövetelése a kijelentkezési kérelmekben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőt tartalmazza.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza a **felhasználói folyamatok**lehetőséget.
1. Nyissa meg a korábban létrehozott felhasználói folyamatot.
1. Válassza ki a **Tulajdonságok** elemet.
1. Engedélyezze az **azonosító jogkivonat megkövetelése lehetőséget a kijelentkezési kérelmekben**.
1. Térjen vissza a  **Azure ad B2Cra**.
1. Válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza ki az alkalmazást.
1. Válassza a **Hitelesítés** lehetőséget.
1. A **kijelentkezési URL-cím** szövegmezőbe írja be a kijelentkezési ÁTirányítási URI-t, majd kattintson a **Mentés**gombra.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure ad B2C-munkamenetről](session-overview.md).
