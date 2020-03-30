---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja Logz.io – Felhőbe ágyazhatóság mérnökök számára | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Logz.io – A mérnökök felhőbeli megfigyelhetősége között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0984471-d12f-4f58-896e-194ea45b01fd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/26/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de1c929ffa790d2abe3a1922cecc2175cd7a8e12
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385479"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-logzio---cloud-observability-for-engineers"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja Logz.io – Felhőbeli megfigyelhetőség mérnökök számára

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja Logz.io – Felhőbeli megfigyelhetőség mérnökök számára az Azure Active Directoryval (Azure AD). Ha integrálja Logz.io - Cloud Observability for Engineers az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Logz.io - Cloud Observability for Engineers.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve Logz.io – Cloud Megfigyelhetősége mérnökök számára az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Logz.io – Felhőbeli megfigyelhetőség mérnökök egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Logz.io - A mérnökök felhőbeli megfigyelhetősége támogatja az **IDP** által kezdeményezett egyszeri szolgáltató iszt.
* Miután konfigurálta Logz.io - Cloud Observability for Engineers kényszerítheti munkamenet-vezérlés, amely védi a kiszivárgásés és a szervezet bizalmas adatainak valós idejű beszivárgása. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-logzio---cloud-observability-for-engineers-from-the-gallery"></a>Logz.io hozzáadása - Cloud Observability for Engineers from the gallery

A Logz.io - Cloud Observability for Engineers integrálásának konfigurálásához hozzá kell adnia Logz.io – A galériából a mérnökök felhőbeli megfigyelhetősége a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a Logz.io - Cloud Observability for Engineers (A **mérnökök felhőalapú megfigyelhetősége** a mérnökök számára) mezőbe.
1. Válassza ki **Logz.io - Cloud Observability for Engineers** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-logzio---cloud-observability-for-engineers"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Logz.io – A mérnökök felhőbeli megfigyelhetősége

Konfigurálja és tesztelje az Azure AD Egyszeri szolgáltatást Logz.io – A **b.Simon**nevű tesztfelhasználó használatával a mérnökök felhőbeli megfigyelhetősége. Ahhoz, hogy az egyszeri bejelentkezés működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között Logz.io - A mérnökök felhőbeli megfigyelhetősége.

Az Azure AD Egyszeri szolgáltatás konfigurálásához és teszteléséhez Logz.io - Cloud Observability for Engineers segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja Logz.io a mérnökök számára a felhőmegfigyelést](#configure-logzio-cloud-observability-for-engineers-sso)** – az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    1. **[Hozzon létre Logz.io Cloud Observability for Engineers teszt felhasználó](#create-logzio-cloud-observability-for-engineers-test-user)** - hogy egy megfelelője B.Simon Logz.io - Cloud megfigyelhetőség mérnökök, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Logz.io – A mérnökök felhőbeli megfigyelhetősége alkalmazásintegrációs** lapon keresse meg a Kezelés **szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`urn:auth0:logzio:CONNECTION-NAME`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://logzio.auth0.com/login/callback?connection=CONNECTION-NAME`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címével. Lépjen kapcsolatba [Logz.io – A mérnökök ügyféltámogatási csapatának felhőbeli megfigyelhatósága](mailto:help@logz.io) ezen értékek lekéri ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Logz.io – A Mérnökök felhőbeli megfigyelhetősége egy adott formátumban várja az SAML-állításokat, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML tokenattribútum-konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül Logz.io - Cloud Observability for Engineers alkalmazás arra számít, hogy néhány további attribútumok at kell átadni vissza SAML választ, amelyek az alábbiakban látható. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.
    
    | Név |  Forrás attribútuma|
    | ---------------| --------- |
    | munkamenet-lejárat | user.session-lejárat |
    | e-mail | user.mail |
    | Csoport | user.csoportok |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Beállítás i Logz.io – A mérnökök felhőbeli megfigyelhetősége** című szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t azáltal, hogy hozzáférést biztosít Logz.io - Cloud megfigyelhetőségét mérnökök.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Logz.io - Cloud Observability for Engineers**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-logzio-cloud-observability-for-engineers-sso"></a>Konfigurálja Logz.io a felhő megfigyelhetőségét a mérnökök sso-jának beállításához

Az egyszeri bejelentkezés konfigurálásához **Logz.io – A mérnökök felhőbeli megfigyelhetősége érdekében** el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-címeket az Azure Portalról [a Logz.io – Cloud Observability for Engineers támogatási csapatszámára.](mailto:help@logz.io) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-logzio-cloud-observability-for-engineers-test-user"></a>Hozzon létre Logz.io felhőmegfigyelhetőségét a mérnökök tesztfelhasználójával

Ebben a szakaszban hozzon létre egy felhasználó nevű Britta Simon Logz.io - Cloud megfigyelhetőség a mérnökök. Együttműködve [Logz.io - Cloud Observability for Engineers támogatási csapat](mailto:help@logz.io) a felhasználók hozzáadása a Logz.io - Cloud Observability for Engineers platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a hozzáférési panelen a Logz.io - Cloud Observability for Engineers csempére kattint, automatikusan be kell jelentkeznie a Logz.io - Felhőmegfigyelhetőség olyan mérnökök számára, amelyekhez beállítja az Egyszeri bejelentkezést. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki Logz.io - Felhőbe való betartathatóság az Azure AD-vel rendelkező mérnökök számára](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Logz.io védelme - Felhőbe való betartathatóság a mérnökök számára a fejlett láthatóság és vezérlés](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

