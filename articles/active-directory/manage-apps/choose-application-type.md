---
title: Alkalmazás hozzáadásakor használandó alkalmazás típusának kiválasztása | Microsoft Docs
description: Megismerheti az Azure AD-vel és a hozzájuk kapcsolódó konfigurációs lehetőségekkel integrálható alkalmazások támogatott típusait
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: d5bd2397c345a4f670bde343f751cd69f825ecb9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056067"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Az alkalmazás típusának kiválasztása Azure Active Directory-alkalmazás hozzáadásakor

Ismerje meg az Azure Active Directory (Azure AD) által felvehető négyféle alkalmazások típusát. Ha Azure Active Directory-alkalmazásban ad hozzá alkalmazást, a rendszer kérni fogja, hogy válasszon egyet a négy alkalmazás típus közül.

## <a name="what-are-the-types-of-applications"></a>Milyen típusú alkalmazások léteznek?

Az Azure AD négy fő alkalmazáshiba-típust támogat, amelyeket a **vállalati alkalmazások**területen található **Add** szolgáltatás használatával adhat hozzá. Ezek a következők:

- **Azure ad Gallery-alkalmazások** – az Azure ad-vel való egyszeri bejelentkezéshez előre integrált alkalmazás.

- **Alkalmazásproxy-alkalmazások** – egy olyan alkalmazás, amely a helyszíni környezetben fut, és biztonságos egyszeri bejelentkezést kíván biztosítani a külsőleg.

- **Egyéni** fejlesztésű alkalmazások – egy alkalmazás, amelyet a szervezet az Azure ad Application Development platformon szeretne fejleszteni, de ez még nem létezik.

- **Galérián kívüli alkalmazások** – saját alkalmazások használata Bármely olyan Webhivatkozás, amelyet szeretne, vagy bármely olyan alkalmazás, amely felhasználónevet és jelszót jelenít meg, támogatja az SAML vagy az OpenID Connect protokollokat, illetve támogatja a SCIM, amelyeket az Azure AD-vel való egyszeri bejelentkezéshez szeretne integrálni.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>Az alkalmazások típusai által támogatott funkciók és képességek

Az Azure AD-ben az előző négy alkalmazás-típus bármelyike támogatja a következő funkciókat:

- Gyors útmutató **– az** [egyszerű üzembe helyezési lépések](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started) követésével gyorsan elsajátíthatja az alkalmazásokat

- **Általános tulajdonságok kezelése** – egy alkalmazásra [mutató közvetlen mély hivatkozás](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) beszerzése, [az alkalmazások arculatának testreszabása](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) vagy [az alkalmazás letiltása](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) az összes felhasználó számára.

- Felhasználók **és csoportok kezelése** – felhasználókat és csoportokat [rendelhet hozzá](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) vagy [távolíthat el](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) egy alkalmazáshoz, és opcionálisan hozzárendelheti azokat a konkrét alkalmazási szerepköröket, amelyekre ezek a felhasználók és csoportok hozzáférnek

- **Önkiszolgáló alkalmazás-hozzáférés** – lehetővé teheti a felhasználók számára, hogy alkalmazás-hozzáférési panelektől önkiszolgáló [alkalmazás-hozzáférést](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) kérjenek, akár közvetlenül, akár [egy önkiszolgáló engedélyezett csoporthoz csatlakozva egy alkalmazáshoz. ](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), opcionálisan megkövetelheti az üzleti jóváhagyást

- **Bejelentkezési naplók** – az alkalmazásba vagy az összes alkalmazásba [való bejelentkezések](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)megtekintése

- **Naplók** – tekintse meg az alkalmazások módosításaival vagy az összes alkalmazásával [kapcsolatos részletes naplókat](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs).

- **Feltételes és kockázatalapú hozzáférés** – olyan nagy teljesítményű, [feltételeken alapuló hozzáférési szabályokat](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) állíthat be, amelyek kényszerítve vannak, amikor a felhasználók egy adott alkalmazásba próbálnak bejelentkezni

- **Engedélyek nézet** – a [OAuth2 engedélyeinek](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) megtekintése az alkalmazások egyetlen helyről férhetnek hozzá a címtárhoz

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Adott alkalmazások típusai által támogatott egyszeri bejelentkezés és kiépítési módok

