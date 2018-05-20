---
title: 'Oktatóanyag: Azure Active Directoryval integrált Netsuite |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Netsuite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2018
ms.author: jeedes
ms.openlocfilehash: 2f2bd227a3d8c0b797f37026032938fbcfe3de9e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Oktatóanyag: Azure Active Directoryval integrált Netsuite

Ebben az oktatóanyagban elsajátíthatja Netsuite integrálása az Azure Active Directory (Azure AD).

Netsuite integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Netsuite hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Netsuite (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Netsuite, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Netsuite egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Netsuite hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-netsuite-from-the-gallery"></a>A gyűjteményből Netsuite hozzáadása
Az Azure AD integrálása a Netsuite konfigurálásához kell hozzáadnia Netsuite a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Netsuite hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Netsuite**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_search.png)

5. Az eredmények panelen válassza ki a **Netsuite**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Netsuite

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Netsuite a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Netsuite közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** Netsuite a.

Az Azure AD egyszeri bejelentkezést a Netsuite tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Netsuite tesztfelhasználó létrehozása](#creating-a-netsuite-test-user)**  - való Britta Simon valami Netsuite, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Netsuite alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Netsuite, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Netsuite** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_samlbase.png)

3. Az a **Netsuite tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_url.png)

    Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:   `https://<tenant-name>.netsuite.com/saml2/acs` `https://<tenant-name>.na1.netsuite.com/saml2/acs` `https://<tenant-name>.na2.netsuite.com/saml2/acs` `https://<tenant-name>.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    > [!NOTE] 
    > Ezek a valódi értékek nem. Frissítheti ezeket az értékeket a tényleges válasz URL-címet. Ügyfél [Netsuite támogatási csoport](http://www.netsuite.com/portal/services/support.shtml) beolvasni ezeket az értékeket.
 
4. Az a **SAML-aláíró tanúsítványa** kattintson **metaadatainak XML-kódja** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-netsuite-tutorial/tutorial_general_400.png)

6. A a **Netsuite konfigurációs** kattintson **konfigurálása Netsuite** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_configure.png) 

7. Új lap megnyitása a böngészőben, és jelentkezzen be rendszergazdaként egy vállalat Netsuite webhelyét.

8. A lap felső eszköztárán kattintson **telepítő**, majd kattintson a **Telepítéskezelő**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

9. Az a **beállítási feladatok** listáról válassza ki **integrációs**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Netsuite-tutorial/ns-integration.png)

10. Az a **kezelése hitelesítési** területén kattintson **SAML-alapú egyszeri bejelentkezést**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Netsuite-tutorial/ns-saml.png)

11. Az a **SAML-alapú telepítő** lapon, a következő lépésekkel:
   
    a. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** értéket **rövid összefoglaló** szakasza **bejelentkezés konfigurálása** és illessze be azt a **szolgáltató bejelentkezési identitás Lap** Netsuite mezőbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png)
  
    b. Válassza ki a Netsuite, **elsődleges hitelesítési módszert**.

    c. A mező feliratú **SAMLV2 Identity Provider metaadatok**, jelölje be **IDP metaadatait tartalmazó fájl feltöltése**. Kattintson a **Tallózás** feltöltése az Azure-portálról letöltött metaadatait tartalmazó fájl.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png)

    d. Kattintson a **nyújt**.

12. Az Azure ad-ben, kattintson a **nézet és egyéb felhasználói attribútumok szerkesztése** jelölőnégyzetet, és adja hozzá attribútumot.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Netsuite-tutorial/ns-attributes.png)

13. Az a **attribútumnév** mezőbe írja be a `account`. Az a **attribútumérték** mezőbe írja be a Netsuite fiók azonosítójaként. Ezt az értéket az állandót és módosítási fiókkal. A Fiókazonosító található útmutatást az alábbiakban találhatók:

      ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Netsuite-tutorial/ns-add-attribute.png)

    a. Netsuite, kattintson **telepítő** a felső navigációs menüjében.

    b. Csoportjában kattintson a **beállítási feladatok** a bal oldali navigációs menü szakasza a **integrációs** szakaszt, és kattintson a **Web Services beállítások**.

    c. A Netsuite Fiókazonosító másolja és illessze be azt a **attribútumérték** mezőjét az Azure ad-ben.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Netsuite-tutorial/ns-account-id.png)

14. Mielőtt a felhasználók Netsuite történő egyszeri bejelentkezéshez hajthatnak végre, akkor először meg kell adni a megfelelő engedélyek szükségesek a Netsuite. Kövesse az alábbi utasításokat hozzá ezeket az engedélyeket.

    a. Kattintson a felső navigációs menü **telepítő**, majd kattintson a **Telepítéskezelő**.
      
      ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    b. Válassza a bal oldali navigációs menü **felhasználók vagy szerepkörök**, majd kattintson a **szerepkörök kezelése**.
      
      ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Netsuite-tutorial/ns-manage-roles.png)

    c. Kattintson a **új szerepkör**.

    d. Írja be a **neve** az új szerepkörhöz.
      
      ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Netsuite-tutorial/ns-new-role.png)

    e. Kattintson a **Save** (Mentés) gombra.

    f. Kattintson a felső menüben **engedélyek**. Kattintson a **telepítő**.
      
       ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Netsuite-tutorial/ns-sso.png)

    g. Válassza ki **beállítva fel SAML-alapú egyszeri bejelentkezést**, és kattintson a **Hozzáadás**.

    h. Kattintson a **Save** (Mentés) gombra.

    i. Kattintson a felső navigációs menü **telepítő**, majd kattintson a **Telepítéskezelő**.
      
       ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    j. Válassza a bal oldali navigációs menü **felhasználók vagy szerepkörök**, majd kattintson a **felhasználók kezelése**.
      
       ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Netsuite-tutorial/ns-manage-users.png)

    k. Válassza ki a tesztfelhasználó számára. Kattintson a **szerkesztése**.
      
       ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Netsuite-tutorial/ns-edit-user.png)

    l. Szerepkörök párbeszédpanelen válassza ki a szerepkör, amely hozott létre, és kattintson a **Hozzáadás**.
      
       ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Netsuite-tutorial/ns-add-role.png)

    m. Kattintson a **Save** (Mentés) gombra.
    
### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-netsuite-tutorial/create_aaduser_01.png) 

2.  Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-netsuite-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-netsuite-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-netsuite-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra. 

### <a name="creating-a-netsuite-test-user"></a>Netsuite tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználó Britta Simon nevű Netsuite jön létre. Netsuite támogatja közvetlenül az időponthoz kötött kiosztást, amely alapértelmezés szerint engedélyezve van.
Nincs ebben a szakaszban az Ön művelet elem. Ha a felhasználó nem létezik a Netsuite, egy új Netsuite elérésére tett kísérlet során jön létre.


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Netsuite Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Netsuite, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Netsuite**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Az egyszeri bejelentkezés beállításainak ellenőrzéséhez nyissa meg a hozzáférési panelre a [ https://myapps.microsoft.com ](https://myapps.microsoft.com/), jelentkezzen be a fiókot, és kattintson a **Netsuite**.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)
* [A felhasználók átadása konfigurálása](active-directory-saas-netsuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_203.png

