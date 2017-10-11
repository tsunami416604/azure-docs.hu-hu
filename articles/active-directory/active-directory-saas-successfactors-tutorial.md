---
title: "Oktatóanyag: Azure Active Directoryval integrált SuccessFactors |} Microsoft Docs"
description: "Megtudhatja, hogyan SuccessFactors használata az Azure Active Directoryval az egyszeri bejelentkezés, automatizált üzembe helyezést és további engedélyezéséhez!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: e85a38ccbe25263ac42bc76351416b023fb77c87
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Oktatóanyag: Azure Active Directoryval integrált SuccessFactors
Ez az oktatóanyag célja SuccessFactors integrálása az Azure Active Directory (Azure AD) mutatjuk be.

SuccessFactors integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

* Megadhatja a SuccessFactors hozzáféréssel rendelkező Azure AD-ben
* Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett SuccessFactors (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
* Kezelheti a fiókokat, egy központi helyen - a klasszikus Azure portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek
Konfigurálása az Azure AD-integrációs SuccessFactors, a következőkre van szükség:

* Egy érvényes Azure-előfizetés
* A bérlő által SuccessFactors

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.
> 
> 

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

* Ne használja az éles környezetben, ha ez nem szükséges.
* Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ez az oktatóanyag célja ahhoz, hogy egy tesztkörnyezetben az Azure AD az egyszeri bejelentkezés tesztelése.

Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből SuccessFactors hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-successfactors-from-the-gallery"></a>A gyűjteményből SuccessFactors hozzáadása
Az Azure AD integrálása a SuccessFactors konfigurálásához kell hozzáadnia SuccessFactors a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből SuccessFactors hozzáadásához hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon, a bal oldali navigációs panelen, kattintson a **Active Directory**.
   
    ![Egyszeri bejelentkezés konfigurálása][1]
2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.
3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
    ![Egyszeri bejelentkezés konfigurálása][2]
4. Kattintson a **Hozzáadás** az oldal alján.
   
    ![Alkalmazások][3]
5. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **hozzáadhat egy alkalmazást a katalógusból**.
   
    ![Egyszeri bejelentkezés konfigurálása][4]
6. Az a **keresőmezőbe**, típus **SuccessFactors**.
   
    ![Egyszeri bejelentkezés konfigurálása][5]
7. Az eredmények panelen válassza ki a **SuccessFactors**, és kattintson a **Complete** hozzáadása az alkalmazáshoz.
   
    ![Egyszeri bejelentkezés konfigurálása][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ez a szakasz célja bemutatják a konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján SuccessFactors.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó SuccessFactors egy olyan felhasználó számára az Azure ad-ben van. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a SuccessFactors közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** SuccessFactors a.

Az Azure AD egyszeri bejelentkezést a SuccessFactors tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[SuccessFactors tesztfelhasználó létrehozása](#creating-a-successfactors-test-user)**  - való Britta Simon valami SuccessFactors, amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása
Ebben a szakaszban az Azure AD egyszeri bejelentkezéssel a klasszikus portálon, és konfigurálása egyszeri bejelentkezéshez az SuccessFactors alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés SuccessFactors, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon a a **SuccessFactors** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
   
    ![Egyszeri bejelentkezés konfigurálása][7]
2. Az a **hová bejelentkezni SuccessFactors felhasználók** lapon jelölje be **Microsoft Azure AD az egyszeri bejelentkezés**, és kattintson a **következő**.
   
    ![Egyszeri bejelentkezés konfigurálása][8]
3. Az a **alkalmazás URL-cím konfigurálása** lapon hajtsa végre az alábbi lépéseket, és kattintson a **következő**.
   
    ![Egyszeri bejelentkezés konfigurálása][9]
   
    a. Az a **URL-cím bejelentkezési** szövegmezőhöz használatával egyike annak a következő URL-címet írja be: 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
   
    b. Az a **válasz URL-CÍMEN** szövegmezőhöz használatával egyike annak a következő URL-címet írja be: 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
    | `https://<company name>.sapsf.eu/<company name>` |
   
    c. Kattintson a **Tovább** gombra. 

    > [!NOTE]
    > Ne feledje, hogy ezek nincsenek a valódi értékek. Akkor frissítheti ezeket az értékeket a tényleges URL-cím bejelentkezési és válasz URL-CÍMEN. Ahhoz, hogy ezeket az értékeket, lépjen kapcsolatba [SuccessFactors támogatási csoport](https://www.successfactors.com/en_us/support.html).

1. A a **konfigurálhatja az egyszeri bejelentkezés SuccessFactors** lapján kattintson **tanúsítvánnyal letöltés**, és mentse helyileg a számítógépen a tanúsítványfájlt.
   
    ![Egyszeri bejelentkezés konfigurálása][10]

2. Egy másik webes böngészőablakban, jelentkezzen be a **SuccessFactors felügyeleti portál** rendszergazdaként.

3. Látogasson el **alkalmazásbiztonsági** és natív módon **egyszeri bejelentkezést a szolgáltatás**. 

4. Helyezze el az értéket a **Token alaphelyzetbe** kattintson **Token mentése** SAML SSO engedélyezése.
   
    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása][11]

    > [!NOTE] 
    > Ezt az értéket csak használja, mint a be-és kikapcsolása kapcsolót. Ha bármely érték menti, a SAML SSO ON értékre állítva. Ha egy üres érték. menti a SAML SSO OFF állásban.

1. Natív módon alábbi képernyőfelvétel és az alábbi műveletek végrehajtását.
   
    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása][12]
   
    a. Válassza ki a **SAML v2 SSO** választógomb
   
    b. Állítsa be a SAML biztos fél Name(e.g. SAml issuer + company name).
   
    c. Az a **SAML kibocsátó** szövegmezőbe írja be az értéket a **kiállítójának URL-címe** az Azure AD alkalmazás-konfigurációs varázsló.
   
    d. Válassza ki **választ (felhasználói generált/IdP/AP)** , **kötelező kötelező aláírás**.
   
    e. Válassza ki **engedélyezett** , **SAML jelző engedélyezése**.
   
    f. Válassza ki **nem** , **bejelentkezési kérelem-aláírás (KB generált/SP/RP)**.
   
    g. Válassza ki **böngésző/utólagos profil** , **SAML profil**.
   
    h. Válassza ki **nem** , **kényszerítése a tanúsítvány érvényes időtartama**.
   
    i. Másolja a letöltött tanúsítvány fájl tartalmát, és illessze be azt a **SAML ellenőrzése tanúsítvány** szövegmező.

    > [!NOTE] 
    > A tanúsítványok tartalmát kell kezdődniük tanúsítványt és a záró tanúsítvány címkék.

1. Navigáljon a SAML V2, és végezze el az alábbi lépéseket:
   
    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása][13]
   
    a. Válassza ki **Igen** , **támogatja a Szolgáltató által kezdeményezett globális kijelentkezési**.
   
    b. Az a **globális kijelentkezési URL-címe (LogoutRequest cél)** szövegmezőbe írja be az értéket a **távoli kijelentkezési URL-cím** az Azure AD alkalmazás-konfigurációs varázsló.
   
    c. Válassza ki **nem** , **sp titkosítani kell az összes NameID elem szükséges**.
   
    d. Válassza ki **meghatározatlan** , **NameID formátum**.
   
    e. Válassza ki **Igen** , **engedélyezése sp kezdeményezett (AuthnRequest) bejelentkezési**.
   
    f. Az a **küldési kérelmek vállalati szintű kiállítóként** szövegmezőbe írja be az értéket a **távoli bejelentkezési URL-cím** az Azure AD alkalmazás-konfigurációs varázsló.
2. Hajtsa végre ezeket a lépéseket, ha engedélyezni szeretné a bejelentkezési felhasználónevek-és nagybetűket,.
   
    a. Látogasson el **vállalati beállítások**(közelében alsó).
   
    b. Jelölje be a jelölőnégyzetet közelében **engedélyezése nem-nagybetűk felhasználónév**.
   
    c.Click **mentése**.
   
    ![Egyszeri bejelentkezés konfigurálása][29]

    > [!NOTE] 
    > Ezzel a kísérli meg, ha a rendszer ellenőrzi, ha az létrehoz egy duplikált SAML-bejelentkezési nevet. Például ha az ügyfél rendelkezik felhasználónevek felhasználó1, mind a felhasználó1. Ezeket az ismétlődéseket véve számítógépnél nagybetűk teszi. A rendszer erre azért van szükség egy hibaüzenet, és nem engedélyezi a szolgáltatást. Az ügyfél módosítania kell a felhasználónevek egyik, a ténylegesen elképzelhető különböző. 

1. A klasszikus Azure portálon, válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **Complete** bezárásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
   
    ![Alkalmazások][14]
2. Az a **az egyszeri bejelentkezés megerősítő** kattintson **Complete**.
   
    ![Alkalmazások][15]

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása Britta Simon neve a klasszikus portálon.

![Az Azure AD-felhasználó létrehozása][16]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **a klasszikus Azure portálon**, a bal oldali navigációs ablaktábláján kattintson **Active Directory**.
   
    ![Az Azure AD tesztfelhasználó létrehozása][17]
2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.
3. A felhasználók listájának megjelenítéséhez a felső menüben, kattintson a **felhasználók**.
   
    ![Az Azure AD tesztfelhasználó létrehozása][18]
4. Lehetőségre a **felhasználó hozzáadása** párbeszédpanel alján, az eszköztárban kattintson **felhasználó hozzáadása**.
   
    ![Az Azure AD tesztfelhasználó létrehozása][19]
5. Az a **adja meg azt a felhasználó** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Az Azure AD tesztfelhasználó létrehozása][20]
   
    a. A felhasználó típusát válassza ki az új felhasználót a szervezetében.
   
    b. A felhasználó nevében **szövegmező**, típus **BrittaSimon**.
   
    c. Kattintson a **Tovább** gombra.
