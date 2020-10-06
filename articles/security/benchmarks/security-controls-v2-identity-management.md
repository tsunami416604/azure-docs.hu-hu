---
title: Azure Security teljesítményteszt v2 – Identitáskezelés
description: Azure Security teljesítményteszt v2 – Identitáskezelés
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8a29dbbf365304240283fe4fd5899b58e3bc227c
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91741407"
---
# <a name="security-control-v2-identity-management"></a>Security Control v2: Identitáskezelés

Az Identitáskezelés kiterjed a biztonságos identitás-és hozzáférés-vezérlések létrehozására Azure Active Directory használatával. Ez magában foglalja az egyszeri bejelentkezést, az erős hitelesítéseket, a felügyelt identitásokat (és a szolgáltatási alapelveket) az alkalmazásokhoz, a feltételes hozzáféréshez és a fiókok rendellenességének figyeléséhez.

## <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: szabványosított Azure Active Directory központi identitás-és hitelesítési rendszerrel

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| IM-1 | 16,1, 16,2, 16,4, 16,5 | IA-2, IA-8, AC-2, AC-3 |

Azure Active Directory (Azure AD) az Azure alapértelmezett identitás-és hozzáférés-kezelési szolgáltatása. Egységesítse az Azure AD-ben a szervezet identitás-és hozzáférés-kezelésének szabályozását a ben:
- A Microsoft felhőalapú erőforrásai, például a Azure Portal, az Azure Storage, az Azure Virtual Machines (Linux és Windows), a Azure Key Vault, a Pásti és az SaaS-alkalmazások.

- A szervezet erőforrásai, például az Azure-beli alkalmazások vagy a vállalati hálózati erőforrások.

Az Azure AD biztonságossá tételének magas prioritásnak kell lennie a szervezet felhőalapú biztonsági gyakorlatában. Az Azure AD egy biztonságos azonosítási pontszámot biztosít, amellyel a Microsoft ajánlott eljárási javaslataihoz képest értékelheti a személyazonosság biztonsági helyzetét. A pontszám használatával mérje fel, hogy a konfiguráció milyen mértékben felel meg az ajánlott eljárásokkal kapcsolatos javaslatoknak, és javítsa a biztonsági helyzetét.

Megjegyzés: az Azure AD támogatja a külső identitás-szolgáltatókat, amelyek lehetővé teszik a felhasználók számára, hogy Microsoft-fiók nélkül bejelentkezzenek alkalmazásaiba és erőforrásaiba külső identitásával.

