---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Azure-hoz készült NS1 SSO-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a NS1 SSO között az Azure-ban.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 24a1afb6-b8b6-4787-bd4b-8fe3a32f8def
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5ba34aeb97808c19c78f187dcc68309893d1d1b
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429746"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Azure-hoz készült NS1 SSO-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a NS1 SSO-t az Azure-ba a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja az NS1 SSO-t az Azure-ba, a következőket teheti:

* Az Azure AD-ben elérhető NS1 SSO-hozzáférés az Azure-ban.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezzenek az Azure-ba az Azure AD-fiókjával való NS1 egyszeri bejelentkezésre.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* NS1 SSO az Azure egyszeri bejelentkezés (SSO) számára engedélyezett előfizetés esetén.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A NS1 SSO for Azure támogatja az **SP és a identitásszolgáltató** által kezdeményezett SSO-t
* Miután konfigurálta a NS1 SSO-t az Azure-hoz, kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="adding-ns1-sso-for-azure-from-the-gallery"></a>NS1 SSO hozzáadása az Azure-hoz a katalógusból

Az Azure NS1 SSO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Azure-hoz készült NS1 SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe a **ns1 egyszeri bejelentkezés az Azure** -ban kifejezést.
1. Válassza ki a **ns1 egyszeri bejelentkezés az Azure** -hoz az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az Azure-hoz készült NS1 SSO-hoz

Konfigurálja és tesztelje az Azure AD SSO-t az Azure-hoz készült NS1 SSO-vel egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a NS1 SSO for Azure-ban.

Az Azure AD SSO az Azure-hoz készült NS1 SSO-vel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Ns1 SSO konfigurálása az Azure SSO](#configure-ns1-sso-for-azure-sso)** -hoz – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
    1. **[Hozzon létre ns1 SSO-t az Azure test User](#create-ns1-sso-for-azure-test-user)** számára – ha a felhasználó Azure ad-beli képviseletéhez csatolt B. Simon-hez tartozó ns1 SSO-t szeretne.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **ns1 SSO for Azure** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be a következő URL-címet: `https://api.nsone.net/saml/metadata`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://api.nsone.net/saml/sso/<ssoid>`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet: `https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. A válasz URL-címének frissítése a tényleges válasz URL-címével. Az érték beszerzéséhez lépjen kapcsolatba a [ns1 SSO-val az Azure ügyfél-támogatási csapatával](mailto:techops@nsone.net) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az Azure-alkalmazásokhoz készült NS1 SSO adott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon, a **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![Attribútum szakasz](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Kattintson az attribútum nevére a jogcím szerkesztéséhez.

    ![Attribútum szakasz](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Válassza az **átalakítás**lehetőséget.

    ![Attribútum szakasz](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. Az **átalakítás kezelése** szakaszban hajtsa végre a következő lépéseket:

    ![Attribútum szakasz](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Válassza a **ExactMailPrefix ()** lehetőséget **átalakításként**.

    1. Válassza a **User. userPrincipalName** elemet az **1. paraméterként**.

    1. Kattintson a **Hozzáadás** gombra.

    1. Kattintson a **Mentés** gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban az Azure egyszeri bejelentkezés használatát teszi lehetővé a B. Simon számára, hogy hozzáférést biztosítson az Azure-hoz készült NS1 SSO-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **ns1 egyszeri bejelentkezés az Azure**-hoz lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-ns1-sso-for-azure-sso"></a>NS1 SSO konfigurálása az Azure SSO-hoz

Ha az egyszeri bejelentkezést az **Azure ns1 SSO** -ra szeretné konfigurálni, az [Azure támogatási csapatának](mailto:techops@nsone.net)az **alkalmazás-összevonási metaadatok URL-címét** kell elküldenie a ns1 SSO-hoz. Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-ns1-sso-for-azure-test-user"></a>NS1 SSO létrehozása az Azure test userhez

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre az Azure-beli NS1 SSO-hoz. A NS1 SSO használata az Azure támogatási csapatához a felhasználók hozzáadásához a NS1 SSO for Azure platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a NS1 SSO for Azure csempére kattint, automatikusan be kell jelentkeznie az Azure-hoz készült NS1 SSO-ra, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Az Azure AD-vel való NS1 egyszeri bejelentkezés kipróbálása](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)