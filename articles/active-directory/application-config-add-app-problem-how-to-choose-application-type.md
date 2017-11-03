---
title: "Alkalmazás típust használja, ha egy alkalmazás hozzáadása kiválasztása |} Microsoft Docs"
description: "A támogatott ismerteti az alkalmazások integrálva az Azure AD és a kapcsolódó konfigurációs beállítások"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: e0d41d1933531c2c633613bcbc1bbcbf075d6a69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-choose-which-application-type-to-use-when-adding-an-application"></a>Alkalmazás típust használja, ha egy alkalmazás hozzáadása kiválasztása

Ez a cikk segítenek megérteni a négy fő típusú alkalmazások integrálható az Azure AD:

* Támogatott források és műveletek szerint
* Miért akkor érdemes választania, melyik alkalmazás
* Ezen alkalmazás core tulajdonságai, például a felhasználók konfigurálásával **kiépített**, vagy mi **egyszeri bejelentkezés** technológiát használatára.

## <a name="supported-application-types-in-azure-ad"></a>Az Azure ad-ben támogatott alkalmazástípusok

Az Azure AD támogatja négy fő típusokat adhat hozzá a **Hozzáadás** szolgáltatás alatt található **vállalati alkalmazások**. Ezek a következők:

-   **Azure AD-gyűjtemény alkalmazások** –, amelyet az egyszeri bejelentkezés az Azure ad-vel előre integrált alkalmazás.

-   **Alkalmazás Proxy alkalmazások** – szeretne biztosítani a biztonságos egyszeri bejelentkezést a külsőleg a helyszíni környezetben futó alkalmazáshoz.

-   **Egyéni által fejlesztett alkalmazások** – az alkalmazás, amely a szervezet által platformon az Azure AD alkalmazás fejlesztési kialakításához, de, amely nem még létezik.

-   **Nem-gyűjtemény alkalmazások** – kapcsolja a saját alkalmazásai! Bármely kívánt webes hivatkozást, vagy egy felhasználónevet és jelszót a mezőben, amely alkalmazás SAML vagy az OpenID Connect protokollt támogatja, vagy az egyszeri bejelentkezés az Azure ad-vel integrálni kívánt SCIM támogatja.

## <a name="features-and-capabilities-supported-by-all-the-above-application-types"></a>Funkciók és képességek az összes fenti alkalmazástípusok által támogatott

Az alábbi szolgáltatások az Azure AD által a fenti 4 alkalmazás típusok támogatottak:

-   **Gyors üzembe helyezési** – induláshoz alkalmazással gyorsan következő [egyszerű üzembe helyezés lépései](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)

-   **Általános tulajdonságok felügyeleti** – lekérni egy [közvetlen mélyhivatkozás](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users) egy alkalmazást, [testre szabhatja a márkajelzési](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) egy alkalmazás vagy [az alkalmazás letiltására](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) az összes felhasználó számára.

-   **Felhasználók és csoportok kezelése** – [hozzárendelése](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) vagy [eltávolítása](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) felhasználókat és csoportokat egy alkalmazást, és ezek a felhasználók az adott alkalmazás szerepköröket rendelhet, és a csoportok fér hozzá a

-   **Önkiszolgáló alkalmazás-hozzáférés** – lehetővé teszi a felhasználók kéréséhez [önkiszolgáló alkalmazás-hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) az alkalmazás hozzáférési panelek vagy hozzáadásával egy alkalmazás közvetlenül az alkalmazáshoz való vagy [önkiszolgáló engedélyezett csoporthoz való csatlakozásra](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), opcionálisan menet üzleti jóváhagyásra van szükség

-   **Bejelentkezési naplók** – lásd: [minden a bejelentkezéseket egy alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins), vagy az alkalmazások

-   **A naplók** – lásd: [naplók kapcsolatos módosítások az alkalmazás részletes](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), vagy az alkalmazások

-   **Feltételes és kockázati-alapú hozzáférés** – hatékony beállított [feltétel-alapú hozzáférési szabályok](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) , amely akkor lépnek érvénybe, amikor a felhasználók próbálnak bejelentkezni az adott alkalmazást

-   **Engedélyek megtekintése** – bármely megtekintése a [OAuth2 engedélyek](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) hozzáfér egy alkalmazás helyezendő ugyanahhoz a könyvtárban

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Egyszeri bejelentkezés és a létesítési módot támogat. az adott alkalmazást típusok

