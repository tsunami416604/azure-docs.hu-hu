---
title: 'Oktatóanyag: Azure Active Directory-integráció az Capriza Platform |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Capriza Platform és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 48d92247-f00a-47b9-8d4e-137028d9e200
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 95f8d4561855e31f6bbcfa03a2db7931265c3524
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048164"
---
# <a name="tutorial-azure-active-directory-integration-with-capriza-platform"></a>Oktatóanyag: Azure Active Directory-integráció Capriza platformmal

Ebben az oktatóanyagban elsajátíthatja, hogyan Capriza Platform integrálása az Azure Active Directory (Azure AD).

Capriza Platform integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Capriza Platform az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Capriza platformra (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Capriza platformmal, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Capriza Platform egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Capriza Platform hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-capriza-platform-from-the-gallery"></a>Capriza Platform hozzáadása a katalógusból
Az Azure AD-be Capriza platform integráció konfigurálásához, hozzá kell Capriza Platform a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Capriza Platform hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **Capriza Platform**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/capriza-tutorial/tutorial_caprizaplatform_search.png)

5. Az eredmények panelen válassza ki a **Capriza Platform**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/capriza-tutorial/tutorial_caprizaplatform_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a egy "Britta Simon." nevű tesztelési felhasználó alapján Capriza platform az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Capriza platformon mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Capriza platformon hivatkozás kapcsolata kell létrehozni.

Capriza Platform, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Konfigurálása és az Azure AD egyszeri bejelentkezés Capriza platformmal való teszteléséhez hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Capriza Platform tesztfelhasználó létrehozása](#creating-a-capriza-platform-test-user)**  – egy megfelelője a Britta Simon Capriza Platform, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Capriza Platform-alkalmazásból az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Capriza platformmal, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Capriza Platform** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/capriza-tutorial/tutorial_caprizaplatform_samlbase.png)

3. Az a **Capriza Platform tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/capriza-tutorial/tutorial_caprizaplatform_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.capriza.com/<tenantid>`

    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Capriza Platform ügyfél-támogatási csapatának](mailTo:support@capriza.com) lekérni ezt az értéket. 

4. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/capriza-tutorial/tutorial_caprizaplatform_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/capriza-tutorial/tutorial_general_400.png)

6. Az a **Capriza Platformregisztrációt** területén kattintson **Capriza Platform konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/capriza-tutorial/tutorial_caprizaplatform_configure.png) 

7. Az egyszeri bejelentkezés konfigurálása **Capriza Platform** oldalon kell küldenie a letöltött **tanúsítvány**, **kijelentkezéses URL-cím**, **SAML Entitásazonosító**és **SAML egyszeri bejelentkezési szolgáltatás URL-cím** való [Capriza Platform támogatási csapatának](mailTo:support@capriza.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/capriza-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/capriza-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/capriza-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/capriza-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-capriza-platform-test-user"></a>Capriza Platform tesztfelhasználó létrehozása

Ez a szakasz célja Capriza Britta Simon nevű felhasználó létrehozásához. Capriza támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. **Győződjön meg arról, hogy a tartománynév van konfigurálva a Capriza felhasználóátadást készíthet elő. Ezt követően csak a just-in-time felhasználókiépítés fog működni.**

Nincs meg ebben a szakaszban a művelet elem. Egy új felhasználót hoz létre az Capriza elérésére, ha még nem létezik tett kísérlet során.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Capriza Platform Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Capriza Platform, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Capriza Platform**.

    ![Egyszeri bejelentkezés konfigurálása](./media/capriza-tutorial/tutorial_caprizaplatform_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Capriza Platform csempére kattint, meg kell lekérése automatikusan bejelentkezett az Capriza alkalmazáshoz. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/capriza-tutorial/tutorial_general_01.png
[2]: ./media/capriza-tutorial/tutorial_general_02.png
[3]: ./media/capriza-tutorial/tutorial_general_03.png
[4]: ./media/capriza-tutorial/tutorial_general_04.png

[100]: ./media/capriza-tutorial/tutorial_general_100.png

[200]: ./media/capriza-tutorial/tutorial_general_200.png
[201]: ./media/capriza-tutorial/tutorial_general_201.png
[202]: ./media/capriza-tutorial/tutorial_general_202.png
[203]: ./media/capriza-tutorial/tutorial_general_203.png

