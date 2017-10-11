---
title: "Oktatóanyag: Azure Active Directoryval integrált SciQuest töltött igazgató |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és SciQuest töltött igazgató között."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
ms.openlocfilehash: 84b707668dc45e92e6151f422f1c919f638533b1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>Oktatóanyag: Azure Active Directoryval integrált SciQuest töltött igazgató
Ez az oktatóanyag célja SciQuest töltött igazgató integrálása az Azure Active Directory (Azure AD) mutatjuk be.  
SciQuest töltött igazgató integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja: 

* Megadhatja a SciQuest töltött igazgató hozzáféréssel rendelkező Azure AD-ben 
* Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett SciQuest töltött igazgató (egyszeri bejelentkezés)
* Kezelheti a fiókokat, egy központi helyen - a klasszikus Azure portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek
Konfigurálása az Azure AD-integrációs SciQuest töltött igazgató, a következőkre van szükség:

* Az Azure AD szolgáltatásra
* Egy SciQuest töltött igazgató egyszeri bejelentkezés engedélyezve van az előfizetésben

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

1. A gyűjteményből SciQuest töltött igazgató hozzáadása 
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>A gyűjteményből SciQuest töltött igazgató hozzáadása
Az Azure AD integrálása a SciQuest töltött igazgató konfigurálásához kell hozzáadnia SciQuest töltött igazgató a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből SciQuest töltött igazgató hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a **a klasszikus Azure portálon**, a bal oldali navigációs ablaktábláján kattintson **Active Directory**. 
   
    ![Active Directory][1]

2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.

3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
    ![Alkalmazások][2]

4. Kattintson a **Hozzáadás** az oldal alján.
   
    ![Alkalmazások][3]

5. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **hozzáadhat egy alkalmazást a katalógusból**.
   
    ![Alkalmazások][4]

6. Írja be a keresőmezőbe, **sciQuest töltött igazgató**.
   
    ![Alkalmazások][5]

7. Az eredmények ablaktáblájában válassza **SciQuest töltött igazgató**, és kattintson a **Complete** az alkalmazás hozzáadása.
   
    ![Alkalmazások][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ez a szakasz célja bemutatják a konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés SciQuest töltött igazgató "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó SciQuest töltött igazgató egy olyan felhasználó számára az Azure ad-ben van. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó SciQuest töltött Director közötti kapcsolat kapcsolatot kell létrehozni.  
Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** SciQuest töltött Director.

Az Azure AD egyszeri bejelentkezést a SciQuest töltött igazgató tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD egy egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[SciQuest töltött igazgató tesztfelhasználó létrehozása](#creating-a-halogen-software-test-user)**  - való egy megfelelője a Britta Simon SciQuest töltött igazgató, amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Az Azure AD egy egyszeri bejelentkezés konfigurálása
Ez a szakasz célja engedélyezése az Azure AD az egyszeri bejelentkezés a klasszikus Azure portálon, és konfigurálása egyszeri bejelentkezéshez az SciQuest töltött igazgató alkalmazásban.

**Az Azure AD az egyszeri bejelentkezés konfigurálása SciQuest töltött igazgató, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon a a **SciQuest töltött igazgató** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a **konfigurálása egyszeri bejelentkezéshez** párbeszédpanel.
   
    ![Egyszeri bejelentkezés konfigurálása][8]

2. A a **hová bejelentkezni SciQuest töltött igazgató felhasználók** lapon jelölje be **az Azure AD az egyszeri bejelentkezés**, és kattintson a **következő**.
   
    ![Az Azure AD-egyszeri bejelentkezés][9]

3. Az a **Alkalmazásbeállítások konfigurálása** párbeszédpanel lapon, a következő lépésekkel: 
   
    ![Alkalmazásbeállítások konfigurálása][10]
   
     a. Az a **URL-cím bejelentkezési** szövegmező, írja be az URL-cím segítségével a felhasználók jelentkezzen be a SciQuest töltött igazgató alkalmazást a következő mintát: *https://.* sciquest.com/.**
   
     b. Az a **válasz URL-CÍMEN** szövegmezőhöz beírt ugyanazt az értéket írja be a **URL-cím bejelentkezési** szövegmező. 
   
     c. Kattintson a **Tovább** gombra.

4. A a **konfigurálhatja az egyszeri bejelentkezés SciQuest töltött igazgató** lapján kattintson **metaadatok letöltése**, és mentse helyileg a számítógépen a metaadatait tartalmazó fájl.
   
    ![Mi az az Azure AD Connect?][11]

5. Kapcsolattartási SciQuest támogatja ezt a hitelesítési módszert a fent letöltött metaadatok segítségével engedélyezéséhez.

6. A klasszikus Azure portálon, válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **Complete** bezárásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel. 
   
    ![Mi az az Azure AD Connect?][15]

7. Az a **az egyszeri bejelentkezés megerősítő** kattintson **Complete**.  

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása a klasszikus Azure portálon Britta Simon nevezik.

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **a klasszikus Azure portálon**, a bal oldali navigációs ablaktábláján kattintson **Active Directory**.
   
    ![Mi az az Azure AD Connect?][100] 

2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.

3. A felhasználók listájának megjelenítéséhez a felső menüben, kattintson a **felhasználók**.
   
    ![Mi az az Azure AD Connect?][101] 

4. Lehetőségre a **felhasználó hozzáadása** párbeszédpanel alján, az eszköztárban kattintson **felhasználó hozzáadása**. 
   
    ![Mi az az Azure AD Connect?][102] 

5. Az a **adja meg azt a felhasználó** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Mi az az Azure AD Connect?][103] 
   
    a. Mint **felhasználó típusa**, jelölje be **a szervezet új felhasználó**.
   
    b. A felhasználó nevében **szövegmező**, típus **BrittaSimon**.
   
    c. Kattintson a **Tovább** gombra.

6. Az a **felhasználói profil** párbeszédpanel lapon, a következő lépésekkel: 
   
    ![Mi az az Azure AD Connect?][104] 
   
    a. Az a **Utónév** szövegmezőhöz típus **Britta**.  
   
    b. Az a **Vezetéknév** txtbox, típusa, **Simon**.
   
    c. Az a **megjelenített név** szövegmezőhöz típus **Britta Simon**.
   
    d. Az a **szerepkör** listáról válassza ki **felhasználói**.
   
    e. Kattintson a **Tovább** gombra.

7. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lap, kattintson a **létrehozása**.
   
    ![Mi az az Azure AD Connect?][105]  

8. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Mi az az Azure AD Connect?][106]   
   
    a. Jegyezze fel az értéket a **új jelszó**.
   
    b. Kattintson a **Befejezés** gombra.   

