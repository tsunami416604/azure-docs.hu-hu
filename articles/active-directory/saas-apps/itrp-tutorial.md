---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ITRP |} A Microsoft Docs'
description: Ebben az oktatóanyagban elsajátíthatja fog konfigurálása egyszeri bejelentkezéshez ITRP és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: d44391624e29d2bdd182bb07452e0e8def2d1407
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656699"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ITRP

Ebben az oktatóanyagban elsajátíthatja a ITRP integrálása az Azure Active Directory (Azure AD) lesz.
Ez az integráció ezeket az előnyöket biztosítja:

* Az Azure AD, hogy ki férhet hozzá ITRP is használhatja.
* Engedélyezheti a felhasználók számára, hogy automatikusan jelentkezzenek be ITRP (egyszeri bejelentkezés), az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása ITRP, szüksége lesz:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókot](https://azure.microsoft.com/free/).
* Egyszeri bejelentkezés engedélyezve tartalmazó ITRP előfizetést.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban fogja konfigurálni, és egy tesztelési környezetben az Azure AD egyszeri bejelentkezés tesztelése.

* ITRP SP által kezdeményezett egyszeri Bejelentkezést támogatja.

## <a name="add-itrp-from-the-gallery"></a>ITRP hozzáadása a katalógusból

ITRP integrálása az Azure AD beállításához, hozzá kell ITRP a galériából a felügyelt SaaS-alkalmazások listájára.

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** > **minden alkalmazás**:

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Egy alkalmazás hozzáadásához válassza **új alkalmazás** az ablak tetején:

    ![Válassza ki az új alkalmazás](common/add-new-app.png)

4. A Keresés mezőbe írja be a **ITRP**. Válassza ki **ITRP** a keresési eredmények, és válassza ki a **Hozzáadás**.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban fog konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés az ITRP Britta Simon nevű tesztfelhasználó használatával.
Egyszeri bejelentkezés engedélyezéséhez szüksége ITRP az Azure AD-felhasználót és a megfelelő felhasználó közötti kapcsolat létrehozásához.

Az Azure AD egyszeri bejelentkezés az ITRP tesztelése és konfigurálása, szüksége a lépések elvégzéséhez:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  a felhasználók számára a funkció engedélyezéséhez.
2. **[ITRP egyszeri bejelentkezés konfigurálása](#configure-itrp-single-sign-on)**  az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  ahhoz, hogy az Azure AD egyszeri bejelentkezés a felhasználó számára.
5. **[Hozzon létre egy ITRP tesztfelhasználót](#create-an-itrp-test-user)**  , amely kapcsolódik a felhasználó Azure ad-ben ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés ITRP, ezeket a lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az alkalmazás integrációs ITRP oldalán válassza **egyszeri bejelentkezési**:

    ![Válassza ki az egyszeri bejelentkezés](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válasszon **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése:

    ![Egyszeri bejelentkezés módszer kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon válassza ki a **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** párbeszédpanel mezőbe az alábbi lépéseket.

    ![Alapszintű SAML-konfigurációja párbeszédpanel](common/sp-identifier.png)

    1. Az a **bejelentkezési URL-cím** mezőbe írjon be egy URL-címe ebben a mintában:
    
       `https://<tenant-name>.itrp.com`

    1. Az a **azonosító (entityid)** mezőbe írjon be egy URL-címe ebben a mintában:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Ezeket az értékeket a helyőrzők. Szeretné használni, a tényleges bejelentkezési URL-cím és azonosító. Forduljon a [ITRP támogatási csoportjának](https://www.itrp.com/support) értékének lekéréséhez. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** párbeszédpanel az Azure Portalon.

5. Az a **SAML-aláíró tanúsítvány** szakaszban jelölje be a **szerkesztése** ikonra kattintva nyissa meg a **SAML-aláíró tanúsítvány** párbeszédpanel:

    ![Szerkesztés ikon](common/edit-certificate.png)

6. Az a **SAML-aláíró tanúsítvány** párbeszédpanelen, a Másolás a **ujjlenyomat** értékét, és mentse azt:

    ![Másolja ki az ujjlenyomat értékét](common/copy-thumbprint.png)

7. Az a **ITRP beállítása** területén másolja a megfelelő URL-címeket, a követelmények alapján:

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL-cím**.

    1. **Az Azure AD-azonosító**.

    1. **Kijelentkezési URL-címe**.

### <a name="configure-itrp-single-sign-on"></a>ITRP egyszeri bejelentkezés konfigurálása

1. Egy új böngészőablakban jelentkezzen be a ITRP vállalati hely rendszergazdaként.

1. Az ablak tetején válassza a **beállítások** ikonra:

    ![Beállítások ikon](./media/itrp-tutorial/ic775570.png "beállítások ikon")

1. A bal oldali panelen válassza ki a **egyszeri bejelentkezés**:

    ![Válassza az egyszeri bejelentkezés](./media/itrp-tutorial/ic775571.png "válassza az egyszeri bejelentkezés")

1. Az a **egyszeri bejelentkezés** konfigurációs szakaszban, az alábbi lépéseket.

    ![Egyszeri bejelentkezés szakasz](./media/itrp-tutorial/ic775572.png "egyszeri bejelentkezés szakasz")

    ![Egyszeri bejelentkezés szakasz](./media/itrp-tutorial/ic775573.png "egyszeri bejelentkezés szakasz")

    1. Válassza ki **engedélyezve**.

    1. Az a **távoli kijelentkezési URL-címe** mezőbe illessze be a **kijelentkezési URL-címe** , az Azure Portalról másolt érték.

    1. Az a **SAML egyszeri bejelentkezési URL-cím** mezőbe illessze be a **bejelentkezési URL-cím** , az Azure Portalról másolt érték.

    1. Az a **tanúsítvány-ujjlenyomat** mezőbe illessze be a **ujjlenyomat** a tanúsítványt, amely az Azure Portalról másolt értékét.

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
  
    1. Az a **felhasználónév** mezőbe írja be **BrittaSimon @\<vállalati_tartomány >.\< bővítmény >** . (Például BrittaSimon@contoso.com.)

    1. Válassza ki **jelszó megjelenítése**, és jegyezze fel az értéket, amely szerepel a **jelszó** mezőbe.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon számára a hozzáférés biztosításával a ITRP Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**, majd válassza ki **ITRP**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **ITRP**.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza ki **felhasználó hozzáadása**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. A a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listában, és kattintson a **válassza** gombra az ablak alján.

6. Ha a SAML-előfeltétel szerepkör értéket a várt a **Szerepkörválasztás** párbeszédpanelen jelölje ki a megfelelő szerepkört a felhasználóhoz a listából. Kattintson a **kiválasztása** gombra az ablak alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **hozzárendelése**.

### <a name="create-an-itrp-test-user"></a>Hozzon létre egy ITRP tesztfelhasználó számára

Ahhoz, hogy az Azure AD-felhasználók ITRP bejelentkezni, azokat hozzá ITRP kell. Adja hozzá manuálisan kell.

Hozzon létre egy felhasználói fiókot, az alábbi lépéseket kell végrehajtani:

1. Jelentkezzen be a ITRP bérlő.

1. Az ablak tetején válassza a **rekordok** ikonra:

    ![Rekordok ikon](./media/itrp-tutorial/ic775575.png "rekordok ikon")

1. Válassza a menüben **személyek**:

    ![Személyek kiválasztása](./media/itrp-tutorial/ic775587.png "személyek kiválasztása")

1. Válassza a pluszjelet ( **+** ) hozzáadása egy új személy:

    ![Válassza a pluszjelet](./media/itrp-tutorial/ic775576.png "válassza a pluszjelet")

1. Az a **hozzáadása új személy** párbeszédpanel mezőbe az alábbi lépéseket.

    ![Hozzáadás új személy párbeszédpanel](./media/itrp-tutorial/ic775577.png "új személy hozzáadása párbeszédpanel")

    1. Adja meg egy érvényes Azure és az e-mail-címét, amely a hozzáadni kívánt AD-fiókot.

    1. Kattintson a **Mentés** gombra.

> [!NOTE]
> Bármely felhasználói fiók létrehozása az eszközzel, vagy az Azure AD-felhasználói fiókok kiépítése ITRP által biztosított API-t.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Most szüksége az Azure AD egyszeri bejelentkezési konfigurációjának tesztelése a hozzáférési Panel használatával.

A ITRP csempe kiválasztásakor a hozzáférési panelen azt kell automatikusan megtörténik a a ITRP példányhoz, amelyhez beállítani az egyszeri bejelentkezés Konfigurálásához. A hozzáférési panelen kapcsolatos további információkért lásd: [alkalmazások használatának és elérésének a saját alkalmazások portál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory számára oktatóanyagokkal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
