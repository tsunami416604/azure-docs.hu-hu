---
title: Annak megállapítása, hogy milyen egyszeri bejelentkezéses módszert használja |} A Microsoft Docs
description: Az egyszeri bejelentkezési módot támogat az Azure AD ismertetése, és hogy miként választható ki, melyiket válassza ki az Önt érdeklő alkalmazáshoz.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 336aa4f671e6d86684664fa5e5d15a03a4beff23
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366289"
---
# <a name="how-to-determine-what-single-sign-on-method-to-use"></a>Annak megállapítása, hogy milyen egyszeri bejelentkezéses módszert szeretné használni

Ez a cikk segít megérteni az egyszeri bejelentkezési módot támogat az Azure AD, és hogy miként választható ki, melyiket válassza ki az Önt érdeklő alkalmazáshoz.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Egyszeri bejelentkezés és a üzembe helyezési módot támogat bizonyos alkalmazástípusok

A következő táblázat ismerteti az egyszeri bejelentkezéshez és az üzembe helyezési mód az előző alkalmazástípusok által támogatott. Ez a táblázat segítségével melyik támogatásához egy bizonyos cél hozzá kell application megértését.

  ![AP típusok táblázata](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Egy egyszeri bejelentkezési mód kiválasztása

Az alábbiakban a támogatott **egyszeri bejelentkezési** módhoz az Azure AD-alkalmazások.

-   **Az Azure AD egyszeri bejelentkezés le van tiltva** – válassza ki az Azure AD egyszeri bejelentkezés le van tiltva **egyszeri bejelentkezési mód** Ha még nem állnak készen áll az alkalmazás integrálása az egyszeri bejelentkezés az Azure ad-vel, vagy egyszerűen csak a tesztelt próbálhatja ki

-   **Csatolt bejelentkezés** – válassza ki a [csatolt bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **egyszeri bejelentkezési mód** Ha egy alkalmazást, amely az egyszeri bejelentkezés megoldás már csatlakoztatva van, vagy ha csak át szeretné a felhasználók számára az egyszerű hivatkozás közzététele saját [alkalmazás-hozzáférési Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) vagy [Office 365 alkalmazásindítójában](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

-   **Jelszóalapú bejelentkezés** – válassza ki a [jelszóalapú bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **egyszeri bejelentkezési mód** , ha az alkalmazás egy HTML-felhasználónév és jelszó mező jelenik meg, és szeretné tárolni, hogy a felhasználónév és a jelszó biztonságosan megismétlését, később az alkalmazáshoz

-   **SAML-alapú bejelentkezés** – válassza ki a [SAML-alapú bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) módba, ha az alkalmazás támogatja az SAML- vagy OpenID Connect protokollok, vagy hogy képes legyen leképezni a felhasználók az adott alkalmazás-szerepkörök szabályok alapján szeretne egyszeri bejelentkezés Az SAML-jogcímek az meghatározott *(** Megjegyzés:** Ez a beállítás nem érhető el, ha a proxy van konfigurálva, az alkalmazás) *

-   **Fejlécalapú bejelentkezés** – válassza ezt a [fejlécalapú bejelentkezés](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) egyszeri bejelentkezési mód, ha egy alkalmazást, amely támogatja a HTTP-fejléc-alapú hitelesítést, amely egyszeri bejelentkezést a végrehajtani kívánt PingAccess segítségével *(** Megjegyzés:** ezt a beállítást csak érhető el, ha a proxy és a PingAccess vannak konfigurálva, az alkalmazás) *

-   **Integrált Windows-hitelesítés** – válassza ki a [integrált Windows-hitelesítés](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) módra, amikor szeretné végrehajtani az egyszeri bejelentkezést a helyszíni WIA alkalmazás által egyszeri bejelentkezés *(** Megjegyzés:** ezt a beállítást csak érhető el, ha a proxy van konfigurálva, az alkalmazás) *

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Egyszeri bejelentkezési módok egyénileg fejlesztett alkalmazásokhoz

Egyéni munka eredményeként kifejlesztett alkalmazások rendelkeznek a [egyénileg fejlesztett alkalmazásba](#_Custom-Developed_Applications) élmény is támogatja a további egyszeri bejelentkezési módok korábban fel nem vett, többek között:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) -alapú bejelentkezésre

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) -alapú bejelentkezésre

-   [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) -alapú bejelentkezésre

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) -alapú bejelentkezésre

Olvassa el a [Azure Active Directory fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) létrehozása egy egyénileg fejlesztett alkalmazásba, amely támogatja az egyszeri bejelentkezéses módokban tájékozódhat.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Egy alkalmazás egyszeri bejelentkezési mód beállítása

Egy alkalmazás beállítása **egyszeri bejelentkezési** módot, az alábbi lépéseket követve:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazást szeretné az egyszeri bejelentkezés konfigurálása

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

