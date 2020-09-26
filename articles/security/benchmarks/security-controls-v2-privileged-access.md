---
title: Azure Security teljesítményteszt v2 – emelt szintű hozzáférés
description: Azure Security teljesítményteszt v2 – emelt szintű hozzáférés
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b5315b4d2ec8b757f7fa1075a438419679f5e798
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317973"
---
# <a name="security-control-v2-privileged-access"></a>Security Control v2: emelt szintű hozzáférés

Az emelt szintű hozzáférés kiterjed az Azure-bérlőhöz és-erőforrásokhoz való rendszerjogosultságú hozzáférések elleni védelemre. Ez számos vezérlőelemet tartalmaz a felügyeleti modell, a rendszergazdai fiókok és a privilegizált hozzáférésű munkaállomások szándékos és véletlen kockázattal szembeni megóvására.

## <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: magas jogosultsági szintű felhasználók elleni védelem és korlátozás

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| PA-1 | 4,3, 4,8 | AC-2 |

Korlátozza a magas jogosultságú felhasználói fiókok számát, és emelt szintű védelemmel kell ellátnia ezeket a fiókokat. Az Azure AD legfontosabb beépített szerepkörei a globális rendszergazda és a Kiemelt szerepkörű rendszergazda, mivel a két szerepkörhöz hozzárendelt felhasználók delegálhatja a rendszergazdai szerepköröket. Ezekkel a jogosultságokkal a felhasználók közvetlenül vagy közvetve elolvashatják és módosíthatják az Azure-környezetben található összes erőforrást:

- Globális rendszergazda/vállalati rendszergazda: az ehhez a szerepkörhöz tartozó felhasználók hozzáférhetnek az Azure AD összes felügyeleti funkciójához, valamint az Azure AD-identitásokat használó szolgáltatásokhoz.

- Kiemelt szerepkörű rendszergazda: az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a szerepkör-hozzárendeléseket az Azure AD-ben, valamint a Azure AD Privileged Identity Managementon (PIM) belül is. Emellett ez a szerepkör lehetővé teszi a PIM és a felügyeleti egységek valamennyi aspektusának kezelését.

Megjegyzés: Előfordulhat, hogy más kritikus szerepkörökre is szükség van, amelyeket akkor kell alkalmaznia, ha egyéni szerepköröket használ bizonyos jogosultsági szintű engedélyekkel társítva. Emellett előfordulhat, hogy hasonló vezérlőket szeretne alkalmazni a kritikus fontosságú üzleti eszközök rendszergazdai fiókjára.  

Az Azure-erőforrásokhoz és az Azure AD-hez az Azure AD Privileged Identity Management (PIM) használatával engedélyezheti az igény szerinti (JIT) jogosultságú hozzáférést. A JIT ideiglenes engedélyeket biztosít a Kiemelt feladatok végrehajtásához, ha a felhasználóknak szükségük van rá. A PIM biztonsági riasztásokat is létrehozhat, ha az Azure AD-szervezetben gyanús vagy nem biztonságos tevékenység van.

