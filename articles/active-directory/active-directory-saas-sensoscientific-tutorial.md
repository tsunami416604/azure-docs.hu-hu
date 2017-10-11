---
title: "Oktatóanyag: Azure Active Directoryval integrált SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeedes
ms.openlocfilehash: fa6242cf7f9559ca394ffde2e5e734cb935b03dc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Oktatóanyag: Azure Active Directory-integráció rendszerrel SensoScientific vezeték nélküli hőmérséklet figyelése

Ebben az oktatóanyagban elsajátíthatja SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer integrálása az Azure Active Directory (Azure AD).

SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a SensoScientific vezeték nélküli hőmérséklet figyelési rendszerhez hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett SensoScientific vezeték nélküli hőmérséklet (egyszeri bejelentkezés) figyelési rendszerhez
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Rendszerrel SensoScientific vezeték nélküli hőmérséklet figyelése az Azure AD-integrációs konfigurálásához a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>A gyűjteményből SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer hozzáadása
Az Azure AD integrálása SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer konfigurálásához kell hozzáadnia SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_search.png)

5. Az eredmények panelen válassza ki a **SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD az egyszeri bejelentkezés rendszerrel SensoScientific vezeték nélküli hőmérséklet figyelés "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó SensoScientific vezeték nélküli hőmérséklet figyelési rendszerben egy felhasználói Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó SensoScientific vezeték nélküli hőmérséklet figyelési rendszerben közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** SensoScientific vezeték nélküli hőmérséklet figyelési rendszerben.

Az Azure AD az egyszeri bejelentkezés rendszerrel SensoScientific vezeték nélküli hőmérséklet figyelési tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer tesztfelhasználó létrehozása](#creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user)**  - való egy megfelelője a Britta Simon SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a SensoScientific vezeték nélküli hőmérséklet figyelési Rendszeralkalmazásban egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD az egyszeri bejelentkezés SensoScientific vezeték nélküli hőmérséklet figyelési rendszerrel, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_samlbase.png)

3. Az a **SensoScientific vezeték nélküli hőmérséklet figyelési rendszer tartomány és az URL-címek** szakaszban, nem szükséges, az alkalmazás már előre integrált Azure-ral, hajtsa végre a lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_url.png)

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_400.png)

6. A a **SensoScientific vezeték nélküli hőmérséklet figyelési rendszerkonfiguráció** kattintson **SensoScientific vezeték nélküli hőmérséklet figyelési rendszer konfigurálása** megnyitásához **konfigurálása bejelentkezés** ablak. Másolás a **Sign-Out URL, SAML Entitásazonosító** és **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_configure.png) 

7. Jelentkezzen be rendszergazdaként a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer alkalmazás.

8. Kattintson a navigációs menü felső **konfigurációs** és goto **konfigurálása** alatt **egyszeri bejelentkezés** az egyszeri bejelentkezési a beállításainak megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png) 

9. A **egyetlen bejelentkezést a beállítások** űrlap hajtsa végre a következő lépéseket:
 
    a. Válassza ki **Kibocsátónév** , az Azure AD.
    
    b. Beillesztés a **SAML Entitásazonosító** amely kibocsátó URL-cím szövegmezőbe az Azure-portálon másolta.
    
    c. Beillesztés a **SAML-alapú egyszeri bejelentkezési URL-címe** , amely az egyszeri bejelentkezési URL-címe szövegmező másolta az Azure portálról.

    d. Beillesztés a **Sign-Out URL-cím** , amely egyetlen Sign-Out szolgáltatás URL-cím szövegmezőbe az Azure-portálon másolta.

    e. Keresse meg a tanúsítványt, amely az Azure-portálról letöltött, és töltse fel itt.
    
    f. Kattintson a **Save** (Mentés) gombra.
  
> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció](https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer bejelentkezni, akkor ki kell építenie SensoScientific vezeték nélküli hőmérséklet figyelési rendszerrel. Együttműködve [SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer támogatási csoport](https://www.sensoscientific.com/contact-us/) a felhasználók hozzáadása a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése. Kattintson a hozzáférési panelen SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer csempére, akkor lesz lehet automatikusan bejelentkezett az SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer alkalmazására. A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_203.png

