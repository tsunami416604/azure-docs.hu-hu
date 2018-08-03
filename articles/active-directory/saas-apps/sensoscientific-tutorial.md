---
title: 'Oktatóanyag: Azure Active Directory-integráció az SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeedes
ms.openlocfilehash: 139a40f339c2f403999f1c3b7fe65192d45c84fd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426921"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Oktatóanyag: Azure Active Directory-integráció rendszerrel SensoScientific vezeték nélküli hőmérséklet figyelése

Ebben az oktatóanyagban elsajátíthatja, hogyan SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer integrálása az Azure Active Directory (Azure AD).

SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá SensoScientific vezeték nélküli hőmérséklet Monitoring rendszer Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett SensoScientific vezeték nélküli hőmérséklet (egyszeri bejelentkezés) figyelési rendszerhez
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása SensoScientific vezeték nélküli hőmérséklet figyelési rendszerrel, a következő elemek van szükség:

- Az Azure AD-előfizetéshez
- Egy SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer hozzáadása a katalógusból
Az Azure AD-be SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer integráció konfigurálásához, hozzá kell SensoScientific vezeték nélküli hőmérséklet figyelése a System a galériából a felügyelt SaaS-alkalmazások listájára.

**SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_search.png)

1. Az eredmények panelen válassza ki a **SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés SensoScientific vezeték nélküli hőmérséklet figyelési rendszer egy "Britta Simon." nevű tesztelési felhasználó alapján

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó SensoScientific vezeték nélküli hőmérséklet figyelési rendszer mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó SensoScientific vezeték nélküli hőmérséklet figyelési rendszerben hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** SensoScientific vezeték nélküli hőmérséklet figyelési rendszerben.

Az Azure AD egyszeri bejelentkezés SensoScientific vezeték nélküli hőmérséklet figyelési rendszer tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer tesztfelhasználó létrehozása](#creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user)**  – van egy megfelelője a Britta Simon SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer alkalmazását az egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés SensoScientific vezeték nélküli hőmérséklet figyelési rendszerrel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon a a **SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer** alkalmazás integrációs oldalán kattintson **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_samlbase.png)

1. Az a **SensoScientific vezeték nélküli hőmérséklet figyelése a System tartományi és URL-címek** szakaszt, végrehajthatja a lépéseket, az alkalmazás már előre integrált, az Azure-ban van szükség:

    ![Egyszeri bejelentkezés konfigurálása](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_url.png)

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sensoscientific-tutorial/tutorial_general_400.png)

1. Az a **SensoScientific vezeték nélküli hőmérséklet figyelése a System Configuration** területén kattintson **SensoScientific vezeték nélküli hőmérséklet figyelési rendszer konfigurálása** megnyitásához **konfigurálása bejelentkezés** ablak. Másolás a **kijelentkezéses URL-címet, a SAML Entitásazonosító** és **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_configure.png) 

1. Jelentkezzen be rendszergazdaként SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer alkalmazását.

1. A felső navigációs menüben kattintson **konfigurációs** és goto **konfigurálása** alatt **az egyszeri bejelentkezést** a egyszeri bejelentkezést a beállításainak megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png) 

1. A **egyszeri bejelentkezést a beállítások** képernyőn tegye a következőket:
 
    a. Válassza ki **Kibocsátónévre** , Azure ad-ben.
    
    b. Illessze be a **SAML Entitásazonosító** amely kiállítójának URL-címe szövegmezőbe az Azure Portalról másolta.
    
    c. Illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** , amely az Azure Portalról történő egyszeri bejelentkezési szolgáltatás URL-címe szövegmezőbe másolta.

    d. Illessze be a **kijelentkezéses URL-cím** , amely az Azure Portalról másolt be egyszeri kijelentkezéses szolgáltatás URL-címe szövegmezőbe.

    e. Tallózással keresse meg a tanúsítványt, amelyre letöltötte az Azure Portalról, és töltse fel ide.
    
    f. Kattintson a **Save** (Mentés) gombra.
  
> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció](https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sensoscientific-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sensoscientific-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sensoscientific-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sensoscientific-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer jelentkezzen be, akkor ki kell építeni rendszerbe SensoScientific vezeték nélküli hőmérséklet figyelése. Együttműködve [SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer támogatási csapatának](https://www.sensoscientific.com/contact-us/) a felhasználók hozzáadása az SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer**.

    ![Egyszeri bejelentkezés konfigurálása](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen. Kattintson a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer csempére a hozzáférési panelen, meg fog lehet automatikusan bejelentkezett a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer alkalmazásba. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sensoscientific-tutorial/tutorial_general_01.png
[2]: ./media/sensoscientific-tutorial/tutorial_general_02.png
[3]: ./media/sensoscientific-tutorial/tutorial_general_03.png
[4]: ./media/sensoscientific-tutorial/tutorial_general_04.png

[100]: ./media/sensoscientific-tutorial/tutorial_general_100.png

[200]: ./media/sensoscientific-tutorial/tutorial_general_200.png
[201]: ./media/sensoscientific-tutorial/tutorial_general_201.png
[202]: ./media/sensoscientific-tutorial/tutorial_general_202.png
[203]: ./media/sensoscientific-tutorial/tutorial_general_203.png

