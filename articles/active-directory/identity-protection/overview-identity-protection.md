---
title: Mi az Azure Active Directory Identity Protection?
description: Kockázatok észlelése, szervizelése, vizsgálata és elemzése Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e928d67ba7102df3d342e77705ea895f9230ff3
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887659"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Mi az Azure Active Directory Identity Protection?

Az Identity Protection egy olyan eszköz, amely lehetővé teszi, hogy a szervezetek három fő feladatot hajtanak végre:

- Automatizálhatja az identitás-alapú kockázatok észlelését és szervizelését.
- Vizsgálja meg a kockázatokat a portálon tárolt adatszolgáltatások használatával.
- A kockázati észlelési adataikat külső gyártótól származó eszközökre exportálhatja további elemzés céljából.

Az Identity Protection a Microsoft által az Azure AD-vel, a Microsoft-fiókokkal, a Microsoft-fiókokkal és az Xbox-szal a felhasználók védelme érdekében a Microsoft által beszerzett tapasztalatokat használja. A Microsoft naponta 6 500 000 000 000-as jeleket elemez, hogy azonosítsa és megvédje az ügyfeleket a fenyegetésektől.

A által generált és az Identity Protection által létrehozott jelek továbbra is bevonhatók olyan eszközökbe, mint a feltételes hozzáférés a hozzáférési döntések meghozatalához, illetve a biztonsági információk és az Event Management (SIEM) eszközre való visszalépéshez a szervezet kényszerített szabályzatok.

## <a name="why-is-automation-important"></a>Miért fontos az Automation?

Az 2018 Alex Weinert [októberi blogbejegyzést](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) , amely a Microsoft Identity Security and Protection csapatát vezeti, magyarázza el, miért fontos az automatizálás az események mennyiségének kezelésekor:

> A gépi tanulás és a heurisztikus rendszerek minden nap több mint 800 000 000 különböző fiókra vonatkozó kockázati pontszámokat biztosítanak a 18 000 000 000 300 000 000-es bejelentkezési kísérletekhez
>
> Az Ignite tavaly megbeszélte az első 3 támadást az Identity Systems-ben. Itt láthatók a támadások legutóbbi mennyisége
>   
>   - **Visszajátszási ismétlés**: 4,6 milliárd támadás észlelhető 2018 májusában
>   - **Jelszó spray**: 350k áprilisban 2018
>   - **Adathalászat**: ez nehezen számszerűsíthető, de a 23M kockázati eseményeinek száma a 2018-as számú.

## <a name="risk-detection-and-remediation"></a>Kockázatok észlelése és szervizelése

Az Identity Protection a következő besorolások kockázatait azonosítja:

| Kockázat észlelésének típusa | Leírás |
| --- | --- |
| Atipikus utazás | Jelentkezzen be egy atipikus helyről a felhasználó legutóbbi bejelentkezései alapján. |
| Névtelen IP-cím | Jelentkezzen be egy névtelen IP-címről (például: Tor-böngésző, névtelenül VPN-ek). |
| Ismeretlen bejelentkezési tulajdonságok | Jelentkezzen be a nemrégiben nem látott tulajdonságokkal az adott felhasználó számára. |
| Kártevők társított IP-címe | Bejelentkezés kártevővel összekapcsolt IP-címről |
| Kiszivárgott hitelesítő adatok | Ez a kockázati észlelés azt jelzi, hogy a felhasználó érvényes hitelesítő adatait kiszivárgott |
| Azure AD-fenyegetések felderítése | A Microsoft belső és külső veszélyforrások hírszerzési forrásai ismert támadási mintát azonosítottak |

A kockázatokról és azok kiszámításáról további részleteket a cikkben [találhat.](concept-identity-protection-risks.md)

A kockázati jelek olyan szervizelési erőfeszítéseket válthatnak ki, mint például a felhasználók megkövetelése: az Azure Multi-Factor Authentication elvégzése, jelszó alaphelyzetbe állítása az önkiszolgáló jelszó-visszaállítással vagy a rendszergazda beavatkozása nélkül.

## <a name="risk-investigation"></a>Kockázati vizsgálat

A rendszergazdák megtekinthetik az észleléseket, és szükség esetén manuálisan is végezhetnek műveleteket. Három kulcsfontosságú jelentés van, amelyet a rendszergazdák a személyazonosság védelme során használnak a nyomozáshoz:

- Kockázatos felhasználók
- Kockázatos bejelentkezések
- Kockázatészlelések

További információt a következő cikkben talál [: a kockázat vizsgálata](howto-identity-protection-investigate-risk.md).

## <a name="exporting-risk-data"></a>Kockázati adatexportálás

Az Identity Protectionből származó adatok exportálhatók más eszközökre az archiváláshoz, valamint a további vizsgálatokhoz és a Corel-kezeléshez. A Microsoft Graph-alapú API-k lehetővé teszik, hogy a szervezetek további feldolgozás céljából gyűjtsék ezeket az adatokat egy olyan eszközön, mint például a SIEM. Az Identity Protection API elérésével kapcsolatos információkat a cikk [Azure Active Directory Identity Protection és Microsoft Graph első lépéseivel foglalkozó](howto-identity-protection-graph-api.md) cikkben találja.

Az Identity Protection-információk Azure Sentineltel való integrálásával kapcsolatos információk a cikkben találhatók, [amelyek az adatok Azure ad Identity Protectionból való összekapcsolását](../../sentinel/connect-azure-ad-identity-protection.md)ismertetik.

## <a name="permissions"></a>Engedélyek

Az Identity Protection használatához a felhasználóknak biztonsági olvasónak, biztonsági kezelőnek, biztonsági rendszergazdának, globális olvasónak vagy globális rendszergazdának kell lenniük ahhoz, hogy hozzáférjenek.

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Szolgáltatás | Részletek | Prémium szintű Azure AD P2 | Prémium szintű Azure AD P1 | Alapszintű Azure AD/ingyenes |
| --- | --- | --- | --- | --- |
| Kockázati szabályzatok | Felhasználói kockázati házirend (Identity Protection használatával) | Igen | Nem | Nem |
| Kockázati szabályzatok | Bejelentkezési kockázati szabályzat (Identity Protection vagy feltételes hozzáférés használatával) | Igen | Nem | Nem |
| Biztonsági jelentések | Áttekintés | Igen | Nem | Nem |
| Biztonsági jelentések | Kockázatos felhasználók | Teljes hozzáférés | Korlátozott információ | Korlátozott információ |
| Biztonsági jelentések | Kockázatos bejelentkezések | Teljes hozzáférés | Korlátozott információ | Korlátozott információ |
| Biztonsági jelentések | Kockázatészlelések | Teljes hozzáférés | Korlátozott információ | Nem |
| Értesítések | Veszélyeztetett felhasználók észlelt riasztásai | Igen | Nem | Nem |
| Értesítések | Heti kivonat | Igen | Nem | Nem |
| | MFA regisztrációs szabályzat | Igen | Nem | Nem |

## <a name="next-steps"></a>Következő lépések

- [Biztonsági áttekintés](concept-identity-protection-security-overview.md)

- [Mi a kockázat](concept-identity-protection-risks.md)

- [A kockázatok enyhítésére rendelkezésre álló szabályzatok](concept-identity-protection-policies.md)
