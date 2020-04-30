---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az új ereklye (szervezet) használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az új ereklye (szervezet) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ffbcf4a-b6b4-45ac-922f-358abe4c05e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71c00ae8ee60f69f10f2e8281fe4fb7cff6bacd4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82190027"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-organization"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az új ereklye használatával (szervezet szerint)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az új ereklye (szervezet szerint) Azure Active Directory (Azure AD) használatával. Az új ereklye (szervezet szerint) az Azure AD-vel való integrálásakor a következőket teheti:

* Az Azure AD-ben való hozzáférés az új ereklye (szervezet) számára.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az új ereklye (szervezet) számára az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Új ereklye (szervezet szerint) egyszeri bejelentkezéses (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az új ereklye (szervezet szerint) támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

* Az új ereklye konfigurálása után a munkamenet-vezérlőelemek kikényszeríthető, amelyekkel valós időben kiszűrése és beszivároghat a szervezet bizalmas adatai. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-new-relic-by-organization-from-the-gallery"></a>Új ereklye (szervezet szerint) hozzáadása a gyűjteményből

Ha az új ereklye (szervezet szerint) integrálását szeretné konfigurálni az Azure AD-be, hozzá kell adnia az új ereklye (szervezet szerint) a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **új ereklye (szervezet szerint)** kifejezést.
1. Válassza ki az **új ereklye (szervezet szerint)** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-new-relic-by-organization"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az új ereklye számára (szervezet szerint)

Konfigurálja és tesztelje az Azure AD SSO-t az új ereklye (szervezet szerint) egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az új Ereklyeben (szervezet szerint).

Az Azure AD SSO és az új ereklye (szervezet szerint) konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Új ereklye (szervezet szerint) egyszeri bejelentkezés beállítása](#configure-new-relic-by-organization-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre új ereklye (szervezet szerint), tesztelje](#create-new-relic-by-organization-test-user)** , hogy van-e a "B. Simon" párja (szervezet szerint), amely a felhasználó Azure ad-képviseletéhez van csatolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **új ereklye (szervezet szerint)** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal**rendelkezik, hajtsa végre a következő lépéseket:

    a. Kattintson a **metaadat-fájl feltöltése**elemre.

    ![image](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés**elemre.

    ![image](common/browse-upload-metadata.png)

    c. A metaadat-fájl feltöltése után a rendszer az alapszintű SAML-konfiguráció szakaszban automatikusan feltölti az **azonosítót** és a **Válasz URL-** értékeket:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Ha az **azonosító** és a **Válasz URL-** értékei nem kapnak automatikus polulated, akkor a követelménynek megfelelően adja meg manuálisan az értékeket.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít az új ereklye (szervezet) számára.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **új ereklye (szervezet szerint)** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-new-relic-by-organization-sso"></a>Új ereklye (szervezet szerint) egyszeri bejelentkezésének konfigurálása

Ha az egyszeri bejelentkezést az **új ereklye (szervezet szerint)** oldalon szeretné konfigurálni, el kell küldenie az **alkalmazás-összevonási metaadatok URL-címét** az [új ereklye (szervezet) támogatási csapatnak](https://support.newrelic.com/). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.


### <a name="create-new-relic-by-organization-test-user"></a>Új ereklye (szervezet általi) tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre az új ereklye (szervezet szerint). Az [új ereklye (szervezet szerint) támogatásával](https://support.newrelic.com/) a felhasználók hozzáadhatók az új ereklye (szervezet) platformhoz. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az új ereklye (szervezet szerint) csempére kattint, automatikusan be kell jelentkeznie az új ereklye (szervezet) számára, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További háttéranyagok

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Új ereklye (szervezet szerint) kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)