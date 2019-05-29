---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Displayr |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Displayr között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93a1ad1f9fbc01cd06b3aaffc8a718634e8454d6
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357034"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Oktatóanyag: Displayr integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a Displayr integrálása az Azure Active Directory (Azure AD) lesz. Displayr integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá Displayr Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve Displayr az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Displayr egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a Displayr **SP** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-displayr-from-the-gallery"></a>Displayr hozzáadása a katalógusból

Az Azure AD integrálása a Displayr konfigurálásához hozzá kell Displayr a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Displayr** kifejezést a keresőmezőbe.
1. Válassza ki **Displayr** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata Displayr **Britta Simon**. Az SSO működjön kell Displayr az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Az Azure AD SSO Displayr tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja a Displayr](#configure-displayr)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Displayr tesztfelhasználót](#create-displayr-test-user)**  van egy megfelelője a Britta Simon Displayr, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Displayr** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **beállítás egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre a következő lépést:

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<YOURDOMAIN>.displayr.com`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím:`<YOURDOMAIN>.displayr.com`

    >[!NOTE]
    >Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [Displayr ügyfél-támogatási csapatának](mailto:support@displayr.com) beolvasni ezeket az értékeket. Az alapszintű SAML-konfigurációja részben az Azure Portalon található mintákat is vonatkoznak.

1. A a **beállítás egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **tanúsítvány (Base64)** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. Displayr alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva nyissa meg a felhasználói attribútumok párbeszédpanel.

    ![image](common/edit-attribute.png)

1. Emellett a fentiekben Displayr alkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. Az a **felhasználói attribútumok & jogcímek** szakaszában a **csoportjogcímek (előzetes verzió)** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    a. Kattintson a **toll** melletti **csoportok visszaküldött jogcímek**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Válassza ki **összes csoport** választógomb listájából.

    c. Válassza ki **forrásattribútumának Attribútumhierarchiája** , **csoportazonosító**.

    d. Ellenőrizze **testreszabása a csoportos jogcímet neve**.

    e. Ellenőrizze **gridre bocsáthatja ki csoportokat szerepkör jogcímekként**.

    f. Kattintson a **Save** (Mentés) gombra.

1. Az a **beállítás Displayr** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Displayr konfigurálása

1. Automatizálhatja a Displayr konfigurációra, telepítenie kell **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kattintva **a bővítmény telepítése**.

    ![Saját alkalmazások kiterjesztése](common/install-myappssecure-extension.png)

2. A felvett bővítmény a böngészőre, kattintson a **telepítő Displayr** fog irányítja át a Displayr alkalmazás. Itt adja meg a rendszergazdai hitelesítő adataival bejelentkezni Displayr. A webböngésző-bővítmény automatikusan konfigurálja az alkalmazást, és 3 – 6. lépések automatizálásához.

    ![Konfiguráció beállítása](common/setup-sso.png)

3. Ha azt szeretné, Displayr manuális beállítása, nyisson meg egy új böngészőablakban, és jelentkezzen be rendszergazdaként vállalati Displayr webhelyét, és hajtsa végre az alábbi lépéseket:

4. Kattintson a **beállítások** majd keresse meg a **fiók**.

    ![Konfiguráció](./media/displayr-tutorial/config01.png)

5. Váltson **beállítások** a felső menüben, és görgessen lefelé az oldalon kattintson a **konfigurálása egyszeri bejelentkezéshez a (SAML)** .

    ![Konfiguráció](./media/displayr-tutorial/config02.png)

6. Az a **egyszeri bejelentkezéshez a SAML** lapon, a következő lépésekkel:

    ![Konfiguráció](./media/displayr-tutorial/config03.png)

    a. Ellenőrizze a **engedélyezése egyszeri bejelentkezés a SAML** mezőbe.

    b. Másolja a tényleges **azonosító** értéket a **alapszintű SAML-konfigurációja** szakaszban az Azure AD, és illessze be azt a **kibocsátó** szövegmezőben.

    c. Az a **bejelentkezési URL-cím** szöveg mezőbe illessze be az értékét **bejelentkezési URL-cím**, az Azure Portalról másolt.

    d. Az a **kijelentkezési URL-címe** szöveg mezőbe illessze be az értékét **kijelentkezési URL-címe**, az Azure Portalról másolt.

    e. A Jegyzettömb alkalmazásban nyissa meg a tanúsítvány (Base64), másolja a tartalmat, és illessze be azt a **tanúsítvány** szövegmezőben.

    f. **Csoport-hozzárendelések** megadása nem kötelező.

    g. Kattintson a **Save** (Mentés) gombra.  

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon Britta Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `BrittaSimon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon által biztosított hozzáférés Displayr Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Displayr**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-displayr-test-user"></a>Displayr tesztfelhasználó létrehozása

Engedélyezi az Azure AD-felhasználók, jelentkezzen be a Displayr, hogy azok ki kell építeni Displayr be. Displayr a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként Displayr.

2. Kattintson a **beállítások** majd keresse meg a **fiók**.

    ![Displayr konfiguráció](./media/displayr-tutorial/config01.png)

3. Váltson **beállítások** a felső menüben, és görgessen lefelé az oldalon, amíg **felhasználók** területen, majd kattintson a **új felhasználó**.

    ![Displayr konfiguráció](./media/displayr-tutorial/config07.png)

4. Az a **új felhasználó** lapon, a következő lépésekkel:

    ![Displayr konfiguráció](./media/displayr-tutorial/config06.png)

    a. A **neve** szöveget adja meg például a felhasználó nevét **Brittasimon**.

    b. A **E-mail** szöveget adja meg az e-mailt, például a felhasználó `Brittasimon@contoso.com`.

    c. Válassza ki a megfelelő **csoporttagság**.

    d. Kattintson a **Save** (Mentés) gombra.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A Displayr csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a Displayr, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
