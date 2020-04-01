---
title: Az Azure AD alkalmazásproxy gyakran feltett kérdései | Microsoft dokumentumok
description: Ismerje meg a válaszokat a gyakori kérdésekre (GYIK) használatával kapcsolatban az Azure AD application proxy belső, helyszíni alkalmazások távoli felhasználók számára való közzétételéhez.
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
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: 31587777ba22dd8b4cebf81f0ff98979bb30fade
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410718"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Az Active Directory (Azure AD) alkalmazásproxyja gyakori kérdések

Ez az oldal választ ad az Azure Active Directory (Azure AD) alkalmazásproxyval kapcsolatos gyakori kérdésekre.

## <a name="enabling-azure-ad-application-proxy"></a>Az Azure AD alkalmazásproxy engedélyezése

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Milyen licenc szükséges az Azure AD alkalmazásproxy használatához?

Az Azure AD alkalmazásproxy használatához rendelkeznie kell egy Azure AD Premium P1 vagy P2 licenccel. A licenceléssel kapcsolatos további információkért lásd: [Azure Active Directory díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Miért szürkén jelenik meg az "Alkalmazásproxy engedélyezése gomb" gomb?

Győződjön meg arról, hogy rendelkezik legalább egy Azure AD Premium P1 vagy P2 licenccel és egy azure AD alkalmazásproxy-összekötővel. Az első összekötő sikeres telepítése után az Azure AD alkalmazásproxy szolgáltatás automatikusan engedélyezve lesz.

## <a name="connector-configuration"></a>Összekötő konfigurációja

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Az Alkalmazásproxy-összekötő szolgáltatások az alapértelmezettnél eltérő felhasználói környezetben futhatnak?

Nem, ez a forgatókönyv nem támogatott. Az alapértelmezett beállítások a következők:

- Microsoft AAD alkalmazásproxy-összekötő - WAPCSvc - Hálózati szolgáltatás
- Microsoft AAD alkalmazásproxy-összekötő frissítő - WAPCUpdaterSvc - NT Hatóság\Rendszer

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Saját back-end alkalmazás házigazdája több webszerver, és megköveteli a felhasználói munkamenet megőrzése (stickiness). Hogyan érhetem el a munkamenet-perzisztenciát? 

A javaslatokért olvassa el az [Alkalmazásproxy-összekötők és -alkalmazások magas rendelkezésre állású és terheléselosztási témakörét.](application-proxy-high-availability-load-balancing.md)

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Elhelyezhetek továbbítási proxyeszközt az összekötő kiszolgáló(k) és a háttéralkalmazás-kiszolgáló között?

Nem, ez a forgatókönyv nem támogatott. Csak az összekötő és a frissítési szolgáltatások konfigurálhatók egy továbbító proxy azure-ba irányuló forgalom használata. Lásd: [Meglévő helyszíni proxykiszolgálók](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers) használata

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>A TLS-végződtetés (TLS/HTTPS ellenőrzés vagy gyorsítás) az összekötő kiszolgálókról az Azure-ba irányuló forgalomban támogatott?

Az alkalmazásproxy-összekötő tanúsítványalapú hitelesítést hajt végre az Azure-ban. A TLS-végződés (TLS/HTTPS ellenőrzés vagy gyorsítás) megszakítja ezt a hitelesítési módszert, és nem támogatott. Az összekötőről az Azure-ba irányuló forgalomnak meg kell kerülnie a TLS-végződtetést végző eszközöket.  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Hozzak létre egy dedikált fiókot az összekötő regisztrálásához az Azure AD alkalmazásproxyval?

Nincs rá okom. Minden globális rendszergazdai vagy alkalmazás-rendszergazdai fiók működni fog. A telepítés során megadott hitelesítő adatok at nem használja a regisztrációs folyamat után. Ehelyett egy tanúsítványt ad ki az összekötő, amely hitelesítésre használják attól a ponttól kezdve.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Hogyan figyelheti az Azure AD alkalmazásproxy-összekötő teljesítményét?

Vannak Teljesítményfigyelő számlálók, amelyek az összekötővel együtt vannak telepítve. Megtekintésükhöz:  

1. Válassza **a Start**lehetőséget, írja be a "Perfmon" parancsot, és nyomja le az ENTER billentyűt.
2. Válassza a **Teljesítményfigyelő lehetőséget,** és kattintson a zöld **+** ikonra.
3. Adja hozzá a figyelni kívánt **Microsoft AAD alkalmazásproxy-összekötő** számlálókat.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Az Azure AD alkalmazásproxy-összekötőnek ugyanazon az alhálózaton kell lennie, mint az erőforrásnak?

Az összekötő nem szükséges, hogy ugyanazon az alhálózaton. Azonban névfeloldásra (DNS, hosts file) van szüksége az erőforráshoz és a szükséges hálózati kapcsolathoz (útválasztás az erőforráshoz, az erőforráson megnyitott portok stb.). A javaslatokért olvassa el a [Hálózati topológia szempontjai tanoncokkal kapcsolatos szempontokat az Azure Active Directory alkalmazásproxy használatakor.](application-proxy-network-topology.md)

## <a name="application-configuration"></a>Alkalmazáskonfiguráció

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Mi az alapértelmezett és a "hosszú" háttéridő-időout hossza? Meghosszabbítható-e az időtúlidőszak?

Az alapértelmezett hossz 85 másodperc. A "hosszú" beállítás 180 másodperc. Az időkorlát nem hosszabbítható meg.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Hogyan módosíthatom az alkalmazásaim által betöltett céloldalt?

Az Alkalmazásregisztrációk lapon módosíthatja a kezdőlap URL-címét a céloldal kívánt külső URL-jére. A megadott lap akkor töltődik be, amikor az alkalmazás elindul a Saját alkalmazások vagy az Office 365 portálról. A konfigurációs lépésekért olvassa el a [Közzétett alkalmazások egyéni kezdőlapjának beállítása az Azure AD alkalmazásproxy használatával című témakört.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Csak Az IIS-alapú alkalmazások tehetők közzé? Mi a helyzet a nem Windows rendszerű webkiszolgálókon futó webalkalmazásokkal? Az összekötőt olyan kiszolgálóra kell telepíteni, amelyen telepítve van az IIS?

Nem, a közzétett alkalmazásokhoz nincs IIS-követelmény. A Windows Server kiszolgálótól eltérő kiszolgálókon futó webalkalmazásokat is közzétehet. Előfordulhat azonban, hogy nem tudja használni az előhitelesítést nem Windows Server kiszolgálóval, attól függően, hogy a webkiszolgáló támogatja-e az egyeztetést (Kerberos-hitelesítés). Az IIS nem kötelező azon a kiszolgálón, amelyen az összekötő telepítve van.

## <a name="integrated-windows-authentication"></a>Beépített Windows-hitelesítés

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Mikor érdemes a Rendszerbiztonsági tagokAllowedToDelegateToAccount metódusát használni a Kerberos korlátozott delegálás (KCD) beállításakor?

A Rendszerspecifikusak AllowedToDelegateToAccount metódusa akkor használatos, ha az összekötő kiszolgálók a webalkalmazás-szolgáltatásfióktól eltérő tartományban vannak. Erőforrás-alapú korlátozott delegálás t igényel.
Ha az összekötő kiszolgálók és a webalkalmazás-szolgáltatás fiók ugyanabban a tartományban vannak, az Active Directory – felhasználók és számítógépek beépülő modul segítségével konfigurálhatja a delegálási beállításokat az összekötő számítógép fiókok mindegyikén, lehetővé téve számukra a cél szolgáltatásnév delegálását.

Ha az összekötő kiszolgálók és a webalkalmazás-szolgáltatás fiók különböző tartományokban található, erőforrás-alapú delegálást használ. A delegálási engedélyek a célwebkiszolgálón és a webalkalmazás-szolgáltatásfiókban vannak konfigurálva. A korlátozott delegálás ezen módszere viszonylag új. A módszer a Windows Server 2012 rendszerben lett bevezetve, amely támogatja a tartományok közötti delegálást azáltal, hogy lehetővé teszi az erőforrás (webszolgáltatás) tulajdonosának, hogy szabályozza, mely gép- és szolgáltatásfiókok delegálhatók rá. Nincs felhasználói felület, amely segítséget nyújt ebben a konfigurációban, ezért a PowerShellt kell használnia.
További információt a [Kerberos korlátozott delegálás ismertetése alkalmazásproxyval című tanulmányban talál.](https://aka.ms/kcdpaper)

## <a name="pass-through-authentication"></a>Átmenő hitelesítés

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Használhatom a feltételes hozzáférési házirendeket az átmenő hitelesítéssel közzétett alkalmazásokhoz?

Feltételes hozzáférési szabályzatok csak az Azure AD sikeresen hitelesített felhasználók számára. Az átmenő hitelesítés nem indítja el az Azure AD-hitelesítést, így a feltételes hozzáférési szabályzatok nem kényszeríthetők. Az átadható hitelesítéssel az MFA-házirendeket lehetőség szerint a helyszíni kiszolgálón kell megvalósítani, vagy az Azure AD alkalmazásproxyval való előhitelesítés engedélyezésével.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Közzétehetek olyan webalkalmazást, amelyügyfél-tanúsítvány hitelesítési követelményt követel?

Nem, ez a forgatókönyv nem támogatott, mert az alkalmazásproxy megszakítja a TLS-forgalmat.  

## <a name="remote-desktop-gateway-publishing"></a>Távoli asztali átjáró közzététele

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Hogyan tehetem közzé a távoli asztali átjárót az Azure AD alkalmazásproxyn keresztül?

Lásd: [Távoli asztal közzététele az Azure AD alkalmazásproxyval.](application-proxy-integrate-with-remote-desktop-services.md)

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>Használhatom a Kerberos korlátozott delegálást (egyszeri bejelentkezés – Windows integrált hitelesítés) a Távoli asztali átjáró közzétételi forgatókönyvében?

Nem, ez a forgatókönyv nem támogatott.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>A felhasználók nem használják az Internet Explorer 11-et, és az előhitelesítési forgatókönyv nem működik számukra. Ez várható?

Igen, ez várható. Az előhitelesítési forgatókönyv hez ActiveX-vezérlő szükséges, amelyet külső böngészők nem támogatnak.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>A Távoli asztali webes ügyfél (HTML5) támogatott?

Nem, ez a forgatókönyv jelenleg nem támogatott. Kövesse [uservoice](https://aka.ms/aadapuservoice) visszajelzés fórum a frissítéseket ezt a funkciót.

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Az előhitelesítési forgatókönyv konfigurálása után rájöttem, hogy a felhasználónak kétszer kell hitelesítenie magát: először az Azure AD bejelentkezési űrlapon, majd az RDWeb bejelentkezési űrlapon. Ez várható? Hogyan csökkenthetem ezt egy bejelentkezésre?

Igen, ez várható. Ha a felhasználó számítógépe az Azure AD-hez csatlakozott, a felhasználó automatikusan bejelentkezik az Azure AD-be. A felhasználónak csak a TÁVOLI webes bejelentkezési űrlapon kell megadnia a hitelesítő adatait.

## <a name="sharepoint-publishing"></a>SharePoint-közzététel

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Hogyan tehetem közzé a SharePointot az Azure AD alkalmazásproxyn keresztül?

Lásd: [Távoli hozzáférés engedélyezése a SharePointhoz az Azure AD alkalmazásproxyval](application-proxy-integrate-with-sharepoint-server.md)című részről.

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>Használhatom a SharePoint mobilalkalmazást (iOS/ Android) egy közzétett SharePoint-kiszolgáló eléréséhez?

A [SharePoint mobilalkalmazás](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) jelenleg nem támogatja az Azure Active Directory előhitelesítését.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Active Directory összevonási szolgáltatások (AD FS) közzététele 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Használhatom az Azure AD alkalmazásproxyt AD FS proxyként (például webalkalmazás-proxyként)?

Nem. Az Azure AD alkalmazásproxy úgy lett kialakítva, hogy együttműködjön az Azure AD-vel, és nem felel meg az AD FS-proxyként való használatra vonatkozó követelményeknek.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>Működik a WebSocket a QlikSense-től eltérő alkalmazásokhoz?

Jelenleg a WebSocket protokoll támogatása továbbra is nyilvános előzetes verzióban érhető el, és előfordulhat, hogy más alkalmazásokesetében nem működik. Egyes ügyfelek vegyes sikert értek el a WebSocket protokoll más alkalmazásokkal való használatával. Ha teszteli az ilyen forgatókönyveket, szeretnénk hallani az eredményeket. Kérjük, küldje aadapfeedback@microsoft.comel nekünk visszajelzését a .

A Windows Felügyeleti központban (WAC) vagy a Távoli asztali webes ügyfélben (HTML5) található szolgáltatások (Eseménynaplók, PowerShell és Távoli asztali szolgáltatások) jelenleg nem működnek az Azure AD alkalmazásproxyn keresztül.

## <a name="link-translation"></a>Hivatkozás fordítása

### <a name="does-using-link-translation-affect-performance"></a>Befolyásolja a linkfordítás használata a teljesítményt?

Igen. A hivatkozás fordítása hatással van a teljesítményre. Az alkalmazásproxy szolgáltatás megvizsgálja az alkalmazást a kódolt hivatkozások után, és lecseréli azokat a megfelelő, közzétett külső URL-címekre, mielőtt a felhasználónak mutatná be őket. 

A legjobb teljesítmény érdekében azt javasoljuk, hogy azonos belső és külső URL-címeket használjon [egyéni tartományok](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)konfigurálásával. Ha az egyéni tartományok használata nem lehetséges, javíthatja a hivatkozásfordítási teljesítményt a My Apps Secure Sign in Extension vagy a Microsoft Edge Browser mobilon. Lásd: [Az Azure AD alkalmazásproxyval közzétett alkalmazások hoz létre kódolt hivatkozások átirányítása.](application-proxy-configure-hard-coded-link-translation.md)

## <a name="wildcards"></a>Helyettesítő karakterek

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Hogyan lehet helyettesítő karaktereket használni két azonos egyéni tartománynévvel rendelkező, de különböző protokollokkal rendelkező, http-vel és egy HTTPS-hez való alkalmazás közzétételére?

Ez a forgatókönyv nem támogatott közvetlenül. A forgatókönyv beállításai a következők:

1. Tegye közzé a HTTP- és HTTPS-URL-címeket is külön alkalmazásként helyettesítő karakterrel, de mindegyiknek más-más egyéni tartományt adjon. Ez a konfiguráció működni fog, mivel különböző külső URL-címekkel rendelkeznek.

2. A HTTPS URL-cím közzététele helyettesítő alkalmazáson keresztül. A HTTP-alkalmazásokat külön tegye közzé az alábbi Alkalmazásproxy-PowerShell-parancsmagok használatával:
   - [Alkalmazásproxy-alkalmazáskezelés](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Alkalmazásproxy-összekötő kezelése](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
