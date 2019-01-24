---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Intacct |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Intacct között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: d7075894bde20f56c9323568ceffe8cad4118cbc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810091"
---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Intacct

Ebben az oktatóanyagban elsajátíthatja, hogyan Intacct integrálása az Azure Active Directory (Azure AD).

Intacct integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Intacct Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Intacct (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Intacct az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Intacct egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Intacct hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-intacct-from-the-gallery"></a>Intacct hozzáadása a katalógusból
Az Azure AD integrálása a Intacct konfigurálásához hozzá kell Intacct a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Intacct hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Intacct**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/intacct-tutorial/tutorial_intacct_search.png)

1. Az eredmények panelen válassza ki a **Intacct**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/intacct-tutorial/tutorial_intacct_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Intacct a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Intacct mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Intacct hivatkozás kapcsolata kell létrehozni.

Intacct, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Intacct tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Egy Intacct tesztfelhasználó létrehozása](#creating-an-intacct-test-user)**  – egy megfelelője a Britta Simon Intacct, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Intacct alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Intacct, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Intacct** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/intacct-tutorial/tutorial_intacct_samlbase.png)

1. Az a **Intacct tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/intacct-tutorial/tutorial_intacct_url.png)

    Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<companyname>.intacct.com/ia/acct/sso_response.phtml`|
    | `https://www.intacct.com/ia/acct/sso_response.phtml` |

    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges válasz URL-cím. Kapcsolattartó [Intacct támogatási csapatának](https://us.intacct.com/support) lekérni ezt az értéket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/intacct-tutorial/tutorial_intacct_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/intacct-tutorial/tutorial_general_400.png)

1. Az a **Intacct konfigurációs** területén kattintson **konfigurálása Intacct** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/intacct-tutorial/tutorial_intacct_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a Intacct vállalati hely rendszergazdaként.

1. Kattintson a **vállalati** fülre, majd **vállalati adatok**.

    ![Vállalati](./media/intacct-tutorial/ic790037.png "vállalati")

1. Kattintson a **biztonsági** fülre, majd **szerkesztése**.

    ![Biztonsági](./media/intacct-tutorial/ic790038.png "biztonsági")

1. Az a **egyszeri bejelentkezés (SSO)** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés](./media/intacct-tutorial/ic790039.png "egyszeri bejelentkezés")

    a. Válassza ki **egyszeri bejelentkezés engedélyezése**.

    b. Mint **identitásszolgáltató típusa**válassza **SAML 2.0**.

    c. A **kiállítójának URL-címe** szövegmezőjébe illessze be az értéket, **SAML Entitásazonosító** Azure Portalról másolt.
   
    d. A **bejelentkezési URL-cím** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.

    e. Nyissa meg a **base-64** kódolású Jegyzettömbben-tanúsítványt, a tartalmát a vágólapra másolja és illessze be azt a **tanúsítvány** mezőbe.
   
    f. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/intacct-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/intacct-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/intacct-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/intacct-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-intacct-test-user"></a>Egy Intacct tesztfelhasználó létrehozása

Beállítása az Azure AD-felhasználók, így Intacct tudjon jelentkezni, akkor ki kell építeni Intacct be. Intacct a kiépítés a manuális feladat.

**Felhasználói fiókok kiépítése, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Intacct** bérlő.

1. Kattintson a **vállalati** fülre, majd **felhasználók**.

    ![Felhasználók](./media/intacct-tutorial/ic790041.png "felhasználók")
1. Kattintson a **Hozzáadás** fülre.

    ![Adjon hozzá](./media/intacct-tutorial/ic790042.png "hozzáadása")
1. Az a **felhasználói adatok** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Felhasználói adatok](./media/intacct-tutorial/ic790043.png "felhasználói adatok")

    a. Adja meg a **Felhasználóazonosító**, a **Vezetéknév**, **Utónév**, a **E-mail-cím**, a **cím**, és a **Phone** egy Azure AD-fiók létrehozásához használni kívánt a **felhasználói adatok** szakaszban.

    b. Válassza ki a **rendszergazdai jogosultságokat** kíván üzembe helyezni egy Azure AD-fiók.
   
    c. Kattintson a **Save** (Mentés) gombra. Az Azure ad-ben fióktulajdonos kap egy e-mailt, és a egy hivatkozást, mielőtt aktívvá válik, győződjön meg arról, hogy fiókjuk követi.

>[!NOTE]
>Üzembe helyezi az Azure AD-felhasználói fiókokat, Intacct felhasználói fiók létrehozása eszközöket és Intacct által biztosított API-kat használhatja.
        
### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Intacct Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Intacct, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Intacct**.

    ![Egyszeri bejelentkezés konfigurálása](./media/intacct-tutorial/tutorial_intacct_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

Ha a hozzáférési panelen a Intacct csempére kattint, akkor kell lehet automatikusan bejelentkezett Intacct alkalmazását.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/intacct-tutorial/tutorial_general_01.png
[2]: ./media/intacct-tutorial/tutorial_general_02.png
[3]: ./media/intacct-tutorial/tutorial_general_03.png
[4]: ./media/intacct-tutorial/tutorial_general_04.png

[100]: ./media/intacct-tutorial/tutorial_general_100.png

[200]: ./media/intacct-tutorial/tutorial_general_200.png
[201]: ./media/intacct-tutorial/tutorial_general_201.png
[202]: ./media/intacct-tutorial/tutorial_general_202.png
[203]: ./media/intacct-tutorial/tutorial_general_203.png

