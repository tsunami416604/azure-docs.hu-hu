---
title: "Oktatóanyag: Azure Active Directory-integráció Kantega egyszeri bejelentkezési modellel a JIRA |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a JIRA Kantega SSO között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e2af4891-e3c8-43b3-bdcb-0500c493e9b4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 06a1d301818f025270137f7eaa9f40e5e4503112
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>Oktatóanyag: Azure Active Directory-integráció Kantega egyszeri bejelentkezési modellel a JIRA

Ebben az oktatóanyagban elsajátíthatja a JIRA Kantega SSO integrálása az Azure Active Directory (Azure AD).

A JIRA Kantega SSO integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja az JIRA Kantega SSO hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Kantega SSO JIRA (egyszeri bejelentkezés) az a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs konfigurálásához Kantega egyszeri bejelentkezési modellel a JIRA, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Kantega SSO JIRA egyszeri bejelentkezést az előfizetés engedélyezve

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A JIRA Kantega SSO hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-kantega-sso-for-jira-from-the-gallery"></a>A JIRA Kantega SSO hozzáadása a gyűjteményből
A JIRA Kantega SSO integrálása az Azure AD konfigurálása, kell hozzáadnia a JIRA Kantega SSO a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a JIRA Kantega SSO a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Kantega SSO a JIRA**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_search.png)

5. Az eredmények panelen válassza ki a **Kantega SSO a JIRA**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD az egyszeri bejelentkezés Kantega egyszeri bejelentkezési modellel, az úgynevezett "Britta Simon" tesztfelhasználó alapján JIRA.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Kantega SSO JIRA a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a JIRA Kantega SSO közötti kapcsolat kapcsolatot kell létrehozni.

JIRA Kantega egyszeri Bejelentkezést, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés Kantega egyszeri bejelentkezési modellel a JIRA tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy Kantega SSO JIRA teszt felhasználó létrehozása](#creating-a-kantega-sso-for-jira-test-user)**  - való egy megfelelője a Britta Simon Kantega SSO a JIRA, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Kantega SSO JIRA alkalmazáshoz az egyszeri bejelentkezés konfigurálása.

**Az Azure AD az egyszeri bejelentkezés Kantega egyszeri bejelentkezési modellel a JIRA megadásához hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Kantega SSO a JIRA** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_samlbase.png)

3. A **IDP** indítható módban, a **Kantega SSO JIRA tartomány és az URL-címek** szakasz hajtsa végre a következő lépést:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_url1.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

4. A **SP** kezdeményezett módban, a jelölőnégyzet **megjelenítése speciális URL-beállításainak** és hajtsa végre a következő lépést:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_url2.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ezek az értékek fogadásának Jira beépülő modul, az oktatóanyag későbbi részében ismertetett konfigurálása során.

5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_400.png)
    
7. Egy másik webes böngészőablakban jelentkezzen be a helyi kiszolgálón JIRA rendszergazdaként.

8. Vigye a mutatót a ikonjára, majd kattintson a **bővítmények**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/addon1.png)

9. A bővítmények lap szakaszban kattintson **található új bővítmények**. Keresési **Kantega SSO JIRA (SAML & Kerberos) a** kattintson **telepítése** az új SAML-alapú beépülő modul telepítése gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/addon2.png)

10. A beépülő modul telepítésének megkezdése.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/addon3.png)

11. A telepítés befejeződése után. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/addon33.png)

12. Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/addon34.png)
    
13. Új beépülő modul megtalálható-e **integrációja**. Kattintson a **konfigurálása** konfigurálása az új beépülő modul.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/addon35.png)

14. Az a **SAML** szakasz. Válassza ki **Azure Active Directory (Azure AD)** a a **Hozzáadás identitásszolgáltató** legördülő menüből.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/addon4.png)

15. Válassza ki az előfizetési szinten **alapvető**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/addon5.png)     

16. Az a **alkalmazás tulajdonságainak** csoportjában hajtsa végre a következő lépéseket: 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/addon6.png)

    a. Másolás a **App ID URI** értékét, és használja azt **azonosító, a válasz URL-CÍMEN és a bejelentkezési URL-cím** a a **Kantega SSO JIRA tartomány és az URL-címek** szakaszban az Azure portálon.

    b. Kattintson a **Tovább** gombra.

17. Az a **metaadatok importálása** csoportjában hajtsa végre a következő lépéseket: 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/addon7.png)

    a. Válassza ki **metaadat-fájlt a számítógépen lévő**, és az Azure-portálról letöltött feltöltés metaadatait tartalmazó fájl.

    b. Kattintson a **Tovább** gombra.

18. Az a **nevét és az egyszeri bejelentkezés hely** csoportjában hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/addon8.png)
    
    a. Adja hozzá az identitásszolgáltató neve **identitás szolgáltatónevet** szövegmező (például az Azure AD).

    b. Kattintson a **Tovább** gombra.

19. Ellenőrizze a tanúsítvány aláírása, és kattintson **következő**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/addon9.png)

20. Az a **JIRA felhasználói fiókok** csoportjában hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/addon10.png)

    a. Válassza ki **felhasználók JIRA tartozó belső könyvtárban szükség esetén hozzon létre** , és írja be a felhasználói csoport megfelelő neve (lehet több nem. vesszővel elválasztott csoportok).

    b. Kattintson a **Tovább** gombra.

21. Kattintson a **Befejezés** gombra.   

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/addon11.png)

22. Az a **tartományok ismert az Azure AD** csoportjában hajtsa végre a következő lépéseket: 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/addon12.png)

    a. Válassza ki **tartományok ismert** a lap bal oldali panelen.

    b. Adja meg a tartomány nevére a **tartományok ismert** szövegmező.

    c. Kattintson a **Save** (Mentés) gombra. 

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-kantegassoforjira-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-kantegassoforjira-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-kantegassoforjira-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-kantegassoforjira-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-kantega-sso-for-jira-test-user"></a>Egy Kantega SSO a JIRA tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók JIRA bejelentkezni, akkor ki kell építenie a JIRA. A JIRA Kantega egyszeri Bejelentkezést egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a JIRA a helyi kiszolgálón.

2. Vigye a mutatót a ikonjára, majd kattintson a **felhasználókezelés**.

    ![Alkalmazott hozzáadása](./media/active-directory-saas-kantegassoforjira-tutorial/user1.png) 

3. A **felhasználókezelés** szakasz lapra, majd **a felhasználó létrehozása**.

    ![Alkalmazott hozzáadása](./media/active-directory-saas-kantegassoforjira-tutorial/user2.png) 

4. Az a **"Új felhasználó létrehozása"** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](./media/active-directory-saas-kantegassoforjira-tutorial/user3.png) 

    a. Az a **E-mail cím** szövegmező, a felhasználó e-mail címe típusát, például Brittasimon@contoso.com.

    b. Az a **teljes nevét** szövegmező, a felhasználó például Britta Simon típus teljes nevét.

    c. Az a **felhasználónév** szövegmezőben, az e-mailt a felhasználó típusát, például Brittasimon@contoso.com.

    d. Az a **jelszó** szövegmező, írja be a felhasználó jelszavát.

    e. Kattintson a **a felhasználó létrehozása**.   

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban Azure egyszeri bejelentkezéshez használandó hozzáférést biztosít a JIRA Kantega SSO Britta Simon engedélyezi.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése a JIRA Kantega SSO, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Kantega SSO a JIRA**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Kattintva a Kantega SSO JIRA csempe a hozzáférési panelen, meg kell beolvasni automatikusan aláírt a a Kantega SSO JIRA alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_203.png

