---
title: Az Azure biztonsági alapterve Azure Firewall Managerhez
description: A Azure Firewall Manager biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8d3288ddde87468f3b6382fab77b0097e350dde2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96171041"
---
# <a name="azure-security-baseline-for-azure-firewall-manager"></a>Az Azure biztonsági alapterve Azure Firewall Managerhez

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 2,0-es verziójáról](../security/benchmarks/overview.md) Azure Firewall Managerre vonatkozó útmutatást alkalmazza. Az Azure biztonsági teljesítménytesztje javaslatokat nyújt ahhoz, hogyan védheti meg felhőalapú megoldásait az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** és a Azure Firewall Managerre vonatkozó kapcsolódó útmutatás szerint van csoportosítva. A Azure Firewall-kezelőre nem alkalmazható **vezérlők** ki lettek zárva.

Ha szeretné megtekinteni, hogy a Azure Firewall Manager hogyan felel meg teljesen az Azure biztonsági Teljesítménytesztének, tekintse meg a [teljes Azure Firewall Manager biztonsági alapkonfiguráció-hozzárendelési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="identity-management"></a>Identitáskezelés

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Az Azure Active Directory szabványosítása központi identitáskezelési és hitelesítési rendszerként

**Útmutató**: a Azure Firewall Manager az Azure Active Directory (Azure ad) alapértelmezett identitás-és hozzáférés-kezelési szolgáltatásként használja. Egységesítse az Azure AD-t a szervezet identitás-és hozzáférés-kezelésének szabályozásához a ben:

- Microsoft Cloud erőforrások, például a Azure Portal, az Azure Storage, az Azure virtuális gép (Linux és Windows), a Azure Key Vault, a Pásti és az SaaS-alkalmazások.

- a szervezet erőforrásaiban, például az Azure-on vagy a vállalati hálózat erőforrásain lévő alkalmazásokban.

Kitüntetett figyelmet kell fordítani rá a cég vagy szervezet felhőalapú biztonsági gyakorlatában, hogy az Azure AD-t biztonságossá tegyék. Az Azure AD egy biztonságos azonosítási pontszámot biztosít, amellyel a Microsoft ajánlott eljárási javaslataihoz képest kiértékelheti a személyazonosság biztonsági helyzetét. A pontszám segít felmérni, hogy a használt konfiguráció milyen mértékben felel meg az ajánlott eljárásokkal kapcsolatos javaslatoknak, és javíthatja a cég vagy szervezet biztonsági állapotát.

Az Azure AD támogatja a külső identitást, amely lehetővé teszi a felhasználók számára, hogy Microsoft-fiók nélkül bejelentkezzenek alkalmazásaiba és erőforrásaiba külső identitásával.

- [Bérlet Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Külső identitás-szolgáltatók használata az alkalmazáshoz](/azure/active-directory/b2b/identity-providers)

- [Mi az identitások biztonságos pontszáma Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Azure AD egyszeri bejelentkezés (SSO) használata az alkalmazások hozzáféréséhez

**Útmutató**: a Azure Firewall Manager a Azure Active Directory használatával biztosítja az identitás-és hozzáférés-kezelést az Azure-erőforrásokhoz, a felhőalapú alkalmazásokhoz és a helyszíni alkalmazásokhoz. Ennek a hatóköre a vállalati identitásokra, például az alkalmazottakra, valamint a külső identitásokra, például a partnerekre, a szállítókra és a szolgáltatókra is kiterjed. Egyszeri bejelentkezéssel (single sign-on, SSO) kezelhető és védhető a szervezet adatainak és erőforrásainak hozzáférése a helyszínen és a felhőben. Az összes felhasználót, alkalmazást és eszközt az Azure AD-hez csatlakoztathatja a zökkenőmentes, biztonságos hozzáférés, a jobb átláthatóság és hatékonyabb kontroll érdekében.

- [SSO alkalmazásokhoz az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Erős hitelesítésvezérlők használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Firewall Manager olyan Azure Active Directory használ, amely támogatja a többtényezős hitelesítés (MFA) és az erős, jelszóval nem használható metódusok erős hitelesítési vezérlését.
- Többtényezős hitelesítés – engedélyezheti az Azure AD MFA-t, és követheti az Azure Security Center identitás- és hozzáférés-kezelési javaslatait, hogy az MFA beállítása során az ajánlott eljárásokat kövesse. Az MFA kikényszeríthető az összes felhasználón, bizonyos felhasználókon vagy felhasználónként a bejelentkezési feltételek és a kockázati tényezők alapján.
- Jelszó nélküli hitelesítés – három, jelszó nélküli hitelesítési lehetőség érhető el: a Vállalati Windows Hello, a Microsoft Authenticator alkalmazás és helyszíni hitelesítési módszerek, például intelligens kártyák.

A rendszergazda és a Kiemelt jogosultságú felhasználók számára győződjön meg arról, hogy az erős hitelesítési módszer legmagasabb szintje van használatban, majd ezt követi a megfelelő erős hitelesítési házirend kiosztása más felhasználók számára.

- [Az MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Az Azure Active Directory jelszó nélküli hitelesítési lehetőségeinek ismertetése](../active-directory/authentication/concept-authentication-passwordless.md)

- [Az Azure AD alapértelmezett jelszószabályzata](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [A nem megfelelő jelszavak használatának kiküszöbölése az Azure AD Password Protectionnel](../active-directory/authentication/concept-password-ban-bad.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Fiókok rendellenességeinek monitorozása és a hozzájuk kapcsolódó riasztások

**Útmutató**: a Azure Firewall Manager integrálva van a Azure Active Directory, amely a következő adatforrásokat biztosítja:
- Bejelentkezések – a bejelentkezési jelentés a felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információkat biztosít.
- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók rögzítik például az erőforrások módosításait az Azure AD-n belül, például a felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadását vagy eltávolítását.
- Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.
- Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

Ezek az adatforrások a Azure Monitor, az Azure Sentinel vagy a harmadik féltől származó SIEM rendszerekkel integrálhatók.

A tevékenység naplója nem támogatja a tűzfalszabályok csoportházirend-csoportokra vonatkozó szabályait, ez egy ismert probléma, és a jövőbeli frissítésekben kerül megcímzésre.

Az Azure Security Center riasztásokat is küldhet bizonyos gyanús tevékenységek észlelésekor. Ezek közé tartozik például a sikertelen hitelesítési kísérletek kiugróan magas száma vagy az előfizetésben talált elavult fiókok.

Az Azure Advanced Threat Protection (ATP) olyan biztonsági megoldás, amely Active Directory-jelek használatával tudja azonosítani, észlelni és kivizsgálni a súlyos fenyegetéseket, a sérült identitásokat és a rosszindulatú belső műveleteket.

- [Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directoryban](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Ismert problémák a Azure Firewall Managerben](overview.md#known-issues)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: az Azure erőforrás-hozzáférés korlátozása feltételek alapján

**Útmutató**: a Azure Firewall Manager támogatja az Azure Active Directory (Azure ad) feltételes hozzáférését egy részletesebb hozzáférés-vezérléshez a felhasználó által definiált feltételek alapján, például olyan IP-tartományokból származó felhasználói bejelentkezéseket, amelyeknek az MFA-t kell használniuk a bejelentkezéshez. A részletes hitelesítési munkamenet-kezelési szabályzat különböző használati esetekben is használható.

- [Az Azure feltételes hozzáférés áttekintése](../active-directory/conditional-access/overview.md)

- [Általános feltételes hozzáférési szabályzatok](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [A hitelesítési munkamenet felügyeletének konfigurálása feltételes hozzáféréssel](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: emelt szintű hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Magas jogosultsági szintű felhasználók védelme és korlátozása

**Útmutató**: a Azure Firewall Manager a Azure Active Directory (Azure ad) szolgáltatást használja az identitáshoz és a hozzáféréshez. A legfontosabb beépített szerepkörök az Azure AD globális rendszergazda és a Kiemelt szerepkörű rendszergazda, mivel a két szerepkörhöz hozzárendelt felhasználók delegálhatja a rendszergazdai szerepköröket:
- Globális rendszergazda/vállalati rendszergazda: az ehhez a szerepkörhöz tartozó felhasználók hozzáférhetnek az Azure AD összes felügyeleti funkciójához, valamint az Azure AD-identitásokat használó szolgáltatásokhoz.
- Kiemelt szerepkörű rendszergazda: az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a szerepkör-hozzárendeléseket az Azure AD-ben, valamint a Azure AD Privileged Identity Managementon (PIM) belül is. Emellett ez a szerepkör lehetővé teszi a PIM és a felügyeleti egységek valamennyi aspektusának kezelését.

Előfordulhat, hogy más kritikus szerepkörökre is szükség van, amelyeket az egyéni szerepkörök hozzárendelésekor kell alkalmazni. Emellett előfordulhat, hogy hasonló vezérlőket szeretne alkalmazni a kritikus fontosságú üzleti eszközök rendszergazdai fiókjára.

Az Azure AD Privileged Identity Management (PIM) használatával engedélyezheti az Azure-erőforrások és az Azure AD igény szerinti (just-in-time, JIT) jogosultságú hozzáférését. A JIT ideiglenes engedélyeket biztosít az érintett feladatok végrehajtásához, csak annyi időre, ameddig a felhasználóknak erre szükségük van. A PIM biztonsági riasztásokat is képes kiadni, amikor gyanús vagy nem biztonságos tevékenységeket észlel az Azure AD-szervezetben.

- [Rendszergazdai szerepköri engedélyek az Azure AD-ben](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Az Azure Privileged Identity Management biztonsági riasztásainak használata](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: az üzleti szempontból kritikus fontosságú rendszerek rendszergazdai hozzáférésének korlátozása

**Útmutató**: egyéni Azure RBAC-szerepkörök használata a Azure Firewall házirendhez tartozó szabálykészlet-csoportok elérésének elkülönítéséhez. Az Azure egyéni szerepkör-definíciójának használatával megakadályozhatja a véletlen alapházirend-eltávolítást, és szelektív hozzáférést biztosíthat a szabályok gyűjtési csoportjaihoz egy előfizetésen vagy erőforráscsoporton belül.

Győződjön meg arról, hogy az üzleti szempontból kritikus rendszerekhez való rendszergazdai hozzáféréssel rendelkező felügyeleti, identitási és biztonsági rendszerekhez is korlátozza a hozzáférést. Azok a támadók, akik veszélyeztetik ezeket a felügyeleti és biztonsági rendszereket, azonnal weaponize azokat az üzleti szempontból kritikus fontosságú eszközök biztonsága érdekében.

A hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani, hogy biztosítsa a konzisztens hozzáférés-vezérlést.

- [Szabály hierarch definiálása Azure Firewall házirend használatával](rule-hierarchy.md)

- [Azure-összetevők és-hivatkozási modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Felügyeleti csoport elérése](../governance/management-groups/overview.md#management-group-access)

- [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Firewall Manager Azure Active Directory-(Azure ad-) fiókokat használ az erőforrások kezeléséhez. Rendszeresen tekintse át a felhasználói fiókokat és a hozzáférés-hozzárendelést, hogy a fiókok és a hozzáférésük érvényes legyen. Az Azure AD hozzáférési felülvizsgálatok segítségével áttekintheti a csoporttagság, a vállalati alkalmazásokhoz való hozzáférés és a szerepkör-hozzárendelések áttekintését. Az Azure AD jelentéskészítési szolgáltatása lehetővé teszi, hogy a naplók segítséget nyújtsanak az elavult fiókok felderítéséhez. A felülvizsgálati folyamat megkönnyítése érdekében a Azure AD Privileged Identity Management használatával hozzáférési felülvizsgálati jelentési munkafolyamatot is létrehozhat.

Emellett az Azure Privileged Identity Management is beállítható úgy, hogy riasztást hozzon létre túl sok rendszergazdai fiók létrehozásakor, valamint a elavult vagy helytelenül konfigurált rendszergazdai fiókok azonosításához.

Bizonyos Azure-szolgáltatások támogatják a helyi felhasználókat és szerepköröket, amelyek nem az Azure AD-n keresztül kezelhetők. Ezeket a felhasználókat külön kell kezelnie.

- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: vészhelyzeti hozzáférés beállítása az Azure AD-ben

**Útmutató**: a Azure Firewall Manager Azure Active Directory használ a szolgáltatást kezelő felhasználók hitelesítéséhez. Ha nem szeretné megakadályozni, hogy véletlenül kizárja az Azure AD-szervezetét, állítson be egy vészhelyzeti hozzáférési fiókot a hozzáféréshez, ha a normál rendszergazdai fiókok nem használhatók. A vészhelyzeti hozzáférési fiókok általában magas jogosultsággal rendelkeznek, és nem rendelhetők hozzá konkrét személyekhez. A vészhelyzeti hozzáférési fiókok olyan vészhelyzeti vagy "break Glass" forgatókönyvekre korlátozódnak, amelyekben nem használhatók normál rendszergazdai fiókok.

Győződjön meg arról, hogy a vészhelyzeti hozzáférési fiókokhoz tartozó hitelesítő adatok (például jelszó, tanúsítvány vagy intelligens kártya) biztonságosak és ismertek, csak azokra a személyekre, akik jogosultak arra, hogy csak vészhelyzetben használják őket.

- [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pa-5-automate-entitlement-management"></a>PA-5: a jogosultságok kezelésének automatizálása 

**Útmutató**: a Azure Firewall Manager integrálva van a Azure Active Directoryekkel az erőforrások kezeléséhez. Az Azure AD-jogosultságok kezelési funkciói segítségével automatizálhatja a hozzáférési kérelmek munkafolyamatait, beleértve a hozzáférési hozzárendeléseket, az értékeléseket és a lejáratokat. A kettős vagy többfázisú jóváhagyás is támogatott.

- [Az Azure AD hozzáférési felülvizsgálatai](../active-directory/governance/access-reviews-overview.md)

- [Mi az Azure AD-jogosultságok kezelése?](../active-directory/governance/entitlement-management-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: emelt szintű hozzáférésű munkaállomások használata

**Útmutató**: a biztonságos, elkülönített munkaállomások kritikus fontosságúak a bizalmas szerepkörök, például a rendszergazdák, a fejlesztők és a kritikus fontosságú szolgáltatók biztonsága szempontjából. A fokozottan biztonságos felhasználói munkaállomások használatával felügyeleti feladatokat hajthat végre a Azure Firewall Manager-erőforrásokkal éles környezetben. A biztonságos és felügyelt felhasználói munkaállomás felügyeleti feladatokhoz való telepítéséhez Azure Active Directory, a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) és/vagy Microsoft Intune használható. A biztonságos munkaállomások központilag felügyelhetők a biztonságos konfiguráció kikényszerítéséhez, beleértve az erős hitelesítést, a szoftver-és a hardver-alapterveket, valamint a korlátozott logikai és hálózati hozzáférést is.

- [Az emelt szintű hozzáférésű munkaállomások ismertetése](../active-directory/devices/concept-azure-managed-workstation.md)

- [Emelt szintű hozzáférési munkaállomás üzembe helyezése](../active-directory/devices/howto-azure-managed-workstation.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: A Just Enough Administration (legkisebb jogosultsági alapelv) követése 

**Útmutató**: a Azure Firewall Manager integrálva van az Azure szerepköralapú hozzáférés-vezérléssel (RBAC) az erőforrások kezeléséhez. Az Azure RBAC lehetővé teszi, hogy szerepkörök hozzárendelésével felügyelje az Azure-erőforrások hozzáférését. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre meghatározott beépített szerepkörök tartoznak, és ezek a szerepkörök olyan eszközökkel leltározhatók vagy kérdezhetők le, mint az Azure CLI, az Azure PowerShell vagy az Azure Portal. Az erőforrásokhoz az Azure RBAC-n keresztül hozzárendelt jogosultságokat mindig arra kell korlátozni, amit a szerepkörök megkövetelnek. Ez a megközelítés megfelel az Azure AD Privileged Identity Management (PIM) igény szerinti (JIT) módszerének, és a jogosultságok rendszeres felülvizsgálatával jár.

A beépített szerepkörökkel engedélyeket oszthat ki, és csak akkor kell egyéni szerepköröket létrehoznia, ha szükséges.

- [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../role-based-access-control/overview.md) 

- [Az RBAC konfigurálása az Azure-ban](../role-based-access-control/role-assignments-portal.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: adatvédelem](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: A bizalmas adatok védelme

**Útmutató**: a bizalmas adatok, például a Azure Firewall házirend konfigurációs adatainak védelme azáltal, hogy az azure szerepköralapú Access Control (Azure RBAC), a hálózati hozzáférés-vezérlés és az Azure-szolgáltatások adott vezérlőinek használatával korlátozza a hozzáférést.

A következetes hozzáférés-vezérlés biztosításához a hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani. A vállalati szegmentálási stratégiát a bizalmas vagy üzleti szempontból kritikus fontosságú adatok és rendszerek helyének tudatában kell kialakítani.

A Microsoft által felügyelt mögöttes platformon a Microsoft az összes ügyféltartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatainak elvesztése és mások általi hozzáférése ellen. Ahhoz, hogy az ügyféladatok az Azure-on belül biztonságban maradjanak, a Microsoft implementált néhány alapértelmezett adatvédelmi vezérlőt és képességet.

- [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: A bizalmas adatok jogosulatlan átvitelének monitorozása

**Útmutató**: Azure Firewall házirend-erőforrások csak a hitelesített felhasználók számára érhetők el. Az ügyfeleknek meg kell győződniük arról, hogy csak a jogosult felhasználók férhetnek hozzá az adatforráshoz.

- [Egyéni szerepkörök létrehozása a szabályok gyűjtési csoportjaihoz való hozzáféréshez](rule-hierarchy.md#create-custom-roles-to-access-the-rule-collection-groups)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: bizalmas adatok titkosítása az átvitel során

**Útmutató**: a hozzáférés-vezérlés kiegészítéseként az átvitt adatoknak védeniük kell a "sávon kívüli" támadásoktól (például Traffic Capture) a titkosítás használatával, hogy a támadók ne tudják egyszerűen beolvasni vagy módosítani az adatokat.

A Azure Firewall Manager támogatja az adattitkosítást a TLS 2.0-s vagy újabb verziókban.

Habár ez nem kötelező a magánhálózaton lévő forgalom esetében, ez kritikus fontosságú a külső és a nyilvános hálózatok forgalmában. HTTP-forgalom esetén győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó összes ügyfél egyeztetheti a TLS v 1.2-es vagy újabb verzióját. A Távoli felügyelet érdekében titkosítatlan protokoll helyett használja az SSH-t (Linuxon) vagy RDP/TLS (Windows rendszeren). Az elavult SSL-, TLS-és SSH-verziók és protokollok, valamint a gyenge titkosítások le lesznek tiltva.

Alapértelmezés szerint az Azure titkosítást biztosít az Azure-adatközpontok közötti adatátvitelhez.

- [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Információk a TLS biztonságáról](/security/engineering/solving-tls1-problem) 

- [Kettős titkosítás az Azure-beli adatforgalomban](../security/fundamentals/double-encryption.md#data-in-transit)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: eszközkezelés](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Gondoskodás arról, hogy a biztonsági csapat átlássa az adategységek kockázatait

**Útmutató**: Győződjön meg arról, hogy a biztonsági csapatok biztonsági olvasó engedélyekkel rendelkeznek az Azure-bérlőben és -előfizetésekben, hogy monitorozni tudják a biztonsági kockázatokat az Azure Security Centerrel. 

A biztonsági kockázatok monitorozása a biztonsági csapat felelősségi köreinek struktúrájától függően egy központi biztonsági csapat vagy egy helyi csapat felelőssége lehet. A biztonsági megállapításokat és kockázatokat azonban mindig központilag kell összesíteni egy szervezeten belül. 

A biztonsági olvasó engedélyek széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoport), vagy a hatókör alkalmazható adott felügyeleti csoportokra vagy előfizetésekre. 

További engedélyek szükségesek lehetnek a számítási feladatok és szolgáltatások láthatóságának megismeréséhez. 

- [A biztonsági olvasó szerepkör áttekintése](../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure-beli felügyeleti csoportok áttekintése](../governance/management-groups/overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: gondoskodjon arról, hogy a biztonsági csapat hozzáférjen az eszközök leltárához és metaadataihoz

**Útmutató**: gondoskodjon arról, hogy a biztonsági csapatok hozzáférjenek a Azure Firewall Manager-eszközök Azure-beli folyamatosan frissített leltárához. Az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetések összes Azure Firewall erőforrását, beleértve az Azure-szolgáltatásokat, az alkalmazásokat és a hálózati erőforrásokat is.

Címkéket alkalmazhat az Azure-erőforrások,-erőforráscsoportok és-előfizetések számára, hogy logikailag szervezze őket egy besorolásba. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md) 

- [További információ az eszközök címkézéséről: erőforrás-elnevezési és címkézési döntési útmutató](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával naplózhatja és korlátozhatja, hogy a felhasználók mely szolgáltatásokat tudják kiépíteni a környezetében. Ez magában foglalja a Azure Firewall erőforrások üzembe helyezésének engedélyezését vagy megtagadását. Az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetésen belüli erőforrásokat. A Azure Monitor használatával olyan szabályokat is létrehozhat, amelyek riasztásokat aktiválnak, ha a rendszer nem jóváhagyott szolgáltatást észlel.

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Az objektuméletciklus-kezelés biztonságának garantálása

**Útmutató**: távolítsa el Azure Firewall Manager erőforrásait, ha már nincs szükségük a támadási felület minimalizálására. A felhasználók a Azure Portal, a CLI vagy a REST API-k segítségével kezelhetik Azure Firewall Manager-erőforrásait.

- [Azure Firewall szabályzat parancssori felület](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/policy?view=azure-cli-latest&amp;preserve-view=true)

- [Azure hálózati CLI](https://docs.microsoft.com/powershell/module/az.network/?view=azps-5.1.0#networking&amp;preserve-view=true)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: a Azure Firewall Manager integrálva van Azure Active Directory (Azure ad) identitás-és hitelesítési szolgáltatásokhoz. Az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure erőforrás-kezelővel való interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetésészlelés

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: Naplózás és fenyegetésészlelés](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: az Azure-erőforrások fenyegetés-észlelésének engedélyezése

**Útmutató**: a tűzfal-szabályzatok által létrehozott vagy a Siem-hez kapcsolódó továbbítási tevékenységek naplója, amely az egyéni veszélyforrások észlelésének beállítására használható. Győződjön meg arról, hogy a különböző típusú Azure-eszközöket figyeli a lehetséges fenyegetések és rendellenességek tekintetében. A magas színvonalú riasztások beszerzésével csökkentheti a hamis pozitív értéket az elemzők számára a rendezéshez. A riasztások naplózási adatokból, ügynökökből vagy más adatokból is származnak.

- [Egyéni elemzési szabályok létrehozása a fenyegetések észleléséhez](../sentinel/tutorial-detect-threats-custom.md) 

- [A Cyber Threat Intelligence és az Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

- [Az Azure Firewall naplói és metrikái](../firewall/firewall-diagnostics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Fenyegetésészlelés engedélyezése az Azure-beli identitás- és hozzáférés-kezeléshez

**Útmutató**: Az Azure AD az alábbi felhasználói naplókat tartalmazza. Ezek megtekinthetők az Azure AD-jelentéskészítésben, vagy integrálhatók az Azure Monitorral, Azure Sentinellel vagy más SIEM/monitorozási eszközzel a kifinomultabb monitorozáshoz és elemzéshez:
- Bejelentkezések – a bejelentkezési jelentés a felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információkat biztosít.

- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók rögzítik például az erőforrások módosításait az Azure AD-n belül, például a felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadását vagy eltávolítását.
- Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.
- Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

Az Azure Security Center riasztásokat is küldhet bizonyos gyanús tevékenységek észlelésekor. Ezek közé tartozik például a sikertelen hitelesítési kísérletek kiugróan magas száma vagy az előfizetésben talált elavult fiókok. A biztonsági állapot alapszintű monitorozása mellett Azure Security Center fenyegetésvédelmi modulja részletesebb biztonsági riasztásokat is gyűjthet az egyes Azure-beli számítási erőforrásokból (virtuális gépek, tárolók, App Service), adatforrásokból (SQL DB és Storage) és az Azure-szolgáltatásrétegekből. Ezzel a képességgel megtekintheti az egyes erőforrásokon belüli fiókok rendellenességeit.

A tevékenység naplója nem támogatja a tűzfalszabályok csoportházirend-csoportokra vonatkozó szabályait, ez egy ismert probléma, és a jövőbeli frissítésekben kerül megcímzésre.

- [Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directoryban](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Az Azure Identity Protection engedélyezése](../active-directory/identity-protection/overview-identity-protection.md)

- [Fenyegetésvédelem az Azure Security Centerben](/azure/security-center/threat-protection)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a automatikusan elérhető tevékenység-naplók tartalmazzák a tűzfalszabályok erőforrásainak összes írási műveletét (Put, post, DELETE), kivéve az olvasási MŰVELETEKET (Get). A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

A tevékenység naplója nem támogatja a tűzfalszabályok csoportházirend-csoportokra vonatkozó szabályait, ez egy ismert probléma, és a jövőbeli frissítésekben kerül megcímzésre.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/platform/platform-logs-overview.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: a biztonsági naplók központosított kezelése és elemzése

**Útmutató**: a korreláció engedélyezéséhez központosítsa a naplózási tárolást és az elemzést. Minden naplózási forrás esetében ellenőrizze, hogy rendelkezik-e az adatok tulajdonosával, a hozzáférési útmutatással, a tárolási hellyel, az adatok feldolgozásához és eléréséhez, valamint az adatmegőrzési követelményekhez használt eszközökhöz.

Győződjön meg arról, hogy integrálja az Azure-tevékenységek naplóit a központi naplózásba. Naplók betöltése Azure Monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz.

Továbbá engedélyezze és helyezze üzembe a napló adatait az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek.

Számos szervezet úgy dönt, hogy a gyakran használt és az Azure-tárolót használja a ritkábban használt adatokhoz az Azure Sentinel használatával.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: a naplózási tár megőrzésének konfigurálása

**Útmutató**: gondoskodjon arról, hogy a tűzfal-naplók tárolására használt összes Storage-fiók vagy log Analytics-munkaterület a szervezet megfelelőségi szabályainak megfelelően állítsa be a napló megőrzési időtartamát.

Azure Monitor az Log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint állíthatja be. Az Azure Storage, a Data Lake vagy a Log Analytics munkaterület-fiókokat használhatja a hosszú távú és az archiválási tároláshoz.

- [Log Analytics munkaterület megőrzési időtartamának konfigurálása](../azure-monitor/platform/manage-cost-storage.md)

- [Erőforrás-naplók tárolása Azure Storage-fiókban](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: Incidensmegoldás](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Előkészítés – incidensmegoldási folyamat frissítése az Azure-ban

**Útmutató**: Győződjön meg arról, hogy a szervezet rendelkezik a biztonsági incidensek megoldásához szükséges folyamatokkal, frissítette ezeket a folyamatokat az Azure-ral való használatra, valamint rendszeresen teszteli őket, hogy készen álljanak a használatra.

- [A biztonság implementálása a vállalati környezetben](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Incidensmegoldás – gyorsútmutató](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Előkészítés – incidensértesítések beállítása

**Útmutató**: Kapcsolattartási adatokat állíthat be a biztonsági incidensekhez az Azure Security Centerben. A Microsoft arra használja ezeket a kapcsolattartási adatokat, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy egy jogosulatlan vagy illetéktelen fél hozzáfért az Ön adataihoz. Az incidensekkel kapcsolatos riasztások és értesítések testreszabhatók a különböző Azure-szolgáltatásokban az incidensmegoldási követelmények alapján. 

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Észlelés és elemzés – incidensek létrehozása kiváló minőségű riasztások alapján

**Útmutató**: Ellenőrizze, hogy van-e olyan folyamat, amely kiváló minőségű riasztásokat hoz létre, és mérje fel a riasztások minőségét. Ezzel tanulhat a korábbi incidensekből, és megadhatja a riasztások fontossági sorrendjét az elemzők számára, akik így nem pazarolnak időt a vakriasztásokra. 

A kiváló minőségű riasztások a múltbeli incidensek, a hitelesített közösségi források és a riasztások létrehozásához és tisztításához tervezett eszközök alapján hozhatók létre a különböző jelforrások elutasításával és korrelációs megoldásával. 

Azure Security Center kiváló minőségű riasztásokat biztosít számos Azure-eszközön. Az ASC adatösszekötővel streamelheti a riasztásokat az Azure Sentinelbe. Az Azure Sentinellel speciális riasztási szabályokat hozhat létre, amelyekkel automatikusan hozhat létre incidenseket a vizsgálatokhoz. 

Az exportálási funkcióval exportálhatja az Azure Security Center riasztásait és javaslatait, amelyek segítenek az Azure-erőforrások kockázatainak azonosításában. A riasztásokat és javaslatokat manuálisan vagy folyamatosan is exportálhatja.

- [Az exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Észlelés és elemzés – incidens vizsgálata

**Útmutató**: Győződjön meg arról, hogy az elemzők különböző adatforrásokat kérdezhetnek le és használhatnak a lehetséges incidensek vizsgálata során, és átfogó képet adhatnak a történtekről. A vakfoltok elkerülése érdekében különböző naplókat kell gyűjteni a potenciális támadók tevékenységének követéséhez a támadási útvonalon.  A megállapítások és eredmények rögzítéséről is érdemes gondoskodnia, hogy később más elemzők referenciaként használhassák őket.  

A vizsgálathoz használt adatforrások közé tartoznak azok a központi naplózási források, amelyeket a rendszer már gyűjt a hatókörbeli szolgáltatásokból és a futó rendszerekről, de a következők is lehetnek:

- Hálózati adatok – az Azure Network Watcher, az Azure Monitor és a hálózati biztonsági csoportok forgalmi naplóinak használata a hálózati forgalom naplóinak és más elemzési adatok rögzítéséhez. 

- A futó rendszerek pillanatképei: 

    - Az Azure-beli virtuális gép pillanatkép-készítési funkciójával pillanatképet készíthet a futó rendszer lemezéről. 

    - Az operációs rendszer natív memóriakép-készítési funkciójával pillanatképet készíthet a futó rendszer memóriájáról.

    - Az Azure-szolgáltatások vagy saját szoftvere pillanatkép-készítési funkciójával pillanatképeket készíthet a futó rendszerekről.

Az Azure Sentinel széles körű adatelemzést tesz lehetővé szinte bármely naplózási forráson és egy esetfelügyeleti portálon az incidensek teljes életciklusának kezeléséhez. A vizsgálatok során kapott információk társíthatók egy incidenssel nyomkövetés és jelentéskészítés céljából. 

- [Windows rendszerű gép lemezének pillanatképe](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux rendszerű gép lemezének pillanatképe](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure-támogatás – diagnosztikai információk és memóriaképek gyűjtése](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidensek vizsgálata az Azure Sentinellel](../sentinel/tutorial-investigate-cases.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Észlelés és elemzés – incidensek rangsorolása

**Útmutató**: Megadhat arra vonatkozó információkat az elemzők számára, hogy melyik incidensekre kell először összpontosítaniuk a súlyosság és az objektum bizalmassága alapján. 

Az Azure Security Center súlyossági szintet rendel az egyes riasztásokhoz, és segít rangsorolni, hogy melyik riasztásokat kell először kivizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett a címkék használatával megjelölheti az erőforrásokat, és létrehozhat egy elnevezési rendszert az Azure-erőforrások, különösen a bizalmas adatok feldolgozását végző erőforrások azonosításához és kategorizálásához.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Elkülönítés, megsemmisítés és helyreállítás – incidensek kezelésének automatizálása

**Útmutató**: Automatizálhatja a manuális, ismétlődő feladatokat a válaszidő lerövidítéséhez és az elemzők terheinek csökkentéséhez. A manuális feladatok végrehajtása hosszabb időt vesz igénybe, lassítja az egyes incidensek kezelését, és csökkenti az egyetlen elemző által kezelhető incidensek számát. A manuális feladatok ki is merítik az elemzőket, ami növeli az emberi hiba lehetőségét, és az elemzők kevésbé tudnak hatékonyan összpontosítani az összetett feladatokra. Az Azure Security Center és az Azure Sentinel munkafolyamat-automatizálási funkcióival automatikusan indíthat el műveleteket és futtathat forgatókönyveket, hogy reagáljon a bejövő biztonsági riasztásokra. A forgatókönyv műveleteket hajt végre, például értesítéseket küld, fiókokat tilt le, és elkülöníti a problémás hálózatokat. 

- [Munkafolyamat-automatizálás konfigurálása a Security Centerben](../security-center/workflow-automation.md)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Security Centerben](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Sentinelben](../sentinel/tutorial-respond-threats-playbook.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="posture-and-vulnerability-management"></a>Állapot- és biztonságirés-kezelés

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: Állapot- és biztonságirés-kezelés](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: biztonságos konfigurációk létrehozása az Azure-szolgáltatásokhoz 

**Útmutató**: Azure Firewall Manager-erőforrások üzembe helyezésének és konfigurálásának automatizálása és konfigurálása a környezetekben olyan mechanizmusok használatával, mint például a következők: Azure Resource Manager sablonok, Azure RBAC-vezérlők és Azure Policy. Adja meg a Azure Firewall Manager erőforrásainak biztonságos konfigurációit az üzembe helyezés során, majd az "Azure. Network" névtér Aliasok használatával határozza meg az egyéni Azure Policy-definíciókat.

- [Azure Firewall házirend-sablon referenciája](/azure/templates/microsoft.network/firewallpolicies)

- [Azure Firewall szabályzat parancssori felület](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/policy?view=azure-cli-latest&amp;preserve-view=true)

- [A guardrails megvalósításának szemléltetése a nagyvállalati szintű kirakodási zónában](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: biztonságos konfigurációk fenntartása az Azure-szolgáltatásokhoz

**Útmutató**: a Azure Firewall Manager Azure Resource Manager-alapú sablonokat és a konfigurációs beállítások kényszerítését Azure Policy használatával támogatja. Az "Azure. Network" névtérben az Aliasok használatával naplózhatja és Azure Firewall érvényesítheti az egyéni Azure Policy-definíciókat.

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

- [Azure Firewall házirend-sablon referenciája](/azure/templates/microsoft.network/firewallpolicies)

- [A guardrails megvalósításának szemléltetése a nagyvállalati szintű kirakodási zónában](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: biztonságos konfigurációk létrehozása számítási erőforrásokhoz

**Útmutató**: nem alkalmazható; Azure Firewall Manager a tűzfal-vezérlési sík felügyeleti szolgáltatás, és nem teszi elérhetővé a mögöttes szolgáltatás számítási infrastruktúráját az ügyfelek számára a konfiguráláshoz.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Támadások rendszeres szimulálása

**Útmutató**: Szükség szerint behatolási teszteket vagy támadásszimulációs tevékenységeket hajthat végre az Azure-erőforrásokon, és gondoskodhat az észlelt kritikus biztonsági rések javításáról.
Kövesse a Microsoft Cloud behatolástesztelési szabályait, így biztosíthatja, hogy a behatolási tesztek nem sértik meg a Microsoft-szabályzatokat. Használja a Microsoft stratégiáját, és hajtson végre támadásszimulációt, valamint végezze el a Microsoft által felügyelt felhő-infrastruktúra, szolgáltatások és alkalmazások éles webhelyen történő behatolástesztelését.

- [Behatolástesztelés az Azure-ban](../security/fundamentals/pen-testing.md)

- [Behatolástesztelési szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud – támadásszimuláció](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

## <a name="backup-and-recovery"></a>Biztonsági másolat és helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: biztonsági mentés és helyreállítás](/azure/security/benchmarks/security-controls-v2-backup-recovery)című témakörben talál.*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: rendszeres automatikus biztonsági mentések biztosítása

**Útmutató**: a Azure Firewall Manager nem rendelkezik az ügyfelek rendszerbiztonsági mentésének fogalmával, a mögöttes infrastruktúrát a Microsoft kezeli.

Erőforrás-konfigurációs biztonsági másolatok esetén a Azure Resource Manager használatával exportálhatja a tűzfal-házirendeket és a kapcsolódó erőforrásokat egy olyan JavaScript Object Notation (JSON) sablonba, amely konfigurációs biztonsági másolatként használható. A tűzfal-házirendek konfigurációit a Azure Firewall exportálás sablon használatával is exportálhatja Azure Portalból. A Azure Automation használatával bármilyen egyéni biztonsági mentési parancsfájlt futtathat, hogy automatikusan rögzítse a Azure Firewall Manager-erőforrások erőforrás-konfigurációit.

- [Biztonságos virtuális központ üzembe helyezése sablon használatával](quick-secure-virtual-hub.md)

- [A Microsoft tűzfal házirend-sablonjának referenciája](/azure/templates/microsoft.network/firewallpolicies)

- [Tudnivalók Azure Automation](../automation/automation-intro.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a Azure Firewall Manager nem rendelkezik az ügyfelekkel szemben álló rendszerbiztonsági mentés fogalmával. Az exportált Azure Firewall Manager-erőforrásokhoz a Azure Resource Manager sablonfájlok használatával rendszeresen végezzen visszaállítást.

- [Biztonságos virtuális központ üzembe helyezése Azure Resource Manager-sablonok használatával](quick-secure-virtual-hub.md)

- [A Microsoft tűzfal házirend-sablonjának referenciája](/azure/templates/microsoft.network/firewallpolicies)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="governance-and-strategy"></a>Irányítás és stratégia

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: Irányítás és stratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Objektumkezelési és adatvédelmi stratégia meghatározása 

**Útmutató**: Győződjön meg arról, hogy egyértelmű stratégiát dokumentál és kommunikál a rendszerek és adatok folyamatos monitorozásához és védelméhez. Priorizálja az üzletileg kritikus adatok és rendszerek felderítését, értékelését, védelmét és monitorozását. 

Ennek a stratégiának tartalmaznia kell az alábbi elemek dokumentált útmutatóit, szabályzatait és szabványait: 

-   Adatbesorolási szabvány az üzleti kockázatokkal összhangban

-   A biztonsági szervezet betekintése a kockázatokba és az objektumleltárba 

-   A biztonsági szervezet jóváhagyása a használandó Azure-szolgáltatásokhoz 

-   Az objektumok biztonsága életciklusuk során

-   A szükséges hozzáférés-vezérlési stratégia a szervezeti adatbesorolásnak megfelelően

-   Az Azure Native és a harmadik féltől származó adatvédelmi képességek használata

-   Adattitkosítási követelmények a használatban lévő és a jelenleg nem használt használati esetekhez

-   Megfelelő titkosítási szabványok

További információkért tekintse meg az alábbi hivatkozásokat:
- [Az Azure biztonsági architektúrájára vonatkozó javaslatok – Tárolás, adatok és titkosítás](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Az Azure biztonsági alapjai – Az Azure-beli adatok biztonsága, titkosítása és tárolása](../security/fundamentals/encryption-overview.md)

- [Felhőbevezetési keretrendszer – Az Azure adatbiztonsággal és titkosítással kapcsolatos ajánlott eljárásai](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure-biztonsági teljesítményteszt – Objektumkezelés](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure-biztonsági teljesítményteszt – Adatvédelem](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Vállalati szegmentálási stratégia meghatározása 

**Útmutató**: Hozzon létre egy vállalati szintű stratégiát az objektumokhoz való hozzáférés szegmentálásához identitás, hálózat, alkalmazás, előfizetés, felügyeleti csoport és egyéb vezérlők kombinációjának használatával.

Ügyeljen az egyensúlyra a biztonsági elkülönítés és az egymással kommunikáló, az adatok elérését biztosító rendszerek napi működése között.

Győződjön meg arról, hogy a szegmentálási stratégia következetesen van implementálva a különböző típusú vezérlőkben, beleértve a hálózati biztonságot, az identitás- és hozzáférési modelleket, az alkalmazásengedély- és hozzáférési modelleket, valamint az emberi folyamatvezérlést.

- [Útmutató a szegmentálási stratégiához az Azure-ban (videó)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Útmutató a szegmentálási stratégiához az Azure-ban (dokumentum)](/security/compass/governance#enterprise-segmentation-strategy)

- [A hálózati szegmentálás igazítása a vállalati szegmentálási stratégiához](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Biztonsági helyzet kezelési stratégiájának meghatározása

**Útmutató**: Folyamatosan mérje fel és mérsékelje az egyes objektumok és az ezeket tartalmazó környezetek kockázatait. Priorizálja a nagy értékű objektumokat és a támadásoknak nagy mértékben kitett felületeket, például a közzétett alkalmazásokat, a hálózat bemeneti és kimeneti pontjait és a felhasználói és rendszergazdai végpontokat.

- [Azure-biztonsági teljesítményteszt – Állapot- és biztonságirés-kezelés](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Szervezeti szerepkörök, felelősségek és elszámoltathatóság összehangolása

**Útmutató**: Győződjön meg arról, hogy egyértelmű stratégiát dokumentál és kommunikál a biztonsági szervezet szerepköreivel és felelősségeivel kapcsolatban. Biztosítson egyértelmű elszámoltathatóságot a biztonsági döntésekhez, tájékoztasson mindenkit a megosztott felelősségi modellről, és tájékoztassa a műszaki csapatokat a technológiáról a felhő védelme érdekében.

- [Az Azure ajánlott biztonsági eljárásai 1 – Személyek: A csapatok tájékoztatása a felhőbiztonság felé vezető útról](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Az Azure ajánlott biztonsági eljárásai 2 – Személyek: A csapatok tájékoztatása a felhőbiztonsági technológiáról](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Az Azure ajánlott biztonsági eljárásai 3 – Folyamat: A felhőbiztonsági döntésekért való elszámoltathatóság biztosítása](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Hálózati biztonsági stratégia meghatározása

**Útmutató**: Hozzon létre egy Azure-beli hálózati biztonsági megközelítést a szervezet általános biztonsági hozzáférés-vezérlési stratégiájának részeként.  

Ennek a stratégiának tartalmaznia kell az alábbi elemek dokumentált útmutatóit, szabályzatait és szabványait: 

-   Központosított hálózatkezelés és biztonsági felelősség

-   A vállalat szegmentálási stratégiájához igazított virtuálishálózat-szegmentálási modell

-   Javítási stratégia a különböző fenyegetési és támadási forgatókönyvekben

-   Internetes peremhálózatra, valamint bejövő és kimenő adatforgalomra vonatkozó stratégia

-   A felhőbeli és a helyszíni infrastruktúra összekapcsolásának hibrid stratégiája

-   Naprakész hálózati biztonsági összetevők (például hálózati diagramok, hálózati referenciaarchitektúra)

További információkért tekintse meg az alábbi hivatkozásokat:
- [Az Azure ajánlott biztonsági eljárásai 11 – Architektúra. Egyetlen, egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure-biztonsági teljesítményteszt – Hálózati biztonság](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Az Azure-hálózat biztonsági áttekintése](../security/fundamentals/network-overview.md)

- [Vállalati hálózati architektúra stratégiája](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Identitás- és emelt szintű hozzáférési stratégia meghatározása

**Útmutató**: Hozzon létre egy Azure-beli identitás- és emelt szintű hozzáférési megközelítést a szervezete általános biztonság hozzáférés-vezérlési stratégiájának részeként.  

Ennek a stratégiának tartalmaznia kell az alábbi elemek dokumentált útmutatóit, szabályzatait és szabványait: 

-   A központosított identitás- és hitelesítési rendszer, valamint a rendszer más belső és külső rendszerekkel való összekapcsolhatósága

-   Erős hitelesítési módszerek a különböző használati esetekben és különböző feltételek esetén

-   Magas szintű jogosultságokkal rendelkező felhasználók védelme

-   Rendellenes felhasználói tevékenységek monitorozása és kezelése  

-   Felhasználói identitás és hozzáférés ellenőrzési és egyeztetési folyamata

További információkért tekintse meg az alábbi hivatkozásokat:

- [Azure-biztonsági teljesítményteszt – Identitáskezelés](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure-biztonsági teljesítményteszt – Emelt szintű hozzáférés](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Az Azure ajánlott biztonsági eljárásai 11 – Architektúra. Egyetlen, egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Az Azure-identitáskezelés biztonsági áttekintése](../security/fundamentals/identity-management-overview.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Naplózási stratégia és a fenyegetésekre adott válaszok stratégiájának meghatározása

**Útmutató**: Hozzon létre egy stratégiát a naplózáshoz és a fenyegetésekre adott válaszokhoz, amelyekkel gyorsan észlelheti és elháríthatja a fenyegetéseket, a megfelelőségi követelmények betartása mellett. Biztosítson jó minőségű riasztásokat és zökkenőmentes élményt az elemzőknek, így a fenyegetésekre összpontosíthatnak az integráció és a manuális lépések helyett. 

Ennek a stratégiának tartalmaznia kell az alábbi elemek dokumentált útmutatóit, szabályzatait és szabványait: 

-   A biztonsági műveletekért (SecOps) felelős szervezet szerepköre és felelősségei 

-   Az incidensre adott, jól definiált válaszfolyamat, amely igazodik az NIST-hez és más iparági keretrendszerekhez 

-   Naplók rögzítése és megőrzése a fenyegetésészlelés, az incidensmegoldás és a megfelelőségi célkitűzések támogatásához

-   A fenyegetések SIEM, natív Azure-képességek és más erőforrások használatával biztosított, központosított láthatósága, illetve korrelációs adatai 

-   Az ügyfelek, a beszállítók és az érdekelt közszektorbeli felekkel folytatott értesítésének, valamint a velük folytatott kommunikáció terve

-   Natív Azure-beli és külső platformok használata az incidensek kezeléséhez, például a naplózáshoz és a fenyegetésészleléshez, a vizsgálathoz, és a támadások elhárításához és megsemmisítéséhez

-   Az incidenseket és az incidensek utáni tevékenységeket, például a levont következtetéseket és a bizonyítékmegőrzést kezelő folyamatok

További információkért tekintse meg az alábbi hivatkozásokat:

- [Azure-biztonsági teljesítményteszt – Naplózás és fenyegetésészlelés](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure-biztonsági teljesítményteszt – Incidensmegoldás](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Az Azure ajánlott biztonsági eljárásai 4 – Folyamat. A felhőre vonatkozó incidensmegoldási folyamatok frissítése](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Útmutató az Azure-bevezetési keretrendszerrel, a naplózással és a jelentéskészítéssel kapcsolatos döntésekhez](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Az Azure vállalati skálázása, kezelése és monitorozása](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="next-steps"></a>Következő lépések

- Lásd: [Az Azure biztonsági teljesítményteszt 2-es verziójának áttekintése](/azure/security/benchmarks/overview)
- További tudnivalók az [Azure biztonsági alapkonfigurációiról](/azure/security/benchmarks/security-baselines-overview)
