---
title: 'Oktatóanyag: Az Azure Active Directory-integráció, a LinkedIn-szintjének emelése |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a LinkedIn jogosultságszint-emelés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 209f87a77ff6e18fa08943385d9cf61bbf915089
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54822637"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Oktatóanyag: Az Azure Active Directory-integráció, a LinkedIn-szintjének emelése

Ebben az oktatóanyagban elsajátíthatja a jogosultságszint-emelés LinkedIn integrálása az Azure Active Directory (Azure AD).

Jogosultságszint-emelés LinkedIn integrálása az Azure ad-vel nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a LinkedIn jogosultságszint-emelés Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a LinkedIn jogosultságszint-emelés (egyszeri bejelentkezés)
- A fiókok egyetlen központi helyen – az Azure felügyeleti portálján kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a jogosultságszint-emelés LinkedIn, a következőkre van szükség:

- Azure AD-előfizetés
- A LinkedIn jogosultságszint-emelés egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Az éles környezetben ne használjon, ha erre szükség.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. LinkedIn jogosultságszint-emelés hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-linkedin-elevate-from-the-gallery"></a>LinkedIn jogosultságszint-emelés hozzáadása a katalógusból
Az Azure AD integrálása a LinkedIn jogosultságszint-emelés konfigurálásához hozzá kell LinkedIn jogosultságszint-emelés a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a LinkedIn-szintjének emelése a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálján](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]

1. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **LinkedIn jogosultságszint-emelés**. Az eredmények panelen kattintson a **LinkedIn jogosultságszint-emelés** , vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés LinkedIn jogosultságszint-emelés a "Britta Simon" nevű tesztelési felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó LinkedIn jogosultságszint-emelés mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó jogosultságszintjének LinkedIn hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a LinkedIn jogosultságszint-emelés.

Az Azure AD egyszeri bejelentkezés a LinkedIn jogosultságszint-emelés tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[LinkedIn jogosultságszint-emelés tesztfelhasználó létrehozása](#creating-a-linkedin-elevate-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure felügyeleti portálon és a LinkedIn jogosultságszint-emelés alkalmazását az egyszeri bejelentkezés konfigurálása.

**LinkedIn jogosultságszint-emelés konfigurálni az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon a a **LinkedIn jogosultságszint-emelés** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédpanelen, **mód** kiválasztása **SAML-alapú bejelentkezés** való egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial-linkedin_01.png)

1. Egy másik böngészőablakban bejelentkezés, a LinkedIn jogosultságszint-emelés bérlői rendszergazdaként.

1. A **Account Center**, kattintson a **globális beállítások** alatt **beállítások**. Jelölje be **jogosultságszint-emelés – AAD-teszt jogosultságszint-emelés** a legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Kattintson a **, vagy kattintson ide betöltése, és másolja ki az egyes mezők az űrlap** és másolási **entitásazonosító** és **helyességi feltétel fogyasztói Access (ACS) URL-címe**

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

1. Az Azure Portalon alatt **LinkedIn jogosultságszint-emelés tartomány és URL-címek**, kövesse az alábbi lépéseket, ha szeretné az egyszeri bejelentkezést **identitásszolgáltató által kezdeményezett** mód

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_01.png)

    a. Az a **azonosító** szövegmezőbe írja be a **Entitásazonosító** LinkedIn Portalról másolt 

    b. Az a **válasz URL-cím** szövegmezőbe írja be a **helyességi feltétel fogyasztói Access (ACS) URL-cím** LinkedIn Portalról másolt

1. Ha szeretné az egyszeri bejelentkezést **SP által kezdeményezett**, majd kattintson a speciális URL-cím megjelenítése beállítás a konfigurációs szakaszban, és konfigurálja a bejelentkezési URL-cím a következő mintának:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_02.png) 

1. LinkedIn jogosultságszint-emelés alkalmazását a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőfelvételen látható erre egy példa látható. Az alapértelmezett érték **felhasználóazonosító** van **user.userprincipalname** , de ezt képezhető le a felhasználó e-mail-címmel a LinkedIn jogosultságszint-emelés vár. Használhatja, amely **user.mail** attribútumot a listából, vagy használja a megfelelő attribútum értéket, a szervezet konfiguráció alapján.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/updateusermail.png)

1. A **felhasználói attribútumok** területén kattintson **megtekintése és egyéb felhasználói attribútumok szerkesztése** és attribútumainak beállítása. Egy másik jogcím nevű hozzá kell adnia **részleg** értékét le kell képezni, és **user.department**.

    | Attribútum neve | Attribútum értéke |
    | --- | --- |
    | Szervezeti egység| user.department |

      ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinelevate-tutorial/userattribute.png)

      a. A megnyitásához az attribútum részleteit megjelenítő oldalon attribútum hozzáadása gombra kattintva adja hozzá a részleg attribútumot, ahogyan az alábbi-

      ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinelevate-tutorial/adduserattribute.png)

      b. Kattintson a **Ok** attribútum mentése.

      c. Módosítsa a nevet a attribútum **emailaddress** való **e-mail**.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_certificate.png) 

1. Kattintson a **Save** (Mentés) gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_general_400.png)

1. Lépjen a **LinkedIn adminisztrátori beállítások** szakaszban. Töltse fel az imént letöltött az Azure Portalon kattintson a feltöltés XML beállítást XML-fájlt.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Kattintson a **a** egyszeri bejelentkezés engedélyezéséhez. Egyszeri bejelentkezés állapota változik **nincs csatlakoztatva** való **csatlakoztatva**

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure felügyeleti portálján, Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinelevate-tutorial/create_aaduser_01.png) 

1. Lépjen a **felhasználók és csoportok** kattintson **minden felhasználó** felhasználók listájának megjelenítéséhez.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinelevate-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinelevate-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinelevate-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-a-linkedin-elevate-test-user"></a>LinkedIn jogosultságszint-emelés tesztfelhasználó létrehozása

LinkedIn jogosultságszint-emelés alkalmazás támogatja a csak az idő felhasználókiépítés, miután a felhasználók hitelesítésére, az alkalmazás automatikusan létrejön. A rendszergazda a beállítások lapon a portál tükrözés LinkedIn szintjének emelése a kapcsoló **rendelhet hozzá licenceket** az aktív igény szerinti üzembe helyezést, és ez lesz is rendeljen egy licencet a felhasználó. LinkedIn jogosultságszintjének emeléséhez is támogatja a felhasználók automatikus átadása, további részleteket talál [Itt](linkedinelevate-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

   ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon számára a hozzáférés biztosításával LinkedIn megszereznie Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel LinkedIn jogosultságszint-emelés, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

1. Az alkalmazások listájában jelölje ki a **LinkedIn jogosultságszint-emelés**.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_0001.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a LinkedIn jogosultságszint-emelés csempére kattint, szerezheti be az Azure bejelentkezési oldalra, és a sikeres bejelentkezést követően szerezheti be a jogosultságszint-emelés LinkedIn alkalmazásba.

## <a name="additional-resources"></a>További források

* [Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directory konfigurálása a LinkedIn szintjének emelése](linkedinelevate-provisioning-tutorial.md)
* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](linkedinelevate-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/linkedinElevate-tutorial/tutorial_general_203.png