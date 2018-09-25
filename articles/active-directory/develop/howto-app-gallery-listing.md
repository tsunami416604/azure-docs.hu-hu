---
title: Az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában |} A Microsoft Docs
description: Ismerje meg, amely támogatja az egyszeri bejelentkezés az Azure Active Directory alkalmazásgyűjtemény alkalmazás listázása
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2018
ms.author: celested
ms.reviewer: elisol, bryanla
ms.custom: aaddev
ms.openlocfilehash: 3d6692808bad2010779323cecc42778def32a38c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980270"
---
# <a name="how-to-list-your-application-in-the-azure-active-directory-application-gallery"></a>Útmutató: az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában

## <a name="what-is-the-azure-ad-application-gallery"></a>Mi az az Azure AD alkalmazáskatalógusában?

- Ügyfeleink a lehető legjobb egyszeri bejelentkezéses élményt találja.
- Az alkalmazás konfigurációs egyszerűen, minimális számú.
- Gyors keresés az alkalmazás megkeresi a katalógusban.
- Ingyenes, alapszintű, és az Azure AD Premium-ügyfelek ezt az integrációt is használja.
- Ügyfelek kaphat részletes konfigurációs oktatóanyagot.
- Ügyfeleink, akik SCIM használhatja ugyanahhoz az alkalmazáshoz tartozó kiépítés.

## <a name="prerequisites"></a>Előfeltételek

- Az összevont alkalmazások (Open ID és az SAML/WS-Fed) az alkalmazás támogatnia kell az Azure AD katalógusából származó címtárban való megjelenésről a SaaS-modell. A vállalati katalógus alkalmazások támogatnia kell a több ügyfél-konfiguráció, és nem bármely adott ügyfél.