Az alábbi táblázat ismerteti a különböző egyszeri bejelentkezést és a fenti alkalmazástípusok által támogatott üzembe helyezési mód. Ez a táblázat segítségével segítenek megérteni, melyik alkalmazás, akkor hozzon létre egy adott cél támogatásához.

  ![Alkalmazás típusú tábla](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Egyszeri bejelentkezés mód kiválasztása

A támogatott **egyszeri bejelentkezés** módjainak Azure AD-alkalmazások az alábbiak.

-   **Az Azure AD az egyszeri bejelentkezés le van tiltva** – válassza ki az Azure AD az egyszeri bejelentkezés le van tiltva **egyszeri bejelentkezés mód** Ha még nem állnak készen áll az alkalmazás integrálható az egyszeri bejelentkezés az Azure ad-vel, vagy egyszerűen csak tesztelés kimenő

-   **Bejelentkezés kapcsolódó** – válassza ki a [társított bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **egyszeri bejelentkezés mód** Ha valamelyik alkalmazásnál, amely a már meglévő egyszeri bejelentkezési megoldással már csatlakoztatva van, vagy ha szeretné a felhasználóknak egy egyszerű hivatkozás közzététele a [alkalmazás hozzáférési Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) vagy [Office 365 alkalmazásindító](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

-   **Jelszó alapú bejelentkezés** – válassza ki a [jelszóalapú bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **egyszeri bejelentkezés mód** Ha az alkalmazás Renderelés egy HTML-felhasználónév és jelszó mezőt, és szeretné tárolni, hogy felhasználónevet és jelszót biztonságos helyen kell újból az alkalmazást később

-   **SAML-alapú bejelentkezés** – válassza ki a [SAML-alapú bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) egyszeri bejelentkezést a módot, ha az alkalmazás támogatja az SAML- vagy OpenID Connect protokoll segítségével a felhasználók adott alkalmazás szerepköreihez a SAML-jogcímek meghatározott szabályok alapján tudja kívánt, vagy *

   >[!NOTE]
   >Ez a beállítás nem érhető el, ha a proxy van konfigurálva, az alkalmazás.
   >
   >

-   **Fejléc-alapú bejelentkezés** – válassza ezt a [fejléc-alapú bejelentkezés](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) egyszeri bejelentkezés módja Ha valamely alkalmazás használatával, amely támogatja a HTTP-fejléc PingAccess alapján hitelesítés egyszeri bejelentkezést a végrehajtani kívánt 

   >[!NOTE]
   >Ez a beállítás csak érhető el, ha a proxy- és PingAccess úgy van konfigurálva, az alkalmazás.
   >
   >

-   **Integrált Windows-hitelesítés** – válassza ki a [integrált Windows-hitelesítés](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) egyszeri bejelentkezést a elvégezni kívánt helyszíni WIA alkalmazás kitettségének üzemmódot egyszeri bejelentkezés 

   >[!NOTE]
   >Ez a beállítás csak érhető el, ha a proxy van konfigurálva, az alkalmazás.
   >
   >

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Egyszeri bejelentkezés módjainak egyéni által fejlesztett alkalmazások

Egyéni használatával fejlesztett alkalmazások rendelkeznek a [egyéni által fejlesztett alkalmazás](#_Custom-Developed_Applications) élmény is további egyszeri bejelentkezés módot támogat fent nem említett. Ezek a következők:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) bejelentkezés alapján

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) bejelentkezés alapján

-   [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) bejelentkezés alapján

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) bejelentkezés alapján

Olvassa el a [Azure Active Directory fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) további egyéni által fejlesztett alkalmazás, amely támogatja az egyszeri bejelentkezés módokban létrehozásával kapcsolatban.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Egy alkalmazás egyszeri bejelentkezés mód beállítása

Az alkalmazás beállítására **egyszeri bejelentkezés** mód, kövesse az alábbi utasításokat:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki az alkalmazást, amelyekhez az egyszeri bejelentkezés konfigurálása.

7.  Ha az alkalmazás betölt, kattintson **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

## <a name="how-to-choose-a-provisioning-mode"></a>A létesítés kiválasztása

-   **Manuális létesítési** – válassza ki a [manuális](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) kiépítési üzemmódban, ha van meglévő fiókokat, vagy szeretné ezt az Azure AD-en kívüli alkalmazáshoz tartozó fiókok kezelése.

-   **Automatikus kiépítés** – válassza ki a [automatikus](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **létesítés** Ha később engedélyezni kívánja automatikus API-alapú üzembe helyezés és/vagy az alkalmazás felhasználói fiókokat megszüntetést 

   >[!NOTE]
   >Ez a beállítás csak a alkalmazások belül érhető el a **kiemelt** kategóriáját a [az Azure AD Application Gallery](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal#the-new-and-improved-application-gallery).
   >
   >

-   **SCIM-alapú automatikus üzembe helyezés** – használható [SCIM-alapú automatikus üzembe helyezés](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) Ha az alkalmazás támogatja-e a SCIM protokoll, az Azure ad integrált felhasználókat és csoportokat, amelyek bármely alkalmazás módosítások automatikusan kibocsátott módosításai észlelése 

   >[!NOTE]
   >Ez a beállítás nem szerepel egy üzembe helyezési mód, de minden olyan alkalmazásnál, az Azure ad-vel integrált alapértelmezés szerint engedélyezve van.
   >
   >

## <a name="how-to-set-an-applications-provisioning-mode"></a>Az alkalmazás beállítása a kiépítési üzemmódban

Az alkalmazás beállítására **kiépítés** mód, kövesse az alábbi utasításokat:

Az alkalmazás beállítására **egyszeri bejelentkezés** mód, kövesse az alábbi utasításokat:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki az alkalmazást, amelynek konfigurálása kiosztás keresi.

7.  Ha az alkalmazás betölt, kattintson **kiépítési** az alkalmazás bal oldali navigációs menüjében.

## <a name="next-steps"></a>Következő lépések
[Alkalmazások kezelése az Azure Active Directoryban](active-directory-enable-sso-scenario.md)
