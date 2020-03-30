---
title: A tevékenységjelentés segítségével helyezze át az AD FS-alkalmazásokat az Azure Active Directoryba | Microsoft Dokumentumok"
description: Az Active Directory összevonási szolgáltatások (AD FS) alkalmazástevékenység-jelentés lehetővé teszi az alkalmazások gyors áttelepítését az AD FS-ről az Azure Active Directoryba (Azure AD). Ez az AD FS-áttelepítési eszköz azonosítja az Azure AD-vel való kompatibilitást, és migrációs útmutatást ad.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333e440fdd5f5062dda45fb12a83543c63e66c04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978036"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Alkalmazások áttelepítése az Azure AD-be az AD FS-alkalmazástevékenység-jelentés (előzetes verzió) használatával

Számos szervezet használja az Active Directory összevonási szolgáltatásokat (AD FS) a felhőalapú alkalmazásokegyszeri bejelentkezéséhez. Az AD FS-alkalmazások Azure AD-be való áthelyezésének jelentős előnyei vannak a hitelesítéshez, különösen a költségkezelés, a kockázatkezelés, a termelékenység, a megfelelőség és a cégirányítási szempont tekintetében. De megértése, hogy mely alkalmazások kompatibilisek az Azure AD és az egyes áttelepítési lépések azonosítása időigényes lehet.

Az AD FS-alkalmazás tevékenységjelentés (előzetes verzió) az Azure Portalon lehetővé teszi, hogy gyorsan azonosítani, amely az alkalmazások képesek áttelepíteni az Azure AD-be. Felméri az összes AD FS-alkalmazást az Azure AD-vel való kompatibilitás szempontjából, ellenőrzi az esetleges problémákat, és útmutatást ad az egyes alkalmazások áttelepítésre való előkészítéséhez. Az AD FS alkalmazástevékenység-jelentéssel a következőket teheti:

* **Fedezze fel az AD FS-alkalmazásokat, és hatóköre az áttelepítést.** Az AD FS-alkalmazástevékenység-jelentés felsorolja a szervezet összes AD FS-alkalmazását, és jelzi, hogy készen állnak az Azure AD-be való áttelepítésre.
* **Az áttelepítési alkalmazások fontossági sorrendbe állása.** Az alkalmazásba az elmúlt 1, 7 vagy 30 nap során bejelentkezett egyedi felhasználók számának leküzdése az alkalmazás áttelepítésének kritikusságának vagy kockázatának meghatározásához.
* **Futtassa az áttelepítési teszteket, és javítsa ki a problémákat.** A jelentéskészítő szolgáltatás automatikusan futtatja a teszteket annak megállapítására, hogy egy alkalmazás készen áll-e az áttelepítésre. Az eredmények az AD FS alkalmazástevékenység-jelentésben áttelepítési állapotként jelennek meg. Ha potenciális áttelepítési problémákat talál, konkrét útmutatást kap a problémák kezeléséhez.

Az AD FS-alkalmazástevékenység-adatok a következő rendszergazdai szerepkörök bármelyikéhez hozzárendelt felhasználók számára érhetők el: globális rendszergazda, jelentésolvasó, biztonsági olvasó, alkalmazás-rendszergazda vagy felhőalkalmazás-rendszergazda.

## <a name="prerequisites"></a>Előfeltételek

* A szervezetnek jelenleg az AD FS-t kell használnia az alkalmazások eléréséhez.
* Az Azure AD Connect Health engedélyezni kell az Azure AD-bérlőben.
   * [További információ az Azure AD Connect Állapotáról](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Az Azure AD Connect Health beállításának első lépései](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Az áttelepíthető AD FS-alkalmazások felderítése 

Az AD FS-alkalmazástevékenység-jelentés az Azure AD **usage & insights** jelentés alatt érhető el az Azure Portalon. Az AD FS alkalmazástevékenység-jelentés elemzi az egyes AD FS-alkalmazásokat, és megállapítja, hogy áttelepíthető-e a hogy van, vagy szükség van-e további felülvizsgálatra. 

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) egy rendszergazdai szerepkörrel, amely hozzáfér az AD FS-alkalmazástevékenység-adatokhoz (globális rendszergazda, jelentésolvasó, biztonsági olvasó, alkalmazás-rendszergazda vagy a felhőalkalmazás-rendszergazda).

