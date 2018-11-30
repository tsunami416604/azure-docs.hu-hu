---
title: 'Oktatóanyag: Azure Active Directory-integráció az végtelen Campus |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a végtelen Campus között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 837d18a04a0cd22f29a5cbcb8b06b7e3f5fae255
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632806"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Oktatóanyag: Azure Active Directory-integráció az végtelen Campus

Ebben az oktatóanyagban elsajátíthatja, hogyan végtelen Campus integrálása az Azure Active Directory (Azure AD).

Végtelen Campus integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a végtelen Campus Azure AD-ben.
- A felhasználók automatikusan első bejelentkezett végtelen Campus (egyszeri bejelentkezés), engedélyezheti az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Végtelen Campus konfigurálni az Azure AD-integráció, a következőkre van szükség:

- Azure AD-előfizetés
- A végtelen Campus egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).
- Legalább kell lennie az Azure Active Directory-rendszergazda, a konfiguráció befejezéséhez.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Végtelen Campus hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-infinite-campus-from-the-gallery"></a>Végtelen Campus hozzáadása a katalógusból

Az Azure AD integrálása a végtelen Campus konfigurálásához hozzá kell végtelen Campus a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Végtelen Campus hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **végtelen Campus**, jelölje be **végtelen Campus** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában végtelen Campus](./media/infinitecampus-tutorial/tutorial_infinitecampus_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés végtelen Campus a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó végtelen Campus mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a végtelen Campus hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a végtelen Campus tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Egy végtelen telephelyi tesztfelhasználót létrehozása](#creating-a-infinite-campus-test-user)**  – egy megfelelője a Britta Simon végtelen Campus, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és végtelen Campus alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés végtelen Campus, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **végtelen Campus** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](common/tutorial_general_301.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](common/editconfigure.png)

5. Az a **alapszintű SAML-konfigurációja** szakaszt, ha rendelkezik **szolgáltató metaadatait tartalmazó fájl** (lépés **11.b**), hajtsa végre az alábbi lépéseket:

    a. Kattintson a **metaadatfájl feltöltése**.

    ![image](common/b9_saml.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![image](common/b9(1)_saml.png)

    c. A metaadatfájl sikeres feltöltését követően a **azonosító** és **válasz URL-cím** értékeket automatikusan az első **alapszintű SAML-konfigurációja** szövegmező szakaszban, ahogy az alábbi :

    ![image](./media/infinitecampus-tutorial/tutorial_infinitecampus_url.png)

    d. Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címet (a tartományt üzemeltető modell számától függ) a következő minta használatával: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    > [!NOTE]
    > Megjelenik a **szolgáltató metaadatait tartalmazó fájl** az oktatóanyag későbbi részében ismertetett végtelen Campus SSO szolgáltató konfigurációja oldalon.

6. Ha nem rendelkezik **szolgáltató metaadatait tartalmazó fájl**, hajtsa végre az alábbi lépéseket:

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Végtelen Campus tartomány és URL-címek egyszeri bejelentkezési adatait](./media/infinitecampus-tutorial/tutorial_infinitecampus_url1.png)

6. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás **ikon** másolása **alkalmazás összevonási metaadatainak URL-címe**  , és illessze be a Jegyzettömbbe.

    ![A tanúsítvány letöltési hivatkozás](./media/infinitecampus-tutorial/tutorial_infinitecampus_certificate.png) 

7. Az a **végtelen Campus beállítása** területén érvényesíthető a fel- vagy az Azure-metaadatokkal fájl/URL-cím használatával a következő értékeket használja.

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

    ![Végtelen Campus konfiguráció](common/configuresection.png)

8. Egy másik böngészőablakban, jelentkezzen be a végtelen Campus egy biztonsági-rendszergazdaként.

9. Kattintson a bal oldali menü, **rendszerfelügyelet**.

    ![A rendszergazda](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

10. Navigáljon a **felhasználói biztonság** > **SAML felügyeleti** > **SSO szolgáltató konfigurációja**.

    ![Az saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

11. Az a **SSO szolgáltató konfigurációja** lapon, a következő lépésekkel:

    ![Az egyszeri bejelentkezés](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Válassza ki **engedélyezése SAML egyszeri bejelentkezési**.

    b. Kattintson a **Service Provider metaadatok** kattintva mentheti a **szolgáltató metaadatait tartalmazó fájl** a számítógépen, és töltse fel a **alapszintű SAML-konfigurációja** automatikus szakasz polulate a **azonosító** és **válasz URL-cím** értékeket az Azure Portalon (lásd 5. lépés).

    c. Az a **válassza ki a Identity Provider (IDP) kiszolgáló adatainak beolvasása** szakaszban válassza ki **metaadatok URL-címe** , és illessze be a **alkalmazás összevonási metaadatainak URL-címe** be a szövegmezőbe, és Kattintson a **szinkronizálási**.

    d. Kattintás után **szinkronizálási** az értékek első automatikusan kitölti a **SSO szolgáltató konfigurációja** lapot.

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az, hogy hozzon létre egy _egyetlen_ tesztfelhasználó Britta Simon nevű az Azure Portalon.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_02.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="creating-a-infinite-campus-test-user"></a>Végtelen Campus tesztfelhasználó létrehozása

Végtelen Campus rendelkezik egy ingyenes demográfiai architektúra. Vegye fel a kapcsolatot [végtelen Campus támogatási csapatának](mailto:sales@infinitecampus.com) a felhasználók hozzáadása az végtelen Campus platformon.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés végtelen Campus Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **végtelen Campus**.

    ![Egyszeri bejelentkezés konfigurálása](./media/infinitecampus-tutorial/tutorial_infinitecampus_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a végtelen Campus csempére kattint, meg kell lekérése automatikusan bejelentkezett a végtelen Campus alkalmazásba. Ha ugyanabból a böngészőből, Azure AD szolgáltatás felügyeletéről végtelen Camnpus alkalmazásba jelentkezik, győződjön meg arról, jelentkezzen be a tesztfelhasználó számára, az Azure AD-be. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
