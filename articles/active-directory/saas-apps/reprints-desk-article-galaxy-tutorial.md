---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az újranyomtatások asztalával – cikk, Galaxy | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és újranyomtatni az Desk-article Galaxy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 00fd0b0e-8de1-4e64-8a9c-4b65c0e47fe0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d94fc0bc736d2136d4711ab02ae554605deeb35
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76761265"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-reprints-desk---article-galaxy"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az újranyomtatások Íróasztalával – cikk galaxis

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az újranyomtatások Desk-article Galaxy Azure Active Directory (Azure AD) használatával. Amikor az Azure AD-vel integrálja a Reprints Desk-article Galaxyt, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér az íróasztal-cikkhez tartozó galaxis újranyomtatásához.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy Újranyomtassák az asztalt az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az asztal újranyomtatása – a Galaxy egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az asztal újranyomtatása – a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogató galaxis

* Az asztal újranyomtatása – a Galaxy **csak időben támogatja a** felhasználók üzembe helyezését.

* [Miután konfigurálta a Reprints Desk-article Galaxy a munkamenet-vezérlők kikényszeríthető, amelyekkel valós időben kiszűrése és beszivároghat a szervezet bizalmas adatai. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-reprints-desk---article-galaxy-from-the-gallery"></a>Újranyomtatási asztal felvétele – cikk a katalógusból

Az újranyomtatások Desk-cikkének az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a katalógusból a felügyelt SaaS-alkalmazások listájához a katalógusból a katalógust.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **asztal újranyomtatása-cikk galaxis** kifejezést.
1. Jelölje be az **asztal újranyomtatása – cikk** az eredmények panelen, majd az alkalmazás hozzáadása lehetőséget. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-reprints-desk---article-galaxy"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az újranyomtatáshoz – cikk galaxis

Az Azure AD SSO konfigurálása és tesztelése a Reprints Desk-article Galaxy a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Reprints Desk-article Galaxyban.

Az Azure AD SSO konfigurálásához és teszteléséhez a Reprints Desk-article Galaxy használatával végezze el a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Konfigurálja az asztal újranyomtatása című cikk Galaxy SSO](#configure-reprints-desk-article-galaxy-sso)** – az egyszeri bejelentkezési beállítások konfigurálását az alkalmazás oldalán.
    * **[Újranyomtatási Desk-cikk a Galaxy test User](#create-reprints-desk-article-galaxy-test-user)** – to have a B. Simon egy párja a Reprints Desk-cikk a felhasználó Azure ad-képviseletéhez kapcsolódó galaxis.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Reprints Desk-cikk Galaxy** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban az alkalmazás előre konfigurálva van, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Save (Mentés** ) gombra kattintva.


1. Az asztal újranyomtatása – a Galaxy Application az SAML-jogcímeket egy adott formátumban várja el, amely megköveteli, hogy egyéni attribútum-hozzárendeléseket adjon hozzá az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül az íróasztal – cikk a Galaxy-alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name (Név) | Forrás attribútum|
    | ------------ | --------- |
    | FirstName | User. givenName |
    | LastName | felhasználó. vezetéknév |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A telepítés **újranyomtatása Desk-cikk Galaxy** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát, ha hozzáférést biztosít az íróasztal-cikkhez.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **asztal újranyomtatása**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-reprints-desk-article-galaxy-sso"></a>Az újranyomtatások beállítása a Galaxy SSO bejelentkezéshez

Ha egyszeri bejelentkezést szeretne beállítani az **újranyomtatások íróasztala számára – a Galaxy** oldalán el kell küldenie a letöltött **összevonási METAADATOKat tartalmazó XML** -fájlt és a megfelelő másolt url-címeket a Azure Portalről, hogy [újranyomtassa az Desk-article Galaxy támogatási csapatát](mailto:customersupport@reprintsdesk.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-reprints-desk-article-galaxy-test-user"></a>Újranyomtatási Desk-cikk létrehozása Galaxy test User

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre az újranyomtatások Desk-cikk galaxisban. Az asztal újranyomtatása – a cikk az igény szerinti felhasználói üzembe helyezést is támogatja, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik az újranyomtatások Desk-cikk galaxisban, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Reprints Desk-article Galaxy csempe elemre kattint, automatikusan be kell jelentkeznie a Reprints Desk-cikkhez, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További háttéranyagok

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálkozzon az Azure AD-vel való újranyomtatással.](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Reprints Desk-cikk a speciális láthatóságot és vezérlést biztosító Galaxy](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
