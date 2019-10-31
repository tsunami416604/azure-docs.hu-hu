---
title: 'Oktatóanyag: Azure Active Directory integráció az MOVEit-Átvitelsel – Azure AD-integráció | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a MOVEit átvitele között – Azure AD-integráció.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 43383e82d983c998d159728997da4757cc364999
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161322"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Oktatóanyag: Azure Active Directory integráció az MOVEit-Átvitelsel – Azure AD-integráció

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a MOVEit-átvitelt – Azure AD-integrációt Azure Active Directory (Azure AD-val).
Az MOVEit-átvitel integrálása – az Azure ad-integráció az alábbi előnyöket nyújtja:

* A MOVEit-átvitelhez hozzáféréssel rendelkező Azure AD-integrációt is szabályozhatja.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a MOVEit-átvitelbe – Azure AD-integráció (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció MOVEit-Átvitelsel való konfigurálásához az Azure AD-integrációhoz a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* MOVEit-átvitel – Azure AD-integráció egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* MOVEit-átvitel – az Azure AD-integráció támogatja az **SP** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>MOVEit-átvitel hozzáadása – Azure AD-integráció a katalógusból

A MOVEit-átvitel – Azure ad-integráció az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a MOVEit-átvitelt – az Azure AD-integrációt a katalógusból a felügyelt SaaS-alkalmazások listájára.

**MOVEit-átvitel hozzáadása – Azure AD-integráció a katalógusból hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **MOVEit átvitel – Azure ad-integráció**, válassza a **MOVEit átvitele – Azure ad-integráció** az eredmények paneljén, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![MOVEit-átvitel – Azure AD-integráció az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a MOVEit-Átvitelsel – Azure AD-integráció a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti MOVEit-átvitelben kell létrehozni az Azure AD-integrációt.

Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az MOVEit-Átvitelsel – Azure AD-integráció, a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[MOVEit-átvitel konfigurálása – Azure ad-integráció egyszeri bejelentkezés](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[MOVEit-átvitel létrehozása – Azure ad-integrációs teszt felhasználó](#create-moveit-transfer---azure-ad-integration-test-user)** – a Britta Simon-nek a MOVEit-átvitelhez való hozzáférése – Azure ad-integráció, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés MOVEit-Átvitelsel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **MOVEit-átvitel – Azure ad Integration** Application Integration oldalon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal**rendelkezik, hajtsa végre a következő lépéseket:

    a. Kattintson a **metaadat-fájl feltöltése**elemre.

    ![Metaadat-fájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés**elemre.

    ![metaadat-fájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadat-fájl feltöltése után az **azonosító** és a **Válasz URL-címe** automatikusan feltöltve lesz az **alapszintű SAML-konfiguráció** szakaszban:

    ![MOVEit-átvitel – Azure AD-integrációs tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://contoso.com`

    > [!NOTE]
    > A **bejelentkezési URL-cím** értéke nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a [MOVEit-átvitelsel – az Azure ad integrációs ügyfelének ügyfélszolgálati](https://community.ipswitch.com/s/support) csapatával. A szolgáltatói **metaadatok fájlját** letöltheti a szolgáltatói **metaadatok URL-címéről** , amelyet később a **MOVEit átvitel konfigurálása – az Azure ad Integration egyszeri bejelentkezés** szakasza ismertet az oktatóanyagban. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. Az **MOVEit átvitelének beállítása – Azure ad-integráció** szakaszban adja meg a megfelelő URL (eke) t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-cím

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>MOVEit-átvitel konfigurálása – Azure AD-integráció egyszeri bejelentkezés

1. Jelentkezzen be a MOVEit-áthelyezési bérlőre rendszergazdaként.

2. A bal oldali navigációs ablaktáblán kattintson a **Beállítások**elemre.

    ![Beállítások szakasz az alkalmazás oldalán](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Kattintson az **egyszeri bejelentkezés** hivatkozásra, amely a **biztonsági házirendek alatt > a felhasználó hitelesítése**lehetőségre.

    ![Biztonsági házirendek az alkalmazás oldalán](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Kattintson a metaadatok URL-hivatkozására a metaadat-dokumentum letöltéséhez.

    ![Szolgáltatói metaadatok URL-címe](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Ellenőrizze, hogy a **entityID** egyezik- **e az** **alapszintű SAML-konfiguráció** szakaszban.
   * Ellenőrizze, hogy a **AssertionConsumerService** hely URL-címe megfelel-e a **Válasz URL-címének** az **alapszintű SAML konfiguráció**
    
     ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Az új összevont identitás-Szolgáltató hozzáadásához kattintson az **identitás-szolgáltató hozzáadása** gombra.

    ![Identitás-szolgáltató hozzáadása](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Kattintson a **Tallózás...** elemre, és válassza ki a Azure Portalból letöltött metaadat-fájlt, majd kattintson az **Identity Provider hozzáadása** lehetőségre a letöltött fájl feltöltéséhez.

    ![SAML-identitás szolgáltatója](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Az **Összevont identitás-szolgáltató beállításainak szerkesztése..** . lapon válassza az "**Igen**" **lehetőséget, majd** kattintson a **Save (Mentés**) gombra.

    ![Összevont identitás-szolgáltató beállításai](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Az **Összevont identitás-szolgáltató felhasználói beállítások szerkesztése** oldalon hajtsa végre a következő műveleteket:
    
    ![Összevont identitás-szolgáltató beállításainak szerkesztése](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Válassza ki az **SAML-NameID** **bejelentkezési nevet**.
    
    b. Válassza a **másik** a **teljes név** lehetőséget, majd az **attribútum neve** szövegmezőben adja meg az értéket: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Válassza az egyéb **e-mail-cím** lehetőséget, majd az **attribútum neve** szövegmezőben adja meg az értéket: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Válassza az **Igen** lehetőséget, ha **a bejelentkezéskor automatikusan létrehozza a fiókot**.
    
    e. Kattintson a **Save (Mentés** ) gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon\@yourcompanydomain. Extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezi az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a MOVEit-átvitelhez – Azure AD-integrációhoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **MOVEit átvitel – Azure ad-integráció**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **MOVEit átvitel – Azure ad-integráció**elemet.

    ![Az MOVEit-átvitel – Azure AD integrációs hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>MOVEit-átvitel létrehozása – Azure AD Integration test User

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a MOVEit-átvitelben – Azure AD-integráció. MOVEit-átvitel – az Azure AD-integráció támogatja az igény szerinti üzembe helyezést, amelyet engedélyez. Ez a szakasz nem tartalmaz műveleti elemeket. Egy új felhasználó jön létre az MOVEit-átvitelhez való hozzáférés során – az Azure AD-integráció, ha még nem létezik.

>[!NOTE]
>Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [MOVEit-átvitelsel – az Azure ad Integration Client ügyfélszolgálati csapatával](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a MOVEit átvitel – Azure AD-integráció csempére kattint, automatikusan be kell jelentkeznie a MOVEit-átvitelbe – az Azure AD-integrációba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

