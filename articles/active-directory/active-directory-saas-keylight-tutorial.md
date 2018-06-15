---
title: 'Oktatóanyag: Azure Active Directoryval integrált LockPath Keylight |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és LockPath Keylight között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 5eb3aa008ccfa5711d8282f6d98df44239d236a4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345911"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Oktatóanyag: Azure Active Directoryval integrált LockPath Keylight

Ebben az oktatóanyagban elsajátíthatja LockPath Keylight integrálása az Azure Active Directory (Azure AD).

LockPath Keylight integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a LockPath Keylight hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a LockPath Keylight (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs LockPath Keylight, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy LockPath Keylight egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből LockPath Keylight hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-lockpath-keylight-from-the-gallery"></a>A gyűjteményből LockPath Keylight hozzáadása
Az Azure AD integrálása a LockPath Keylight konfigurálásához kell hozzáadnia LockPath Keylight a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből LockPath Keylight hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **LockPath Keylight**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_search.png)

5. Az eredmények panelen válassza ki a **LockPath Keylight**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálhatja, és a vizsgálat az Azure AD egyszeri bejelentkezést a LockPath Keylight "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó LockPath Keylight a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a LockPath Keylight közötti kapcsolat kapcsolatot kell létrehozni.

LockPath Keylight, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a LockPath Keylight tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[LockPath Keylight tesztfelhasználó létrehozása](#creating-a-lockpath-keylight-test-user)**  - való egy megfelelője a Britta Simon LockPath Keylight, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az LockPath Keylight alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés LockPath Keylight, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **LockPath Keylight** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_samlbase.png)

3. A a **LockPath Keylight tartomány és az URL-címek** területen az alábbi lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<company name>.keylightgrc.com/`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<company name>.keylightgrc.com`

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<company name>.keylightgrc.com/Login.aspx`
    
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [LockPath Keylight ügyfél-támogatási csoport](https://www.lockpath.com/contact/) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Raw)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-keylight-tutorial/tutorial_general_400.png)
    
6. A a **LockPath Keylight konfigurációs** kattintson **konfigurálása LockPath Keylight** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out és SAML-alapú egyszeri bejelentkezés szolgáltatás URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_configure.png) 

7. Ahhoz, hogy a LockPath Keylight SSO, hajtsa végre az alábbi lépéseket:
   
    a. Bejelentkezés LockPath Keylight fiókjába rendszergazdaként.
    
    b. Kattintson a felső menüben **személy**, és válassza ki **Keylight telepítő**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-keylight-tutorial/401.png) 

    c. Kattintson a bal oldali fastruktúrában, **SAML**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-keylight-tutorial/402.png) 

    d. Az a **SAML beállítások** párbeszédpanel, kattintson a **szerkesztése**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-keylight-tutorial/404.png) 

8. Az a **SAML beállításainak szerkesztése** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-keylight-tutorial/405.png) 
   
    a. Állítsa be **SAML-alapú hitelesítés** való **aktív**.

    b. Beillesztés a **SAML-alapú egyszeri bejelentkezési URL-címe** érték, amely az Azure-portálról másolta a **Identity Provider bejelentkezési URL-cím** szövegmező.

    c. Beillesztés a **egyetlen Sign-Out URL-címe** érték, amely az Azure-portálról másolta a **Identity Provider kijelentkezési URL-cím** szövegmező.

    d. Kattintson a **Choose File** válassza ki a letöltött LockPath Keylight tanúsítványt, és kattintson **nyitott** töltse fel a tanúsítványt.

    e. Állítsa be **SAML felhasználóazonosító hely** való **NameIdentifier elem a tulajdonos utasítás**.
    
    f. Adja meg a **Keylight szolgáltató** használatával a következő mintát: **https://&lt;#companyname&gt;. keylightgrc.com**.
    
    g. Állítsa be **automatikus-kiépítési felhasználók** való **aktív**.

    h. Állítsa be **automatikus-kiépítési fióktípus** való **teljes felhasználói**.

    i. Állítsa be **automatikus-kiépítési biztonsági szerepkör**, jelölje be **SAML az általános jogú felhasználó**.
    
    j. Állítsa be **automatikus-kiépítési biztonsági config**, jelölje be **normál felhasználói konfigurációban**.
     
    k. Az a **E-mail attribútum** szövegmezőhöz típus `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    l. Az a **Keresztnév attribútum** szövegmezőhöz típus `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    m. Az a **utolsó name attribútum** szövegmezőhöz típus `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    
    n. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-keylight-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-keylight-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-lockpath-keylight-test-user"></a>LockPath Keylight tesztfelhasználó létrehozása

Ebben a szakaszban egy LockPath Keylight Britta Simon nevű felhasználót hoz létre. LockPath Keylight támogatja közvetlenül az időponthoz kötött kiosztást, amely alapértelmezés szerint engedélyezve van.

Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre, amikor LockPath Keylight elérését, ha a felhasználó még nem létezik. 

>[!NOTE]
>Hozza létre a felhasználó manuálisan kell, ha szeretné-e lépjen kapcsolatba a [LockPath Keylight ügyfél-támogatási csoport](https://www.lockpath.com/contact/). 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés LockPath Keylight Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése LockPath Keylight, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **LockPath Keylight**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen LockPath Keylight csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az LockPath Keylight alkalmazására. 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png

