---
title: 'Oktatóanyag: Az Azure Active Directory-integráció, a Bitbucket Kantega SSO-val |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Bitbucket Kantega SSO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 4f0b6dc725fbce2140ede98e7924730c4426e084
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727507"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Oktatóanyag: Az Azure Active Directory-integráció, a Bitbucket Kantega SSO-val

Ebben az oktatóanyagban elsajátíthatja a Bitbucket Kantega SSO integrálása az Azure Active Directory (Azure AD).
A Bitbucket Kantega SSO integrálása az Azure AD nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Kantega SSO a bitbucket-alapú Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Kantega egyszeri Bejelentkezést, a Bitbucket (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Bitbucket Kantega SSO-val, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* A bitbucket-alapú egyszeri bejelentkezés Kantega SSO engedélyezése előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Bitbucket Kantega SSO **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>A Bitbucket Kantega SSO hozzáadása a katalógusból

Adja meg a Bitbucket Kantega SSO integrálása az Azure AD, szüksége Kantega egyszeri Bejelentkezést, a Bitbucket hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**A Bitbucket Kantega SSO hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Kantega egyszeri Bejelentkezést, a Bitbucket**, jelölje be **Kantega egyszeri Bejelentkezést, a Bitbucket** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Kantega egyszeri Bejelentkezést, a Bitbucket, a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés Kantega SSO-val Bitbucket alapján nevű tesztfelhasználó **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Kantega egyszeri Bejelentkezést, a Bitbucket hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a Bitbucket Kantega SSO-val tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Kantega egyszeri bejelentkezést, a bitbucket-alapú egyszeri bejelentkezés](#configure-kantega-sso-for-bitbucket-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Kantega egyszeri bejelentkezés létrehozása a Bitbucket tesztfelhasználó](#create-kantega-sso-for-bitbucket-test-user)**  – van egy megfelelője a Britta Simon Kantega egyszeri Bejelentkezést, a Bitbucket, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálásához a Bitbucket Kantega SSO-val, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Kantega egyszeri Bejelentkezést, a Bitbucket** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Kantega egyszeri Bejelentkezést, a bitbucket-alapú tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Kantega egyszeri Bejelentkezést, a bitbucket-alapú tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Ezeket az értékeket az oktatóanyag későbbi részében ismertetett bitbucket-alapú beépülő modul konfigurálása során érkeznek.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

7. Az a **be Kantega egyszeri Bejelentkezést, a Bitbucket** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-kantega-sso-for-bitbucket-single-sign-on"></a>Kantega SSO bitbucket-alapú egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a bitbucket-alapú felügyeleti portálra rendszergazdaként.

1. Kattintson a fogaskerék alakú ikonjára, majd kattintson a **új bővítmények keresése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. Keresés **Kantega egyszeri bejelentkezési Bitbucket SAML & Kerberos** kattintson **telepítése** gombra az új SAML beépülő modul telepítéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. A beépülő modul telepítése elindul.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. A telepítés befejezése után. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon33.png)

1. Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon34.png)

1. Kattintson a **konfigurálása** a új beépülő modul konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. Az a **SAML** szakaszban. Válassza ki **Azure Active Directory (Azure AD)** származó a **Hozzáadás identitásszolgáltató** legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Válassza ki az előfizetés szintjén, **alapszintű**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. Az a **alkalmazás tulajdonságai** szakaszban, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Másolás a **Alkalmazásazonosító URI-t** értékét, és használja azt **azonosítóját, a válasz URL-cím és a bejelentkezési URL-** a a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    b. Kattintson a **tovább**.

1. Az a **metaadatok importálása** szakaszban, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Válassza ki **metaadatait tartalmazó fájl a számítógépen**, és az Azure Portalról letöltött feltöltési metaadatait tartalmazó fájl.

    b. Kattintson a **tovább**.

1. Az a **nevét és az egyszeri bejelentkezés helyét** szakaszban, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Adja hozzá az identitásszolgáltató nevét **identitásszolgáltató neve** (például: az Azure AD) szövegmezőbe.

    b. Kattintson a **tovább**.

1. Ellenőrizze az aláíró tanúsítványt, és kattintson a **tovább**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. Az a **bitbucket-alapú felhasználói fiókok** szakaszban, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. Válassza ki **felhasználók létrehozása a Bitbucket a belső könyvtár szükség esetén** , és adja meg a felhasználók számára a megfelelő nevet a csoport (lehet több nem. a vesszővel elválasztott csoportok).

    b. Kattintson a **tovább**.

1. Kattintson a **Befejezés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. Az a **ismert tartományok az Azure ad** szakaszban, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. Válassza ki **tartományok ismert** az oldal bal oldali panelen.

    b. Adja meg a tartomány nevét a **tartományok ismert** szövegmezőbe.

    c. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus `brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Kantega egyszeri Bejelentkezést, a bitbucket-alapú Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Kantega egyszeri Bejelentkezést, a Bitbucket**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Kantega egyszeri Bejelentkezést, a Bitbucket**.

    ![A Kantega egyszeri bejelentkezés az alkalmazások listáját a Bitbucket-hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-kantega-sso-for-bitbucket-test-user"></a>Kantega egyszeri bejelentkezés létrehozása a Bitbucket tesztfelhasználó számára

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a Bitbucket, akkor ki kell építeni, Bitbucket. Bitbucket Kantega egyszeri Bejelentkezést, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a bitbucket-alapú vállalati webhely.

1. Kattintson a beállítások ikonra.

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. A **felügyeleti** szakasz lapra, majd **felhasználók**.

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user2.png)

1. Kattintson a **felhasználó létrehozása**.

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. Az a **Create User** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. Az a **felhasználónév** szövegmezőbe írja be az e-mailt, felhasználó, például Brittasimon@contoso.com.

    b. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be a felhasználó például Britta Simon teljes neve.

    c. Az a **E-mail-cím** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.

    d. Az a **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    e. Az a **jelszó megerősítése** szövegmezőbe írja be újból a felhasználó jelszavát.

    f. Kattintson a **felhasználó létrehozása**.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint a Kantega SSO Bitbucket csempe a hozzáférési panelen, meg kell lehet automatikusan bejelentkezett a Kantega egyszeri bejelentkezés, a Bitbucket, amelynek beállítása egyszeri bejelentkezés az. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

