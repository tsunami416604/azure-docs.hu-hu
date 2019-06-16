---
title: Oktatóanyag – felhasználói folyamatok - létrehozása az Azure Active Directory B2C-vel
description: Megtudhatja, hogyan hozhat létre a felhasználói folyamatok bejelentkezés engedélyezése mentése, jelentkezzen be az Azure Portalon, és a felhasználói profil módosítása az Azure Active Directory B2C-alkalmazások.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 111196388d0e17ecde8a2055959f2f573e43ade8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056344"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Oktatóanyag: Felhasználói folyamatok létrehozása az Azure Active Directory B2C-vel

Az alkalmazások szükség lehet [felhasználókövetési adatai](active-directory-b2c-reference-policies.md) , amelyekkel a felhasználók a regisztráció, bejelentkezés vagy saját profil kezelése. Hozzon létre a különböző típusú több felhasználói folyamatot az Azure Active Directory (Azure AD) B2C-bérlőben, és igény szerint használhatja őket az alkalmazásokban. Felhasználói folyamatok alkalmazásokhoz felhasználhatók.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Regisztráció és bejelentkezés a felhasználói folyamat létrehozása
> * Profilszerkesztési felhasználói folyamata létrehozása
> * Jelszó alaphelyzetbe állítása felhasználói folyamat létrehozása

