---
title: 'Oktatóanyag: Az Azure Active Directory-integráció, a LinkedIn-szintjének emelése |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a LinkedIn jogosultságszint-emelés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92af3a18be8471013e93afad47146bdbabd4be18
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128787"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Oktatóanyag: Az Azure Active Directory-integráció, a LinkedIn-szintjének emelése

Ebben az oktatóanyagban elsajátíthatja a jogosultságszint-emelés LinkedIn integrálása az Azure Active Directory (Azure AD).
Jogosultságszint-emelés LinkedIn integrálása az Azure ad-vel nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a LinkedIn jogosultságszint-emelés Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett a LinkedIn jogosultságszint-emelés (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a jogosultságszint-emelés LinkedIn, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* LinkedIn jogosultságszint-emelés egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a LinkedIn jogosultságszint-emelés **SP és IDP** által kezdeményezett egyszeri bejelentkezés

* Támogatja a LinkedIn jogosultságszint-emelés **igény szerinti** felhasználók átadása

* Támogatja a LinkedIn jogosultságszint-emelés [ **automatikus** felhasználók átadása](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>LinkedIn jogosultságszint-emelés hozzáadása a katalógusból

Az Azure AD integrálása a LinkedIn jogosultságszint-emelés konfigurálásához hozzá kell LinkedIn jogosultságszint-emelés a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a LinkedIn-szintjének emelése a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **LinkedIn jogosultságszint-emelés**, jelölje be **LinkedIn jogosultságszint-emelés** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![LinkedIn jogosultságszint-emelés a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a LinkedIn jogosultságszint-emelés nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó jogosultságszintjének LinkedIn hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a LinkedIn jogosultságszint-emelés tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[LinkedIn jogosultságszint-emelés egyszeri bejelentkezés konfigurálása](#configure-linkedin-elevate-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre tesztfelhasználót LinkedIn jogosultságszint-emelés](#create-linkedin-elevate-test-user)**  – LinkedIn jogosultságszintjének, amelyhez a felhasználó Azure ad-ben ábrázolása kapcsolódik egy megfelelője a Britta Simon van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

LinkedIn jogosultságszint-emelés konfigurálni az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **LinkedIn jogosultságszint-emelés** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![LinkedIn jogosultságszint-emelés tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. A a **azonosító** szöveget adja meg a **Entitásazonosító** érték kell átmásolnia Entitásazonosító értéket az oktatóanyag későbbi részében ismertetett Linkedin-portálról.

    b. A a **válasz URL-cím** szöveget adja meg a **helyességi feltétel fogyasztói Access (ACS) URL-cím** értéket kell átmásolnia helyességi feltétel fogyasztói Access (ACS) URL-cím, az oktatóanyag későbbi részében ismertetett értéket a Linkedin-portálról.

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![LinkedIn jogosultságszint-emelés tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

6. Alkalmazás LinkedIn jogosultságszint-emelés a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg, hol **nameidentifier** le van képezve a **user.userprincipalname**. LinkedIn jogosultságszint-emelés alkalmazás vár a leképezendő nameidentifier **user.mail**, így a attribútumleképezés szerkesztéséhez kattintson a Szerkesztés ikonra, és módosítsa a attribútumleképezés szükséges.

    ![image](common/edit-attribute.png)

7. Emellett a fentiekben LinkedIn jogosultságszint-emelés alkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. Felhasználói jogcímek című szakaszban a **felhasználói attribútumok** párbeszédpanelen a következő lépésekkel adja hozzá a SAML-jogkivonat attribútumot, ahogyan az alábbi táblázatban:

    | Name (Név) | Adatforrás-attribútum|
    | -------| -------------|
    | Szervezeti egység | user.department |

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

9. Az a **állítsa be a jogosultságszint-emelés LinkedIn** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-linkedin-elevate-single-sign-on"></a>LinkedIn konfigurálása egyszeri bejelentkezéshez jogosultságszintjének

1. Egy másik böngészőablakban bejelentkezés, a LinkedIn jogosultságszint-emelés bérlői rendszergazdaként.

1. A **Account Center**, kattintson a **globális beállítások** alatt **beállítások**. Jelölje be **jogosultságszint-emelés – AAD-teszt jogosultságszint-emelés** a legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Kattintson a **, vagy kattintson ide betöltése, és másolja ki az egyes mezők az űrlap** , és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Másolás **entitásazonosító** és illessze be azt a **azonosító** szöveget szövegmezőbe a **alapszintű SAML-konfigurációja** az Azure Portalon.

    b. Másolás **helyességi feltétel fogyasztói Access (ACS) URL-cím** és illessze be azt a **válasz URL-cím** szöveget szövegmezőbe a **alapszintű SAML-konfigurációja** az Azure Portalon.

1. Lépjen a **LinkedIn adminisztrátori beállítások** szakaszban. Töltse fel az XML-fájl feltöltése XML beállítást kattintva letöltött az Azure Portalról.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Kattintson a **a** egyszeri bejelentkezés engedélyezéséhez. Egyszeri bejelentkezés állapota változik **nincs csatlakoztatva** való **csatlakoztatva**

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

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

Ebben a szakaszban engedélyezze Britta Simon LinkedIn megszereznie a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **LinkedIn jogosultságszint-emelés**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **LinkedIn jogosultságszint-emelés**.

    ![Az alkalmazások listáját a LinkedIn jogosultságszint-emelés hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-linkedin-elevate-test-user"></a>LinkedIn jogosultságszint-emelés tesztfelhasználó létrehozása

LinkedIn jogosultságszint-emelés alkalmazás támogatja a csak az idő felhasználókiépítés, miután a felhasználók hitelesítésére, az alkalmazás automatikusan létrejön. A rendszergazda a beállítások lapon a portál tükrözés LinkedIn szintjének emelése a kapcsoló **rendelhet hozzá licenceket** az aktív igény szerinti üzembe helyezést, és ez lesz is rendeljen egy licencet a felhasználó. LinkedIn jogosultságszintjének emeléséhez is támogatja a felhasználók automatikus átadása, további részleteket talál [Itt](linkedinelevate-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

   ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a LinkedIn jogosultságszint-emelés csempére kattint, akkor kell automatikusan megtörténik a LinkedIn megszerzését, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)