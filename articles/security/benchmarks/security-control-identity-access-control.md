---
title: Azure Security Control – identitás- és hozzáférés-vezérlés
description: Biztonságvezérlés identitás- és hozzáférés-vezérlése
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 543573610c2ea3ab0bcd89e1b8f4ee5f5a34dbc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934426"
---
# <a name="security-control-identity-and-access-control"></a>Biztonságvezérlés: Identitás- és hozzáférés-vezérlés

Az identitás- és hozzáférés-kezelési javaslatok az identitásalapú hozzáférés-vezérléssel, a felügyeleti hozzáférés zárolásával, az identitással kapcsolatos eseményekre, a fiók rendellenes viselkedésével és a szerepköralapú hozzáférés-vezérléssel kapcsolatos problémák kezelésére összpontosítanak.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Az adminisztratív számlák leltárának fenntartása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 3,1 | 4.1 | Ügyfél |

Az Azure AD beépített szerepkörök, amelyek explicit módon hozzá kell rendelni, és lekérdezhető. Az Azure AD PowerShell modul használatával ad hoc lekérdezések végrehajtásával felügyeleti csoportok tagjaiként rendelkező fiókok felderítéséhez.

Címtárszerepkör beszereznie az Azure AD-ben a PowerShell használatával:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Címtárszerepkör tagjainak beszereznie az Azure AD-ben a PowerShell segítségével:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

## <a name="32-change-default-passwords-where-applicable"></a>3.2: Adott esetben változtassa meg az alapértelmezett jelszavakat

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 3,2 | 4.2 | Ügyfél |

Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával. A jelszóhoz szükséges egyéb Azure-erőforrások bonyolultsági követelményekkel és minimális jelszóhosszal rendelkező jelszót hoznak létre, amely a szolgáltatástól függően eltérő. Ön felelős a külső alkalmazásokért és a piactéri szolgáltatásokért, amelyek alapértelmezett jelszavakat használhatnak.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált felügyeleti fiókok használata

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 3.3 | 4.3 | Ügyfél |

Hozzon létre szabványos működési eljárásokat a dedikált felügyeleti számlák használata körül. Az Azure Security Center identitás- és hozzáférés-kezelése segítségével figyelheti a felügyeleti fiókok számát.

A Just-In-Time / Just-Enough-Access az Azure AD kiemelt identitáskezelési kiemelt szerepkörök a Microsoft Services és az Azure Resource Manager használatával is engedélyezheti a Just-In-Time / Just-Enough-Access-t. 

tudj meg többet:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Egyszeri bejelentkezés (SSO) használata az Azure Active Directoryval

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 3.4 | 4.4 | Ügyfél |

Ahol csak lehetséges, használja az Azure Active Directory egyszeri szolgáltatás használata helyett az egyes önálló hitelesítő adatok szolgáltatásonként konfigurálása helyett. Használja az Azure Security Center identitás- és hozzáférés-kezelési javaslatait.

Az SSO ismertetése az Azure AD-vel:

https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata az összes Azure Active Directory-alapú hozzáféréshez

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 3.5 | 4.5, 11.5, 12.11, 16.3 | Ügyfél |

Engedélyezze az Azure AD MFA-t, és kövesse az Azure Security Center identitás- és hozzáférés-kezelési javaslatait.

Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Az Azure Security Center identitásának és hozzáférésének figyelése:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Használjon dedikált gépeket (kiemelt hozzáférésű munkaállomásokat) minden adminisztratív feladathoz

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | Ügyfél |

Emelt hozzáférési szintű munkaállomások (privilegizált hozzáférési munkaállomások) használatával az MFA konfigurálva van az Azure-erőforrásokba való bejelentkezéshez és konfiguráláshoz.

Tudnivalók a kiemelt hozzáférésű munkaállomásokról:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: A gyanús tevékenységek naplózása és riasztása az adminisztratív fiókokból

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 3.7 | 4.8, 4.9 | Ügyfél |

Az Azure Active Directory biztonsági jelentéseit naplók és riasztások létrehozásához használja, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. Az Azure Security Center használatával figyelheti az identitás- és hozzáférési tevékenységeket.

Kockázatos tevékenységesetén megjelölt Azure AD-felhasználók azonosítása:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

A felhasználók identitásának és hozzáférési tevékenységének figyelése az Azure Security Centerben:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Az Azure-erőforrások kezelése csak jóváhagyott helyekről

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 3.8 | 11.7 | Ügyfél |

A feltételes hozzáféréssel elnevezett helyek használatával csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportjaiból engedélyezheti a hozzáférést.

Named-helyek konfigurálása az Azure-ban:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

## <a name="39-use-azure-active-directory"></a>3.9: Az Azure Active Directory használata

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | Ügyfél |

Az Azure Active Directory (AAD) használata központi hitelesítési és engedélyezési rendszerként. Az AAD az adatokat erős titkosítással védi az inaktív és az átvitel során tárolt adatokhoz. Az AAD a felhasználói hitelesítő adatokat is megsózta, kiigazítja és biztonságosan tárolja.

AAD-példány létrehozása és konfigurálása:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rendszeresen vizsgálja felül és egyeztetje össze a felhasználói hozzáférést

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 3.10 | 16.9, 16.10 | Ügyfél |

Az Azure AD naplókat biztosít az elavult fiókok felderítéséhez. Emellett az Azure Identity Access-vélemények használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen ellenőrizhető, hogy csak a megfelelő Felhasználók rendelkezhessenek folyamatos hozzáféréssel. 

Azure AD-jelentés:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Az Azure Identity Access-vélemények használata:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

## <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Az inaktivált fiókok elérésére tett kísérletek figyelése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 3.11 | 16.12 | Ügyfél |

Hozzáféréssel rendelkezik az Azure AD bejelentkezési tevékenység, naplózási és kockázati eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely SIEM/Monitoring eszközzel való integrációt.

Ezt a folyamatot egyszerűsítheti az Azure Active Directory felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplónaplók és a bejelentkezési naplók elküldésével a Log Analytics-munkaterületre. A Log Analytics-munkaterületen konfigurálhatja a kívánt riasztásokat.

Az Azure-tevékenységnaplók integrálása az Azure Monitorba:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 3.12 | 16.13 | Ügyfél |

Az Azure AD Risk and Identity Protection szolgáltatásaival konfigurálhatja a felhasználói identitásokkal kapcsolatos észlelt gyanús műveletekre adott automatikus válaszokat. További vizsgálat céljából adatokat is bevihet az Azure Sentinelbe.

Az Azure AD kockázatos bejelentkezései megtekintése:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Az identitásvédelem kockázati házirendjeinek konfigurálása és engedélyezése:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Az Azure Sentinel fedélzeti szolgáltatása:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: A Microsoft hozzáférésének biztosítása a releváns ügyféladatokhoz támogatási forgatókönyvek során

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 3.13 | 16 | Ügyfél |

Olyan támogatási esetekben, amikor a Microsoftnak hozzá kell férnie az ügyféladatokhoz, az Ügyfélszéf egy felületet biztosít az ügyféladatok-hozzáférési kérelmek áttekintéséhez, jóváhagyásához vagy elutasításához.

Az ügyfélszéf ismertetése:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

## <a name="next-steps"></a>További lépések

Tekintse meg a következő biztonsági ellenőrzést: [Adatvédelem](security-control-data-protection.md)
