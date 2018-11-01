---
title: 'Oktatóanyag: Azure Active Directory-integráció az 8 x 8 virtuális Office |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active könyvtárhoz, majd 8 x 8 virtuális Office között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 53db637bf7ad47896747b491fcbe31123fdb104e
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741810"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Oktatóanyag: Azure Active Directory-integráció az 8 x 8 virtuális Office

Ebben az oktatóanyagban elsajátíthatja, hogyan 8 x 8 virtuális Office integrálható az Azure Active Directory (Azure AD).

8 x 8 integrálása az Azure ad-vel virtuális Office nyújt a következő előnyökkel jár:

- Az Azure ad-ben 8 x 8 virtuális Office hozzáféréssel rendelkező szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett 8 x 8 virtuális Office (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az 8 x 8 virtuális Office, a következőkre van szükség:

- Azure AD-előfizetés
- Egy 8 x 8 virtuális Office egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. 8 x 8 virtuális Office hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>8 x 8 virtuális Office hozzáadása a katalógusból

Az Azure AD-be 8 x 8 virtuális Office-integráció konfigurálásához, hozzá kell 8 x 8 virtuális Office a galériából a felügyelt SaaS-alkalmazások listájára.

**8 x 8 virtuális Office hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **8 x 8 virtuális Office**válassza **8 x 8 virtuális Office** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![8 x 8 virtuális Office, a találatok listájában](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés 8 x 8 virtuális Office "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó 8 x 8 virtuális Office, hogy egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó 8 x 8 hivatkozás kapcsolatának virtuális Office kell létrehozni.

Az Azure AD egyszeri bejelentkezés az 8 x 8 virtuális Office tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[8 x 8 virtuális Office tesztfelhasználó létrehozása](#creating-a-8x8-virtual-office-test-user)**  – a felhasználó Azure ad-ben reprezentációja kapcsolódó 8 x 8 virtuális Office-megfelelője a Britta Simon rendelkeznie.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és 8 x 8 virtuális Office-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása 8 x 8 virtuális Office-, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **8 x 8 virtuális Office** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](common/tutorial_general_301.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](common/editconfigure.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![8 x 8 virtuális Office tartomány és URL-címek egyetlen bejelentkezési adatait](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_url.png)

    a. Az a **azonosító** szövegmezőbe írja be egy URL-címe: `https://sso.8x8.com/saml2`

    b. Az a **válasz URL-cím** szövegmezőbe írja be egy URL-címe: `https://sso.8x8.com/saml2`

5. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez **tanúsítvány (Raw)**, és mentse a tanúsítványfájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_certificate.png) 

6. Az a **8 x 8 virtuális Office beállítása** területén másolja a megfelelő URL-címet a követelmény alapján.

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

    ![8 x 8 virtuális Office-konfiguráció](common/configuresection.png)

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

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_02.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.
  
### <a name="creating-a-8x8-virtual-office-test-user"></a>8 x 8 virtuális Office tesztfelhasználó létrehozása

Ez a szakasz célja egy 8 x 8 virtuális Office Britta Simon nevű felhasználó létrehozásához. 8 x 8 virtuális Office támogatja just-in-time-kiépítés, amely alapértelmezés szerint van engedélyezve.

Nincs meg ebben a szakaszban a művelet elem. Új felhasználó 8 x 8 virtuális Office elérését, ha még nem létezik tett kísérlet során jön létre.

> [!NOTE]
> Hozzon létre egy felhasználót manuálisan kell, ha kapcsolódni kell a [8 x 8 virtuális Office támogatási csoport](https://www.8x8.com/about-us/contact-us).

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés 8 x 8 virtuális Office Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **8 x 8 virtuális Office**.

    ![Egyszeri bejelentkezés konfigurálása](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint a 8 x 8 virtuális Office csempét, a hozzáférési panelen, meg kell beolvasása automatikusan bejelentkezett, 8 x 8 virtuális Office-alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
