---
title: 'Oktatóanyag: Azure Active Directory-integráció az FM:Systems |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és FM:Systems között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f78c58c5-6e98-458b-8991-78624a245665
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: 4bb0d3cd247862d7bb8d4b17e26c26f7cda48c95
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042434"
---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>Oktatóanyag: Azure Active Directory-integráció az FM:Systems

Ebben az oktatóanyagban elsajátíthatja, hogyan FM:Systems integrálása az Azure Active Directory (Azure AD).

FM:Systems integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá FM:Systems Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az Azure AD-fiókjukkal (egyszeri bejelentkezés) FM:Systems
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

FM:Systems az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy FM:Systems egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. FM:Systems hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-fmsystems-from-the-gallery"></a>FM:Systems hozzáadása a katalógusból
Az Azure AD integrálása a FM:Systems konfigurálásához hozzá kell FM:Systems a katalógusból a felügyelt SaaS-alkalmazások listájára.

**FM:Systems hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **FM:Systems**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/fm-systems-tutorial/tutorial_fmsystems_search.png)

5. Az eredmények panelen válassza ki a **FM:Systems**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/fm-systems-tutorial/tutorial_fmsystems_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés FM:Systems a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó FM:Systems mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó FM:Systems hivatkozás kapcsolata kell létrehozni.

FM:Systems, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az FM:Systems tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Egy FM:Systems tesztfelhasználó létrehozása](#creating-an-fmsystems-test-user)**  – egy megfelelője a Britta Simon a felhasználó Azure ad-ben reprezentációja kapcsolódó FM:Systems rendelkeznie.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és FM:Systems alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés FM:Systems, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **FM:Systems** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/fm-systems-tutorial/tutorial_fmsystems_samlbase.png)

3. Az a **FM:Systems tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/fm-systems-tutorial/tutorial_fmsystems_url.png)

    Az a **válasz URL-cím** szövegmezőbe írja be a FM:Systems **válasz URL-cím**, írja be az URL-cím a következő minta használatával: `https://<companyname>.fmshosted.com/fminteract/ConsumerService2.aspx`

    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges válasz URL-cím. Kapcsolattartó [FM:Systems támogatási csapatának](https://fmsystems.com/ask-us/) lekérni ezt az értéket.
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/fm-systems-tutorial/tutorial_fmsystems_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/fm-systems-tutorial/tutorial_general_400.png)

6. Az egyszeri bejelentkezés konfigurálása **FM:Systems** oldalon kell küldenie a letöltött **metaadatainak XML** való [FM:Systems támogatási csapatának](https://fmsystems.com/ask-us/). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon. Értesítést fog kapni, amikor az egyszeri bejelentkezés az előfizetés engedélyezve van.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/fm-systems-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/fm-systems-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/fm-systems-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/fm-systems-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-fmsystems-test-user"></a>Egy FM:Systems tesztfelhasználó létrehozása

1. Egy böngészőablakban jelentkezzen be a FM:Systems vállalati hely rendszergazdaként.

2. Lépjen a **rendszerfelügyelet \> biztonság kezeléséhez \> felhasználók \> Felhasználólista**.
   
    ![Rendszerfelügyelet](./media/fm-systems-tutorial/ic795905.png "rendszerfelügyelet")

3. Kattintson a **új felhasználó létrehozása**.
   
    ![Új felhasználó létrehozása](./media/fm-systems-tutorial/ic795906.png "új felhasználó létrehozása")

4. Az a **Create User** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Felhasználó létrehozása](./media/fm-systems-tutorial/ic795907.png "felhasználó létrehozása")
   
    a. Írja be a **felhasználónév**, a **jelszó**, **jelszó megerősítése**, **E-mail** és a **alkalmazott azonosítója** , egy érvényes Azure Active Directory-fiókot szeretné a kapcsolódó szövegmezőkben létrehozásához.
   
    b. Kattintson a **Tovább** gombra.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés FM:Systems való hozzáférést.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel FM:Systems, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **FM:Systems**.

    ![Egyszeri bejelentkezés konfigurálása](./media/fm-systems-tutorial/tutorial_fmsystems_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a FM:Systems csempére kattint, meg kell lekérése automatikusan bejelentkezett az FM:Systems alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fm-systems-tutorial/tutorial_general_01.png
[2]: ./media/fm-systems-tutorial/tutorial_general_02.png
[3]: ./media/fm-systems-tutorial/tutorial_general_03.png
[4]: ./media/fm-systems-tutorial/tutorial_general_04.png

[100]: ./media/fm-systems-tutorial/tutorial_general_100.png

[200]: ./media/fm-systems-tutorial/tutorial_general_200.png
[201]: ./media/fm-systems-tutorial/tutorial_general_201.png
[202]: ./media/fm-systems-tutorial/tutorial_general_202.png
[203]: ./media/fm-systems-tutorial/tutorial_general_203.png

