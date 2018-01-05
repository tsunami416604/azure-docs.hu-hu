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
ms.date: 01/03/2018
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 6e8167c1152fe80813d5c13706a72badce0a0ce9
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-halosys"></a>Oktatóanyag: Azure Active Directoryval integrált Halosys

Ebben az oktatóanyagban elsajátíthatja Halosys integrálása az Azure Active Directory (Azure AD).

Halosys integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Halosys hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Halosys (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

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

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

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

Ebben a szakaszban az Azure AD egyszeri bejelentkezéssel a portálon, és konfigurálása egyszeri bejelentkezéshez az Halosys alkalmazásban.


**Konfigurálása az Azure AD az egyszeri bejelentkezés Halosys, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **SCC életciklus** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scclifecycle-tutorial/tutorial_scclifecycle_samlbase.png)

3. Az a **Halosys tartomány és az URL-címek** területen tegye a következőket:
    1. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<sub-domain>.hs.com/ic7/welcome/customer/PICTtest.aspx`

    2. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:
    | |
    |--|--|
    | `https://bs1.hs.com/<entity>`|
    | `https://lifecycle.hs.com/<entity>`|
    
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [SCC életciklus ügyfél-támogatási csoport](mailto:lifecycle.support@scc.com) beolvasni ezeket az értékeket. 
         
4. A a **SAML-aláíró tanúsítványa** szakaszban jelölje be **metaadatainak XML-kódja** alatt **letöltése**, és mentse a metaadat-fájlt a számítógépen.
   
5. Ahhoz, hogy az egyszeri bejelentkezés az alkalmazáshoz konfigurált, lépjen kapcsolatba a terméktámogatással Halosys és adja meg a következő:

  * A letöltött **metaadatait tartalmazó fájl**
  * A **SAML SSO URL-címe**
    

  >[!NOTE]
  >Egyszeri bejelentkezés ki engedélyezni lehessen az Halosys támogatási csapatával.


### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ebben a szakaszban a portálon Britta Simon nevű tesztfelhasználó létrehozása.


![Az Azure AD-felhasználó létrehozása][20]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-scclifecycle-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-scclifecycle-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-scclifecycle-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-scclifecycle-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.


### <a name="creating-a-halosys-test-user"></a>Halosys tesztfelhasználó létrehozása

Ebben a szakaszban egy Halosys Britta Simon nevű felhasználót hoz létre. A felhasználók hozzáadása a Halosys platform Halosys támogatási csapattal működik.


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított a hozzáférés Halosys Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Halosys, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon, az alkalmazások nézet megnyitásához a könyvtár nézet megnyitása és Ugrás **vállalati alkalmazások** kattintson **összes alkalmazást.**

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Halosys**.

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra a **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra a **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Halosys csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Halosys alkalmazására. A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).


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
[202]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_205.png
 