---
title: "Oktatóanyag: Azure Active Directoryval integrált Velpic SAML |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Velpic SAML között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.openlocfilehash: 5325f3cca00167e6b7b687509ce43435447ad2f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Oktatóanyag: Azure Active Directoryval integrált Velpic SAML

Ebben az oktatóanyagban elsajátíthatja Velpic SAML integrálása az Azure Active Directory (Azure AD).

Velpic SAML integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Velpic SAML hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Velpic SAML (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure felügyeleti portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Velpic SAML konfigurálása az Azure AD-integrációra, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Velpic SAML-alapú egyszeri bejelentkezést engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, ha ez nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Velpic SAML hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-velpic-saml-from-the-gallery"></a>A gyűjteményből Velpic SAML hozzáadása
Az Azure AD integrálása a Velpic SAML konfigurálásához kell hozzáadnia Velpic SAML a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Velpic SAML hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Velpic SAML**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_search.png)

5. Az eredmények panelen válassza ki a **Velpic SAML**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálhatja, és az Azure AD az egyszeri bejelentkezés Velpic SAML-teszthez "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Velpic SAML a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Velpic SAML közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a Velpic SAML.

Az Azure AD egyszeri bejelentkezéshez a Velpic SAML tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Velpic SAML tesztfelhasználó létrehozása](#creating-a-velpic-saml-test-user)**  - való egy megfelelője a Britta Simon Velpic SAML, amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure felügyeleti portálon, és konfigurálása egyszeri bejelentkezéshez az Velpic SAML-alkalmazásban.

**Velpic SAML konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálján a a **Velpic SAML** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. A a **egyszeri bejelentkezés** párbeszédpanel, mint **mód** kiválasztása **SAML-alapú bejelentkezés** a engedélyezése az egyszeri bejelentkezéshez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_samlbase.png)

3. Adja meg a részleteket a **Velpic SAML-tartomány és az URL-címek** szakasz -

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, írja be az értéket, mint:`https://<sub-domain>.velpicsaml.net`

    b. Az a **azonosító** szövegmező, illessze be a **"Egyszeri bejelentkezési URL-címe"** érték`https://auth.velpic.com/saml/v2/<entity-id>/login`
    
    > [!NOTE]
    > Vegye figyelembe, hogy a Velpic SAML team biztosítja a bejelentkezési URL-címen, és azonosító értéket az SSO beépülő modul Velpic SAML oldalon konfigurálásakor lesz elérhető. Ezt az értéket Velpic SAML alkalmazáslap másolja és illessze be ide kell.

4. Az a **SAML-aláíró tanúsítványa** kattintson **metaadatainak XML-kódja** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_400.png)

6. Kattintson a Velpic SAML-alapú konfigurációs szakasz Velpic SAML konfigurálása konfigurálása bejelentkezés ablak megnyitásához. Másolja a SAML Entitásazonosító rövid összefoglaló szakaszából.

7. Egy másik webes böngészőablakban jelentkezzen be a Velpic SAML-alapú vállalati webhely rendszergazdaként.

8. Kattintson a **kezelése** lapra, és navigáljon a **integrációs** szakaszban kattintson a kell **beépülő modulok** gombra kattintva hozzon létre új beépülő modul a bejelentkezéshez.

    ![Beépülő modul](./media/active-directory-saas-velpicsaml-tutorial/velpic_1.png)

9. Kattintson a **"Beépülő modul hozzáadása"** gombra.
    
    ![Beépülő modul](./media/active-directory-saas-velpicsaml-tutorial/velpic_2.png)

10. Kattintson a **SAML** a beépülő modul hozzáadása oldalon csempére.
    
    ![Beépülő modul](./media/active-directory-saas-velpicsaml-tutorial/velpic_3.png)

11. Adja meg az új SAML-alapú beépülő modul nevét, majd kattintson a **"Add"** gombra.

    ![Beépülő modul](./media/active-directory-saas-velpicsaml-tutorial/velpic_4.png)

12. Adja meg a következőképpen:

    ![Beépülő modul](./media/active-directory-saas-velpicsaml-tutorial/velpic_5.png)

    a. Az a **neve** szövegmező, írja be a SAML-alapú beépülő modul nevét.

    b. A a **kiállítójának URL-címe** szövegmező, illessze be a **SAML Entitásazonosító** , átmásolva a **bejelentkezés konfigurálása** ablakot, az Azure-portálon.

    c. Az a **szolgáltató metaadatok Config** a metaadatok XML-fájl, amely az Azure-portálról letöltött feltöltése.

    d. SAML engedélyezése csak az idő-kiépítés engedélyezése is beállíthatja a **"Automatikus hozzon létre új felhasználók"** jelölőnégyzetet. Ha egy felhasználó Velpic nem létezik, és ez a jelző nincs engedélyezve, az Azure-ból a bejelentkezés sikertelen lesz. Ha a jelző engedélyezve van a felhasználó automatikusan megkapják a Velpic bejelentkezés alkalmával. 

    e. Másolás a **egyszeri bejelentkezés URL-címen** szöveg mezőbe, majd illessze be az Azure-portálon.
    
    f. Kattintson a **Save** (Mentés) gombra.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure felügyeleti portálján Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_01.png) 

2. Ugrás a **felhasználók és csoportok** kattintson **minden felhasználó** azon felhasználók listájának megjelenítéséhez.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-velpic-saml-test-user"></a>Velpic SAML tesztfelhasználó létrehozása

Ebben a lépésben esetén általában nincs szükség, ha az alkalmazás csak az idő a felhasználók átadása támogatja. Ha nincs engedélyezve az automatikus felhasználó-átadási majd kézi felhasználó létrehozása teheti az alább ismertetett.

Jelentkezzen be rendszergazdaként egy vállalati Velpic SAML-alapú webhelyekhez, és hajtsa végre a következő lépéseket:
    
1. Kattintson a kezelés lapon, majd nyissa meg a felhasználók szakaszban kattintson a felhasználók hozzáadása az új gombra.

    ![felhasználó hozzáadása](./media/active-directory-saas-velpicsaml-tutorial/velpic_7.png)

2. Az a **"Az új felhasználó létrehozása"** párbeszédpanel lapon, a következő lépésekkel.

    ![Felhasználó](./media/active-directory-saas-velpicsaml-tutorial/velpic_8.png)
    
    a. Az a **Keresztnév** szövegmezőhöz Britta Simon az első nevét.

    b. Az a **Vezetéknév** szövegmezőhöz Britta Simon utolsó neve.

    c. Az a **felhasználónév** szövegmező, írja be a felhasználónevet Britta Simon.

    d. Az a **E-mail** szövegmezőhöz Britta Simon fiók e-mail címét.

    e. További információk megadása nem kötelező, megadhatja azt, ha szükséges.
    
    f. Kattintson a **SAVE** (Mentés) gombra.  

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított a hozzáférés Velpic SAML Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Velpic SAML, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálra, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Velpic SAML**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

1. Ha a hozzáférési panelen Velpic SAML csempére kattint, Velpic SAML alkalmazás bejelentkezési oldalán szerezheti be. Megjelenik a **"Jelentkezzen be az Azure AD"** a bejelentkezési oldal gombjára.

    ![Beépülő modul](./media/active-directory-saas-velpicsaml-tutorial/velpic_6.png)

2. Kattintson a **"Az Azure AD bejelentkezés"** gombra kattintva Velpic az Azure AD-fiókkal bejelentkezni.


## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_203.png

