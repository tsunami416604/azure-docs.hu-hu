---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező EverBridge |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és EverBridge között.
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
ms.openlocfilehash: 886cfc59ed41e25c8c3953888690f58e4cc4c252
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695339"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező EverBridge

Ebben az oktatóanyagban elsajátíthatja, hogyan EverBridge integrálása az Azure Active Directory (Azure AD).
EverBridge integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá EverBridge Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve EverBridge (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

EverBridge az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* EverBridge egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a EverBridge **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-everbridge-from-the-gallery"></a>EverBridge hozzáadása a katalógusból

Az Azure AD integrálása a EverBridge konfigurálásához hozzá kell EverBridge a katalógusból a felügyelt SaaS-alkalmazások listájára.

**EverBridge hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **EverBridge**válassza **EverBridge** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában EverBridge](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az EverBridge nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó EverBridge hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az EverBridge tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[EverBridge, EverBridge Manager portál egyszeri bejelentkezés konfigurálása](#configure-everbridge-as-everbridge-manager-portal-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[EverBridge, EverBridge Manager portál egyszeri bejelentkezés konfigurálása](#configure-everbridge-as-everbridge-member-portal-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
4. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
5. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
6. **[Hozzon létre EverBridge tesztfelhasználót](#create-everbridge-test-user)**  – egy megfelelője a Britta Simon EverBridge, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
7. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés EverBridge, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **EverBridge** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

    >[!NOTE]
    >Kell tennie az alkalmazást a Manager portál vagy a tag portál mindkét végén, azaz az Azure Portal és Everbridge portál konfigurációit.

4. Konfigurálhatja a **EverBridge** alkalmazást, **EverBridge Manager portál**, az a **alapszintű SAML-konfigurációja** szakaszban a következő lépésekkel:

    ![EverBridge tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://sso.everbridge.net/<API_Name>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [EverBridge támogatási csapatának](mailto:support@everbridge.com) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Konfigurálhatja a **EverBridge** alkalmazást, **EverBridge tag portál**, a a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

   * Ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![EverBridge tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    * Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    * Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

* Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![EverBridge tartomány és URL-címeket egyetlen bejelentkezési adatait](common/both-signonurl.png)

    * Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezeket az értékeket frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-címe. Kapcsolattartó [EverBridge támogatási csapatának](mailto:support@everbridge.com) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

7. Az a **EverBridge beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>EverBridge EverBridge Manager portál egyszeri bejelentkezés konfigurálása

1. Egyszeri bejelentkezés konfigurálva beolvasásához **EverBridge** , **EverBridge Manager portál** alkalmazás, hajtsa végre az alábbi lépéseket: 
 
2. Egy másik böngészőablakban jelentkezzen be EverBridge rendszergazdaként.

3. A felső menüben kattintson a **beállítások** lapot, és válasszon **egyszeri bejelentkezés** alatt **biztonsági**.
   
     ![Egyszeri bejelentkezés konfigurálása](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Az a **neve** szövegmezőbe írja be a szolgáltató azonosító neve (például: a vállalat neve).
   
     b. Az a **API neve** szövegmezőbe írja be az API neve.
   
     c. Kattintson a **fájl kiválasztása** gombra kattintva töltse fel a metaadat-fájlt, amely az Azure Portalról letöltött.
   
     d. Válassza ki a SAML-identitás helyen **identitás a tulajdonos utasítás NameIdentifier elemében van**.
   
     e. Az a **Identity Provider bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **bejelentkezési URL-cím** Azure Portalról másolt.
   
     f. Válassza ki a szolgáltató által kezdeményezett kérelem kötés, **HTTP-átirányítás**.

     g. Kattintson a **Save** (Mentés) gombra.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>EverBridge EverBridge tag portál egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **EverBridge** , **EverBridge tag portál**, kell küldenie a letöltött **összevonási metaadatainak XML** való [Everbridge támogatási csoport](mailto:support@everbridge.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés EverBridge Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **EverBridge**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **EverBridge**.

    ![Az alkalmazások listáját a EverBridge hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-everbridge-test-user"></a>EverBridge tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Everbridge hoz létre. Együttműködve [EverBridge támogatási csapatának](mailto:support@everbridge.com) a felhasználók hozzáadása az Everbridge platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt EverBridge aktiválva. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a EverBridge csempére kattint, meg kell lehet automatikusan bejelentkezett a EverBridge, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