2. Válassza az **Azure Active Directory**, majd a Vállalati **alkalmazások**lehetőséget.

3. A **Tevékenység csoportban**válassza **a Használati & elemzési adatok (előzetes verzió)** lehetőséget, majd az **AD FS-alkalmazástevékenység** lehetőséget választva nyissa meg a szervezetösszes AD FS-alkalmazáslistáját.

   ![AD FS alkalmazástevékenység](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Az AD FS alkalmazástevékenység-listájában szereplő minden egyes alkalmazás esetében tekintse meg az **Áttelepítés állapotát:**

   * **Az áttelepítésre kész** azt jelenti, hogy az AD FS-alkalmazás konfigurációja teljes mértékben támogatott az Azure AD-ben, és ugyanúgy áttelepíthető, ahogy van.

   * **Szükségleti felülvizsgálat** azt jelenti, hogy az alkalmazás egyes beállításait át lehet telepíteni az Azure AD-be, de át kell tekintenie a beállításokat, amelyek nem telepíthetők át,as-is.

   * **További szükséges lépések** azt jelenti, hogy az Azure AD nem támogatja az alkalmazás egyes beállításait, így az alkalmazás nem telepíthető át a jelenlegi állapotában.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Áttelepítési kérelem készenlétének kiértékelése 

1. Az AD FS alkalmazástevékenység-listájában kattintson az állapotra az **Áttelepítés állapota** oszlopban az áttelepítés részleteinek megnyitásához. Megjelenik az átadott konfigurációs tesztek összegzése, valamint az esetleges áttelepítési problémák.

   ![A migrálás részletei](media/migrate-adfs-application-activity/migration-details.png)

2. Kattintson egy üzenetre az áttelepítési szabályok további részleteinek megnyitásához. A tesztelt tulajdonságok teljes listáját az [AD FS alkalmazáskonfigurációs tesztek](#ad-fs-application-configuration-tests) táblázatában talál.

   ![Áttelepítési szabály részletei](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS alkalmazás konfigurációs tesztjei

Az alábbi táblázat az AD FS-alkalmazásokon végrehajtott összes konfigurációs tesztet sorolja fel.

|Eredmény  |Áthaladás/Figyelmeztetés/Sikertelen  |Leírás  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> Az AdditionalAuthentication hitelesítéshez legalább egy nem áttelepíthető szabály észlelhető.       | Át/Figyelmeztetés          | A függő entitás rendelkezik a többtényezős hitelesítésre (MFA) vonatkozó rákérdezési szabályokkal. Az Azure AD-re való áttéréshez fordítsa le ezeket a szabályokat feltételes hozzáférési szabályzatokba. Ha helyszíni mfa-t használ, azt javasoljuk, hogy helyezze át az Azure MFA-ra. [További információ a feltételes hozzáférésről.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> A függő entitás AdditionalWSFedEndpoint értéke igaz.       | Sikeres/sikertelen          | Az AD FS függő entitása több WS-Fed helyességi végpontot engedélyez.Jelenleg az Azure AD csak egyet támogat.Ha olyan forgatókönyve van, amelyben ez az eredmény blokkolja az [áttelepítést, tudassa velünk.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> A függő entitás beállította az AllowedAuthenticationClassReferences metódust.       | Sikeres/sikertelen          | Az AD FS ezen beállításával megadhatja, hogy az alkalmazás csak bizonyos hitelesítési típusok engedélyezésére legyen-e beállítva. Ezt a funkciót feltételes hozzáférés használata ajánlott a feltételhez való hozzáférés használata. Ha olyan forgatókönyve van, amelyben ez az eredmény blokkolja az [áttelepítést, tudassa velünk.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)  [További információ a feltételes hozzáférésről.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | Sikeres/sikertelen          | Az AD FS ezen beállításával megadhatja, hogy az alkalmazás úgy van-e beállítva, hogy figyelmen kívül hagyja az SSO-cookie-kat, és **mindig kéri a hitelesítést.** Az Azure AD-ben a hitelesítési munkamenet feltételes hozzáférési szabályzatok használatával kezelheti a hasonló viselkedést. [További információ a hitelesítési munkamenet-kezelés feltételes hozzáféréssel történő konfigurálásáról.](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)          |
|Teszt-ADFSRPAutoUpdateEnabled <br> A függő entitás automatikus frissítése true értékre van állítva       | Át/Figyelmeztetés          | Az AD FS ezen beállításával megadhatja, hogy az AD FS úgy legyen-e beállítva, hogy az összevonási metaadatokon belüli módosítások alapján automatikusan frissítse az alkalmazást. Az Azure AD nem támogatja ezt ma, de nem blokkolja az alkalmazás áttelepítését az Azure AD-be.           |
|Test-ADFSRPclaimsProviderName <br> A függő entitás több jogcímszolgáltatóval rendelkezik       | Sikeres/sikertelen          | Az AD FS ezen beállítása felhívja azokat az identitásszolgáltatókat, amelyektől a függő entitás jogcímeket fogad. Az Azure AD-ben engedélyezheti a külső együttműködést az Azure AD B2B használatával. [További információ az Azure AD B2B-ről.](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)          |
|Test-ADFSRPDelegationAuthorizationRules      | Sikeres/sikertelen          | Az alkalmazás egyéni delegálási engedélyezési szabályokkal rendelkezik. Ez egy WS-Trust koncepció, amelyet az Azure AD modern hitelesítési protokollok, például openId connect és OAuth 2.0 használatával támogat. [További információ a Microsoft Identity Platformról.](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)          |
|Test-ADFSRPImpersonationAuthorizationRules       | Át/Figyelmeztetés          | Az alkalmazás egyéni megszemélyesítési engedélyezési szabályokkal rendelkezik.Ez egy WS-Trust koncepció, amelyet az Azure AD modern hitelesítési protokollok, például openId connect és OAuth 2.0 használatával támogat. [További információ a Microsoft Identity Platformról.](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)          |
|Test-ADFSRPIssuanceAuthorizationRules <br> A rendszer legalább egy nem áttelepíthető szabályt észlelt az IssuanceAuthorization számára.       | Át/Figyelmeztetés          | Az alkalmazás rendelkezik az AD FS-ben definiált egyéni kiállítási engedélyezési szabályokkal.Az Azure AD támogatja ezt a funkciót az Azure AD feltételes hozzáféréssel. [További információ a feltételes hozzáférésről.](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) <br> Az alkalmazáshoz való hozzáférést az alkalmazáshoz rendelt felhasználó vagy csoportok is korlátozhatja. [További információ arról, hogy miként rendeli hozzá a felhasználókat és csoportokat az alkalmazások eléréséhez.](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)            |
|Test-ADFSRPIssuancetransformRules <br> Az IssuanceTransform legalább egy nem áttelepíthető szabályt észlelt.       | Át/Figyelmeztetés          | Az alkalmazás rendelkezik az AD FS-ben definiált egyéni kiállításátalakítási szabályokkal. Az Azure AD támogatja a jogkivonatban kiadott jogcímek testreszabását. További információ: [Az SAML-jogkivonatban vállalati alkalmazásokhoz kiadott jogcímek testreszabása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)című témakörben olvashat.           |
|Teszt-ADFSRPMonitoringEnabled <br> A függő entitás MonitoringEnabled értéke true.       | Át/Figyelmeztetés          | Az AD FS ezen beállításával megadhatja, hogy az AD FS úgy legyen-e beállítva, hogy az összevonási metaadatokon belüli módosítások alapján automatikusan frissítse az alkalmazást. Az Azure AD nem támogatja ezt ma, de nem blokkolja az alkalmazás áttelepítését az Azure AD-be.           |
|Teszt-ADFSRPNotBeforeskew <br> NotBeforeSkewCheckResult előtt      | Át/Figyelmeztetés          | Az AD FS lehetővé teszi az időeltérést a NotBefore és notOnOrAfter idők alapján az SAML tokenben. Az Azure AD alapértelmezés szerint automatikusan kezeli ezt.          |
|Test-ADFSRPRequestMFAFromclaimsProviders <br> A függő entitás RequestMFAFromClaimsProviders értéke igaz.       | Át/Figyelmeztetés          | Ez a beállítás az AD FS határozza meg a viselkedését MFA, ha a felhasználó érkezik egy másik jogcímszolgáltató. Az Azure AD-ben engedélyezheti a külső együttműködést az Azure AD B2B használatával. Ezután feltételes hozzáférési házirendeket alkalmazhat a vendéghozzáférés védelmére. További információ az [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) és [a feltételes hozzáférésről.](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)          |
|Test-ADFSRPSignedSamlRequestsRequired <br> A függő entitás signedSamlRequestsRequired értéke igaz       | Sikeres/sikertelen          | Az alkalmazás az AD FS-ben úgy van konfigurálva, hogy ellenőrizze az SAML-kérelemben lévő aláírást. Az Azure AD elfogadja az aláírt SAML-kérelmet; azonban nem ellenőrzi az aláírást. Az Azure AD különböző módszerekkel rendelkezik a rosszindulatú hívások elleni védelemre. Az Azure AD például az alkalmazásban konfigurált válasz URL-címeket használja az SAML-kérelem érvényesítéséhez. Az Azure AD csak egy jogkivonatot küld az alkalmazáshoz konfigurált URL-címek válaszküldéséhez. Ha olyan forgatókönyve van, amelyben ez az eredmény blokkolja az [áttelepítést, tudassa velünk.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Át/Figyelmeztetés         | Az alkalmazás egyéni jogkivonat-élettartamra van konfigurálva. Az AD FS alapértelmezett beállítása egy óra.Az Azure AD támogatja ezt a funkciót a feltételes hozzáférés használatával. További információ: [A hitelesítési munkamenet-kezelés konfigurálása feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)című témakörben olvashat.          |
|A Függő entitás titkosításra van beállítva. Ezt az Azure AD támogatja       | Át          | Az Azure AD-vel titkosíthatja az alkalmazásnak küldött jogkivonatot. További információ: [Azure AD SAML token titkosítás konfigurálása.](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)          |
|EncryptedNameIdRequiredCheckResult      | Sikeres/sikertelen          | Az alkalmazás úgy van beállítva, hogy titkosítsa a nameID jogcímet az SAML-jogkivonatban.Az Azure AD-vel titkosíthatja az alkalmazásnak küldött teljes jogkivonatot.Az egyes jogcímek titkosítása még nem támogatott. További információ: [Azure AD SAML token titkosítás konfigurálása.](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)         |

## <a name="check-the-results-of-claim-rule-tests"></a>A jogcímszabály-tesztek eredményeinek ellenőrzése

Ha beállított egy jogcímszabályt az alkalmazáshoz az AD FS-ben, a felhasználói felület részletes elemzést biztosít az összes jogcímszabályhoz. Látni fogja, hogy mely jogcímszabályok helyezhetők át az Azure AD-be, és melyek további felülvizsgálatra szorulnak.

1. Az AD FS alkalmazástevékenység-listájában kattintson az állapotra az **Áttelepítés állapota** oszlopban az áttelepítés részleteinek megnyitásához. Megjelenik az átadott konfigurációs tesztek összegzése, valamint az esetleges áttelepítési problémák.

2. Az **Áttelepítési szabály részletei** lapon bontsa ki az eredményeket a lehetséges áttelepítési problémák részleteinek megjelenítéséhez, és további útmutatást kapjon. Az összes tesztelt jogcímszabály részletes listáját lásd alább a [Jogcímszabály-vizsgálatok eredményeinek ellenőrzése](#check-the-results-of-claim-rule-tests) táblázatban.

   Az alábbi példa az IssuanceTransform szabály áttelepítési szabályának részleteit mutatja be. Felsorolja a jogcím azon részeit, amelyeket felül kell vizsgálni és címezni kell, mielőtt áttelepítheti az alkalmazást az Azure AD-be.

   ![Az áttelepítési szabály további útmutatást tartalmaz](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Jogcímszabály-tesztek

Az alábbi táblázat az AD FS-alkalmazásokon végrehajtott összes jogcímszabály-tesztet sorolja fel.

|Tulajdonság  |Leírás  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | A feltételutasítás reguláris kifejezéseket használ annak kiértékelésére, hogy a jogcím megegyezik-e egy bizonyos mintával.Az Azure AD hasonló funkcióeléréséhez használhatja az előre definiált átalakítást, például az IfEmpty(), a StartWith(), contains() többek között. További információt a [Vállalati alkalmazások SAML-jogkivonatában kiadott jogcímek testreszabása című témakörben talál.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)          |
|UNSUPPORTED_CONDITION_CLASS      | A feltételutasítás több feltétellel rendelkezik, amelyeket ki kell értékelni a kiállítási utasítás futtatása előtt.Az Azure AD támogathatja ezt a funkciót a jogcím átalakítási funkcióival, ahol több jogcímértéket értékelhet ki.További információt a [Vállalati alkalmazások SAML-jogkivonatában kiadott jogcímek testreszabása című témakörben talál.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)          |
|UNSUPPORTED_RULE_TYPE      | A jogcímszabály nem ismerhető fel. A jogcímek Azure AD-ben való konfigurálásáról a [Vállalati alkalmazások SAML-jogkivonatában kiadott jogcímek testreszabása című témakörben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)talál további információt.          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | A feltételutasítás olyan kiállítót használ, amelyet az Azure AD nem támogat.Jelenleg az Azure AD nem forrás jogcímek az üzletekkülönböző, hogy az Active Directory vagy az Azure AD. Ha ez megakadályozza az alkalmazások áttelepítését az Azure AD-be, [tudassa velünk.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)         |
|UNSUPPORTED_CONDITION_FUNCTION      | A feltételutasítás összesítő függvényt használ egyetlen jogcím kiállításához vagy hozzáadásához, függetlenül az egyezések számától.Az Azure AD-ben kiértékelheti a felhasználó attribútumát, hogy eldöntse, milyen értéket használjon a jogcímhez többek között ifEmpty(), StartWith(), Contains() függvényekkel.További információt a [Vállalati alkalmazások SAML-jogkivonatában kiadott jogcímek testreszabása című témakörben talál.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)          |
|RESTRICTED_CLAIM_ISSUED      | A feltételutasítás egy azure AD-ben korlátozott jogcímet használ. Előfordulhat, hogy korlátozott jogcímet tud kiadni, de nem módosíthatja annak forrását, és nem alkalmazhat semmilyen átalakítást. További [információ: A jogcímekben kibocsátott jogcímekben az Azure AD-ben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)című témakörben talál.          |
|EXTERNAL_ATTRIBUTE_STORE      | A kiállítási utasítás az Active Directorytól eltérő attribútumtárolót használ. Jelenleg az Azure AD nem forrás jogcímek az üzletekkülönböző, hogy az Active Directory vagy az Azure AD. Ha ez az eredmény megakadályozza az alkalmazások áttelepítését az Azure AD-be, [tudassa velünk.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)          |
|UNSUPPORTED_ISSUANCE_CLASS      | A kiállítási utasítás az ADD segítségével adja hozzá a jogcímeket a bejövő jogcímkészlethez. Az Azure AD-ben ez lehet konfigurálni több jogcímátalakítások.További információt a [Vállalati alkalmazások SAML-jogkivonatában kiadott jogcímek testreszabása című témakörben talál.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | A kiállítási utasítás reguláris kifejezéseket használ a kibocsátott jogcím értékének átalakítására.Az Azure AD hasonló funkciók eléréséhez használhatja az előre definiált átalakítás, például a Extract(), Trim(), ToLower, többek között. További információt a [Vállalati alkalmazások SAML-jogkivonatában kiadott jogcímek testreszabása című témakörben talál.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)          |


## <a name="next-steps"></a>További lépések

- [Videó: Az AD FS tevékenységjelentés használata alkalmazás áttelepítéséhez](https://www.youtube.com/watch?v=OThlTA239lU)
- [Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
- [Alkalmazások hozzáférésének kezelése](what-is-access-management.md)
- [Azure AD Connect-összevonás](../hybrid/how-to-connect-fed-whatis.md)
