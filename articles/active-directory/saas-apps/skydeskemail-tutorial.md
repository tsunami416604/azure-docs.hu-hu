---
title: 'Oktatóanyag: Az Azure Active Directory-integráció SkyDesk e-mail-címével |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés SkyDesk e-mailek és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: e0c2dc6c370e697f896e24e7d56c6eb8900601a9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271050"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Oktatóanyag: Az Azure Active Directory-integráció SkyDesk e-mail-címmel

Ebben az oktatóanyagban elsajátíthatja, hogyan SkyDesk E-mail integrálása az Azure Active Directory (Azure AD).
SkyDesk E-mail integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá SkyDesk e-mailt az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett SkyDesk e-mailek (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása SkyDesk e-mail-címmel, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* SkyDesk e-mailt az egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a SkyDesk E-mail **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-skydesk-email-from-the-gallery"></a>SkyDesk E-mail hozzáadása a katalógusból

Konfigurálása az Azure AD integrálása a SkyDesk e-mailt, hozzá kell SkyDesk e-mailt a galériából a felügyelt SaaS-alkalmazások listájára.

**SkyDesk e-mail hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **SkyDesk E-mail**, jelölje be **SkyDesk E-mail** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában SkyDesk e-mailben](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálni és SkyDesk e-mailt az Azure AD egyszeri bejelentkezés tesztelése alapján nevű tesztfelhasználó **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó SkyDesk e-mailben szereplő hivatkozásra kapcsolatát kell hozható létre.

Tesztelje az Azure AD egyszeri bejelentkezés SkyDesk e-mail-címmel konfigurálni, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[E-mailek SkyDesk egyszeri bejelentkezés konfigurálása](#configure-skydesk-email-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre SkyDesk E-mail tesztfelhasználót](#create-skydesk-email-test-user)**  – egy megfelelője a Britta Simon SkyDesk e-mailt, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása SkyDesk e-mail-címével, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **SkyDesk E-mail** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SkyDesk E-mail tartománya és URL-címek egyszeri bejelentkezési adatait](common/sp-signonurl.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [SkyDesk E-mail ügyfél-támogatási csapatának](https://www.skydesk.jp/apps/support/) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **SkyDesk E-mail beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-skydesk-email-single-sign-on"></a>SkyDesk E-mail egyszeri bejelentkezés konfigurálása

1. Egy másik webböngészőben bejelentkezést SkyDesk E-mail fiókja rendszergazdaként.

1. A felső menüben kattintson **telepítő**, és válassza ki **szervezeti**.

    ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Kattintson a **tartományok** a bal oldali panelen.

    ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Kattintson a **tartomány hozzáadása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Adja meg a tartomány nevét, és ellenőrizze a tartományt.

    ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Kattintson a **SAML-hitelesítés** a bal oldali panelen.

    ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Az a **SAML-hitelesítés** párbeszédpanel lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > SAML-alapú hitelesítés használatára, vagy rendelkeznie kell **ellenőrzött tartomány** vagy **portál URL-cím** beállítása. Beállíthatja, hogy a portál URL-CÍMÉT az egyedi névvel.

    ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

    b. Az a **kijelentkezési** URL-címe szövegmezőbe, illessze be az értéket a **kijelentkezési URL-címe**, az Azure Portalról másolt.

    c. **Jelszó URL-Címének módosítása** nem kötelező, ezért hagyja üresen a mezőt.

    d. Kattintson a **kulcs beolvasása fájlból** válassza ki a letöltött tanúsítvány az Azure Portalról, és kattintson a **nyílt** feltölteni a tanúsítványt.

    e. Mint **algoritmus**válassza **RSA**.

    f. Kattintson a **Ok** menti a módosításokat.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon SkyDesk levelezéshez való hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **SkyDesk E-mail**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **SkyDesk E-mail**.

    ![Az alkalmazások listáját a SkyDesk E-mail hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-skydesk-email-test-user"></a>SkyDesk E-mail tesztfelhasználó létrehozása

Ebben a szakaszban egy e-mailben SkyDesk Britta Simon nevű felhasználó létrehozásához.

Kattintson a **felhasználói hozzáférés** bal oldali panelen SkyDesk e-mailben, és írja be a felhasználónevét.

![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Ha a felhasználók tömeges létrehozásához szüksége, lépjen kapcsolatba kell a [SkyDesk E-mail ügyfél-támogatási csapatának](https://www.skydesk.jp/apps/support/).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SkyDesk E-mail csempére kattint, akkor kell automatikusan megtörténik a SkyDesk e-mail-, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

