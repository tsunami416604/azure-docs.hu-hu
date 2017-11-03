---
title: "Az Azure Active Directory koncepció alkalmazástervezési megvalósítása |} Microsoft Docs"
description: "Vizsgálatát, és gyorsan végrehajthatja az identitás- és kezelési helyzetek"
services: active-directory
keywords: "az Azure active directory, a forgatókönyv, a koncepció igazolása, PoC"
documentationcenter: 
author: dstefanMSFT
manager: asuthar
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 10877ee33ec04cf0d350417af59d598eab249aa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-implementation"></a>Az Azure Active Directory igazolása koncepció forgatókönyv: végrehajtása

## <a name="foundation---syncing-ad-to-azure-ad"></a>Foundation - AD-ből az Azure AD szinkronizálása 

A hibrid identitás a vállalati felhasználók, akiknek már van egy helyszíni címtárat a legtöbb alapját képezi. A cél szándékosan egy kevesebb alkalommal Itt a lehető a tényleges identitások és hozzáférések forgatókönyvek értékének megjelenítése. 

| Forgatókönyv | Építőelemek| 
| --- | --- |  
| [Kiterjesztése a felhőbe a helyszíni identitás](#extending-your-on-premises-identity-to-the-cloud) | [A címtár-szinkronizálás - Jelszókivonat-szinkronizálás](active-directory-playbook-building-blocks.md#directory-synchronization---password-hash-sync-phs---new-installation) <br/>**Megjegyzés:**: Ha már rendelkezik a DirSync/ADSync vagy az Azure AD Connect korábbi verzióiban, ez a lépés nem kötelező megadni. Bizonyos esetekben az útmutató az Azure AD Connect újabb verzióra lehet szükség.  <br/>[Védjegyek](active-directory-playbook-building-blocks.md#branding) | 
| [Csoportok használata az Azure AD-licencek hozzárendelése](#assigning-azure-ad-licenses-using-groups) | [Csoport alapú licencelés](active-directory-playbook-building-blocks.md#group-based-licensing) |


### <a name="extending-your-on-premises-identity-to-the-cloud"></a>Kiterjesztése a felhőbe a helyszíni identitás 

1. Bob a Contoso Active Directory rendszergazda. Ezután lekérdezi a követelmény identitás engedélyezése a felhasználók szolgáltatásként. Miután végrehajtása az Azure AD Connect varázsló, a felhasználók megcélzása érhető el a felhőben identitását. 
2. Bob Susie, egyik tároló felhasználó férhessen hozzá az Azure Active Directory-hozzáférési panelre, és győződjön meg arról, hogy ő hitelesítheti kéri. Susie megtekinti egy vállalati arculattal ellátott bejelentkezési oldal és egy üres hozzáférési panel, amely készen áll a jövőbeli alkalmazás-hozzáférés engedélyezése.

### <a name="assigning-azure-ad-licenses-using-groups"></a>Csoportok használata az Azure AD-licencek hozzárendelése 

1. Bob a Azure AD globális rendszergazda, és szeretne lefoglalni az Azure AD licencet a felhasználók adott csoportja számára a kezdeti Bevezetés az Azure AD részeként.
2. Bob a próbaüzem felhasználóinak csoportot hoz létre. 
3. Bob a licenceket rendel hozzá a csoporthoz
4. Susie, egy információkkal dolgozó szakemberek saját munkakörök részeként kerül a biztonsági csoport
5. Némi várakozás után Susie hozzáfér az Azure AD premium licenccel. Ezzel a lépéssel engedélyezi a Koncepció eseteket később.

## <a name="theme---lots-of-apps-one-identity"></a>Téma - alkalmazások rengeteg, egy identitás

| Forgatókönyv | Építőelemek| 
| --- | --- |  
| [Integrálhatja SaaS-alkalmazásokhoz - összevont egyszeri bejelentkezés](#integrate-saas-applications---federated-sso) | [SaaS összevont egyszeri bejelentkezés konfigurálása](active-directory-playbook-building-blocks.md#saas-federated-sso-configuration) <br/>[Csoportok - delegált tulajdonosa](active-directory-playbook-building-blocks.md#groups---delegated-ownership) |
| [Integrálhatja SaaS-alkalmazásokhoz - jelszó egyszeri bejelentkezés](#integrate-saas-applications---password-sso) | [SaaS-jelszó egyszeri bejelentkezés konfigurálása](active-directory-playbook-building-blocks.md#saas-password-sso-configuration) |
| [Egyszeri Bejelentkezéssel és identitás életciklus-események](#sso-and-identity-lifecycle-events) | [SaaS és identitás életciklusa](active-directory-playbook-building-blocks.md#saas-and-identity-lifecycle) |
| [Biztonságos hozzáférés a megosztott fiókok](#secure-access-to-shared-accounts) | [Megosztott SaaS konfigurációja](active-directory-playbook-building-blocks.md#saas-shared-accounts-configuration) |
| [A helyszíni alkalmazások biztonságos távoli hozzáférést](#secure-remote-access-to-on-premises-applications) | [Alkalmazás proxykonfigurációt](active-directory-playbook-building-blocks.md#app-proxy-configuration) |
| [Az Azure AD-LDAP identitások szinkronizálása](#synchronize-ldap-identities-to-azure-ad) |  [Általános LDAP-összekötő konfigurálása](active-directory-playbook-building-blocks.md#generic-ldap-connector-configuration) |

### <a name="integrate-saas-applications---federated-sso"></a>Integrálhatja SaaS-alkalmazásokhoz - összevont egyszeri bejelentkezés 

1. Bob a Azure AD globális rendszergazda, és kérelmet kap a marketingosztály a ServiceNow-példányhoz való hozzáférés engedélyezéséhez. Bob a részletes oktatóanyag az Azure AD dokumentációjában talál és azt követő, és a felhasználók számára az alkalmazás-hozzárendelés gazdakiszolgálója Kevin, Marketing csapat vezetője. 
2. Kevin ServiceNow jogosultságok tulajdonosaként jelentkezik be, és Susie rendel hozzá az alkalmazást. Kevin is észleli, hogy Susie tartozó profil automatikusan ServiceNow jött létre
3. Susie az Infomunkás a marketingosztály. Ő jelentkezik be, az azure AD, és ő hozzá van rendelve minden SaaS-alkalmazásokhoz myapps portálon talál. Ott így zökkenőmentesen megkapja a ServiceNow elérésére.
4. A marketingosztály kívánja naplózni a ServiceNow hozzáférő. Bob tölt le egy tevékenységgel kapcsolatos jelentés, és azt Kevin megosztja e-mailek keresztül.  

### <a name="sso-and-identity-lifecycle-events"></a>Egyszeri Bejelentkezéssel és identitás életciklus-események

1. Susie tart egy hagyja az hiánya, és a vállalati házirenddel a helyszíni AD-fiókot az ideiglenes le van tiltva. Susie most nem tud bejelentkezni az Azure AD, és ezért nem tud hozzáférni a ServiceNow. 
2. Susie oldalirányú áthelyezésre hajt végre a Marketing értékesítési. Kevin saját hozzáférés ServiceNow eltávolítja. Susie jelentkezik be az azure ad myapps, és azt nem látja a ServiceNow Csempén. 10 perc elteltével Kevin megerősíti, hogy Susie fiók le lett tiltva, a ServiceNow felügyeleti konzolról.

### <a name="integrate-saas-applications---password-sso"></a>Integrálhatja SaaS-alkalmazásokhoz - jelszó egyszeri bejelentkezés

1. Bob Atlassian HipChat elérésére konfigurálja. HipChat jelszó SSO-integrációval és hozzáférést biztosít Susie rendelkezik
2. Susie jelentkezik be a myapps portálra, és megtekinti a ő tölti le az Azure AD Internet Explorer böngésző bővítmény letöltésére mutató hivatkozás
3. Gomb megnyomásakor, ő lekérdezi az HipChat felhasználónévvel és jelszóval hitelesítő adatokat kér. Ez egy egyszeri művelet, és azt befejezése után ugyanúgy HipChat hozzáféréssel rendelkezik
4. Később, néhány nap múlva Susie myapps portál megnyitja és HipChat újra kattint. Ez alkalommal körülbelül így megkapja zökkenőmentes hozzáférést
5. Kevin, az HipChat alkalmazás tulajdonosa szeretné naplózni, akik érhető el az alkalmazást. Bob tölti le a jelentést, és azt Kevin megosztja e-mailek keresztül. 

### <a name="secure-access-to-shared-accounts"></a>Biztonságos hozzáférés a megosztott fiókok 

1. Bob van az értékesítési csapat tagjai számára a megosztott Twitter-leíró védelmét biztosítja. Ezután SSO alkalmazásként Twitter hozzáadja, és hozzárendeli az értékesítési csapat a biztonsági csoport. A megosztott fiókhoz megadott hitelesítő adatokat, és ezután Megadja azt a rendszerben. 
2. Megosztás a Twitteren hitelesítő adatok már nem megbízható ismerete, hogy több ember miatt. Bob lehetővé teszi, hogy a Twitter jelszó automatikus kapcsolódó kulcsváltást.
3. Susie, az értékesítési csapat egyik tagja jelentkezik be a myapps portálra, és megtekinti az Azure AD Internet Explorer böngésző bővítmény letöltésére mutató hivatkozás. Telepítené.
4. Gomb megnyomásakor ő eléréséhez közvetlenül Twitter. Ezzel nem tudja a jelszót.
5. Arnold emellett az értékesítők részét képezi. Ugyanazt a felhasználói élményt Susie, 3 – 4. lépésben rendelkezik
6. Az értékesítési részleg szeretné naplózni ki fért hozzá a Twitteren. Bob tölt le egy tevékenységgel kapcsolatos jelentés, és azt Kevin megosztja e-mailek keresztül. 

### <a name="secure-remote-access-to-on-premises-applications"></a>A helyszíni alkalmazások biztonságos távoli hozzáférést

1. Bob, az Azure AD globális rendszergazda ágyazott számos kérelmek alkalmazottak számos hasznos a helyszíni erőforrások eléréséhez, például a költségek alkalmazás, amikor távolról dolgoznak. Ezt követi a [alkalmazásproxy dokumentáció](active-directory-application-proxy-enable.md) feltelepít egy összekötőt és alkalmazásproxy alkalmazásként is közzéteheti. 
2. Bob megosztása Susie, egy távoli hozzáférésre van szüksége az alkalmazottak a költségek alkalmazás külső URL-CÍMÉT. Ő fér hozzá a hivatkozásra, és után AAD hitelesítést, ő a költségek alkalmazáshoz hozzáférhetnek, és továbbra is képesek legyenek hatékonyan dolgozni távoli közben. 
3. Bob majd továbbra is ugyanazt az eljárást használják, és adjon hozzáférést a felhasználók igény szerint további helyszíni alkalmazások közzététele. Feltételes hozzáférés és a többtényezős hitelesítést a több érzékeny alkalmazások, amelyek ezután tesz közzé, annak biztosítása érdekében további biztonsági adja.

### <a name="synchronize-ldap-identities-to-azure-ad"></a>Az Azure AD-LDAP identitások szinkronizálása

1. Bob vállalatnak összetett identitás-infrastruktúra. A felhasználók többsége karbantartása belül a Windows Server Active Directory tartományi szolgáltatások (ADDS). Egy részüket HR rendszer belül Active Directory Lightweight Directory Services (ADLDS) kezeli.
2. Bob van biztosítja az összes felhasználó számára (is ezek nem szerepelnek ADDS) SaaS-alkalmazásokhoz való hozzáférés engedélyezése.
3. Bob általános LDAP-összekötő segítségével szerez adatokat a ADLDS az Azure AD Connectben konfigurálja.
4. Bob szinkronizálási szabályokat hoz létre, így a felhasználók LDAP fel van töltve a Metaverzumba, az Azure AD
5. LDAP felhasználó hozzáfér az SaaS alkalmazás használatával alatt Susie szinkronizált identitás



> [!IMPORTANT] 
> Ez a beállítás egy speciális igénylő FIM vagy MIM bizonyos fokú ismeretét. Ha használja termelési, javasoljuk, hogy ez a konfiguráció kapcsolatos kérdéseket halad át [Premier támogatási](https://support.microsoft.com/premier).



## <a name="theme---increase-your-security"></a>Téma - a biztonság növelése 

| Forgatókönyv | Építőelemek| 
| --- | --- |  
| [Biztonságos rendszergazdai fiók hozzáféréssel](#secure-administrator-account-access) | [Az Azure MFA telefonos hívások](active-directory-playbook-building-blocks.md#azure-multi-factor-authentication-with-phone-calls) |
| [Az alkalmazások biztonságos hozzáférést](#secure-access-to-applications) | [Feltételes hozzáférés a Szolgáltatottszoftver-alkalmazáshoz](active-directory-playbook-building-blocks.md#mfa-conditional-access-for-saas-applications) |
| [Csak a felügyelet engedélyezése](#enable-just-in-time-jit-administration) | [Privileged Identity Management](active-directory-playbook-building-blocks.md#privileged-identity-management-pim) |
| [A kockázat alapján identitások védelme](#protect-identities-based-on-risk) | [Kockázati események felderítéséhez](active-directory-playbook-building-blocks.md#discovering-risk-events) <br/>[Bejelentkezés a kockázat házirendek](active-directory-playbook-building-blocks.md#deploying-sign-in-risk-policies) |
| [A jelszavak a Tanúsítványalapú hitelesítés használata nélkül végezzen hitelesítést](#authenticate-without-passwords-using-certificate-based-authentication) | [A Tanúsítványalapú hitelesítés konfigurálása](active-directory-playbook-building-blocks.md#configuring-certificate-based-authentication)

### <a name="secure-administrator-account-access"></a>Biztonságos rendszergazdai fiók hozzáféréssel

1. Bob az Azure AD globális rendszergazda. A szolgáltatás társadminisztrátoraként Stuart rendelkezik talált. 
2. Bob meghatározott biztonsági előírások javítása érdekében mindig megkövetelő Stuart tartozó fiók konfigurálása
3. Az Azure-portálon, és a megjegyzések, amelyek a bejelentkezési folytatja a telefonszám regisztrálni kell naplók Stuart
4. Stuart a későbbi bejelentkezések során ezzel védetté tette a multi-factor Authentication hitelesítéshez, és most kapjuk a telefonhívás, a személyazonosságát.

### <a name="secure-access-to-applications"></a>Biztonságos hozzáférés a alkalmazások

1. Kevin ServiceNow üzleti tulajdonosa. A vállalat most szeretne azoknak a felhasználóknak a többtényezős hitelesítéssel bejelentkezés a vállalati hálózaton kívülről való hozzáféréskor.
2. Bob, az Azure AD globális rendszergazda, a feltételes hozzáférési házirend hozzáadása a ServiceNow alkalmazást, hogy engedélyezi az MFA Használatát a külső hozzáférés
3. Susie, az információkkal dolgozó szakember jelentkezik be a alkalmazások portál, és a ServiceNow csempére kattint. Ő most megkérdőjelezhető MFA-val.

### <a name="enable-just-in-time-jit-administration"></a>Csak a szerinti (JIT) felügyelet engedélyezése

1. Bob és Stuart az Azure AD globális rendszergazda. A felügyeleti szerepköre JIT elérésének lehetővé tétele és továbbra is rögzíti az használatát, a kiemelt szerepköröket a kívánják.
2. Bob lehetővé teszi a PIM az Azure AD-bérlő és a biztonsági rendszergazdája lesz. Ezután módosítja saját maga és Stuart tartozó globális rendszergazdai szerepkör tagsági állandó támogatható.
3. Bob és Stuart most kell az Azure portálon keresztül a szerepkör aktiválása az Azure AD-alapú konfigurációs módosítások végrehajtása előtt. 

### <a name="protect-identities-based-on-risk"></a>A kockázat alapján identitások védelme 

1. Susie, az Infomunkás kísérel meg a tor böngészőből van bejelentkezve. 
2. Bob ellenőrzi az Azure AD identity protection-irányítópult és névtelen IP-címet a Susie tartozó bejelentkezési látja. A biztonsági csoport szeretne ilyen fér hozzá felhasználókat a multi-factor Authentication szolgáltatás ellenőrző kérdés
3. Bob lehetővé teszi, hogy az Azure AD Identity Protection-házirendet arra MFA, közepes vagy magasabb kockázati események
4. Idő kerül, és Susie jelentkezik be a Tor böngészőből újra. Most, hogy jelenik meg az MFA-kérdést

### <a name="authenticate-without-passwords-using-certificate-based-authentication"></a>A jelszavak a Tanúsítványalapú hitelesítés használata nélkül végezzen hitelesítést

1. Bob globális rendszergazda a pénzügyi intézménynél, amely tiltja a jelszavak használatát egy hitelesítési tényezőként az alkalmazásuk kezelésére.
2. Bob lehetővé teszi, és érvényesíti a az AD FS és az Azure AD a tanúsítványhitelesítés
3. Susie elérésekor az alkalmazás kéri, hogy hitelesítést a tanúsítvány használatával

## <a name="theme---scale-with-self-service"></a>Téma - méretezéssel önkiszolgáló

| Forgatókönyv | Építőelemek| 
| --- | --- |  
| [Önkiszolgáló jelszóváltoztatás](#self-service-password-reset) | [Önkiszolgáló jelszóváltoztatás](active-directory-playbook-building-blocks.md#self-service-password-reset) |
| [Önkiszolgáló alkalmazások eléréséhez](#self-service-access-to-applications) | [Önkiszolgáló alkalmazások eléréséhez](active-directory-playbook-building-blocks.md#self-service-access-to-application-management) |

### <a name="self-service-password-reset"></a>Önkiszolgáló jelszóváltoztatás 

1. Bob a Azure AD globális rendszergazda, és lehetővé teszi, hogy az önkiszolgáló jelszó-kezelő felhasználók, beleértve a Susie egy részét. 
2. Susie naplókat a myapps portálon, és ellenőrizze, hogy egy üzenetet, amely a saját biztonsági információkat a jövőbeli jelszó alaphelyzetbe állítása események.
3. Előre néhány nap múlva, Susie elfelejti a jelszavát, majd visszaállítja az Azure AD portálon keresztül

### <a name="self-service-access-to-applications"></a>Önkiszolgáló alkalmazások eléréséhez 

1. Kevin ServiceNow üzleti tulajdonosa. Azt szeretné, hogy a felhasználók "előfizetés" azt az igény szerinti, ahelyett, hogy hozzáadják őket egyszerre
2. Bob, az Azure AD globális rendszergazda, módosítja a ServiceNow alkalmazás önkiszolgáló naplózásának engedélyezése
3. Susie, az információkkal dolgozó szakember jelentkezik be a alkalmazások portál és a "További alkalmazások felvétele" gombra kattint, és a ServiceNow látnak az ajánlott alkalmazások közül. Ezután ő Visszanavigál a alkalmazások portál, és tekintse meg a ServiceNow alkalmazás.

[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]