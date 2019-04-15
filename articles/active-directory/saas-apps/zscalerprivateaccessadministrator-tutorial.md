---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Zscaler privát hozzáférés rendszergazdája |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Zscaler privát hozzáférés rendszergazdája és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce75431de24886c038cd2eb4ee7db02d2b6cde31
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59563353"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Zscaler privát hozzáférés rendszergazdája

Ebben az oktatóanyagban elsajátíthatja, hogyan Zscaler privát hozzáférés rendszergazdája integrálása az Azure Active Directory (Azure AD).
Zscaler privát hozzáférés rendszergazdája integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Az Azure ad-ben a Zscaler privát hozzáférés rendszergazdai hozzáféréssel rendelkező szabályozhatja.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett Zscaler privát hozzáférés rendszergazdája (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Zscaler privát hozzáférés adminisztrátora, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Zscaler privát hozzáférés rendszergazdai egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Zscaler privát hozzáférés rendszergazdája **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Zscaler privát hozzáférést rendszergazda hozzáadása a katalógusból

Az Azure AD-be Zscaler privát hozzáférést rendszergazda-integráció konfigurálása szüksége Zscaler privát hozzáférést rendszergazda hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Zscaler privát hozzáférést rendszergazda hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Zscaler privát hozzáférés rendszergazdája**, jelölje be **Zscaler privát hozzáférés rendszergazdája** eredmény panelen kattintson a **Hozzáadás** gombra kattintva adhat hozzá a az alkalmazás.

    ![Az eredmények listájában Zscaler privát hozzáférésű rendszergazda](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Zscaler privát hozzáférést rendszergazda nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Zscaler privát hozzáférés rendszergazdája hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés Zscaler privát hozzáférés rendszergazdája tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Zscaler személyes hozzáférési rendszergazdai egyszeri bejelentkezés konfigurálása](#configure-zscaler-private-access-administrator-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Zscaler privát hozzáférés rendszergazdája tesztfelhasználót](#create-zscaler-private-access-administrator-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon a Zscaler privát hozzáférés rendszergazdája, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Zscaler privát hozzáférés rendszergazdája, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Zscaler privát hozzáférés rendszergazdája** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Zscaler privát hozzáférés rendszergazdai tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-relay.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Kattintson a **további URL-címet beállítani**.

    d. Az a **továbbítási állapot** szövegmezőbe írja be egy URL-címe: `idpadminsso`

5.  Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód, hajtsa végre a következő lépést:

    ![Zscaler privát hozzáférés rendszergazdai tartomány és URL-címeket egyetlen bejelentkezési adatait](common/both-signonurl.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-címet. Kapcsolattartó [Zscaler privát hozzáférés rendszergazdai ügyfél-támogatási csapatának](https://help.zscaler.com/zpa-submit-ticket) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

7. Az a **állítsa be Zscaler privát hozzáférés rendszergazdája** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Zscaler privát hozzáférés rendszergazdai egyszeri bejelentkezés konfigurálása

1. Jelentkezzen a Zscaler privát hozzáférés rendszergazdája egy másik böngészőablakban, rendszergazdaként.

2. A képernyő felső részén kattintson **felügyeleti** , és keresse meg **hitelesítési** szakaszban kattintson **identitásszolgáltató konfigurációja**.

    ![Zscaler Private Access Administrator admin](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Kattintson a jobb felső sarokban **adja hozzá az identitásszolgáltató konfigurációja**. 

    ![Zscaler Private Access Administrator addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Az a **adja hozzá az identitásszolgáltató konfigurációja** oldalon tegye a következőket:
 
    ![Zscaler privát hozzáférés rendszergazdája idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Kattintson a **fájl kiválasztása** feltölteni a letöltött metaadat-fájlt az Azure AD-ből a **IdP-metaadatok fájlfeltöltés** mező.

    b. Olvassa be, hogy a **identitásszolgáltató metaadatok** Azure AD-ből és tölti fel a mezők adatai az alább látható módon.

    ![Zscaler Private Access Administrator idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Válassza ki **egyszeri bejelentkezés** , **rendszergazda**.

    d. Válassza ki a tartományt a **tartományok** mező.
    
    e. Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban engedélyezze Britta Simon Zscaler privát hozzáférés adminisztrátora, a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Zscaler privát hozzáférés rendszergazdai**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Zscaler privát hozzáférés rendszergazdája**.

    ![A Zscaler privát hozzáférés rendszergazdája hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Zscaler privát hozzáférés rendszergazdája tesztfelhasználó létrehozása

Az Azure AD-felhasználók Zscaler privát hozzáférés rendszergazdai bejelentkezés engedélyezéséhez, ki kell építeni Zscaler privát hozzáférés rendszergazdája be. Esetén Zscaler privát hozzáférés adminisztrátora, a manuális tevékenység kiépítése.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Zscaler privát hozzáférés rendszergazdája vállalati webhely.

2. A képernyő felső részén kattintson **felügyeleti** , és keresse meg **hitelesítési** szakaszban kattintson **identitásszolgáltató konfigurációja**.

    ![Zscaler Private Access Administrator admin](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Kattintson a **rendszergazdák** a bal oldali menüben.

    ![Zscaler Private Access Administrator administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. Kattintson a jobb felső sarokban **-rendszergazda felvétele**:

    ![Zscaler Private Access Administrator add admin](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Az a **-rendszergazda felvétele** lapon, a következő lépésekkel:

    ![Zscaler privát hozzáférést rendszergazda felhasználó felügyeleti](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Az a **felhasználónév** szövegmezőben adja meg az e-mail címét, például a felhasználó BrittaSimon@contoso.com.

    b. Az a **jelszó** szövegmezőbe írja be a jelszót.

    c. Az a **jelszó megerősítése** szövegmezőbe írja be a jelszót.

    d. Válassza ki **szerepkör** , **Zscaler privát hozzáférés rendszergazdája**.

    e. Az a **E-mail** szövegmezőben adja meg az e-mail címét, például a felhasználó BrittaSimon@contoso.com.

    f. Az a **Phone** szövegmezőbe írja be a telefonszámot.

    g. Az a **időzóna** szövegmezőben válassza ki az időzónát.

    h. Kattintson a **Save** (Mentés) gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Zscaler privát hozzáférés rendszergazdája csempére kattint, akkor kell automatikusan megtörténik a a Zscaler privát hozzáférést rendszergazdától, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

