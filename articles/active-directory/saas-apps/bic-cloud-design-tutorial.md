---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a BIC Cloud Design programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a BIC Cloud Design között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68709502-480d-4f47-add2-05e0046be8ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13f6de35400280d68227af1dd7e3a981494d9e61
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77088213"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bic-cloud-design"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a BIC Cloud Design programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a BIC Cloud Design alkalmazást az Azure Active Directoryval (Azure AD). Ha integrálja a BIC Cloud Design alkalmazást az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, hogy ki férhet hozzá a BIC Cloud Design-hoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezhessenek a BIC Cloud Design szolgáltatásba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* A BIC Cloud Design egyszeri bejelentkezés (SSO) előfizetéssel rendelkezik.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A BIC Cloud Design támogatja az **SP** által kezdeményezett sso-t
* A BIC Cloud Design konfigurálása után kényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlők a feltételes hozzáféréstől származnak. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-bic-cloud-design-from-the-gallery"></a>A BIC Cloud Design hozzáadása a galériából

A BIC Cloud Design Azure AD-be való integrálásához hozzá kell adnia a BIC Cloud Design-t a galériából a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **katalógus hozzáadásszakaszában** írja be a **BIC Cloud Design kifejezést** a keresőmezőbe.
1. Válassza a **BIC Cloud Design** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bic-cloud-design"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a BIC Cloud Design alkalmazáshoz

Konfigurálja és tesztelje az Azure AD SSO-t a BIC Cloud Design segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó bic cloud design.

Az Azure AD SSO konfigurálásához és teszteléséhez a BIC Cloud Design segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[A BIC Cloud Design SSO konfigurálása](#configure-bic-cloud-design-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Hozzon létre BIC Cloud Design tesztfelhasználót](#create-bic-cloud-design-test-user)** – hogy b.Simon megfelelője legyen a BIC Cloud Design-ban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **BIC Cloud Design** alkalmazásintegrációs lapon keresse meg a Kezelés **szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha **a Szolgáltató metaadatfájlja**található, hajtsa végre az alábbi lépéseket:

    a. Kattintson **a Metaadatfájl feltöltése gombra.**

    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

    ![metaadatfájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadatfájl sikeres feltöltése után az **azonosító** érték automatikusan feltöltődik az egyszerű SAML-konfiguráció szakaszban.

    ![A BIC Cloud Design Domain és URL-ek egyszeri bejelentkezési információi](common/sp-identifier.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:

    |||
    |-|-|
    | `https://<customer-specific-name/tenant>.biccloud.com`|
    | `https://<customer-specific-name/tenant>.biccloud.de` |

    > [!Note]
    > Ha az **azonosító** érték nem kap automatikus polulated, akkor kérjük, töltse ki az értéket manuálisan a követelménynek megfelelően. A bejelentkezési URL-érték nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [a BIC Cloud Design Ügyfél támogatási csapatával,](mailto:bicsupport@gbtec.de) hogy megkapja ezt az értéket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. A BIC Cloud Design alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumkonfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a BIC Cloud Design alkalmazás arra számít, hogy néhány további attribútum ot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név | Forrás attribútuma|
    | ------------ | --------- |
    | Név | user.name |
    | E-mail cím | user.mail |
    | Névazonosító | user.userprincipalname |
    | e-mail | user.mail |
    | névteszt | user.displayname |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

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

Ebben a szakaszban engedélyezi b.Simon az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a BIC Cloud Design.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **BIC Cloud Design**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-bic-cloud-design-sso"></a>A BIC felhőalapú tervezési sso-jának konfigurálása

Az egyszeri bejelentkezés konfigurálásához a **BIC Cloud Design** oldalon el kell küldenie az App Federation **metaadat-címét** a [BIC Cloud Design támogatási csapatának.](mailto:bicsupport@gbtec.de) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-bic-cloud-design-test-user"></a>Bic Cloud Design tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználót hoz létre a BIC Cloud Design alkalmazásban. Együttműködve [bic cloud design támogatási csapatával](mailto:bicsupport@gbtec.de) a BIC Cloud Design platform felhasználóinak hozzáadásához. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a BIC Cloud Design csempére kattint, automatikusan be kell jelentkeznie a BIC Cloud Design programba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a BIC Cloud Design alkalmazást az Azure AD-vel](https://aad.portal.azure.com/)