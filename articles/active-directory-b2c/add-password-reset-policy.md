---
title: Jelszó-visszaállítási folyamat beállítása
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan állíthatja be a jelszó-visszaállítási folyamatot a Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6e6e4aa4ece781f415308b638323f8d4d4b34038
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618754"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Jelszó-visszaállítási folyamat beállítása Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-rest-flow"></a>Jelszó – Rest-folyamat

A jelszó-visszaállítási házirend lehetővé teszi a felhasználók számára a saját elfelejtett jelszavuk visszaállítását. A jelszó-visszaállítási folyamat a következő lépéseket foglalja magában: 
1. A regisztrációs és bejelentkezési oldalon a felhasználó az "Elfelejtette a jelszavát?" gombra kattint hivatkozással. Azure AD B2C visszaadja az alkalmazás AADB2C90118 hibakódját. Az alkalmazás a jelszó-visszaállítási házirend meghívásával kezeli ezt a hibakódot. 
1. A felhasználók megadhatják és ellenőrizhetik az e-mailjeit az egyszer használatos jelszóval.
1. Adjon meg egy új jelszót.

![Jelszó-visszaállítási folyamat](./media/add-password-reset-policy/password-reset-flow.png)

## <a name="prerequisites"></a>Előfeltételek

Ha még nem tette meg, [regisztráljon egy webalkalmazást Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-password-reset-user-flow"></a>Jelszó-visszaállítási felhasználói folyamat létrehozása

Ha engedélyezni szeretné, hogy az alkalmazás felhasználói visszaállítsa a jelszavukat, jelszó-visszaállítási felhasználói folyamatot kell használnia.

1. A Azure AD B2C bérlő áttekintő menüjében válassza a **felhasználói folyamatok** lehetőséget, majd válassza az **új felhasználói folyamat** elemet.
1. A **felhasználói folyamat létrehozása** lapon válassza a **jelszó alaphelyzetbe állítása** lehetőséget. 
1. **A verzió kiválasztása** területen válassza az **ajánlott** lehetőséget, majd válassza a **Létrehozás** lehetőséget.
1. Adja meg a felhasználói folyamat **nevét** . Például: *passwordreset1*.
1. Az **Identity Providers** esetében engedélyezze a **jelszó alaphelyzetbe állítását e-mail-cím használatával**.
2. Az alkalmazás jogcímei területen kattintson a **továbbiak megjelenítése** lehetőségre, és válassza ki azokat a jogcímeket, amelyeket vissza szeretne állítani az alkalmazásnak visszaküldött engedélyezési jogkivonatokban. Válassza például a **Felhasználó objektumazonosítója** lehetőséget.
3. Kattintson az **OK** gombra.
4. A felhasználói folyamat hozzáadásához kattintson a **Létrehozás** gombra. Az *B2C_1* előtagját a rendszer automatikusan hozzáfűzi a névhez.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza ki a létrehozott felhasználói folyamatot az Áttekintés oldal megnyitásához, majd válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre, ellenőrizze a korábban létrehozott fiók e-mail-címét, majd válassza a **Folytatás** lehetőséget.
1. Most lehetősége van a felhasználó jelszavának módosítására. Módosítsa a jelszót, és válassza a **Folytatás** lehetőséget. A rendszer visszaküldi a tokent, `https://jwt.ms` és megjelenik Önnek.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-password-reset-policy"></a>Új jelszó kérésére vonatkozó szabályzat létrehozása

Az egyéni házirendek olyan XML-fájlok készletei, amelyeket feltölt a Azure AD B2C bérlőre a felhasználói útvonalak definiálásához. Alapszintű csomagokat biztosítunk számos előre elkészített házirenddel, többek között a regisztrálással és a bejelentkezéssel, a jelszó-visszaállítással és a profil szerkesztési házirendjével. További információ: Ismerkedés [az egyéni szabályzatokkal Azure ad B2Cban](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>További lépések

[Profil szerkesztési folyamatának](add-profile-editing-policy.md)beállítása.