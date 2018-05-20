---
title: 'Oktatóanyag: Azure Active Directoryval integrált Moxtra |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Moxtra között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: 1d0b89cf0d090795a305b5bfcce070136cf0289a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Oktatóanyag: Azure Active Directoryval integrált Moxtra

Ebben az oktatóanyagban elsajátíthatja Moxtra integrálása az Azure Active Directory (Azure AD).

Moxtra integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Moxtra hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Moxtra (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Moxtra, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Moxtra egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Moxtra hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-moxtra-from-the-gallery"></a>A gyűjteményből Moxtra hozzáadása
Az Azure AD integrálása a Moxtra konfigurálásához kell hozzáadnia Moxtra a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Moxtra hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Moxtra**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_search.png)

5. Az eredmények panelen válassza ki a **Moxtra**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Moxtra.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Moxtra a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Moxtra közötti kapcsolat kapcsolatot kell létrehozni.

Moxtra, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Moxtra tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Moxtra tesztfelhasználó létrehozása](#creating-a-moxtra-test-user)**  - való Britta Simon valami Moxtra, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Moxtra alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Moxtra, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Moxtra** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_samlbase.png)

3. Az a **Moxtra tartomány és az URL-címek** csoportjában hajtsa végre a következő lépést:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg az URL-címet: `https://www.moxtra.com/service/#login`

4. Moxtra alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa ehhez a konfigurációhoz. 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_attributes.png)
    
5. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ------------------- | -------------------- |    
    | Utónév | User.givenName |
    | Vezetéknév | User.surname |
    | idpid    | < SAML entitás azonosítója > 

    > [!Note]
    > Értékének **idpid** attribútum nincs valós. A tényleges érték kaphat **rövid összefoglaló** szakaszában **Moxtra konfigurációs**.
    
    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_04.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_05.png)

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d. Kattintson az **OK** gombra.
    
5. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-moxtra-tutorial/tutorial_general_400.png)

7. A a **Moxtra konfigurációs** kattintson **konfigurálása Moxtra** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_configure.png) 

8. Egy másik böngészőablakban jelentkezzen be a Moxtra vállalati webhely rendszergazdaként.

9. A bal oldali eszköztárán kattintson **felügyeleti konzol > SAML-alapú egyszeri bejelentkezést**, és kattintson a **új**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) 

10. Az a **SAML** lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Az a **neve** szövegmező, adja meg a konfiguráció nevét (pl.: *SAML*). 
  
    b. Az a **IdP Entitásazonosító** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta. 
 
    c. A **bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta. 
 
    d. Az a **AuthnContextClassRef** szövegmezőhöz típus **urn: oasis: nevek: tc: SAML:2.0:ac:classes:Password**. 
 
    e. Az a **NameID formátum** szövegmezőhöz típus **urn: oasis: nevek: tc: SAML:1.1:nameid-formátum: emailAddress**. 
 
    f. Nyissa meg a tanúsítványt, amely a Jegyzettömbben az Azure portálról letöltött másolja a tartalmat, és illessze be azt a **tanúsítvány** szövegmező.    
 
    g. A SAML-alapú e-mail tartomány szövegmezőben írja be a SAML-alapú e-mail tartománya.    
  
    >[!NOTE]
    >A tartomány ellenőrzésének lépéseit megtekintéséhez kattintson a "**i**" alatt.

    h. Kattintson a **frissítés**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-moxtra-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-moxtra-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-moxtra-test-user"></a>Moxtra tesztfelhasználó létrehozása

Ez a szakasz célja Moxtra Britta Simon nevű felhasználót létrehozni.

**A felhasználó Britta Simon meghívta Moxtra létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Moxtra vállalati webhely.

2. A bal oldali eszköztárán kattintson **felügyeleti konzol > felhasználók kezelése**, majd **felhasználó hozzáadása**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) 

3. Az a **felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:
  
    a. Az a **Utónév** szövegmezőhöz típus **Britta**.
  
    b. Az a **Vezetéknév** szövegmezőhöz típus **Simon**.
  
    c. Az a **E-mail** szövegmezőhöz Britta tartozó e-mail cím megegyezik az Azure-portál típusa.
  
    d. Az a **osztás** szövegmezőhöz típus **fejlesztői**.
  
    e. Az a **részleg** szövegmezőhöz típus **informatikai**.
  
    f. Válassza ki **rendszergazda**.
  
    g. Kattintson a **Hozzáadás** parancsra.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Moxtra Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Moxtra, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Moxtra**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Moxtra csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Moxtra alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png

