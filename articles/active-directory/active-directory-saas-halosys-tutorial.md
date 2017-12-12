---
title: "Oktatóanyag: Azure Active Directoryval integrált Halosys |} Microsoft Docs"
description: "Megtudhatja, hogyan Halosys használata az Azure Active Directoryval az egyszeri bejelentkezés, automatizált üzembe helyezést és további engedélyezéséhez!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: mtillman
ms.assetid: 42a0eb7c-5cb7-44a9-b00b-b0e7df4b63e8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: cfd932fa87ffd40ffc6ac96ad72ae7eac31e0b98
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-halosys"></a>Oktatóanyag: Azure Active Directoryval integrált Halosys

Ebben az oktatóanyagban elsajátíthatja Halosys integrálása az Azure Active Directory (Azure AD).

Halosys integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Halosys hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Halosys (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen - a klasszikus Azure portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Halosys, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Halosys egyszeri bejelentkezés engedélyezve van az előfizetésben


> [!NOTE] 
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.


Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, ha ez nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben.

Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Halosys hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés


## <a name="adding-halosys-from-the-gallery"></a>A gyűjteményből Halosys hozzáadása
Az Azure AD integrálása a Halosys konfigurálásához kell hozzáadnia Halosys a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Halosys hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a **a klasszikus Azure portálon**, a bal oldali navigációs ablaktábláján kattintson **Active Directory**.

    ![Active Directory][1]
2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.

3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.

    ![Alkalmazások][2]

4. Kattintson a **Hozzáadás** az oldal alján.

    ![Alkalmazások][3]

5. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **hozzáadhat egy alkalmazást a katalógusból**.

    ![Alkalmazások][4]

6. Írja be a keresőmezőbe, **Halosys**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_01.png)
    
7. Az eredmények ablaktáblájában válassza **Halosys**, és kattintson a **Complete** hozzáadása az alkalmazáshoz.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_011.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Halosys.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Halosys a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Halosys közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** Halosys a.

Az Azure AD egyszeri bejelentkezést a Halosys tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Halosys tesztfelhasználó létrehozása](#creating-a-halosys-test-user)**  - való Britta Simon valami Halosys, amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezéssel a klasszikus portálon, és konfigurálása egyszeri bejelentkezéshez az Halosys alkalmazásban.


**Konfigurálása az Azure AD az egyszeri bejelentkezés Halosys, hajtsa végre az alábbi lépéseket:**

1. A klasszikus portálon a a **Halosys** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a **konfigurálása egyszeri bejelentkezéshez** párbeszédpanel.
     
    ![Egyszeri bejelentkezés konfigurálása][6] 

2. Az a **hová bejelentkezni Halosys felhasználók** lapon jelölje be **az Azure AD az egyszeri bejelentkezés**, és kattintson a **tovább**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_03.png) 

3. Az a **Alkalmazásbeállítások konfigurálása** párbeszédpanel lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_04.png) 

    a. Az a **URL-cím bejelentkezési** szövegmező, írja be az URL-címet használják-e a felhasználók bejelentkezés a következő minta használatával Halosys Alkalmazásmódosítások: `https://<company-name>.Halosys.com/client-api/api`.

    b.In a **azonosító URL-t** szövegmező, írja be a következő mintát: `https://<company-name>.Halosys.com`.   
         
4. A a **konfigurálhatja az egyszeri bejelentkezés Halosys** lapján kattintson **metaadatok letöltése**, majd mentse a fájlt a számítógépen:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_05.png)
   
5. Ahhoz, hogy az alkalmazáshoz konfigurált SSO, lépjen kapcsolatba a terméktámogatással Halosys és adja meg a következő:

    • A letöltött **metaadatait tartalmazó fájl**
    
    • A **SAML SSO URL-címe**
    

6. A klasszikus portálon, válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **következő**.
    
    ![Az Azure AD-egyszeri bejelentkezés][10]

7. Az a **az egyszeri bejelentkezés megerősítő** kattintson **Complete**.  
 
    ![Az Azure AD-egyszeri bejelentkezés][11]


### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ebben a szakaszban Britta Simon neve a klasszikus portálon tesztfelhasználó létrehozása.


![Az Azure AD-felhasználó létrehozása][20]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **a klasszikus Azure portálon**, a bal oldali navigációs ablaktábláján kattintson **Active Directory**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-Halosys-tutorial/create_aaduser_09.png) 

2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.

3. A felhasználók listájának megjelenítéséhez a felső menüben, kattintson a **felhasználók**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-Halosys-tutorial/create_aaduser_03.png) 

4. Lehetőségre a **felhasználó hozzáadása** párbeszédpanel alján, az eszköztárban kattintson **felhasználó hozzáadása**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-Halosys-tutorial/create_aaduser_04.png) 

5. Az a **adja meg azt a felhasználó** párbeszédpanel lapon, a következő lépésekkel: ![létrehozása az Azure AD-teszt felhasználó](./media/active-directory-saas-Halosys-tutorial/create_aaduser_05.png) 

    a. A felhasználó típusát válassza ki az új felhasználót a szervezetében.

    b. A felhasználó nevében **szövegmező**, típus **BrittaSimon**.

    c. Kattintson a **Tovább** gombra.

6.  Az a **felhasználói profil** párbeszédpanel lapon, a következő lépésekkel: ![létrehozása az Azure AD-teszt felhasználó](./media/active-directory-saas-Halosys-tutorial/create_aaduser_06.png) 

    a. Az a **Utónév** szövegmezőhöz típus **Britta**.  

    b. Az a **Vezetéknév** szövegmezőhöz típusa, **Simon**.

    c. Az a **megjelenített név** szövegmezőhöz típus **Britta Simon**.

    d. Az a **szerepkör** listáról válassza ki **felhasználói**.

    e. Kattintson a **Tovább** gombra.

7. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lap, kattintson a **létrehozása**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-Halosys-tutorial/create_aaduser_07.png) 

8. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lapon, a következő lépésekkel:

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-Halosys-tutorial/create_aaduser_08.png) 

    a. Jegyezze fel az értéket a **új jelszó**.

    b. Kattintson a **Befejezés** gombra.   



### <a name="creating-a-halosys-test-user"></a>Halosys tesztfelhasználó létrehozása

Ebben a szakaszban egy Halosys Britta Simon nevű felhasználót hoz létre. A felhasználók hozzáadása a Halosys platform Halosys támogatási csapattal működik.


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított a hozzáférés Halosys Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Halosys, hajtsa végre az alábbi lépéseket:**

1. A klasszikus portál megnyitásához az alkalmazások megtekintése a könyvtár nézetben kattintson **alkalmazások** a felső menüben.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Halosys**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_50.png) 

3. Kattintson a felső menüben **felhasználók**.

    ![Felhasználó hozzárendelése][203]

4. A felhasználók listában válassza ki a **Britta Simon**.

5. Kattintson az alsó eszköztár **hozzárendelése**.

    ![Felhasználó hozzárendelése][205]


### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Halosys csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Halosys alkalmazására.


## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_205.png
