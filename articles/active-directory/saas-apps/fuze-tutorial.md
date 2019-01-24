---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Fuze |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Fuze között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 9780b4bf-1fd1-48c1-9ceb-f750225ae08a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeedes
ms.openlocfilehash: 2fdad7df43310132684eae983eae2278629a9b63
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810193"
---
# <a name="tutorial-azure-active-directory-integration-with-fuze"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Fuze

Ebben az oktatóanyagban elsajátíthatja, hogyan Fuze integrálása az Azure Active Directory (Azure AD).

Fuze integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Fuze Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Fuze (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- A fiókok egyetlen központi helyen – az Azure felügyeleti portálján kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Fuze az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Fuze egyszeri bejelentkezéses engedélyezett előfizetés


> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.


Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Az éles környezetben ne használjon, ha erre szükség.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Fuze hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés


## <a name="adding-fuze-from-the-gallery"></a>Fuze hozzáadása a katalógusból
Az Azure AD integrálása a Fuze konfigurálásához hozzá kell Fuze a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Fuze hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálján](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Fuze**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/fuze-tutorial/tutorial_fuze_000.png)

1. Az eredmények panelen válassza ki a **Fuze**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/fuze-tutorial/tutorial_fuze_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Fuze a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Fuze mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Fuze hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** Fuze a.

Az Azure AD egyszeri bejelentkezés az Fuze tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Fuze tesztfelhasználó létrehozása](#creating-a-fuze-test-user)**  - a-megfelelője a Britta Simon szerepel, amely kapcsolódik az Azure ad-ben ábrázolása őt Fuze.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure felügyeleti portálon, és Fuze alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Fuze, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon a a **Fuze** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédpanelen, **mód** kiválasztása **SAML-alapú bejelentkezés** való egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/fuze-tutorial/tutorial_fuze_01.png)

1. Az a **Fuze tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/fuze-tutorial/tutorial_fuze_020.png)
    
    Az a **bejelentkezési URL-cím** szövegmezőbe írja be a Sign-on URL-cím: `https://www.thinkingphones.com/jetspeed/portal/`

1.  Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/fuze-tutorial/tutorial_general_400.png)

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse az xml-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/fuze-tutorial/tutorial_fuze_05.png) 

1. Az egyszeri bejelentkezés konfigurálása **Fuze** oldalon kell küldenie a letöltött **metaadatainak XML** való [Fuze támogatási csapatának](https://www.fuze.com/support). Ezek beállítja ezt annak érdekében, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.


### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure felügyeleti portálján, Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/fuze-tutorial/create_aaduser_01.png) 

1. Lépjen a **felhasználók és csoportok** kattintson **minden felhasználó** felhasználók listájának megjelenítéséhez.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/fuze-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/fuze-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/fuze-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra. 


### <a name="creating-a-fuze-test-user"></a>Fuze tesztfelhasználó létrehozása

Fuze alkalmazás a teljes csak az idő felhasználói kiépítését, támogatja, így a felhasználók fog lekérése automatikusan létrehozza, ha be. A további tisztázása, lépjen kapcsolatba az Fuze [támogatja](https://www.fuze.com/support).

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés Fuze saját hozzáférésének engedélyezésére használja.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Fuze, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Fuze**.

    ![Egyszeri bejelentkezés konfigurálása](./media/fuze-tutorial/tutorial_fuze_50.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Fuze csempére kattint, meg kell lekérése automatikusan bejelentkezett az Fuze alkalmazáshoz.


## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fuze-tutorial/tutorial_general_01.png
[2]: ./media/fuze-tutorial/tutorial_general_02.png
[3]: ./media/fuze-tutorial/tutorial_general_03.png
[4]: ./media/fuze-tutorial/tutorial_general_04.png

[100]: ./media/fuze-tutorial/tutorial_general_100.png

[200]: ./media/fuze-tutorial/tutorial_general_200.png
[201]: ./media/fuze-tutorial/tutorial_general_201.png
[202]: ./media/fuze-tutorial/tutorial_general_202.png
[203]: ./media/fuze-tutorial/tutorial_general_203.png