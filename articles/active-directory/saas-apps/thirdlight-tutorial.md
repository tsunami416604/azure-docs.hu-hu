---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ThirdLight |} A Microsoft Docs'
description: Ebben az oktatóanyagban elsajátíthatja fog konfigurálása egyszeri bejelentkezéshez ThirdLight és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 67c8dcfffd78d4c0114a96622235d6548627fa92
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236962"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ThirdLight

Ebben az oktatóanyagban elsajátíthatja a ThirdLight integrálása az Azure Active Directory (Azure AD) lesz. Ez az integráció ezeket az előnyöket biztosítja:

* Az Azure AD, hogy ki férhet hozzá ThirdLight is használhatja.
* Engedélyezheti a felhasználók számára, hogy automatikusan jelentkezzenek be ThirdLight (egyszeri bejelentkezés), az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

Ha szeretne további SaaS alkalmazás integrálása az Azure ad-vel, tekintse meg [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása ThirdLight, szüksége lesz:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókot](https://azure.microsoft.com/free/).
* ThirdLight előfizetés, amely egyszeri bejelentkezés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban fogja konfigurálni, és egy tesztelési környezetben az Azure AD egyszeri bejelentkezés tesztelése.

* ThirdLight SP által kezdeményezett egyszeri Bejelentkezést támogatja.

## <a name="add-thirdlight-from-the-gallery"></a>ThirdLight hozzáadása a katalógusból

ThirdLight integrálása az Azure AD beállításához, hozzá kell ThirdLight a galériából a felügyelt SaaS-alkalmazások listájára.

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** > **minden alkalmazás**:

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Egy alkalmazás hozzáadásához válassza **új alkalmazás** az ablak tetején:

    ![Válassza ki az új alkalmazás](common/add-new-app.png)

4. A Keresés mezőbe írja be a **ThirdLight**. Válassza ki **ThirdLight** a keresési eredmények, és válassza ki a **Hozzáadás**.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban fog konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés az ThirdLight Britta Simon nevű tesztfelhasználó használatával.
Egyszeri bejelentkezés engedélyezéséhez szüksége ThirdLight az Azure AD-felhasználót és a megfelelő felhasználó közötti kapcsolat létrehozásához.

Az Azure AD egyszeri bejelentkezés az ThirdLight tesztelése és konfigurálása, szüksége a lépések elvégzéséhez:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  a felhasználók számára a funkció engedélyezéséhez.
2. **[ThirdLight egyszeri bejelentkezés konfigurálása](#configure-thirdlight-single-sign-on)**  az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  ahhoz, hogy az Azure AD egyszeri bejelentkezés a felhasználó számára.
5. **[Hozzon létre egy ThirdLight tesztfelhasználót](#create-a-thirdlight-test-user)**  , amely kapcsolódik a felhasználó Azure ad-ben ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés ThirdLight, ezeket a lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az alkalmazás integrációs ThirdLight oldalán válassza **egyszeri bejelentkezési**:

    ![Válassza ki az egyszeri bejelentkezés](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válasszon **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése:

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon válassza ki a **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** párbeszédpanelen töltse ki az alábbi lépéseket.

    ![Alapszintű SAML-konfigurációja párbeszédpanel](common/sp-identifier.png)

    1. Az a **bejelentkezési URL-cím** mezőbe írjon be egy URL-címe ebben a mintában:
    
          `https://<subdomain>.thirdlight.com/`

    1. Az a **azonosító (entityid)** mezőbe írjon be egy URL-címe ebben a mintában:

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > Ezeket az értékeket a helyőrzők. Szeretné használni, a tényleges bejelentkezési URL-cím és azonosító. Forduljon a [ThirdLight támogatási csapatának](https://www.thirdlight.com/support) értékének lekéréséhez. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** párbeszédpanel az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban jelölje be a **letöltése** mellett kapcsolni **összevonási metaadatainak XML** , a igényeknek, és mentse a fájlt a számítógépen:

    ![Tanúsítvány letöltésére szolgáló hivatkozásra.](common/metadataxml.png)

6. Az a **ThirdLight beállítása** területén másolja a megfelelő URL-címeket, a követelmények alapján:

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL-cím**.

    1. **Az Azure AD-azonosító**.

    1. **Kijelentkezési URL-címe**.

### <a name="configure-thirdlight-single-sign-on"></a>ThirdLight egyszeri bejelentkezés konfigurálása

1. Egy új böngészőablakban jelentkezzen be a ThirdLight vállalati hely rendszergazdaként.

1. Lépjen a **konfigurációs** > **rendszerfelügyelet** > **egy SAML2**:

    ![Rendszerfelügyelet](./media/thirdlight-tutorial/ic805843.png "rendszerfelügyelet")

1. Az egy SAML2 konfigurációs szakaszban az alábbi lépéseket.
  
    ![Egy SAML2 konfigurációs szakasz](./media/thirdlight-tutorial/ic805844.png "egy SAML2 konfigurációs szakasz")

    1. Válassza ki **egy SAML2 egyszeri bejelentkezés engedélyezése**.

    1. Alatt **IdP-metaadatok forrás**válassza **terhelés identitásszolgáltató metaadatainak XML-ről**.

    1. Nyissa meg a metaadatait tartalmazó fájl, amelyet az Azure Portalról az előző szakaszban töltött le. Másolja a fájlt, és illessze be azt a **identitásszolgáltató metaadatainak XML** mezőbe.

    1. Válassza ki **menteni egy SAML2 beállítások**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon Britta Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portalon válassza ki a **Azure Active Directory** a bal oldali panelen válassza ki a **felhasználók**, majd válassza ki **minden felhasználó**:

    ![Válassza ki az összes felhasználó](common/users.png)

2. Válassza ki **új felhasználó** az ablak tetején:

    ![Válassza ki az új felhasználó](common/new-user.png)

3. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket.

    ![Felhasználói párbeszédpanel](common/user-properties.png)

    1. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    1. Az a **felhasználónév** mezőbe írja be **BrittaSimon @\<vállalati_tartomány >.\< bővítmény >** . (Például BrittaSimon@contoso.com.)

    1. Válassza ki **jelszó megjelenítése**, és jegyezze fel az értéket, amely szerepel a **jelszó** mezőbe.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon számára a hozzáférés biztosításával a ThirdLight Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**, majd válassza ki **ThirdLight**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **ThirdLight**.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza ki **felhasználó hozzáadása**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. A a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listában, és kattintson a **válassza** gombra az ablak alján.

6. Ha a SAML-előfeltétel szerepkör értéket a várt a **Szerepkörválasztás** párbeszédpanelen jelölje ki a megfelelő szerepkört a felhasználóhoz a listából. Kattintson a **kiválasztása** gombra az ablak alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **hozzárendelése**.

### <a name="create-a-thirdlight-test-user"></a>ThirdLight tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók ThirdLight bejelentkezni, azokat hozzá ThirdLight kell. Adja hozzá manuálisan kell.

Hozzon létre egy felhasználói fiókot, az alábbi lépéseket kell végrehajtani:

1. A ThirdLight vállalati hely jelentkezzen be rendszergazdaként.

1. Nyissa meg a **felhasználók** fülre.

1. Válassza ki **felhasználók és csoportok**.

1. Válassza ki **új felhasználó hozzáadása**.

1. Adja meg a felhasználónevet, egy név vagy leírás és e-mail-címe érvényes Azure AD-fiók kíván üzembe helyezni. Válasszon egy készletet vagy új tagokat.

1. Kattintson a **Létrehozás** gombra.

> [!NOTE]
> Bármely felhasználói fiók létrehozása az eszközzel, vagy az Azure AD-felhasználói fiókok kiépítése ThirdLight által biztosított API-t.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Most szüksége az Azure AD egyszeri bejelentkezési konfigurációjának tesztelése a hozzáférési Panel használatával.

A ThirdLight csempe kiválasztásakor a hozzáférési panelen azt kell automatikusan megtörténik a a ThirdLight példányhoz, amelyhez beállítani az egyszeri bejelentkezés Konfigurálásához. A hozzáférési panelen kapcsolatos további információkért lásd: [alkalmazások használatának és elérésének a saját alkalmazások portál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory számára oktatóanyagokkal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
