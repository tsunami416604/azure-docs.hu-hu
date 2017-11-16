---
title: "Oktatóanyag: Azure Active Directory-integráció való összefolyás felett az SAML-alapú egyszeri felbontása GmbH |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a SAML SSO való összefolyás felett a felbontása GmbH."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6b47d483-d3a3-442d-b123-171e3f0f7486
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: 17bd8294c957c0666ffe75d1ff06b863f1176048
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Oktatóanyag: Azure Active Directory-integráció való összefolyás felett az SAML-alapú egyszeri felbontása GmbH

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálását a SAML SSO való összefolyás felett a feloldási GmbH az Azure Active Directoryval (Azure AD).

SAML SSO való összefolyás felett a felbontása GmbH az Azure AD integrálása lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáféréssel rendelkezik SAML SSO való összefolyás felett a felbontása GmbH Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a SAML SSO való összefolyás felett a felbontása GmbH (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs való összefolyás felett a SAML SSO feloldási GmbH, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A SAML SSO a felbontása GmbH egyszeri bejelentkezés engedélyezve van az előfizetésben való összefolyás felett

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Megoldási GmbH való összefolyás felett a SAML SSO hozzáadását a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Megoldási GmbH való összefolyás felett a SAML SSO hozzáadását a gyűjteményből

A integrálása a SAML SSO való összefolyás felett a feloldási GmbH által az Azure AD konfigurálásához kell hozzáadnia való összefolyás felett a SAML SSO felbontása GmbH a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a SAML SSO való összefolyás felett a felbontása GmbH a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **felbontása GmbH való összefolyás felett a SAML SSO**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_search.png)

5. Az eredmények panelen válassza ki a **felbontása GmbH való összefolyás felett a SAML SSO**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezéshez való összefolyás felett az SAML-alapú egyszeri által GmbH alapuló "Britta Simon." nevű tesztfelhasználó felbontás

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó a SAML SSO való összefolyás felett a az Azure AD egy felhasználónak van GmbH felbontása. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a SAML SSO való összefolyás felett a felbontása hivatkozás kapcsolatának GmbH kell létrehozni.

A SAML SSO a felbontása GmbH való összefolyás felett, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezéshez a SAML SSO való összefolyás felett a felbontása GmbH tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[A SAML SSO a feloldási GmbH teszt felhasználó való összefolyás felett létrehozása](#creating-a-saml-sso-for-confluence-by-resolution-gmbh-test-user)**  - való egy megfelelője a Britta Simon való összefolyás felett a SAML SSO felbontása GmbH, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, majd állítsa egyszeri bejelentkezéshez a SAML SSO való összefolyás felett a felbontása GmbH alkalmazás.

**Konfigurálja az Azure AD egyszeri bejelentkezéssel való összefolyás felett az SAML-alapú egyszeri feloldási GmbH, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **felbontása GmbH való összefolyás felett a SAML SSO** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_samlbase.png)

3. Az a **SAML SSO való összefolyás felett felbontása GmbH tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_url_1.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<server-base-url>/plugins/servlet/samlsso`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<server-base-url>/plugins/servlet/samlsso`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak**. Ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_url_2.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [való összefolyás felett feloldási GmbH ügyfél által a SAML SSO támogatási csoport](https://www.resolution.de/go/support) beolvasni ezeket az értékeket. 

5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_400.png)  
    
7. Egy másik webes böngészőablakban, jelentkezzen be a **való összefolyás felett feloldási GmbH felügyeleti portál a SAML SSO** rendszergazdaként.

8. Vigye a mutatót a ikonjára, majd kattintson a **bővítmények**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon1.png)

9. Rendszergazdai hozzáférés lap van átirányítva. Adja meg a jelszót, és kattintson a **megerősítése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon2.png)

10. A **ATLASSIAN piactér** lapra, majd **található új bővítmények**. 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon.png)

11. Keresési **SAML-alapú egyszeri bejelentkezés (SSO) való összefolyás felett a** kattintson **telepítése** az új SAML-alapú beépülő modul telepítése gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon7.png)

