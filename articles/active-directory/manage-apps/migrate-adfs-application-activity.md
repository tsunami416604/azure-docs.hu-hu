---
title: AD FS-alkalmazások áthelyezése a tevékenység-jelentés használatával Azure Active Directory | Microsoft Docs "
description: Az Active Directory összevonási szolgáltatások (AD FS) (AD FS) alkalmazási tevékenység jelentés segítségével gyorsan áttelepítheti az alkalmazásokat a AD FSról a Azure Active Directory (Azure AD) szolgáltatásba. Ez a AD FS áttelepítési eszköz azonosítja az Azure AD-vel való kompatibilitást, és áttelepítési útmutatót biztosít.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/30/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a267811b7e7ac8715b6823a24c2b3a1f0d430c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180536"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Alkalmazások áttelepíthetők az Azure AD-be a AD FS alkalmazás-tevékenységi jelentés (előzetes verzió) használatával

Számos szervezet Active Directory összevonási szolgáltatások (AD FS) (AD FS) használatával teszi lehetővé az egyszeri bejelentkezést a felhőalapú alkalmazásokhoz. Jelentős előnyökkel jár a AD FS-alkalmazások Azure AD-ba való áthelyezése a hitelesítéshez, különösen a Cost Management, a kockázatkezelés, a termelékenység, a megfelelőség és a szabályozás szempontjából. Az Azure AD-vel kompatibilis alkalmazások és az áttelepítési lépések azonosítása azonban időigényes lehet.

A Azure Portal AD FS alkalmazás-tevékenység jelentés (előzetes verzió) segítségével gyorsan azonosíthatja, hogy mely alkalmazások képesek áttelepíteni az Azure AD-be. Az Azure AD-vel való kompatibilitás érdekében az összes AD FS alkalmazást értékeli, ellenőrzi a problémákat, és útmutatást ad az egyes alkalmazások áttelepítésre való előkészítéséhez. A AD FS alkalmazás-tevékenység jelentéssel a következőket teheti:

* **Fedezze fel AD FS alkalmazásait és hatókörét az áttelepítés során.** A AD FS alkalmazás tevékenység jelentés felsorolja a szervezet összes AD FS alkalmazását, és jelzi az Azure AD-ba való Migrálás készültségét.
* **Alkalmazások rangsorolása az áttelepítéshez.** Azon egyedi felhasználók számának lekérése, akik az elmúlt 1, 7 vagy 30 napban bejelentkezett az alkalmazásba az alkalmazás áttelepítésének sikeressége vagy kockázata alapján.
* **Futtassa az áttelepítési teszteket és javítsa ki a problémákat.** A Reporting szolgáltatás automatikusan futtatja a teszteket annak megállapítására, hogy az alkalmazás készen áll-e az áttelepítés Az eredmények áttelepítési állapotként jelennek meg az AD FS alkalmazás tevékenység jelentésében. Ha azonosítják a lehetséges áttelepítési problémákat, konkrét útmutatást kap a problémák megoldásához.

A AD FS alkalmazás tevékenységi adatai a következő rendszergazdai szerepkörökhöz rendelt felhasználók számára érhetők el: globális rendszergazda, jelentéskészítő olvasó, biztonsági olvasó, alkalmazás-rendszergazda vagy Felhőbeli alkalmazás rendszergazdája.

## <a name="prerequisites"></a>Előfeltételek

* A szervezetnek jelenleg AD FS kell használnia az alkalmazások eléréséhez.
* Az Azure AD Connect Health engedélyezni kell az Azure AD-bérlőben.
   * [További információ a Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Ismerkedés a Azure AD Connect Health beállításával](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Az áttelepíthető AD FS alkalmazások felderítése 

A AD FS alkalmazás tevékenységéről szóló jelentés az Azure AD- **használat &** a bejelentési jelentések területen elérhető Azure Portalban érhető el. A AD FS alkalmazási tevékenység jelentés elemzi az egyes AD FS alkalmazásait annak megállapítására, hogy az áttelepíthető-e, vagy ha további felülvizsgálatra van szükség. 

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) egy olyan rendszergazdai szerepkörrel, amely hozzáféréssel rendelkezik AD FS alkalmazás-tevékenységgel kapcsolatos adathoz (globális rendszergazda, jelentéskészítő olvasó, biztonsági olvasó, alkalmazás-rendszergazda vagy Felhőbeli alkalmazás rendszergazdája).