- Az Open ID Connect, az alkalmazás legyen, több-bérlős és [Azure ad-ben hozzájárulási keretrendszer](quickstart-v1-integrate-apps-with-azure-ad.md#overview-of-the-consent-framework) megfelelően valósuljon meg az alkalmazáshoz. A felhasználó a bejelentkezési kérelem küldhet egy közös végpontot, hogy minden ügyfél biztosíthat hozzájárulás az alkalmazáshoz. Szabályozhatja a felhasználói hozzáférést a bérlő Azonosítóját és a felhasználó egyszerű Felhasználónevét a jogkivonat érkezett alapján.

- Az SAML 2.0 vagy WS-Fed az alkalmazás rendelkeznie kell a funkció a SAML/WS-Fed egyszeri bejelentkezési integrálni SP vagy Identitásszolgáltató módban. Ellenőrizze, hogy ez megfelelően működik-e a kérés elküldése előtt.

- A jelszavas egyszeri Bejelentkezést győződjön meg arról, hogy az alkalmazás támogatja-e a űrlap-hitelesítést, így a jelszótárolást egyszeri bejelentkezés munkahelyi megszerezni a várt módon teheti meg.

- Az automatikus felhasználó-átadási kérelem alkalmazás szerepelnie kell a katalógusban az egyszeri bejelentkezési szolgáltatás engedélyezve van a fent leírt összevonási protokollt használja. Egyszeri Bejelentkezéssel és Felhasználókiépítés együtt a portálon, ha az még nem szereplő igényelhet.

## <a name="submit-the-request-in-the-portal"></a>Küldje el a kérelmet a portálon

Tesztelését, hogy működik-e az alkalmazás-integráció az Azure ad-vel, követően elküldéséhez a hozzáféréssel kapcsolatos kérelmét az [Application hálózati portál](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Ha rendelkezik Office 365-fiókja, amelyek segítségével jelentkezzen be a portálra. Ha nem, jelentkezzen be Microsoft-fiókjával (például az Outlook vagy Hotmail) használatával.

Ha a bejelentkezés után megjelenik a következő lap, lépjen kapcsolatba a [Azure AD SSO integrációs csapat](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) , és adja meg az e-mail-fiók, amely a kérelem küldéséhez használni kívánt. Az Azure AD-csapat adja hozzá a az a fiókot a Microsoft alkalmazás hálózati portálján.

![Hozzáférési kérelem SharePoint portál](./media/howto-app-gallery-listing/errorimage.png)

Miután hozzáadta a fiókot, bejelentkezhet a Microsoft alkalmazás hálózati portálra.

És ha a bejelentkezés után megjelenik a következő lap, adja meg az üzleti indoklásának hozzáférnének a szövegmezőbe, és válassza **hozzáférés kérése**.

  ![Hozzáférési kérelem SharePoint portál](./media/howto-app-gallery-listing/accessrequest.png)

Csapatunk áttekinti a részletek és a megfelelő hozzáférést biztosít. Miután jóváhagyják a kérelmét, jelentkezzen be a portálra, és küldje el a kérelmet kattintva a **kérelem elküldése (ISV)** csempe formájában a kezdőlapon.

![A SharePoint portál kezdőlapján](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Ha rendelkezik hozzáféréssel kapcsolatos problémákat, lépjen kapcsolatba a [Azure AD SSO integrációs csapat](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-federation-protocol"></a>Az egyszeri bejelentkezés összevonási protokollt használó implementálása

Az Azure AD-alkalmazásgyűjtemény alkalmazás listázásához először megvalósítása Azure AD által támogatott következő összevonási protokoll, és fogadja el az Azure AD application Gallery feltételek és kikötések. Olvassa el a feltételeket és kikötéseket, az Azure AD alkalmazáskatalógusában a [Itt](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect**: az alkalmazás integrálása az Azure ad-ben, az Open ID Connect protokoll használatával, kövesse a [fejlesztők utasításokat](authentication-scenarios.md).

    ![A támadás idővonalának, a katalógusban a OpenID Connect alkalmazás listázása](./media/howto-app-gallery-listing/openid.png)

    * Ha szeretne hozzáadni a listához az alkalmazás használatával az OpenID Connect, válassza ki a katalógus **OpenID Connect és az OAuth 2.0** a fentiek szerint.
    * Ha rendelkezik hozzáféréssel kapcsolatos problémákat, lépjen kapcsolatba a [Azure AD SSO integrációs csapat](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

*   **SAML 2.0** vagy **WS-Fed**: Ha az alkalmazás támogatja az SAML 2.0, integrálva közvetlenül az Azure AD-bérlő használatával a [utasításokat egy egyéni alkalmazás hozzáadása](../active-directory-saas-custom-apps.md).

    ![A támadás idővonalának, a katalógusban a SAML 2.0 vagy WS-Fed alkalmazás listázása](./media/howto-app-gallery-listing/saml.png)

    * Ha a katalógusban található az alkalmazás listához hozzáadni kívánt **SAML 2.0** vagy **WS-Fed**válassza **SAMl 2.0 vagy WS-Fed** a fentiek szerint.
    * Ha rendelkezik hozzáféréssel kapcsolatos problémákat, lépjen kapcsolatba a [Azure AD SSO integrációs csapat](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-password-sso"></a>Jelszavas egyszeri Bejelentkezést használó egyszeri bejelentkezés megvalósítása

Hozzon létre egy webalkalmazást, amely rendelkezik egy HTML-bejelentkezési lap konfigurálása [jelszóalapú egyszeri bejelentkezés](../manage-apps/what-is-single-sign-on.md). Jelszavas egyszeri Bejelentkezést, jelszó vaulting, engedélyezi a kezelheti a felhasználói hozzáférés és a jelszavakat a webes alkalmazások, amelyek nem támogatják az identitás-összevonást is hivatkoznak. Emellett akkor is, amelyben több felhasználónak kell megosztani egyetlen fiókban, mint például a szervezet közösségi alkalmazás fiókok forgatókönyvek esetén hasznos.

![A támadás idővonalának, a katalógusban a jelszó SSO-alkalmazás listázása](./media/howto-app-gallery-listing/passwordsso.png)

* Ha szeretne hozzáadni a listához az alkalmazás használatával, jelszó SSO, válassza ki a katalógus **jelszó SSO** a fentiek szerint.
* Ha rendelkezik hozzáféréssel kapcsolatos problémákat, lépjen kapcsolatba a [Azure AD SSO integrációs csapat](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="updateremove-existing-listing"></a>Meglévő listaelem frissítése és törlése

Frissítse vagy távolítsa el a meglévő alkalmazások az Azure AD-alkalmazásgyűjtemény, szüksége lesz a kérelem elküldéséhez a [Application hálózati portál](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Ha rendelkezik Office 365-fiókja, amelyek segítségével jelentkezzen be a portálra. Ha nem, jelentkezzen be Microsoft-fiókjával (például az Outlook vagy Hotmail) használatával.

- Válassza ki a megfelelő beállítást, az alábbi képen látható módon:

    ![Idősor listázása a katalógusban a saml-kérelem](./media/howto-app-gallery-listing/updateorremove.png)

    * Ha egy meglévő alkalmazás frissítésére, jelölje be **frissítse a meglévő alkalmazás listaelem**.
    * Ha el kívánja távolítani a meglévő alkalmazások az Azure AD katalógusából, válassza ki a **távolítsa el a meglévő alkalmazás listaelem**.
    * Ha rendelkezik hozzáféréssel kapcsolatos problémákat, lépjen kapcsolatba a [Azure AD SSO integrációs csapat](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="timelines"></a>Idősor

A folyamat a tőzsdei a SAML 2.0 vagy WS-Fed alkalmazás a katalógus ütemtervét 7 – 10 munkanapon.

   ![Idősor listázása a katalógusban a saml-kérelem](./media/howto-app-gallery-listing/timeline.png)

A folyamat a tőzsdei a katalógusban OpenID Connect alkalmazás ütemtervét 2 – 5 munkanapon.

   ![Idősor listázása a katalógusban a saml-kérelem](./media/howto-app-gallery-listing/timeline2.png)

A folyamat a tőzsdei az alkalmazást a katalógusban a felhasználókiépítés támogatási ütemtervét 40-45 munkanapon belül.

   ![Idősor listázása a katalógusban a saml-kérelem](./media/howto-app-gallery-listing/provisioningtimeline.png)

## <a name="escalations"></a>Eszkalálást

Az összes eszkalálást e-mailben a [Azure AD SSO integrációs csapat](mailto:SaaSApplicationIntegrations@service.microsoft.com) amely SaaSApplicationIntegrations@service.microsoft.com és a lehető legrövidebb időn belül válaszolunk.