---
title: 'Oktatóanyag: Szűrjön át az Azure Active Directory-integráció |} A Microsoft Docs'
description: Ebben az oktatóanyagban elsajátíthatja fog konfigurálása egyszeri bejelentkezéshez az Azure Active Directory, és Szűrjön át között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 83027e9fbc1826de727f123afe4507c2858c49ff
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560565"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Oktatóanyag: Szűrjön át az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja a Szűrjön át integrálása az Azure Active Directory (Azure AD) lesz.

Ez az integráció ezeket az előnyöket biztosítja:

* Az Azure AD, hogy ki férhet hozzá Szűrjön át is használhatja.
* Engedélyezheti a felhasználók számára (egyszeri bejelentkezés) az Azure AD-fiókjukat Szűrjön át az automatikusan megtörténik a.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Szűrjön át az Azure AD-integráció konfigurálásához szüksége lesz:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókot](https://azure.microsoft.com/free/).
* Szűrjön át-előfizetés, amely egyszeri bejelentkezés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban fogja konfigurálni, és egy tesztelési környezetben az Azure AD egyszeri bejelentkezés tesztelése.

* Szűrjön át a Szolgáltató által kezdeményezett, és az identitásszolgáltató által kezdeményezett egyszeri Bejelentkezést támogatja.

## <a name="add-percolate-from-the-gallery"></a>Szűrjön át hozzáadása a katalógusból

Az Azure AD integrálása a Szűrjön át konfigurálásához hozzá kell Szűrjön át a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** > **minden alkalmazás**:

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Egy alkalmazás hozzáadásához válassza **új alkalmazás** az ablak tetején:

    ![Válassza ki az új alkalmazás](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Szűrjön át**. Válassza ki **Szűrjön át** a keresési eredmények, és válassza ki a **Hozzáadás**.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban fog konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés az Szűrjön át Britta Simon nevű tesztfelhasználó használatával.
Egyszeri bejelentkezés engedélyezéséhez szüksége Szűrjön át az Azure AD-felhasználót és a megfelelő felhasználó közötti kapcsolat létrehozásához.

Az Azure AD egyszeri bejelentkezés az Szűrjön át tesztelése és konfigurálása, szüksége a lépések elvégzéséhez:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  a felhasználók számára a funkció engedélyezéséhez.
2. **[Szűrjön át egyszeri bejelentkezés konfigurálása](#configure-percolate-single-sign-on)**  az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  ahhoz, hogy az Azure AD egyszeri bejelentkezés a felhasználó számára.
5. **[Szűrjön át tesztfelhasználó létrehozása](#create-a-percolate-test-user)**  , amely kapcsolódik a felhasználó Azure ad-ben ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Szűrjön át, ezeket a lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Szűrjön át** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**:

    ![Válassza ki az egyszeri bejelentkezés](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válasszon **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése:

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon válassza ki a **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** párbeszédpanel, nem kell konfigurálnia az alkalmazást az identitásszolgáltató által kezdeményezett módban semmit sem kell. Az alkalmazás már integrálva van az Azure-ral.

    ![Szűrjön a tartomány és URL-címeket egyetlen bejelentkezési adatait át](common/preintegrated.png)

5. Ha az alkalmazás konfigurálása a Szolgáltató által kezdeményezett módban szeretne, válassza ki a **további URL-címet beállítani** és a a **bejelentkezési URL-cím** mezőbe írja be **https://percolate.com/app/login**:

   ![Szűrjön a tartomány és URL-címeket egyetlen bejelentkezési adatait át](common/metadata-upload-additional-signon.png)
6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban jelölje be a **másolási** ikonra kattintva másolja a **alkalmazás összevonási metaadatainak URL-címe** . Mentse az URL-címet.

    ![Másolja az alkalmazás összevonási metaadatainak URL-címe](common/copy-metadataurl.png)

7. Az a **Szűrjön át beállítása** területén másolja a megfelelő URL-címeket, a követelmények alapján.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL-cím**.

    1. **Az Azure AD-azonosító**.

    1. **Kijelentkezési URL-címe**.

### <a name="configure-percolate-single-sign-on"></a>Szűrjön át egyszeri bejelentkezés konfigurálása

1. Egy új böngészőablakban jelentkezzen be Szűrjön át rendszergazdaként.

2. A kezdőlap bal oldalán válassza ki a **beállítások**:
    
    ![Beállítások kiválasztása](./media/percolate-tutorial/configure01.png)

3. A bal oldali panelen válassza ki a **SSO** alatt **szervezet**:

    ![Válassza ki a szervezete egyszeri bejelentkezés](./media/percolate-tutorial/configure02.png)

    1. Az a **bejelentkezési URL-cím** mezőbe illessze be a **bejelentkezési URL-cím** , az Azure Portalról másolt érték.

    1. Az a **Entitásazonosító** mezőbe illessze be a **az Azure AD-azonosító** , az Azure Portalról másolt érték.

    1. A Jegyzettömbben nyissa meg a base-64 kódolású tanúsítványt, az Azure Portalról letöltött. Másolja a tartalmat, és illessze be azt a **x509 tanúsítványok** mezőbe.

    1. Az a **E-mail attribútum** mezőbe írja be **emailaddress**.

    1. A **Identity provider metaadatok URL-címe** mező nem kötelező mező. Ha másolja egy **alkalmazás összevonási metaadatainak URL-címe** az Azure Portalon, akkor is illessze be ezt a jelölőnégyzetet.

    1. Az a **AuthNRequests kell aláírni?** listáról válassza ki **nem**.

    1. Az a **SSO engedélyezése automatikus kiépítés** listáról válassza ki **nem**.

    1. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon Britta Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portalon válassza ki a **Azure Active Directory** a bal oldali panelen válassza ki a **felhasználók**, majd válassza ki **minden felhasználó**:

    ![Válassza ki az összes felhasználó](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején:

    ![Válassza ki az új felhasználó](common/new-user.png)

3. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket.

    ![Felhasználói párbeszédpanel](common/user-properties.png)

    1. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    1. Az a **felhasználónév** mezőbe írja be **BrittaSimon @\<vállalati_tartomány >.\< bővítmény >**. (Például BrittaSimon@contoso.com.)

    1. Válassza ki **jelszó megjelenítése**, és jegyezze fel az értéket, amely szerepel a **jelszó** mezőbe.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon számára a hozzáférés biztosításával Szűrjön át az Azure AD egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**, majd válassza ki **Szűrjön át**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Szűrjön át**.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza ki **felhasználó hozzáadása**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![Felhasználók és csoportok kiválasztása](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza ki **Britta Simon** a felhasználók listában, és kattintson a **válassza** gombra a képernyő alján.

6. Ha a SAML-előfeltétel szerepkör értéket a várt a **Szerepkörválasztás** párbeszédpanelen jelölje ki a megfelelő szerepkört a felhasználóhoz a listából. Kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **hozzárendelése**.

### <a name="create-a-percolate-test-user"></a>Szűrjön át tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be a Szűrjön át az Azure AD-felhasználók, azokat hozzá Szűrjön át kell. Adja hozzá manuálisan kell.

Hozzon létre egy felhasználói fiókot, az alábbi lépéseket kell végrehajtani:

1. Jelentkezzen be rendszergazdaként Szűrjön át

2. A bal oldali panelen válassza ki a **felhasználók** alatt **szervezet**. Válassza ki **új felhasználók**:

    ![Válassza ki az új felhasználók](./media/percolate-tutorial/configure03.png)

3. Az a **felhasználók létrehozása** lapon, az alábbi lépéseket.

    ![Felhasználók lap létrehozása](./media/percolate-tutorial/configure04.png)

    1. Az a **E-mail** mezőbe írja be a felhasználó e-mail-címét. Például: brittasimon@contoso.com.

    1. Az a **teljes fájlvisszaállítási név** mezőbe írja be a felhasználó nevét. Ha például **Brittasimon**.

    1. Válassza ki **felhasználók létrehozása**.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Most szüksége az Azure AD egyszeri bejelentkezési konfigurációjának tesztelése a hozzáférési Panel használatával.

A Szűrjön át csempe kiválasztásakor a hozzáférési panelen azt kell automatikusan megtörténik a Szűrjön át példányhoz, amelyhez beállítani az egyszeri bejelentkezés Konfigurálásához. További információkért lásd: [alkalmazások használatának és elérésének a saját alkalmazások portál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory számára oktatóanyagokkal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)