Ez az oktatóanyag bemutatja, hogyan hozhat létre néhány ajánlott felhasználókövetési adatai az Azure portal használatával. Ha, hogyan állítható be egy erőforrás-tulajdonos jelszavas hitelesítő adatokat (ROPC) folyamatot az alkalmazásban kapcsolatos információkat keres, tekintse meg [konfigurálása az erőforrás tulajdonosának jelszavas hitelesítő adatai az Azure AD B2C flow](configure-ropc.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

[Az alkalmazások regisztrálását](tutorial-register-applications.md) a létrehozni kívánt felhasználói folyamatok részét.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Regisztráció és bejelentkezés a felhasználói folyamat létrehozása

A regisztráció és bejelentkezés a felhasználói folyamatot egyetlen konfigurációval is regisztráljon, és jelentkezzen be környezeteket kezeli. Az alkalmazást a rendszer le a megfelelő elérési útját a környezettől függően vezetett.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.

    ![Váltás előfizetési könyvtár](./media/tutorial-create-user-flows/switch-directories.PNG)

1. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
1. A bal oldali menüben lévő **házirendek**válassza **felhasználókövetési adatai (szabályzatok)** , majd válassza ki **új felhasználói folyamat**.

    ![Válassza ki az új felhasználói folyamat](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Az a **ajánlott** lapon jelölje be a **jelentkezzen be, és jelentkezzen be a** felhasználói folyamatot.

    ![Válassza ki a regisztrációs és bejelentkezési felhasználói folyamata](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Adjon meg egy **neve** a felhasználói folyamat. Ha például *signupsignin1*.
1. A **Identitásszolgáltatók**válassza **regisztráció E-mail-címmel**.

    ![A folyamat tulajdonságainak beállítása](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. A **felhasználói attribútumokról és jogcímekről**, válassza a jogcímek és az attribútumokat, amelyek gyűjtése és a regisztráció során a felhasználónak küldött. Válassza ki például **Továbbiak megjelenítése**, majd válassza a attribútumokról és jogcímekről a **ország/régió**, **megjelenített név**, és **irányítószám**. Kattintson az **OK** gombra.

    ![Válassza ki a attribútumokról és jogcímekről](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Kattintson a **létrehozás** hozzáadása a felhasználói folyamatot. Az előtag *B2C_1* automatikusan hozzáfűzi a rendszer a neve.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza ki a felhasználói folyamatot, az Áttekintés lap megnyitásához, majd válassza ki a létrehozott **felhasználói folyamat futtatása**.
1. A **alkalmazás**, válassza ki a webalkalmazás nevű *webapp1* , amely korábban regisztrálva. A **válasz URL-cím** megjelennie `https://jwt.ms`.
1. Kattintson a **felhasználói folyamat futtatása**, majd válassza ki **regisztráció**.

    ![A felhasználói folyamat futtatása](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Adja meg egy érvényes e-mail címet, és kattintson a **ellenőrző kód küldése**, adja meg az ellenőrző kódot, amely fogadni, majd válassza ki **kód ellenőrzése**.
1. Adjon meg egy új jelszót, és erősítse meg a jelszót.
1. Válassza ki az ország és régió, adja meg a kívánt megjelenített név, postai irányítószám adja meg, és kattintson **létrehozás**. A jogkivonat visszaadott `https://jwt.ms` és üzenetnek kell megjelennie az Ön számára.
1. Mostantól a felhasználói folyamat ismét futtathatja, és, jelentkezzen be azzal a fiókkal létrehozott képesnek kell lennie. A visszaadott jogkivonat tartalmazza a kiválasztott ország/régió, a név és postai irányítószám jogcímeket.

## <a name="create-a-profile-editing-user-flow"></a>Profilszerkesztési felhasználói folyamata létrehozása

Ha azt szeretné, szerkesztheti a profilját, az alkalmazás a felhasználók, használhat egy Profilszerkesztési felhasználói folyamata.

1. Az Azure AD B2C bérlő áttekintés oldal bal oldali menüben válassza ki a **felhasználókövetési adatai (szabályzatok)** , majd válassza ki **új felhasználói folyamat**.
1. Válassza ki a **profilszerkesztést** felhasználói folyamat az ajánlott lapon.
1. Adjon meg egy **neve** a felhasználói folyamat. Ha például *profileediting1*.
1. A **Identitásszolgáltatók**válassza **helyi fiókba**.
1. A **felhasználói attribútumok**, válassza ki az attribútumokat, amelyek azt szeretné, hogy az ügyfél számára a profilján belül szerkesztheti. Válassza ki például **Továbbiak megjelenítése**, majd kattintson az attribútumok és a jogcímek az **megjelenített név** és **beosztás**. Kattintson az **OK** gombra.
1. Kattintson a **létrehozás** hozzáadása a felhasználói folyamatot. Az előtag *B2C_1* automatikusan hozzáfűzi a rendszer a neve.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza ki a felhasználói folyamatot, az Áttekintés lap megnyitásához, majd válassza ki a létrehozott **felhasználói folyamat futtatása**.
1. A **alkalmazás**, válassza ki a webalkalmazás nevű *webapp1* , amely korábban regisztrálva. A **válasz URL-cím** megjelennie `https://jwt.ms`.
1. Kattintson a **felhasználói folyamat futtatása**, majd jelentkezzen be a fiókot, amelyet korábban hozott létre.
1. Most már lehetősége van módosítani a felhasználó megjelenített nevét és címét. Kattintson a **Folytatás** gombra. A jogkivonat visszaadott `https://jwt.ms` és üzenetnek kell megjelennie az Ön számára.

## <a name="create-a-password-reset-user-flow"></a>Jelszó alaphelyzetbe állítása felhasználói folyamat létrehozása

Ahhoz, hogy a felhasználók visszaállíthatják a jelszavukat az alkalmazás, használhat egy jelszó alaphelyzetbe állítása felhasználói folyamat.

1. A bal oldali menüben válassza ki a **felhasználókövetési adatai (szabályzatok)** , majd válassza ki **új felhasználói folyamat**.
1. Válassza ki a **új jelszó kérésére vonatkozó** felhasználói folyamat az ajánlott lapon.
1. Adjon meg egy **neve** a felhasználói folyamat. Ha például *passwordreset1*.
1. A **Identitásszolgáltatók**, engedélyezze **jelszó visszaállítása e-mail-címmel**.
1. Kattintson a jogcímek, az alkalmazás **Továbbiak megjelenítése** , és válassza ki a jogcímeket, amelyeket szeretne visszaküldött hitelesítő jogkivonatokban az alkalmazásnak. Válassza például a **Felhasználó objektumazonosítója** lehetőséget.
1. Kattintson az **OK** gombra.
1. Kattintson a **létrehozás** hozzáadása a felhasználói folyamatot. Az előtag *B2C_1* automatikusan hozzáfűzi a rendszer a neve.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza ki a felhasználói folyamatot, az Áttekintés lap megnyitásához, majd válassza ki a létrehozott **felhasználói folyamat futtatása**.
1. A **alkalmazás**, válassza ki a webalkalmazás nevű *webapp1* , amely korábban regisztrálva. A **válasz URL-cím** megjelennie `https://jwt.ms`.
1. Kattintson a **felhasználói folyamat futtatása**, győződjön meg arról, hogy a korábban létrehozott, és válassza ki a fiók e-mail-címe **Folytatás**.
1. Most már lehetősége van módosítani a jelszót a felhasználó számára. Módosítsa a jelszót, és válassza ki **Folytatás**. A jogkivonat visszaadott `https://jwt.ms` és üzenetnek kell megjelennie az Ön számára.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Regisztráció és bejelentkezés a felhasználói folyamat létrehozása
> * Profilszerkesztési felhasználói folyamata létrehozása
> * Jelszó alaphelyzetbe állítása felhasználói folyamat létrehozása

Ezután a felhasználó jelentkezzen be például az Azure AD-ben engedélyezése az alkalmazások identitás-szolgáltatóktól hozzáadásával kapcsolatos további Amazon, a Facebook, a GitHub, a LinkedIn, a Microsoft vagy a Twitter.

> [!div class="nextstepaction"]
> [Az alkalmazások identitás-szolgáltatóktól hozzáadása >](tutorial-add-identity-providers.md)