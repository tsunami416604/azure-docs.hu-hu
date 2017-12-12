---
title: "Oktatóanyag: Azure Active Directoryval integrált @Task|} Microsoft Docs"
description: "Egyszeri bejelentkezés Azure Active Directory közötti konfigurálásával és @Task."
services: active-directory
documentationcenter: 
author: jeevansd
manager: mtillman
editor: 
ms.assetid: aab8bd2f-f9dd-42da-a18e-d707865687d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: 2443c7e2e57bd9c36af292658ff85a973d5ec224
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-task"></a>Oktatóanyag: Azure Active Directory integrációja@Task
Ez az oktatóanyag célja mutatjuk be, hogyan integrálható @Task az Azure Active Directoryval (Azure AD).  
Integrálása @Task az Azure AD lehetővé teszi a következő előnyöket biztosítja: 

* Szabályozhatja, aki az Azure AD-ben@Task
* Engedélyezheti a felhasználók automatikusan el a bejelentkezett @Task (egyszeri bejelentkezés) a saját Azure AD-fiókok
* Kezelheti a fiókokat, egy központi helyen - a klasszikus Azure portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek
Az Azure AD integrálása konfigurálása @Task, a következőkre van szüksége:

* Az Azure AD szolgáltatásra
* Egy @Task egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.
> 
> 

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

* Ne használja az éles környezetben, ha ez nem szükséges.
* Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Forgatókönyv leírása
Ez az oktatóanyag célja ahhoz, hogy egy tesztkörnyezetben az Azure AD az egyszeri bejelentkezés tesztelése.  
A forgatókönyv ebben az oktatóanyagban leírt három fő építőelemeket áll:

1. Hozzáadás @Task a gyűjteményből 
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-task-from-the-gallery"></a>Hozzáadás @Task a gyűjteményből
Az integráció konfigurálásához @Task az Azure AD hozzá kell adnia @Task a felügyelt SaaS-alkalmazások listájára a gyűjteményből.

**Hozzáadandó @Task a gyűjteményből, hajtsa végre a következő lépéseket:**

1. Az a **a klasszikus Azure portálon**, a bal oldali navigációs ablaktábláján kattintson **Active Directory**. 
   
    ![Active Directory][1] 
2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.
3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
    ![Alkalmazások][2] 
4. Kattintson a **Hozzáadás** az oldal alján.
   
    ![Alkalmazások][3] 
5. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **hozzáadhat egy alkalmazást a katalógusból**.
   
    ![Alkalmazások][4] 
6. Írja be a keresőmezőbe,  **@Task** .
   
    ![Alkalmazások][5] 
7. Az eredmények ablaktáblájában válassza  **@Task** , és kattintson a **Complete** hozzáadása az alkalmazáshoz.
   
    ![Alkalmazások][30] 

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ez a szakasz az a célja, hogy bemutatják az Azure AD egyszeri bejelentkezést, tesztelése és konfigurálása @Task "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez, az Azure AD tudnia kell, mely a partner felhasználót a @Task van egy olyan felhasználó számára az Azure ad-ben. Ez azt jelenti, az Azure AD-felhasználó és a kapcsolódó felhasználó a közötti kapcsolat kapcsolatot @Task kell létrehozni.   
Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a @Task.

