---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ThousandEyes |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és ThousandEyes között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43aa43d37099d69441a5dde47327e784d67fa9ac
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189446"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ThousandEyes

Ebben az oktatóanyagban elsajátíthatja, hogyan ThousandEyes integrálása az Azure Active Directory (Azure AD).

ThousandEyes integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá ThousandEyes Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett ThousandEyes (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

ThousandEyes az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy ThousandEyes egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség: [Próbaverziós ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. ThousandEyes hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-thousandeyes-from-the-gallery"></a>ThousandEyes hozzáadása a katalógusból
Az Azure AD integrálása a ThousandEyes konfigurálásához hozzá kell ThousandEyes a katalógusból a felügyelt SaaS-alkalmazások listájára.

**ThousandEyes hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **ThousandEyes**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/thousandeyes-tutorial/tutorial_thousandeyes_search.png)

1. Az eredmények panelen válassza ki a **ThousandEyes**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés ThousandEyes a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó ThousandEyes mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó ThousandEyes hivatkozás kapcsolata kell létrehozni.

ThousandEyes, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az ThousandEyes tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[ThousandEyes tesztfelhasználó létrehozása](#creating-a-thousandeyes-test-user)**  – egy megfelelője a Britta Simon ThousandEyes, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és ThousandEyes alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés ThousandEyes, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **ThousandEyes** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

1. Az a **ThousandEyes tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-cím: `https://app.thousandeyes.com/login/sso`

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/thousandeyes-tutorial/tutorial_thousandeyes_certificate.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/thousandeyes-tutorial/tutorial_general_400.png)

1. Az a **ThousandEyes konfigurációs** területén kattintson **konfigurálása ThousandEyes** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

1. Egy másik böngészőablakban, jelentkezzen be a **ThousandEyes** rendszergazdaként a vállalati webhely.

1. A felső menüben kattintson **beállítások**.

    ![Beállítások](./media/thousandeyes-tutorial/ic790066.png "beállításai")

1. Kattintson a **fiók**

    ![Fiók](./media/thousandeyes-tutorial/ic790067.png "fiók")

1. Kattintson a **biztonság és hitelesítés** fülre.

    ![A biztonság és hitelesítés](./media/thousandeyes-tutorial/ic790068.png "biztonság és hitelesítés")

1. Az a **beállítása egyszeri bejelentkezéshez** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés beállítása](./media/thousandeyes-tutorial/ic790069.png "egyszeri bejelentkezés beállítása")

    a. Válassza ki **egyszeri bejelentkezés engedélyezése**.

    b. A **bejelentkezési oldal URL-cím** szövegmezőjébe illessze be **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.

    c. A **kijelentkezési URL-címe** szövegmezőjébe illessze be **kijelentkezéses URL-cím**, amely az Azure Portalról másolt.

    d. **Identitásszolgáltató kibocsátója** szövegmezőjébe illessze be **SAML Entitásazonosító**, amely az Azure Portalról másolt.

    e. A **ellenőrző tanúsítvány**, kattintson a **fájl kiválasztása**, majd töltse fel a tanúsítványt, hogy letöltötte az Azure Portalról.

    f. Kattintson a **Save** (Mentés) gombra.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/thousandeyes-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/thousandeyes-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/thousandeyes-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/thousandeyes-tutorial/create_aaduser_04.png)

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-a-thousandeyes-test-user"></a>ThousandEyes tesztfelhasználó létrehozása

Ez a szakasz célja ThousandEyes Britta Simon nevű felhasználó létrehozásához. ThousandEyes támogatja a felhasználók automatikus átadása, amely alapértelmezés szerint van engedélyezve. További részleteket talál [Itt](thousandeyes-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

**Hozza létre a felhasználó manuálisan kell, ha hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a ThousandEyes vállalati hely rendszergazdaként.

1. Kattintson a **beállítások**.

    ![Beállítások](./media/thousandeyes-tutorial/IC790066.png "beállításai")

1. Kattintson a **fiók**.

    ![Fiók](./media/thousandeyes-tutorial/IC790067.png "fiók")

1. Kattintson a **fiókok és a felhasználók** fülre.

    ![Fiókok és a felhasználók](./media/thousandeyes-tutorial/IC790073.png "fiókok és a felhasználók")

1. Az a **hozzáadása felhasználók és fiókok** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Felhasználói fiókok hozzáadása](./media/thousandeyes-tutorial/IC790074.png "felhasználói fiókok hozzáadása")

    a. A **neve** szövegmezőbe írja be a nevet a felhasználó például **Britta Simon**.

    b. A **E-mail** szövegmezőbe írja be az e-mailt, felhasználó, például **brittasimon@contoso.com**.

    b. Kattintson a **új felhasználó hozzáadása a fiókhoz**.

    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos fog kapni egy e-mailt és a telepítőre mutató erősítse meg, és a fiók aktiválásához.

> [!NOTE]
> Bármely más ThousandEyes felhasználói fiók létrehozása eszközöket használhatja, vagy API-k által biztosított ThousandEyes üzembe helyezni az Azure Active Directory felhasználói fiókokat.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés ThousandEyes Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel ThousandEyes, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **ThousandEyes**.

    ![Egyszeri bejelentkezés konfigurálása](./media/thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a ThousandEyes csempére kattint, meg kell lekérése automatikusan bejelentkezett az ThousandEyes alkalmazáshoz.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](thousandeyes-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/thousandeyes-tutorial/tutorial_general_203.png
