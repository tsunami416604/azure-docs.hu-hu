---
title: 'Oktatóanyag: Azure Active Directory-integráció fája CX Suite-tal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés fája CX Suite és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5f4b7830-6186-4d17-b77b-504d4192bfde
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: jeedes
ms.openlocfilehash: 4c126b3bf08174886b3668cd54c76b72d113ba76
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047110"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Oktatóanyag: Azure Active Directory-integráció az CX Suite fája

Ebben az oktatóanyagban elsajátíthatja, hogyan fája CX Suite integrálása az Azure Active Directory (Azure AD).

Fája CX Suite integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a CX Suite bizonyulhat az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett CX Suite fája (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a CX Suite bizonyulhat, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A CX Suite fája egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Fája CX Suite hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>Fája CX Suite hozzáadása a katalógusból
Az Azure AD-be fája CX Suite integrációjának konfigurálásához hozzá kell CX Suite bizonyulhat a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a CX Suite bizonyulhat a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **CX Suite bizonyulhat**, jelölje be **fája CX Suite** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában CX Suite fája](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés fája CX Suite egy "Britta Simon" nevű tesztelési felhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó CX Suite bizonyulhat a mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó fája CX Suite hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az fája CX Suite tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy fája CX Suite tesztfelhasználót](#create-a-foresee-cx-suite-test-user)**  – van egy megfelelője a Britta Simon fája CX csomag, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és fája CX csomag alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés CX Suite bizonyulhat, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **fája CX Suite** alkalmazás integrációs oldalán kattintson **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_samlbase.png)

3. Az a **fája CX Suite tartomány és URL-címek** szakaszt, ha rendelkezik **szolgáltató metaadatait tartalmazó fájl**, hajtsa végre az alábbi lépéseket:

    ![Fája CX Suite tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/foreseecxsuite-tutorial/upload.png)

    a. Kattintson a **metaadatfájl feltöltése**.

    ![Fája CX Suite tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/foreseecxsuite-tutorial/tutorial_foreseen_uploadconfig.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    c. Feltöltés sikeres befejezése után **szolgáltató metaadatait tartalmazó fájl** a **azonosító** érték get automatikusan kitölti a **fája CX Suite tartomány és URL-címek** szakasz Szövegmező alább látható módon:

    ![Fája CX Suite tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/foreseecxsuite-tutorial/urlupload.png)

4. Ha nem rendelkezik **szolgáltató metaadatait tartalmazó fájl**, hajtsa végre az alábbi lépéseket:

    ![Fája CX Suite tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím: `https://cxsuite.foresee.com/`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.okta.com/saml2/service-provider/<UniqueID>`

    > [!NOTE]
    > Az azonosító értéke nem valódi. Ez az érték frissítse a tényleges azonosítója. Kapcsolattartó [fája CX Suite ügyfél-támogatási csapatának](mailto:support@foresee.com) lekérni ezt az értéket.

5. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_certificate.png)

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/foreseecxsuite-tutorial/tutorial_general_400.png)

7. Az egyszeri bejelentkezés konfigurálása **fája CX Suite** oldalon kell küldenie a letöltött **metaadatainak XML** való [fája CX Suite támogatási csapatával](mailto:support@foresee.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/foreseecxsuite-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/foreseecxsuite-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/foreseecxsuite-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/foreseecxsuite-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-foresee-cx-suite-test-user"></a>Fája CX Suite tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű CX Suite bizonyulhat a felhasználó hoz létre. Együttműködve [fája CX Suite támogatási csapatának](mailto:support@foresee.com) hozzáadása a felhasználók és a tartományt, amely a CX Suite fája platform az engedélyezéshez van szükség. Ha a tartomány a csapata által hozzáadott, a CX Suite fája platform automatikusan kiépítve felhasználók. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon CX Suite bizonyulhat a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel CX Suite bizonyulhat, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **fája CX Suite**.

    ![A CX Suite fája hivatkozásra az alkalmazások listáját](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_app.png)

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a CX Suite fája csempére kattint, kell lekérése automatikusan bejelentkezett a CX Suite fája alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/foreseecxsuite-tutorial/tutorial_general_01.png
[2]: ./media/foreseecxsuite-tutorial/tutorial_general_02.png
[3]: ./media/foreseecxsuite-tutorial/tutorial_general_03.png
[4]: ./media/foreseecxsuite-tutorial/tutorial_general_04.png

[100]: ./media/foreseecxsuite-tutorial/tutorial_general_100.png

[200]: ./media/foreseecxsuite-tutorial/tutorial_general_200.png
[201]: ./media/foreseecxsuite-tutorial/tutorial_general_201.png
[202]: ./media/foreseecxsuite-tutorial/tutorial_general_202.png
[203]: ./media/foreseecxsuite-tutorial/tutorial_general_203.png

