---
title: Oktatóanyag – felhasználói folyamatok - létrehozása az Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre az alkalmazások az Azure Portalon az Azure Active Directory B2C felhasználói folyamatokat.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b76d56b17973c04992a8855917c814ced649bd0f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338258"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Oktatóanyag: Felhasználói folyamatok létrehozása az Azure Active Directory B2C-vel

Az Ön alkalmazásai, előfordulhat, hogy rendelkezik [felhasználókövetési adatai](active-directory-b2c-reference-policies.md) , amelyekkel a felhasználók a regisztráció, bejelentkezés vagy saját profil kezelése. Hozzon létre a különböző típusú több felhasználói folyamatot az Azure Active Directory (Azure AD) B2C-bérlőben, és igény szerint használhatja őket az alkalmazásokban. Felhasználói folyamatok alkalmazásokhoz felhasználhatók.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Regisztráció és bejelentkezés a felhasználói folyamat létrehozása
> * Profilszerkesztési felhasználói folyamata létrehozása
> * Jelszó alaphelyzetbe állítása felhasználói folyamat létrehozása

Ez az oktatóanyag bemutatja, hogyan hozhat létre néhány ajánlott felhasználókövetési adatai az Azure portal használatával. Hogyan állítható be egy erőforrás-tulajdonos jelszavas hitelesítő adatokat (ROPC) folyamatot az alkalmazásban kapcsolatos információkat keres, ha [konfigurálása az erőforrás tulajdonosának jelszavas hitelesítő adatai az Azure AD B2C flow](configure-ropc.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

[Az alkalmazások regisztrálását](tutorial-register-applications.md) a létrehozni kívánt felhasználói folyamatok részét. 

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Regisztráció és bejelentkezés a felhasználói folyamat létrehozása

A regisztráció és bejelentkezés a felhasználói folyamatot egyetlen konfigurációval is regisztráljon, és jelentkezzen be környezeteket kezeli. Az alkalmazást a rendszer le a megfelelő elérési útját a környezettől függően vezetett.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.

    ![Váltás előfizetési könyvtár](./media/tutorial-create-user-flows/switch-directories.png)

3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. A bal oldali menüben válassza ki a **felhasználói folyamatok**, majd válassza ki **új felhasználói folyamat**.

    ![Válassza ki az új felhasználói folyamat](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

5. Válassza ki a **regisztráció és bejelentkezés** felhasználói folyamat az ajánlott lapon.

    ![Válassza ki a regisztrációs és bejelentkezési felhasználói folyamata](./media/tutorial-create-user-flows/signup-signin-type.png)

6. Adjon meg egy **neve** a felhasználói folyamat. Ha például *signupsignin1*.
7. A **Identitásszolgáltatók**válassza **regisztráció E-mail-címmel**.

    ![A folyamat tulajdonságainak beállítása](./media/tutorial-create-user-flows/signup-signin-properties.png)

8. A **felhasználói attribútumokról és jogcímekről**, válassza a jogcímek és az attribútumokat, amelyek gyűjtése és a regisztráció során a felhasználónak küldött. Válassza ki például **Továbbiak megjelenítése**, és válassza a **ország/régió**, **megjelenített név**, és **irányítószám**. Kattintson az **OK** gombra.

    ![Válassza ki a attribútumokról és jogcímekről](./media/tutorial-create-user-flows/signup-signin-attributes.png)

9. Kattintson a **létrehozás** hozzáadása a felhasználói folyamatot. Az előtag *B2C_1* automatikusan hozzáfűzi a rendszer a neve.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. A létrehozott felhasználói folyamat áttekintése lapon válassza az **felhasználói folyamat futtatása**.
2. A **alkalmazás**, válassza ki a webalkalmazás nevű *webapp1* , amely korábban regisztrálva. A **válasz URL-cím** megjelennie `https://jwt.ms`.
3. Kattintson a **felhasználói folyamat futtatása**, majd válassza ki **regisztráció**.

    ![A felhasználói folyamat futtatása](./media/tutorial-create-user-flows/signup-signin-run-now.png)

4. Adja meg egy érvényes e-mail címet, és kattintson a **ellenőrző kód küldése**, majd adja meg az ellenőrzőkódot tartalmazó kap.
5. Adjon meg egy új jelszót, és erősítse meg a jelszót.
6. Adja meg a kívánt nevet jelenik meg, válassza ki az ország és régió, adja meg az irányítószámot és kattintson a **létrehozás**. A jogkivonat visszaadott `https://jwt.ms` és üzenetnek kell megjelennie az Ön számára.
7. Mostantól a felhasználói folyamat ismét futtathatja, és, jelentkezzen be azzal a fiókkal létrehozott képesnek kell lennie. A visszaadott jogkivonat neve, ország és postai irányítószám kiválasztott jogcímeket tartalmaz.

## <a name="create-a-profile-editing-user-flow"></a>Profilszerkesztési felhasználói folyamata létrehozása

Ha azt szeretné, szerkesztheti a profilját, az alkalmazás a felhasználók, használhat egy Profilszerkesztési felhasználói folyamata.

1. A bal oldali menüben válassza ki a **felhasználói folyamatok**, majd válassza ki **új felhasználói folyamat**.
2. Válassza ki a **profilszerkesztést** felhasználói folyamat az ajánlott lapon.
3. Adjon meg egy **neve** a felhasználói folyamat. Ha például *profileediting1*.
4. A **Identitásszolgáltatók**válassza **helyi fiókba**.
5. A **felhasználói attribútumok**, válassza ki az attribútumokat, amelyek azt szeretné, hogy az ügyfél számára a profilján belül szerkesztheti. Válassza ki például **Továbbiak megjelenítése**, és válassza a **megjelenítendő név** és **beosztás**. Kattintson az **OK** gombra.
6. Kattintson a **létrehozás** hozzáadása a felhasználói folyamatot. Az előtag *B2C_1* automatikusan hozzáfűzi a rendszer a neve.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. A létrehozott felhasználói folyamat áttekintése lapon válassza az **felhasználói folyamat futtatása**.
2. A **alkalmazás**, válassza ki a webalkalmazás nevű *webapp1* , amely korábban regisztrálva. A **válasz URL-cím** megjelennie `https://jwt.ms`.
3. Kattintson a **felhasználói folyamat futtatása**, majd jelentkezzen be a fiókot, amelyet korábban hozott létre.
4. Most már lehetősége van módosítani a felhasználó megjelenített nevét és címét. Kattintson a **Folytatás** gombra. A jogkivonat visszaadott `https://jwt.ms` és üzenetnek kell megjelennie az Ön számára.

## <a name="create-a-password-reset-user-flow"></a>Jelszó alaphelyzetbe állítása felhasználói folyamat létrehozása

Fontos, hogy lehetővé teszik a felhasználónak az alkalmazás visszaállíthatják a jelszavukat, ha szükséges. Ahhoz, hogy a jelszó-visszaállítás, használhatja a jelszó alaphelyzetbe állítása felhasználói folyamat.

1. A bal oldali menüben válassza ki a **felhasználói folyamatok**, majd válassza ki **új felhasználói folyamat**.
2. Válassza ki a **új jelszó kérésére vonatkozó** felhasználói folyamat az ajánlott lapon.
3. Adjon meg egy **neve** a felhasználói folyamat. Ha például *passwordreset1*.
4. A **Identitásszolgáltatók**, engedélyezze **jelszó visszaállítása e-mail-címmel**.
5. Kattintson a jogcímek, az alkalmazás **Továbbiak megjelenítése** , és válassza ki a jogcímeket, amelyeket szeretne visszaküldött hitelesítő jogkivonatokban az alkalmazásnak. Válassza például a **Felhasználó objektumazonosítója** lehetőséget.
6. Kattintson az **OK** gombra.
7. Kattintson a **létrehozás** hozzáadása a felhasználói folyamatot. Az előtag *B2C_1* automatikusan hozzáfűzi a rendszer a neve.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. A létrehozott felhasználói folyamat áttekintése lapon válassza az **felhasználói folyamat futtatása**.
2. A **alkalmazás**, válassza ki a webalkalmazás nevű *webapp1* , amely korábban regisztrálva. A **válasz URL-cím** megjelennie `https://jwt.ms`.
3. Kattintson a **felhasználói folyamat futtatása**, majd jelentkezzen be a fiókot, amelyet korábban hozott létre.
4. Most már lehetősége van módosítani a jelszót a felhasználó számára. Kattintson a **Folytatás** gombra. A jogkivonat visszaadott `https://jwt.ms` és üzenetnek kell megjelennie az Ön számára.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Regisztráció és bejelentkezés a felhasználói folyamat létrehozása
> * Profilszerkesztési felhasználói folyamata létrehozása
> * Jelszó alaphelyzetbe állítása felhasználói folyamat létrehozása

> [!div class="nextstepaction"]
> [Identitásszolgáltató hozzáadása az alkalmazásait az Azure Active Directory B2C-vel](tutorial-add-identity-providers.md)