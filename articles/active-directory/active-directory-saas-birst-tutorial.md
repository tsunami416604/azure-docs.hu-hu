---
title: "Oktatóanyag: Azure Active Directoryval integrált Birst megalapozott üzleti Analytics |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az Birst gyors üzleti elemzések között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 677183b1-5348-4302-88cc-5c8ab63a3c6c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: aec0e475bc897b3d983a8054819562be4aa4682b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-birst-agile-business-analytics"></a>Oktatóanyag: Azure Active Directoryval integrált Birst megalapozott üzleti elemzés

Ebben az oktatóanyagban elsajátíthatja Birst megalapozott üzleti Analytics integrálása az Azure Active Directory (Azure AD).

Birst megalapozott üzleti Analytics integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja az Azure AD, aki hozzáfér Birst megalapozott üzleti elemzés
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Birst megalapozott üzleti Analytics (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Birst megalapozott üzleti Analytics, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Birst megalapozott üzleti Analytics egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből hozzáadása a Birst megalapozott üzleti elemzés
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-birst-agile-business-analytics-from-the-gallery"></a>A gyűjteményből hozzáadása a Birst megalapozott üzleti elemzés
Az Azure AD integrálása a Birst megalapozott üzleti Analytics konfigurálásához kell hozzáadnia Birst megalapozott üzleti elemzés a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Birst megalapozott üzleti Analytics hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Birst megalapozott üzleti Analytics**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-birst-tutorial/tutorial_birst_search.png)

5. Az eredmények panelen válassza ki a **Birst megalapozott üzleti Analytics**, és kattintson a **hozzáadása** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-birst-tutorial/tutorial_birst_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálhatja, és tesztelés az Azure AD az egyszeri bejelentkezés Birst "Britta Simon." nevű tesztfelhasználó alapján a nagy üzleti elemzés

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Birst megalapozott üzleti Analytics egy felhasználó az Azure ad-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó Birst megalapozott üzleti Analytics közötti kapcsolat kapcsolatot kell létrehozni.

A nagy üzleti Analytics Birst, rendelje értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést az Birst megalapozott üzleti Analytics tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Birst megalapozott üzleti Analytics tesztfelhasználó létrehozása](#creating-a-birst-agile-business-analytics-test-user)**  - való egy megfelelője a Britta Simon Birst megalapozott üzleti Analytics, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Birst megalapozott üzleti Analytics alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Birst megalapozott üzleti Analytics, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Birst megalapozott üzleti Analytics** alkalmazás integráció lapján, kattintson **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-birst-tutorial/tutorial_birst_samlbase.png)

3. Az a **Birst megalapozott üzleti Analytics tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-birst-tutorial/tutorial_birst_url.png)

     Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`

     Az URL-címet, hogy a Birst fiók tartozik a datacenter függ: 

     * Az Amerikai Egyesült Államok datacenter használja a következő mintát:`https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID` 

     * Európa datacenter használja a következő mintát:`https://login.eu1.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`

    > [!NOTE] 
    > Ez az érték nincs valós. Frissítse az értéket a tényleges bejelentkezési URL-címet. Ügyfél [Birst megalapozott üzleti Analytics ügyfél-támogatási csoport](mailto:info@birst.com) az értéket be kell olvasni. 
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-birst-tutorial/tutorial_birst_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-birst-tutorial/tutorial_general_400.png)

6. A a **Birst megalapozott üzleti konfigurációja** területen kattintson **konfigurálása Birst megalapozott üzleti Analytics** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-birst-tutorial/tutorial_birst_configure.png) 

7. Egyszeri bejelentkezés konfigurálása **Birst megalapozott üzleti elemzés** oldalon kell küldeniük a letöltött **tanúsítvány (Base64)**, **Sign-Out URL-címe, SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezést. URL-címe** való [Birst megalapozott üzleti Analytics támogatási csoport](mailto:info@birst.com). 

    > [!NOTE]
    > Birst csapat említse meg, hogy ez az integráció SHA256 algoritmust kell-e (SHA1 nem támogatott), hogy azok a megfelelő kiszolgálón állíthat be az egyszeri Bejelentkezést, például **app2101** stb.
  

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-birst-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-birst-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-birst-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-birst-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-birst-agile-business-analytics-test-user"></a>Birst megalapozott üzleti Analytics tesztfelhasználó létrehozása

Ez a szakasz célja Britta Simon Birst megalapozott üzleti Analytics nevű felhasználót létrehozni. Együttműködve [Birst megalapozott üzleti Analytics támogatási csoport](mailto:info@birst.com) a felhasználók hozzáadása a Birst fiók. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban Britta Simon által biztosított hozzáférés Birst megalapozott üzleti Analytics használandó Azure egyszeri bejelentkezés engedélyezése.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Birst megalapozott üzleti Analytics, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Birst megalapozott üzleti Analytics**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-birst-tutorial/tutorial_birst_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a hozzáférési panelen az Azure AD SSO-konfigurációjának tesztelése.

Ha a hozzáférési panelen Birst megalapozott üzleti Analytics csempére kattint, meg kell beolvasása automatikusan bejelentkezett az Birst megalapozott üzleti Analytics alkalmazására. 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-birst-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-birst-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-birst-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-birst-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-birst-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-birst-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-birst-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-birst-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-birst-tutorial/tutorial_general_203.png

