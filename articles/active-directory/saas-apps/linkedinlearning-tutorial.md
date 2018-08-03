---
title: 'Oktatóanyag: A LinkedIn Learning Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a LinkedIn Learning között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 02e7d9d26b389e82365f3447cceb5566244236f1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440908"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Oktatóanyag: A LinkedIn Learning Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan LinkedIn Learning integrálása az Azure Active Directory (Azure AD).

LinkedIn Learning integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a LinkedIn Learning az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett LinkedIn Learning (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a LinkedIn Learning, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A LinkedIn Learning egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadás a katalógusból, LinkedIn Learning
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-linkedin-learning-from-the-gallery"></a>Hozzáadás a katalógusból, LinkedIn Learning
Az Azure AD integrálása a LinkedIn Learning konfigurálásához hozzá kell LinkedIn Learning a galériából a felügyelt SaaS-alkalmazások listájára.

**LinkedIn Learning hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **LinkedIn Learning**. Az eredmények panelen kattintson a **LinkedIn Learning** , vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálni, és a LinkedIn Learning a teszt "Britta Simon" nevű felhasználó az Azure AD egyszeri bejelentkezés teszteléséhez.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a LinkedIn Learning tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a LinkedIn Learning hivatkozás kapcsolatát kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** LinkedIn Learning.

Az Azure AD egyszeri bejelentkezés a LinkedIn Learning tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[LinkedIn Learning tesztfelhasználó létrehozása](#creating-a-linkedin-learning-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a LinkedIn Learning alkalmazás egyszeri bejelentkezés konfigurálása.

**A LinkedIn Learning konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **LinkedIn Learning** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/tutorial-linkedin_01.png)

1. Egy másik böngészőablakban bejelentkezés, a LinkedIn Learning bérlői rendszergazdaként.

1. A **Account Center**, kattintson a **globális beállítások** alatt **beállítások**. Jelölje be **tanulás – alapértelmezett** a legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

1. Kattintson a **, vagy kattintson ide betöltése, és másolja ki az egyes mezők az űrlap** és másolási **entitásazonosító** és **helyességi feltétel fogyasztói Access (ACS) URL-címe**

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

1. Az Azure Portal alatt **LinkedIn Learning tartomány és URL-címek**, kövesse az alábbi lépéseket, ha szeretné az egyszeri bejelentkezést **identitásszolgáltató által kezdeményezett** mód

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_01.png)

    a. Az a **azonosító** szövegmezőbe írja be a **Entitásazonosító** LinkedIn Portalról másolt 

    b. Az a **válasz URL-cím** szövegmezőbe írja be a **helyességi feltétel fogyasztói Access (ACS) URL-cím** LinkedIn Portalról másolt

1. Ha szeretné az egyszeri bejelentkezést **SP által kezdeményezett**, majd kattintson a speciális URL-cím megjelenítése beállítás a konfigurációs szakaszban, és a bejelentkezési URL-cím konfigurálása a következő mintának:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_02.png)   
    
1. LinkedIn Learning alkalmazását a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőfelvételen látható erre egy példa látható. Az alapértelmezett érték **felhasználóazonosító** van **user.userprincipalname** , de a LinkedIn Learning vár ezt a képezhető le a felhasználó e-mail-címmel. Használhatja, amely **user.mail** attribútumot a listából, vagy használja a megfelelő attribútum értéket, a szervezet konfiguráció alapján. 

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/updateusermail.png)
    
1. A **felhasználói attribútumok** területén kattintson **megtekintése és egyéb felhasználói attribútumok szerkesztése** és attribútumainak beállítása. A felhasználónak kell nevű négy jogcímek hozzáadása **e-mail**, **részleg**, **firstname**, és **lastname** értéke pedig leképezve**user.mail**, **user.department**, **user.givenname**, és **user.surname** osztályban

    | Attribútum neve | Attribútum értéke |
    | --- | --- |
    | e-mailben| user.mail |    
    | Szervezeti egység| User.Department |
    | Keresztnév| User.givenName |
    | Vezetéknév| User.surname |
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinlearning-tutorial/userattribute.png)
    
    a. Kattintson a **attribútum hozzáadása** a attribútum párbeszédpanel megnyitásához.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinlearning-tutorial/tutorial_attribute_04.png)

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinlearning-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson a **Ok**

1. Hajtsa végre a következő lépéseket a **neve** attribútum -

    a. Az attribútum megnyitásához kattintson a **attribútum szerkesztése** ablak.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/url_update.png)

    b. Az URL-Címének értékét törli a **névtér**.
    
    c. Kattintson a **Ok** a beállítás mentéséhez.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_certificate.png)

1. Kattintson a **Save** (Mentés) gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/tutorial_general_400.png)

1. Lépjen a **LinkedIn adminisztrátori beállítások** szakaszban. A feltöltés XML-fájl lehetőségre kattintva az Azure Portalról letöltött XML-fájl feltöltése.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

1. Kattintson a **a** egyszeri bejelentkezés engedélyezéséhez. Egyszeri bejelentkezés állapota változik **nincs csatlakoztatva** való **csatlakoztatva**

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinlearning-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinlearning-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinlearning-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinlearning-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-a-linkedin-learning-test-user"></a>LinkedIn Learning tesztfelhasználó létrehozása

LinkedIn Learning alkalmazás támogatja a csak az idő felhasználókiépítés, miután a felhasználók hitelesítésére automatikusan létrehozza az alkalmazásban. A rendszergazda a beállítások lapot a LinkedIn Learning portál helyezné a kapcsoló **rendelhet hozzá licenceket** az aktív igény szerinti üzembe helyezést, és ez lesz is rendeljen egy licencet a felhasználó.

   ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával LinkedIn Learning Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**LinkedIn Learning Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

1. Az alkalmazások listájában jelölje ki a **LinkedIn Learning**.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_0001.png)

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a LinkedIn Learning csempére kattint, szerezheti be az Azure bejelentkezési oldalra, és a sikeres bejelentkezést követően fog kapni a LinkedIn Learning alkalmazásba.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/linkedinlearning-tutorial/tutorial_general_01.png
[2]: ./media/linkedinlearning-tutorial/tutorial_general_02.png
[3]: ./media/linkedinlearning-tutorial/tutorial_general_03.png
[4]: ./media/linkedinlearning-tutorial/tutorial_general_04.png

[100]: ./media/linkedinlearning-tutorial/tutorial_general_100.png

[200]: ./media/linkedinlearning-tutorial/tutorial_general_200.png
[201]: ./media/linkedinlearning-tutorial/tutorial_general_201.png
[202]: ./media/linkedinlearning-tutorial/tutorial_general_202.png
[203]: ./media/linkedinlearning-tutorial/tutorial_general_203.png
