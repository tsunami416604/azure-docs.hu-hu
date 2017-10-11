---
title: "Oktatóanyag: Azure Active Directoryval integrált UserEcho |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és UserEcho között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 2d824d8d5eb8a25db128397b908a126bd87213ea
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Oktatóanyag: Azure Active Directoryval integrált UserEcho

Ebben az oktatóanyagban elsajátíthatja UserEcho integrálása az Azure Active Directory (Azure AD).

UserEcho integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a UserEcho hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett UserEcho (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs UserEcho, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy UserEcho egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió Itt kaphat: [próbaverzió ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből UserEcho hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-userecho-from-the-gallery"></a>A gyűjteményből UserEcho hozzáadása
Az Azure AD integrálása a UserEcho konfigurálásához kell hozzáadnia UserEcho a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből UserEcho hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **UserEcho**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_search.png)

5. Az eredmények panelen válassza ki a **UserEcho**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján UserEcho.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó UserEcho a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a UserEcho közötti kapcsolat kapcsolatot kell létrehozni.

UserEcho, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a UserEcho tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[UserEcho tesztfelhasználó létrehozása](#creating-a-userecho-test-user)**  - való Britta Simon valami UserEcho, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az UserEcho alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés UserEcho, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **UserEcho** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_samlbase.png)

3. Az a **UserEcho tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.userecho.com/`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [UserEcho ügyfél-támogatási csoport](https://feedback.userecho.com/) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-userecho-tutorial/tutorial_general_400.png)

6. A a **UserEcho konfigurációs** kattintson **konfigurálása UserEcho** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out és SAML-alapú egyszeri bejelentkezés szolgáltatás URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_configure.png) 

7. Egy másik böngészőablakban jelentkezzen be a UserEcho vállalati webhely rendszergazdaként.

8. Az a felső eszköztáron kattintson a felhasználónevére, bontsa ki a menüt, és kattintson **telepítő**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 

9. Kattintson a **integrációja**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) 

10. Kattintson a **webhely**, és kattintson a **egyszeri bejelentkezés (egy SAML2)**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) 

11. Az a **egyszeri bejelentkezés (SAML)** lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png)
    
    a. Mint **SAML-kompatibilis**, jelölje be **Igen**.
    
    b. Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálról másolta a **SAML SSO URL-cím** szövegmező.
    
    c. Beillesztés **Sign-Out URL-cím**, amely az Azure-portálról másolta a **távoli logoout URL-cím** szövegmező.
    
    d. A letöltött tanúsítvány megnyitása a Jegyzettömbben, másolja a tartalmat, és illessze be azt a **X.509 tanúsítvány** szövegmező.
    
    e. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-userecho-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-userecho-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-userecho-test-user"></a>UserEcho tesztfelhasználó létrehozása

Ez a szakasz célja UserEcho Britta Simon nevű felhasználót létrehozni.

**A felhasználó Britta Simon meghívta UserEcho létrehozásához hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés a UserEcho vállalati webhely rendszergazdaként.

2. Az a felső eszköztáron kattintson a felhasználónevére, bontsa ki a menüt, és kattintson **telepítő**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png)

3. Kattintson a **felhasználók**, bontsa ki a **felhasználók** szakasz.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png)

4. Kattintson a **felhasználók**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png)

5. Kattintson a **új felhasználó meghívása**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png)

6. Az a **új felhasználó meghívása** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png)

    a. Az a **neve** szövegmezőhöz Britta Simon például a felhasználó nevét.
    
    b.  Az a **E-mail** szövegmező, a felhasználó e-mail címe típusát, például Brittasimon@contoso.com.
    
    c. Kattintson a **meghívása**.

A rendszer meghívót küld Britta, amely lehetővé teszi, hogy UserEcho indíthatja el. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés UserEcho Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése UserEcho, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **UserEcho**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a hozzáférési panelen az Azure AD SSO-konfigurációjának tesztelése.  

Ha a hozzáférési panelen UserEcho csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az UserEcho alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png

