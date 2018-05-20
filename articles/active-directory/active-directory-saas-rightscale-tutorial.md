---
title: 'Oktatóanyag: Azure Active Directoryval integrált Rightscale |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Rightscale között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 37df1eca6b55a8bd126645f43d177263a1bede70
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Oktatóanyag: Azure Active Directoryval integrált Rightscale

Ebben az oktatóanyagban elsajátíthatja Rightscale integrálása az Azure Active Directory (Azure AD).

Rightscale integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Rightscale hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Rightscale (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Rightscale, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Rightscale egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Rightscale hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-rightscale-from-the-gallery"></a>A gyűjteményből Rightscale hozzáadása
Az Azure AD integrálása a Rightscale konfigurálásához kell hozzáadnia Rightscale a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Rightscale hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Rightscale**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_search.png)

5. Az eredmények panelen válassza ki a **Rightscale**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Rightscale.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Rightscale a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Rightscale közötti kapcsolat kapcsolatot kell létrehozni.

Rightscale, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Rightscale tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Rightscale tesztfelhasználó létrehozása](#creating-a-rightscale-test-user)**  - való Britta Simon valami Rightscale, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Rightscale alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Rightscale, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Rightscale** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_samlbase.png)

3. Az a **Rightscale tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás **IDP kezdeményezett mód** nincs bármely lépések végrehajtásához, mert az alkalmazás már előre integrált Azure-ral.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_url.png)

4. Az a **Rightscale tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás **Szolgáltató kezdeményezett mód**, hajtsa végre a következő lépéseket:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_url1.png)

    a. Kattintson a **megjelenítése speciális URL-beállításainak**.

    b. Az a **URL-cím bejelentkezési** szövegmező, írja be az URL-cím: `https://login.rightscale.com/`

5. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rightscale-tutorial/tutorial_general_400.png)

7. A a **Rightscale konfigurációs** kattintson **konfigurálása Rightscale** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_configure.png) 
<CS>
8. Ahhoz, hogy az alkalmazáshoz konfigurált SSO, meg kell bejelentkezés a RightScale bérlő rendszergazdaként.

    a. A felső menüben kattintson a **beállítások** lapot, és válasszon **egyszeri bejelentkezés**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_001.png) 

    b. Kattintson a "**új**" gombra kattintva adja hozzá **a SAML-alapú identitás-szolgáltatóktól**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_002.png) 
 
    c. A szövegmezőben **megjelenített név**, adjon meg a cég nevét.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_003.png)
 
    d. Válassza ki **engedélyezése RightScale által kezdeményezett egyszeri Bejelentkezést használó felderítés mutató** bemeneti és a **tartománynév** a a szövegmező alatt.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_004.png)

    e. Illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** , amely az Azure portálról másolta **SAML SSO végpont** a RightScale.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_006.png)

    f. Illessze be az értékét **SAML Entitásazonosító** , amely az Azure portálról másolta **SAML entityid beállítást** a RightScale.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_008.png)

    g. Kattintson a **böngésző** töltse fel a tanúsítványt, amely az Azure-portálról letöltött gombra.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_009.png)

    h. Kattintson a **Save** (Mentés) gombra.
<CE>
> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-rightscale-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-rightscale-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-rightscale-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-rightscale-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-rightscale-test-user"></a>Rightscale tesztfelhasználó létrehozása

Ebben a szakaszban egy RightScale Britta Simon nevű felhasználót hoz létre. Együttműködve [Rightscale ügyfél-támogatási csoport](mailto:support@rightscale.com) a felhasználók hozzáadása a RightScale platform.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Rightscale Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Rightscale, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Rightscale**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a hozzáférési panelen az Azure AD SSO-konfigurációjának tesztelése.  

Ha a hozzáférési panelen RightScale csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az RightScale alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_203.png

