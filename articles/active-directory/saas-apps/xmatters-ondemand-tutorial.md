---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az xMatters OnDemand |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és xMatters OnDemand között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 5eab595c12fda25768a3bf8a04650870e1c4f57e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153542"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Oktatóanyag: Az xMatters OnDemand az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan xMatters OnDemand integrálása az Azure Active Directory (Azure AD).

XMatters OnDemand integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá xMatters OnDemand Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett xMatters OnDemand (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az xMatters OnDemand, a következőkre van szükség:

- Azure AD-előfizetés
- Egy OnDemand egyszeri bejelentkezés xMatters előfizetés engedélyezve van.

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. XMatters OnDemand hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>XMatters OnDemand hozzáadása a katalógusból
Az Azure AD integrálása a xMatters OnDemand konfigurálásához hozzá kell xMatters OnDemand a katalógusból a felügyelt SaaS-alkalmazások listájára.

**XMatters OnDemand hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **xMatters OnDemand**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_search.png)

1. Az eredmények panelen válassza ki a **xMatters OnDemand**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az OnDemand alapján "Britta Simon" nevű tesztfelhasználó xMatters.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, milyen a partner felhasználó xMatters OnDemand, hogy egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó xMatters hivatkozás kapcsolata OnDemand kell létrehozni.

XMatters OnDemand, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az xMatters OnDemand tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[XMatters OnDemand tesztfelhasználó létrehozása](#creating-a-xmatters-ondemand-test-user)**  – egy megfelelője a Britta Simon xMatters OnDemand, amely kapcsolódik az Azure AD felhasználói ábrázolása a rendelkeznie.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a xMatters OnDemand-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés xMatters OnDemand, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **xMatters OnDemand** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_samlbase.png)

1. Az a **xMatters OnDemand tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_url.png)
    
    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [xMatters OnDemand támogatási csoportjának](https://www.xmatters.com/company/contact-us/) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse helyben a tanúsítványfájlt, **c:\\XMatters OnDemand.cer**.

    ![Egyszeri bejelentkezés konfigurálása](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_certificate.png)

    > [!IMPORTANT]
    > Kell továbbítani a tanúsítványt a [xMatters OnDemand támogatási csoportjának](https://www.xmatters.com/company/contact-us/). A tanúsítványt kell feltölteni a xMatters támogatási csapat is az egyszeri bejelentkezési konfigurációjának véglegesítése előtt. 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/xmatters-ondemand-tutorial/tutorial_general_400.png)

1. Az a **xMatters OnDemand konfigurációs** területén kattintson **xMatters OnDemand konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a XMatters OnDemand vállalati hely rendszergazdaként.

1. A felső eszköztáron kattintson **rendszergazdai**, és kattintson a **cég adatait** a a bal oldali navigációs sávon.

    ![Rendszergazdai](./media/xmatters-ondemand-tutorial/IC776795.png "rendszergazda")

1. Az a **SAML-konfigurációja** lapon, a következő lépésekkel:

    ![SAML-konfigurációja](./media/xmatters-ondemand-tutorial/IC776796.png "SAML-konfigurációja")

    a. Válassza ki **SAML engedélyezése**.

    b. Az a **identitás Szolgáltatóazonosító** szövegmezőjébe illessze be **SAML Entitásazonosító** az Azure Portalról másolt érték.

    c. Az a **egyszeri bejelentkezési URL-Címként** szövegmezőjébe illessze be **SAML egyszeri bejelentkezési szolgáltatás URL-cím** az Azure Portalról másolt érték.

    d. Az a **egyszeri kijelentkezési URL-cím** szövegmezőjébe illessze be **kijelentkezéses URL-cím**, amely az Azure Portalról másolt.

    e. A vállalat ismertető lapon, a lap tetején kattintson **módosítások mentése**.

    ![A vállalati részletei](./media/xmatters-ondemand-tutorial/IC776797.png "vállalati részletei")

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/xmatters-ondemand-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/xmatters-ondemand-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/xmatters-ondemand-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/xmatters-ondemand-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-a-xmatters-ondemand-test-user"></a>XMatters OnDemand tesztfelhasználó létrehozása

Ez a szakasz célja xMatters OnDemand Britta Simon nevű felhasználó létrehozásához.

**Hozza létre a felhasználó manuálisan kell, ha hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **XMatters OnDemand** bérlő.

1.  Kattintson a **felhasználók** fülre, majd kattintson **felhasználó hozzáadása**.

    ![Felhasználók](./media/xmatters-ondemand-tutorial/IC781048.png "felhasználók")

1. Az a **felhasználó hozzáadása** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Felhasználó hozzáadása](./media/xmatters-ondemand-tutorial/IC781049.png "felhasználó hozzáadása")

    a. Válassza ki **aktív**.

    b. Az a **Felhasználóazonosító** szövegmező, például a felhasználó a felhasználói azonosító típusa Brittasimon@contoso.com.

    c. Az a **Utónév** szövegmezőbe írja be keresztnevét Britta például a felhasználó.

    d. Az a **Vezetéknév** szövegmezőbe írja be a felhasználó például Simon vezetékneve.

    e. Az a **hely** szövegmezőben adjon meg egy érvényes Azure érvényes hely AD-fiókot kíván üzembe helyezni.

    f. Kattintson a **Save** (Mentés) gombra.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés xMatters OnDemand való hozzáférést.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel xMatters OnDemand, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **xMatters OnDemand**.

    ![Egyszeri bejelentkezés konfigurálása](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint a xMatters OnDemand csempe a hozzáférési panelen, kell lekérése automatikusan bejelentkezett, a xMatters OnDemand-alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/xmatters-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/xmatters-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/xmatters-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/xmatters-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/xmatters-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/xmatters-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/xmatters-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/xmatters-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/xmatters-ondemand-tutorial/tutorial_general_203.png
