---
title: "Oktatóanyag: Azure Active Directoryval integrált tárgyában |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és tárgyában között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6aa771e9-31c6-48d1-8dde-024bebc06943
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: 327cc1e3d0836e79376e0a3cd5a4422a967f5943
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-humanity"></a>Oktatóanyag: Azure Active Directoryval integrált tárgyában

Ebben az oktatóanyagban elsajátíthatja tárgyában integrálása az Azure Active Directory (Azure AD).

Tárgyában integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a tárgyában hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett tárgyában (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs tárgyában, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy tárgyában egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből tárgyában hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-humanity-from-the-gallery"></a>A gyűjteményből tárgyában hozzáadása
Az Azure AD-be tárgyában integrálása konfigurálásához kell hozzáadnia tárgyában a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből tárgyában hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **tárgyában**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_search.png)

5. Az eredmények panelen válassza ki a **tárgyában**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján tárgyában a

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó tárgyában a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a tárgyában közötti kapcsolat kapcsolatot kell létrehozni.

Tárgyában, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Tárgyában az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Tárgyában tesztfelhasználó létrehozása](#creating-a-humanity-test-user)**  - való egy megfelelője a Britta Simon tárgyában, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az tárgyában alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés tárgyában, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **tárgyában** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_samlbase.png)

3. Az a **tárgyában tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://company.humanity.com/includes/saml/`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://company.humanity.com/app/`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [tárgyában ügyfél-támogatási csoport](https://www.humanity.com/support/) beolvasni ezeket az értékeket. 
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_400.png)

6. A a **tárgyában konfigurációs** kattintson **konfigurálása tárgyában** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe és Sign-Out URL-cím** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_configure.png) 

7. Egy másik webes böngészőablakban, jelentkezzen be a **tárgyában** vállalati hely rendszergazdaként.

8. Kattintson a felső menüben **Admin**.
   
    ![Felügyeleti](./media/active-directory-saas-shiftplanning-tutorial/iC786619.png "rendszergazda")

9. A **integrációs**, kattintson a **egyszeri bejelentkezés**.
   
    ![Egyszeri bejelentkezés](./media/active-directory-saas-shiftplanning-tutorial/iC786620.png "egyszeri bejelentkezés")

10. Az a **egyszeri bejelentkezés** területen tegye a következőket:
   
    ![Egyszeri bejelentkezés](./media/active-directory-saas-shiftplanning-tutorial/iC786905.png "egyszeri bejelentkezés")
   
    a. Válassza ki **SAML engedélyezett**.

    b. Válassza ki **engedélyezte a bejelentkezést jelszó**.

    c. Beillesztés a **SAML-alapú egyszeri bejelentkezési URL-címe** be értéket a **SAML kiállítójának URL-címe** szövegmező.

    d. Beillesztés a **Sign-Out URL-cím** be értéket a **távoli kijelentkezési URL-cím** szövegmező.
   
    e. Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben, a tartalmának másolása a vágólapra és illessze be azt a **X.509 tanúsítvány** szövegmező.

11. Kattintson a **beállítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-humanity-test-user"></a>Tárgyában tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók tárgyában bejelentkezni, akkor ki kell építenie tárgyában történő. Tárgyában, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **tárgyában** vállalati hely rendszergazdaként.

2. Kattintson a **Admin**.
   
    ![Felügyeleti](./media/active-directory-saas-shiftplanning-tutorial/iC786619.png "rendszergazda")

3. Kattintson a **személyzet**.
   
    ![Személyzet](./media/active-directory-saas-shiftplanning-tutorial/ic786623.png "személyzet")

4. A **műveletek**, kattintson a **adja hozzá az alkalmazottak**.
   
    ![Adja hozzá az alkalmazottak](./media/active-directory-saas-shiftplanning-tutorial/iC786624.png "alkalmazottak hozzáadása")

5. Az a **adja hozzá az alkalmazottak** területen tegye a következőket:
   
    ![Az alkalmazottak mentése](./media/active-directory-saas-shiftplanning-tutorial/iC786625.png "alkalmazottak mentése")
   
    a. Típus a **Utónév**, **Vezetéknév**, és **E-mail** szeretné azokat a kapcsolódó szövegmezők rendelkezés érvényes AAD-fiók.

    b. Kattintson a **alkalmazottak mentése**.

>[!NOTE]
>Bármely más tárgyában felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz tárgyában által nyújtott API-k.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés tárgyában Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése tárgyában, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **tárgyában**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen tárgyában csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az tárgyában alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_203.png