Az Azure AD egyszeri bejelentkezést, tesztelése és konfigurálása a @Task, végre kell hajtania a következő építőelemeket:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Létrehozása egy @Tasktest felhasználói](#creating-a-halogen-software-test-user)**  - kell rendelkeznie a Britta Simon valami @Taskthat csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD-egyszeri bejelentkezés konfigurálása
Ez a szakasz célja az Azure AD az egyszeri bejelentkezés a klasszikus Azure portálon engedélyezése és konfigurálása egyszeri bejelentkezéshez a a @Task alkalmazás.

**Konfigurálhatja az Azure AD egyszeri bejelentkezést a @Task, hajtsa végre a következő lépéseket:**

1. A klasszikus Azure portálon a a  **@Task**  alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a **konfigurálása egyszeri bejelentkezéshez** párbeszédpanel.
   
    ![Egyszeri bejelentkezés konfigurálása][6] 
2. Az a **hová felhasználók jelentkezhetnek be @Task**  lapon jelölje be **az Azure AD az egyszeri bejelentkezés**, és kattintson a **tovább**.
   
    ![Az Azure AD-egyszeri bejelentkezés][7] 
3. Az a **Alkalmazásbeállítások konfigurálása** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Alkalmazásbeállítások konfigurálása][8] 
   
     a. Az a **URL-cím bejelentkezési** szövegmező, írja be az URL-címet használják-e a felhasználók bejelentkezés a @Task alkalmazás (pl.:*https://<Tenant name>.attask-ondemand.com*).
   
     b. Kattintson a **Tovább** gombra.
4. A a **konfigurálása egyszeri bejelentkezéshez, @Task**  kattintson **metaadatok letöltése**, mentse helyileg a számítógépen a metaadatok fájlt, és kattintson a **következő**.
   
    ![Mi az az Azure AD Connect?][9] 
5. Bejelentkezés a @Task vállalati hely rendszergazdaként.
6. Ugrás a **az egyszeri bejelentkezés beállítása**.
7. Az a **egyszeri bejelentkezés** párbeszédpanelen hajtsa végre az alábbi lépéseket
   
    ![Egyszeri bejelentkezés konfigurálása][23]
   
    a. Mint **típus**, jelölje be **SAML 2.0**.
   
    b. Válassza ki **Szolgáltatóazonosító szolgáltatás**.
   
    c. A klasszikus Azure portálra, másolja a **távoli bejelentkezési URL-cím**, és illessze be azt a **bejelentkezési portál URL-cím** szövegmező.
   
    d. A klasszikus Azure portálra, másolja a **egyetlen Sign-Out URL-címe**, majd illessze be azt a **Sign-Out URL-cím** szövegmező.
   
    e. A klasszikus Azure portálra, másolja a **jelszó URL-cím módosítása**, majd illessze be azt a **jelszó URL-cím módosítása** szövegmező.
   
    f. Kattintson a **Save** (Mentés) gombra.
8. A klasszikus Azure portálon, válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **következő**. 
   
    ![Mi az az Azure AD Connect?][10]
9. Az a **az egyszeri bejelentkezés megerősítő** kattintson **Complete**.  
   
    ![Mi az az Azure AD Connect?][11]

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása a klasszikus Azure portálon Britta Simon nevezik.  

![Az Azure AD-felhasználó létrehozása][20]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **a klasszikus Azure portálon**, a bal oldali navigációs ablaktábláján kattintson **Active Directory**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 
2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.
3. A felhasználók listájának megjelenítéséhez a felső menüben, kattintson a **felhasználók**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
4. Lehetőségre a **felhasználó hozzáadása** párbeszédpanel alján, az eszköztárban kattintson **felhasználó hozzáadása**. 
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 
5. Az a **adja meg azt a felhasználó** párbeszédpanel lapon, a következő lépésekkel: 
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 
   
    a. A felhasználó típusát válassza ki az új felhasználót a szervezetében.
   
    b. A felhasználó nevében **szövegmező**, típus **BrittaSimon**.
   
    c. Kattintson a **Tovább** gombra.
6. Az a **felhasználói profil** párbeszédpanel lapon, a következő lépésekkel: 
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
   
    a. Az a **Utónév** szövegmezőhöz típus **Britta**.  
   
    b. Az a **Vezetéknév** szövegmezőhöz típusa, **Simon**.
   
    c. Az a **megjelenített név** szövegmezőhöz típus **Britta Simon**.
   
    d. Az a **szerepkör** listáról válassza ki **felhasználói**.

    e. Kattintson a **Tovább** gombra.

7. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lap, kattintson a **létrehozása**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
8. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
   
    a. Jegyezze fel az értéket a **új jelszó**.
   
    b. Kattintson a **Befejezés** gombra.   

### <a name="creating-an-task-test-user"></a>Létrehozása egy @Task tesztfelhasználó számára
Ez a szakasz célja Britta Simon nevű felhasználó létrehozásához @Task.

**Britta Simon nevű felhasználó létrehozásához @Task, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a @Task vállalati hely rendszergazdaként.
2. Kattintson a felső menüben **személyek**.
3. Kattintson a **új személy**. 
4. Új személy párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Hozzon létre egy @Task tesztfelhasználó számára][21] 
   
    a. Az a **Keresztnév** szövegmező, írja be a "Britta".
   
    b. Az a **Vezetéknév** szövegmező, írja be a "Simon".
   
    c. Az a **E-mail cím** szövegmező, írja be a Britta Simon e-mail címét az Azure Active Directoryban.
   
    d. Kattintson a **személy hozzáadása**.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése
Ez a szakasz az a célja, hogy Britta Simon szerint saját való hozzáférés biztosítása használandó Azure egyszeri bejelentkezés engedélyezése @Task.

![Felhasználó hozzárendelése][200] 

**A Britta Simon hozzárendelni @Task, hajtsa végre a következő lépéseket:**

1. A klasszikus Azure portálon, a könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
    ![Felhasználó hozzárendelése][201] 
2. Az alkalmazások listában válassza ki a  **@Task** .
   
    ![Felhasználó hozzárendelése][202] 
3. Kattintson a felső menüben **felhasználók**.
   
    ![Felhasználó hozzárendelése][203] 
4. A felhasználók listában válassza ki a **Britta Simon**.
5. Kattintson az alsó eszköztár **hozzárendelése**.
   
    ![Felhasználó hozzárendelése][205]

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése
Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.  
Ha a @Task csempére a hozzáférési panelen kapja meg automatikusan bejelentkezett a a @Task alkalmazás.

## <a name="additional-resources"></a>További erőforrások
* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png






