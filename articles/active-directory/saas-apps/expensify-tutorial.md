---
title: 'Oktatóanyag: Azure Active Directory-integráció az Expensify |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Expensify között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: jeedes
ms.openlocfilehash: c9ee0af3cbf2c1aa7b24d2f4cf5fba9d664bc087
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248040"
---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>Oktatóanyag: Azure Active Directory-integráció az Expensify

Ebben az oktatóanyagban elsajátíthatja, hogyan Expensify integrálása az Azure Active Directory (Azure AD).

Expensify integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Expensify Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Expensify (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Expensify az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Expensify egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Expensify hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-expensify-from-the-gallery"></a>Expensify hozzáadása a katalógusból

Az Azure AD integrálása a Expensify konfigurálásához hozzá kell Expensify a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Expensify hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![image](./media/expensify-tutorial/selectazuread.png)

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![image](./media/expensify-tutorial/a_select_app.png)
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![image](./media/expensify-tutorial/a_new_app.png)

4. A Keresés mezőbe írja be a **Expensify**válassza **Expensify** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![image](./media/expensify-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Expensify a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Expensify mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Expensify hivatkozás kapcsolata kell létrehozni.

Expensify, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Expensify tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Expensify tesztfelhasználót](#create-an-expensify-test-user)**  – egy megfelelője a Britta Simon Expensify, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Expensify alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Expensify, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com/), az a **Expensify** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![image](./media/expensify-tutorial/b1_b2_select_sso.png)

2. Kattintson a **módosítása egyszeri bejelentkezési mód** felett válassza ki a képernyő a **SAML** mód.

      ![image](./media/expensify-tutorial/b1_b2_saml_ssso.png)

3. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![image](./media/expensify-tutorial/b1_b2_saml_sso.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![image](./media/expensify-tutorial/b1-domains_and_urlsedit.png)

5. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    a. Az a **bejelentkezési URL-** szövegmezőbe írjon be egy URL-cím: `https://www.expensify.com/authentication/saml/login`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://www.<companyname>.expensify.com`

    ![image](./media/expensify-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Cserélje le a <companyname> szakaszában az azonosító URL-CÍMÉT a munkahelyi tartományhoz. A példa `https://contoso.expensify.com` felett. A Expensify, ez a annak a tartománynak a nevét ahogyan **beállítások > tartomány-ellenőrzési**.

    ![Tartományadatokat expensify](./media/expensify-tutorial/tutorial_expensify_domain.png)

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** megfelelően a megfelelő tanúsítvány letöltéséhez a követelmény, és mentse a számítógépre.

    ![image](./media/expensify-tutorial/certificatebase64.png)

7. Ahhoz, hogy az egyszeri bejelentkezés az Expensify, akkor először engedélyeznie kell **tartomány-ellenőrzési** az alkalmazásban. Tartomány-ellenőrzési engedélyezheti az alkalmazást a felsorolt lépéseket [Itt](http://help.expensify.com/domain-control). Ha további támogatási együttműködve [Expensify ügyfél-támogatási csapatának](mailto:help@expensify.com). Ha már rendelkezik a tartomány-ellenőrzési engedélyezve van, kövesse az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/expensify-tutorial/tutorial_expensify_51.png)
    
    a. Jelentkezzen be az Expensify alkalmazás.
    
    b. Kattintson a bal oldali panel **beállítások** , és keresse meg **SAML**.
    
    c. Váltás a **SAML bejelentkezési** lehetőséget igény **engedélyezve**.
    
    d. Nyissa meg a letöltött összevonási metaadatok az Azure ad-ből a Jegyzettömbben, másolja a tartalmat, és illessze be azt a **Identity Provider metaadatok** szövegmezőbe.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![image](./media/expensify-tutorial/d_users_and_groups.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![image](./media/expensify-tutorial/d_adduser.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![image](./media/expensify-tutorial/d_userproperties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-an-expensify-test-user"></a>Hozzon létre egy Expensify tesztfelhasználó számára

Ebben a szakaszban egy felhasználói Britta Simon nevű Expensify hoz létre. Együttműködve [Expensify ügyfél-támogatási csapatának](mailto:help@expensify.com) a felhasználók hozzáadása az Expensify platformon.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Expensify Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![image](./media/expensify-tutorial/d_all_applications.png)

2. Az alkalmazások listájában jelölje ki a **Expensify**.

    ![image](./media/expensify-tutorial/d_all_proapplications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![image](./media/expensify-tutorial/d_leftpaneusers.png)

4. Válassza ki a **Hozzáadás** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![image](./media/expensify-tutorial/d_assign_user.png)

4. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

5. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Expensify csempére kattint, meg kell lekérése automatikusan bejelentkezett az Expensify alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)




