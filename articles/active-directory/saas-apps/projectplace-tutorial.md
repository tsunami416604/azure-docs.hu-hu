---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a Projectplace |} A Microsoft Docs'
description: Ebben az oktatóanyagban elsajátíthatja fog konfigurálása egyszeri bejelentkezéshez az Azure Active Directory és a Projectplace között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 17fbc6bc4f022a15c34c5ca7b9465be392cdc639
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560626"
---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Oktatóanyag: Projectplace az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja a Projectplace integrálása az Azure Active Directory (Azure AD) lesz.

Ez az integráció ezeket az előnyöket biztosítja:

* Az Azure AD, hogy ki férhet hozzá a Projectplace is használhatja.
* Engedélyezheti a felhasználók számára, hogy automatikusan jelentkezzenek be a Projectplace (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Projectplace az Azure AD-integráció konfigurálásához lesz szüksége:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, akkor regisztráljon egy [egy havi próbalehetőség](https://azure.microsoft.com/pricing/free-trial/) előfizetés.
* Projectplace-előfizetés, amely egyszeri bejelentkezés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban fogja konfigurálni, és egy tesztelési környezetben az Azure AD egyszeri bejelentkezés tesztelése.

* Projectplace a Szolgáltató által kezdeményezett egyszeri Bejelentkezést támogatja.

## <a name="add-projectplace-from-the-gallery"></a>Projectplace hozzáadása a katalógusból

Projectplace integrálása az Azure AD beállításához, hozzá kell Projectplace a galériából a felügyelt SaaS-alkalmazások listájára.

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** > **minden alkalmazás**:

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Egy alkalmazás hozzáadásához válassza **új alkalmazás** az ablak tetején:

    ![Válassza ki az új alkalmazás](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Projectplace**. Válassza ki **Projectplace** a keresési eredmények, és válassza ki a **Hozzáadás**.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban fog konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés a Projectplace Britta Simon nevű tesztfelhasználó használatával.
Egyszeri bejelentkezés engedélyezéséhez szüksége egy Azure AD-felhasználót és a megfelelő felhasználó közötti kapcsolat létrehozására a Projectplace.

Az Azure AD egyszeri bejelentkezés a Projectplace tesztelése és konfigurálása, szüksége a lépések elvégzéséhez:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  a felhasználók számára a funkció engedélyezéséhez.
2. **[Projectplace egyszeri bejelentkezés konfigurálása](#configure-projectplace-single-sign-on)**  az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  ahhoz, hogy az Azure AD egyszeri bejelentkezés a felhasználó számára.
5. **[Hozzon létre egy Projectplace tesztfelhasználót](#create-a-projectplace-test-user)**  , amely kapcsolódik a felhasználó Azure ad-ben ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Projectplace, ezeket a lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Projectplace** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**:

    ![Válassza ki az egyszeri bejelentkezés](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válasszon **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése:

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon válassza ki a **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** párbeszédpanel a **bejelentkezési URL-** mezőbe írjon be egy URL-címe ebben a mintában:

    `https://<company>.projectplace.com`

   ![Alapszintű SAML-konfigurációja párbeszédpanel](common/sp-signonurl.png)
    > [!NOTE]
    > Ez az érték pedig. A tényleges bejelentkezési URL-címet kell. Forduljon a [Projectplace-ügyfélszolgálathoz](https://success.planview.com/Projectplace/Support) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** párbeszédpanel az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban jelölje be a **letöltése** mellett kapcsolni **összevonási metaadatainak XML** , a igényeknek, és mentse el a tanúsítványt a számítógépen:

    ![Tanúsítvány letöltésére szolgáló hivatkozásra.](common/metadataxml.png)

6. Az a **állítsa be a Projectplace** területén másolja a megfelelő URL-címeket, a követelmények alapján.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL-cím**.

    1. **Az Azure AD-azonosító**.

    1. **Kijelentkezési URL-címe**.

### <a name="configure-projectplace-single-sign-on"></a>Projectplace egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálásához a **Projectplace** oldalon kell küldenie a letöltött **összevonási metaadatainak XML** tanúsítvány és az Azure Portalról másolt URL-címeket a [ Projectplace-ügyfélszolgálathoz](https://success.planview.com/Projectplace/Support). Ez a csapat biztosítja, hogy a SAML SSO-kapcsolat mindkét oldalán megfelelően beállítva.

>[!NOTE]
>Az egyszeri bejelentkezés beállításainak által végrehajtandó rendelkezik a [Projectplace-ügyfélszolgálathoz](https://success.planview.com/Projectplace/Support). Értesítést fog kapni, amint a konfigurálás befejeződött.

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

Ebben a szakaszban Britta Simon projectplace tartalomcsomagjához számára a hozzáférés biztosításával az Azure AD egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**, majd válassza ki **Projectplace**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Projectplace**.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza ki **felhasználó hozzáadása**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza ki **Britta Simon** a felhasználók listában, és kattintson a **válassza** gombra a képernyő alján.

6. Ha a SAML-előfeltétel szerepkör értéket a várt a **Szerepkörválasztás** párbeszédpanelen jelölje ki a megfelelő szerepkört a felhasználóhoz a listából. Kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **hozzárendelése**.

### <a name="create-a-projectplace-test-user"></a>Projectplace tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók a Projectplace bejelentkezni, hozzá kell őket a Projectplace. Ezeket manuálisan kell hozzáadni.

Hozzon létre egy felhasználói fiókot, az alábbi lépéseket kell végrehajtani:

1. Jelentkezzen be a **Projectplace** vállalati hely rendszergazdaként.

2. Lépjen a **személyek**, majd válassza ki **tagok**:
   
    ![Ugrás a személyek, és kattintson a tagok](./media/projectplace-tutorial/ic790228.png "személyek")

3. Válassza ki **tag hozzáadása**:
   
    ![Válassza ki a tag hozzáadása](./media/projectplace-tutorial/ic790232.png "tagok hozzáadása")

4. Az a **tag hozzáadása** területén az alábbi lépéseket.
   
    ![Tag szakasszal](./media/projectplace-tutorial/ic790233.png "új tagok")
   
    1. Az a **új tagok** mezőbe írja be az e-mail-címe érvényes Azure AD-fiókot, amely a hozzáadni kívánt.
   
    1. Kattintson a **Küldés** gombra.

   Mielőtt aktívvá válik, győződjön meg arról, hogy a fiók mutató hivatkozást tartalmazó e-mail érkezik az Azure ad-ben fióktulajdonos.

>[!NOTE]
>Bármely más felhasználóifiók-létrehozási eszköz is használhatja, vagy Projectplace adhat hozzá felhasználói fiókokat az Azure AD által biztosított API-t.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Most szüksége az Azure AD egyszeri bejelentkezési konfigurációjának tesztelése a hozzáférési Panel használatával.

A Projectplace-csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a Projectplace-példányt, amelynek beállítása egyszeri bejelentkezés. További információkért lásd: [alkalmazások használatának és elérésének a saját alkalmazások portál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory számára oktatóanyagokkal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
