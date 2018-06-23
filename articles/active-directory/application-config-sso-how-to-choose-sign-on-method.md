---
title: Annak megállapítása, milyen egyszeri bejelentkezést használandó módszer |} Microsoft Docs
description: Az egyszeri bejelentkezés módot támogat az Azure AD megértéséhez, és válassza ki az alkalmazás érdekli kiválasztásáról módjáról.
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
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 79fe30fbe0baed9d62e6bc3328e754958516ef70
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335407"
---
# <a name="how-to-determine-what-single-sign-on-method-to-use"></a>Annak megállapítása, milyen egyszeri bejelentkezést használandó módszer

Ez a cikk segítenek megérteni az egyszeri bejelentkezés módot támogat az Azure AD és az alkalmazás érdekli kiválasztásáról válasszon.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Egyszeri bejelentkezés és a létesítési módot támogat. az adott alkalmazást típusok

A következő táblázat ismerteti a egyszeri bejelentkezés és üzembe helyezési mód az előző alkalmazástípusok által támogatott. Ez a táblázat segítségével segítenek megérteni, melyik alkalmazás, akkor hozzon létre egy adott cél támogatásához.

  ![Ázsia és a csendes típusai tábla](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Egyszeri bejelentkezés mód kiválasztása

Az alábbiakban a támogatott **egyszeri bejelentkezés** Azure AD-alkalmazások mód választható.

-   **Az Azure AD az egyszeri bejelentkezés le van tiltva** – válassza ki az Azure AD az egyszeri bejelentkezés le van tiltva **egyszeri bejelentkezés mód** Ha még nem állnak készen áll az alkalmazás integrálható az egyszeri bejelentkezés az Azure ad-vel, vagy egyszerűen csak tesztelés kimenő

-   **Bejelentkezés kapcsolódó** – válassza ki a [társított bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **egyszeri bejelentkezés mód** Ha valamelyik alkalmazásnál, amely a már meglévő egyszeri bejelentkezési megoldással már csatlakoztatva van, vagy ha szeretné a felhasználóknak egy egyszerű hivatkozás közzététele a [alkalmazás hozzáférési Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) vagy [Office 365 alkalmazásindító](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

-   **Jelszó alapú bejelentkezés** – válassza ki a [jelszóalapú bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **egyszeri bejelentkezés mód** Ha az alkalmazás Renderelés egy HTML-felhasználónév és jelszó mezőt, és szeretné tárolni, hogy a felhasználónév és a jelszó biztonságosan később az alkalmazás bejegyzéseit meg kell ismételni

-   **SAML-alapú bejelentkezés** – válassza ki a [SAML-alapú bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) mód, ha az alkalmazás támogatja az SAML-alapú vagy az OpenID Connect protokollok, vagy azt szeretné, hogy segítségével a felhasználók adott alkalmazás szerepköreihez szabályok alapján egyszeri bejelentkezés a SAML-jogcímek meghatározott *(** Megjegyzés:** Ez a beállítás nem érhető el, ha a proxy van konfigurálva, az alkalmazás) *

-   **Fejléc-alapú bejelentkezés** – válassza ezt a [fejléc-alapú bejelentkezés](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) egyszeri bejelentkezés mód Ha valamelyik alkalmazásnál, amely támogatja a HTTP-fejléc-alapú hitelesítés egyszeri bejelentkezést a végrehajtani kívánt PingAccess használatával *(** Megjegyzés:** ezt a beállítást csak érhető el, ha a proxy- és PingAccess vannak beállítva, az alkalmazás) *

-   **Integrált Windows-hitelesítés** – válassza ki a [integrált Windows-hitelesítés](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) egyszeri bejelentkezést a elvégezni kívánt helyszíni WIA alkalmazás kitettségének üzemmódot egyszeri bejelentkezés *(** Megjegyzés:** ezt a beállítást csak érhető el, ha a proxy van konfigurálva, az alkalmazás) *

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Egyszeri bejelentkezés módjainak egyéni által fejlesztett alkalmazások

Egyéni használatával fejlesztett alkalmazások rendelkeznek a [egyéni által fejlesztett alkalmazás](#_Custom-Developed_Applications) élmény is további egyszeri bejelentkezés módot támogat korábban nem szerepel a listában, többek között:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) bejelentkezés alapján

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) bejelentkezés alapján

-   [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) bejelentkezés alapján

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) bejelentkezés alapján

Olvassa el a [Azure Active Directory fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) további egyéni által fejlesztett alkalmazás, amely támogatja az egyszeri bejelentkezés módokban létrehozásával kapcsolatban.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Egy alkalmazás egyszeri bejelentkezés mód beállítása

Az alkalmazás beállítására **egyszeri bejelentkezés** mód, kövesse az alábbi utasításokat:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes Alkalmazások.**

6.  Válassza ki az egyszeri bejelentkezés konfigurálni kívánt alkalmazást

7.  Ha az alkalmazás betölt, kattintson **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

## <a name="next-steps"></a>További lépések
[Adja meg az egyszeri bejelentkezés az alkalmazásokba a Proxy](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