### <a name="creating-a-sciquest-spend-director-test-user"></a>SciQuest töltött igazgató tesztfelhasználó létrehozása
Ez a szakasz célja SciQuest töltött igazgató Britta Simon nevű felhasználót létrehozni.

Szeretné a SciQuest töltött igazgató támogatási csoportjához, és adja meg a teszt fiókját is létre részleteit.

Másik lehetőségként is kihasználhatja just-in-time kiépítés, egyetlen bejelentkezés szolgáltatásként SciQuest töltött igazgató által támogatott.  
Ha közvetlenül az időponthoz kötött kiépítés engedélyezve van, felhasználók automatikusan létrejönnek SciQuest töltött igazgató egy egyszeri bejelentkezési kísérlet során, ha azok még nem léteznek. Ez a funkció nem kell létrehoznia az egyszeri bejelentkezés tartozó felhasználók.

Közvetlenül az időponthoz kötött kiépítés engedélyezve beszerzéséhez kapcsolatba kell lépnie még a SciQuest töltött igazgató támogatási csoportjához.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése
Ez a szakasz célja Britta Simon nyújtó saját SciQuest töltött igazgató használandó Azure egyszeri bejelentkezés engedélyezése.

![Mi az az Azure AD Connect?][200]

**Britta Simon hozzárendelése SciQuest töltött igazgató, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon, a könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
    ![Mi az az Azure AD Connect?][201]

2. Az alkalmazások listában válassza ki a **SciQuest töltött igazgató**.
   
    ![Mi az az Azure AD Connect?][202]

3. Kattintson a felső menüben **felhasználók**.
   
    ![Mi az az Azure AD Connect?][203]

4. A felhasználók listában válassza ki a **Britta Simon**.
   
    ![Mi az az Azure AD Connect?][204]

5. Kattintson az alsó eszköztár **hozzárendelése**.
   
    ![Mi az az Azure AD Connect?][205]

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése
Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.  
Ha a hozzáférési panelen SciQuest töltött igazgató csempére kattint, meg kell beolvasása automatikusan bejelentkezett az SciQuest töltött igazgató alkalmazására.

## <a name="additional-resources"></a>További források
* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_09.png 
[101]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_10.png 
[102]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_11.png 
[103]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_12.png 
[104]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_13.png 
[105]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_14.png 
[106]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_15.png 
[200]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_16.png 
[201]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_17.png 
[202]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_20.png

