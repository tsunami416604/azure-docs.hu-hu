---
title: 'Oktatóanyag: Azure Active Directory integráció az Ariba szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az Ariba között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 139aeb2fad37f96ad9e3d7fc801d574a93e896bd
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457699"
---
# <a name="tutorial-azure-active-directory-integration-with-ariba"></a>Oktatóanyag: Azure Active Directory integráció az Aribavel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az Aribat Azure Active Directory (Azure AD) használatával.
Az Ariba és az Azure AD integrálása a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-t, aki hozzáfér az Aribahöz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Aribaba (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az Ariba szolgáltatással való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Ariba egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az Ariba támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* Az Ariba konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-ariba-from-the-gallery"></a>Az Ariba hozzáadása a katalógusból

Az Ariba Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Aribat a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **Ariba** kifejezést.
1. Válassza az **Ariba** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD SSO konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Ariba szolgáltatással konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az Ariban kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés az Ariba szolgáltatással való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. Az **[Ariba SSO konfigurálása](#configure-ariba-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. Az **[Ariba test User létrehozása](#create-ariba-test-user)** – hogy rendelkezzen a Britta Simon in Ariba szolgáltatásával, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **Ariba** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Az Ariba tartomány és az URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:
    
    ```http
    https://<subdomain>.sourcing.ariba.com
    https://<subdomain>.supplier.ariba.com
    ```

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `http://<subdomain>.procurement-2.ariba.com`

    c. A **Válasz URL-cím**mezőben adja meg a következő URL-minta egyikét:

    | Válasz URL-cím|
    |----------|
    | `https://<subdomain>.ariba.com/CUSTOM_URL` |
    | `https://<subdomain>.procurement-eu.ariba.com/CUSTOM_URL` |
    | `https://<subdomain>.procurement-eu.ariba.com` |
    | `https://<subdomain>.procurement-2.ariba.com` |
    | `https://<subdomain>.procurement-2.ariba.com/CUSTOM_URL` |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Itt javasoljuk, hogy a karakterlánc egyedi értékét használja az azonosítóban. A következő értékek beszerzéséhez lépjen kapcsolatba az Ariba ügyfél-támogatási csapattal a **1-866-218-2155** címen. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 


Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. A képernyő felső részén válassza az **új felhasználó**lehetőséget.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A név mezőbe írja be a **B. Simon** **nevet** .  
   1. A **Felhasználónév** mezőbe írja be a nevet `<username>@<companydomain>.<extension>` . Például így: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd jegyezze fel a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az Ariba szolgáltatáshoz való hozzáférés megadásával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **Ariba**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-ariba-sso"></a>Az Ariba SSO konfigurálása

Ha be szeretné állítani az egyszeri bejelentkezést az alkalmazáshoz, hívja meg az Ariba támogatási csapatot a **1-866-218-2155** -es számítógépen, és segítséget nyújt Önnek a letöltött **tanúsítvány (Base64**) fájl megadásához.

### <a name="create-ariba-test-user"></a>Az Ariba test User létrehozása

Ebben a szakaszban egy Britta Simon nevű, az Ariba-ben létrehozott felhasználót hoz létre. A felhasználók az Ariba platformon való hozzáadásához a **1-866-218-2155** -es verzióban dolgozhat az Ariba támogatási csapatával. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az Ariba csempére kattint, automatikusan be kell jelentkeznie arra az Ariba szolgáltatásba, amelyhez az SSO-t beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)