---
title: "Oktatóanyag: Azure Active Directory-integráció EthicsPoint Incident Management (EPIM) |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és EthicsPoint Incident Management (EPIM) között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 8cb31a4c-9309-469b-93ac-daf0d3c7a3e6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: b5ac3afd973b5765ba151e766754934b49ac0e0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ethicspoint-incident-management-epim"></a>Oktatóanyag: Azure Active Directory-integráció EthicsPoint Incident Management (EPIM)

Ebben az oktatóanyagban elsajátíthatja EthicsPoint Incident Management (EPIM) integrálása az Azure Active Directory (Azure AD).

EthicsPoint Incident Management (EPIM) integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja az Azure AD, aki hozzáfér a EthicsPoint Incident Management (EPIM)
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett a EthicsPoint Incident Management (EPIM) (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása EthicsPoint Incident Management (EPIM), a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy EthicsPoint Incident Management (EPIM) egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből EthicsPoint Incident Management (EPIM) hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-ethicspoint-incident-management-epim-from-the-gallery"></a>A gyűjteményből EthicsPoint Incident Management (EPIM) hozzáadása
Az Azure AD integrálása EthicsPoint Incident Management (EPIM) konfigurálásához kell hozzáadnia EthicsPoint Incident Management (EPIM) a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből EthicsPoint Incident Management (EPIM) hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **EthicsPoint Incident Management (EPIM)**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspoint_search.png)

5. Az eredmények panelen válassza ki a **EthicsPoint Incident Management (EPIM)**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspoint_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a EthicsPoint Incident Management (EPIM) "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó EthicsPoint Incident Management (EPIM) a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználói EthicsPoint Incident Management (EPIM) közötti kapcsolat kapcsolatot kell létrehozni.

EthicsPoint Incident Management (EPIM) rendelje értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés EthicsPoint Incident Management (EPIM) tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[EthicsPoint Incident Management (EPIM) tesztfelhasználó létrehozása](#creating-a-ethicspoint-incident-management-epim-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon a EthicsPoint Incident Management (EPIM), amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az EthicsPoint Incident Management (EPIM) alkalmazásban.

**Az Azure AD az egyszeri bejelentkezés konfigurálása EthicsPoint Incident Management (EPIM), hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **EthicsPoint Incident Management (EPIM)** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspoint_samlbase.png)

3. Az a **EthicsPoint Incident Management (EPIM) tartományhoz és URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspoint_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<companyname>.navexglobal.com`|
    | `https://<companyname>.ethicspointvp.com`|

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.navexglobal.com/adfs/services/trust`

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<servername>.navexglobal.com/adfs/ls/`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges válasz URL-CÍMEN, azonosítóját és a bejelentkezési URL-cím. Ügyfél [EthicsPoint Incident Management (EPIM) ügyfél-támogatási csoport](http://www.navexglobal.com/company/contact-us) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspoint_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_400.png)
    
6. Egyszeri bejelentkezés konfigurálása **EthicsPoint Incident Management (EPIM)** oldalon kell küldeniük a letöltött **metaadatainak XML-kódja** való [EthicsPoint Incident Management (EPIM) támogatási csoport](http://www.navexglobal.com/company/contact-us).

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-ethicspoint-incident-management-epim-test-user"></a>EthicsPoint Incident Management (EPIM) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon EthicsPoint Incident Management (EPIM) nevű felhasználót hoz létre. Adjon együttműködve [EthicsPoint Incident Management (EPIM) támogatási csoport](http://www.navexglobal.com/company/contact-us) a felhasználók hozzáadása a EthicsPoint Incident Management (EPIM) platform.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezéshez használandó EthicsPoint Incident Management (EPIM) való hozzáférés biztosítása.

![Felhasználó hozzárendelése][200] 

**Rendelje hozzá a Britta Simon EthicsPoint Incident Management (EPIM), hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **EthicsPoint Incident Management (EPIM)**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspoint_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.
Ha a hozzáférési panelen EthicsPoint Incident Management (EPIM) csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az EthicsPoint Incident Management (EPIM) alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_203.png

