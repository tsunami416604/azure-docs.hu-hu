---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Spotinst |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Spotinst között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5b3c9a0152144ac0d705c7d3f12c18cbc38398f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090153"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Spotinst

Ebben az oktatóanyagban elsajátíthatja, hogyan Spotinst integrálása az Azure Active Directory (Azure AD).
Spotinst integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Spotinst Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Spotinst (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Spotinst az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Spotinst egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Spotinst **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-spotinst-from-the-gallery"></a>Spotinst hozzáadása a katalógusból

Az Azure AD integrálása a Spotinst konfigurálásához hozzá kell Spotinst a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Spotinst hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Spotinst**válassza **Spotinst** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Spotinst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Spotinst nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Spotinst hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Spotinst tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Spotinst egyszeri bejelentkezés konfigurálása](#configure-spotinst-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Spotinst tesztfelhasználót](#create-spotinst-test-user)**  – egy megfelelője a Britta Simon Spotinst, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Spotinst, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Spotinst** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Spotinst tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-preintegrated-relay.png)

    a. Ellenőrizze **további URL-címet beállítani**.

    b. Az a **továbbítási állapot** szövegmezőbe írja be egy értéket: `<ID>`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Spotinst tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím:  `https://console.spotinst.com`

    > [!NOTE]
    > A továbbítási állapot értéke nem valódi. Továbbítási állapot értéke frissíti a tényleges továbbítási állapot érték, amelynek az ismertetése az oktatóanyag későbbi részében.

6. Spotinst alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

7. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen, a jogcímek szerkesztése használatával **Szerkesztés ikon** , vagy adja hozzá a jogcímek használatával **hozzáadása új jogcímet**SAML-jogkivonat attribútum beállítása, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket: 

    | Name (Név) | Adatforrás-attribútum|
    | -----| --------------- |
    | E-mail | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

8. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

9. Az a **Spotinst beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-spotinst-single-sign-on"></a>Spotinst egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be Spotinst egy biztonsági-rendszergazdaként.

2. Kattintson a **felhasználói ikon** meg a képernyőn, majd kattintson a jobb felső **beállítások**.

    ![Spotinst beállításai](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Kattintson a **biztonsági** felső lapot, majd **Identitásszolgáltatók** , és hajtsa végre az alábbi lépéseket:

    ![Spotinst biztonsági](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Másolás a **továbbítási állapot** a példány értékét, és illessze be a **továbbítási állapot** szövegmezőjébe **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    b. Kattintson a **Tallózás** Azure Portalról letöltött metaadatainak xml-fájl feltöltése

    c. Kattintson a **SAVE** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Spotinst Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Spotinst**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Spotinst**.

    ![Az alkalmazások listáját a Spotinst hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-spotinst-test-user"></a>Spotinst tesztfelhasználó létrehozása

Ez a szakasz célja Spotinst Britta Simon nevű felhasználó létrehozásához.

1. Ha konfigurálta az alkalmazás a **SP** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

   a. Egy másik böngészőablakban jelentkezzen be Spotinst egy biztonsági-rendszergazdaként.

   b. Kattintson a **felhasználói ikon** meg a képernyőn, majd kattintson a jobb felső **beállítások**.

    ![Spotinst beállításai](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Kattintson a **felhasználók** válassza **felhasználó hozzáadása**.

    ![Spotinst beállításai](./media/spotinst-tutorial/adduser1.png)

    d. A felhasználó hozzáadása szakaszról hajtsa végre a következő lépéseket:

    ![Spotinst beállításai](./media/spotinst-tutorial/adduser2.png)

    * Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be például a felhasználó teljes nevét **BrittaSimon**.

    * Az a **E-mail** szövegmezőbe írja be például a felhasználó e-mail-címe `brittasimon\@contoso.com`.

    * Válassza ki a szervezet konkrét részletei a **szervezeti szerepkör, a fiók szerepkör és a fiókok**.

2. Ha konfigurálta az alkalmazás a **Identitásszolgáltató** ott a kezdeményezett módban, nincs teendő ebben a szakaszban az Ön számára. Spotinst támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Új felhasználó jön létre az Spotinst elérésére, ha még nem létezik tett kísérlet során.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Spotinst csempére kattint, meg kell lehet automatikusan bejelentkezett a Spotinst, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

