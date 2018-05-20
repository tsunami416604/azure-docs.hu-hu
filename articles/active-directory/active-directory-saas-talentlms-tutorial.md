---
title: 'Oktatóanyag: Azure Active Directoryval integrált TalentLMS |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és TalentLMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: 1c81d3e01170b2d134f83551911f9fcbd6e5f6e9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Oktatóanyag: Azure Active Directoryval integrált TalentLMS

Ebben az oktatóanyagban elsajátíthatja TalentLMS integrálása az Azure Active Directory (Azure AD).

TalentLMS integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a TalentLMS hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett TalentLMS (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs TalentLMS, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy TalentLMS egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió Itt kaphat: [próbaverzió ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből TalentLMS hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-talentlms-from-the-gallery"></a>A gyűjteményből TalentLMS hozzáadása
Az Azure AD integrálása a TalentLMS konfigurálásához kell hozzáadnia TalentLMS a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből TalentLMS hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **TalentLMS**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_search.png)

5. Az eredmények panelen válassza ki a **TalentLMS**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján TalentLMS

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó TalentLMS a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a TalentLMS közötti kapcsolat kapcsolatot kell létrehozni.

TalentLMS, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a TalentLMS tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[TalentLMS tesztfelhasználó létrehozása](#creating-a-talentlms-test-user)**  - való Britta Simon valami TalentLMS, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az TalentLMS alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés TalentLMS, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **TalentLMS** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_samlbase.png)

3. Az a **TalentLMS tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<tenant-name>.TalentLMSapp.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `http://<tenant-name>.talentlms.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [TalentLMS ügyfél-támogatási csoport](https://www.talentlms.com/contact) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** szakaszban, másolja a **UJJLENYOMAT** érték a tanúsítványból.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-talentlms-tutorial/tutorial_general_400.png)

6. A a **TalentLMS konfigurációs** kattintson **konfigurálása TalentLMS** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_configure.png)  

7. Egy másik webes böngészőablakban jelentkezzen be a TalentLMS vállalati webhely rendszergazdaként.

8. Az a **fiók & beállítások** területen kattintson a **felhasználók** fülre.
   
    ![Fiók & beállítások](./media/active-directory-saas-talentlms-tutorial/IC777296.png "fiók & beállítások")

9. Kattintson a **egyszeri bejelentkezés (SSO)**,

10. Egyszeri bejelentkezés csoportjában hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés](./media/active-directory-saas-talentlms-tutorial/IC777297.png "egyszeri bejelentkezés")   

    a. Az a **SSO integrációs típus** listáról válassza ki **SAML 2.0**.

    b. Az a **identitásszolgáltató (IDP)** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító**, amely az Azure-portálon másolta.
 
    c. Beillesztés a **ujjlenyomat** érték az Azure-portálon a **tanúsítvány-ujjlenyomat** szövegmező.    

    d.  A a **távoli bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.
 
    e. A a **távoli kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím**, amely az Azure-portálon másolta.

    f. Töltse ki a következőket: 

    * Az a **TargetedID** szövegmezőhöz típusa `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
     
    * Az a **Keresztnév** szövegmezőhöz típusa `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`
    
    * Az a **Vezetéknév** szövegmezőhöz típusa `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`
    
    * Az a **E-mail** szövegmezőhöz típusa `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`
    
11. Kattintson a **Save** (Mentés) gombra.
 
> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-talentlms-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-talentlms-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-talentlms-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-talentlms-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-talentlms-test-user"></a>TalentLMS tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók TalentLMS bejelentkezni, akkor ki kell építenie a TalentLMS. TalentLMS, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **TalentLMS** bérlő.

2. Kattintson a **felhasználók**, és kattintson a **felhasználó hozzáadása**.

3. Az a **felhasználó hozzáadása** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Felhasználó hozzáadása](./media/active-directory-saas-talentlms-tutorial/IC777299.png "felhasználó hozzáadása")  

    a. Az a **Utónév** szövegmező, írja be például a felhasználó utónevét **Britta**.

    b. Az a **Vezetéknév** szövegmező, írja be például a felhasználó vezetéknevét **Simon**.
 
    c. Az a **E-mail cím** szövegmező, adja meg az e-mail címét, például a felhasználó **brittasimon@contoso.com**.

    d. Kattintson a **felhasználó hozzáadása**.

>[!NOTE]
>Bármely más TalentLMS felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz TalentLMS által nyújtott API-k.
 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés TalentLMS Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése TalentLMS, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **TalentLMS**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen TalentLMS csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az TalentLMS alkalmazáshoz

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_203.png

