---
title: Azure Security teljesítményteszt v2 – emelt szintű hozzáférés
description: Azure Security teljesítményteszt v2 – emelt szintű hozzáférés
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8727cbd07fad1960f4bdb33742729b6bda3a369e
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059342"
---
# <a name="security-control-privileged-access"></a>Biztonsági ellenőrzés: emelt szintű hozzáférés

Az emelt szintű hozzáférés kiterjed az Azure-bérlőhöz és-erőforrásokhoz való rendszerjogosultságú hozzáférések elleni védelemre. Ez számos vezérlőelemet tartalmaz a felügyeleti modell, a rendszergazdai fiókok és a privilegizált hozzáférésű munkaállomások szándékos és véletlen kockázattal szembeni megóvására.

## <a name="pa-1-protect-and-limit-the-global-administrators"></a>PA-1: a globális rendszergazdák elleni védelem és korlátozás

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| PA-1 | 4,3, 4,8 | AC-2 |

A globális rendszergazdai szerepkörhöz hozzárendelt felhasználók elolvashatják és módosíthatják az Azure AD-szervezet minden felügyeleti beállítását. Az egyes előfizetések esetében legfeljebb kettőnél korlátozza az Azure globális rendszergazdai fiókjainak számát. Az Azure AD legfontosabb beépített szerepkörei a globális rendszergazda és a Kiemelt szerepkörű rendszergazda, mivel az ehhez a két szerepkörhöz hozzárendelt felhasználók delegálni tudják a rendszergazdai szerepköröket:
- Globális rendszergazda/vállalati rendszergazda: az ehhez a szerepkörhöz tartozó felhasználók hozzáférhetnek az Azure AD összes felügyeleti funkciójához, valamint az Azure AD-identitásokat használó szolgáltatásokhoz.

- Kiemelt szerepkörű rendszergazda: az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a szerepkör-hozzárendeléseket az Azure AD-ben, valamint a Azure AD Privileged Identity Managementon (PIM) belül is. Emellett ez a szerepkör lehetővé teszi a PIM és a felügyeleti egységek valamennyi aspektusának kezelését.

Megjegyzés: Előfordulhat, hogy más kritikus szerepkörökre is szükség van, amelyeket akkor kell alkalmaznia, ha egyéni szerepköröket használ bizonyos jogosultsági szintű engedélyekkel társítva. Emellett előfordulhat, hogy hasonló vezérlőket szeretne alkalmazni a kritikus fontosságú üzleti eszközök rendszergazdai fiókjára.  

Az Azure-erőforrásokhoz és az Azure AD-hez az Azure AD Privileged Identity Management (PIM) használatával engedélyezheti az igény szerinti (JIT) jogosultságú hozzáférést. A JIT ideiglenes engedélyeket biztosít a Kiemelt feladatok végrehajtásához, ha a felhasználóknak szükségük van rá. A PIM biztonsági riasztásokat is létrehozhat, ha az Azure AD-szervezetben gyanús vagy nem biztonságos tevékenység van.

Megjegyzés: egyes Azure-szolgáltatások, például az Azure SQL támogatja a helyi felhasználók hitelesítését az Azure AD-hitelesítés mellett. Ezt a típusú helyi felhasználói hitelesítést nem az Azure AD kezeli. Ezeket a felhasználókat külön kell kezelnie.

- [Rendszergazdai szerepkör engedélyei az Azure AD-ben](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Egyéni Azure-szerepkörök](../../role-based-access-control/custom-roles.md)

- [Azure Privileged Identity Management biztonsági riasztások használata](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Identitás és hozzáférés figyelése Azure Security Center használatával](../../security-center/security-center-identity-access.md)

**Felelősség**: ügyfél

**Ügyfelek biztonsági résztvevői**:

- [Identitás és kulcsok](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="pa-2-review-and-reconcile-user-access-regularly"></a>PA-2: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| PA-2 | 4,1, 16,9, 16,10 | AC-2 |

Rendszeresen tekintse át a felhasználói fiókokat és a hozzáférési jogosultságokat, hogy a felhasználói fiókok és a hozzáférésük érvényes legyen. 

Az Azure AD identitás-és hozzáférési felülvizsgálatok segítségével kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. Az Azure AD jelentéskészítési szolgáltatása lehetővé teszi, hogy a naplók segítséget nyújtsanak az elavult fiókok felderítéséhez. A felülvizsgálati folyamat megkönnyítése érdekében a Azure AD Privileged Identity Management használatával hozzáférési felülvizsgálati jelentési munkafolyamatot is létrehozhat.

Az Azure szolgáltatás és a számítási feladatok szintjén a rendszergazda felhasználók számára gyakoribb felhasználói és hozzáférési felülvizsgálatot kell végezni. Az Azure Privileged Identity Management biztonsági riasztásokat is használhat a rendszergazdai fiókok létrehozásakor, valamint a elavult vagy helytelenül konfigurált rendszergazdai fiókok kereséséhez. 

Megjegyzés: egyes Azure-szolgáltatások, például az Azure SQL-támogatás támogatják a helyi felhasználókat, akiket nem az Azure AD-n keresztül felügyelnek. Ezeket a felhasználókat külön kell kezelnie.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Identitás és hozzáférés figyelése Azure Security Center használatával](../../security-center/security-center-identity-access.md)

- [Azure Privileged Identity Management biztonsági riasztások használata](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Adminisztrációs feladatok összehangolása a csapatok között](/azure/cloud-adoption-framework/organize/raci-alignment) 

- [Az Azure AD jelentéskészítés ismertetése](/azure/active-directory/reports-monitoring/)

- [Az Azure AD-identitás és a hozzáférési felülvizsgálatok használata](../../active-directory/governance/access-reviews-overview.md)

- [Privileged Identity Management – az Azure AD-szerepkörökhöz való hozzáférés áttekintése](../../active-directory/privileged-identity-management/pim-how-to-start-security-review.md)

- [Azure Security Center – identitás és hozzáférés figyelése](../../security-center/security-center-identity-access.md)

**Felelősség**: ügyfél

**Ügyfelek biztonsági résztvevői**:

- [Identitás és kulcsok](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-3-set-up-an-emergency-access-account-in-azure-ad"></a>PA-3: vészhelyzeti hozzáférési fiók beállítása az Azure AD-ben

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| PA-3 | 12,3 | AC-2 |

Ha nem szeretné megakadályozni, hogy véletlenül kizárja az Azure AD-szervezetét, állítson be egy vészhelyzeti hozzáférési fiókot a hozzáféréshez, ha a normál rendszergazdai fiókok nem használhatók. A vészhelyzeti hozzáférési fiókok általában magas jogosultsággal rendelkeznek, és nem rendelhetők hozzá konkrét személyekhez. A vészhelyzeti hozzáférési fiókok olyan vészhelyzeti vagy "break Glass" forgatókönyvekre korlátozódnak, amelyekben nem használhatók normál rendszergazdai fiókok.

Győződjön meg arról, hogy a vészhelyzeti hozzáférési fiókokhoz tartozó hitelesítő adatok (például jelszó, tanúsítvány vagy intelligens kártya) biztonságosak és ismertek, csak azokra a személyekre, akik jogosultak arra, hogy csak vészhelyzetben használják őket.

- [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](../../active-directory/users-groups-roles/directory-emergency-access.md)

**Felelősség**: ügyfél

**Ügyfelek biztonsági résztvevői**:

- [Identitás és kulcsok](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Biztonsági műveletek központja (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-4-automate-azure-identity-and-access-request-workflow"></a>PA-4: az Azure identitás-és hozzáférési kérelmek munkafolyamatának automatizálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| PA-4 | N/A | AC-2, AC-5, PM-10 |

Az Azure AD-jogosultságok kezelési funkciói segítségével automatizálhatja az Azure-hozzáférési kérelmek munkafolyamatait, beleértve a hozzáférési hozzárendeléseket, az értékeléseket és a lejáratokat. A kettős vagy többfázisú jóváhagyás is támogatott.  

- [Mi az Azure AD-jogosultságok kezelése?](../../active-directory/governance/entitlement-management-overview.md)

- [Hozzáférési kérelem és jóváhagyási folyamat beállítása](../../active-directory/governance/entitlement-management-access-package-request-policy.md)

**Felelősség**: ügyfél

**Ügyfelek biztonsági résztvevői**:

- [Identitás és kulcsok](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-5-use-highly-secured-machines-for-administrative-tasks"></a>PA-5: biztonságos gépek használata felügyeleti feladatokhoz

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| PA-5 | 4,6, 11,6, 12,12 | AC-2, SC-7 |

A biztonságos, elkülönített munkaállomások kritikus fontosságúak a bizalmas szerepkörök, például a rendszergazdák, a fejlesztők és a kritikus fontosságú szolgáltatók biztonsága szempontjából. A biztonságos felhasználói munkaállomások és/vagy az Azure Bastion használata felügyeleti feladatokhoz:
- A biztonságos és felügyelt felhasználói munkaállomás felügyeleti feladatokhoz való telepítéséhez Azure Active Directory, a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) és/vagy Microsoft Intune használható. A biztonságos munkaállomások központilag felügyelhetők a biztonságos konfiguráció kényszerítéséhez, beleértve az erős hitelesítést, a szoftver-és a hardver alapkonfigurációit, a korlátozott logikai és hálózati hozzáférést. 

- Az Azure Bastion szolgáltatással biztonságos elérési utat használhat a virtuális gépek RDP-vagy SSH-kapcsolaton keresztüli eléréséhez. Az Azure Bastion egy teljes körűen felügyelt, új virtuális hálózat létrehozása nélkül kiépíthető, virtuális hálózatban üzembe helyezhető szolgáltatás. 

- [A biztonságos, Azure által felügyelt munkaállomások ismertetése](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Biztonságos, Azure által felügyelt munkaállomás üzembe helyezése](../../active-directory/devices/howto-azure-managed-workstation.md)

- [Az Azure Bastion-gazdagép használata](../../bastion/bastion-create-host-portal.md)

**Felelősség**: ügyfél

**Ügyfelek biztonsági résztvevői**:

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Biztonsági műveletek központja (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Identitás és kulcsok](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-6-assign-privileges-to-resources-using-azure-rbac"></a>PA-6: jogosultságok kiosztása erőforrásokhoz az Azure RBAC

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| PA-6 | 14,6 | AC-2, AC-3 |

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi az Azure-erőforrásokhoz való hozzáférés jogosultságának kezelését a szerepkör-hozzárendeléseken keresztül. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre definiált beépített szerepkörök tartoznak, és ezeket a szerepköröket az Azure CLI-vel, a Azure PowerShellsal vagy a Azure Portal eszközzel leltározott vagy lekérdezheti. 

- [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md)

- [A RBAC konfigurálása az Azure-ban](../../role-based-access-control/role-assignments-portal.md)

**Felelősség**: ügyfél

**Ügyfelek biztonsági résztvevői**:

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Identitás és kulcsok](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-choose-approval-process-for-microsoft-support"></a>PA-7: a Microsoft támogatási szolgálatának jóváhagyási folyamatának kiválasztása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| PA-7 | 16 | AC-2, AC-3, AC-4 |

Olyan esetekben, amikor a Microsoftnak hozzá kell férnie az ügyféladatok eléréséhez, Ügyfélszéf lehetőséget nyújt az ügyfelek adathozzáférési kérelmeinek explicit áttekintésére és jóváhagyására vagy elutasítására.

- [A Ügyfélszéf megismerése](../fundamentals/customer-lockbox-overview.md)

**Felelősség**: ügyfél

**Ügyfelek biztonsági résztvevői**:

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Identitás és kulcsok](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