12. A beépülő modul telepítése elindul. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon8.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon9.png)

13. Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon10.png)
    
14. Kattintson a **konfigurálása** konfigurálása az új beépülő modul.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon11.png)

15. Az új beépülő modult is található **felhasználók és biztonsági** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon3.png)
    
16. A **SAML SingleSignOn beépülő modul konfigurációs** kattintson **adja hozzá az új IdP** gombra identitásszolgáltató beállításainak konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon4.png)

17. A **válassza a SAML-Identitásszolgáltatóként** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon5a.png)
 
    a. Állítsa be **az Azure AD** IdP típusként.
    
    b. Adja hozzá **neve** az Identity Provider (például az Azure AD).
    
    c. Adja hozzá **leírás** az Identity Provider (például az Azure AD).
    
    d. Kattintson a **Tovább** gombra.
    
18. A **identitás szolgáltató konfigurálása** kattintson **következő** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon5b.png)

19. A **SAML IdP metaadatok importálása** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon5c.png)

    a. Kattintson a **fájl betöltése** gombra, majd válassza ki az 5. lépésben letöltött metaadatok XML-fájl.

    b. Kattintson a **importálási** gombra.
    
    c. Várja meg a rövid időre, amíg importálása sikeres.
    
    d. Kattintson a **következő** gombra.
    
20. A **felhasználói azonosító attribútum és átalakítás** kattintson **következő** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon5d.png)
    
21. A **felhasználó létrehozása és a frissítés** kattintson **Mentés & következő** beállításainak mentése.   
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon6a.png)
    
22. A **tesztelje a beállításokat** kattintson **teszt kihagyása & kézzel konfigurálásához** a felhasználó vizsgálat most kihagyja. Ez a következő szakaszban történik, és egyes beállítások, az Azure portálon igényel. 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon6b.png)
    
23. Apprearing párbeszédpanel olvasási **kihagyása a vizsgálati eszköz...** , kattintson a **OK**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/addon6c.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>A SAML SSO a való összefolyás felett feloldási GmbH teszt felhasználó létrehozása

Ahhoz, hogy a SAML SSO való összefolyás felett a feloldási GmbH jelentkezzen be az Azure AD-felhasználók, azok kell üzembe való összefolyás felett a SAML SSO be névfeloldási GmbH által.  
A SAML SSO a felbontása GmbH való összefolyás felett egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a feloldási GmbH vállalati hely való összefolyás felett a SAML SSO.

2. Vigye a mutatót a ikonjára, majd kattintson a **felhasználókezelés**.

    ![Alkalmazott hozzáadása](./media/active-directory-saas-samlssoconfluence-tutorial/user1.png) 

3. A felhasználók szakaszban kattintson **felhasználók hozzáadása az** fülre. Az a **"A felhasználó hozzáadása"** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](./media/active-directory-saas-samlssoconfluence-tutorial/user2.png) 

    a. Az a **felhasználónév** szövegmező, írja be a felhasználó Britta Simon például az e-mailt.

    b. Az a **teljes nevét** szövegmező, írja be például a Britta Simon felhasználói teljes nevét.

    c. Az a **E-mail** szövegmező, a felhasználó e-mail címe típusát, például Brittasimon@contoso.com.

    d. Az a **jelszó** szövegmezőhöz Britta Simon írja be a jelszót.

    e. Kattintson a **jelszó megerősítése** írja be újból a jelszót.
    
    f. Kattintson a **Hozzáadás** gombra.    

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban Britta Simon való összefolyás felett a SAML SSO GmbH megoldás által biztosított hozzáférés szerint használandó Azure egyszeri bejelentkezés engedélyezése.

![Felhasználó hozzárendelése][200] 

**Britta Simon GmbH megoldási történő való összefolyás felett a SAML SSO hozzárendelését, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **felbontása GmbH való összefolyás felett a SAML SSO**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

A SAML SSO való összefolyás felett a kattintson a feloldás GmbH csempe a hozzáférési panelen által, meg kell beolvasása automatikusan aláírt a a SAML SSO való összefolyás felett a felbontása GmbH alkalmazás.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_203.png

