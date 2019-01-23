---
title: Hogyan választható ki a használandó alkalmazás hozzáadásakor mely alkalmazástípust |} A Microsoft Docs
description: Megismerheti a támogatott típusok is integrálhatja az Azure AD-alkalmazások és azok kapcsolódó konfigurációs beállítások
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: barbkess
ms.openlocfilehash: 10f2b30addd874d39a6193c810ccbdf4ac1a06e6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54473316"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Az alkalmazástípus kiválasztása, amikor egy alkalmazás hozzáadása az Azure Active Directoryban
További információ a négy típusú alkalmazások Azure Active Directory (Azure AD) adhat hozzá. Ha egy alkalmazás az Azure Active Directoryban ad hozzá, a program felszólítja válasszon egyet a négy alkalmazástípus. 

## <a name="what-are-the-types-of-applications"></a>Mik azok az alkalmazások?

Az Azure AD támogatja négy fő alkalmazástípusok használatával adhat hozzá a **Hozzáadás** szolgáltatás alatt található **vállalati alkalmazások**. Ezek a következők:

-   **Az Azure AD-katalógusban alkalmazások** –, az egyszeri bejelentkezés az Azure ad-vel előre integrált alkalmazás.

-   **Application Proxy alkalmazásai** – szeretne biztosítani a biztonságos egyszeri bejelentkezés, a külsőleg a helyszíni környezetben futó alkalmazásokhoz.

-   **Saját fejlesztésű alkalmazásokat** – el alkalmazást, amely a szervezet által blokkolni kívánt fejlesztése az Azure AD-alkalmazás fejlesztési platform, amely nem még létezik.

-   **Katalógusban nem szereplő alkalmazások** – saját alkalmazások importálása! Összes webes hivatkozás szeretne, vagy egy felhasználónév és jelszó mezőt megjelenítő alkalmazás támogatja az SAML- vagy OpenID Connect protokollok, vagy az egyszeri bejelentkezés az Azure ad-vel integrálni kívánt SCIM támogatja.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>Funkciók és képességek az alkalmazástípusok által támogatott

Az alábbi szolgáltatások az Azure ad-ben szerint az előző négy alkalmazás típusok támogatottak:

-   **Gyors üzembe helyezési** – az alkalmazás gyors induláshoz a következő [egyszerű üzembe helyezés lépései](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)

-   **Általános tulajdonságok felügyeleti** – beolvasása egy [közvetlen mélyhivatkozás](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users) egy alkalmazást, [a márkajelzés testreszabása](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) egy alkalmazás vagy [tiltsa le az alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) az összes felhasználó számára.

-   **Felhasználók és csoportok kezelése** – [hozzárendelése](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) vagy [eltávolítása](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) felhasználókat és csoportokat egy alkalmazást, és ezek a felhasználók az adott alkalmazás szerepköröket rendelhet, és a csoportok rendelkezik hozzáféréssel

-   **Az önkiszolgáló alkalmazás-hozzáférés** – a felhasználókat, hogy a kérelem engedélyezéséhez [önkiszolgáló alkalmazás-hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) az alkalmazás hozzáférési Paneljükön vagy hozzáadásával az alkalmazás közvetlenül egy alkalmazás vagy [ Csatlakozás egy önkiszolgáló engedélyezett csoporthoz](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), igény szerint követhető üzleti jóváhagyásra van szükség

-   **Bejelentkezési naplók** – lásd: [összes a bejelentkezés egy alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins), vagy az alkalmazások

-   **Auditnaplók** – lásd: [auditnaplók alkalmazás módosításai kapcsolatos részletes](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), vagy az alkalmazások

-   **Feltételes és kockázat-alapú hozzáférés** – állítson be hatékony [feltétel-alapú hozzáférési szabályok](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) , amely kényszerítettek, amikor a felhasználók megpróbálnak bejelentkezni egy adott alkalmazást

-   **Engedélyek megtekintése** – bármelyik megtekintése a [OAuth2 engedélyek](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) alkalmazás hozzáfér a címtárban, egyetlen elhelyezése

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Egyszeri bejelentkezés és a üzembe helyezési módot támogat bizonyos alkalmazástípusok