6. Az a **felhasználói profil** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Az Azure AD tesztfelhasználó létrehozása][21]
   
    a. Az a **Utónév** szövegmezőhöz típus **Britta**.  
   
    b. Az a **Vezetéknév** szövegmezőhöz típusa, **Simon**.
   
    c. Az a **megjelenített név** szövegmezőhöz típus **Britta Simon**.
   
    d. Az a **szerepkör** listáról válassza ki **felhasználói**.
   
    e. Kattintson a **Tovább** gombra.
7. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lap, kattintson a **létrehozása**.
   
    ![Az Azure AD tesztfelhasználó létrehozása][22]
8. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Az Azure AD tesztfelhasználó létrehozása][23]
   
    a. Jegyezze fel az értéket a **új jelszó**.
   
    b. Kattintson a **Befejezés** gombra.  

### <a name="creating-a-successfactors-test-user"></a>SuccessFactors tesztfelhasználó létrehozása
Ahhoz, hogy az Azure AD-felhasználók SuccessFactors bejelentkezni, akkor ki kell építenie SuccessFactors be.  
SuccessFactors, ha egy kézi tevékenység.

Ahhoz, hogy a felhasználó hozott létre az SuccessFactors, kapcsolatba kell lépnie a [SuccessFactors támogatási csoport](https://www.successfactors.com/en_us/support.html).

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése
Ez a szakasz célja Britta Simon által biztosított a hozzáférés SuccessFactors használandó Azure egyszeri bejelentkezés engedélyezése.

![Felhasználó hozzárendelése][24]

**Britta Simon hozzárendelése SuccessFactors, hajtsa végre az alábbi lépéseket:**

1. A klasszikus portál megnyitásához az alkalmazások megtekintése a könyvtár nézetben kattintson **alkalmazások** a felső menüben.
   
    ![Felhasználó hozzárendelése][25]
2. Az alkalmazások listában válassza ki a **SuccessFactors**.
   
    ![Egyszeri bejelentkezés konfigurálása][26]
3. Kattintson a felső menüben **felhasználók**.
   
    ![Felhasználó hozzárendelése][27]
4. A felhasználók listában válassza ki a **Britta Simon**.
5. Kattintson az alsó eszköztár **hozzárendelése**.
   
    ![Felhasználó hozzárendelése][28]

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése
Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen SuccessFactors csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az SuccessFactors alkalmazására.

## <a name="additional-resources"></a>További források
* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_00.png
[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_01.png
[6]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_02.png
[7]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_03.png
[8]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_04.png
[9]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_05.png
[10]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_06.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png

[16]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_00.png
[17]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png
[18]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png
[19]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png
[20]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png
[21]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_05.png
[22]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_06.png
[23]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_07.png

[24]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_07.png
[25]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_08.png
[26]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_11.png
[27]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_09.png
[28]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_10.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png
