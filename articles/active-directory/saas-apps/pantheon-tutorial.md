---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Pantheon |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Pantheon között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d2c965d1-666f-44c2-b08f-b73163096374
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 0bfe96bb169e54b1ed6c79390b4aea714da8ee69
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814579"
---
# <a name="tutorial-azure-active-directory-integration-with-pantheon"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Pantheon

Ebben az oktatóanyagban elsajátíthatja, hogyan Pantheon integrálása az Azure Active Directory (Azure AD).

Pantheon integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Pantheon Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Pantheon (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Pantheon az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Pantheon egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Pantheon hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-pantheon-from-the-gallery"></a>Pantheon hozzáadása a katalógusból
Az Azure AD integrálása a Pantheon konfigurálásához hozzá kell Pantheon a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Pantheon hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Pantheon**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/pantheon-tutorial/tutorial_pantheon_search.png)

1. Az eredmények panelen válassza ki a **Pantheon**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/pantheon-tutorial/tutorial_pantheon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Pantheon a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Pantheon mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Pantheon hivatkozás kapcsolata kell létrehozni.

Pantheon, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Pantheon tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Pantheon tesztfelhasználó létrehozása](#creating-a-pantheon-test-user)**  – egy megfelelője a Britta Simon Pantheon, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Pantheon alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Pantheon, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Pantheon** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/pantheon-tutorial/tutorial_pantheon_samlbase.png)

1. Az a **Pantheon tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/pantheon-tutorial/tutorial_pantheon_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `urn:auth0:pantheon:<orgname>-SSO`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://pantheon.auth0.com/login/callback?connection=<orgname>-SSO`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [Pantheon támogatási csapatának](https://pantheon.io/docs/getting-support/) beolvasni ezeket az értékeket.

1. Pantheon alkalmazás adott formátumban, amely megköveteli, hogy a felhasználó e-mail-címmel UserIdentifier attribútumértéket Ön a SAML helyességi feltétel vár. Alapértelmezés szerint az Azure AD használja a UserPrincipalName UserIdentifier attribútum. Azonban a sikeres integráció, hogy módosítani kell ezt az értéket felel meg a felhasználó e-mail címét. Az integráció csak a megfelelő leképezés ezt követően fog működni.

    ![Egyszeri bejelentkezés konfigurálása](./media/pantheon-tutorial/tutorial_attribute.png)    


1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/pantheon-tutorial/tutorial_pantheon_certificate.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/pantheon-tutorial/tutorial_general_400.png)

1. Az a **Pantheon konfigurációs** területén kattintson **konfigurálása Pantheon** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/pantheon-tutorial/tutorial_pantheon_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **Pantheon** oldalon kell küldenie a letöltött **tanúsítvány** és **SAML egyszeri bejelentkezési szolgáltatás URL-cím** való [Pantheon támogatási csoport](https://pantheon.io/docs/getting-support/).

     > [!Note]
     > Meg kell adnia az E-mail tartomány(ok) információkat és dátum idő, ha engedélyezi ezt a kapcsolatot is. Minderről daria további részleteket talál [Itt](https://pantheon.io/docs/sso-organizations/)

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/pantheon-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/pantheon-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/pantheon-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/pantheon-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-pantheon-test-user"></a>Pantheon tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Pantheon hoz létre. Kérjük, kövesse az alábbi lépéseket a felhasználó hozzáadása a Pantheon. 

>[!NOTE] 
>Az SSO felhasználói működéséhez Pantheon először létre kell.

1. Jelentkezzen be Pantheon rendszergazdai hitelesítő adataival.

1. Navigáljon a **szervezet** irányítópult-oldalon.
 
1. Kattintson a **személyek**.

1. Kattintson a **Felhasználó hozzáadása** parancsra.

1. Adja meg a felhasználó e-mail címét.

1. Válassza ki a felhasználói szerepkör.

1. Kattintson a **Felhasználó hozzáadása** parancsra.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Pantheon Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Pantheon, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Pantheon**.

    ![Egyszeri bejelentkezés konfigurálása](./media/pantheon-tutorial/tutorial_pantheon_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Pantheon csempére kattint, meg kell lekérése automatikusan bejelentkezett az Pantheon alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/pantheon-tutorial/tutorial_general_01.png
[2]: ./media/pantheon-tutorial/tutorial_general_02.png
[3]: ./media/pantheon-tutorial/tutorial_general_03.png
[4]: ./media/pantheon-tutorial/tutorial_general_04.png

[100]: ./media/pantheon-tutorial/tutorial_general_100.png

[200]: ./media/pantheon-tutorial/tutorial_general_200.png
[201]: ./media/pantheon-tutorial/tutorial_general_201.png
[202]: ./media/pantheon-tutorial/tutorial_general_202.png
[203]: ./media/pantheon-tutorial/tutorial_general_203.png

