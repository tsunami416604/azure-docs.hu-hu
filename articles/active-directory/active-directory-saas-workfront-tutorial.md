---
title: "Oktatóanyag: Azure Active Directoryval integrált Workfront |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Workfront között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: aab8bd2f-f9dd-42da-a18e-d707865687d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: f7ba8d4895474de0da0e04da5f31959963ae65ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workfront"></a>Oktatóanyag: Azure Active Directoryval integrált Workfront

Ebben az oktatóanyagban elsajátíthatja Workfront integrálása az Azure Active Directory (Azure AD).

Workfront integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Workfront hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Workfront (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Workfront, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Workfront egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Workfront hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-workfront-from-the-gallery"></a>A gyűjteményből Workfront hozzáadása
Az Azure AD integrálása a Workfront konfigurálásához kell hozzáadnia Workfront a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Workfront hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Workfront**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workfront-tutorial/tutorial_workfront_search.png)

5. Az eredmények panelen válassza ki a **Workfront**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workfront-tutorial/tutorial_workfront_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Workfront

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Workfront a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Workfront közötti kapcsolat kapcsolatot kell létrehozni.

Workfront, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Workfront tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Workfront tesztfelhasználó létrehozása](#creating-a-workfront-test-user)**  - való Britta Simon valami Workfront, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Workfront alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Workfront, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Workfront** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workfront-tutorial/tutorial_workfront_samlbase.png)

3. Az a **Workfront tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workfront-tutorial/tutorial_workfront_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.attask-ondemand.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.attasksandbox.com/SAML2`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Workfront ügyfél-támogatási csoport](https://www.workfront.com/contact-us/) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workfront-tutorial/tutorial_workfront_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workfront-tutorial/tutorial_general_400.png)

6. A a **Workfront konfigurációs** kattintson **konfigurálása Workfront** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workfront-tutorial/tutorial_workfront_configure.png) 

7. Bejelentkezés a Workfront vállalati webhely rendszergazdaként.

8. Ugrás a **az egyszeri bejelentkezés beállítása**.

9. Az a **egyszeri bejelentkezés** párbeszédpanelen hajtsa végre az alábbi lépéseket
    
    ![Egyszeri bejelentkezés konfigurálása][23]
   
    a. Mint **típus**, jelölje be **SAML 2.0**.
   
    b. Válassza ki **Szolgáltatóazonosító szolgáltatás**.
   
    c. Beillesztés a **SAML-alapú egyszeri bejelentkezési URL-címe** azokat a **bejelentkezési portál URL-cím** szövegmező.
   
    d. Beillesztés **egyetlen Sign-Out URL-címe** azokat a **Sign-Out URL-cím** szövegmező.
   
    e. Beillesztés **jelszó URL-cím módosítása** azokat a **jelszó URL-cím módosítása** szövegmező.
   
    f. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workfront-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workfront-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workfront-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workfront-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-workfront-test-user"></a>Workfront tesztfelhasználó létrehozása

Ez a szakasz célja Workfront Britta Simon nevű felhasználót létrehozni.

**A felhasználó Britta Simon meghívta Workfront létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Workfront vállalati webhely.
2. Kattintson a felső menüben **személyek**.
3. Kattintson a **új személy**. 
4. Új személy párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Hozzon létre egy Workfront tesztfelhasználó számára][21] 
   
    a. Az a **Keresztnév** szövegmező, írja be a "Britta."
   
    b. Az a **Vezetéknév** szövegmező, írja be a "Simon."
   
    c. Az a **E-mail cím** szövegmező, írja be a Britta Simon e-mail címét az Azure Active Directoryban.
   
    d. Kattintson a **személy hozzáadása**.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Workfront Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Workfront, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Workfront**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workfront-tutorial/tutorial_workfront_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Workfront csempére kattint, Workfront alkalmazás bejelentkezési oldalán szerezheti be.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-workfront-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workfront-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workfront-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workfront-tutorial/tutorial_general_04.png
[21]:./media/active-directory-saas-workfront-tutorial/tutorial_attask_08.png
[23]:./media/active-directory-saas-workfront-tutorial/tutorial_attask_06.png
[100]: ./media/active-directory-saas-workfront-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workfront-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workfront-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workfront-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workfront-tutorial/tutorial_general_203.png

