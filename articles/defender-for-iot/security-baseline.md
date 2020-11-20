---
title: Azure Security alapkonfiguráció a IoT készült Azure Defender számára
description: Az Azure Defender for IoT biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához nyújt eljárási útmutatást és forrásokat.
author: msmbaldwin
ms.service: defender-for-iot
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad10195c50d3de49ce89c3917ebac5ba76ca4194
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94974856"
---
# <a name="azure-security-baseline-for-azure-defender-for-iot"></a>Azure Security alapkonfiguráció a IoT készült Azure Defender számára

Ez a biztonsági alapkonfiguráció a 2,0-es [Azure biztonsági teljesítményteszt-es verziójának](../security/benchmarks/overview.md) útmutatását alkalmazza Microsoft Azure Defender for IoT. Az Azure biztonsági teljesítményteszt az Azure-beli felhőalapú megoldások biztonságossá tételével kapcsolatos ajánlásokat tartalmaz. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint az Azure Defender for IoT vonatkozó kapcsolódó útmutatás. Az Azure Defender for IoT nem alkalmazható **vezérlők** ki vannak zárva.

Ha szeretné megtudni, hogy az Azure Defender hogyan IoT teljes mértékben az Azure biztonsági Teljesítménytesztre, tekintse meg a [teljes Azure Defender for IoT biztonsági alapkonfiguráció-hozzárendelési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Identitáskezelés

*További információt az [Azure biztonsági teljesítményteszt: Identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management)című témakörben talál.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: szabványosított Azure Active Directory központi identitás-és hitelesítési rendszerrel

**Útmutató**: az Azure Defender for IoT integrálva van Azure Active Directory (Azure ad), az Azure alapértelmezett identitás-és hozzáférés-kezelési szolgáltatásával. Egységesítse az Azure AD-t a szervezet identitás-és hozzáférés-kezelésének szabályozásához a ben:

- Microsoft Cloud erőforrások, például a Azure Portal, az Azure Storage, az Azure virtuális gép (Linux és Windows), a Azure Key Vault, a Pásti és az SaaS-alkalmazások.

- A szervezet erőforrásai, például az Azure-beli alkalmazások vagy a vállalati hálózati erőforrások.

Az Azure AD biztonságossá tételének magas prioritásnak kell lennie a szervezet felhőalapú biztonsági gyakorlatában. Az Azure AD egy biztonságos azonosítási pontszámot biztosít, amellyel a Microsoft ajánlott eljárási javaslataihoz képest kiértékelheti a személyazonosság biztonsági helyzetét. A pontszám használatával mérje fel, hogy a konfiguráció milyen mértékben felel meg az ajánlott eljárásokkal kapcsolatos javaslatoknak, és javítsa a biztonsági helyzetét.

Az Azure AD támogatja a külső identitást, amely lehetővé teszi a felhasználók számára, hogy Microsoft-fiók nélkül bejelentkezzenek alkalmazásaiba és erőforrásaiba külső identitásával.

- [Bérlet Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Külső identitás-szolgáltatók használata az alkalmazáshoz](/azure/active-directory/b2b/identity-providers) 

- [Mi az identitások biztonságos pontszáma Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: az Azure AD egyszeri bejelentkezés (SSO) használata az alkalmazásokhoz való hozzáféréshez

**Útmutató**: a IoT-hez készült Azure Defender az Azure-erőforrások, a Felhőbeli alkalmazások és a helyszíni alkalmazások identitás-és hozzáférés-kezelésének biztosítására Azure Active Directory használ. Ide tartoznak a vállalati identitások, például az alkalmazottak, valamint a külső identitások, például a partnerek, a szállítók és a szállítók. Ez lehetővé teszi az egyszeri bejelentkezést (SSO) a szervezet adataihoz és erőforrásaihoz a helyszínen és a felhőben való hozzáférés felügyeletéhez és biztonságossá tételéhez. Az összes felhasználó, alkalmazás és eszköz csatlakoztatása az Azure AD-hez zökkenőmentes, biztonságos hozzáférés és jobb láthatóság és vezérlés érdekében.

- [Alkalmazás egyszeri bejelentkezésének megismerése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: erős hitelesítési vezérlők használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: az Azure Defender for IoT olyan Azure Active Directory használ, amely támogatja a többtényezős hitelesítés (MFA) és az erős, jelszóval nem használható metódusok erős hitelesítési vezérlését.

- Többtényezős hitelesítés – engedélyezheti az Azure AD MFA-t, és követheti Azure Security Center identitás-és hozzáférés-kezelési javaslatait az MFA-telepítéssel kapcsolatos ajánlott eljárásokhoz. Az MFA kikényszeríthető az összes, a felhasználók vagy a felhasználónkénti szinten a bejelentkezési feltételek és a kockázati tényezők alapján.
- Jelszóval nem rendelkező hitelesítés – a Windows Hello for Business, az Microsoft Authenticator az alkalmazás és a helyszíni hitelesítési módszerek, például az intelligens kártyák.

A rendszergazda és a Kiemelt jogosultságú felhasználók számára győződjön meg arról, hogy az erős hitelesítési módszer legmagasabb szintje van használatban, majd ezt követi a megfelelő erős hitelesítési házirend kiosztása más felhasználók számára.

- [Az MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Bevezetés a Azure Active Directory jelszavas hitelesítési lehetőségeibe](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Az Azure AD alapértelmezett jelszavas házirendje](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Helytelen jelszavak eltávolítása az Azure AD jelszavas védelemmel](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: a fiókok rendellenességeit figyelő és riasztások

**Útmutató**: az Azure Defender for IoT integrálva van a Azure Active Directory, amely a következő adatforrásokat biztosítja:

Bejelentkezések – a bejelentkezési jelentés információt nyújt a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.

Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók például a felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadásával vagy eltávolításával kapcsolatos, az Azure AD-ban található összes erőforráson végrehajtott módosítások.

Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.

Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

Ezek az adatforrások a Azure Monitor, az Azure Sentinel vagy a harmadik féltől származó SIEM rendszerekkel integrálhatók.

Azure Security Center riasztást is beállíthat bizonyos gyanús tevékenységekre, például a sikertelen hitelesítési kísérletek túlzott számára, az előfizetés elavult fiókjaira.

Az Azure komplex veszélyforrások elleni védelem (ATP) olyan biztonsági megoldás, amely Active Directory jeleket használ a fejlett fenyegetések, a feltört identitások és a rosszindulatú bennfentes műveletek azonosítására, észlelésére és kivizsgálására.

- [Tevékenység-jelentések naplózása az Azure AD-ben](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Az Azure AD kockázatos bejelentkezések megtekintése](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [A kockázatos tevékenységre megjelölt Azure AD-felhasználók azonosítása](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Center](../security-center/security-center-identity-access.md) 

- [Riasztások Azure Security Center veszélyforrások elleni védelmi moduljában](../security-center/alerts-reference.md) 

- [Azure-beli tevékenység-naplók integrálása a Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információt az [Azure biztonsági teljesítményteszt: privilegizált hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access)című témakörben talál.*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: az üzleti szempontból kritikus fontosságú rendszerek rendszergazdai hozzáférésének korlátozása

**Útmutató**: a IoT készült Azure Defender az Azure RBAC segítségével elkülöníti az üzleti szempontból kritikus rendszerek hozzáférését azáltal, hogy korlátozza, hogy mely fiókok emelt szintű hozzáférést kapnak az előfizetésekhez és a felügyeleti csoportokhoz.

Győződjön meg arról, hogy az üzleti szempontból kritikus fontosságú rendszerekre telepített ügynökökkel való rendszergazdai hozzáféréssel rendelkező felügyeleti, identitási és biztonsági rendszerekhez is korlátozza a hozzáférést, például Active Directory-tartomány vezérlőket (DCs), biztonsági eszközöket és rendszerfelügyeleti eszközöket. Azok a támadók, akik veszélyeztetik ezeket a felügyeleti és biztonsági rendszereket, azonnal weaponize azokat az üzleti szempontból kritikus fontosságú eszközök biztonsága érdekében.

A hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani, hogy biztosítsa a konzisztens hozzáférés-vezérlést.

- [Azure-összetevők és-hivatkozási modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Felügyeleti csoport elérése](../governance/management-groups/overview.md#management-group-access) 

- [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: vészhelyzeti hozzáférés beállítása az Azure AD-ben

**Útmutató**: az Azure Defender for IoT integrálva van a Azure Active Directoryekkel az erőforrások kezeléséhez. Ha nem szeretné megakadályozni, hogy véletlenül kizárja az Azure AD-szervezetét, állítson be egy vészhelyzeti hozzáférési fiókot a hozzáféréshez, ha a normál rendszergazdai fiókok nem használhatók. A vészhelyzeti hozzáférési fiókok általában magas jogosultsággal rendelkeznek, és nem rendelhetők hozzá konkrét személyekhez. A vészhelyzeti hozzáférési fiókok olyan vészhelyzeti vagy "break Glass" forgatókönyvekre korlátozódnak, amelyekben nem használhatók normál rendszergazdai fiókok.

Győződjön meg arról, hogy a vészhelyzeti hozzáférési fiókokhoz tartozó hitelesítő adatok (például jelszó, tanúsítvány vagy intelligens kártya) biztonságosak és ismertek, csak azokra a személyekre, akik jogosultak arra, hogy csak vészhelyzetben használják őket.

- [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pa-5-automate-entitlement-management"></a>PA-5: a jogosultságok kezelésének automatizálása 

**Útmutató**: az Azure Defender for IoT integrálva van a Azure Active Directoryekkel az erőforrások kezeléséhez. Az Azure AD-jogosultságok kezelési funkciói segítségével automatizálhatja a hozzáférési kérelmek munkafolyamatait, beleértve a hozzáférési hozzárendeléseket, az értékeléseket és a lejáratokat. A kettős vagy többfázisú jóváhagyás is támogatott.

- [Az Azure AD hozzáférési felülvizsgálatai](../active-directory/governance/access-reviews-overview.md) 

- [Mi az Azure AD-jogosultságok kezelése?](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: csak elég felügyelet (a legalacsonyabb jogosultsági elv) követése 

**Útmutató**: az Azure Defender for IoT integrálva van az Azure szerepköralapú hozzáférés-vezérléssel (RBAC) az erőforrások kezeléséhez. Az Azure RBAC lehetővé teszi az Azure-erőforrások hozzáférésének kezelését a szerepkör-hozzárendeléseken keresztül. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre definiált beépített szerepkörök tartoznak, és ezeket a szerepköröket az Azure CLI-vel, a Azure PowerShellsal vagy a Azure Portal eszközzel leltározott vagy lekérdezheti. Az erőforrásokhoz az Azure RBAC-on keresztül hozzárendelt jogosultságokat mindig a szerepkörök által igényelt értékre kell korlátozni. Ez kiegészíti a Azure AD Privileged Identity Management (PIM) igény szerinti (JIT) megközelítését, és rendszeresen felül kell vizsgálni.

A beépített szerepkörök használatával lefoglalhatja az engedélyt, és szükség esetén csak egyéni szerepkört hozhat létre.

- [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../role-based-access-control/overview.md) 

- [A RBAC konfigurálása az Azure-ban](../role-based-access-control/role-assignments-portal.md) 

- [Az Azure AD-identitás és a hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információt az [Azure biztonsági teljesítményteszt: Adatvédelem](/azure/security/benchmarks/security-controls-v2-data-protection)című témakörben talál.*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: bizalmas adatok felderítése, osztályozása és címkézése

**Útmutató**: bizalmas adatok felderítése, osztályozása és címkézése, hogy megtervezze a megfelelő vezérlőket, hogy a bizalmas adatok tárolása, feldolgozása és továbbítása biztonságos legyen a szervezet technológiai rendszereinek megfelelően. 

Az Azure-ban, a helyszínen, az Office 365-on és más helyeken található Office-dokumentumokban található bizalmas információkhoz használja a Azure Information Protection (és a hozzá tartozó ellenőrzési eszközt). 

Az Azure SQL Information Protection használatával segítséget nyújthat az Azure SQL-adatbázisokban tárolt adatok besorolásában és címkézésében.

- [Bizalmas adatok címkézése Azure Information Protection használatával](/azure/information-protection/what-is-information-protection) 

- [Az Azure SQL-adatfelderítés megvalósítása](/azure/sql-database/sql-database-data-discovery-and-classification)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="dp-2-protect-sensitive-data"></a>DP-2: bizalmas adatok védelme

**Útmutató**: a bizalmas adatok védelme az azure szerepköralapú Access Control (Azure RBAC), a hálózati hozzáférés-vezérlés és az Azure-szolgáltatások adott vezérlői (például az SQL és más adatbázisok titkosítása) használatával történő hozzáférés korlátozásával. 

A konzisztens hozzáférés-vezérlés biztosításához a hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani. A nagyvállalati szegmentálási stratégiát is tájékoztatni kell a bizalmas vagy üzleti szempontból kritikus fontosságú adatok és rendszerek helyéről.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatvesztése és a kitettség ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft néhány alapértelmezett adatvédelmi vezérlőt és képességet alkalmazott.

- [Azure-beli szerepköralapú Access Control (RBAC)](../role-based-access-control/overview.md)

- [Az ügyfelek adatvédelmének megismerése az Azure-ban](../security/fundamentals/protection-customer-data.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: bizalmas adatok titkosítása az átvitel során

**Útmutató**: a hozzáférés-vezérlés kiegészítéseként a forgalomban lévő adatoknak védelmet biztosítanak a "sávon kívüli" támadások (például a forgalmi rögzítés) ellen, hogy a támadók ne tudják egyszerűen beolvasni vagy módosítani az adatokat. 

Habár ez nem kötelező a magánhálózaton lévő forgalom esetében, ez kritikus fontosságú a külső és a nyilvános hálózatok forgalmában. HTTP-forgalom esetén győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó összes ügyfél egyeztetheti a TLS v 1.2-es vagy újabb verzióját. A Távoli felügyelet érdekében titkosítatlan protokoll helyett használja az SSH-t (Linuxon) vagy RDP/TLS (Windows rendszeren). Az elavult SSL-, TLS-és SSH-verziók és protokollok, valamint a gyenge titkosítások le lesznek tiltva.  

Alapértelmezés szerint az Azure titkosítást biztosít az Azure-adatközpontok közötti adatátvitelhez. 

- [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Információk a TLS biztonságáról](/security/engineering/solving-tls1-problem)

- [Kettős titkosítás az Azure-beli adatforgalomban](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információt az [Azure biztonsági teljesítményteszt: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management)című témakörben talál.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Győződjön meg arról, hogy a biztonsági csapat az eszközök kockázataival rendelkezik

**Útmutató**: gondoskodjon arról, hogy a biztonsági csapatok biztonsági olvasói engedélyeket kapjanak az Azure-bérlőben és-előfizetésekben, hogy a biztonsági kockázatokat a Azure Security Center használatával tudják figyelni. 

A biztonsági csapat feladatainak strukturálása, a biztonsági kockázatok figyelése a központi biztonsági csapat vagy egy helyi csapat feladata lehet. Azt is mondta, hogy a biztonsági ismereteket és a kockázatokat mindig központilag összesíteni kell egy szervezeten belül. 

A biztonsági olvasó engedélyei széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoportra), vagy a felügyeleti csoportokra vagy az adott előfizetésekre is. 

További engedélyek szükségesek lehetnek a számítási feladatok és szolgáltatások láthatóságának megismeréséhez. 

- [A biztonsági olvasó szerepkör áttekintése](../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure Management Groups áttekintése](../governance/management-groups/overview.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával naplózhatja és korlátozhatja, hogy a felhasználók mely szolgáltatásokat tudják kiépíteni a környezetben. Az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetésen belüli erőforrásokat. A Azure Monitor használatával olyan szabályokat is létrehozhat, amelyek riasztásokat aktiválnak, ha a rendszer nem jóváhagyott szolgáltatást észlel.

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: az eszközök életciklus-kezelésének biztonsága

**Útmutató**: olyan biztonsági házirendeket hozhat létre vagy frissíthet, amelyek az eszköz életciklus-kezelési folyamataiban potenciálisan nagy hatású módosításokat végeznek. A módosítások közé tartoznak a következők: az identitás-szolgáltatók és a hozzáférés, az adatok érzékenysége, a hálózati konfiguráció és a rendszergazdai jogosultságok kiosztása.

Ha már nincs rájuk szükség, távolítsa el az Azure-erőforrásokat.

- [Azure-erőforráscsoport és-erőforrás törlése](../azure-resource-manager/management/delete-resource-group.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure ad feltételes hozzáférés használatával korlátozhatja a felhasználók interakcióját a Azure Resource Manager az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információt az [Azure biztonsági teljesítményteszt: incidens válasza](/azure/security/benchmarks/security-controls-v2-incident-response)című témakörben talál.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: előkészítés – az incidensek frissítési folyamatának frissítése az Azure-ban

**Útmutató**: Ellenőrizze, hogy a szervezet rendelkezik-e olyan folyamatokkal, amelyek reagálnak a biztonsági incidensekre, és frissítette ezeket a folyamatokat az Azure-ban, és rendszeresen gyakorolja őket a készültség biztosítására.

- [A biztonság megvalósítása a nagyvállalati környezetben](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Incidens-válasz referenciájának útmutatója](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: előkészítés – telepítési incidens értesítése

**Útmutató**: biztonsági incidensek elérhetőségi adatainak beállítása Azure Security Centerban. A Microsoft a kapcsolattartási adatokat arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. Lehetőség van az incidensek riasztásának és az értesítések különböző Azure-szolgáltatásokban való testreszabására is az incidensek igényei alapján. 

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: észlelés és elemzés – incidensek létrehozása kiváló minőségű riasztások alapján 

**Útmutató**: Ellenőrizze, hogy van-e olyan folyamat, amely kiváló minőségű riasztásokat hoz létre, és mérje fel a riasztások minőségét. Ez lehetővé teszi, hogy megismerje a múltbeli incidensek tanulságait, és rangsorolja a riasztásokat az elemzők számára, hogy ne pazarolja az időt a hamis pozitív értékekre. 

A kiváló minőségű riasztások a múltbeli incidensek, a hitelesített közösségi források és a riasztások létrehozásához és tisztításához tervezett eszközök alapján hozhatók létre a különböző jelforrások elutasításával és korrelációs megoldásával. 

Azure Security Center kiváló minőségű riasztásokat biztosít számos Azure-eszközön. Az ASC adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba. Az Azure Sentinel lehetővé teszi, hogy speciális riasztási szabályokat hozzon létre az incidensek automatikus létrehozásához a vizsgálathoz. 

A Azure Security Center-riasztások és-javaslatok exportálásával az Exportálás funkcióval azonosíthatók az Azure-erőforrásokkal kapcsolatos kockázatok. Riasztásokat és javaslatokat manuálisan vagy folyamatos, folyamatos módon exportálhat.

- [Az Exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások továbbítása az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: észlelés és elemzés – incidens vizsgálata

**Útmutató**: az elemzők különböző adatforrásokat tudnak lekérdezni és használni a lehetséges incidensek kivizsgálásával, így teljes képet kaphatnak arról, hogy mi történt. Különböző naplókat kell gyűjteni a lehetséges támadók tevékenységének nyomon követéséhez a támadási láncon belül a vak foltok elkerülése érdekében.  Gondoskodjon arról is, hogy az elemzések és a megszerzett ismeretek rögzítése más elemzők és a későbbi korábbi referenciák esetében is megtörténjen.  

A vizsgálathoz használt adatforrások közé tartoznak azok a központi naplózási források, amelyeket a rendszer már gyűjt a hatókörbeli szolgáltatásokból és a futó rendszerekről, de a következőket is tartalmazhatja:

- Hálózati adatok – hálózati biztonsági csoportok folyamatábrája, Azure Network Watcher és Azure Monitor a hálózati folyamat naplói és egyéb elemzési információk rögzítésére. 

- Futó rendszerek pillanatképei: 

    - Az Azure-beli virtuális gép pillanatkép-funkciója segítségével pillanatkép készíthető a futó rendszer lemezéről. 

    - A futó rendszer memóriájának pillanatképének létrehozásához használja az operációs rendszer natív memóriaképének képességét.

    - Használja az Azure-szolgáltatások pillanatkép-szolgáltatását, vagy az Ön szoftverének saját funkciója a futó rendszerek pillanatképének létrehozásához.

Az Azure Sentinel széles körű adatelemzést tesz lehetővé gyakorlatilag bármely naplózási forrás és egy eseti felügyeleti portál között az incidensek teljes életciklusának kezeléséhez. A nyomozás során felmerülő hírszerzési információk követési és jelentéskészítési célokra is társíthatók. 

- [Windows-gép lemezének pillanatképe](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [A Linux rendszerű gép lemezének pillanatképe](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure a diagnosztikai információk és a memóriakép-gyűjtés támogatása](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidensek vizsgálata az Azure Sentineltel](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: észlelés és elemzés – az incidensek rangsorolása

**Útmutató**: az elemzők környezetének biztosítása, amelyeken a riasztás súlyossága és az eszközök érzékenysége alapján az elsőre összpontosíthat. 

A Azure Security Center az egyes riasztásokhoz súlyosságot rendel, így rangsorolhatja, hogy a rendszer mely riasztásokat vizsgálja meg először. A súlyosság azon alapul, hogy az Security Center milyen mértékben van megkeresve vagy a riasztás kibocsátására szolgáló analitikusan, valamint a riasztást eredményező tevékenység mögötti olyan megbízhatósági szinten, amely rosszindulatú szándékkal rendelkezik.

Emellett a címkék használatával megjelölheti az erőforrásokat, és létrehozhat egy elnevezési rendszert az Azure-erőforrások azonosítására és kategorizálására, különösen a bizalmas adatok feldolgozására.  Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: tárolás, mentesítés és helyreállítás – az incidensek kezelésének automatizálása

**Útmutató**: automatizálja a manuális ismétlődő feladatokat a válaszidő felgyorsításához és az elemzők terhének csökkentéséhez. A manuális feladatok végrehajtása hosszabb időt vehet igénybe, és lelassítja az egyes incidenseket, és csökkenti az elemző által kezelhető incidensek számát. A manuális feladatok növelik az elemzői fáradtságot is, ami növeli a késést okozó emberi hibák kockázatát, és csökkenti az elemzők azon képességét, hogy az összetett feladatokon hatékonyan összpontosítsanak. A Azure Security Center és az Azure Sentinel munkafolyamat-automatizálási funkcióinak használatával automatikusan aktiválhatja a műveleteket, vagy futtathat egy forgatókönyvet a bejövő biztonsági riasztásokra való reagáláshoz. A forgatókönyv végrehajtja a műveleteket, például értesítések küldését, fiókok letiltását és a problémás hálózatok elkülönítését. 

- [Munkafolyamat-automatizálás konfigurálása Security Centerban](../security-center/workflow-automation.md)

- [Automatizált veszélyforrásokkal kapcsolatos válaszok beállítása Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Automatizált veszélyforrásokkal kapcsolatos válaszok beállítása az Azure Sentinelben](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="posture-and-vulnerability-management"></a>A testtartás és a sebezhetőségek kezelése

*További információt az [Azure biztonsági teljesítményteszt: testtartás és sebezhetőség kezelése](/azure/security/benchmarks/security-controls-v2-vulnerability-management)című témakörben talál.*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: rendszeres támadási szimulációk végrehajtása

**Útmutatás**: igény szerint az Azure-erőforrásokon bevezetési teszteket vagy Red Team-tevékenységeket hajthat végre, és biztosíthatja az összes kritikus biztonsági vizsgálat szervizelését.
Kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft szabályzatait. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja.

- [Behatolás-tesztelés az Azure-ban](../security/fundamentals/pen-testing.md)

- [Behatolás-tesztelési szabályok engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud vörös összevonása](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

## <a name="governance-and-strategy"></a>Irányítás és stratégia

*További információt az [Azure biztonsági teljesítményteszt: kormányzás és stratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy)című témakörben talál.*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: eszközkezelés és adatvédelmi stratégia meghatározása 

**Útmutató**: a rendszerek és az információk folyamatos monitorozására és védelmére vonatkozó egyértelmű stratégia dokumentálása és közlése. Az üzleti szempontból kritikus fontosságú adatmennyiségek és rendszerek felderítésének, értékelésének, védelmének és figyelésének rangsorolása. 

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

-   Az adatbesorolási szabvány az üzleti kockázatoknak megfelelően

-   A biztonsági szervezet betekintést nyerhet a kockázatokra és az eszközök leltározására 

-   Az Azure-szolgáltatások használatának biztonsági szervezet általi jóváhagyása 

-   Az eszközök biztonsága életcikluson keresztül

-   A szükséges hozzáférés-vezérlési stratégia a szervezeti adatbesorolásnak megfelelően

-   Az Azure Native és a harmadik féltől származó adatvédelmi képességek használata

-   Adattitkosítási követelmények átvitel közbeni és nyugalmi használati esetekben

-   Megfelelő titkosítási szabványok

További információkért tekintse meg a következő referenciákat:
- [Azure biztonsági architektúra – javaslat – tárolás, adatkezelés és titkosítás](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Az Azure biztonsági alapjai – Azure-adatbiztonság, titkosítás és tárolás](../security/fundamentals/encryption-overview.md)

- [Felhőalapú bevezetési keretrendszer – az Azure adatbiztonsági és-titkosítási ajánlott eljárásai](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure biztonsági teljesítményteszt – eszközkezelés](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure biztonsági teljesítményteszt – adatvédelem](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: vállalati szegmentálási stratégia definiálása 

**Útmutató**: hozzon létre egy nagyvállalati stratégiát, amely az eszközök hozzáférésének szegmentálására szolgál az identitás, a hálózat, az alkalmazás, az előfizetés, a felügyeleti csoport és más vezérlők kombinációjával.

Körültekintően kell elkülöníteni a biztonsági elkülönítés szükségességét annak érdekében, hogy lehetővé váljon a rendszerek napi működésének engedélyezése, amelyeknek kommunikálnia kell egymással, és az adathozzáférésre van szükségük.

Győződjön meg arról, hogy a szegmentálási stratégia következetesen van implementálva a különböző típusú vezérlőkben, beleértve a hálózati biztonságot, az identitás-és hozzáférési modelleket, valamint az alkalmazás engedély/hozzáférési modelljeit, valamint az emberi folyamatok vezérlés

- [Útmutató a szegmentálási stratégiához az Azure-ban (videó)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Útmutató a szegmentálási stratégiához az Azure-ban (dokumentum)](/security/compass/governance#enterprise-segmentation-strategy)

- [Hálózati szegmentálás igazítása nagyvállalati szegmentálási stratégiával](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: a biztonsági testhelyzet kezelési stratégiájának meghatározása

**Útmutató**: az egyes eszközökre és a szolgáltatásban üzemeltetett környezetre vonatkozó kockázatok folyamatos mérése és enyhítése. Rangsorolja a nagy értékű eszközöket és a nagy teljesítményű támadási felületeket, például a közzétett alkalmazásokat, a hálózati bejövő és kimenő pontokat, a felhasználói és rendszergazdai végpontokat stb.

- [Azure biztonsági teljesítményteszt – a testtartás és a sebezhetőségek kezelése](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: szervezeti szerepkörök, felelősségek és elszámoltathatóság igazítása

**Útmutató**: gondoskodjon arról, hogy a biztonsági szervezetében a szerepkörökkel és a felelősségekkel kapcsolatos egyértelmű stratégiát dokumentáljon és tájékoztasson. Rangsorolja a biztonsági döntések egyértelmű elszámoltathatóságát, mindenki számára elérhetővé teszi a megosztott felelősségi modellt, és megtanítja a technikai csapatokat a felhő védelméhez.

- [Azure Security – ajánlott eljárás 1 – emberek: a csapatok képzése a Felhőbeli biztonsági úton](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Security – ajánlott eljárás 2 – emberek: a csapatok képzése a Felhőbeli biztonsági technológiában](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security – ajánlott eljárás 3 – folyamat: elszámoltathatóság kiosztása a Felhőbeli biztonsági döntésekhez](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-5-define-network-security-strategy"></a>GS-5: hálózati biztonsági stratégia definiálása

**Útmutató**: hozzon létre egy Azure-alapú hálózati biztonsági megközelítést a szervezet általános biztonsági hozzáférés-vezérlési stratégiájának részeként.  

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

-   Központosított Hálózatkezelés és biztonsági felelősség

-   A virtuális hálózat szegmentálási modellje a nagyvállalati szegmentálási stratégiához igazodik

-   Szervizelési stratégia a különböző veszélyforrások és támadási helyzetekben

-   Az Internet Edge és a bejövő forgalom és a kimenő forgalom stratégiája

-   Hibrid felhő és helyszíni összekapcsolási stratégia

-   Naprakész hálózati biztonsági összetevők (például hálózati diagramok, hivatkozási hálózati architektúra)

További információkért tekintse meg a következő referenciákat:
- [Azure Security – ajánlott eljárás 11 – architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure biztonsági teljesítményteszt – hálózati biztonság](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Az Azure hálózati biztonság áttekintése](../security/fundamentals/network-overview.md)

- [Vállalati hálózati architektúra stratégia](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: identitás-és privilegizált hozzáférési stratégia meghatározása

**Útmutató**: hozzon létre egy Azure-identitást és egy emelt szintű hozzáférési megközelítést a szervezet általános biztonsági hozzáférés-vezérlési stratégiájának részeként.  

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

-   Központi identitás-és hitelesítési rendszer, valamint az egyéb belső és külső identitási rendszerekkel való kapcsolat

-   Erős hitelesítési módszerek különböző használati esetekben és kikötésekben

-   Magas jogosultsági szintű felhasználók védelme

-   Felhasználói tevékenységek rendellenességének figyelése és feldolgozása  

-   Felhasználói identitás és hozzáférés felülvizsgálata és egyeztetési folyamata

További információkért tekintse meg a következő referenciákat:

- [Azure biztonsági teljesítményteszt – Identitáskezelés](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure biztonsági teljesítményteszt – emelt szintű hozzáférés](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Security – ajánlott eljárás 11 – architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Az Azure Identity Management biztonsági áttekintése](../security/fundamentals/identity-management-overview.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: a naplózási és a veszélyforrások reagálási stratégiájának meghatározása

**Útmutató**: naplózási és veszélyforrási reagálási stratégia létrehozása a fenyegetések gyors észlelése és elhárítása érdekében a megfelelőségi követelmények teljesítése mellett. Kiváló minőségű riasztásokkal és zökkenőmentes tapasztalatokkal látja el az elemzőket, hogy az integráció és a manuális lépések helyett a fenyegetésekre összpontosítsanak. 

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

-   A biztonsági műveletek (SecOps) szervezet szerepköre és feladatai 

-   Egy jól definiált incidens-reagálási folyamat NIST vagy más iparági keretrendszerrel való összehangolása 

-   A fenyegetések észlelését, az incidensek megválaszolását és a megfelelőségi igényeket támogató napló rögzítése és megőrzése

-   A fenyegetésekkel kapcsolatos központosított láthatóság és korrelációs információk a SIEM, a natív Azure-képességek és más források használatával 

-   Kommunikációs és értesítési terv ügyfeleivel, szállítókkal és az érdekelt felekkel

-   Az Azure Native és a harmadik féltől származó platformok használata az incidensek kezelésére, mint például a naplózás és a veszélyforrások észlelése, a kriminalisztika, valamint a támadási problémák elhárítása és felszámolása

-   Az incidensek és az incidens utáni tevékenységek kezelésének folyamatai, például a tanulságok és a bizonyítékok megőrzése

További információkért tekintse meg a következő referenciákat:

- [Azure biztonsági teljesítményteszt – naplózás és fenyegetések észlelése](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure biztonsági teljesítményteszt – incidens válasza](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security – ajánlott eljárás 4 – folyamat. Incidensek frissítési folyamatainak frissítése a felhőben](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure bevezetési keretrendszer, naplózás és jelentéskészítési döntési útmutató](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Az Azure nagyvállalati szintű skálázása, kezelése és monitorozása](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure Security teljesítményteszt v2 áttekintése](/azure/security/benchmarks/overview) című témakört.
- További információ az [Azure biztonsági alaptervekről](/azure/security/benchmarks/security-baselines-overview)
