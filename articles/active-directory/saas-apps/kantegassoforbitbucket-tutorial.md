---
title: 'Oktatóanyag: Azure Active Directory-integráció Kantega egyszeri bejelentkezési modellel a Bitbucketből |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Bitbucketből Kantega SSO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 80b9e9453edf72b91d8928f8ce6610b0897b065f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35921713"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Oktatóanyag: Azure Active Directory-integráció Kantega egyszeri bejelentkezési modellel a Bitbucketből

Ebben az oktatóanyagban elsajátíthatja a Bitbucketből Kantega SSO integrálása az Azure Active Directory (Azure AD).

A Bitbucketből Kantega SSO integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja a Bitbucketből Kantega SSO hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Kantega SSO a Bitbucketből (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs konfigurálásához Kantega egyszeri bejelentkezési modellel a Bitbucketből, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Kantega SSO Bitbucket egyszeri bejelentkezést az előfizetés engedélyezve

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A Bitbucketből Kantega SSO hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>A Bitbucketből Kantega SSO hozzáadása a gyűjteményből
A Bitbucketből Kantega SSO integrálása az Azure AD konfigurálása, kell hozzáadnia a Bitbucketből Kantega SSO a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Bitbucketből Kantega SSO a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Kantega SSO a Bitbucketből**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_search.png)

5. Az eredmények panelen válassza ki a **Kantega SSO a Bitbucketből**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD az egyszeri bejelentkezés Kantega egyszeri bejelentkezési modellel a Bitbucketből "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Kantega SSO Bitbucket az a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Bitbucketből Kantega SSO közötti kapcsolat kapcsolatot kell létrehozni.

Bitbucket Kantega egyszeri Bejelentkezést, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés Kantega egyszeri bejelentkezési modellel a Bitbucketből tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy Kantega SSO Bitbucket teszt felhasználó létrehozása](#creating-a-kantega-sso-for-bitbucket-test-user)**  - való egy megfelelője a Britta Simon Kantega SSO a Bitbucketből, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Kantega SSO Bitbucket alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD az egyszeri bejelentkezés Kantega egyszeri bejelentkezési modellel a Bitbucketből, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Kantega SSO a Bitbucketből** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_samlbase.png)

3. A **IDP** indítható módban, a **Kantega SSO Bitbucket tartomány és az URL-címek** szakasz hajtsa végre a következő lépést:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url1.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

4. A **SP** kezdeményezett módban, a jelölőnégyzet **megjelenítése speciális URL-beállításainak** és hajtsa végre a következő lépést:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url2.png)
    
    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ezeket az értékeket az oktatóanyag későbbi részében ismertetett Bitbucket beépülő modul konfigurálása során érkezik.

5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/tutorial_general_400.png)

7. Egy másik webes böngészőablakban jelentkezzen be a Bitbucketből felügyeleti portálon rendszergazdaként.

8. Kattintson a ikonjára, majd kattintson a **található új bővítmények**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon1.png)

9. Keresési **Kantega SSO Bitbucket SAML & Kerberos** kattintson **telepítése** az új SAML-alapú beépülő modul telepítése gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon2.png)

10. A beépülő modul telepítésének megkezdése.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon31.png)

11. A telepítés befejeződése után. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon33.png)

12. Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon34.png)
    
13. Kattintson a **konfigurálása** konfigurálása az új beépülő modul.    

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon35.png)

14. Az a **SAML** szakasz. Válassza ki **Azure Active Directory (Azure AD)** a a **Hozzáadás identitásszolgáltató** legördülő menüből.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon4.png)

15. Válassza ki az előfizetési szinten **alapvető**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon5.png)

16. Az a **alkalmazás tulajdonságainak** csoportjában hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Másolás a **App ID URI** értékét, és használja azt **azonosító, a válasz URL-CÍMEN és a bejelentkezési URL-cím** a a **Kantega SSO Bitbucket tartomány és az URL-címek** szakaszban az Azure portálon.

    b. Kattintson a **Tovább** gombra.

17. Az a **metaadatok importálása** csoportjában hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Válassza ki **metaadat-fájlt a számítógépen lévő**, és az Azure-portálról letöltött feltöltés metaadatait tartalmazó fájl.

    b. Kattintson a **Tovább** gombra.

18. Az a **nevét és az egyszeri bejelentkezés hely** csoportjában hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Adja hozzá az identitásszolgáltató neve **identitás szolgáltatónevet** szövegmező (például az Azure AD).

    b. Kattintson a **Tovább** gombra.

19. Ellenőrizze a tanúsítvány aláírása, és kattintson **következő**.  

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon9.png)

20. Az a **Bitbucket felhasználói fiókok** csoportjában hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. Válassza ki **felhasználók bitbucket szolgáltatásokhoz tartozó belső könyvtárban szükség esetén hozzon létre** , és írja be a felhasználói csoport megfelelő neve (lehet több nem. vesszővel elválasztott csoportok).

    b. Kattintson a **Tovább** gombra.

21. Kattintson a **Befejezés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon11.png)

22. Az a **tartományok ismert az Azure AD** csoportjában hajtsa végre a következő lépéseket: 

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon12.png)

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

    ![Az Azure AD tesztfelhasználó létrehozása](./media/kantegassoforbitbucket-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/kantegassoforbitbucket-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/kantegassoforbitbucket-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/kantegassoforbitbucket-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-kantega-sso-for-bitbucket-test-user"></a>Egy Kantega SSO a Bitbucketből tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Bitbucket bejelentkezni, akkor ki kell építenie Bitbucket be. A Bitbucketből Kantega egyszeri Bejelentkezést egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Bitbucketből vállalati webhely.

2. Kattintson a beállítások ikonra.

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user1.png) 

3. A **felügyeleti** szakasz lapra, majd **felhasználók**.

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user2.png)

4. Kattintson a **a felhasználó létrehozása**.

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user3.png)   

5. Az a **felhasználó létrehozása** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. Az a **felhasználónév** szövegmezőben, az e-mailt a felhasználó típusát, például Brittasimon@contoso.com.
    
    b. Az a **teljes nevét** szövegmező, a felhasználó például Britta Simon típus teljes nevét.
    
    c. Az a **E-mail cím** szövegmező, a felhasználó e-mail címe típusát, például Brittasimon@contoso.com.

    d. Az a **jelszó** szövegmező, írja be a felhasználó jelszavát.  

    e. Az a **jelszó megerősítése** szövegmezőhöz felhasználói jelszó megerősítése.

    f. Kattintson a **a felhasználó létrehozása**.   

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban Azure egyszeri bejelentkezéshez használandó hozzáférést biztosít a Bitbucketből Kantega SSO Britta Simon engedélyezi.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése a Bitbucketből Kantega SSO, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Kantega SSO a Bitbucketből**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Kattintva a Kantega SSO Bitbucket csempe a hozzáférési panelen, meg kell beolvasása automatikusan bejelentkezett a Kantega egyszeri Bejelentkezést a Bitbucketből alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_203.png

