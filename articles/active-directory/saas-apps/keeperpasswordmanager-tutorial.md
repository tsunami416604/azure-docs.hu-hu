---
title: 'Oktatóanyag: Azure Active Directory integráció a Keeper Password Managerrel & Digital Vaulttal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Keeper Password Manager & Digital Vault között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e1a98f6a-2dae-4734-bdbf-4fba742a61d2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ee2ae8161ffe2e2b04f563e218fd356b3d68c8b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656499"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Oktatóanyag: Azure Active Directory integráció a Keeper Password Managerrel & Digital Vaulttal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Keeper Password Managert & Digital Vaultba Azure Active Directory (Azure AD).
A Keeper Password Manager & Digital Vault és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Keeper Password Manager & Digital Vaulthoz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy az Azure AD-fiókjával & digitális tárolóban (egyszeri bejelentkezés) megőrizze a Password Managert.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt a Keeper Password Managerrel & Digital Vaulttal szeretné konfigurálni, a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* A Keeper Password Manager & a digitális tár egyszeri bejelentkezésre engedélyezett előfizetését

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Keeper Password Manager & Digital Vault támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A Keeper Password Manager & Digital Vault **a** felhasználók üzembe helyezését támogatja

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>A Keeper Password Manager & a digitális tár hozzáadása a katalógusból

A Keeper Password Manager & Digital Vault Azure AD-ba való integrálásának konfigurálásához a Keeper Password Managert & Digital Vaultot a katalógusból a felügyelt SaaS-alkalmazások listájára kell felvennie.

**A Keeper Password Manager & digitális tárolójának a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Keeper Password manager & digitális**tároló kifejezést, válassza a **Keeper Password Manager & digitális** tár az eredmény panelről lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![A Keeper Password Manager & Digital Vault az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a Keeper Password Managerrel & Digital Vaulton egy **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az érintett felhasználó közötti kapcsolati kapcsolatra van szükség a Keeper Password Managerben, & digitális tárolót kell létrehozni.

Az Azure AD egyszeri bejelentkezés konfigurálásához és teszteléséhez a Keeper Password Manager & Digital Vault használatával a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. A **Keeper Password Manager & a digitális tár egyszeri bejelentkezésének beállítása** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. A **[Keeper Password manager & a digitális tár tesztelési felhasználójának létrehozása](#create-keeper-password-manager--digital-vault-test-user)** – hogy a Britta egy, a felhasználó Azure ad-képviseletéhez kapcsolódó, a Keeper Password Managerben található, a (& digitális tárolóban található).
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezést a Keeper Password Managerrel & Digital Vaulttal szeretné konfigurálni, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Keeper Password Manager & Digital Vault** Application Integration lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A Keeper Password Manager & a digitális tár tartományát és az URL-címek egyszeri bejelentkezési adatait](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://{SSO CONNECT SERVER}/sso-connect/saml/login`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://{SSO CONNECT SERVER}/sso-connect`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://{SSO CONNECT SERVER}/sso-connect/saml/sso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Vegye fel a kapcsolatot a [Keeper Password managerrel & a digitális tároló ügyfél-támogatási csapatával](https://keepersecurity.com/contact.html) , hogy ezeket az értékeket kapja. Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. A **Keeper Password Manager beállítása & digitális** tároló szakaszban másolja ki a megfelelő URL-címeket (ek) a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL

### <a name="configure-keeper-password-manager--digital-vault-single-sign-on"></a>A Keeper Password Manager konfigurálása a digitális tár egyszeri bejelentkezésének &

Ha be szeretné állítani az egyszeri bejelentkezést a **Keeper Password managerben & a digitális tároló konfigurációs** oldalát, kövesse az Keeper [támogatási útmutatójában](https://keepersecurity.com/assets/pdf/KeeperSSOConnect_v11.pdf)megadott útmutatást.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **brittasimon\@yourcompanydomain. Extension** **nevet**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban az Azure egyszeri bejelentkezés használatára engedélyezi a Britta, hogy hozzáférést biztosítson a Keeper Password Manager & Digital Vaulthoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Keeper Password Manager & Digital Vault**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Keeper Password Manager & Digital Vault**elemet.

    ![A Keeper Password Manager & Digital Vault-hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>A Keeper Password Manager létrehozása & Digital Vault test User

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Keeper Password Manager & digitális tárolóba, ki kell építeni őket a Keepers Password Manager & Digital Vaultba. Az alkalmazás a felhasználó üzembe helyezését támogatja, és a hitelesítéssel rendelkező felhasználók automatikusan jönnek létre az alkalmazásban. Ha manuálisan szeretné beállítani a felhasználókat, vegye fel a kapcsolatot a [Keeper támogatási szolgálatával](https://keepersecurity.com/contact.html).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférés panelen a Keeper Password Manager & digitális tároló csempére kattint, automatikusan be kell jelentkeznie a Keeper Password Manager & digitális tárolóba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

