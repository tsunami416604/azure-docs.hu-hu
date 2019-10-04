---
title: Az alkalmazás listázása a Azure Active Directory Application Galleryben | Microsoft Docs
description: Útmutató az egyszeri bejelentkezést támogató alkalmazások listázásához a Azure Active Directory app Galleryben
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88d74fe794f4de95b7ba8b0dd5575ca56d2016e5
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176842"
---
# <a name="how-to-list-your-application-in-the-azure-active-directory-application-gallery"></a>Útmutató: Az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában

Ez a cikk bemutatja, hogyan listázhat egy alkalmazást az Azure AD-alkalmazás-katalógusban, hogyan alkalmazhat egyszeri bejelentkezést (SSO), és hogyan kezelheti az adatokat.

## <a name="what-is-the-azure-ad-application-gallery"></a>Mi az Azure AD Application Gallery?

- Az ügyfelek megtalálják a lehető legjobb egyszeri bejelentkezési élményt.
- Az alkalmazás konfigurálása egyszerű és minimális.
- A gyors keresés megkeresi az alkalmazást a galériában.
- Az ingyenes, alapszintű és prémium szintű Azure AD-ügyfelek használhatják ezt az integrációt.
- A kölcsönös ügyfelek részletes konfigurációs oktatóanyagot kapnak.
- A SCIM-t használó ügyfelek ugyanazt az alkalmazást használhatják a kiépítés során.

## <a name="prerequisites"></a>Előfeltételek

- Összevont alkalmazások esetében (Open ID és SAML/WS-Fed) az alkalmazásnak támogatnia kell az SaaS modellt az Azure AD Galleryben való ismerkedéshez. A nagyvállalati katalógus alkalmazásai több ügyfél-konfigurációt is támogatnak, és nem adott ügyfelet.

- A nyitott AZONOSÍTÓk összekapcsolásához az alkalmazásnak több-bérlős és [Azure ad-engedélyezési keretrendszert](consent-framework.md) kell alkalmaznia az alkalmazáshoz. A felhasználó elküldheti a bejelentkezési kérést egy közös végpontnak, így bármely ügyfél beleegyezik az alkalmazásba. A felhasználó hozzáférését a bérlő azonosítója és a tokenben kapott felhasználó egyszerű felhasználóneve alapján szabályozhatja.

- Az SAML 2.0/WS-fed esetében az alkalmazásnak képesnek kell lennie az SAML/WS-fed SSO-integráció SP-vagy IDENTITÁSSZOLGÁLTATÓ módban történő elvégzésére. Győződjön meg arról, hogy ez megfelelően működik a kérelem elküldése előtt.

- A jelszó egyszeri bejelentkezéséhez győződjön meg arról, hogy az alkalmazás támogatja az űrlapos hitelesítést, hogy a jelszó-tárolók a várt módon működjenek az egyszeri bejelentkezéshez.

- Állandó fiókra van szükség a 2. atleast regisztrált felhasználóval való teszteléshez.

## <a name="submit-the-request-in-the-portal"></a>A kérelem elküldése a portálon