- [Rendszergazdai szerepkör engedélyei az Azure AD-ben](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Azure Privileged Identity Management biztonsági riasztások használata](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](../../active-directory/users-groups-roles/directory-admin-roles-secure.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitás-és kulcskezelő](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: az üzleti szempontból kritikus fontosságú rendszerek rendszergazdai hozzáférésének korlátozása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| PA-2 | 13,2, 2,10 | AC-2, SC-3, SC-7 |

Az üzleti szempontból kritikus fontosságú rendszerekhez való hozzáférés elkülönítése azáltal, hogy az előfizetések és a felügyeleti csoportok számára magas jogosultsági szintű hozzáférést kapnak a fiókokhoz. Ügyeljen arra, hogy az üzleti szempontból kritikus fontosságú rendszerekre telepített ügynökökkel, például a Active Directory-tartomány vezérlőkkel (DCs), a biztonsági eszközökkel és a Rendszerfelügyeleti eszközökkel való rendszergazdai hozzáféréssel rendelkező felügyeleti, identitási és biztonsági rendszerekhez is korlátozza a hozzáférést. Azok a támadók, akik veszélyeztetik ezeket a felügyeleti és biztonsági rendszereket, azonnal weaponize azokat az üzleti szempontból kritikus fontosságú eszközök biztonsága érdekében. 

A hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani, hogy biztosítsa a konzisztens hozzáférés-vezérlést. 

Győződjön meg arról, hogy az e-mail-, a böngészési és a termelékenységi feladatokhoz használt normál felhasználói fiókoktól eltérő, különálló rendszerjogosultságú fiókokat rendel hozzá.

- [Azure-összetevők és-hivatkozási modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Felügyeleti csoport elérése](../../governance/management-groups/overview.md#management-group-access)

- [Azure-előfizetés rendszergazdái](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitás-és kulcskezelő](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

## <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| PA-3 | 4,1, 16,9, 16,10 | AC-2 |

Rendszeresen tekintse át a felhasználói fiókokat és a hozzáférés-hozzárendelést, hogy a fiókok és azok hozzáférési szintje érvényes legyen. Az Azure AD hozzáférési felülvizsgálatok segítségével áttekintheti a csoporttagság, a vállalati alkalmazásokhoz való hozzáférés és a szerepkör-hozzárendelések áttekintését. Az Azure AD jelentéskészítési szolgáltatása lehetővé teszi, hogy a naplók segítséget nyújtsanak az elavult fiókok felderítéséhez. A Azure AD Privileged Identity Management használatával olyan hozzáférési felülvizsgálati jelentési munkafolyamatot is létrehozhat, amely megkönnyíti a felülvizsgálati folyamatot.
Emellett az Azure Privileged Identity Management beállítható úgy, hogy riasztást hozzon létre túl sok rendszergazdai fiók létrehozásakor, valamint a elavult vagy helytelenül konfigurált rendszergazdai fiókok azonosításához. 

Megjegyzés: egyes Azure-szolgáltatások támogatják a helyi felhasználókat és a nem az Azure AD-n keresztül felügyelt szerepköröket. Ezeket a felhasználókat külön kell kezelnie.

- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Az Azure AD-identitás és a hozzáférési felülvizsgálatok használata](../../active-directory/governance/access-reviews-overview.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitás-és kulcskezelő](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: vészhelyzeti hozzáférés beállítása az Azure AD-ben

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| PA-4 | 16 | AC-2, CP-2 |

Ha nem szeretné megakadályozni, hogy véletlenül kizárja az Azure AD-szervezetét, állítson be egy vészhelyzeti hozzáférési fiókot a hozzáféréshez, ha a normál rendszergazdai fiókok nem használhatók. A vészhelyzeti hozzáférési fiókok általában magas jogosultsággal rendelkeznek, és nem rendelhetők hozzá konkrét személyekhez. A vészhelyzeti hozzáférési fiókok olyan vészhelyzeti vagy "break Glass" forgatókönyvekre korlátozódnak, amelyekben nem használhatók normál rendszergazdai fiókok.
Győződjön meg arról, hogy a vészhelyzeti hozzáférési fiókokhoz tartozó hitelesítő adatok (például jelszó, tanúsítvány vagy intelligens kártya) biztonságosak és ismertek, csak azokra a személyekre, akik jogosultak arra, hogy csak vészhelyzetben használják őket.

- [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](../../active-directory/users-groups-roles/directory-emergency-access.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitás-és kulcskezelő](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Biztonsági műveletek (SecOps)](//azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-5-automate-entitlement-management"></a>PA-5: a jogosultságok kezelésének automatizálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| PA-5 | 16 | AC-2, AC-5, PM-10 |

Az Azure AD-jogosultságok kezelési funkciói segítségével automatizálhatja a hozzáférési kérelmek munkafolyamatait, beleértve a hozzáférési hozzárendeléseket, az értékeléseket és a lejáratokat. A kettős vagy többfázisú jóváhagyás is támogatott.
- [Az Azure AD hozzáférési felülvizsgálatai](../../active-directory/governance/access-reviews-overview.md) 

- [Mi az Azure AD-jogosultságok kezelése?](../../active-directory/governance/entitlement-management-overview.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitás-és kulcskezelő](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-6-use-privileged-access-workstations"></a>PA-6: emelt szintű hozzáférésű munkaállomások használata

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| PA-6 | 4,6, 11,6, 12,12 | AC-2, SC-3, SC-7 |

A biztonságos, elkülönített munkaállomások kritikus fontosságúak a bizalmas szerepkörök, például a rendszergazdák, a fejlesztők és a kritikus fontosságú szolgáltatók biztonsága szempontjából. A biztonságos felhasználói munkaállomásokat és/vagy az Azure Bastion-t a felügyeleti feladatokhoz használhatja. A biztonságos és felügyelt felhasználói munkaállomás felügyeleti feladatokhoz való telepítéséhez Azure Active Directory, a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) és/vagy Microsoft Intune használható. A biztonságos munkaállomások központilag felügyelhetők a biztonságos konfiguráció kikényszerítéséhez, beleértve az erős hitelesítést, a szoftver-és a hardver-alapterveket, valamint a korlátozott logikai és hálózati hozzáférést is. 

- [Az emelt szintű hozzáférésű munkaállomások ismertetése](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Emelt szintű hozzáférési munkaállomás üzembe helyezése](../../active-directory/devices/howto-azure-managed-workstation.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Biztonsági műveletek (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Identitás-és kulcskezelő](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: csak elég felügyelet (a legalacsonyabb jogosultsági elv) követése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| PA-7 | 14,6 | AC-2, AC-3, SC-3 |

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi az Azure-erőforrások hozzáférésének kezelését a szerepkör-hozzárendeléseken keresztül. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoport egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre definiált beépített szerepkörök tartoznak, és ezeket a szerepköröket leltározott vagy lekérdezheti olyan eszközökkel, mint például az Azure CLI, a Azure PowerShell és a Azure Portal. Az erőforrásokhoz az Azure RBAC-on keresztül hozzárendelt jogosultságokat mindig a szerepkörök által igényelt értékekre kell korlátozni. A korlátozott jogosultságok kiegészítik a Azure AD Privileged Identity Management (PIM) igény szerinti (JIT) megközelítését, és ezeket a jogosultságokat rendszeresen felül kell vizsgálni.
A beépített szerepkörök használatával lefoglalhatja az engedélyt, és szükség esetén csak egyéni szerepkört hozhat létre. 

- [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md)

- [Az Azure RBAC konfigurálása](../../role-based-access-control/role-assignments-portal.md)

- [Az Azure AD-identitás és a hozzáférési felülvizsgálatok használata](../../active-directory/governance/access-reviews-overview.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Identitás-és kulcskezelő](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: a Microsoft támogatási szolgálatának jóváhagyási folyamatának kiválasztása 

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| PA-8 | 16 | AC-2, AC-3, AC-4 |

Olyan esetekben, amikor a Microsoftnak hozzá kell férnie az ügyféladatok eléréséhez, Ügyfélszéf lehetőséget nyújt az ügyfelek adatelérési kérelmének explicit áttekintésére és jóváhagyására vagy elutasítására.

- [A Ügyfélszéf megismerése](../fundamentals/customer-lockbox-overview.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Identitás-és kulcskezelő](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

