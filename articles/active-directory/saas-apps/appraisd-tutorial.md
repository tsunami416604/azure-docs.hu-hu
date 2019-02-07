---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Appraisd |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Appraisd között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 44d4988ff68d5ccca575fc29ec3f0b7dce0bf948
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765432"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Appraisd

Ebben az oktatóanyagban elsajátíthatja, hogyan Appraisd integrálása az Azure Active Directory (Azure AD).
Appraisd integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Appraisd Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Appraisd (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Appraisd az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Appraisd egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Appraisd **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-appraisd-from-the-gallery"></a>Appraisd hozzáadása a katalógusból

Az Azure AD integrálása a Appraisd konfigurálásához hozzá kell Appraisd a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Appraisd hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Appraisd**válassza **Appraisd** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Appraisd](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Appraisd nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Appraisd hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Appraisd tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Appraisd egyszeri bejelentkezés konfigurálása](#configure-appraisd-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Appraisd tesztfelhasználót](#create-appraisd-test-user)**  – egy megfelelője a Britta Simon Appraisd, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Appraisd, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Appraisd** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **beállítás egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Appraisd tartomány és URL-címeket egyetlen bejelentkezési adatait](common/both-preintegrated-advanced-urls.png)

    a. Kattintson a **további URL-címet beállítani**.

    b. Az a **továbbítási állapot** szövegmezőbe írja be egy URL-címe: `<TENANTCODE>`

    c. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód, a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > A tényleges bejelentkezési URL- és a továbbítási állapot értéke a Appraisd egyszeri bejelentkezési konfiguráció lapon, az oktatóanyag későbbi részében ismertetett kap.

5. Appraisd alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen, a jogcímek szerkesztése használatával **Szerkesztés ikon** , vagy adja hozzá a jogcímek használatával **hozzáadása új jogcímet**SAML-jogkivonat attribútum beállítása, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Name (Név) |  Adatforrás-attribútum|
    | ---------------| --------------- |
    | nameidentifier | user.mail |
    | | |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

8. Az a **Appraisd beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-appraisd-single-sign-on"></a>Appraisd egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be Appraisd egy biztonsági-rendszergazdaként.

2. A felső, az oldal jobb kattintson a **beállítások** ikonra, majd keresse meg a **konfigurációs**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

3. A menüt a bal oldali menüjében kattintson a **SAML egyszeri bejelentkezés**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

4. Az a **SAML 2.0 egyszeri bejelentkezés konfigurációja** lapon, a következő lépésekkel:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Másolás a **alapértelmezett továbbítási állapot** értékét, és illessze be a **továbbítási állapot** szövegmezőjébe **alapszintű SAML-konfigurációja** az Azure Portalon.

    b. Másolás a **szolgáltatás által kezdeményezett bejelentkezési URL-cím** értékét, és illessze be a **bejelentkezési URL-** szövegmezőjébe **alapszintű SAML-konfigurációja** az Azure Portalon.

5. Görgessen lefelé az oldalon, amelyek ugyanazt a **felhasználók azonosítása**, hajtsa végre az alábbi lépéseket:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Az a **Identity Provider egyszeri bejelentkezési URL-** szövegmezőbe, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalon, majd kattintson a másolt **mentése**.

    b. Az a **Identity Provider kiállítójának URL-címe** szövegmezőbe, illessze be az értéket a **Azure Ad-azonosító**, az Azure Portalon, majd kattintson a másolt **mentése**.

    c. A Jegyzettömbben, nyissa meg az Azure Portalról letöltött base-64 kódolású tanúsítványt, másolja a tartalmat, és illessze be azt a **X.509-tanúsítvány** mezőbe, majd kattintson a **mentése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Appraisd Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Appraisd**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Appraisd**.

    ![Az alkalmazások listáját a Appraisd hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-appraisd-test-user"></a>Appraisd tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD felhasználói bejelentkeznek a Appraisd, akkor ki kell építeni Appraisd be. Appraisd a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be egy biztonsági-rendszergazdaként Appraisd.

2. A felül, jobb a lapon kattintson a **beállítások** ikonra, majd keresse meg a **felügyeleti központ**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Kattintson az eszköztáron az oldal tetején lévő **személyek**, majd keresse meg a **új felhasználó hozzáadása**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Az a **új felhasználó hozzáadása** lapon, a következő lépésekkel:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. A **Utónév** szöveget adja meg például a felhasználó utónevét **Britta**.

    b. A **Vezetéknév** szöveget adja meg például a felhasználó vezetékneve **simon**.

    c. A **E-mail** szöveget adja meg az e-mailt, például a felhasználó **Brittasimon@contoso.com**.

    d. Kattintson a **Felhasználó hozzáadása** parancsra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Appraisd csempére kattint, meg kell lehet automatikusan bejelentkezett a Appraisd, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
