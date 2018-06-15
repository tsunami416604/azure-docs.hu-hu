---
title: 'Oktatóanyag: Azure Active Directoryval integrált Syncplicity |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Syncplicity között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 29581fda8cb27989518f6a3d5c69e1cfac763ede
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34351647"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Oktatóanyag: Azure Active Directoryval integrált Syncplicity

Ebben az oktatóanyagban elsajátíthatja Syncplicity integrálása az Azure Active Directory (Azure AD).

Syncplicity integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Syncplicity hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Syncplicity (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Syncplicity, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Syncplicity egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Syncplicity hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-syncplicity-from-the-gallery"></a>A gyűjteményből Syncplicity hozzáadása
Az Azure AD integrálása a Syncplicity konfigurálásához kell hozzáadnia Syncplicity a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Syncplicity hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Syncplicity**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_search.png)

5. Az eredmények panelen válassza ki a **Syncplicity**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Syncplicity

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Syncplicity a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Syncplicity közötti kapcsolat kapcsolatot kell létrehozni.

Syncplicity, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Syncplicity tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Syncplicity tesztfelhasználó létrehozása](#creating-a-syncplicity-test-user)**  - való Britta Simon valami Syncplicity, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Syncplicity alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Syncplicity, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Syncplicity** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

3. Az a **Syncplicity tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.syncplicity.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Syncplicity ügyfél-támogatási csoport](https://www.syncplicity.com/contact-us) beolvasni ezeket az értékeket. 
 

4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-syncplicity-tutorial/tutorial_general_400.png)

6. A a **Syncplicity konfigurációs** kattintson **konfigurálása Syncplicity** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_configure.png) 

7. Jelentkezzen be a **Syncplicity** bérlő.

8. A felső menüben kattintson a **admin**, jelölje be **beállítások**, és kattintson a **egyéni tartomány és az egyszeri bejelentkezés**.
   
    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/ic769545.png "Syncplicity")

9. Az a **egyszeri bejelentkezés (SSO)** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés \(egyszeri bejelentkezés\)](./media/active-directory-saas-syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a. Az a **egyéni tartomány** szövegmező, írja be annak a tartománynak a nevét.
  
    b. Válassza ki **engedélyezett** , **az egyszeri bejelentkezés állapot**.

    c. Az a **entitásazonosító** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta.

    d. Az a **bejelentkezési URL-címe** szövegmező, illessze be a **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.

    e. Az a **kijelentkezési URL-címe** szövegmező, illessze be a **Sign-Out URL-cím** ami Azure-portálon másolta.

    f. A **szolgáltató Identitástanúsítvány**, kattintson a **fájl kiválasztása**, majd töltse fel az Azure-portálról letöltött tanúsítványt. 

    g. Kattintson a **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-syncplicity-test-user"></a>Syncplicity tesztfelhasználó létrehozása
Az AAD-felhasználókat kell jelentkezhetnek be akkor ki kell építenie Syncplicity alkalmazás. Ez a szakasz ismerteti, hogyan AAD felhasználói fiókok létrehozása a Syncplicity.

**Egy felhasználói fiókot Syncplicity kiépítéséhez, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Syncplicity** bérlő (például: `https://company.Syncplicity.com`).

2. Kattintson a **admin** válassza **felhasználói fiókok**.

3. Kattintson a **hozzáadni egy felhasználót**.
   
    ![Felhasználók kezelése](./media/active-directory-saas-syncplicity-tutorial/ic769764.png "felhasználók kezelése")

4. Típus a **E-mail címről** egy AAD-fiókba rendelkezés szeretne, válassza ki **felhasználói** , **szerepkör**, és kattintson a **következő**.
   
    ![Fiókadatok](./media/active-directory-saas-syncplicity-tutorial/ic769765.png "fiókadatok")
   
    >[!NOTE]
    >Az aad-ben fióktulajdonos kap egy e-mailt hivatkozással erősítse meg, és aktiválja a fiókot. 
    > 

5. Válasszon ki egy csoportot, amely az új felhasználót kell tagjává válik, és kattintson a vállalat **következő**.
   
    ![Csoporttagságát](./media/active-directory-saas-syncplicity-tutorial/ic769772.png "csoporttagságát")
   
    >[!NOTE]
    >Ha nincsenek felsorolva csoportok, kattintson a **következő**. 
    > 

6. Jelölje ki azokat a mappákat a felhasználó Syncplicity tartozó vezérlőelem alá helyezni, és kattintson a kívánt **következő**.
   
    ![Syncplicity mappák](./media/active-directory-saas-syncplicity-tutorial/ic769773.png "Syncplicity mappák")

>[!NOTE]
>Bármely más Syncplicity felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz Syncplicity által nyújtott API-k. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Syncplicity Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Syncplicity, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Syncplicity**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen Syncplicity csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Syncplicity alkalmazására.
## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_203.png