- [Bérlet az Azure AD-ben](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [Azure AD-példány létrehozása és konfigurálása](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure AD-bérlők definiálása](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [Külső identitás-szolgáltatók használata alkalmazáshoz](/azure/active-directory/b2b/identity-providers)

- [Mi az Active Directory biztonságos pontszáma az Azure AD-ben](../../active-directory/fundamentals/identity-secure-score.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitás-és kulcskezelő](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: az alkalmazás-identitások biztonságos és automatikus kezelése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| IM-2 | N/A | AC-2, AC-3, IA-2, IA-4, IA-9 |

A nem emberi fiókok, például a szolgáltatások vagy az automatizálás esetében az Azure által felügyelt identitások használata helyett az erőforrások eléréséhez és a kód végrehajtásához hatékonyabb emberi fiókot kell létrehoznia. Az Azure által felügyelt identitások hitelesítése az Azure AD-hitelesítést támogató Azure-szolgáltatásokban és-erőforrásokban történik. A hitelesítés az előre meghatározott hozzáférés-engedélyezési szabályokon keresztül engedélyezett, a forráskódban vagy a konfigurációs fájlokban lévő, nehezen kódolt hitelesítő adatok elkerülésével. 

A felügyelt identitásokat nem támogató szolgáltatások esetében az Azure AD használatával hozzon létre egy egyszerű szolgáltatásnevet a korlátozott engedélyekkel az erőforrás szintjén.  Javasoljuk, hogy konfigurálja a tanúsítvány hitelesítő adataival rendelkező egyszerű szolgáltatásokat, és térjen vissza az ügyfél titkos kulcsaihoz. Mindkét esetben a Azure Key Vault az Azure által felügyelt identitásokkal együtt használható, így a futásidejű környezet (például egy Azure-függvény) lekérheti a hitelesítő adatokat a kulcstartóból.

- [Azure-beli felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md)

- [Az Azure-erőforrások felügyelt identitásait támogató szolgáltatások](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure-szolgáltatásnév](/powershell/azure/create-azure-service-principal-azureps)

- [Egyszerű szolgáltatás létrehozása tanúsítványokkal](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

Azure Key Vault használata rendszerbiztonsági tag regisztrálásához: hitelesítés # engedélyezés-a-Security-Principal-to-Access-Key-Vault

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitás-és kulcskezelő](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: az Azure AD egyszeri bejelentkezés (SSO) használata az alkalmazásokhoz való hozzáféréshez

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| IM-3 | 4.4 | IA-2, IA-4 |

Az Azure AD identitás-és hozzáférés-kezelést biztosít az Azure-erőforrásokhoz, a felhőalapú alkalmazásokhoz és a helyszíni alkalmazásokhoz. Az identitás-és hozzáférés-kezelés olyan vállalati identitásokra vonatkozik, mint az alkalmazottak, valamint a külső identitások, mint például a partnerek, a szállítók és a szállítók.

Az Azure AD egyszeri bejelentkezés (SSO) használatával felügyelheti és biztonságossá teheti a szervezet adataihoz és erőforrásaihoz való hozzáférést a helyszínen és a felhőben. Az összes felhasználó, alkalmazás és eszköz csatlakoztatása az Azure AD-hez zökkenőmentes, biztonságos hozzáférés és jobb láthatóság és vezérlés érdekében. 

- [Alkalmazás egyszeri bejelentkezésének megismerése az Azure AD-vel](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Identitás-és kulcskezelő](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: erős hitelesítési vezérlők használata az összes Azure Active Directory-alapú hozzáféréshez

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| IM-4 | 4,2, 4,4 4,5, 11,5, 12,11, 16,3 | AC-2, AC-3, IA-2, IA-4 |

Az Azure AD a többtényezős hitelesítéssel (MFA) és az erős, jelszóval nem rendelkező metódusokkal támogatja az erős hitelesítési vezérlést.  
- Többtényezős hitelesítés: engedélyezze az Azure AD MFA-t, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait az MFA-telepítéshez. Az MFA kikényszeríthető minden felhasználóra, kiválaszthatja a felhasználókat vagy a felhasználónkénti szinten a bejelentkezési feltételek és kockázati tényezők alapján. 

- Jelszóval nem rendelkező hitelesítés: három jelszóval nem rendelkező hitelesítési lehetőség érhető el: a vállalati Windows Hello, Microsoft Authenticator alkalmazás és a helyszíni hitelesítési módszerek, például az intelligens kártyák. 

A rendszergazda és a Kiemelt jogosultságú felhasználók számára győződjön meg arról, hogy az erős hitelesítési módszer legmagasabb szintje van használatban, majd ezt követi a megfelelő erős hitelesítési házirend kiosztása más felhasználók számára.

Ha az Azure AD-hitelesítéshez továbbra is az örökölt jelszó-alapú hitelesítést használja, vegye figyelembe, hogy a csak felhőalapú fiókok (az Azure-ban közvetlenül létrehozott felhasználói fiókok) rendelkeznek alapértelmezett alapkonfiguráció-házirenddel. És a hibrid fiókok (a helyszíni Active Directoryból származó felhasználói fiókok) a helyszíni jelszóházirend-szabályzatokat követik. A jelszó-alapú hitelesítés használatakor az Azure AD jelszavas védelmet biztosít, amely megakadályozza, hogy a felhasználók könnyen kitalált jelszavakat állítsanak be. A Microsoft globális listát biztosít a telemetria alapján frissített tiltott jelszavakról, és az ügyfelek igényeik alapján is kiterjeszthetik a listát (például branding, kulturális referenciák stb.). Ez a jelszavas védelem csak felhőalapú és hibrid fiókok esetében használható. 

Megjegyzés: a jelszó-hitelesítő adatokon alapuló hitelesítés csak a népszerű támadási módszerekre van kitéve. A nagyobb biztonság érdekében használjon erős hitelesítést, például MFA-t és egy erős jelszóházirend-beállítást. A külső gyártóktól származó alkalmazások és Marketplace-szolgáltatások esetében, amelyek alapértelmezett jelszavakkal rendelkezhetnek, a kezdeti szolgáltatás telepítése során érdemes módosítani. 

- [Az MFA engedélyezése az Azure-ban](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Bevezetés a Azure Active Directory jelszavas hitelesítési lehetőségeibe](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Az Azure AD alapértelmezett jelszavas házirendje](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Helytelen jelszavak eltávolítása az Azure AD jelszavas védelemmel](../../active-directory/authentication/concept-password-ban-bad.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Identitás-és kulcskezelő](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: a fiókok rendellenességeit figyelő és riasztások

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| IM-5 | 4,8, 4,9, 16,12, 16,13 | AC-2, AC-3, AC-7, AU-6 |

Az Azure AD a következő adatforrásokat biztosítja: 
-   Bejelentkezések – a bejelentkezési jelentés információt nyújt a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.

-   Naplók – az Azure AD különböző funkciói által végrehajtott módosítások naplókban való nyomon követését teszi lehetővé. Naplózott változások naplózása például felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadásával vagy eltávolításával kapcsolatos naplók.

-   Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.

-   Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

Ezek az adatforrások a Azure Monitor, az Azure Sentinel vagy harmadik féltől származó SIEM rendszerekkel integrálhatók.

Azure Security Center riasztást is beállíthat bizonyos gyanús tevékenységekről, például a sikertelen hitelesítési kísérletekről, valamint az előfizetésben elavult fiókokról. 

Az Azure komplex veszélyforrások elleni védelem (ATP) olyan biztonsági megoldás, amely helyszíni Active Directory jeleket használ a fejlett fenyegetések, a feltört identitások és a rosszindulatú bennfentes műveletek azonosítására, észlelésére és kivizsgálására.

- [Tevékenység-jelentések naplózása az Azure AD-ben](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Az Azure AD kockázatos bejelentkezések megtekintése](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [A kockázatos tevékenységre megjelölt Azure AD-felhasználók azonosítása](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Center](../../security-center/security-center-identity-access.md)

- [Riasztások Azure Security Center veszélyforrások elleni védelmi moduljában](//azure/security-center/alerts-reference)

- [Azure-beli tevékenység-naplók integrálása a Azure Monitorba](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Adatok összekapcsolásának Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: az Azure erőforrás-hozzáférés korlátozása feltételek alapján

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| IM-6 | N/A | AC-2, AC-3 |

Az Azure AD feltételes hozzáférés használatával részletesebb hozzáférés-vezérlést használhat a felhasználó által definiált feltételek alapján, például bizonyos IP-címtartományok felhasználói bejelentkezésének megkövetelése az MFA használatára. A részletes hitelesítési munkamenetek kezelése az Azure AD feltételes hozzáférési szabályzatán keresztül is felhasználható a különböző használati esetekben. 

- [Az Azure feltételes hozzáférés áttekintése](../../active-directory/conditional-access/overview.md)

- [Gyakori feltételes hozzáférési szabályzatok](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [A hitelesítési munkamenetek kezelésének konfigurálása feltételes hozzáféréssel](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitás-és kulcskezelő](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Fenyegetésészlelési intelligencia](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: a nem szándékolt hitelesítő adatok expozíciójának megszüntetése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| IM-7 | 18,1, 18,7 | IA-5 |

Implementálja az Azure DevOps hitelesítőadat-olvasót a kódban található hitelesítő adatok azonosításához. A hitelesítő adatok képolvasó a felderített hitelesítő adatoknak a biztonságosabb helyszínekre (például Azure Key Vault) való áthelyezését is javasolja

A GitHub esetében a natív titkos keresési funkcióval azonosíthatók a kódokon belüli hitelesítő adatok vagy egyéb titkok.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub-titkos vizsgálat](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: a felhasználók hozzáférésének biztosítása az örökölt alkalmazásokhoz

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| IM-8 | 14,6 | AC-2, AC-3, SC-11 |

Győződjön meg arról, hogy rendelkezik a modern hozzáférés-vezérléssel és a munkamenet-figyeléssel a régi alkalmazásokhoz, valamint az általuk tárolt és feldolgozott adatkezelés Habár a VPN-eket általában a régi alkalmazások elérésére használják, gyakran csak alapszintű hozzáférés-vezérléssel és korlátozott munkamenet-figyeléssel rendelkeznek.

Az Azure AD Application Proxy lehetővé teszi az örökölt helyszíni alkalmazások közzétételét a távoli felhasználók számára egyszeri bejelentkezéssel (SSO), miközben explicit módon érvényesíti a távoli felhasználók és az eszközök megbízhatóságát az Azure AD feltételes hozzáféréssel. 

Azt is megteheti, Microsoft Cloud App Security egy felhőalapú hozzáférés-vezérlési (CASB) szolgáltatás, amely lehetővé teszi a felhasználók alkalmazás-munkameneteinek figyelését és a blokkoló műveletek (a régi helyszíni alkalmazások és a felhőalapú szoftverek (SaaS) alkalmazások) monitorozását. 

- [Azure-AD Application Proxy](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Microsoft Cloud App Security ajánlott eljárások](/cloud-app-security/best-practices)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Alkalmazásbiztonság és DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)
