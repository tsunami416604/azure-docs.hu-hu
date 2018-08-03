---
title: 'Oktatóanyag: Azure Active Directory-integráció az myPolicies |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és myPolicies között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bf79e858-1dfb-4ab3-a6df-74b2d5a878d2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: jeedes
ms.openlocfilehash: f4c34d224c65a6e339f12def01079a87247d2d60
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429114"
---
# <a name="tutorial-azure-active-directory-integration-with-mypolicies"></a>Oktatóanyag: Azure Active Directory-integráció az myPolicies

Ebben az oktatóanyagban elsajátíthatja, hogyan myPolicies integrálása az Azure Active Directory (Azure AD).

MyPolicies integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá myPolicies Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az Azure AD-fiókjukkal (egyszeri bejelentkezés) myPolicies
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

MyPolicies az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy myPolicies egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. MyPolicies hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-mypolicies-from-the-gallery"></a>MyPolicies hozzáadása a katalógusból
Az Azure AD integrálása a myPolicies konfigurálásához hozzá kell myPolicies a katalógusból a felügyelt SaaS-alkalmazások listájára.

**MyPolicies hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **myPolicies**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mypolicies-tutorial/tutorial_mypolicies_search.png)

1. Az eredmények panelen válassza ki a **myPolicies**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mypolicies-tutorial/tutorial_mypolicies_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés myPolicies a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó myPolicies mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó myPolicies hivatkozás kapcsolata kell létrehozni.

MyPolicies, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az myPolicies tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[MyPolicies tesztfelhasználó létrehozása](#creating-a-mypolicies-test-user)**  – egy megfelelője a Britta Simon a felhasználó Azure ad-ben reprezentációja kapcsolódó myPolicies rendelkeznie.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és myPolicies alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés myPolicies, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **myPolicies** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/mypolicies-tutorial/tutorial_mypolicies_samlbase.png)

1. Az a **myPolicies tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/mypolicies-tutorial/tutorial_mypolicies_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenantname>.mypolicies.com/`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenantname>.mypolicies.com/users/auth/saml/callback`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [myPolicies támogatási csoportjának](mailto:support@mypolicies.com) beolvasni ezeket az értékeket.

1. A myPolicies alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható. 

    ![Egyszeri bejelentkezés konfigurálása](./media/mypolicies-tutorial/tutorial_mypolicies_attribute.png)

1. Kattintson a **megtekintése és egyéb felhasználói attribútumok szerkesztése** a jelölőnégyzetet a **felhasználói attribútumok** szakaszban bontsa ki az attribútumokat. Hajtsa végre az alábbi lépéseket az egyes a megjelenített attribútumok-

    | Attribútum neve | Attribútum értéke |
    | ------------------- | ---------- |
    | givenName | User.givenName |
    | Vezetéknév | User.surname |
    | e-mail cím | user.mail |
    | név | User.userPrincipalName |
    
    a. Az attribútum megnyitásához kattintson a **attribútum szerkesztése** párbeszédpanel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/mypolicies-tutorial/tutorial_attribute_05.png)
    
    b. Az URL-Címének értékét törli a **Namespace**.
    
    c. Kattintson a **Ok** a beállítás mentéséhez.
    
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/mypolicies-tutorial/tutorial_mypolicies_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/mypolicies-tutorial/tutorial_general_400.png)

1. Az a **myPolicies konfigurációs** területén kattintson **myPolicies konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/mypolicies-tutorial/tutorial_mypolicies_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **myPolicies** oldalon kell küldenie a letöltött **Certificate(Base64)** és **SAML egyszeri bejelentkezési szolgáltatás URL-cím** való [ myPolicies támogatási csoportjának](mailto:support@mypolicies.com). 

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mypolicies-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mypolicies-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mypolicies-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mypolicies-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-mypolicies-test-user"></a>MyPolicies tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű myPolicies hoz létre. Együttműködve [myPolicies támogatási csoportjának](mailto:support@mypolicies.com) a felhasználók hozzáadása az myPolicies platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés myPolicies való hozzáférést.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel myPolicies, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **myPolicies**.

    ![Egyszeri bejelentkezés konfigurálása](./media/mypolicies-tutorial/tutorial_mypolicies_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a myPolicies csempére kattint, meg kell lekérése automatikusan bejelentkezett az myPolicies alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mypolicies-tutorial/tutorial_general_01.png
[2]: ./media/mypolicies-tutorial/tutorial_general_02.png
[3]: ./media/mypolicies-tutorial/tutorial_general_03.png
[4]: ./media/mypolicies-tutorial/tutorial_general_04.png

[100]: ./media/mypolicies-tutorial/tutorial_general_100.png

[200]: ./media/mypolicies-tutorial/tutorial_general_200.png
[201]: ./media/mypolicies-tutorial/tutorial_general_201.png
[202]: ./media/mypolicies-tutorial/tutorial_general_202.png
[203]: ./media/mypolicies-tutorial/tutorial_general_203.png

