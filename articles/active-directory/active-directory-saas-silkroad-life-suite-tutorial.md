---
title: "Oktatóanyag: Azure Active Directory-integráció SilkRoad élettartama Suite |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és SilkRoad élettartama Suite között."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
ms.openlocfilehash: ecf4e31ecea00d003fc47ea4cebb781ca58957f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Oktatóanyag: Azure Active Directory-integráció SilkRoad élettartama csomaggal
Ez az oktatóanyag célja SilkRoad élettartama Suite integrálása az Azure Active Directory (Azure AD) mutatjuk be. 

SilkRoad élettartama Suite integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja: 

* Megadhatja a SilkRoad élettartama Suite hozzáféréssel rendelkező Azure AD-ben 
* Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a SilkRoad élettartama Suite egyszeri bejelentkezés (SSO) és az Azure AD-fiókok

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek
Az Azure AD-integráció konfigurálása SilkRoad élettartama csomaggal, a következőkre van szükség:

* Az Azure AD szolgáltatásra
* Egy SilkRoad élettartama Suite SSO előfizetés engedélyezése

>[!NOTE]
>Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben. 
> 

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

* Ne használja az éles környezetben, ha ez nem szükséges.
* Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, beszerezheti a [egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Forgatókönyv leírása
Ez az oktatóanyag célja ahhoz, hogy az Azure AD SSO teszteléséhez tesztkörnyezetben.

Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből SilkRoad élettartama csomag hozzáadása 
2. Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-silkroad-life-suite-from-the-gallery"></a>A gyűjteményből SilkRoad élettartama csomag hozzáadása
Az Azure AD integrálása a SilkRoad élettartama Suite konfigurálásához kell hozzáadnia SilkRoad élettartama Suite a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből SilkRoad élettartama Suite hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a **a klasszikus Azure portálon**, a bal oldali navigációs ablaktábláján kattintson **Active Directory**. 
   
    ![Active Directory][1]

2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.

3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
    ![Alkalmazások][2]

4. Kattintson a **Hozzáadás** az oldal alján.
   
    ![Alkalmazások][3]

5. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **hozzáadhat egy alkalmazást a katalógusból**.
   
    ![Alkalmazások][4]

6. Írja be a keresőmezőbe, **SilkRoad élettartama Suite**.
   
    ![Alkalmazások][5]

7. Az eredmények ablaktáblájában válassza **SilkRoad élettartama Suite**, és kattintson a **Complete** hozzáadása az alkalmazáshoz.
   
    ![Alkalmazások][50]

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ez a szakasz célja bemutatják a Azure AD SSO SilkRoad élettartama Suite "Britta Simon" nevű tesztfelhasználó alapján tesztelése és konfigurálása.

Az egyszeri bejelentkezés működjön az Azure AD tudnia kell, a partner felhasználó SilkRoad élettartama csomagban található egy olyan felhasználó számára az Azure ad-ben van. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó SilkRoad élettartama csomagban található hivatkozás kapcsolatának kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** SilkRoad élettartama csomagban található.

Az Azure AD az egyszeri bejelentkezés SilkRoad élettartama Suite tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[SilkRoad élettartama Suite tesztfelhasználó létrehozása](#creating-a-silkroad-life-suite-test-user)**  - való egy megfelelője a Britta Simon SilkRoad élettartama csomag, amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása
Ez a szakasz célja engedélyezze az Azure AD egyszeri Bejelentkezést a klasszikus Azure portálon és egyszeri bejelentkezés konfigurálása az SilkRoad élettartama Suite alkalmazásban.

**Konfigurálja az Azure AD az egyszeri bejelentkezés SilkRoad élettartama csomaggal, hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés a SilkRoad vállalati webhely rendszergazdaként. 

  >[!NOTE] 
  > A Microsoft Azure AD-összevonás konfigurálása az SilkRoad élettartama Suite hitelesítési alkalmazáshoz való hozzáférési, forduljon SilkRoad támogatási vagy az Ön SilkRoad szolgáltatások képviselőjével.
  > 

2. Ugrás a **szolgáltató**, és kattintson a **összevonási részletek**. 
   
    ![Az Azure AD-egyszeri bejelentkezés][10] 

3. Kattintson a **töltse le az összevonási metaadatok**, majd mentse a metaadatok fájlt a számítógépen.
   
    ![Az Azure AD-egyszeri bejelentkezés][11] 

4. A klasszikus Azure portálon a a **SilkRoad élettartama Suite** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a **konfigurálása egyszeri bejelentkezéshez** párbeszédpanel.
   
    ![Egyszeri bejelentkezés konfigurálása][6] 

5. Az a **hová bejelentkezni SilkRoad élettartama Suite felhasználók** lapon jelölje be **az Azure AD az egyszeri bejelentkezés**, és kattintson a **tovább**.
   
    ![Az Azure AD-egyszeri bejelentkezés][7] 

6. Az a **Alkalmazásbeállítások konfigurálása** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Az Azure AD-egyszeri bejelentkezés][8]   
 1. Az a **URL-cím bejelentkezési** szövegmező, írja be az URL-címet használják-e a felhasználók bejelentkezés SilkRoad élettartama Suite webhelyekhez (pl.: *https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*).  
 2. Nyissa meg a letöltött **Silkroad** metaadatait tartalmazó fájl. 
 3. Keresse meg a **AssertionConsumerService** címkét, és másolja a **hely** attribútum.         
   
    ![Az Azure AD-egyszeri bejelentkezés][21] 
 4. Illessze be az érték a **válasz URL-CÍMEN** szövegmező.  
 5. Kattintson a **Tovább** gombra.

6. Az a **konfigurálhatja az egyszeri bejelentkezés SilkRoad élettartama Suite** lapon, a következő lépésekkel:
   
    ![Az Azure AD-egyszeri bejelentkezés][9]  
 1. Kattintson a letöltés tanúsítványt, és mentse a fájlt a számítógépen.  
 2. Kattintson a **Tovább** gombra.

7. Az a **SilkRoad** alkalmazás, kattintson a **hitelesítési források**.
   
    ![Az Azure AD-egyszeri bejelentkezés][12] 

8. Kattintson a **hitelesítési forrás hozzáadása**. 
   
    ![Az Azure AD-egyszeri bejelentkezés][13] 

9. Az a **hitelesítési forrás hozzáadása** területen tegye a következőket: 
   
    ![Az Azure AD-egyszeri bejelentkezés][14]  
 1. A **beállítás 2 - metaadatfájl**, kattintson a **Tallózás** feltölteni a fájlt a letöltött metaadat.  
 2. Kattintson a **fájladatok használatával hozzon létre identitásszolgáltató**.

10. Az a **hitelesítési források** kattintson **szerkesztése**. 
    
     ![Az Azure AD-egyszeri bejelentkezés][15] 

11. Az a **szerkesztése hitelesítési forrás** párbeszédpanelen hajtsa végre a következő lépéseket: 
    
     ![Az Azure AD-egyszeri bejelentkezés][16] 
 1. Mint **engedélyezve**, jelölje be **Igen**.   
 2. Az a **IdP leírás** szövegmező, adja meg a konfiguráció leírása (pl.: *Azure AD SSO*).  
 3. Az a **IdP neve** szövegmező, írja be a konfigurációs adott nevét (pl.: *Azure SP*).  
 4. Kattintson a **Save** (Mentés) gombra.

12. Tiltsa le a többi hitelesítési forrást. 
    
     ![Az Azure AD-egyszeri bejelentkezés][17]

13. A klasszikus Azure portálon a a **az egyszeri bejelentkezés megerősítő** kattintson **következő**.  
    
     ![Az Azure AD-egyszeri bejelentkezés][18]

14. Az a **az egyszeri bejelentkezés megerősítő** kattintson **Complete**.
    
     ![Az Azure AD-egyszeri bejelentkezés][19]

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó létrehozása a klasszikus Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][20]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **a klasszikus Azure portálon**, a bal oldali navigációs ablaktábláján kattintson **Active Directory**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png)  

