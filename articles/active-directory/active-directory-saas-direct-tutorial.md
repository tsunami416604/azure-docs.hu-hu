---
title: "Oktatóanyag: Azure Active Directory-integráció közvetlen |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a közvetlen."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 7c2cd1f0-d14c-42f0-94a8-9b800008b285
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 0409ef01630f97d772b08f72006cc1e06e8dae24
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-direct"></a>Oktatóanyag: Azure Active Directory-integráció közvetlen

Ebben az oktatóanyagban elsajátíthatja közvetlen integrálása az Azure Active Directory (Azure AD).

Közvetlen integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a közvetlen hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett közvetlen (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása közvetlen, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A közvetlen egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Közvetlen felvétele a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-direct-from-the-gallery"></a>Közvetlen felvétele a gyűjteményből
Az Azure AD integrálása a közvetlen konfigurálásához kell hozzáadnia a közvetlen a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Vegye fel közvetlenül a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **közvetlen**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-direct-tutorial/tutorial_direct_search.png)

5. Az eredmények panelen válassza ki a **közvetlen**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-direct-tutorial/tutorial_direct_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést az "Britta Simon." nevű tesztfelhasználó közvetlen alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó közvetlen a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a közvetlen hivatkozás kapcsolatának kell létrehozni.

Közvetlen, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés közvetlen tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Közvetlen tesztfelhasználó létrehozása](#creating-a-direct-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon közvetlen, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a közvetlen alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD az egyszeri bejelentkezés konfigurálása közvetlen, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **közvetlen** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-direct-tutorial/tutorial_direct_samlbase.png)

3. Az a **közvetlen tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-direct-tutorial/tutorial_direct_url.png)

    Az a **azonosító** szövegmező, írja be az URL-cím:`https://direct4b.com/`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak**, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-direct-tutorial/tutorial_direct_url1.png)

     Az a **bejelentkezési URL-cím** szövegmező, írja be az URL-cím:`https://direct4b.com/sso` 
    
5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-direct-tutorial/tutorial_direct_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-direct-tutorial/tutorial_general_400.png)

7. Egyszeri bejelentkezés konfigurálása **közvetlen** oldalon kell küldeniük a letöltött **metaadatainak XML-kódja** való [közvetlen támogatási csoport](https://direct4b.com/ja/support.html#inquiry). 

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-direct-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-direct-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-direct-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-direct-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-direct-test-user"></a>Közvetlen tesztfelhasználó létrehozása

Ebben a szakaszban egy közvetlen Britta Simon nevű felhasználót hoz létre. Együttműködve [közvetlen támogatási csoport](https://direct4b.com/ja/support.html#inquiry) a felhasználók hozzáadása a közvetlen platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezéshez használandó való közvetlen hozzáférés biztosítása.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése közvetlen, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **közvetlen**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-direct-tutorial/tutorial_direct_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

1. Ha a tesztelni kívánt **IDP kezdeményezett mód**:

    Ha a **közvetlen** csempére a hozzáférési panelen kapja meg automatikusan bejelentkezett a a **közvetlen** alkalmazás.

2. Ha a tesztelni kívánt **SP kezdeményezett mód**:
    
    a. Kattintson a **közvetlen** csempére a hozzáférési panelen, és az alkalmazás bejelentkezési oldalra irányítja.

    b. Adjon meg a `subdomain` a szövegmező jelenik meg, és nyomja le az "次へ (Tovább)", és akkor kapja meg automatikusan bejelentkezett a a **közvetlen** alkalmazás.
    
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-direct-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-direct-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-direct-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-direct-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-direct-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-direct-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-direct-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-direct-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-direct-tutorial/tutorial_general_203.png

