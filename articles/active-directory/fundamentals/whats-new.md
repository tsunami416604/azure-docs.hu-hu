---
title: Újdonságok Kibocsátási megjegyzések az Azure ad-hez |} A Microsoft Docs
description: Ismerje meg, mi az új Azure Active Directoryval (Azure AD), például a legújabb kibocsátási megjegyzések, az ismert problémák, hibajavításokat tartalmaz, az elavult funkciók és a jövőbeni változtatásokról.
services: active-directory
author: eross-msft
manager: mtillman
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: lizross
ms.reviewer: dhanyahk
ms.openlocfilehash: dc4e421808ab0f79070224edea4b75f527affaf0
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426237"
---
# <a name="whats-new-in-azure-active-directory"></a>Újdonságok az Azure Active Directoryban?

>Értesítést kaphat arról, hogy a másolás és beillesztés, az URL-cím szerint nyissa meg újra ezt oldal frissítések mikor: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` be a ![RSS ikon](./media/whats-new/feed-icon-16x16.png) hírcsatorna-olvasó.

Az Azure AD folyamatosan fejlesztései kap. Naprakész a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

- A legújabb kiadásaihoz.
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- Módosítások tervek

Ezen a lapon havonta frissül, így rendszeresen ellenőrizni. Ha a keresett elemek, amelyek régebbiek 6 hónapos, megtalálhatja a a [archiválás What's new in Azure Active Directory](whats-new-archive.md).

---
## <a name="october-2018"></a>2018. október

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Az Azure AD naplókat mostantól működik az Azure Log Analytics (nyilvános előzetes verzió)

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** jelentéskészítés  
**A termék funkció:** Monitorozás és jelentéskészítés

Örömmel jelentjük, hogy most már továbbítás az Azure AD-naplók az Azure Log Analytics! A várt funkció segítségével még jobb hozzáférést biztosíthat a elemzés az üzleti, műveletek, és a biztonsági, valamint az infrastruktúra figyelése a megoldást. További információkért lásd: a [Azure Active Directory-Tevékenységnaplók naplók az Azure Log Analytics mostantól elérhető](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény – 2018. október

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** vállalati alkalmazások  
**A termék funkció:** 3. fél integrációja
 
2018 október tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 14 új alkalmazásokról:

[A díjjal járó pontokat](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 virtuális környezet](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler három](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot vezérlő](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Az Azure AD Domain Services E-mail-értesítések

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** Azure AD tartományi szolgáltatások  
**A termék funkció:** Azure AD tartományi szolgáltatások

Az Azure AD Domain Services konfigurációs hibák vagy problémák a felügyelt tartományhoz az Azure Portalon riasztásokat biztosít. Ezek a riasztások tartalmazzák a részletes útmutatókat, így anélkül, hogy forduljon az ügyfélszolgálathoz a probléma javításához próbálkozhat.

Októbertől az lesz az értesítési beállításokat a felügyelt tartományhoz tartozó, amikor új kapcsolatos riasztások történnek, egy e-mailt küld egy kijelölt csoport tagjainak, így nem kell folyamatosan ellenőrzése a portálon a frissítéseket.

További információkért lásd: [értesítési beállítások az Azure AD tartományi szolgáltatásokban](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Az Azure AD portal támogatja a ForceDelete tartomány API használatával törli az egyéni tartományok 

**Típus:** megváltozott funkció  
**Szolgáltatási kategóriához:** Címtárkezelés  
**A termék funkció:** könyvtár

Örömmel jelentjük, hogy mostantól használhatja a ForceDelete tartomány API aszinkron módon átnevezésével, mint például a felhasználók, csoportok és alkalmazások az egyéni tartománynevet (contoso.com) a biztonsági mutató hivatkozásokat a kezdeti alapértelmezett tartomány nevét (az egyéni tartománynevek törlése contoso.onmicrosoft.com).

Ez a változás segítségével gyorsabban törölni az egyéni tartománynevek, ha a szervezet már nem használja a nevét, vagy ha szeretné használni a tartománynév egy másik Azure AD-val.

További információkért lásd: [egy egyéni tartománynév törlése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>2018. szeptember
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>A dinamikus csoportok esetében frissültek a rendszergazdai szerepkör engedélyei

**Típus:** kijavítva  
**Szolgáltatási kategóriához:** eszközcsoport-kezelés  
**A termék funkció:** együttműködés

Azt már megtörtént egy probléma javítása, adott rendszergazdai szerepkörök mostantól létrehozhat és frissíthet a dinamikus tagsági szabályok, anélkül, hogy a csoport tulajdonosa legyen.

A szerepkörök a következők:

- Globális rendszergazda vagy a vállalati író

- Intune szolgáltatás rendszergazdája

- Felhasználóifiók-adminisztrátor

További információkért lásd: [állapotának ellenőrzése és a egy dinamikus csoport létrehozása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Néhány külső gyártótól származó alkalmazás esetében egyszerűsödött az egyszeri bejelentkezés (SSO) konfigurálása

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** vállalati alkalmazások  
**A termék funkció:** egyszeri bejelentkezés

Tisztában vagyunk vele, hogy állítja be egyszeri bejelentkezést (SSO) szoftverfrissítési szolgáltatásként (SaaS) alkalmazások kihívást jelenthet az egyes alkalmazások konfiguráció egyedi jellege miatt. Elkésztettünk egy leegyszerűsített konfigurációs élményt biztosít az egyszeri bejelentkezés beállításai a következő külső SaaS-alkalmazások automatikus feltöltéséhez:

- Zendesk

- Az ArcGis Online

- A Jamf Pro

Az egykattintásos felület használatának megkezdéséhez nyissa meg a **az Azure portal** > **egyszeri bejelentkezési konfiguráció** az alkalmazás lapját. További információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Az Azure Active Directory - hol található a adatait? lap

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** más  
**A termék funkció:** GoLocal

Válassza ki a régiót a vállalat a **Azure Active Directory - hol található a adatait** oldal nézet, mely Azure-beli adatközpont-környezetet tároló az Azure AD-szolgáltatásokhoz az Azure AD inaktív adatokat. Specifikus szerint szűrheti az adatokat az Azure AD-szolgáltatások számára, a vállalat.

Ez a funkció eléréséhez, és további információkért lásd: [Azure Active Directory - hol található a adatait](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Új üzembe helyezési terv érhető el a Saját alkalmazások hozzáférési panelen

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** saját alkalmazások  
**A termék funkció:** egyszeri bejelentkezés

Tekintse meg az új központi telepítési csomag, amely a saját alkalmazások hozzáférési panelen érhető el (https://aka.ms/deploymentplans).
A saját alkalmazások hozzáférési panel itt rendelkező felhasználók számára egyetlen helyen megtalálhatja és elérheti a saját alkalmazásokban. Ezen a portálon a felhasználók önkiszolgáló lehetőségeket biztosít, például alkalmazások és a csoportok hozzáférést kér vagy erőforrásokhoz való hozzáférés kezelését ezek mások nevében is biztosít.

További információkért lásd: [Mi az a saját alkalmazások portál?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Új lappal (Hibaelhárítás és támogatás) bővült az Azure Portal Bejelentkezések weblapja

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** jelentéskészítés  
**A termék funkció:** Monitorozás és jelentéskészítés

Az új **hibaelhárítás és támogatás** lapján a **bejelentkezések** oldal az Azure portal, ismerteti, hogy a rendszergazdák és a támogatási szakértők az Azure AD bejelentkezési kapcsolatos problémák elhárítása. Ezen a lapon biztosítja a hibakódot, hibaüzenet jelenik meg, és korrigálási javaslatokat tesz (ha van ilyen) a probléma megoldásához. Ha nem sikerül a probléma megoldásához, azt is meg kell adni, hozzon létre egy támogatási jegyet az új módon a **példányt vágólapra** észlel, amely feltölti a **Kérelemazonosító** és **dátuma (UTC)** meg a naplófájlt a támogatási jegy mezőket.  

![Jelentkezzen be az új lap naplók](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>A dinamikus tagsági szabályok létrehozására szolgáló egyénibővítmény-tulajdonságok továbbfejlesztett támogatása

**Típus:** megváltozott funkció  
**Szolgáltatási kategóriához:** eszközcsoport-kezelés  
**A termék funkció:** együttműködés

Ez a frissítés most kattintson a **egyéni bővítménytulajdonság lekérése** a dinamikus felhasználói csoport szabály builder a hivatkozásra, adja meg az alkalmazás egyedi azonosítója, és kap egy dinamikus létrehozásakor egyéni bővítménytulajdonságok teljes listája Tagsági szabály a felhasználók számára. Ez a lista is frissíthetők úgy, hogy minden olyan új egyéni bővítmény tulajdonságainak lekérése az adott alkalmazáshoz.

A dinamikus tagsági szabályok egyéni bővítménytulajdonságok használatával kapcsolatos további információkért lásd: [bővítmény és egyéni bővítmény tulajdonságai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD-beli alkalmazásalapú feltételes hozzáférés – a jóváhagyott ügyfélalkalmazások listája új alkalmazásokkal bővült

**Típus:** tervezett változtatás  
**Szolgáltatási kategóriához:** feltételes hozzáférés  
**A termék funkció:** identitás biztonsága és védelme

A következő alkalmazások olyan listájában [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

További információkért lásd:

- [Az Azure AD, alkalmazásalapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Már támogatott a Windows 7/8/8.1 zárolási képernyőjéről történő önkiszolgáló jelszómódosítás

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** SSPR  
**A termék funkció:** felhasználói hitelesítés

Miután beállította az új funkciót, a felhasználók a jelszavuk egy hivatkozás jelenik-e a **zárolási** egy Windows 7, Windows 8 vagy Windows 8.1 rendszerű eszköz képernyőjén. A hivatkozásra kattintva a felhasználó rendszer végigvezeti a azonos jelszóvisszaállítási folyamatot a webböngészőn keresztül.

További információkért lásd: [a Windows 7, 8 és 8.1 jelszóátállítás engedélyezése](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Változással kapcsolatos értesítés: a hitelesítő kódok a későbbiekben már nem lesznek újrafelhasználhatók 

**Típus:** tervezett változtatás  
**Szolgáltatási kategóriához:** hitelesítések (Bejelentkezések)  
**A termék funkció:** felhasználói hitelesítés

2018. November 15., kezdve az Azure AD leáll, az alkalmazások korábban használt hitelesítési kódok elfogadásával. Ez a változás segítséget nyújt ahhoz, hogy az Azure AD az OAuth-specifikációnak megfelelően, és alkalmazza a v1 és v2 végpontokon.

Ha az alkalmazás újból felhasználja a jogkivonatok lekérésére, több erőforrás-engedélyezési kódokat, javasoljuk, hogy a kód használatával egy frissítési jogkivonat lekérése, és a frissítési jogkivonat használatával más erőforrások kiegészítő jogkivonatok beszerzéséhez. Engedélyezési kód csak egyszer használhatók fel, de frissítési biztonsági jogkivonat használható többször több erőforrást. Egy alkalmazás, amely megpróbálja újból felhasználhatja a hitelesítési kódot az OAuth hitelesítésikód-folyamata során invalid_grant hiba lép fel.

Ez és egyéb protokollok-vel kapcsolatos módosításokat, lásd: [teljes listája megtalálható az új hitelesítési](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában – 2018. szeptember

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** vállalati alkalmazások  
**A termék funkció:** 3. fél integrációja
 
2018 szeptember tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 16 új alkalmazásokról:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet szoftver felvételi](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS vállalati](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [ JDA felhőalapú](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft egyszeri bejelentkezés az Azure-hoz, használható surveymonkey szolgáltatást [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Bővült a támogatott jogcím-átalakítási módszerek köre

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** vállalati alkalmazások  
**A termék funkció:** egyszeri bejelentkezés

Új jogcím átalakítása módszer ToLower() és ToUpper(), amely is lehet alkalmazni a SAML-jogkivonatok az SAML-alapú vezettünk **egyszeri bejelentkezési konfigurációjának** lapot.

További információkért lásd: [az Azure ad-ben a vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Frissült az SAML-alapú alkalmazáskonfigurációs kezelőfelület (előzetes verzió)

**Típus:** megváltozott funkció  
**Szolgáltatási kategóriához:** vállalati alkalmazások  
**A termék funkció:** egyszeri bejelentkezés

A frissített SAML-alapú alkalmazás-konfigurációs felhasználói Felületet részeként láthatja:

- Egy frissített útmutató felületet, az SAML-alapú alkalmazások konfigurálásához.

- Mi az a hiányzó vagy helytelen a konfiguráció a kapcsolatos láthatóságot.

- Lehetővé teszi, hogy az e-mail címeket lejárati tanúsítványt az értesítésre.

- Új jogcím-átalakítási módszerek, ToLower() ToUpper() és még.

- Töltse fel a saját jogkivonat-aláíró tanúsítvány a vállalati alkalmazások lehetőséget.

- Is be lehet állítani az alkalmazások SAML NameID-formátum, és a NameID értéket állítja be Címtárbővítmények módja.

A frissített nézet bekapcsolásához kattintson a **próbálja ki az új funkciót** hivatkozás tetején a **egyszeri bejelentkezés** lap. További információkért lásd: [oktatóanyag: konfigurálása SAML-alapú egyszeri bejelentkezés az Azure Active Directory-alkalmazás](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).

---

## <a name="august-2018"></a>2018. augusztus

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Módosultak az Azure Active Directory-beli IP-címtartományok

**Típus:** tervezett változtatás  
**Szolgáltatási kategóriához:** más  
**A termék funkció:** Platform

Nagyobb IP-címtartományok vezetünk be az Azure ad Szolgáltatásba, ami azt jelenti, ha konfigurálta az Azure AD IP-címtartományok esetében a tűzfalak, útválasztók és hálózati biztonsági csoportok kell azokat. Így nem kell a tűzfalat, útválasztó vagy a hálózati biztonsági csoportok IP-címtartomány konfigurációk újra módosítása esetén az Azure AD új végpontjait hozzáadja ezt a frissítést végzünk. 

Hálózati forgalom ezen új tartományok áthelyezése a következő két hónapban. Szolgáltatás megszakításmentes folytatásához, hozzá kell adnia a frissített értékeket az IP-címek 2018. szeptember 10. előtt:

- 20.190.128.0/18 

- 40.126.0.0/18 

Javasoljuk, hogy nem távolítja a régi IP-címtartományokat, mindaddig, amíg az összes, a hálózati forgalom át lett helyezve az új tartományokat. Frissítések információt, és ismerje meg, amikor eltávolíthatja a régi tartományokat, lásd: [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Változással kapcsolatos értesítés: a hitelesítő kódok a későbbiekben már nem lesznek újrafelhasználhatók 

**Típus:** tervezett változtatás  
**Szolgáltatási kategóriához:** hitelesítések (Bejelentkezések)  
**A termék funkció:** felhasználói hitelesítés

2018. November 15., kezdve az Azure AD leáll, az alkalmazások korábban használt hitelesítési kódok elfogadásával. Ez a változás segítséget nyújt ahhoz, hogy az Azure AD az OAuth-specifikációnak megfelelően, és alkalmazza a v1 és v2 végpontokon.

Ha az alkalmazás újból felhasználja a jogkivonatok lekérésére, több erőforrás-engedélyezési kódokat, javasoljuk, hogy a kód használatával egy frissítési jogkivonat lekérése, és a frissítési jogkivonat használatával más erőforrások kiegészítő jogkivonatok beszerzéséhez. Engedélyezési kód csak egyszer használhatók fel, de frissítési biztonsági jogkivonat használható többször több erőforrást. Egy alkalmazás, amely megpróbálja újból felhasználhatja a hitelesítési kódot az OAuth hitelesítésikód-folyamata során invalid_grant hiba lép fel.

Ez és egyéb protokollok-vel kapcsolatos módosításokat, lásd: [teljes listája megtalálható az új hitelesítési](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Önkiszolgáló jelszóregisztráció (SSPR) és többtényezős hitelesítés (MFA) – összevont biztonságiadat-kezelés

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** SSPR  
**A termék funkció:** felhasználói hitelesítés
 
Ez az új funkció lehetővé teszi a biztonsági adataikat (mint például a telefonszám, mobilalkalmazás és így tovább) kezelheti az SSPR és a egy egyetlen helyen, és felület; MFA képest a korábban, ahol tette két különböző helyen.

SSPR vagy MFA használatával személyek is használható az átszervezett felületet. Ezenkívül ha a szervezet nem kényszeríti a többtényezős hitelesítés vagy az SSPR regisztrációt, személyek továbbra is regisztrálhatja bármilyen MFA vagy az SSPR biztonsági adatai módszer engedélyezett a munkahelyen a saját alkalmazások portálról.

Egy választható nyilvános előzetes kiadásról. A rendszergazdák bekapcsolhatja az új felhasználói felület (ha szükséges) a kijelölt csoport vagy a bérlő összes felhasználója esetében. Konvergens funkciókkal kapcsolatos további információkért lásd: a [Konvergens élmény blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Új (csak a HTTP használatát támogató) cookie-beállítás az Azure AD Application Proxy-alkalmazásokban

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** alkalmazásproxy  
**A termék funkció:** hozzáférés-vezérlés

Van egy új nevű beállítása, **HTTP-Only cookie-k** az alkalmazásproxy-alkalmazásokban. Ez a beállítás biztosítja, beleértve a HTTP-válaszfejléc mindkét alkalmazásproxy hozzáférési és munkamenet-cookie-khoz a HTTPOnly jelző, hozzáférés leállítása a cookie-val ügyféloldali parancsfájl és további megakadályozza a műveleteket, például a Másolás adja meg a további biztonsági vagy a cookie-k módosítását. Bár ez a jelző korábban még nem használt, a cookie-kat mindig lettek titkosítva, és nem megfelelő módosításokat elleni védelem érdekében az SSL-kapcsolat használatával.

Ez a beállítás nem kompatibilis az alkalmazások az ActiveX-vezérlők, például a távoli asztal használatával. Ha ebben a helyzetben, azt javasoljuk, hogy kapcsolja ki ezt a beállítást.

A HTTP-Only cookie-kra vonatkozó beállítással kapcsolatos további információkért lásd: [alkalmazások közzététele az Azure AD-alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>A Privileged Identity Management (PIM) az Azure-erőforrások esetében támogatja a felügyeleti csoport típusú erőforrásokat

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** Privileged Identity Management  
**A termék funkció:** Privileged Identity Management
 
Just-In-Time aktiválási és hozzárendelési beállítások is érvényesek a felügyeleti csoport erőforrástípusok, ugyanúgy, mint az előfizetések, erőforráscsoportok és erőforrások (például virtuális gépek, alkalmazásszolgáltatások, és további) már nincs. Ezenkívül bárki egy szerepkör, amely rendszergazdai hozzáférést biztosít a felügyeleti csoport felderítése és kezelése a PIM erőforrás.

A PIM és az Azure-erőforrásokkal kapcsolatos további információkért lásd: [felderítése és az Azure-erőforrások kezelése a Privileged Identity Management használatával](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Az alkalmazás-hozzáférés (előzetes verzió) gyorsabb hozzáférést biztosít az Azure AD-portálhoz.

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** Privileged Identity Management  
**A termék funkció:** Privileged Identity Management
 
Jelenleg a PIM használata szerepkör aktiválásakor is igénybe vehet az engedélyek érvénybe több mint 10 perc. Ha úgy dönt, hogy az alkalmazás-hozzáférést, amely jelenleg nyilvános előzetes verzióban érhető el, használja a rendszergazdák hozzáférhetnek az Azure AD portálra, amint az aktiválási kérelem befejeződött.

Jelenleg alkalmazás elérése csak támogatja az Azure AD-portál felületének és az Azure-erőforrások. A PIM és az alkalmazások kapcsolatos további információkért lásd: [Mi az Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában – 2018. augusztus

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** vállalati alkalmazások  
**A termék funkció:** 3. fél integrációja
 
2018 augusztus tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 16 új alkalmazásokról:

[Szarvascsőrűmadár](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [kötetlen Bridgeline](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [mártás Labs - mobil- és webes tesztelés](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta-összekötő hálózatok](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [úgy tesszük](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (által Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dokumentáció](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - kiadás jelentések](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 élő csevegés](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [ Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>A natív Tableau-támogatás már elérhető az Azure AD Application Proxyban

**Típus:** megváltozott funkció  
**Szolgáltatási kategóriához:** alkalmazásproxy  
**A termék funkció:** hozzáférés-vezérlés

A frissítés az OpenID Connect, az üzem előtti hitelesítési protokoll az OAuth 2.0-s Kódmegadás protokoll már nem kell az alkalmazásproxy használatával a Tableau használandó további konfigurációt elvégezni. Ez a protokoll változás is segít alkalmazásproxy hatékonyabban támogatják a több modern alkalmazások csak a HTTP átirányítást, gyakran támogatott JavaScript és HTML-címkék használatával.

A natív módon támogatja a Tableau kapcsolatos további információkért lásd: [mostantól natív Tableau támogatásával az Azure AD-alkalmazásproxy](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Már támogatott a Google identitásszolgáltatóként való beállítása a vállalatközi vendégfelhasználók számára (előzetes verzió)

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** B2B  
**A termék funkció:** B2B és B2C-vel

A szervezet a Google összevonási beállításával engedélyezheti, meghívott Gmail felhasználók bejelentkezési a megosztott alkalmazások és erőforrások a meglévő Google-fiók létrehozása a személyes Microsoft-Account (msa-k) vagy egy Azure AD-fiók nélkül.

Egy választható nyilvános előzetes kiadásról. Google összevonási kapcsolatos további információkért lásd: [Identitásszolgáltatóként B2B vendégfelhasználó hozzáadása Google](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>2018. július

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Az Azure Active Directory értesítő e-mailjeinek fejlesztései

**Típus:** megváltozott funkció  
**Szolgáltatási kategóriához:** más  
**A termék funkció:** identitás-életciklus-felügyelet
 
Az Azure Active Directory (Azure AD) e-mailek most szolgáltatás egy új megjelenés, valamint a módosítások a feladó e-mail címe és a feladó megjelenített neve, amikor a következő szolgáltatások által küldött:
 
- Az Azure AD hozzáférési felülvizsgálatok
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Vállalati alkalmazás lejáró Tanúsítványértesítéseket
- Vállalati alkalmazás-kiépítés szolgáltatási értesítések
 
Az e-mail-értesítések, a következő e-mail címre küldi, és a megjelenített neve:

- E-mail-cím: azure-noreply@microsoft.com
- Megjelenített név: Microsoft Azure
 
Egy vonatkozó példáért egyes új e-mail mintákra és további információkat lásd: [E-mail-értesítések az Azure AD PIM-ben](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Az Azure AD tevékenységnaplói most már elérhetők az Azure Monitorban

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** jelentéskészítés  
**A termék funkció:** Monitorozás és jelentéskészítés

Az Azure AD-Tevékenységnaplók mostantól elérhetők az Azure monitor (az Azure platform kiterjedő figyelési szolgáltatás) nyilvános előzetes verzióban érhető el. Az Azure Monitor kínál hosszú távú adatmegőrzés és zökkenőmentes integráció mellett ezek a fejlesztések:

- Hosszú távú megőrzése a naplófájlok irányítva a saját Azure storage-fiókba.

- Zökkenőmentes SIEM-integráció, nem kell írnia, vagy egyéni parancsfájlok karbantartása.

- Zökkenőmentes integráció a saját egyéni megoldások, elemzési eszközök vagy az incidenskezelés megoldásokat.

Ezekkel az új képességekkel kapcsolatos további információkért lásd: blogunkat [az Azure AD-Tevékenységnaplók a diagnosztika jelenleg nyilvános előzetes verzióban elérhető az Azure Monitor](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) és a dokumentációban, [Azure Active Directory-Tevékenységnaplók az Azure-ban A figyelő (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Az Azure AD bejelentkezési jelentése feltételes hozzáférési adatokkal bővült

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** jelentéskészítés  
**A termék funkció:** Identitásbiztonság és -védelem
 
Ez a frissítés lehetővé teszi, hogy láthatja, hogy mely házirendek kiértékelése a felhasználó bejelentkezésekor mellett szabályzat eredménye. A jelentés emellett mostantól tartalmazza a felhasználó által használt, így azonosíthatja a régi protokoll forgalom ügyfélalkalmazás típusa. Jelentés bejegyzések most is kereshető a korrelációs Azonosítót, amely tekintheti meg a felhasználók felé néző hibaüzenet és azonosítása és megoldása a megfelelő bejelentkezési kérelem használható.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>A régi hitelesítések megtekintése a bejelentkezések tevékenységnaplói révén

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** jelentéskészítés  
**A termék funkció:** Monitorozás és jelentéskészítés
 
Bevezetésével a **ügyfélalkalmazás** -naplókat a bejelentkezési tevékenységek mezője ügyfelek is most lásd: felhasználók, amelyek az örökölt hitelesítés használata. Ügyfelek érhetik el ezeket az információkat a bejelentkezések az MS Graph API használatával is, vagy a bejelentkezés keresztül Tevékenységnaplók az Azure AD-portálra, ahol használhatja a **ügyfélalkalmazás** örökölt hitelesítések szűrés vezérlő. Tekintse meg a dokumentáció További részletekért.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>2018 júliusától új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** vállalati alkalmazások  
**A termék funkció:** 3. fél integrációja
 
2018 július tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 16 új alkalmazásokról:

[Innováció Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [bizonyos rendszergazdai egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC átmeneti, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [képernyőfelvétel-O felosztásban](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial) , PowerSchool osztályterem, egységes [Eli bevezetési](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar távoli támogatási](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial) , [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow összekötő](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [képességek alapja](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>2018. július – új felhasználókiépítési funkció az SaaS-alkalmazás-integrációkban

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** Alkalmazáskiosztási  
**A termék funkció:** 3. fél integrációja
 
Azure ad-ben automatizálhatja a létrehozás, a karbantartással és a SaaS-alkalmazások, például a Dropbox, a Salesforce, ServiceNow vagy további felhasználói identitásokat eltávolítását teszi lehetővé. 2018 július hozzáadtuk a felhasználók a következő alkalmazások az Azure AD-alkalmazásgyűjtemény támogatása:

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Minden alkalmazás, amely támogatja a felhasználók átadásának az Azure AD katalógusban listáját lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync – Az árván maradt és ismétlődő attribútumok szinkronizálásából fakadó hibák javításának egyszerűbb módja

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** AD Connect  
**A termék funkció:** Monitorozás és jelentéskészítés
 
Az Azure AD Connect Health önkiszolgáló szervizelést kell végeznie, jelölje ki és hárítsa el a szinkronizálási hibák mutatja be. Ez a szolgáltatás hibaelhárítása a duplikált attribútummal szinkronizálási hibák, és kijavítja a rendszer árva objektumokat az Azure ad-ből. A diagnosztika a következő előnyökkel jár:

- Duplikált attribútummal szinkronizálási hibák, adott javításokat nyújtó leszűkíti

- A javítás vonatkozik, kijelölve, az Azure AD-forgatókönyvek esetén egyetlen lépésben hibák megoldása

- Nincs frissítés vagy a konfiguráció be-és a funkció használatához szükséges

További információkért lásd: [diagnosztizálása és a duplikált attribútummal szinkronizálási hibák javítása](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Az Azure AD és az MSA bejelentkezési felületének vizuális frissítései

**Típus:** megváltozott funkció  
**Szolgáltatási kategóriához:** Azure ad-ben  
**A termék funkció:** felhasználói hitelesítés

Frissítettük a felhasználói felületen, a Microsoft online services bejelentkezési élményt, például az Office 365 és az Azure. Ez a változás lehetővé teszi a képernyők, kevésbé zsúfolt és egyszerűbb. Ez a változás kapcsolatos további információkért lásd: a [hamarosan várható az Azure AD bejelentkezési felület fejlesztései](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) blog.

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Az Azure AD Connect új kiadása – 2018. július

**Típus:** megváltozott funkció  
**Szolgáltatási kategóriához:** Alkalmazáskiosztási  
**A termék funkció:** identitás-életciklus-felügyelet

Az Azure AD Connect legújabb kiadása tartalmazza: 

- Hibajavítások és a támogatási frissítéseket. 

- A Ping összevonni integráció általános elérhetősége

- A legújabb SQL 2012-ügyfél frissítései 

A frissítéssel kapcsolatos további információkért lásd: [az Azure AD Connect: verziókiadások](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-tou-end-user-ui"></a>A használati feltételek (TOU) végfelhasználói felületének frissítései

**Típus:** megváltozott funkció  
**Szolgáltatási kategóriához:** használati feltételek  
**A termék funkció:** Cégirányítási

Frissítjük a jelen használati feltételek végfelhasználó felhasználói felületén az elfogadási karakterlánc.

**Aktuális szöveg.** [TenantName] erőforrásokhoz férhet hozzá, el kell fogadnia a használati feltételeket.<br>**Új szöveget.** [TenantName] erőforrás eléréséhez olvassa el a használati feltételeket.

**Aktuális szöveggel:** választva fogadja el az azt jelenti, hogy Ön vállalja, hogy az összes a fenti használati feltételeket.<br>**Új szöveges:** kattintson az Elfogadás gombra annak megerősítéséhez, hogy elolvasta és megértette a használati feltételeket.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Az átmenő hitelesítés támogatja az örökölt protokollok és alkalmazások használatát

**Típus:** megváltozott funkció  
**Szolgáltatási kategóriához:** hitelesítések (Bejelentkezések)  
**A termék funkció:** felhasználói hitelesítés
 
Az átmenő hitelesítés most már támogatja az örökölt protokollok és alkalmazások. Most már teljes mértékben támogatja a következő korlátozások vonatkoznak:

- Felhasználói bejelentkezések örökölt Office ügyfélalkalmazások számára, az Office 2010 és Office 2013, anélkül, hogy a modern hitelesítést.

- A hozzáférést az naptár megosztása és a rendelkezésre állási információk az Exchange hibrid környezetek az Office 2010-et csak.

- Felhasználói bejelentkezések Skype az üzleti ügyfélalkalmazások számára a modern hitelesítés nélkül.

- Felhasználói bejelentkezések a PowerShell 1.0-s verzióját.

- Az Apple Device Enrollment Program (Apple DEP), az iOS beállítási asszisztens használatával. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Az önkiszolgáló jelszóátállításhoz és a többtényezős hitelesítéshez használt biztonsági adatok kezelésének összevonása

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** SSPR  
**A termék funkció:** felhasználói hitelesítés

Ez az új funkció lehetővé teszi, hogy a felhasználók saját biztonsági adatok (például telefonszám, e-mail címét, mobilalkalmazás és így tovább) önkiszolgáló jelszó-visszaállítás (SSPR) és a multi-factor Authentication (MFA) egyetlen felületen kezeljék. Felhasználók többé nem kell az azonos biztonsági információkat kelljen regisztrálniuk az SSPR és a többtényezős hitelesítés a két különböző környezeteket. Az új felületet is vonatkozik, akik rendelkeznek vagy az SSPR, vagy a többtényezős hitelesítés.

Egy szervezet MFA vagy az SSPR regisztrációs nem kényszerítése, ha a felhasználók regisztrálhatják-e a biztonsági adataikat keresztül a **saját alkalmazások** portálon. Itt a felhasználók regisztrálhatnak semmilyen metódus az MFA vagy az SSPR engedélyezve van. 

Egy választható nyilvános előzetes kiadásról. A rendszergazdák bekapcsolhatja az új felhasználói felület (ha szükséges) felhasználók vagy a bérlő minden felhasználók egy kiválasztott csoportja számára.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>A Microsoft Authenticator alkalmazással igazolhatja személyazonosságát a jelszava átállításakor

**Típus:** megváltozott funkció  
**Szolgáltatási kategóriához:** SSPR  
**A termék funkció:** felhasználói hitelesítés

Ez a funkció lehetővé teszi, hogy a nem rendszergazda jogosultságú igazolják egy értesítést vagy a Microsoft Authenticator (vagy bármely más hitelesítő alkalmazást) kód jelszó alaphelyzetbe állítása. Rendszergazdák bekapcsolása után a önkiszolgáló jelszó-visszaállítási mód, mobilalkalmazás aka.ms/mfasetup vagy aka.ms/setupsecurityinfo keresztül regisztráló felhasználók használhatja mobilalkalmazásukkal ellenőrzési módszerként a jelszó alaphelyzetbe állítása során.

Mobilalkalmazás-értesítés csak be kell kapcsolni, szabályzata előírja a két módszer a jelszó részeként.

---

## <a name="june-2018"></a>2018. június

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Módosítsa az értesítés: a delegált engedélyezési folyamat használata az Azure AD tevékenység naplók API-alkalmazások biztonsági javítás

**Típus:** tervezett változtatás  
**Szolgáltatási kategóriához:** jelentéskészítés  
**A termék funkció:** Monitorozás és jelentéskészítés

Az erősebb biztonsági kényszerítési miatt volt módosítást a delegált engedélyezési folyamatok eléréséhez használó alkalmazásokra vonatkozó engedélyeinek [az Azure AD tevékenység naplók API-k](https://aka.ms/aadreportsapi). Ez a változás történik a **2018. június 26**.

Ha bármely, az alkalmazások az Azure AD tevékenység API-k, kövesse az alábbi lépéseket annak érdekében, hogy az alkalmazás nem lesz történik, a módosítás után új.

**Az Alkalmazásengedélyek frissítése**

1. Jelentkezzen be az Azure Portalon, válassza **Azure Active Directory**, majd válassza ki **Alkalmazásregisztrációk**.
2. Válassza ki az Azure AD tevékenység naplók API-t használó, válassza ki az alkalmazást **beállítások**, jelölje be **szükséges engedélyek**, majd válassza ki a **Windows Azure Active Directory** API.
3. A a **delegált engedélyek** területén a **hozzáférés engedélyezése** panelen jelölje be a **olvasási directory** adatokat, és válassza ki **mentése**.
4. Válassza ki **engedélyeket**, majd válassza ki **Igen**.
    
    >[!Note]
    >A következőkhöz biztosítanak engedélyeket: az alkalmazás globális rendszergazdának kell lennie.

További információkért lásd: a [engedélyeket](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) eléréséhez az Azure AD jelentéskészítési API-t a cikk az Előfeltételek területéhez.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Az Azure AD-szolgáltatásokhoz, a PCI DSS TLS-beállítások konfigurálása

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** N/A  
**A termék funkció:** Platform

Transport Layer Security (TLS) protokoll, amely két kommunikáló alkalmazás közötti adatvédelmi és adatintegritás biztosít, és a jelenleg használt leggyakrabban telepített biztonsági protokoll.

A [PCI biztonsági szabványok Tanácsa](https://www.pcisecuritystandards.org/) megállapította, hogy korai verziói a TLS és a Secure Sockets Layer (SSL) le kell tiltani, új és biztonságosabb alkalmazások protokollok engedélyezése, a megfelelőségi től értéke **június 30., 2018-as**. Ez a módosítás azt jelenti, hogy ha az Azure AD-szolgáltatásokhoz és PCI-DSS szabvány igényelnek, le kell tiltania a TLS 1.0. A TLS több verziói elérhetők, de a TLS 1.2 a legújabb verzió érhető el az Azure Active Directory Services. Erősen ajánlott közvetlenül a TLS 1.2-es ügyfél-kiszolgáló és a böngésző és a kiszolgáló kombinációk áthelyezése.

Elavult böngészők nem támogatja a TLS újabb verziók, például a TLS 1.2. A böngésző által támogatott mely verziói a TLS megtekintéséhez nyissa meg a [Qualys SSL Labs](https://www.ssllabs.com/) webhelyről, és kattintson a **teszteléséhez a böngésző**. Javasoljuk, hogy a böngésző legújabb verziójára frissítse és lehetőleg engedélyezése csak a TLS 1.2-es.

**A TLS 1.2-es, böngésző engedélyezése**

- **A Microsoft Edge és az Internet Explorer (mindkettő vannak beállítva az Internet Explorerben)**

    1. Válassza ki az Internet Explorer megnyitásához **eszközök** > **Internetbeállítások** > **speciális**.
    2. Az a **biztonsági** területen válassza **TLS 1.2 használatára**, majd válassza ki **OK**.
    3. Minden böngészőablakot bezárni, majd indítsa újra az Internet Explorerben. 

- **Google Chrome**

    1. Nyissa meg a Google Chrome, típus *chrome://settings/* a címsorában, majd nyomja le az **Enter**.
    2. Bontsa ki a **speciális** lehetőségeket, nyissa meg a **rendszer** területre, és válassza ki **nyissa meg a proxybeállítások**.
    3. Az a **internetes tulajdonságok** jelölje ki a **speciális** lépjen a **biztonsági** területen válassza **TLS 1.2 használatára**, majd válassza ki a  **OK**.
    4. Minden böngészőablakot bezárni, majd indítsa újra a Google Chrome-ban.

- **Mozilla Firefox**

    1. Nyissa meg a Firefox, típus *kapcsolatos: config* címsorában, és nyomja le az **Enter**.
    2. Keresse meg az érvényességi *TLS*, majd válassza ki a **security.tls.version.max** bejegyzés.
    3. Állítsa az értékét **3** kényszerítése a böngészőben, hogy a TLS 1.2-es verzió használatával, és válassza **OK**.

        >[!NOTE]
        >Firefox 60.0 verzió támogatja a TLS 1.3-as, így a security.tls.version.max érték is megadható **4**.

    4. Minden böngészőablakot bezárni, majd indítsa újra a Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény – 2018. június

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** vállalati alkalmazások  
**A termék funkció:** 3. fél integrációja
 
2018 június tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 15 új alkalmazásokról:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [zene stabilizálódási](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [ Végponti biztonsági mentési](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh hálózatok](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho egy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [ Helyszíni SharePoint-](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [CX Suite fája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Nyilvános előzetes verzióban érhető el az Azure AD jelszóvédelem

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** Identity Protection  
**A termék funkció:** felhasználói hitelesítés

Segítségével az Azure AD jelszóvédelem kiiktathatja könnyen kitalálni a jelszót a környezetből. Mivel ezek a jelszavak segítséget nyújt egy jelszó megfelelő típusú támadás a biztonsági sérülés kockázata.

Pontosabban az Azure AD jelszóvédelem nyújt segítséget:

- A munkahelyi fiókok védelmét is az Azure AD-ben és a Windows Server Active Directory (AD). 
- Leállítja a felhasználók listáját a leggyakrabban használt jelszavak több mint 500, és több mint 1 millió karakter helyettesítő változata ezeket a jelszavakat a jelszavak használatával.
- Felügyelheti az Azure AD-jelszó védelmi egyetlen helyről az Azure AD-portálon is az Azure ad és helyszíni Windows Server AD.

Az Azure AD jelszóvédelem kapcsolatos további információkért lásd: [rossz jelszavak, a szervezet számára](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Új "az összes vendégek" feltételes hozzáférési házirend sablon használati feltételek (feltételek) létrehozása során létrehozott

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** használati feltételek  
**A termék funkció:** Cégirányítási

A használati feltételek (feltételek) elkészítése során egy új feltételes hozzáférési szabályzat sablont is létrejön a "minden Vendég" és "minden alkalmazás". Az új csoportházirend-sablon létrehozásának és érvényesítési folyamat egyszerűsítésével vendégek újonnan létrehozott használati feltételek vonatkoznak.

További információkért lásd: [Azure Active Directory használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Új "egyéni" feltételes hozzáférési szabályzat sablon létrehozása a használati feltételek (feltételek) létrehozása közben

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** használati feltételek  
**A termék funkció:** Cégirányítási

A használati feltételek (feltételek) elkészítése során egy új "egyéni" feltételes hozzáférési szabályzat sablont is létrejön. Az új csoportházirend-sablon lehetővé teszi a használati feltételek létrehozása, és azonnal folytassa a feltételes hozzáférési szabályzat létrehozása panelen anélkül, hogy manuálisan keresse meg a portálon keresztül.

További információkért lásd: [Azure Active Directory használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Üzembe helyezése az Azure multi-factor Authentication az új és átfogó útmutatást

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** más  
**A termék funkció:** Identitásbiztonság és -védelem
 
Kiadás új részletes útmutatást nyújt az Azure multi-factor Authentication (MFA) telepítése a szervezetben.

Az MFA üzembe helyezési útmutató megtekintéséhez nyissa meg a [identitás üzembe helyezési útmutatók](https://aka.ms/DeploymentPlans) adattárat a Githubon. Az üzembe helyezési útmutatók visszajelzést megadásához használja a [központi telepítési csomag visszajelzési űrlap](https://aka.ms/deploymentplanfeedback). Ha az üzembe helyezési útmutatók kapcsolatban bármilyen kérdése van, írjon nekünk az [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Az Azure AD delegált felügyeleti szerepkör a nyilvános előzetes verzióban érhető el

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** vállalati alkalmazások  
**A termék funkció:** hozzáférés-vezérlés

A rendszergazdák mostantól delegálhatja kezelési feladatokat a globális rendszergazdai szerepkör hozzárendelése nélkül. Az új szerepkörök és funkciók a következők:

- **Új standard szintű Azure AD felügyeleti szerepkörök:**

    - **Alkalmazás-rendszergazda.** Minden alkalmazást, beleértve a regisztráció, egyszeri bejelentkezési beállításainak, alkalmazás-hozzárendelések és a licencelés, alkalmazást proxybeállításokat és jóváhagyás minden aspektusa kezelhető képesek (kivéve az Azure AD-erőforrások).

    - **Felhőalkalmazás-rendszergazda.** Biztosít minden, az alkalmazás-rendszergazda funkcióit, kivéve az alkalmazásproxy, mert ez nem biztosít helyszíni hozzáférés.

    - **Alkalmazás fejlesztője.** Listázását teszi létrehozása alkalmazásregisztrációkat, akkor is, ha a **engedélyezése a felhasználók számára alkalmazások regisztrálása** beállítás ki van kapcsolva.

- **Tulajdonjog (alkalmazásonkénti regisztrációs és enterprise alkalmazás beállítása, a csoport tulajdonjogának folyamat hasonló:**
 
    - **Alkalmazásregisztráció tulajdonosa.** A saját tulajdonú alkalmazásregisztráció, beleértve az alkalmazás-jegyzékfájl, és további tulajdonosok hozzáadása minden aspektusa kezelhető szerepkörök.

    - **Vállalati alkalmazás tulajdonosa.** Szerepkörök a vállalati tulajdonban lévő alkalmazások, beleértve az egyszeri bejelentkezési beállításainak, alkalmazás-hozzárendelések és jóváhagyás számos szempontból kezelése (kivéve az Azure AD-erőforrások).

További információ a nyilvános előzetes verzióban: a [Azure ad-ben delegált felügyelet szerepkör a nyilvános előzetes verzióban érhető el!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blog. További információ a szerepkörökről és engedélyekről: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>2018. május

### <a name="expressroute-support-changes"></a>Az ExpressRoute-támogatás módosításai

**Típus:** tervezett változtatás  
**Szolgáltatási kategóriához:** hitelesítések (Bejelentkezések)  
**A termék funkció:** Platform  

A szoftver egy szolgáltatott szoftverként, például az Azure Active Directory (Azure AD) úgy tervezték, hogy leginkább végigkövetésével közvetlenül az internethez, anélkül, hogy az expressroute-on vagy bármely más személyes VPN-alagutat. Emiatt a **2018. augusztus 1**, hogy-környezetei már nem az ExpressRoute használatával az Azure nyilvános társviszony-létesítés Azure AD-szolgáltatások és a Microsoft társviszony-létesítés Azure Közösségek. Ez a változás által érintett szolgáltatások előfordulhat, hogy figyelje meg, hogy az Azure AD-forgalom fokozatosan pótlása ExpressRoute-ból az interneten.

Amíg tudjuk módosítani a támogatási azzal is tisztában van vannak továbbra is olyan helyzetekben, ahol szüksége lehet használ egy dedikált Kapcsolatcsoportok a hitelesítési forgalomhoz. Ez az oka Azure ad-ben továbbra is támogatja a bérlőnkénti IP-címtartomány korlátozások használata az ExpressRoute és a szolgáltatások már a Microsoft társviszony-létesítés az "Egyéb Office 365 Online szolgáltatások"-Közösség tagjaival. Ha a szolgáltatásokat érintő eseményekről, de az ExpressRoute van szüksége, tegye a következőket:

- **Ha Ön az Azure nyilvános társviszony-létesítés.** Ugrás a Microsoft társviszony-létesítés, és regisztráljon a **egyéb Office 365-szolgáltatások (12076:5100)** közösségi. Az Azure nyilvános társviszony-létesítésről Microsoft társviszony-létesítésre áthelyezéséről további információ: a [áthelyezni egy nyilvános társviszony-létesítés Microsoft társviszony-létesítésre](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) cikk.

- **Ha Ön a Microsoft társviszony-létesítés.** Regisztráljon a **egyéb Office 365 Online szolgáltatás (12076:5100)** közösségi. Útválasztási követelményeivel kapcsolatos további információkért tekintse meg a [támogatása BGP-Közösségek szakasz](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) az ExpressRoute útválasztási követelményei című cikkben.

Ha folytatja, dedikált Kapcsolatcsoportok használni kell, kommunikáljon a Microsoft Account team való használatához engedélyt kell a **egyéb Office 365 Online szolgáltatás (12076:5100)** közösségi. A MS Office-felügyelt felülvizsgáló Bizottság ellenőrzi-e ezeket a Kapcsolatcsoportok kell, és győződjön meg arról, hogy megszegéseinek műszaki tartja őket. Az Office 365-höz útvonalszűrők létrehozására tett kísérlet nem engedélyezett előfizetések hibaüzenetet fog kapni. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Felügyeleti forgatókönyvek a használati feltételek a Microsoft Graph API-k

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** használati feltételek  
**A termék funkció:** fejlesztői élmény
 
Lehetőségekkel bővült a Microsoft Graph API-k az Azure AD – használati felügyeleti működéséhez. Is tudja létesítése, frissítése és a használati feltételek objektum törlése.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Identitás-szolgáltatóként az Azure AD B2C az Azure AD több-bérlős végpont hozzáadása

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** B2C - felhasználói identitás kezelése  
**A termék funkció:** B2B és B2C-vel
 
Egyéni szabályzatok használatával most már hozzáadhat az Azure AD közös végpont identitásszolgáltatójaként az Azure AD B2C-ben. Ez lehetővé teszi, hogy az összes olyan jelentkezik be az alkalmazások az Azure AD-felhasználó bejegyzés hibaérzékeny pont. További információkért lásd: [Azure Active Directory B2C: felhasználók jelentkezzen be egy egyéni szabályzatok használatával több-bérlős Azure AD-identitásszolgáltató](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Belső URL-címeket használnak az alkalmazások bárhonnan elérheti az Azure AD-alkalmazásproxy és a saját alkalmazások bejelentkezési bővítménye

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** saját alkalmazások  
**A termék funkció:** egyszeri bejelentkezés
 
Felhasználók most már elérheti alkalmazások belső URL-címeket, még ha a vállalati hálózaton kívülről keresztül a saját alkalmazások biztonságos bejelentkezési bővítménye az Azure ad használatával. Ez minden olyan alkalmazással, amely bármely böngészőben, amelyen telepítve van hozzáférési Panel böngészőbővítményének használatánál az Azure AD-alkalmazásproxy használatával közzétett fog működni. Az URL-cím átirányítási funkció automatikusan engedélyezve lesz, miután egy felhasználó bejelentkezik a bővítményt. A bővítmény letölthető [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367), és [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Az Azure Active Directory - adatok Európában Európa-ügyfelek számára

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** más  
**A termék funkció:** GoLocal

Ügyfeleknek az Európai szükséges az Európai maradjon, és Európai adatközpontok értekezlet védelmében, és az Európai törvényi kívül nem replikálódnak. Ez [cikk](https://go.microsoft.com/fwlink/?linkid=872328) milyen azonosító információkat a vonatkozó részletes Európa tárolja, és adatokat részleteiért tárolt kívül az Európai adatközpontok biztosítanak. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Új felhasználók SaaS-alkalmazás Integrációk – 2018. május

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** Alkalmazáskiosztási  
**A termék funkció:** 3. fél integrációja
 
Azure ad-ben automatizálhatja a létrehozás, a karbantartással és a SaaS-alkalmazások, például a Dropbox, a Salesforce, ServiceNow vagy további felhasználói identitásokat eltávolítását teszi lehetővé. 2018 május hozzáadtuk a felhasználók a következő alkalmazások az Azure AD-alkalmazásgyűjtemény támogatása:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Alapkövei OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Minden alkalmazás, amely támogatja a felhasználók átadásának az Azure AD katalógusban listáját lásd: [ https://aka.ms/appstutorial ](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>A csoportok Azure AD hozzáférési felülvizsgálatok és alkalmazás-hozzáférés mostantól lehetőséget nyújt az ismétlődő értékelések

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** a hozzáférési felülvizsgálatok  
**A termék funkció:** Cégirányítási
 
Hozzáférési felülvizsgálat csoportok és alkalmazások már általánosan elérhető az Azure AD Premium P2 részeként.  A rendszergazdák tudják a hozzáférési felülvizsgálatok például havonta vagy negyedévente rendszeres időközönként automatikusan ismétlődni csoporttagságok és alkalmazás-hozzárendelések konfigurálása.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Az Azure Active Directory-naplók (bejelentkezési és hitelesítési) mostantól elérhetők az MS Graph használatával

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** jelentéskészítés  
**A termék funkció:** Monitorozás és jelentéskészítés
 
Az Azure AD tevékenységeket tartalmazó naplók, amely tartalmazza a bejelentkezések és auditnaplók, mostantól elérhetők az MS Graph használatával. A Microsoft közzétette a két végpontok keresztül az MS Graph ezek a naplók eléréséhez. Tekintse meg a [dokumentumok](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) az első lépések az Azure AD Reporting API programozás alapú hozzáférést. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Élmény és a egy szervezeti hagyja a B2B-érvényesítési fejlesztései

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** B2B  
**A termék funkció:** B2B és B2C-vel

**Csak az érvényesítési idő:** után B2B API-val vendégfelhasználóval megosztott erőforrás – nem kell egy különleges meghívás e-mailt küld. A legtöbb esetben a vendégfelhasználó az erőforrás eléréséhez, és megnyílik a beváltási élmény szerinti keresztül. Nincs több hatással miatt kimaradt e-maileket. Nincs több kell megadniuk a Vendég "Volt, érvényesítési hivatkozásra kattintva a rendszer akkor küld?". Ez azt jelenti, miután SPO a meghívó Managert használja – zavaros mellékleteket is rendelkezhet – belső és külső – bármely érvényesítési állapotát az összes felhasználó azonos kanonikus URL-CÍMÉT.

**A modern érvényesítési élmény:** képernyő érvényesítési kezdőlap nincs több felosztása. A modern a felhasználók látják az adatvédelmi nyilatkozat a meghívó szervezetet, felhasználói jóváhagyás, ugyanúgy, mint az ilyen harmadik felek alkalmazásaihoz.

**Vendégfelhasználók a szervezettel hagyhatja:** után a felhasználó kapcsolat szervezettel való felett van, akkor is önkiszolgáló a szervezetből. Nincs több történt a meghívó szervezeti admin "el kell távolítani", több ritikus támogatási jegyeket.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény – 2018. május

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** vállalati alkalmazások  
**A termék funkció:** 3. fél integrációja
 
2018 május tettünk elérhetővé összevonással 18 új alkalmazásokról, az alkalmazáskatalógusban támogatja:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty szabályozzák, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [a Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montázs Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [Arc-közzététel – egyszeri bejelentkezés ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [állományt](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial).

Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Az Azure Active Directory új részletes üzembe helyezési útmutatók

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** más  
**A termék funkció:** könyvtár
 
Új, részletes útmutató Azure Active Directory (Azure AD), beleértve az önkiszolgáló jelszó-visszaállítás (SSPR), üzembe helyezésével kapcsolatos egyszeri bejelentkezést (SSO), a feltételes hozzáférés (CA), -alkalmazásproxyval, a felhasználók átadásához, az Active Directory összevonási szolgáltatások (ADFS) Az átmenő hitelesítés (ESP), és az AD FS a Jelszókivonat-szinkronizálás (nál).

Az üzembe helyezési útmutatók megtekintéséhez nyissa meg a [identitás üzembe helyezési útmutatók](https://aka.ms/DeploymentPlans) adattárat a Githubon. Az üzembe helyezési útmutatók visszajelzést megadásához használja a [központi telepítési csomag visszajelzési űrlap](https://aka.ms/deploymentplanfeedback). Ha az üzembe helyezési útmutatók kapcsolatban bármilyen kérdése van, írjon nekünk az [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Vállalati alkalmazások keresése, – további alkalmazások betöltése

**Típus:** új szolgáltatás  
**Szolgáltatási kategóriához:** vállalati alkalmazások  
**A termék funkció:** egyszeri bejelentkezés
 
Ha nem találja az alkalmazás / egyszerű szolgáltatások? Hozzáadtunk további alkalmazásokat a vállalati alkalmazások betöltéséhez összes alkalmazások listáját. Alapértelmezés szerint 20 alkalmazás mutatjuk be. Rákattinthat, **Továbbiak betöltése** további alkalmazások megtekintéséhez. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Az aad Connect kiadása tartalmaz egy nyilvános előzetes verziója a pingfederate-tel, az integráció május fontos biztonsági frissítések, számos hibajavítást és új nagyszerű új hibaelhárítási eszközei. 

**Típus:** megváltozott funkció  
**Szolgáltatási kategóriához:** AD Connect  
**A termék funkció:** identitás-életciklus-felügyelet
 
Az aad Connect kiadása tartalmaz egy nyilvános előzetes verziója a pingfederate-tel, az integráció május fontos biztonsági frissítések, számos hibajavítást és új nagyszerű új hibaelhárítási eszközei. A kibocsátási megjegyzésekben talál [Itt](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Az Azure AD hozzáférési felülvizsgálatok: automatikus alkalmazása

**Típus:** megváltozott funkció  
**Szolgáltatási kategóriához:** a hozzáférési felülvizsgálatok  
**A termék funkció:** Cégirányítási

A hozzáférési felülvizsgálatok a csoportok és alkalmazások most már általánosan elérhetők az Azure AD Premium P2 részeként. Automatikusan a felülvizsgáló módosítások alkalmazásához, csoport vagy alkalmazás, a hozzáférési felülvizsgálatot követően a rendszergazdák konfigurálhatják. A rendszergazda azt is megadhatja, folyamatos hozzáférésre a felhasználó, mi történik, ha a felülvizsgálók nem válaszol, távolítsa el a hozzáférést, férjenek vagy rendszer ajánlásokat. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-responsemode-for-new-apps"></a>Azonosító-jogkivonatokat már nem adható vissza a lekérdezés response_mode használatával új alkalmazások esetében. 

**Típus:** megváltozott funkció  
**Szolgáltatási kategóriához:** hitelesítések (Bejelentkezések)  
**A termék funkció:** felhasználói hitelesítés
 
Be- és 2018. április 25. után létrehozott alkalmazások már nem igényelhetnek egy **id_token** használatával a **lekérdezés** response_mode.  Ekkor az Azure AD beágyazott OIDC előírásoknak, és segítséget nyújt az alkalmazások támadási felület csökkentése.  2018. április 25. előtt létrehozott alkalmazások nincs letiltva a használatával a **lekérdezés** együtt, egy response_type response_mode **id_token**.  A hiba, az aad-ből, id_token kérésekor **AADSTS70007: "query" érték nem támogatott a "response_mode" jogkivonat kérése során**.

A **töredék** és **form_post** response_modes továbbra is működni – Ha létrehozni új alkalmazás objektumok (például az alkalmazásproxy-használat), győződjön meg, hogy ezek response_modes egyikének használata előtt, hozhatnak létre egy Új alkalmazás.  

---