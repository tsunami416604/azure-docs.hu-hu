---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Firmex VDR-rel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Firmex VDR között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 670ff192-c23e-49e4-8fd1-516e02d8856c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdfb857d3a68081fda84aef33e6b5a4b4d1bce28
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76761237"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-firmex-vdr"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Firmex VDR-rel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Firmex VDR-t az Azure Active Directoryval (Azure AD). Ha integrálja a Firmex VDR-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Firmex VDR-hez.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezhessenek a Firmex VDR-be az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Firmex VDR egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Firmex VDR támogatja az **SP és az IDP** által kezdeményezett SSO-t

* A Firmex konfigurálása után kényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlők a feltételes hozzáféréstől származnak. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-firmex-vdr-from-the-gallery"></a>Firmex VDR hozzáadása a galériából

A Firmex VDR Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Firmex VDR-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Firmex VDR** kifejezést a keresőmezőbe.
1. Válassza a **Firmex VDR** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-firmex-vdr"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a Firmex VDR-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Firmex VDR segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Firmex VDR-ben.

Az Azure AD SSO firmex VDR-rel való konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Firmex VDR Egyszeri bejelentkezést](#configure-firmex-vdr-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Hozzon létre Firmex VDR tesztfelhasználót](#create-firmex-vdr-test-user)** – hogy a Firmex VDR-ben b.Simon megfelelője legyen, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Firmex VDR-alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A beállítások szerkesztéséhez kattintson az **SAML-lel való egyszeri bejelentkezés** beállítása lapon az egyszerű **SAML-konfiguráció** szerkesztési/tollikonjára.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania semmilyen lépést, mivel az alkalmazás már előre integrálva van az Azure-ral.

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írja be az URL-címet:`https://login.firmex.com`

1. Kattintson a **Mentés** gombra.

1. A Firmex VDR alkalmazás egy adott formátumban várja az SAML-állításokat, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Firmex VDR alkalmazás arra számít, hogy néhány további attribútum ot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név | Forrás attribútuma|
    | ------------ | --------- |
    | e-mail | user.mail |

1. Az **egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszban** keresse meg az **összevonási metaadat-XML-t,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Set-up Firmex VDR** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t a Firmex VDR hozzáférést biztosítva a Firmex VDR.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Firmex VDR**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-firmex-vdr-sso"></a>A Firmex VDR SSO konfigurálása

### <a name="before-you-get-started"></a>A kezdés előtt

#### <a name="what-youll-need"></a>Amire szüksége lesz?

-   Aktív Firmex-előfizetés
-   Az Azure AD az SSO szolgáltatás
-   A rendszergazda az SSO konfigurálásához
-   Az Egyszeri bejelentkezés engedélyezése után a vállalat összes felhasználójának sso használatával kell bejelentkeznie a Firmex-be, és nem kell bejelentkezési jelszót használnia.

#### <a name="how-long-will-this-take"></a>Meddig fog ez tartani?

Az SSO megvalósítása néhány percet vesz igénybe. Gyakorlatilag nincs állásidő a Firmex Support között, amely lehetővé teszi az Egyszeri használat talvaló használatát a webhelyszámára, és a vállalat felhasználói nak az Egyszeri sso használatával történő hitelesítése között. Csak kövesse az alábbi lépéseket.

### <a name="step-1---identify-your-companys-domains"></a>1. lépés – A vállalat tartományainak azonosítása

Azonosítsa azokat a tartományokat, amelyekbe a vállalat felhasználói bejelentkeznek.

Példa:

- @firmex.com
- @firmex.ca

### <a name="step-2---contact-firmex-support-with-your-domains"></a>2. lépés - Lépjen kapcsolatba a Firmex ügyfélszolgálatával a domainjeivel

Írjon [e-mailt a Firmex ügyfélszolgálatának,](mailto:support@firmex.com) vagy hívja a 1888 688 4042 x.11-et, hogy beszéljen a Firmex ügyfélszolgálatával. Adja át a domain adatait. A Firmex Support hozzáadja a domaineket a VDR-hez, mint **azt állította domainek**. A rendszergazdának most konfigurálnia kell az SSO-t.

Figyelmeztetés: Amíg a webhely rendszergazdája nem konfigurálja az igényelt tartományokat, a vállalat felhasználói nem tudnak bejelentkezni a VDR-be. A nem vállalati felhasználók (azaz a vendégfelhasználók) továbbra is bejelentkezhetnek e-mailcímükkel/jelszavukkal. A konfiguráció néhány percet vesz igénybe.

### <a name="step-3---configure-the-claimed-domains"></a>3. lépés - Az igényelt tartományok konfigurálása

1. Jelentkezzen be a Firmex webhelyadminisztrátoraként.
1. A bal felső sarokban kattintson a vállalat emblémájára.
1. Válassza az **SSO** lapot. Ezután válassza **az SSO-konfiguráció lehetőséget.** Kattintson a konfigurálni kívánt tartományra.

    ![Igényelt tartományok](./media/firmex-vdr-tutorial/edit-sso.png)  

1. A rendszergazda töltse ki a következő mezőket. A mezőket az identitásszolgáltatótól kell venni:  

    ![SSO-konfiguráció](./media/firmex-vdr-tutorial/SSO-config.png)

    a. Az **Entitásazonosító** szövegdobozába illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    b. Az **identitásszolgáltató URL-címmezőjébe** illessze be a **bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    c. **Nyilvános kulcsú tanúsítvány** – Hitelesítési célokra a kibocsátó digitálisan aláírhatja az SAML-üzenetet. Az üzenet aláírásának ellenőrzéséhez az üzenetfogadó egy nyilvános kulcsot használ, amely ről ismert, hogy a kibocsátóhoz tartozik. Hasonlóképpen, egy üzenet titkosításához a kibocsátónak ismernie kell a végső fogadóhoz tartozó nyilvános titkosítási kulcsot. Mindkét esetben – az aláírás és a titkosítás – a megbízható nyilvános kulcsokat előre meg kell osztani.  Ez az **Összevonási metaadat-XML** **X509Certificate tanúsítványa**

    d. Kattintson a **Mentés** gombra az SSO-konfiguráció befejezéséhez. A módosítások azonnal életbe lépnek.

1. Jelenleg az Egyszeri sso engedélyezve van a webhelyen.

### <a name="create-firmex-vdr-test-user"></a>Firmex VDR tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználót hoz létre a Firmex alkalmazásban. Működjön együtt a [Firmex támogatási csapatával](mailto:support@firmex.com) a felhasználók firmex platformon való hozzáadásához. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Firmex VDR csempére kattint, automatikusan be kell jelentkeznie a Firmex VDR-be, amelyhez az Egyszeri bejelentkezést beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Firmex VDR-t az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hogyan védheti meg a Firmex-et a fejlett láthatósággal és ellenőrzésekkel?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
