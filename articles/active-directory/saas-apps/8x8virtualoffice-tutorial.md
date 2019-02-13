---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az 8 x 8 virtuális Office |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active könyvtárhoz, majd 8 x 8 virtuális Office között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c92d4fe683ae483ba6384c66dedfa8c1fdd75f8b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205817"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Oktatóanyag: Az Azure Active Directory-integráció az 8 x 8 virtuális Office

Ebben az oktatóanyagban elsajátíthatja, hogyan 8 x 8 virtuális Office integrálható az Azure Active Directory (Azure AD).
8 x 8 integrálása az Azure ad-vel virtuális Office nyújt a következő előnyökkel jár:

* Az Azure ad-ben 8 x 8 virtuális Office hozzáféréssel rendelkező szabályozhatja.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett 8 x 8 virtuális Office (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az 8 x 8 virtuális Office, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* 8 x 8 virtuális Office egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.


* 8 x 8 virtuális Office támogatja **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

* 8 x 8 virtuális Office támogatja **igény szerinti** felhasználók átadása

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>8 x 8 virtuális Office hozzáadása a katalógusból

Az Azure AD-be 8 x 8 virtuális Office-integráció konfigurálásához, hozzá kell 8 x 8 virtuális Office a galériából a felügyelt SaaS-alkalmazások listájára.

**8 x 8 virtuális Office hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **8 x 8 virtuális Office**válassza **8 x 8 virtuális Office** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![8 x 8 virtuális Office, a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés 8 x 8 virtuális Office alapján nevű tesztfelhasználó **Britta Simon**.
Az egyszeri bejelentkezés működéséhez egy Azure AD-felhasználót és a kapcsolódó felhasználó 8 x 8 hivatkozás kapcsolatának virtuális Office kell létrehozni.

Az Azure AD egyszeri bejelentkezés az 8 x 8 virtuális Office tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[8 x 8 konfigurálása virtuális Office egyszeri bejelentkezés](#configure-8x8-virtual-office-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre 8 x 8 virtuális Office tesztfelhasználót](#create-8x8-virtual-office-test-user)**  – a felhasználó Azure ad-ben reprezentációja kapcsolódó 8 x 8 virtuális Office-megfelelője a Britta Simon rendelkeznie.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása 8 x 8 virtuális Office-, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **8 x 8 virtuális Office** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![8 x 8 virtuális Office tartomány és URL-címek egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://sso.8x8.com/saml2`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://sso.8x8.com/saml2`

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **(Raw)tanúsítvány** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificateraw.png)

6. Az a **8 x 8 virtuális Office beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-8x8-virtual-office-single-sign-on"></a>8 x 8 virtuális Office egyszeri bejelentkezés konfigurálása

7. Bejelentkezés a 8 x 8 virtuális Office bérlői rendszergazdaként.

8. Válassza ki **virtuális Office fiók Mgr** alkalmazás panel.

    ![A kiszolgálóoldali alkalmazás konfigurálása](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

9. Válassza ki **üzleti** kezeléséhez, és kattintson a fiók **bejelentkezés** gombra.

    ![A kiszolgálóoldali alkalmazás konfigurálása](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

10. Kattintson a **fiókok** lapon a menüben listában.

    ![A kiszolgálóoldali alkalmazás konfigurálása](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

11. Kattintson a **az egyszeri bejelentkezést** fiókok listájában.
  
    ![A kiszolgálóoldali alkalmazás konfigurálása](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

12. Válassza ki **az egyszeri bejelentkezést** hitelesítési módszereket, és kattintson a **SAML**.

    ![A kiszolgálóoldali alkalmazás konfigurálása](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

13. Az a **egyszeri bejelentkezési SAML** szakaszban, hajtsa végre az alábbi lépéseket:

    ![A kiszolgálóoldali alkalmazás konfigurálása](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. Az a **bejelentkezési az URL-cím** szövegmezőjébe illessze be **bejelentkezési URL-cím** az Azure Portalról másolt érték.

    b. Az a **bejelentkezési kijelentkezési URL-cím** szövegmezőjébe illessze be **kijelentkezési URL-címe** az Azure Portalról másolt érték.

    c. Az a **kiállítójának URL-címe** szövegmezőjébe illessze be **az Azure AD-azonosító** az Azure Portalról másolt érték.

    d. Kattintson a **Tallózás** gombra kattintva töltse fel a tanúsítványt, amely az Azure Portalról letöltött.

    e. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés 8 x 8 virtuális Office Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **8 x 8 virtuális Office**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **8 x 8 virtuális Office**.

    ![A 8 x 8 virtuális Office hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-8x8-virtual-office-test-user"></a>8 x 8 virtuális Office tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó 8 x 8 virtuális Office jön létre. 8 x 8 virtuális Office támogatja **just-in-time-felhasználók létrehozásának**, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a 8 x 8 virtuális Office, a hitelesítés után egy új jön létre.

> [!NOTE]
> Hozzon létre egy felhasználót manuálisan kell, ha kapcsolódni kell a [8 x 8 virtuális Office támogatási csoport](https://www.8x8.com/about-us/contact-us).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint a hozzáférési panelen, a 8 x 8 virtuális Office csempét, meg kell automatikusan megtörténik a az 8 x 8 virtuális Office, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

