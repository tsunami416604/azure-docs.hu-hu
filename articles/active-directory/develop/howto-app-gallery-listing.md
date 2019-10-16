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
ms.openlocfilehash: c77657101f5cd8a117b2163386f6d551b7985458
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374072"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában

Ez a cikk bemutatja, hogyan listázhat alkalmazást az Azure Active Directory (Azure AD) alkalmazás-katalógusban, hogyan implementálhat egyszeri bejelentkezést (SSO), és hogyan kezelheti az adatokat.

## <a name="what-is-the-azure-ad-application-gallery"></a>Mi az Azure AD Application Gallery?

- Az ügyfelek megtalálják a lehető legjobb egyszeri bejelentkezési élményt.
- Az alkalmazás konfigurálása egyszerű és minimális.
- A gyors keresés megkeresi az alkalmazást a galériában.
- Az ingyenes, alapszintű és prémium szintű Azure AD-ügyfelek használhatják ezt az integrációt.
- A kölcsönös ügyfelek részletes konfigurációs oktatóanyagot kapnak.
- Azok a felhasználók, akik a rendszert használják a tartományok közötti Identitáskezelés ([scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) számára, használhatják a kiépítés használatát ugyanahhoz az alkalmazáshoz.

## <a name="prerequisites"></a>Előfeltételek

- Összevont alkalmazások esetében (Open ID és SAML/WS-Fed) az alkalmazásnak támogatnia kell az Azure AD-katalógusban való ismerkedéshez szükséges szoftveres (SaaS) modellt. A nagyvállalati katalógus alkalmazásainak több ügyfél-konfigurációt kell támogatnia, és nem adott ügyfelet.
- A nyitott AZONOSÍTÓk összekapcsolásához az alkalmazásnak több-Bérlőnek kell lennie, és az [Azure ad-beli engedélyezési keretrendszert](consent-framework.md) megfelelően kell megvalósítani az alkalmazáshoz. A felhasználó elküldheti a bejelentkezési kérést egy közös végpontnak, így bármely ügyfél beleegyezik az alkalmazásba. A felhasználó hozzáférését a bérlő azonosítója és a tokenben kapott felhasználó egyszerű felhasználóneve alapján szabályozhatja.
- Az SAML 2.0/WS-fed esetében az alkalmazásnak képesnek kell lennie az SAML/WS-fed SSO-integráció SP-vagy IDENTITÁSSZOLGÁLTATÓ módban történő elvégzésére. Győződjön meg arról, hogy ez a funkció megfelelően működik a kérelem elküldése előtt.
- Az egyszeri jelszavas bejelentkezéshez győződjön meg arról, hogy az alkalmazás támogatja az űrlapos hitelesítést, hogy a jelszó-tárolót úgy lehessen elvégezni, hogy az egyszeri bejelentkezés a várt módon működjön.
- Állandó fiókra van szükség legalább két regisztrált felhasználó teszteléséhez.

## <a name="submit-the-request-in-the-portal"></a>A kérelem elküldése a portálon

Miután tesztelte az alkalmazás-integrációt az Azure AD-vel, küldje el a hozzáférési kérelmét az [Application Network portálon](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Ha rendelkezik Office 365-fiókkal, akkor jelentkezzen be a portálra. Ha nem, használja a Microsoft-fiókt, például az Outlookot vagy a Hotmailt a bejelentkezéshez.

Ha a bejelentkezés után a következő oldal jelenik meg, lépjen kapcsolatba az [Azure ad SSO integrációs csapatával](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Adja meg a kérelem elküldéséhez használni kívánt e-mail-fiókot. Az Azure AD csapata felveszi a fiókot a Microsoft Application Network portálon.

![Hozzáférési kérelemre vonatkozó üzenet a SharePoint-portálon](./media/howto-app-gallery-listing/errorimage.png)

A fiók hozzáadása után bejelentkezhet a Microsoft Application Network portálra.

Ha a bejelentkezés után a következő oldal jelenik meg, adjon meg egy üzleti indoklást a szövegmezőben való hozzáféréshez. Ezután válassza a **hozzáférés kérése**lehetőséget.

  ![Üzleti indoklási mező a SharePoint-portálon](./media/howto-app-gallery-listing/accessrequest.png)

Csapatunk áttekinti a részleteket, és ennek megfelelően hozzáférést biztosít Önnek. A kérelem jóváhagyását követően bejelentkezhet a portálra, és elküldheti a kérést, ha a kezdőlapon a **küldési kérelem (ISV)** csempét választja.

![A beküldési kérelem (ISV) csempéje a kezdőlapon](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Ha bármilyen probléma merül fel a hozzáféréssel kapcsolatban, forduljon az [Azure ad SSO Integration csapatához](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>Egyszeri bejelentkezés implementálása az összevonási protokoll használatával

Egy alkalmazás az Azure AD-katalógusban való listázásához először meg kell valósítania az Azure AD által támogatott alábbi összevonási protokollok egyikét. Emellett el kell fogadnia az Azure AD Application Gallery használati feltételeit is. Olvassa el [ezen a webhelyen](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)az Azure ad Application Gallery használati feltételeit.

- **OpenID Connect**: az alkalmazás Azure ad-val való integrálásához az Open ID Connect protokoll használatával kövesse a [fejlesztők utasításait](v1-authentication-scenarios.md).

    ![OpenID Connect-alkalmazás listázása a katalógusban](./media/howto-app-gallery-listing/openid.png)

    * Ha az OpenID Connect használatával szeretné felvenni az alkalmazást a katalógusban szereplő listára, válassza az **OpenID connect & OAuth 2,0** lehetőséget a képen látható módon.
    * Ha bármilyen probléma merül fel a hozzáféréssel kapcsolatban, forduljon az [Azure ad SSO Integration csapatához](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **Saml 2,0** vagy **ws-fed**: Ha az alkalmazás támogatja az SAML 2,0-et, akkor közvetlenül is integrálhatja azt egy Azure ad-Bérlővel az [egyéni alkalmazások hozzáadásához szükséges utasítások](../active-directory-saas-custom-apps.md)követésével.

  ![SAML 2,0 vagy WS-fed alkalmazás listázása a katalógusban](./media/howto-app-gallery-listing/saml.png)

  * Ha az **saml 2,0** vagy a **ws-fed**használatával szeretné felvenni az alkalmazást a katalógusba, válassza az **SAML 2.0/ws-fed** lehetőséget, amint az látható.
  * Ha bármilyen probléma merül fel a hozzáféréssel kapcsolatban, forduljon az [Azure ad SSO Integration csapatához](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>Egyszeri bejelentkezés implementálása a jelszó-SSO használatával

Hozzon létre egy webalkalmazást, amely tartalmaz egy HTML-bejelentkezési oldalt a [jelszó alapú egyszeri bejelentkezés](../manage-apps/what-is-single-sign-on.md)konfigurálásához. A jelszó-alapú egyszeri bejelentkezés (más néven a jelszó-tároló) lehetővé teszi a felhasználók hozzáférésének és jelszavának kezelését az identitás-összevonást nem támogató webalkalmazásokhoz. Olyan helyzetekben is hasznos, amikor több felhasználónak egyetlen fiókot kell megosztania, például a szervezete közösségi Media app-fiókjaival.

![Jelszó-SSO-alkalmazás listázása a katalógusban](./media/howto-app-gallery-listing/passwordsso.png)

* Ha hozzá szeretné adni az alkalmazást a katalógusban a jelszó SSO használatával, válassza a **jelszó egyszeri bejelentkezés** lehetőséget.
* Ha bármilyen probléma merül fel a hozzáféréssel kapcsolatban, forduljon az [Azure ad SSO Integration csapatához](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>A felhasználó kiépítési kérelme

A felhasználó kiépítés igényléséhez kövesse az alábbi képen látható eljárást.

   ![A felhasználó kiépítési kérelme](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Meglévő lista frissítése vagy eltávolítása

Egy meglévő alkalmazás Azure AD-katalógusban való frissítéséhez vagy eltávolításához először el kell küldenie a kérést az [Application Network portálon](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Ha rendelkezik Office 365-fiókkal, akkor jelentkezzen be a portálra. Ha nem, használja a Microsoft-fiókt, például az Outlookot vagy a Hotmailt a bejelentkezéshez.

- Válassza ki a megfelelő beállítást, ahogy az az alábbi képen is látható.

    ![SAML-alkalmazás listázása a katalógusban](./media/howto-app-gallery-listing/updateorremove.png)

    * Egy meglévő alkalmazás frissítéséhez válassza ki a megfelelő beállítást a követelménynek megfelelően.
    * Ha el szeretne távolítani egy meglévő alkalmazást az Azure AD-alkalmazás- **katalógusból, válassza az alkalmazás listájának eltávolítása lehetőséget a gyűjteményből**.
    * Ha bármilyen probléma merül fel a hozzáféréssel kapcsolatban, forduljon az [Azure ad SSO Integration csapatához](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Ügyfelek által küldött kérések listázása

Az ügyfelek az alkalmazásra **vonatkozó kérelmeket az ügyfelek által** > **új kérelem beküldése**lehetőség kiválasztásával kérhetik az alkalmazások listázására.

![Az ügyfél által kért alkalmazások csempe megjelenítése](./media/howto-app-gallery-listing/customer-submit-request.png)

Az ügyfél által kért alkalmazások folyamata.

![Az ügyfél által kért alkalmazások folyamatát jeleníti meg](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>Ütemtervek

Az SAML 2,0 vagy WS-fed alkalmazás a katalógusban való listázásának ütemterve 7 – 10 munkanap.

  ![Az SAML-alkalmazások katalógusban való listázásának ütemterve](./media/howto-app-gallery-listing/timeline.png)

A katalógusban található OpenID Connect-alkalmazás listázási folyamatának ütemterve 2 – 5 munkanap.

  ![Az OpenID Connect alkalmazás listázása a katalógusban](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Azok következményeinek

Minden eszkaláció esetében küldjön e-mailt az [Azure ad SSO integrációs csapatának](mailto:SaaSApplicationIntegrations@service.microsoft.com) SaaSApplicationIntegrations@service.microsoft.com címen, és a lehető leghamarabb válaszoljon.
