---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező dmarcian |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és dmarcian között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce82775b65316fc646b45b4749a6b1719ed6e59f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443147"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező dmarcian

Ebben az oktatóanyagban elsajátíthatja, hogyan dmarcian integrálása az Azure Active Directory (Azure AD).
Dmarcian integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá dmarcian Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve dmarcian (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Dmarcian az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* dmarcian egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* támogatja a dmarcian **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-dmarcian-from-the-gallery"></a>Dmarcian hozzáadása a katalógusból

Az Azure AD integrálása a dmarcian konfigurálásához hozzá kell dmarcian a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Dmarcian hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **dmarcian**válassza **dmarcian** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![az eredmények listájában dmarcian](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az dmarcian nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó dmarcian hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az dmarcian tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Egyszeri bejelentkezés dmarcian konfigurálása](#configure-dmarcian-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre dmarcian tesztfelhasználót](#create-dmarcian-test-user)**  – egy megfelelője a Britta Simon a felhasználó Azure ad-ben reprezentációja kapcsolódó dmarcian rendelkeznie.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés dmarcian, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **dmarcian** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![dmarcian tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím:
    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![dmarcian tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. A tényleges azonosítója, válasz URL-cím és bejelentkezési URL-címet az oktatóanyag későbbi részében ismertetett frissíteni ezeket az értékeket. 

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** és mentse a számítógép.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

### <a name="configure-dmarcian-single-sign-on"></a>Dmarcian egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be dmarcian egy biztonsági-rendszergazdaként.

2. Kattintson a **profil** a jobb felső sarokban, és keresse meg a **beállítások**.

    ![A beállítások](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

3. Görgessen lefelé, és kattintson a **egyszeri bejelentkezés** területen, majd kattintson a **konfigurálása**.

    ![Az egyetlen](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

4. Az a **SAML egyszeri bejelentkezés** lapon állítsa be a **állapot** , **engedélyezve** , és hajtsa végre az alábbi lépéseket:

    ![A hitelesítés](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * A **dmarcian ad hozzá az identitásszolgáltató** területén kattintson **MÁSOLÁSI** másolása a **helyességi feltétel fogyasztói URL-címe** a példány, és illessze be a  **Válasz URL-cím** szövegmezőjébe **alapszintű SAML-konfigurációja szakasz** az Azure Portalon.

    * Alatt **dmarcian ad hozzá az identitásszolgáltató** területén kattintson **MÁSOLÁSI** másolása a **Entitásazonosító** a példány, és illessze be a **azonosító**szövegmezőjébe **alapszintű SAML-konfigurációja szakasz** az Azure Portalon.

    * Alatt **hitelesítés beállítása** ebben a szakaszban a **Identity Provider metaadatok** szövegmező illessze be a **alkalmazás összevonási metaadatainak URL-címe**, az Azure Portalról másolt.

    * Alatt **hitelesítés beállítása** ebben a szakaszban a **attribútum utasítások** szövegmező illessze be az URL-cím `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * Alatt **állítsa be a bejelentkezési URL-cím** területén másolja a **bejelentkezési URL-cím** a példány, és illessze be a **bejelentkezési URL-** szövegmezőjébe **alapszintűSAML-konfigurációjaszakasz** az Azure Portalon.

        > [!Note]
        > Módosíthatja a **bejelentkezési URL-cím** a szervezet szerint.

    * Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés dmarcian való hozzáférést.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **dmarcian**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **dmarcian**.

    ![Az alkalmazások listáját a dmarcian hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-dmarcian-test-user"></a>Dmarcian tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók dmarcian bejelentkezni, akkor ki kell építeni dmarcian be. Dmarcian a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be dmarcian egy biztonsági-rendszergazdaként.

2. Kattintson a **profil** a felső jobb sarokban található, és keresse meg **felhasználók kezelése**.

    ![A felhasználó](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. A jobb oldalán található **SSO felhasználók** területén kattintson a **új felhasználó hozzáadása**.

    ![A felhasználó hozzáadása](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Az a **új felhasználó hozzáadása** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![Az új felhasználó](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Az a **új felhasználó e-mail-címe** szövegmezőben adja meg az e-mail címét, például a felhasználó **brittasimon@contoso.com**.

    b. Ha azt szeretné, hogy a felhasználó rendszergazdai jogokat, válassza ki a **győződjön meg arról, felhasználói rendszergazda**.

    c. Kattintson a **felhasználó hozzáadása**.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a dmarcian csempére kattint, meg kell lehet automatikusan bejelentkezett a dmarcian, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

