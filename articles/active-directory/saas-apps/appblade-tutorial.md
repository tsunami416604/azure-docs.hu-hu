---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező AppBlade |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és AppBlade között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 3360d7aa-6518-4f99-88bd-b7f7258183e8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 119647c4aeabc8f4a6dd3a1933fcfafe6b6cf5c2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813321"
---
# <a name="tutorial-azure-active-directory-integration-with-appblade"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező AppBlade

Ebben az oktatóanyagban elsajátíthatja, hogyan AppBlade integrálása az Azure Active Directory (Azure AD).

AppBlade integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá AppBlade Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett AppBlade (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

AppBlade az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy AppBlade egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. AppBlade hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-appblade-from-the-gallery"></a>AppBlade hozzáadása a katalógusból
Az Azure AD integrálása a AppBlade konfigurálásához hozzá kell AppBlade a katalógusból a felügyelt SaaS-alkalmazások listájára.

**AppBlade hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **AppBlade**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/appblade-tutorial/tutorial_appblade_search.png)

5. Az eredmények panelen válassza ki a **AppBlade**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/appblade-tutorial/tutorial_appblade_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó AppBlade az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó AppBlade mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó AppBlade hivatkozás kapcsolata kell létrehozni.

AppBlade, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az AppBlade tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Egy AppBlade tesztfelhasználó létrehozása](#creating-an-appblade-test-user)**  – egy megfelelője a Britta Simon AppBlade, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és AppBlade alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés AppBlade, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **AppBlade** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/appblade-tutorial/tutorial_appblade_samlbase.png)

3. Az a **AppBlade tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/appblade-tutorial/tutorial_appblade_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.appblade.com/saml/<tenantid>`

    > [!NOTE] 
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [AppBlade ügyfél-támogatási csapatának](mailto:support@appblade.com) a gépkulcsengedélyek értékének. 
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/appblade-tutorial/tutorial_appblade_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/appblade-tutorial/tutorial_general_400.png)

6. Az egyszeri bejelentkezés konfigurálása **AppBlade** oldalon kell küldenie a letöltött **metaadatainak XML** való [AppBlade támogatási csapatának](mailto:support@appblade.com). Is, kérje meg őket, konfigurálhatja a **SSO kiállítójának URL-címe** , `https://appblade.com/saml`. Ez a beállítás egyszeri bejelentkezésre működéséhez szükséges.


> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/appblade-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/appblade-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/appblade-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/appblade-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-appblade-test-user"></a>Egy AppBlade tesztfelhasználó létrehozása

Ez a szakasz célja AppBlade Britta Simon nevű felhasználó létrehozásához. AppBlade támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. **Győződjön meg arról, hogy a tartománynév van konfigurálva a AppBlade felhasználóátadást készíthet elő. Követően, hogy csak a just-in-time-felhasználó kiépítési működik.**

Ha a felhasználó ezután a felhasználó automatikusan csatlakozik a fiók a megadott engedélyszinttel rendelkező tagként azzal a tartománnyal, a fiók AppBlade által konfigurált végződésű e-mail-címmel rendelkezik, amely az egyik "Alapszintű" (egy alapszintű felhasználó csak telepíthető alkalmazásokat) , "Csapattag" (a felhasználó új verziók feltöltheti és projektek kezelése), vagy a "Rendszergazda" (a fiók teljes körű rendszergazdai jogosultságokkal). Ehhez általában egy válassza az alapszintű, és előreléphet a felhasználók manuálisan-rendszergazdai bejelentkezési nevét (AppBlade kell előre konfigurálhatja vagy egy rendszergazda e-mail-alapú bejelentkezést, vagy egy felhasználó elősegítik a vásárló nevében a bejelentkezés után).

Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az AppBlade elérésére, ha még nem létezik tett kísérlet során. 

> [!NOTE]
> Hozzon létre egy felhasználót manuálisan kell, ha kapcsolódni kell a [AppBlade támogatási csapatának](mailto:support@appblade.com).

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés AppBlade Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel AppBlade, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **AppBlade**.

    ![Egyszeri bejelentkezés konfigurálása](./media/appblade-tutorial/tutorial_appblade_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.  
Ha a hozzáférési panelen a AppBlade csempére kattint, meg kell lekérése automatikusan bejelentkezett az AppBlade alkalmazáshoz. 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/appblade-tutorial/tutorial_general_01.png
[2]: ./media/appblade-tutorial/tutorial_general_02.png
[3]: ./media/appblade-tutorial/tutorial_general_03.png
[4]: ./media/appblade-tutorial/tutorial_general_04.png

[100]: ./media/appblade-tutorial/tutorial_general_100.png

[200]: ./media/appblade-tutorial/tutorial_general_200.png
[201]: ./media/appblade-tutorial/tutorial_general_201.png
[202]: ./media/appblade-tutorial/tutorial_general_202.png
[203]: ./media/appblade-tutorial/tutorial_general_203.png