A következő táblázat ismerteti az előző alkalmazások különböző típusai által támogatott egyszeri bejelentkezést és kiépítési módokat. A táblázat segítségével megtudhatja, hogy melyik alkalmazást kell hozzáadnia egy adott cél támogatásához.

  ![Tábla Az egyes alkalmazások típusai által támogatott SSO-és üzembe helyezési módok](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Egyszeri bejelentkezési mód kiválasztása

Az alábbiakban az Azure AD-alkalmazások támogatott **egyszeri bejelentkezési** módjai láthatók.

- **Azure ad egyszeri bejelentkezés letiltva** – válassza az Azure ad egyszeri bejelentkezés letiltott **egyszeri bejelentkezés üzemmódot** , ha még nem áll készen az alkalmazás integrálására az Azure ad-vel való egyszeri bejelentkezéssel, vagy egyszerűen tesztelheti azt

- **Csatolt bejelentkezés** – ha olyan alkalmazással rendelkezik, amely már kapcsolódik egy meglévő egyszeri bejelentkezési megoldáshoz, akkor válassza a [kapcsolt bejelentkezési](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) **egyszeri bejelentkezési módot** , vagy ha csak egy egyszerű hivatkozást szeretne közzétenni a felhasználók számára [ Alkalmazás-hozzáférési panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) vagy [Office 365 Application Launcher](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

- **Jelszó-alapú bejelentkezés** – válassza ki a [jelszó-alapú bejelentkezési](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) **módot** , ha az alkalmazás HTML-felhasználónevet és jelszót jelenít meg, és a felhasználónevet és a jelszót biztonságosan szeretné lejátszani a következő helyre: alkalmazás később

- **SAML-alapú bejelentkezés** – válassza ki az [SAML-alapú bejelentkezési](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) módot, ha az alkalmazás támogatja az SAML-vagy OpenID Connect-protokollokat, vagy ha az SAML-ban meghatározott szabályok alapján szeretné leképezni a felhasználókat egy adott alkalmazás szerepköreire jogcímek

  >[!NOTE]
  >Ez a beállítás nem érhető el, ha az alkalmazásproxy konfigurálva van egy alkalmazáshoz.

- **Fejléc-alapú bejelentkezés** – válassza ezt a [fejléc-alapú bejelentkezési](application-proxy-configure-single-sign-on-with-ping-access.md) módot, ha olyan PingAccess használó alkalmazással rendelkezik, amely támogatja a HTTP-fejléc-alapú hitelesítést, és így szeretne egyszeri bejelentkezést végezni

  >[!NOTE]
  >Ez a beállítás csak akkor érhető el, ha az alkalmazásproxy és a PingAccess egy alkalmazáshoz van konfigurálva.

- **Integrált Windows-hitelesítés** – válassza az [integrált Windows-hitelesítés](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) egyszeri bejelentkezés üzemmódot, ha egy helyszíni WIA-alkalmazást kíván végrehajtani, amelyen egyszeri bejelentkezést szeretne végezni

  >[!NOTE]
  >Ez a beállítás csak akkor érhető el, ha az alkalmazásproxy konfigurálva van egy alkalmazáshoz.

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Egyszeri bejelentkezési módok egyéni fejlesztésű alkalmazásokhoz

Az egyéni fejlesztésű alkalmazásokkal kifejlesztett alkalmazások a korábban nem felsorolt további egyszeri bejelentkezési módokat is támogatják, többek között a következők:

- [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) -alapú bejelentkezés

- [OpenID Connect 1,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) -alapú bejelentkezés

- [WS-Federation 1,2-](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) alapú bejelentkezés

- [SAML 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) -alapú bejelentkezés

A [Azure Active Directory fejlesztői útmutatóból](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) megtudhatja, hogyan hozhat létre olyan egyéni alkalmazást, amely támogatja ezeket az egyszeri bejelentkezési módokat.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Alkalmazás egyszeri bejelentkezési módjának beállítása

Az alkalmazás egyszeri bejelentkezési módjának beállításához kövesse az alábbi utasításokat:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.
1. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.
1. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.
1. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.
1. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

1. Válassza ki azt az alkalmazást, amelyhez be szeretné állítani az egyszeri bejelentkezést.
1. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezés** elemre az alkalmazás bal oldali navigációs menüjében.

## <a name="how-to-choose-a-provisioning-mode"></a>Kiépítési mód kiválasztása

- **Manuális kiépítés** – válassza ki a [manuális](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) létesítési módot, ha már van fiókja, vagy az Azure ad-n kívül szeretné kezelni az alkalmazás fiókját.

- **Automatikus kiépítés** – válassza ki az [automatikus](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **kiépítési módot** , ha engedélyezni szeretné az automatikus API-alapú üzembe helyezést és/vagy a felhasználói fiókok ezen alkalmazáshoz való kivonását. 

- **Scim-alapú automatikus kiépítés** – [scim-alapú automatikus kiépítés](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) használata, ha az alkalmazás támogatja a scim protokollt a felhasználók és csoportok változásainak észlelésére, amelyeket a rendszer automatikusan kibocsát az összes integrált alkalmazás változásaihoz Az Azure AD-vel 

  >[!NOTE]
  >Ez a beállítás nem szerepel a megadott kiépítési módban, de alapértelmezés szerint engedélyezve van az Azure AD-vel integrált összes alkalmazás esetében.

## <a name="how-to-set-an-applications-provisioning-mode"></a>Alkalmazás létesítési módjának beállítása

Az alkalmazás **létesítési** módjának beállításához kövesse az alábbi utasításokat:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.
1. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.
1. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.
1. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.
1. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

1. Válassza ki azt az alkalmazást, amelyhez konfigurálni kívánja a létesítést.
1. Az alkalmazás betöltése után kattintson a **kiépítés** lehetőségre az alkalmazás bal oldali navigációs menüjében.

## <a name="next-steps"></a>További lépések

[Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
