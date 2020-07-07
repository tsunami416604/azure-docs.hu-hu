---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Shopify Plus szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Shopify plusz között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4f3f2888-1bc8-42c6-94d5-163d05eeb66d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd71789d6c2fb54007f3d6623ba8d14f98383b5a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027647"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-shopify-plus"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Shopify Plus szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Shopify Plust Azure Active Directory (Azure AD) használatával. A Shopify Plus és az Azure AD integrálásával a következőket teheti:

* Szabályozza az Azure AD-t, hogy ki férhet hozzá a Shopify Plushoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Shopify-be és az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Shopify és az egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Shopify Plus támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t

* A Shopify és a konfigurálását követően kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-shopify-plus-from-the-gallery"></a>A Shopify Plus hozzáadása a gyűjteményhez

A Shopify és az Azure AD integrálásának konfigurálásához hozzá kell adnia a Shopify Plust a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe a **Shopify Plus** kifejezést.
1. Válassza az **Shopify Plus** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-shopify-plus"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Shopify Plus szolgáltatásban

Konfigurálja és tesztelje az Azure AD SSO-t a Shopify Plus használatával egy **B. Simon**nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Shopify Plusban.

Az Azure AD SSO konfigurálásához és teszteléséhez a Shopify Plus használatával végezze el a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[Shopify és az egyszeri bejelentkezés konfigurálása](#configure-shopify-plus-sso)** – az egyszeri bejelentkezési beállítások konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Shopify Plus test User](#create-shopify-plus-test-user)** -to have a B. Simon in Shopify Plus partnere, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Shopify Plus** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://accounts.shopify.com/saml/consume/organization/<ORGANIZATION_ID>`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:`https://shopify.plus/login`

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. Frissítse az értéket a tényleges válasz URL-címével. Az érték beszerzéséhez lépjen kapcsolatba a [Shopify és az ügyfél-támogatási csapattal](mailto:plus-user-management@shopify.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Shopify Plus alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül az Shopify Plus alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name |  |  Forrás attribútum|
    | ---------------| --------------- | --------- |
    | e-mail | | User. mail |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Shopify Plushoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Shopify plusz**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-shopify-plus-sso"></a>Shopify és egyszeri bejelentkezés konfigurálása

A teljes lépések megtekintéséhez tekintse meg az [SAML-integrációk beállításával kapcsolatos Shopify dokumentációját](https://help.shopify.com/en/manual/shopify-plus/saml).

Ha az egyszeri bejelentkezést az **Shopify plusz** oldalon szeretné konfigurálni, másolja az **alkalmazás-összevonási metaadatok URL-címét** a Azure Active Directoryból. Ezután jelentkezzen be a [szervezeti rendszergazdába](https://shopify.plus) , és lépjen a **felhasználók**  >  **biztonsága**elemre. Válassza a **konfiguráció beállítása**lehetőséget, majd illessze be az alkalmazás-összevonási metaadatok URL-címét az **Identity Provider metaadatok URL-címe** szakaszba. A lépés befejezéséhez válassza a **Hozzáadás** lehetőséget.

### <a name="create-shopify-plus-test-user"></a>Shopify és tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Shopify Plusban. Térjen vissza a **felhasználók** szakaszhoz, és adjon hozzá egy felhasználót az e-mail-cím és az engedélyek megadásával. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="enforce-saml-authentication"></a>SAML-hitelesítés betartatása

> [!NOTE]
> Javasoljuk, hogy az integrációt az egyes felhasználók használatával széles körben alkalmazza.

Egyéni felhasználók:
1. Nyissa meg a Shopify egy egyéni felhasználó lapját, és egy Azure AD által felügyelt e-mail-tartománnyal és a Shopify Plus-ben ellenőrzött.
1. Az SAML hitelesítés szakaszban válassza a **Szerkesztés**lehetőséget, válassza a **kötelező**lehetőséget, majd kattintson a **Mentés**gombra.
1. Ellenőrizze, hogy a felhasználó sikeresen be tud-e jelentkezni a identitásszolgáltató által kezdeményezett és az SP által kezdeményezett folyamatokon keresztül.

Egy e-mail tartományba tartozó összes felhasználó esetén:
1. Térjen vissza a **Biztonság** lapra.
1. Válassza ki az SAML hitelesítési beállításhoz **szükséges** értéket. Ez az SAML-t az e-mail-tartománnyal rendelkező összes felhasználóra kikényszeríti az Shopify Plus-ben.
1. Kattintson a **Mentés** gombra.

> [!IMPORTANT]
> Az SAML engedélyezése az e-mail-tartomány összes felhasználója számára hatással van az alkalmazást használó összes felhasználóra. A felhasználók nem tudnak bejelentkezni a szokásos bejelentkezési oldalára. Csak Azure Active Directoryon keresztül férhetnek hozzá az alkalmazáshoz. A Shopify nem biztosít olyan biztonsági mentési bejelentkezési URL-címet, amelyen a felhasználók bejelentkezhetnek a normál felhasználónevével és jelszavával. Ha szükséges, az SAML kikapcsolásához forduljon a Shopify támogatási szolgálatához.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Shopify plusz csempére kattint, automatikusan be kell jelentkeznie a Shopify, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Shopify Plust az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Shopify és a speciális láthatóság és vezérlők elleni védelem](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
