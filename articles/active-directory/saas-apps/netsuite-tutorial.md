---
title: 'Oktatóanyag: Azure Active Directory-integráció az NetSuite |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és NetSuite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: jeedes
ms.openlocfilehash: 511fdcf587d16a59ff2bb11dfc55504b2218a569
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431408"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Oktatóanyag: Azure Active Directory-integráció az NetSuite

Ebben az oktatóanyagban elsajátíthatja, hogyan NetSuite integrálása az Azure Active Directory (Azure AD).

NetSuite integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá NetSuite Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett NetSuite (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

NetSuite az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy NetSuite egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. NetSuite hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-netsuite-from-the-gallery"></a>NetSuite hozzáadása a katalógusból
Az Azure AD integrálása a NetSuite konfigurálásához hozzá kell NetSuite a katalógusból a felügyelt SaaS-alkalmazások listájára.

**NetSuite hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]

1. Kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **NetSuite**válassza **NetSuite** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában NetSuite](./media/netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó NetSuite az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó NetSuite mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó NetSuite hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** NetSuite a.

Az Azure AD egyszeri bejelentkezés az NetSuite tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[NetSuite tesztfelhasználó létrehozása](#creating-a-netsuite-test-user)**  – egy megfelelője a Britta Simon NetSuite, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és NetSuite alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés NetSuite, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **NetSuite** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/tutorial_NetSuite_samlbase.png)

1. Az a **NetSuite tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/tutorial_NetSuite_url.png)

    Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`
    
    > [!NOTE]
    > Ezek nem tényleges értékek állnak. Frissítse a tényleges válasz URL-cím ezeket az értékeket. Kapcsolattartó [NetSuite támogatási csapatának](http://www.NetSuite.com/portal/services/support.shtml) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/tutorial_NetSuite_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/tutorial_general_400.png)

1. Az a **NetSuite konfigurációs** területén kattintson **konfigurálása NetSuite** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/tutorial_NetSuite_configure.png)

1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be rendszergazdaként vállalati NetSuite webhelyét.

1. Kattintson az eszköztáron az oldal tetején lévő **telepítő**, majd keresse meg a **vállalati** kattintson **szolgáltatások engedélyezése a**.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setupsaml.png)

1. Kattintson az eszköztáron az oldal közepén, **SuiteCloud**.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-suitecloud.png)

1. Alatt **kezelése hitelesítési** szakaszban jelölje be **SAML EGYSZERI bejelentkezés** NetSuite az SAML EGYSZERI bejelentkezés beállítás engedélyezéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-ticksaml.png)

1. Kattintson az eszköztáron az oldal tetején lévő **telepítő**.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setup.png)

1. Az a **beállítási feladatok** listáról **integrációs**.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-integration.png)

1. Az a **kezelése hitelesítési** területén kattintson **SAML egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-saml.png)

1. Az a **SAML-telepítő** lap **NetSuite konfigurációs** szakaszban a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Válassza ki **elsődleges hitelesítési módszert**.

    b. A címkével ellátott mező **SAMLV2 IDENTITY PROVIDER METAADATOK**válassza **Identitásszolgáltató METAADATFÁJL FELTÖLTÉSE**. Kattintson a **Tallózás** az Azure Portalról letöltött metaadatfájl feltöltése.

    c. Kattintson a **elküldése**.

1. Az Azure AD-ben kattintson a **megtekintése és egyéb felhasználói attribútumok szerkesztése** jelölőnégyzetet, és adja hozzá a attribútum.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-attributes.png)

1. Az a **attribútumnév** mezőbe írja be a `account`. Az a **attribútumérték** mezőbe írja be a NetSuite azonosítót. Ezt az értéket az állandó, és módosítsa a fiókot. Most a Fiókazonosítójával található útmutatást az alábbiakban találhatók:

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-add-attribute.png)

    a. NetSuite, kattintson **telepítő** majd keresse meg a **vállalati** kattintson **Cégadatok** a felső navigációs menüjében.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-com.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-account-id.png)

    b. Az a **Cégadatok** lapon a jobb oldali oszlopban példányán a **Fiókazonosító**.

    c. Illessze be a **Fiókazonosító** amely másolta NetSuite fiókból be azt a **attribútumérték** mezőt az Azure ad-ben. 

1. Mielőtt a felhasználók NetSuite való egyszeri bejelentkezés hajthat végre, azok először meg kell adni a megfelelő engedélyekkel a NetSuite. Kövesse az alábbi utasításokat a hozzá ezeket az engedélyeket.

    a. Kattintson a felső navigációs menü **telepítő**.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setup.png)

    b. Válassza a bal oldali navigációs menü **felhasználók/szerepkörök**, majd kattintson a **szerepkörök kezelése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Kattintson a **új szerepkör**.

    d. Írjon be egy **neve** az új szerepkörhöz.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-new-role.png)

    e. Kattintson a **Save** (Mentés) gombra.

    f. A felső menüben kattintson **engedélyek**. Kattintson a **telepítő**.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-sso.png)

    g. Válassza ki **SAML egyszeri bejelentkezés**, és kattintson a **Hozzáadás**.

    h. Kattintson a **Save** (Mentés) gombra.

    i. Kattintson a felső navigációs menü **telepítő**, majd kattintson a **Telepítéskezelő**.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setup.png)

    j. Válassza a bal oldali navigációs menü **felhasználók/szerepkörök**, majd kattintson a **felhasználók kezelése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Válassza ki egy tesztfelhasználót. Kattintson a **szerkesztése** , majd lépjen **hozzáférés** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-edit-user.png)

    l. A szerepkörök párbeszédpanelen rendelje hozzá a megfelelő szerepkör, amely létrehozta.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-add-role.png)

    m. Kattintson a **Save** (Mentés) gombra.
 
### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/NetSuite-tutorial/create_aaduser_01.png) 

1.  A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/NetSuite-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/NetSuite-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/NetSuite-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra. 

### <a name="creating-a-netsuite-test-user"></a>NetSuite tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó NetSuite jön létre. NetSuite támogatja a just-in-time-kiépítés, amely alapértelmezés szerint engedélyezve van.
Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó már nem létezik az NetSuite, amikor megpróbálja elérni NetSuite egy új jön létre.


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés NetSuite Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel NetSuite, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **NetSuite**.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/tutorial_NetSuite_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha tesztelni szeretné az egyszeri bejelentkezés beállításai, nyissa meg a hozzáférési Panel Itt [ https://myapps.microsoft.com ](https://myapps.microsoft.com/), jelentkezzen be a teszt-fiókjába, és kattintson **NetSuite**.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](NetSuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/NetSuite-tutorial/tutorial_general_01.png
[2]: ./media/NetSuite-tutorial/tutorial_general_02.png
[3]: ./media/NetSuite-tutorial/tutorial_general_03.png
[4]: ./media/NetSuite-tutorial/tutorial_general_04.png

[100]: ./media/NetSuite-tutorial/tutorial_general_100.png

[200]: ./media/NetSuite-tutorial/tutorial_general_200.png
[201]: ./media/NetSuite-tutorial/tutorial_general_201.png
[202]: ./media/NetSuite-tutorial/tutorial_general_202.png
[203]: ./media/NetSuite-tutorial/tutorial_general_203.png

