---
title: 'Oktatóanyag: Azure Active Directory integráció az LMS-sel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az LMS befogadására.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: jeedes
ms.openlocfilehash: 59fb36765ad3cd584af4d6459cd78e2886d0edce
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88538705"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Oktatóanyag: Azure Active Directory integráció az LMS-sel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az LMS-t az Azure Active Directory (Azure AD) használatával.
A bevonási LMS és az Azure AD integrálásával a következő előnyöket nyújtja:

* Megadhatja az Azure AD-t, aki hozzáfér az LMS befogadásához.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az LMS (egyszeri bejelentkezés) Azure AD-fiókjával való befogadására.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a befogadási LMS szolgáltatással való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Az LMS egyszeri bejelentkezésre engedélyezett előfizetésének befogadása

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A befogadási LMS támogatja a **identitásszolgáltató** KEZDEMÉNYEZett SSO-t

## <a name="adding-absorb-lms-from-the-gallery"></a>Elnyelő LMS hozzáadása a katalógusból

A befogadási LMS Azure AD-be való integrálásának konfigurálásához fel kell vennie az LMS szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A következő lépések végrehajtásával adhat hozzá befogadó LMS-t a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **elnyelő LMS**, válassza az **LMS befogadása** az eredményekből panelt, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Az LMS befogadása az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez egy Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolatra van szükség a befogadási LMS-ben.

Az Azure AD egyszeri bejelentkezés a befogadási LMS használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[befogadási LMS egyszeri bejelentkezésének konfigurálása](#configure-absorb-lms-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre egy befogadási LMS-teszt felhasználót](#create-absorb-lms-test-user)** – a Britta Simon-nek a felhasználó Azure ad-képviseletéhez kapcsolódó LMS-ben való befogadásához.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a befogadási LMS használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/)az **LMS** -alkalmazás integrációja lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** gombra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Az LMS-tartomány és az URL-címek egyszeri bejelentkezési adatainak befogadása](common/idp-intiated.png)

    Ha **5 felhasználói felületet** használ, használja a következő konfigurációt:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://company.myabsorb.com/account/saml`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://company.myabsorb.com/account/saml`

    Ha **5 – új tanulási élményt** használ, használja a következő konfigurációt:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek beszerzéséhez vegye fel a kapcsolatot az [LMS ügyfél-támogatási csapatával](https://support.absorblms.com/hc/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName**leképezéssel van leképezve.

    ![image](common/edit-attribute.png)

6. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **befogadási LMS beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-absorb-lms-single-sign-on"></a>A befogadási LMS egyszeri bejelentkezésének konfigurálása

1. Egy új böngészőablakban jelentkezzen be rendszergazdaként az LMS céges webhelyre.

2. Kattintson a jobb felső sarokban található **fiók** gombra.

    ![A fiók gomb](./media/absorblms-tutorial/1.png)

3. A fiók ablaktáblán válassza a **portál beállításai**lehetőséget.

    ![A portál beállításainak hivatkozása](./media/absorblms-tutorial/2.png)

4. Válassza az **SSO-beállítások kezelése** lapot.

    ![A felhasználók lap](./media/absorblms-tutorial/managesso.png)

5. Az **egyszeri bejelentkezési beállítások kezelése** lapon tegye a következőket:

    ![Az egyszeri bejelentkezés konfigurációs lapja](./media/absorblms-tutorial/settings.png)

    a. A **név** szövegmezőbe írja be a nevet, például az Azure ad Marketplace egyszeri bejelentkezést.

    b. Válassza ki az **SAML** -t **metódusként**.

    c. Nyissa meg a Jegyzettömbben a Azure Portal letöltött tanúsítványt. Távolítsa el a **---BEGIN certificate---** és **---END Certificate---** címkéket. Ezt követően illessze be a többi tartalmat a **kulcs** mezőbe.

    d. A **Mode (mód** ) mezőben válassza az **identitás-szolgáltató kezdeményezve**lehetőséget.

    e. Az **azonosító tulajdonság** mezőben válassza ki azt az attribútumot, amelyet felhasználói azonosítóként konfigurált az Azure ad-ben. Ha például az *NameIdentifier* van kiválasztva az Azure ad-ben, válassza a **username (Felhasználónév**) lehetőséget.

    f. Válassza **Sha256** ki a Sha256 **aláírási típusként**.

    : A **bejelentkezési URL-cím** mezőben illessze be a **felhasználói hozzáférési URL-címet** az Azure Portal alkalmazás **Tulajdonságok** lapjáról.

    h. A **kijelentkezési URL-címben**illessze be a Azure Portal a **bejelentkezés beállítása** ablakból másolt kijelentkezési **URL** -értéket.

    i. **Automatikus átirányításának** bekapcsolása **a**következőre:.

6. Válassza a **Mentés lehetőséget.**

    ![Az egyszeri bejelentkezési bejelentkezés engedélyezése](./media/absorblms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: `brittasimon\@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az LMS befogadására való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza az **LMS befogadása**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza az **LMS befogadása**lehetőséget.

    ![A befogadási LMS hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-absorb-lms-test-user"></a>Befogadási LMS-teszt felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek az LMS-be, be kell őket állítani az LMS-ben. A befogadási LMS esetében a kiépítés manuális feladat.

**A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a befogadása az LMS vállalati webhelyre rendszergazdaként.

2. A **felhasználók** ablaktáblán válassza a **felhasználók**lehetőséget.

    ![A felhasználók hivatkozás](./media/absorblms-tutorial/absorblms_userssub.png)

3. Válassza a **felhasználó** fület.

    ![Az új legördülő lista hozzáadása](./media/absorblms-tutorial/absorblms_createuser.png)

4. A **felhasználó hozzáadása** lapon tegye a következőket:

    ![A felhasználó hozzáadása lap](./media/absorblms-tutorial/user.png)

    a. Az **Utónév** mezőbe írja be az utónév nevet (például **Britta**).

    b. A **vezetéknév** mezőbe írja be a vezetéknevet, például **Simon**nevet.

    c. A **Felhasználónév** mezőbe írja be a teljes nevet, például **Britta Simon**.

    d. A **jelszó** mezőbe írja be a felhasználói jelszó mezőt.

    e. A **Jelszó megerősítése** mezőbe írja be újra a jelszót.

    f. **Az aktív váltógomb beállítása** **aktívra**.

5. Válassza a **Mentés lehetőséget.**

    ![Az egyszeri bejelentkezési bejelentkezés engedélyezése](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Alapértelmezés szerint a felhasználó kiépítés nincs engedélyezve az SSO-ban. Ha az ügyfél szeretné engedélyezni ezt a szolgáltatást, azt a [jelen](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) dokumentációban említetteknek megfelelően be kell állítani. Azt is vegye figyelembe, hogy a felhasználói elővision csak az **5 – új tanulói élmény** az ACS URL-címmel érhető el`https://company.myabsorb.com/api/rest/v2/authentication/saml`

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a befogadási LMS csempére kattint, automatikusan be kell jelentkeznie a befogadási LMS-be, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
