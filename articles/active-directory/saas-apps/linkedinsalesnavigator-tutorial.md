---
title: 'Oktatóanyag: Az Azure Active Directory-integráció, a LinkedIn Sales Navigator |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és LinkedInSalesNavigator között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: b4a30259c4aa00530e5aadd2cb98c19b63bd5d8a
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54817027"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Oktatóanyag: A LinkedIn Sales Navigator az Azure Active Directory-integráció

Ebben az oktatóanyagban megismerheti, hogyan integrálható a LinkedIn Sales Navigator az Azure Active Directory (Azure AD).

Az Azure AD integrálása a LinkedIn Sales Navigator nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a LinkedIn Sales Navigator Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett LinkedIn Sales Navigator (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, Tallózás [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a LinkedIn Sales Navigator, a következőkre van szükség:

- Azure AD-előfizetés
- A LinkedIn Sales Navigator egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Kerülje az éles környezetben nem szükséges.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. LinkedIn Sales Navigator hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>LinkedIn Sales Navigator hozzáadása a katalógusból
Az Azure AD integrálása a LinkedIn Sales Navigator konfigurálásához hozzá kell LinkedIn Sales Navigator a galériából a felügyelt SaaS-alkalmazások listájára.

**LinkedIn Sales Navigator hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **LinkedIn Sales Navigator**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_search.png)

1. Az eredmények panelen válassza ki a **LinkedIn Sales Navigator**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálni, és a LinkedIn Sales Navigator a teszt "Britta Simon" nevű felhasználó az Azure AD egyszeri bejelentkezés teszteléséhez.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a LinkedIn Sales Navigator tartozó felhasználó Mi az a felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a LinkedIn Sales Navigator hivatkozás kapcsolatát kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a LinkedIn Sales Navigator.

Az Azure AD egyszeri bejelentkezés vizsgálata a LinkedIn Sales Navigator konfigurálni, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[A LinkedIn Sales Navigator tesztfelhasználó létrehozása](#creating-a-linkedin-sales-navigator-test-user)**  – egy megfelelője a Britta Simon LinkedIn Sales Navigator, amely a felhasználó Azure ad-ben reprezentációja van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a LinkedIn Sales Navigator-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához a LinkedIn Sales Navigator, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **LinkedIn Sales Navigator** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédpanelen, a **mód** válassza **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_samlbase.png)

1. Egy másik böngészőablakban, a bejelentkezés a **LinkedIn Sales Navigator** webhely rendszergazdaként.

1. A **Account Center**, kattintson a **globális beállítások** alatt **beállítások**. Jelölje be **Sales Navigator** a legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Kattintson a **, vagy kattintson ide betöltése, és másolja ki az egyes mezők az űrlap** és másolási **entitásazonosító** és **helyességi feltétel fogyasztói Access (ACS) URL-cím**.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

1. Az Azure Portal alatt **LinkedIn Sales Navigator-tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url1.png)

    a. Az a **azonosító** szövegmezőbe írja be a **Entitásazonosító** LinkedIn Portalról másolt 

    b. Az a **válasz URL-cím** szövegmezőbe írja be a **helyességi feltétel fogyasztói Access (ACS) URL-cím** LinkedIn Portalról másolt

1. Ellenőrizze **speciális URL-beállítások megjelenítése**, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url2.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az értéket a következő minta használatával: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. A **LinkedIn Sales Navigator** alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőfelvételen szereplő példán látható. Az alapértelmezett érték **felhasználóazonosító** van **user.userprincipalname** , de a LinkedIn Sales Navigator vár, hogy a felhasználó e-mail címét kell leképezve. Használhat **user.mail** attribútumot a listából, vagy használja a megfelelő attribútum értéket, a szervezet konfiguráció alapján. 

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/updateusermail.png)
    
1. A **felhasználói attribútumok** területén kattintson **megtekintése és egyéb felhasználói attribútumok szerkesztése** és attribútumainak beállítása. A felhasználónak kell nevű négy jogcímek hozzáadása **e-mail**, **részleg**, **firstname**, és **lastname** értéke pedig leképezve**user.mail**, **user.department**, **user.givenname**, és **user.surname** osztályban

    | Attribútum neve | Attribútum értéke |
    | --- | --- |    
    | e-mail| user.mail |
    | Szervezeti egység| user.department |
    | Keresztnév| user.givenname |
    | Vezetéknév| user.surname |
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinsalesnavigator-tutorial/userattribute.png)
    
    a. Kattintson a **attribútum hozzáadása** a attribútum párbeszédpanel megnyitásához.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_04.png)
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_05.png)
   
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson a **Ok**

1. Hajtsa végre a következő lépéseket a **neve** attribútum -

    a. Az attribútum megnyitásához kattintson a **attribútum szerkesztése** ablak.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/url_update.png)

    b. Az URL-Címének értékét törli a **névtér**.
    
    c. Kattintson a **Ok** a beállítás mentéséhez.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_general_400.png)

1. Lépjen a **LinkedIn adminisztrátori beállítások** szakaszban. Kattintson a **feltöltése XML-fájl** az Azure Portalról letöltött metaadatainak XML-fájl feltöltése.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Kattintson a **a** egyszeri bejelentkezés engedélyezéséhez. Egyszeri bejelentkezés állapota változik **nincs csatlakoztatva** való **csatlakoztatva**

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)


> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinsalesnavigator-tutorial/create_aaduser_01.png) 

1. Lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinsalesnavigator-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinsalesnavigator-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinsalesnavigator-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-linkedin-sales-navigator-test-user"></a>A LinkedIn Sales Navigator tesztfelhasználó létrehozása

Társított Sales Navigator-alkalmazás támogatja a csak az idő szerinti (JIT) felhasználókiépítés, miután a felhasználók hitelesítésére automatikusan létrehozza az alkalmazásban. Aktiválja **rendelhet hozzá licenceket** rendel hozzá licenceket a felhasználó számára.
   
   ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával a LinkedIn Sales Navigator Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel a LinkedIn Sales Navigator, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **LinkedIn Sales Navigator**.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a LinkedIn Sales Navigator csempére kattint, szervezeti oldalra, ahol meg kell adnia a LinkedIn-fiók személyes adatai a rendszer átirányítja. A személyes fiókot a LinkedIn-fiók üzleti hivatkozik. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_01.png
[2]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_02.png
[3]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_03.png
[4]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_04.png

[100]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_100.png

[200]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_200.png
[201]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_201.png
[202]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_202.png
[203]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_203.png

