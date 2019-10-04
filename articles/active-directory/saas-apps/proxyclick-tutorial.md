---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Proxyclick |} A Microsoft Docs'
description: Ebben az oktatóanyagban elsajátíthatja fog konfigurálása egyszeri bejelentkezéshez Proxyclick és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 6a4d1c8a390ebd1194d14c057bb32d3111bf39be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093490"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Proxyclick

Ebben az oktatóanyagban elsajátíthatja a Proxyclick integrálása az Azure Active Directory (Azure AD) lesz.
Ez az integráció ezeket az előnyöket biztosítja:

* Az Azure AD, hogy ki férhet hozzá Proxyclick is használhatja.
* Engedélyezheti a felhasználók számára, hogy automatikusan jelentkezzenek be Proxyclick (egyszeri bejelentkezés), az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Proxyclick, szüksége lesz:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, akkor regisztráljon egy [egy havi próbalehetőség](https://azure.microsoft.com/pricing/free-trial/).
* Proxyclick előfizetés, amely egyszeri bejelentkezés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban fogja konfigurálni, és egy tesztelési környezetben az Azure AD egyszeri bejelentkezés tesztelése.

* Proxyclick supports SP-initiated and IdP-initiated SSO.

## <a name="add-proxyclick-from-the-gallery"></a>Proxyclick hozzáadása a katalógusból

Proxyclick integrálása az Azure AD beállításához, hozzá kell Proxyclick a galériából a felügyelt SaaS-alkalmazások listájára.

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** > **minden alkalmazás**:

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Egy alkalmazás hozzáadásához válassza **új alkalmazás** az ablak tetején:

    ![Válassza ki az új alkalmazás](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Proxyclick**. Válassza ki **Proxyclick** a keresési eredmények, és válassza ki a **Hozzáadás**.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban fog konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés az Proxyclick Britta Simon nevű tesztfelhasználó használatával.
Egyszeri bejelentkezés engedélyezéséhez szüksége Proxyclick az Azure AD-felhasználót és a megfelelő felhasználó közötti kapcsolat létrehozásához.

Az Azure AD egyszeri bejelentkezés az Proxyclick tesztelése és konfigurálása, szüksége a lépések elvégzéséhez:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  a felhasználók számára a funkció engedélyezéséhez.
2. **[Proxyclick egyszeri bejelentkezés konfigurálása](#configure-proxyclick-single-sign-on)**  az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  ahhoz, hogy az Azure AD egyszeri bejelentkezés a felhasználó számára.
5. **[Hozzon létre egy Proxyclick tesztfelhasználót](#create-a-proxyclick-test-user)**  , amely kapcsolódik a felhasználó Azure ad-ben ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Proxyclick, ezeket a lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az alkalmazás integrációs Proxyclick oldalán válassza **egyszeri bejelentkezési**:

    ![Válassza ki az egyszeri bejelentkezés](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válasszon **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése:

    ![Egyszeri bejelentkezés módszer kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon válassza ki a **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** párbeszédpanel, ha azt szeretné, konfigurálja az alkalmazás identitásszolgáltató által kezdeményezett módban, az alábbi lépéseket.

    ![Alapszintű SAML-konfigurációja párbeszédpanel](common/idp-intiated.png)

    1. Az a **azonosító** mezőbe írjon be egy URL-címe ebben a mintában:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. Az a **válasz URL-cím** mezőbe írjon be egy URL-címe ebben a mintában:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Ha az alkalmazás konfigurálása a Szolgáltató által kezdeményezett módban szeretne, válassza ki a **további URL-címet beállítani**. Az a **bejelentkezési URL-cím** mezőbe írjon be egy URL-címe ebben a mintában:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Proxyclick tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Ezeket az értékeket a helyőrzők. Tényleges azonosítóját használja, válasz URL-cím és a sign-on URL-címet kell. Ez az oktatóanyag egy későbbi része ismerteti ezeket az értékeket szükséges lépések.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban jelölje be a **letöltése** mellett kapcsolni **tanúsítvány (Base64)** , a igényeknek, és mentse el a tanúsítványt a számítógépen:

    ![Tanúsítvány letöltésére szolgáló hivatkozásra.](common/certificatebase64.png)

7. Az a **Proxyclick beállítása** területén másolja a megfelelő URL-címeket, a követelmények alapján:

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL-cím**.

    1. **Az Azure AD-azonosító**.

    1. **Kijelentkezési URL-címe**.

### <a name="configure-proxyclick-single-sign-on"></a>Proxyclick egyszeri bejelentkezés konfigurálása

1. Egy új böngészőablakban jelentkezzen be a Proxyclick vállalati hely rendszergazdaként.

2. Válassza ki **fiók & beállítások**:

    ![Válassza ki a fiók & beállításai](./media/proxyclick-tutorial/configure1.png)

3. Görgessen le a **Integrációk** szakaszt, és válassza **SAML**:

    ![Válassza ki az SAML](./media/proxyclick-tutorial/configure2.png)

4. Az a **SAML** területén az alábbi lépéseket.

    ![SAML-szakaszában](./media/proxyclick-tutorial/configure3.png)

    1. Másolás a **SAML ügyfél URL-címe** értékét, és illessze be azt a **válasz URL-cím** párbeszédpanel a **alapszintű SAML-konfigurációja** párbeszédpanel az Azure Portalon.

    1. Másolás a **SAML egyszeri bejelentkezés átirányítási URL-cím** értékét, és illessze be azt a **bejelentkezési URL-cím** és **azonosító** lévő a **alapszintű SAML-konfigurációja** az Azure Portal párbeszédpanel.

    1. Az a **SAML-kérelem metódus** listáról válassza ki **HTTP-átirányítás**.

    1. Az a **kibocsátó** mezőbe illessze be a **az Azure AD-azonosító** , az Azure Portalról másolt érték.

    1. Az a **SAML 2.0-s végponti URL-cím** mezőbe illessze be a **bejelentkezési URL-cím** , az Azure Portalról másolt érték.

    1. A Jegyzettömbben nyissa meg a tanúsítványfájlt, amely az Azure Portalról letöltött. Illessze be a fájl tartalmát a **tanúsítvány** mezőbe.

    1. Válassza ki **módosítások mentése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon Britta Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portalon válassza ki a **Azure Active Directory** a bal oldali panelen válassza ki a **felhasználók**, majd válassza ki **minden felhasználó**:

    ![Válassza ki az összes felhasználó](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején:

    ![Válassza ki az új felhasználó](common/new-user.png)

3. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket.

    ![Felhasználói párbeszédpanel](common/user-properties.png)

    1. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    1. Az a **felhasználónév** mezőbe írja be **BrittaSimon @\<vállalati_tartomány >.\< bővítmény >** . (Például BrittaSimon@contoso.com.)

    1. Válassza ki **jelszó megjelenítése**, és jegyezze fel az értéket, amely szerepel a **jelszó** mezőbe.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon számára a hozzáférés biztosításával a Proxyclick Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**, majd válassza ki **Proxyclick**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Proxyclick**.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza ki **felhasználó hozzáadása**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. A a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listában, és kattintson a **válassza** gombra az ablak alján.

6. Ha a SAML-előfeltétel szerepkör értéket a várt a **Szerepkörválasztás** párbeszédpanelen jelölje ki a megfelelő szerepkört a felhasználóhoz a listából. Kattintson a **kiválasztása** gombra az ablak alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **hozzárendelése**.

### <a name="create-a-proxyclick-test-user"></a>Proxyclick tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Proxyclick bejelentkezni, azokat hozzá Proxyclick kell. Adja hozzá manuálisan kell.

Hozzon létre egy felhasználói fiókot, az alábbi lépéseket kell végrehajtani:

1. A Proxyclick vállalati hely jelentkezzen be rendszergazdaként.

1. Válassza ki **munkatársai** az ablak tetején:

    ![Válassza ki a munkatársakkal](./media/proxyclick-tutorial/user1.png)

1. Válassza ki **munkatársa hozzáadása**:

    ![Válassza a munkatársak hozzáadása](./media/proxyclick-tutorial/user2.png)

1. Az a **adjon hozzá egy munkatársa** területén az alábbi lépéseket.

    ![Adjon hozzá egy munkatársa szakasz](./media/proxyclick-tutorial/user3.png)

    1. Az a **E-mail** mezőbe írja be a felhasználó e-mail-címét. Ebben az esetben **brittasimon\@contoso.com**.

    1. Az a **Utónév** mezőbe írja be a felhasználó utónevét. Ebben az esetben **Britta**.

    1. Az a **Vezetéknév** mezőbe írja be a felhasználó vezetékneve. Ebben az esetben **Simon**.

    1. Válassza ki **felhasználó hozzáadása**.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Most szüksége az Azure AD egyszeri bejelentkezési konfigurációjának tesztelése a hozzáférési Panel használatával.

A Proxyclick csempe kiválasztásakor a hozzáférési panelen azt kell automatikusan megtörténik a a Proxyclick példányhoz, amelyhez beállítani az egyszeri bejelentkezés Konfigurálásához. A hozzáférési panelen kapcsolatos további információkért lásd: [alkalmazások használatának és elérésének a saját alkalmazások portál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory számára oktatóanyagokkal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