Miután tesztelte az alkalmazás-integrációt az Azure AD-vel, küldje el a hozzáférési kérelmét az [Application Network](https://microsoft.sharepoint.com/teams/apponboarding/Apps)-portálon. Ha rendelkezik Office 365-fiókkal, akkor jelentkezzen be a portálra. Ha nem, használja a Microsoft-fiókt (például az Outlookot vagy a Hotmailt) a bejelentkezéshez.

Ha a bejelentkezés után a következő oldal jelenik meg, lépjen kapcsolatba az [Azure ad SSO integrációs csapatával](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) , és adja meg azt az e-mail-fiókot, amelyet a kérelem elküldéséhez használni kíván. Ezt követően az Azure AD csapata felveszi a fiókot a Microsoft Application Network portálon.

![Hozzáférési kérelem a SharePoint-portálon](./media/howto-app-gallery-listing/errorimage.png)

A fiók hozzáadása után bejelentkezhet a Microsoft Application Network portálra.

Ha a bejelentkezés után a következő oldal jelenik meg, adjon meg egy üzleti indoklást a hozzáférés igénybe lépéséhez a szövegmezőben, majd válassza a **hozzáférés kérése**lehetőséget.

  ![Hozzáférési kérelem a SharePoint-portálon](./media/howto-app-gallery-listing/accessrequest.png)

Csapatunk áttekinti a részleteket, és ennek megfelelően hozzáférést biztosít Önnek. A kérelem jóváhagyását követően bejelentkezhet a portálra, és elküldheti a kérést, ha a kezdőlapon a **küldési kérelem (ISV)** csempére kattint.

![SharePoint-portál kezdőlapja](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Ha problémák merülnek fel a hozzáféréssel kapcsolatban, lépjen kapcsolatba az [Azure ad SSO integrációs csapatával](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-federation-protocol"></a>SSO implementálása összevonási protokoll használatával

Egy alkalmazás az Azure AD-katalógusban való listázásához először a következő, az Azure AD által támogatott összevonási protokollok egyikét kell végrehajtania, és el kell fogadnia az Azure AD Application Gallery használati feltételeit. Olvassa el az Azure AD Application Gallery használati feltételeit innen [.](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)

- **OpenID Connect**: Az alkalmazás az Azure AD-val való integrálásához az Open ID csatlakozási protokoll használatával kövesse a [fejlesztők utasításait](authentication-scenarios.md).

    ![Az OpenID Connect-alkalmazás katalógusba helyezésének idővonala](./media/howto-app-gallery-listing/openid.png)

    * Ha az OpenID Connect használatával szeretné felvenni az alkalmazást a katalógusban szereplő listára, válassza az **OpenID connect & OAuth 2,0** lehetőséget a fentiekben leírtak szerint.
    * Ha problémák merülnek fel a hozzáféréssel kapcsolatban, lépjen kapcsolatba az [Azure ad SSO integrációs csapatával](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **SAML 2,0** vagy **ws-fed**: Ha az alkalmazás támogatja az SAML 2,0-et, azt közvetlenül integrálhatja egy Azure AD-Bérlővel az [egyéni alkalmazások hozzáadására szolgáló utasítások](../active-directory-saas-custom-apps.md)segítségével.

  ![Az SAML 2,0-vagy WS-fed-alkalmazás katalógusba való felvételének ütemterve](./media/howto-app-gallery-listing/saml.png)

  * Ha az **saml 2,0** vagy a **ws-fed**használatával szeretné felvenni az alkalmazást a katalógusba, válassza a fent látható **SAML 2.0/ws-fed** lehetőséget.
  * Ha problémák merülnek fel a hozzáféréssel kapcsolatban, lépjen kapcsolatba az [Azure ad SSO integrációs csapatával](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-password-sso"></a>SSO implementálása jelszó-SSO használatával

Hozzon létre egy webalkalmazást, amely tartalmaz egy HTML-bejelentkezési oldalt a [jelszó alapú egyszeri bejelentkezés](../manage-apps/what-is-single-sign-on.md)konfigurálásához. A jelszó-alapú egyszeri bejelentkezés (más néven a jelszó-tároló) lehetővé teszi a felhasználók hozzáférésének és jelszavának kezelését az identitás-összevonást nem támogató webalkalmazásokhoz. Olyan helyzetekben is hasznos, amikor több felhasználónak egyetlen fiókot kell megosztania, például a szervezete közösségi Media app-fiókjaival.

![Jelszó-SSO-alkalmazás listázási idővonala a katalógusban](./media/howto-app-gallery-listing/passwordsso.png)

* Ha hozzá szeretné adni az alkalmazást a katalógusban a jelszó-SSO használatával, válassza a fenti **jelszó egyszeri bejelentkezés** lehetőséget.
* Ha problémák merülnek fel a hozzáféréssel kapcsolatban, lépjen kapcsolatba az [Azure ad SSO integrációs csapatával](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="requesting-for-user-provisioning"></a>Felhasználói kiépítés kérelmezése

A felhasználók üzembe helyezéséhez kövesse az alábbi eljárást:

   ![Az SAML-alkalmazás katalógusba való felvételének ütemterve](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="updateremove-existing-listing"></a>Meglévő lista frissítése/eltávolítása

Egy meglévő alkalmazás Azure AD-katalógusban való frissítéséhez vagy eltávolításához először el kell küldenie a kérést az [Application Network portálon](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Ha rendelkezik Office 365-fiókkal, akkor jelentkezzen be a portálra. Ha nem, használja a Microsoft-fiókt (például az Outlookot vagy a Hotmailt) a bejelentkezéshez.

- Válassza ki a megfelelő beállítást, ahogy az az alábbi képen is látható:

    ![Az SAML-alkalmazás katalógusba való felvételének ütemterve](./media/howto-app-gallery-listing/updateorremove.png)

    * Ha egy meglévő alkalmazást szeretne frissíteni, válassza a megfelelő lehetőséget a követelménynek megfelelően.
    * Ha el szeretne távolítani egy meglévő alkalmazást az Azure AD-katalógusból, válassza az **alkalmazás listázása elemet a gyűjteményből**lehetőséget.
    * Ha problémák merülnek fel a hozzáféréssel kapcsolatban, lépjen kapcsolatba az [Azure ad SSO integrációs csapatával](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="listing-requests-by-customers"></a>Ügyfelek kéréseinek listázása

Az ügyfelek az alkalmazásra **vonatkozó kérelmeket** -> az**új kérelem**beküldésére kattintva küldhetik be az alkalmazások listázására.

![Az ügyfél által kért alkalmazások csempe megjelenítése](./media/howto-app-gallery-listing/customer-submit-request.png)

Alább látható az ügyfél által kért alkalmazások folyamata –

![Az ügyfél által kért alkalmazások folyamatát jeleníti meg](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>Ütemtervek

Az SAML 2,0 vagy a WS-fed alkalmazás a katalógusban való listázásának ütemterve 7-10 munkanap.

  ![Az SAML-alkalmazás katalógusba való felvételének ütemterve](./media/howto-app-gallery-listing/timeline.png)

A katalógusban található OpenID Connect-alkalmazás listázási folyamatának idővonala 2-5 munkanap.

  ![Az SAML-alkalmazás katalógusba való felvételének ütemterve](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Azok következményeinek

Bármilyen eszkaláció esetén küldjön e-mailt az [Azure ad SSO integrációs csapatának](mailto:SaaSApplicationIntegrations@service.microsoft.com) , SaaSApplicationIntegrations@service.microsoft.com amely a lehető leghamarabb válaszol.
