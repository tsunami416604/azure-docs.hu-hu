---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Everbridge |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Everbridge között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: f8dd11e7fb0b9fda0e0f1c7d3f794f6bfd766cdf
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231466"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Everbridge

Ebben az oktatóanyagban elsajátíthatja, hogyan Everbridge integrálása az Azure Active Directory (Azure AD).
Everbridge integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá Everbridge Azure AD-ben.
* Lehetővé teszi a felhasználók számára, hogy automatikusan jelentkezzenek be Everbridge, az Azure AD-fiókjukat. A hozzáférés-vezérlés az egyszeri bejelentkezés (SSO) nevezzük.
* A fiókok egyetlen központi helyen kezelheti az Azure portal használatával.
Az Azure ad-vel szoftverként (saas biztosított) alkalmazás integrációja szoftverrel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Everbridge az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókot](https://azure.microsoft.com/free/).
* Egyszeri bejelentkezést használó Everbridge előfizetésbe.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Everbridge Identitásszolgáltató által kezdeményezett egyszeri Bejelentkezést támogatja.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Az Azure Marketplace-ről Everbridge hozzáadása

Az Azure AD integrálása a Everbridge konfigurálásához adja hozzá az Azure Marketplace-ről a felügyelt SaaS-alkalmazások listájában Everbridge.

Az Azure Marketplace-ről Everbridge hozzáadásához kövesse az alábbi lépéseket.

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory-gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza **új alkalmazás** a párbeszédpanel tetején.

    ![Új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Everbridge**. Válassza ki **Everbridge** az eredmény panelen, és válassza ki a **Hozzáadás**.

     ![Az eredmények listájában Everbridge](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Everbridge a tesztfelhasználó számára Britta Simon alapján.
Az egyszeri bejelentkezés működjenek, Everbridge az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Az Azure AD egyszeri bejelentkezés az Everbridge tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

- [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
- [Everbridge Everbridge manager portál egyszeri bejelentkezés konfigurálása](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazás oldalán.
- [Everbridge Everbridge tag portál egyszeri bejelentkezés konfigurálása](#configure-everbridge-as-everbridge-member-portal-single-sign-on) az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazás oldalán.
- [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
- [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
- [Hozzon létre egy Everbridge tesztfelhasználót](#create-an-everbridge-test-user) egy megfelelője a Britta Simon Everbridge, amely a felhasználó Azure ad-ben reprezentációja van csatolva van.
- [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Everbridge, kövesse az alábbi lépéseket.

1. Az a [az Azure portal](https://portal.azure.com/), az a **Everbridge** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza ki a **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon jelölje be **szerkesztése** megnyitásához a **alapszintű SAML-konfigurációja** párbeszédpanel bezárásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

    >[!NOTE]
    >Az alkalmazás vagy a manager portál konfigurálása *vagy* , a tag portálon az Azure portal és a Everbridge portálon is.

4. Konfigurálhatja a **Everbridge** alkalmazást, a **Everbridge manager portál**, a a **alapszintű SAML-konfigurációja** területén kövesse az alábbi lépéseket:

    ![Everbridge tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** mezőben adja meg azt a mintát követi `https://sso.everbridge.net/<API_Name>`

    b. Az a **válasz URL-cím** mezőben adja meg azt a mintát követi `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Ezek az értékek nem valódi. Az értékeket módosítsa a tényleges azonosítóját és a válasz URL-cím értéke. Ezek az értékek beszerzéséhez forduljon a [Everbridge támogatási csapatának](mailto:support@everbridge.com). Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Konfigurálhatja a **Everbridge** alkalmazást, a **Everbridge tag portál**, a a **alapszintű SAML-konfigurációja** területén kövesse az alábbi lépéseket:

  * Ha meg szeretné konfigurálni az alkalmazás Identitásszolgáltató által kezdeményezett módban, kövesse az alábbi lépéseket:

     ![Everbridge tartomány és URL-címeket egyetlen bejelentkezési adatait az Identitásszolgáltató által kezdeményezett mód](common/idp-intiated.png)

    a. Az a **azonosító** mezőben adja meg azt a mintát követi `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. Az a **válasz URL-cím** mezőben adja meg azt a mintát követi `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Ha az alkalmazás konfigurálása a Szolgáltató által kezdeményezett módban szeretne, válassza ki **további URL-címet beállítani** , és kövesse az alábbi lépéseket:

     ![Everbridge tartomány és URL-címeket egyetlen bejelentkezési adatait a Szolgáltató által kezdeményezett mód](common/both-signonurl.png)

     a. Az a **bejelentkezési URL-cím** mezőben adja meg azt a mintát követi `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Ezek az értékek nem valódi. Az értékeket módosítsa a tényleges azonosítóval, válasz URL-cím, és jelentkezzen be az URL-cím értékeit. Ezek az értékek beszerzéséhez forduljon a [Everbridge támogatási csapatának](mailto:support@everbridge.com). Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban jelölje be **letöltése** letöltéséhez a **összevonási metaadatainak XML** . Mentse a számítógépre.

    ![Tanúsítvány letöltésére szolgáló hivatkozásra.](common/metadataxml.png)

7. Az a **Everbridge beállítása** területén másolja az URL-címeket, a követelmények kielégítése érdekében:

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    - Bejelentkezési URL
    - Azure AD-azonosító
    - Kijelentkezési URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Everbridge Everbridge manager portál egyszeri bejelentkezés konfigurálása

Egyszeri bejelentkezés konfigurálása **Everbridge** , egy **Everbridge manager portál** alkalmazást, kövesse az alábbi lépéseket.
 
1. Egy másik böngészőablakban jelentkezzen be Everbridge rendszergazdaként.

1. A felső menüben válassza a **beállítások** fülre. A **biztonsági**válassza **egyszeri bejelentkezés**.
   
     ![Egyszeri bejelentkezés konfigurálása](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Az a **neve** adja meg az azonosító szolgáltató neve. Ilyen például, a cég nevét.
   
     b. Az a **API neve** adja meg az API neve.
   
     c. Válassza ki **fájl kiválasztása** az Azure-portálról letöltött metaadatfájl feltöltése.
   
     d. A **SAML identitás hely**válassza **identitás a tulajdonos utasítás NameIdentifier elemében van**.
   
     e. Az a **Identity Provider bejelentkezési URL-cím** mezőbe illessze be a **bejelentkezési URL-cím** , az Azure Portalról másolt érték.
   
     f. A **szolgáltató által kezdeményezett kérelem kötés**válassza **HTTP-átirányítás**.

     g. Kattintson a **Mentés** gombra.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Everbridge Everbridge tag portál egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **Everbridge** , egy **Everbridge tag portál**, küldjön a letöltött **összevonási metaadatainak XML** , a [Everbridge támogatási csoport](mailto:support@everbridge.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Az Azure Portalon a tesztfelhasználó számára Britta Simon létrehozásához kövesse az alábbi lépéseket.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory** > **felhasználók** > **minden felhasználó**.

    ![Felhasználók és a felhasználók minden kapcsolat](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az új felhasználó gomb](common/new-user.png)

3. Az a **felhasználói** párbeszédpanelen kövesse az alábbi lépéseket.

    ![Felhasználói párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com.

    c. Válassza ki a **jelszó megjelenítése** jelölőnégyzetet. Írja le az értéket, amely jeleníti meg a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Britta Simon által biztosított hozzáférés Everbridge Azure egyszeri bejelentkezés használata lehetővé.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások** > **minden alkalmazás** >**Everbridge**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Everbridge**.

    ![Az alkalmazások listáját Everbridge hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![Felhasználók és csoportok hivatkozás](common/users-groups-blade.png)

4. Válassza ki **felhasználó hozzáadása**. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    ![Hozzárendelés párbeszédpanel hozzáadása](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listában. Válasszon **kiválasztása** a képernyő alján.

6. Ha a várt bármely szerepkör értékét az SAML-előfeltétel a **Szerepkörválasztás** párbeszédpanelen jelölje ki a megfelelő szerepkört a felhasználóhoz a listából. Válasszon **kiválasztása** a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **hozzárendelése**.

### <a name="create-an-everbridge-test-user"></a>Hozzon létre egy Everbridge tesztfelhasználó számára

Ebben a szakaszban a tesztfelhasználó számára a Everbridge Britta Simon hoz létre. Felhasználók hozzáadása az Everbridge platformon, dolgozni a [Everbridge támogatási csapatának](mailto:support@everbridge.com). Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt Everbridge aktiválva. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata 

Az Azure AD egyszeri bejelentkezés beállításainak ellenőrzéséhez a hozzáférési panelen.

A Everbridge csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a Everbridge fiókot, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

