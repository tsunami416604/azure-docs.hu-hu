---
title: Oktatóanyag – az alkalmazások regisztrálása az Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg, hogyan kell regisztrálni az alkalmazásait az Azure Active Directory B2C-vel, az Azure portal használatával.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/11/2019
ms.author: davidmu
ms.openlocfilehash: 511e1e9f29e6ae7602a977819f5295f76236595d
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54248983"
---
# <a name="tutorial-register-your-applications-in-azure-active-directory-b2c"></a>Oktatóanyag: Az alkalmazások regisztrálása az Azure Active Directory B2C-vel

Mielőtt a [alkalmazások](active-directory-b2c-apps.md) kezelheti az Azure Active Directory (Azure AD) B2C-vel, azokat egy felügyelt bérlőhöz kell regisztrálni. Ez az oktatóanyag bemutatja, hogyan kell regisztrálni az alkalmazást az Azure portal használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Webalkalmazás regisztrációja
> * Webes API regisztrálása
> * Mobil vagy natív alkalmazás regisztrálása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozott létre a saját [Azure AD B2C-bérlő](tutorial-create-tenant.md), hozzon létre egyet most. Egy meglévő bérlőt is használhat.

## <a name="register-a-web-application"></a>Webalkalmazás regisztrációja

1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.

    ![Váltás előfizetési könyvtár](./media/tutorial-register-applications/switch-directories.png)

2. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
3. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.

    ![Alkalmazás hozzáadása](./media/tutorial-register-applications/add-application.png)

4. Adjon meg egy nevet az alkalmazásnak. Ha például *webapp1*.
5. A **közé tartozik a webalkalmazás vagy webes API** és **implicit engedélyezési folyamat engedélyezése**válassza **Igen**.
6. A **válasz URL-cím**, adja meg a végpont, ahol az Azure AD B2C-t adja vissza az alkalmazás által kért jogkivonatokat. Például beállíthatja, hogy helyileg naslouchání `https://localhost:44316` Ha még nem ismeri a portnak a számát, adjon meg egy helyőrző értéket, és később módosíthatja. Tesztelési célokra, beállíthatja a `https://jwt.ms`, megjeleníti a tartalmát egy ellenőrzési token. Ebben az oktatóanyagban beállíthatja azt a `https://jwt.ms`. 

    A válasz URL-címet a sémával kell kezdődnie `https`, és valamennyi válasz URL-címértékekre egyetlen DNS-tartományba kell megosztani. Például, ha az alkalmazás válasz URL-Címének `https://login.contoso.com`, adhat hozzá, mint például az URL-cím `https://login.contoso.com/new`. Vagy olvassa el, a DNS altartományában `login.contoso.com`, mint például `https://new.login.contoso.com`. Ha azt szeretné, hogy az alkalmazás `login-east.contoso.com` és `login-west.contoso.com` , válasz URL-címek, hozzá kell adnia ezeket a válasz-URL az itt látható sorrendben: `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. Az utóbbi két adhat hozzá, mivel korábban már az első válasz URL-cím, altartományok `contoso.com`.

7. Kattintson a **Create** (Létrehozás) gombra.

    ![Alkalmazás tulajdonságainak megadása](./media/tutorial-register-applications/application-properties.png)

### <a name="create-a-client-secret"></a>Ügyfél titkos kulcs létrehozása

Ha Ön alkalmazást egy jogkivonat-kód cseréje kell, hogy hozzon létre egy.

1. Válassza ki **kulcsok** majd **kulcs generálása**.

    ![Kulcsok létrehozása](./media/tutorial-register-applications/generate-keys.png)

2. Válassza ki **mentése** a kulcs megtekintéséhez. Jegyezze fel az **Alkalmazáskulcs** értékét. Ezt az értéket fogja használni az alkalmazás kódjában alkalmazáskulcsként.

    ![Mentse a kulcsot](./media/tutorial-register-applications/save-key.png)
    
3. Válassza ki **API-hozzáférés**, kattintson a **Hozzáadás**, és válassza ki a webes API-t és a hatóköröket (engedélyeket).

    ![API-hozzáférés konfigurálása](./media/tutorial-register-applications/api-access.png)

## <a name="register-a-web-api"></a>Webes API regisztrálása

1. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
3. Adjon meg egy nevet az alkalmazásnak. Ha például *webapi1*.
4. A **közé tartozik a webalkalmazás vagy webes API** és **implicit engedélyezési folyamat engedélyezése**válassza **Igen**.
5. A **válasz URL-cím**, adja meg a végpont, ahol az Azure AD B2C-t adja vissza az alkalmazás által kért jogkivonatokat. Például beállíthatja, hogy helyileg naslouchání `https://localhost:44316`. Ha még nem ismeri a portnak a számát, adjon meg egy helyőrző értéket, és később módosíthatja.
6. A **Alkalmazásazonosító URI-t**, adja meg a webes API-hoz használt azonosító. A teljes azonosító URI-t, a tartománnyal együtt, a rendszer hozza létre. Például: `https://contosotenant.onmicrosoft.com/api`.
7. Kattintson a **Create** (Létrehozás) gombra.
8. Válassza ki a *webapi1* , és válassza ki a létrehozott alkalmazás **közzétett hatókörök** , szükség szerint több hatókört is felvehet. Alapértelmezés szerint a `user_impersonation` hatókör van meghatározva. A `user_impersonation` hatókör lehetővé teszi más alkalmazások az API elérése a bejelentkezett felhasználó nevében. Ha szeretné, a `user_impersonation` hatókör eltávolítható.

    ![Állítsa be a közzétett hatókörök](./media/tutorial-register-applications/published-scopes.png)


## <a name="register-a-mobile-or-native-application"></a>Mobil vagy natív alkalmazás regisztrálása

1. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
2. Adjon meg egy nevet az alkalmazásnak. Ha például *nativeapp1*.
3. A **közé tartozik a webalkalmazás vagy webes API**válassza **nem**.
4. A **natív ügyfél belefoglalása**válassza **Igen**.
5. A **átirányítási URI-t**, adjon meg egy érvényes átirányítási URI-t egy egyéni sémával. Két szempontot fontos egy átirányítási URI-JÁNAK kiválasztásakor:

    - **Egyedi** -átirányítási URI sémájának minden alkalmazás esetén egyedinek kell lennie. A példában `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` a séma. Ezt a mintát kell követnie. Ha két alkalmazás ugyanazt a sémát használja, a felhasználó a hibajelentési szolgáltatás egy alkalmazás kiválasztásához. Ha a felhasználó helytelenül választ, a bejelentkezés meghiúsul.
    - **Teljes** -átirányítási URI-JÁNAK rendelkeznie kell egy sémát és a egy elérési utat. Az elérési utat a tartomány után legalább egy perjelet tartalmaznia kell. Ha például `//contoso/` működik és `//contoso` sikertelen lesz. Győződjön meg arról, hogy az átirányítási URI-t nem tartalmazza a különleges karaktereket, például aláhúzásjeleket.

6. Kattintson a **Create** (Létrehozás) gombra.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Webalkalmazás regisztrációja
> * Webes API regisztrálása
> * Mobil vagy natív alkalmazás regisztrálása

> [!div class="nextstepaction"]
> [Felhasználói folyamatok létrehozása az Azure Active Directory B2C-vel](tutorial-create-user-flows.md)