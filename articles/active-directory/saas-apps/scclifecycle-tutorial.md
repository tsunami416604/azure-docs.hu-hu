---
title: 'Oktatóanyag: Azure Active Directory-integráció SCC életciklusával |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és SCC életciklus között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 1d7ce538ed6ae76f9392433eb88929e375f9d0cb
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045923"
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Oktatóanyag: Azure Active Directory-integráció az SCC életciklusa

Ebben az oktatóanyagban elsajátíthatja, hogyan SCC életciklus integrálása az Azure Active Directory (Azure AD).

SCC életciklus integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá SCC életciklus Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a SCC életciklus (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása SCC életciklusával, a következő elemek szükségesek:

- Az Azure AD-előfizetéshez
- Egy SCC életciklus egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség: [próba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. SCC életciklus hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-scc-lifecycle-from-the-gallery"></a>SCC életciklus hozzáadása a katalógusból
SCC életciklus integrálása az Azure AD beállítása, hozzá kell SCC életciklus a galériából a felügyelt SaaS-alkalmazások listájára.

**SCC életciklus hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **SCC életciklus**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/scclifecycle-tutorial/tutorial_scclifecycle_search.png)

5. Az eredmények panelen válassza ki a **SCC életciklus**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/scclifecycle-tutorial/tutorial_scclifecycle_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

Ebben a szakaszban konfigurálja, és a egy "Britta Simon." nevű tesztelési felhasználó alapján SCC életciklusával az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó SCC életciklusának mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó SCC életciklusának hivatkozás kapcsolatát kell létrehozni.

SCC életciklusát, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés SCC életciklusával tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Egy SCC életciklus tesztfelhasználó létrehozása](#creating-an-scc-lifecycle-test-user)**  - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása SCC életciklusának.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és SCC életciklus alkalmazását az egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés SCC életciklusával, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **SCC életciklus** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/scclifecycle-tutorial/tutorial_scclifecycle_samlbase.png)

3. Az a **SCC életciklus tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/scclifecycle-tutorial/tutorial_scclifecycle_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<sub-domain>.scc.com/ic7/welcome/customer/PICTtest.aspx`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|--|
    | `https://bs1.scc.com/<entity>`|
    | `https://lifecycle.scc.com/<entity>`|
    
    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [SCC életciklus ügyfél-támogatási csapatának](mailto:lifecycle.support@scc.com) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/scclifecycle-tutorial/tutorial_scclifecycle_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/scclifecycle-tutorial/tutorial_general_400.png)

6. Az egyszeri bejelentkezés konfigurálása **SCC életciklus** oldalon kell küldenie a letöltött **metaadatainak XML** való [SCC életciklus támogatási csapatának](mailto:lifecycle.support@scc.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

  >[!NOTE]
  >Egyszeri bejelentkezés engedélyezni kell a SCC életciklus támogatási csapat rendelkezik.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/scclifecycle-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/scclifecycle-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/scclifecycle-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/scclifecycle-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-scc-lifecycle-test-user"></a>Egy SCC életciklus tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók SCC életciklus-ba való bejelentkezéshez, akkor ki kell építeni SCC életciklus be. Nincs művelet elem SCC életciklus való konfigurálását.

Ha egy hozzárendelt felhasználó megpróbálja SCC életciklus-ba való bejelentkezéshez, SCC életciklus fiók automatikusan létrejön, ha szükséges.

> [!NOTE]
> Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a egy hivatkozást, mielőtt aktívvá válik, győződjön meg arról, hogy fiókjuk követi.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SCC életciklus Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel SCC életciklusát, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, majd a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **összes alkalmazáshoz.**

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **SCC életciklus**.

    ![Egyszeri bejelentkezés konfigurálása](./media/scclifecycle-tutorial/tutorial_scclifecycle_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SCC életciklus csempére kattint, meg kell lekérése automatikusan bejelentkezett SCC életciklus-alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/scclifecycle-tutorial/tutorial_general_01.png
[2]: ./media/scclifecycle-tutorial/tutorial_general_02.png
[3]: ./media/scclifecycle-tutorial/tutorial_general_03.png
[4]: ./media/scclifecycle-tutorial/tutorial_general_04.png

[100]: ./media/scclifecycle-tutorial/tutorial_general_100.png

[200]: ./media/scclifecycle-tutorial/tutorial_general_200.png
[201]: ./media/scclifecycle-tutorial/tutorial_general_201.png
[202]: ./media/scclifecycle-tutorial/tutorial_general_202.png
[203]: ./media/scclifecycle-tutorial/tutorial_general_203.png

