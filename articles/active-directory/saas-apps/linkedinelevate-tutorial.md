---
title: 'Oktatóanyag: Azure Active Directory a LinkedIn emelt szintű integrációval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a LinkedIn jogosultságszint-emelése között.
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
ms.openlocfilehash: 5b7cb8d6ab34a632e36ea2fd1c87005a038bc523
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823715"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Oktatóanyag: Azure Active Directory a LinkedIn-jogosultságszint-integrációval

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a LinkedIn-t a Azure Active Directory (Azure AD) szolgáltatással.
A LinkedIn emelt szintű Azure AD-vel való integrálásával az alábbi előnyökkel jár:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a LinkedIn jogosultságszint-emeléshez.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a LinkedIn-jogosultságszint (egyszeri bejelentkezés) szolgáltatásba az Azure AD-fiókkal.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a LinkedIn jogosultságszint-vel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* LinkedIn emelt szintű egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A LinkedIn Jogosultságszint-emelés támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t

* A LinkedIn jogosultságszint-emelési **szolgáltatás csak időben támogatja a** felhasználók üzembe helyezését

* A LinkedIn jogosultságszint-emelése támogatja az [ **automatikus** felhasználó-kiépítés használatát](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>LinkedIn-jogosultságszint hozzáadása a katalógusból

A LinkedIn emelt szintű Azure AD-integrációjának konfigurálásához hozzá kell adnia a LinkedIn-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A következő lépések végrehajtásával adhatja hozzá a LinkedIn-jogosultságszint-emelést a gyűjteményhez:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **LinkedIn jogosultságszint**-emelést, válassza a **LinkedIn Jogosultságszint-emelés** az eredmények panelen lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![LinkedIn-Jogosultságszint-emelés az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a LinkedIn megemelésével a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a LinkedIn-jogosultságszint kapcsolódó felhasználója közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a LinkedIn jogosultságszint-vel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. A **[LinkedIn emelt szintű egyszeri bejelentkezés beállítása](#configure-linkedin-elevate-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. A **[LinkedIn jogosultságszint-emelési teszt felhasználójának létrehozása](#create-linkedin-elevate-test-user)** – hogy rendelkezzen egy, a felhasználó Azure ad-képviseletéhez kapcsolódó, a LinkedIn-beli Britta.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezést a LinkedIn jogosultságszint-emeléssel szeretné konfigurálni, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **LinkedIn jogosultságszint** -emelése alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![A LinkedIn tartomány és az URL-címek egyszeri bejelentkezési információi](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az **entitás azonosítójának** értékét, majd másolja a LinkedIn-portál entitás-azonosító értékét az oktatóanyag későbbi részében ismertetett értékre.

    b. A **Válasz URL-címe** szövegmezőbe írja be az " **ügyfél-hozzáférési (ACS-) URL-cím** " értéket, amely az oktatóanyag későbbi részében ismertetett, a LinkedIn-portálon megjelenő URL-cím értékét másolja.

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![A LinkedIn tartomány és az URL-címek egyszeri bejelentkezési információi](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

6. A LinkedIn jogosultságszint-emelési alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName**leképezéssel van leképezve. A LinkedIn jogosultságszint-emelési alkalmazás azt várja, hogy a NameIdentifier a **User. mail**használatával legyen leképezve, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy a Szerkesztés ikonra kattint, és megváltoztatja az attribútumok leképezését.

    ![image](common/edit-attribute.png)

7. A fentieken kívül a LinkedIn jogosultságszint-emelési alkalmazás néhány további attribútumot vár az SAML-válaszban. A **felhasználó attribútumai** párbeszédpanel felhasználói jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:

    | Name (Név) | Forrás attribútum|
    | -------| -------------|
    | Szervezeti egység | felhasználó. részleg |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

8. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

9. A **LinkedIn-jogosultságszint beállítása** szakaszban adja meg a megfelelő URL (eke) t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-linkedin-elevate-single-sign-on"></a>A LinkedIn emelt szintű egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a LinkedIn emelt szintű bérlőre rendszergazdaként.

1. A **Account Centerben**kattintson a **globális beállítások** elemre a **Beállítások**területen. A legördülő listából válassza az emelt szintű **HRE teszt** lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Kattintson **vagy kattintson ide az egyes mezők az űrlapról való betöltéséhez és másolásához** , majd hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Másolja ki az **entitás azonosítóját** , és illessze be a Azure Portal alapszintű **SAML** -konfigurációjának **azonosító** szövegmezőbe.

    b. Másolja ki az alkalmazás **-hozzáférési (ACS) URL-címet** , és illessze be az alapszintű **SAML-konfiguráció** **Válasz URL-címe** szövegmezőbe a Azure Portal.

1. Ugrás a **LinkedIn admin Settings** szakaszra. Töltse fel a Azure Portal letöltött XML-fájlt az XML-fájl feltöltése lehetőségre kattintva.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Az egyszeri bejelentkezés engedélyezéséhez kattintson **a be** gombra. Az SSO-állapot **nem** kapcsolódik kapcsolathoz

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`a nevet. Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a LinkedIn jogosultságszint-emeléshez való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **LinkedIn Jogosultságszint-emelés**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **LinkedIn Jogosultságszint-emelés**lehetőséget.

    ![A LinkedIn jogosultságszint-emelési hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-linkedin-elevate-test-user"></a>LinkedIn jogosultságszint-emelési teszt felhasználó létrehozása

A LinkedIn jogosultságszint-emelési alkalmazás a felhasználók üzembe helyezését támogatja, és a hitelesítéssel rendelkező felhasználók automatikusan jönnek létre az alkalmazásban. A LinkedIn jogosultságszint-emelési portál rendszergazdai beállítások lapján a kapcsoló **automatikusan hozzárendeli** a licenceket az aktív üzembe helyezéshez, és ez a felhasználóhoz is hozzárendel egy licencet. A LinkedIn Jogosultságszint-emelés a felhasználók automatikus üzembe helyezését is támogatja. további részletekért tekintse meg az automatikus felhasználó-kiépítés konfigurálását ismertető témakört. [](linkedinelevate-provisioning-tutorial.md)

   ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a LinkedIn Jogosultságszint-emelés csempére kattint, automatikusan be kell jelentkeznie arra a LinkedIn-jogosultságszint-emelésre, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)