2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.

3. A felhasználók listájának megjelenítéséhez a felső menüben, kattintson a **felhasználók**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png) 

4. Lehetőségre a **felhasználó hozzáadása** párbeszédpanel alján, az eszköztárban kattintson **felhasználó hozzáadása**. 
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png) 

5. Az a **adja meg azt a felhasználó** párbeszédpanel lapon, a következő lépésekkel: 
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png)  
 1. A felhasználó típusát válassza ki az új felhasználót a szervezetében.  
 2. A felhasználó nevében **szövegmező**, típus **BrittaSimon**. 
 3. Kattintson a **Tovább** gombra.

6. Az a **felhasználói profil** párbeszédpanel lapon, a következő lépésekkel: 
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png)  
 1. Az a **Utónév** szövegmezőhöz típus **Britta**.    
 2. Az a **Vezetéknév** szövegmezőhöz típusa, **Simon**. 
 3. Az a **megjelenített név** szövegmezőhöz típus **Britta Simon**. 
 4. Az a **szerepkör** listáról válassza ki **felhasználói**.
 5. Kattintson a **Tovább** gombra.

7. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lap, kattintson a **létrehozása**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png) 

8. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png)  
 1. Jegyezze fel az értéket a **új jelszó**. 
 2. Kattintson a **Befejezés** gombra.   

### <a name="create-a-silkroad-life-suite-test-user"></a>SilkRoad élettartama Suite tesztfelhasználó létrehozása
Ez a szakasz célja Britta Simon SilkRoad élettartama Suite nevű felhasználót létrehozni. Britta tartozó egyszeri bejelentkezési Azonosítóval kell rendelkeznie (más néven egy *AuthParam*), amely megfelel a Britta **emailaddress** Azure AD-ben.

**A felhasználó Britta Simon SilkRoad élettartama Suite nevű létrehozásához hajtsa végre az alábbi lépéseket:**

- Kérje meg a SilkRoad élettartama Suite támogatási csoport, amely rendelkezik felhasználó létrehozásához **egyszeri bejelentkezési azonosító** attribútum értéke megegyezik a **emailaddress** a Britta Simon Azure AD-ben.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó
Ez a szakasz célja Britta Simon Azure SSO saját hozzáférést biztosít SilkRoad élettartama Suite által használandó engedélyezéséhez.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése SilkRoad élettartama Suite, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon, a könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SilkRoad élettartama Suite**.
   
    ![Felhasználó hozzárendelése][202] 

3. Kattintson a felső menüben **felhasználók**.
   
    ![Felhasználó hozzárendelése][203] 

4. A felhasználók listában válassza ki a **Britta Simon**.

5. Kattintson az alsó eszköztár **hozzárendelése**.
   
    ![Felhasználó hozzárendelése][205]

### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése
Ez a szakasz célja a hozzáférési panelen az Azure AD SSO-konfigurációjának tesztelése.  

Ha a hozzáférési panelen SilkRoad élettartama Suite csempére kattint, meg kell beolvasása automatikusan bejelentkezett az SilkRoad élettartama Suite alkalmazására.

## <a name="additional-resources"></a>További források
* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png





