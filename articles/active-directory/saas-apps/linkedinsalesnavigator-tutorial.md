---
title: 'Oktatóanyag: Az Azure Active Directory-integráció, a LinkedIn Sales Navigator |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a LinkedIn Sales Navigator között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a216e720523767f428036290aea7151c2dca34
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708166"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Oktatóanyag: A LinkedIn Sales Navigator az Azure Active Directory-integráció

Ebben az oktatóanyagban megismerheti, hogyan integrálható a LinkedIn Sales Navigator az Azure Active Directory (Azure AD).
Az Azure AD integrálása a LinkedIn Sales Navigator nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a LinkedIn Sales Navigator Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett LinkedIn Sales Navigator (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a LinkedIn Sales Navigator, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* LinkedIn Sales Navigator egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a LinkedIn Sales Navigator **SP és IDP** által kezdeményezett egyszeri bejelentkezés

* Támogatja a LinkedIn Sales Navigator **igény szerinti** felhasználók átadása

* Támogatja a LinkedIn Sales Navigator [ **automatikus** felhasználók átadása](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>LinkedIn Sales Navigator hozzáadása a katalógusból

Az Azure AD integrálása a LinkedIn Sales Navigator konfigurálásához hozzá kell LinkedIn Sales Navigator a galériából a felügyelt SaaS-alkalmazások listájára.

**LinkedIn Sales Navigator hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **LinkedIn Sales Navigator**válassza **LinkedIn Sales Navigator** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában a LinkedIn Sales Navigator](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a LinkedIn Sales Navigator nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó a LinkedIn Sales Navigator hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés vizsgálata a LinkedIn Sales Navigator konfigurálni, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[LinkedIn Sales Navigator egyszeri bejelentkezés konfigurálása](#configure-linkedin-sales-navigator-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre a LinkedIn Sales Navigator tesztfelhasználó](#create-linkedin-sales-navigator-test-user)**  – van egy Britta Simon megfelelője a LinkedIn Sales Navigator, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálásához a LinkedIn Sales Navigator, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **LinkedIn Sales Navigator** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![LinkedIn Sales Navigator-tartomány és URL-címek egyszeri bejelentkezési adatait](common/idp-intiated.png)

    a. A a **azonosító** szöveget adja meg a **Entitásazonosító** érték kell átmásolnia Entitásazonosító értéket az oktatóanyag későbbi részében ismertetett Linkedin-portálról.

    b. A a **válasz URL-cím** szöveget adja meg a **helyességi feltétel fogyasztói Access (ACS) URL-cím** értéket kell átmásolnia helyességi feltétel fogyasztói Access (ACS) URL-cím, az oktatóanyag későbbi részében ismertetett értéket a Linkedin-portálról.

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![LinkedIn Sales Navigator-tartomány és URL-címek egyszeri bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

6. LinkedIn Sales Navigator-alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg, hol **nameidentifier** le van képezve a **user.userprincipalname**. LinkedIn Sales Navigator alkalmazás vár a leképezendő nameidentifier **user.mail**, így a attribútumleképezés szerkesztéséhez kattintson a Szerkesztés ikonra, és módosítsa a attribútumleképezés szükséges.

    ![image](common/edit-attribute.png)

7. Emellett a fent LinkedIn Sales Navigator-alkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. Felhasználói jogcímek című szakaszban a **felhasználói attribútumok** párbeszédpanelen a következő lépésekkel adja hozzá a SAML-jogkivonat attribútumot, ahogyan az alábbi táblázatban:

    | Name (Név) | Adatforrás-attribútum|
    | --- | --- |
    | e-mail| user.mail |
    | Szervezeti egység| user.department |
    | Keresztnév| user.givenname |
    | Vezetéknév| user.surname |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

8. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

9. Az a **állítsa be a LinkedIn Sales Navigator** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-linkedin-sales-navigator-single-sign-on"></a>LinkedIn Sales Navigator egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, a bejelentkezés a **LinkedIn Sales Navigator** webhely rendszergazdaként.

1. A **Account Center**, kattintson a **globális beállítások** alatt **beállítások**. Jelölje be **Sales Navigator** a legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Kattintson a **, vagy kattintson ide betöltése, és másolja ki az egyes mezők az űrlap** , és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Másolás **entitásazonosító** és illessze be azt a **azonosító** szöveget szövegmezőbe a **alapszintű SAML-konfigurációja** az Azure Portalon.

    b. Másolás **helyességi feltétel fogyasztói Access (ACS) URL-cím** és illessze be azt a **válasz URL-cím** szöveget szövegmezőbe a **alapszintű SAML-konfigurációja** az Azure Portalon.

1. Lépjen a **LinkedIn adminisztrátori beállítások** szakaszban. Az Azure Portalon kattintson a letöltött XML-fájl feltöltése a **feltöltése XML-fájl** lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Kattintson a **a** egyszeri bejelentkezés engedélyezéséhez. Egyszeri bejelentkezés állapota változik **nincs csatlakoztatva** való **csatlakoztatva**

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával a LinkedIn Sales Navigator Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **LinkedIn Sales Navigator**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **LinkedIn Sales Navigator**.

    ![Az alkalmazások listáját az a LinkedIn Sales Navigator-hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-linkedin-sales-navigator-test-user"></a>LinkedIn Sales Navigator tesztfelhasználó létrehozása

Társított Sales Navigator-alkalmazás támogatja a csak az idő szerinti (JIT) felhasználókiépítés, miután a felhasználók hitelesítésére automatikusan létrehozza az alkalmazásban. Aktiválja **rendelhet hozzá licenceket** rendel hozzá licenceket a felhasználó számára.

   ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a LinkedIn Sales Navigator csempére kattint, akkor kell automatikusan megtörténik a a LinkedIn Sales Navigator, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