A következő táblázat ismerteti az egyszeri bejelentkezéshez és az üzembe helyezési mód az előző alkalmazástípusok által támogatott. Ez a táblázat segítségével melyik támogatásához egy bizonyos cél hozzá kell application megértését.

  ![Alkalmazás-típusok táblázata](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Egy egyszeri bejelentkezési mód kiválasztása

Az alábbiakban a támogatott **egyszeri bejelentkezési** módhoz az Azure AD-alkalmazások.

-   **Az Azure AD egyszeri bejelentkezés le van tiltva** – válassza ki az Azure AD egyszeri bejelentkezés le van tiltva **egyszeri bejelentkezési mód** Ha még nem állnak készen áll az alkalmazás integrálása az egyszeri bejelentkezés az Azure ad-vel, vagy egyszerűen csak a tesztelt próbálhatja ki

-   **Csatolt bejelentkezés** – válassza ki a [csatolt bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **egyszeri bejelentkezési mód** Ha egy alkalmazást, amely az egyszeri bejelentkezés megoldás már csatlakoztatva van, vagy ha csak át szeretné a felhasználók számára az egyszerű hivatkozás közzététele saját [alkalmazás-hozzáférési Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) vagy [Office 365 alkalmazásindítójában](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

-   **Jelszóalapú bejelentkezés** – válassza ki a [jelszóalapú bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **egyszeri bejelentkezési mód** , ha az alkalmazás egy HTML-felhasználónév és jelszó mező jelenik meg, és szeretné tárolni, hogy a felhasználónév és a jelszó biztonságosan megismétlését, később az alkalmazáshoz

-   **SAML-alapú bejelentkezés** – válassza ki a [SAML-alapú bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) módba, ha az alkalmazás támogatja az SAML- vagy OpenID Connect protokollok, vagy hogy képes legyen leképezni a felhasználók az adott alkalmazás-szerepkörök szabályok alapján szeretne egyszeri bejelentkezés Az SAML-jogcímek az meghatározott *

   >[!NOTE]
   >Ez a beállítás nem érhető el, ha egy alkalmazás a proxy van konfigurálva.
   >
   >

-   **Fejlécalapú bejelentkezés** – válassza ezt a [fejlécalapú bejelentkezés](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) egyszeri bejelentkezési mód, ha egy alkalmazást, amely támogatja a HTTP-fejléc-alapú hitelesítést, amely egyszeri bejelentkezést a végrehajtani kívánt PingAccess segítségével 

   >[!NOTE]
   >Ezt a beállítást csak érhető el, ha a proxy és a PingAccess egy alkalmazás van konfigurálva.
   >
   >

-   **Integrált Windows-hitelesítés** – válassza ki a [integrált Windows-hitelesítés](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) módra, amikor szeretné végrehajtani az egyszeri bejelentkezést a helyszíni WIA alkalmazás által egyszeri bejelentkezés 

   >[!NOTE]
   >Ez a beállítás csak érhető el, ha egy alkalmazás a proxy van konfigurálva.
   >
   >

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Egyszeri bejelentkezési módok egyénileg fejlesztett alkalmazásokhoz

Egyéni munka eredményeként kifejlesztett alkalmazások rendelkeznek a [egyénileg fejlesztett alkalmazásba](#_Custom-Developed_Applications) élmény is támogatja a további egyszeri bejelentkezési módok korábban fel nem vett, többek között:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) -alapú bejelentkezésre

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) -alapú bejelentkezésre

-   [WS-Federation 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) -alapú bejelentkezésre

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) -alapú bejelentkezésre

Olvassa el a [Azure Active Directory fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) létrehozása egy egyénileg fejlesztett alkalmazásba, amely támogatja az egyszeri bejelentkezés módokban tájékozódhat.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Egy alkalmazás egyszeri bejelentkezési mód beállítása

Egy alkalmazás beállítása **egyszeri bejelentkezési** módot, az alábbi lépéseket követve:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazást, legyen az egyszeri bejelentkezés konfigurálásához.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

## <a name="how-to-choose-a-provisioning-mode"></a>Kiépítési mód kiválasztása

-   **Manuális kiépítési** – válassza ki a [manuális](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) Kiépítési mód, ha meglévő fiókot, vagy az Azure AD-en kívül az alkalmazáshoz tartozó fiókokat szeretne kezelni kívánja.

-   **Az Automatikus kiépítés** – válassza ki a [automatikus](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **létesítés** szeretné-e az automatikus API-alapú üzembe helyezés és/vagy megszüntetést felhasználói fiókok ezen alkalmazás engedélyezése 

   >[!NOTE]
   >Ez a beállítás csak alkalmazásokhoz érhető el a **kiemelt** kategóriáját a [az Azure AD Alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal#the-new-and-improved-application-gallery).
   >
   >

-   **Az Automatikus kiépítés SCIM-alapú** – használata [SCIM-alapú automatikus üzembe helyezés](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) Ha az alkalmazás támogatja-e a felhasználók és csoportok, amelyek automatikusan a módosítások kibocsátott változtatások észlelése az SCIM-protokoll az Azure AD-vel integrált alkalmazások 

   >[!NOTE]
   >Ez a beállítás nem szerepel egy adott üzembe helyezési módot, de minden olyan alkalmazás, amelyek integrálhatók az Azure AD alapértelmezés szerint engedélyezve van.
   >
   >

## <a name="how-to-set-an-applications-provisioning-mode"></a>Egy alkalmazás beállítása a kiépítési mód

Egy alkalmazás beállítása **kiépítés** módot, az alábbi lépéseket követve:

Egy alkalmazás beállítása **egyszeri bejelentkezési** módot, az alábbi lépéseket követve:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazást, amelynek meg szeretné átadásának konfigurálása.

7.  Ha az alkalmazás betöltött, kattintson a **kiépítési** az alkalmazás bal oldali navigációs menüjében.

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
