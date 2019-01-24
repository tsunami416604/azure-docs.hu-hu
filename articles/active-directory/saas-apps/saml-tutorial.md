---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az SAML 1.1-es jogkivonat engedélyezve van a LOB-alkalmazás |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory között, és a SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 45673fcf09ae497859ba3fd3a05046eca5452050
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814834"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Oktatóanyag: Az Azure Active Directory integrációja az SAML 1.1-es jogkivonat engedélyezve van a LOB-alkalmazás

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás az Azure Active Directoryval (Azure AD).
Integrálja a SAML 1.1-es jogkivonat engedélyezett LOB-alkalmazás az Azure ad-vel nyújt a következő előnyökkel jár:

* Szabályozhatja, hogy az Azure AD ki férhet hozzá a SAML 1.1-es Token LOB-alkalmazás engedélyezve van.
* Engedélyezheti a felhasználók automatikusan bejelentkezett a SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Konfigurálhatja az Azure AD-integráció a SAML 1.1-es Token LOB-alkalmazás engedélyezve van, a következőkre van szüksége:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás egyszeri bejelentkezési engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás által támogatott **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>SAML 1.1-jogkivonat hozzáadása engedélyezve van a LOB-alkalmazás a katalógusból

SAML 1.1-es Token integrációjának konfigurálása engedélyezve LOB-alkalmazás az Azure AD-be, a SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás a galériából a felügyelt SaaS-alkalmazások listájában, hozzá kell adnia.

**SAML 1.1-es jogkivonat engedélyezve van a katalógusból LOB-alkalmazás hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás**, jelölje be **SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálni és vizsgálat az Azure AD egyszeri bejelentkezés a SAML 1.1-es jogkivonat engedélyezve van a LOB-alkalmazást egy teszt nevű felhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működéséhez egy Azure AD-felhasználót és a kapcsolódó felhasználó a SAML 1.1-es Token hivatkozás kapcsolata engedélyezve van a LOB-alkalmazás kell létrehozni.

A konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a SAML 1.1-es jogkivonat engedélyezve van a LOB-alkalmazáshoz, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálása SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás egyszeri bejelentkezéssel](#configure-saml-11-token-enabled-lob-app-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre a SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás tesztfelhasználó](#create-saml-11-token-enabled-lob-app-test-user)**  – hogy egy megfelelője a Britta Simon a SAML 1.1-es jogkivonat engedélyezve van a LOB-alkalmazás, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálhatja az Azure AD egyszeri bejelentkezés a SAML 1.1-es jogkivonat engedélyezve van a LOB-alkalmazáshoz, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás tartomány és URL-címek egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://your-app-url`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://your-app-url`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartási SAML 1.1-es jogkivonat engedélyezve van a LOB-alkalmazás ügyfél-támogatási csoport beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **SAML 1.1-es Token beállítása engedélyezve van a LOB-alkalmazás** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-saml-11-token-enabled-lob-app-single-sign-on"></a>Konfigurálása LOB-alkalmazás egyszeri bejelentkezéssel SAML 1.1-es jogkivonat engedélyezve

Az egyszeri bejelentkezés konfigurálása **SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás** oldalon kell küldenie a letöltött **tanúsítvány (Base64)** és megfelelő másolt URL-címek az Azure Portalról SAML 1.1-es jogkivonat engedélyezve van a LOB-alkalmazás támogatási csoportjának. Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon használható az Azure engedélyezi az egyszeri bejelentkezés a hozzáférés biztosításával SAML 1.1-es jogkivonat engedélyezve van a LOB-alkalmazás.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás**.

    ![A SAML 1.1-es jogkivonat engedélyezve van az alkalmazások listáját a LOB-alkalmazás-hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>Hozzon létre a SAML 1.1-es jogkivonat engedélyezve van a tesztfelhasználó LOB-alkalmazás

Ebben a szakaszban hoz létre egy nevű Britta Simon felhasználó SAML 1.1-es Token LOB-alkalmazás engedélyezve. A SAML 1.1-es jogkivonat használata engedélyezve van a LOB-alkalmazás támogatási csapatával a SAML 1.1-es jogkivonat számára engedélyezve van a LOB-alkalmazás platform hozzáadása. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Kattintás után a SAML 1.1-es jogkivonat engedélyezve van a hozzáférési panelen LOB-alkalmazás csempéjére, meg kell hogy automatikusan jelentkezzenek be a LOB-alkalmazás, amelynek beállítása egyszeri bejelentkezés a SAML 1.1-es jogkivonat engedélyezve. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