2. Válassza a **Azure Active Directory**lehetőséget, majd válassza a **vállalati alkalmazások**lehetőséget.

3. A **tevékenység**területen válassza a **használat & elemzése (előzetes verzió)** lehetőséget, majd válassza a **AD FS alkalmazás tevékenység** lehetőséget, hogy megnyissa a szervezet összes AD FS alkalmazásának listáját.

   ![AD FS alkalmazási tevékenység](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Tekintse meg az **áttelepítési állapotot**a AD FS alkalmazás-tevékenység listán szereplő egyes alkalmazásokhoz:

   * Az **Áttelepítésre kész érték** azt jelenti, hogy a AD FS alkalmazás konfigurációja teljes mértékben támogatott az Azure ad-ben, és a-ként is telepíthető.

   * Az **igények felülvizsgálata** azt jelenti, hogy az alkalmazás egyes beállításai áttelepíthetők az Azure ad-be, de át kell tekintenie azokat a beállításokat, amelyek nem telepíthetők át.

   * A **további szükséges lépések** azt jelentik, hogy az Azure ad nem támogatja az alkalmazás egyes beállításait, így az alkalmazás jelenlegi állapotában nem telepíthető át.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Egy alkalmazás készültségének kiértékelése áttelepítésre 

1. Az áttelepítési adatok megnyitása a AD FS alkalmazási tevékenység listában kattintson az **áttelepítés állapota** oszlopban található állapotra. Ekkor megjelenik az átadott konfigurációs tesztek összegzése, valamint az esetleges áttelepítési problémák.

   ![Áttelepítési adatok](media/migrate-adfs-application-activity/migration-details.png)

2. Kattintson egy üzenetre további áttelepítési szabály részleteinek megnyitásához. A tesztelt tulajdonságok teljes listájáért tekintse meg az alábbi [AD FS alkalmazás-konfigurációs tesztek](#ad-fs-application-configuration-tests) táblázatban.

   ![Áttelepítési szabály részletei](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>Alkalmazás-konfigurációs tesztek AD FS

A következő táblázat felsorolja a AD FS alkalmazásokon végrehajtott összes konfigurációs tesztet.

|Tulajdonság  |Állapot  |Leírás  |
|---------|---------|---------|
|A AdditionalAuthentication legalább egy nem áttelepíthető szabályt észlelt a rendszer.       | Továbbítás/figyelmeztetés          | A AD FS függő entitása helyszíni MFA-szolgáltatót használ. Az Azure AD-re való áttéréshez javasoljuk, hogy az Azure MFA-ra vagy az egyéni vezérlőkre váltson át külső MFA-szolgáltatóval.  [További információ az Azure MFA-ról](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).        |
|A függő entitás AdditionalWSFedEndpoint értéke TRUE (igaz).       | Pass/Fail          | A AD FS függő entitása több WS-fed kiállítási végpontot is lehetővé tesz. Az Azure AD jelenleg csak az egyiket támogatja (1). Ha van olyan forgatókönyv, ahol ez blokkolja az áttelepítést, [tudassa velünk](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|A függő entitás beállította a AllowedAuthenticationClassReferences.       | Pass/Fail          | Ez egy beállítás a AD FSban, amely lehetővé teszi annak megadását, hogy az alkalmazás úgy legyen konfigurálva, hogy csak bizonyos hitelesítési típusokat engedélyezzen. Az Azure AD jelenleg nem támogatja ezt a szolgáltatást. Ha van olyan forgatókönyv, ahol ez blokkolja az áttelepítést, [tudassa velünk](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).          |
|A AlwaysRequireAuthentication be van állítva.      | Pass/Fail          | Azt határozza meg, hogy az alkalmazás úgy van-e konfigurálva, hogy figyelmen kívül hagyja az egyszeri bejelentkezéses cookie-kat és a "mindig Az Azure AD jelenleg nem támogatja.          |
|A függő entitás AutoUpdateEnabled értéke TRUE (igaz)       | Továbbítás/figyelmeztetés          | Ez egy beállítás a AD FSban, amely lehetővé teszi, hogy a AD FS úgy legyen konfigurálva, hogy az összevonási metaadatokon belüli módosítások alapján automatikusan frissítse az alkalmazást. Az Azure AD nem támogatja ezt a ma, de nem blokkolja az alkalmazás Azure AD-be való áttelepítését.           |
|A függő entitás több ClaimsProviders engedélyezve van       | Pass/Fail          | A függő entitás a Active Directorytól eltérő jogcím-szolgáltatótól származó jogcímek forrására van konfigurálva.  Ez az Azure AD-ben nem támogatott. Ha van olyan forgatókönyv, ahol ez blokkolja az áttelepítést, [tudassa velünk](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|A DelegationAuthorization érvényes      | Pass/Fail          | Az alkalmazáshoz egyéni delegálási engedélyezési szabályok vannak meghatározva. Az Azure AD jelenleg nem támogatja ezt a szolgáltatást. Ha van olyan forgatókönyv, ahol ez blokkolja az áttelepítést, [tudassa velünk](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695747-allow-to-define-delegation-authorization-rules).          |
|A függő entitás rendelkezik ADFSRPImpersonationAuthorizationRules       | Továbbítás/figyelmeztetés          | Az alkalmazáshoz egyéni megszemélyesítési engedélyezési szabályok vannak meghatározva. Az Azure AD nem támogatja ezt a ma, de nem blokkolja az alkalmazás Azure AD-be való áttelepítését.          |
|A IssuanceAuthorization legalább egy nem áttelepíthető szabályt észlelt a rendszer.       | Továbbítás/figyelmeztetés          | Az alkalmazás AD FSban definiált egyéni kiállítási engedélyezési szabályokkal rendelkezik. Az Azure AD támogatja ezt a funkciót az Azure AD feltételes hozzáférésével. [További információ a feltételes hozzáférésről](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). Az Azure AD-ben az alkalmazáshoz rendelt felhasználók vagy csoportok is korlátozhatják az alkalmazáshoz való hozzáférést. [További információ a felhasználók és csoportok az alkalmazásokhoz való hozzárendeléséről](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups). A AD FSban konfigurált nyers IssuanceAuthorization-szabályokkal kapcsolatos információkért tekintse meg a [jogcím-szabály tesztelési](#check-the-results-of-claim-rule-tests)táblázatát.           |
|A IssuanceTransform legalább egy nem áttelepíthető szabályt észlelt a rendszer.       | Továbbítás/figyelmeztetés          | Az alkalmazásnak egyéni kiállítási átalakítási szabályai vannak definiálva AD FSban. Az Azure AD támogatja a jogkivonatban kiadott jogcímek testreszabását. További információ: [a vállalati alkalmazások SAML-jogkivonatában kiadott jogcímek testreszabása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).  A AD FSban konfigurált nyers IssuanceTransformation-szabályokkal kapcsolatos információkért tekintse meg a [jogcím-szabály tesztelési](#check-the-results-of-claim-rule-tests)táblázatát.          |
|A függő entitás MonitoringEnabled értéke TRUE (igaz).       | Továbbítás/figyelmeztetés          | Ez egy beállítás a AD FSban, amely lehetővé teszi annak megadását, hogy a AD FS az alkalmazás összevonási metaadatainak figyelésére legyen konfigurálva. Ez az Azure AD-ben nem támogatott, de nem lehet letiltani az alkalmazás áttelepítését az Azure AD-be.          |
|A függő entitás NotBeforeSkew van beállítva.      | Továbbítás/figyelmeztetés          | A AD FS az SAML-token NotBefore és NotOnOrAfter időpontját is lehetővé teszi. Az Azure AD nem támogatja ezt a ma, de nem blokkolja az alkalmazás Azure AD-be való áttelepítését.          |
|A függő entitás RequestMFAFromClaimsProviders értéke TRUE (igaz).       | Továbbítás/figyelmeztetés          | Ez egy beállítás a AD FSban, amely lehetővé teszi, hogy megadhatja, hogy az alkalmazás hardcoded-e egy másik jogcím-szolgáltatóhoz, és hogy az MFA szükséges Az Azure AD-re való áttéréshez javasoljuk, hogy az Azure MFA-ra vagy az egyéni vezérlőkre váltson át egy harmadik féltől származó MFA-szolgáltatóval.  [További információ az Azure MFA-ról](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).          |
|A függő entitás SignedSamlRequestsRequired értéke TRUE (igaz)       | Pass/Fail          | Az alkalmazás a AD FSban van konfigurálva az SAML-kérelem aláírásának ellenőrzéséhez. Ez egy nem kötelezően megadandó beállítás, és nem tiltható le az áttelepítés. Azure Active Directory a válasz URL-címek használatával ellenőrzi a szolgáltatót.  Ha van olyan forgatókönyv, ahol ez blokkolja az áttelepítést, [tudassa velünk](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|TokenLifetimeCheckResult        | Pass/Fail         | Az alkalmazás egyéni jogkivonat élettartamára van konfigurálva. AD FS alapértelmezett érték 1 óra. Az Azure AD a feltételes hozzáférés használatával támogatja ezt a funkciót. További információ: [a hitelesítési munkamenetek kezelésének beállítása feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|A függő entitás a jogcímek titkosítására van beállítva. Ezt az Azure AD támogatja       | Pass          | Az Azure AD segítségével titkosíthatja a token küldését az alkalmazásnak. További információ: az [Azure ad SAML-jogkivonat titkosításának konfigurálása](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).          |
|A függő entitás úgy van beállítva, hogy Titkosítsa a NameID az SAML-jogkivonatban.      | Pass/Fail          | Az alkalmazás úgy van konfigurálva, hogy Titkosítsa a nameID jogcímet az SAML-jogkivonatban. Az Azure AD segítségével titkosíthatja az alkalmazásnak eljuttatott teljes tokent. Az egyes jogcímek titkosítása még nem támogatott. További információ: az [Azure ad SAML-jogkivonat titkosításának konfigurálása](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).         |

## <a name="check-the-results-of-claim-rule-tests"></a>A jogcím-szabályhoz tartozó tesztek eredményeinek ellenőrzése

Ha AD FS-ben konfigurálta az alkalmazáshoz tartozó jogcímet, akkor a felhasználói élmény részletes elemzést nyújt az összes jogcím-szabályról. Láthatja, hogy mely jogcímek helyezhetők át az Azure AD szolgáltatásba, és melyeknek további felülvizsgálatra van szükségük.

1. Az áttelepítési adatok megnyitása a AD FS alkalmazási tevékenység listában kattintson az **áttelepítés állapota** oszlopban található állapotra. Ekkor megjelenik az átadott konfigurációs tesztek összegzése, valamint az esetleges áttelepítési problémák.

2. Az **áttelepítési szabály részletei** lapon bontsa ki az eredményeket, hogy megjelenítse az esetleges áttelepítési problémák részleteit, és további útmutatást kapjon. A kipróbált jogcímek részletes listáját a következő témakörben találja: a jogcím-szabályok [tesztelési táblázatának eredményeinek ellenőrzése](#check-the-results-of-claim-rule-tests) .

   Az alábbi példa a IssuanceTransform szabály áttelepítési szabályának részleteit mutatja be. Felsorolja a jogcím azon részeit, amelyeket felül kell vizsgálni és kezelni kell, mielőtt áttelepíti az alkalmazást az Azure AD szolgáltatásba.

   ![Áttelepítési szabály részletei – további útmutatás](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Jogcím-szabály tesztelése

A következő táblázat felsorolja a AD FS alkalmazásokon végrehajtott összes jogcímet.

|Tulajdonság  |Leírás  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | A Condition utasítás reguláris kifejezéseket használ annak kiértékeléséhez, hogy a jogcím megfelel-e egy adott mintának.  Ahhoz, hogy hasonló funkciókat lehessen elérni az Azure AD-ben, az előre definiált átalakítást, például a IfEmpty (), a StartWith (), a () függvényt is használhatja egyebek között. További információ: [az SAML-jogkivonatban kiadott jogcímek testreszabása nagyvállalati alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_CONDITION_CLASS      | A Condition utasítás több olyan feltételt tartalmaz, amelyeket a kiállítási utasítás futtatása előtt ki kell értékelni. Az Azure AD ezt a funkciót a jogcím átalakítási funkcióiról is támogatja, ahol több jogcím értékét is kiértékelheti.  További információ: [az SAML-jogkivonatban kiadott jogcímek testreszabása nagyvállalati alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_RULE_TYPE      | Nem sikerült felismerni a jogcím szabályát. A jogcímek Azure AD-ben való konfigurálásával kapcsolatos további információkért lásd: [az SAML-jogkivonat által a vállalati alkalmazásokhoz kiadott jogcímek testreszabása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | A Condition utasítás olyan kiállítót használ, amely nem támogatott az Azure AD-ben. Jelenleg az Azure AD nem a Active Directory vagy az Azure AD-től eltérő áruházakból származó jogcímeket állítja le. Ha ez blokkolja az alkalmazások Azure AD-ba való áttelepítését, [tudassa velünk](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | A Condition utasítás összesítő függvénnyel állítja ki vagy adja hozzá az egyes jogcímeket, a egyezések számától függetlenül.  Az Azure AD-ben kiértékelheti a felhasználó attribútumát, hogy eldöntse, milyen értéket kell használnia a jogcímek számára a (z) IfEmpty (), a StartWith (), a () függvényekkel együtt, többek között. További információ: [az SAML-jogkivonatban kiadott jogcímek testreszabása nagyvállalati alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|RESTRICTED_CLAIM_ISSUED      | A Condition utasítás olyan jogcímet használ, amely korlátozott az Azure AD-ben. Lehet, hogy kiállít egy korlátozott jogcímet, de nem módosíthatja a forrását, vagy nem alkalmazhat átalakítást. További információ: az [Azure ad-ben egy adott alkalmazás jogkivonatában kibocsátott jogcímek testreszabása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).          |
|EXTERNAL_ATTRIBUTE_STORE      | A kiállítási utasítás a Active Directory eltérő attribútum-tárolót használ. Jelenleg az Azure AD nem a Active Directory vagy az Azure AD-től eltérő áruházakból származó jogcímeket állítja le. Ha ez blokkolja az alkalmazások Azure AD-ba való áttelepítését, [tudassa velünk](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | A kiállítási utasítás a Hozzáadás elem használatával adja hozzá a jogcímeket a bejövő jogcímek készletéhez. Az Azure AD-ben ez több jogcím-átalakításként is konfigurálható.  További információ: [az SAML-jogkivonatban kiadott jogcímek testreszabása nagyvállalati alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | A kiállítási utasítás reguláris kifejezéseket használ a kibocsátott jogcím értékének átalakításához. Ahhoz, hogy hasonló funkciókat lehessen elérni az Azure AD-ben, használhatja az előre definiált transzformációt, például a Extract (), a Trim (), a ToLower, a többit is. További információ: [az SAML-jogkivonatban kiadott jogcímek testreszabása nagyvállalati alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |


## <a name="next-steps"></a>Következő lépések

- [Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
- [Alkalmazások hozzáférésének kezelése](what-is-access-management.md)
- [Azure AD Connect-összevonás](../hybrid/how-to-connect-fed-whatis.md)
