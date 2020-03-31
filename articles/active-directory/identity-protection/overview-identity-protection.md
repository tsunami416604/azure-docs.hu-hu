---
title: Mi az az Azure Active Directory Identity Protection?
description: Kockázatok észlelése, kiújítása, vizsgálata és elemzése az Azure AD Identity Protection segítségével
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 03/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2b1d9748b243dcc2104ce7b8e0e8735a7b7276f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79497677"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Mi az az Azure Active Directory Identity Protection?

Az Identitásvédelem olyan eszköz, amely lehetővé teszi a szervezetek számára, hogy három kulcsfontosságú feladatot végezzenek el:

- Automatizálhatja az identitásalapú kockázatok észlelését és szervizelését.
- Kockázatok vizsgálata a portálon lévő adatok használatával.
- További elemzés céljából exportálja a kockázatészlelési adatokat harmadik féltől származó segédprogramokba.

Az Identity Protection azAzure AD-vel, a Microsoft-fiókkal rendelkező fogyasztói térrel és az Xbox-szal való játékokkal kapcsolatos tanulási lehetőségeket használja a Microsoft által a szervezetben szerzett adatokból, hogy megvédje a felhasználókat. A Microsoft naponta 6,5 billió jelet elemez, hogy azonosítsa és megvédje az ügyfeleket a fenyegetésektől.

Az Identitásvédelem által generált és az identitásvédelembe táplált jelek tovább táplálhatók olyan eszközökbe, mint a feltételes hozzáférés a hozzáférési döntések meghozatalához, vagy visszatáplálhatók egy biztonsági információs és eseménykezelési (SIEM) eszközhöz a szervezet kényszerített házirendek.

## <a name="why-is-automation-important"></a>Miért fontos az automatizálás?

2018 októberében Alex Weinert, a Microsoft identitásbiztonsági és -védelmi csapatának vezetője [2018 októberében közzétett blogbejegyzésében](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) elmagyarázza, hogy miért olyan fontos az automatizálás az események mennyiségének kezelése során:

> Gépi tanulási és heurisztikus rendszereink naponta 18 milliárd bejelentkezési kísérlethez biztosítanak kockázati pontszámokat több mint 800 millió különböző fiókhoz, amelyek közül 300 milliót észrevehetően az ellenfelek végeznek (például bűnöző iratok, hackerek).
>
> Tavaly az Ignite-nál beszéltem az identitásrendszerünk elleni 3 legjobb támadásról. Itt van a legutóbbi mennyisége ezeket a támadásokat
>   
>   - **Megsértése visszajátszás**: 4.6BN támadások észlelt május 2018
>   - **Jelszó spray**: 350k 2018 áprilisában
>   - **Adathalászat**: Ezt nehéz pontosan számszerűsíteni, de 23M kockázati eseményeket láttunk 2018 márciusában, amelyek közül sok adathalászattal kapcsolatos

## <a name="risk-detection-and-remediation"></a>Kockázatészlelés és -elhárítás

Az Identitásvédelem a következő besorolásokban azonosítja a kockázatokat:

| Kockázatészlelés típusa | Leírás |
| --- | --- |
| Atipikus utazás | Jelentkezzen be egy atipikus helyről a felhasználó legutóbbi bejelentkezései alapján. |
| Névtelen IP-cím | Jelentkezzen be névtelen IP-címről (például Tor böngésző, anonimizáló VPN-ek). |
| Ismeretlen bejelentkezési tulajdonságok | Jelentkezzen be olyan tulajdonságokkal, amelyeket a közelmúltban nem láttunk az adott felhasználó nál. |
| Kártevőhez csatolt IP-cím | Bejelentkezés rosszindulatú programokhoz csatolt IP-címről |
| Kiszivárgott hitelesítő adatok | Ez a kockázatészlelés azt jelzi, hogy a felhasználó érvényes hitelesítő adatai kiszivárogtak |
| Azure AD fenyegetésfelderítés | A Microsoft belső és külső fenyegetésfelderítési forrásai ismert támadási mintát azonosítottak |

További részletek ezekről a kockázatokról és hogyan / mikor számítják ki megtalálható a cikkben, [Mi a kockázat](concept-identity-protection-risks.md).

A kockázati jelek kiválthatja a javítási erőfeszítéseket, például a felhasználók számára, hogy: azure többtényezős hitelesítés, állítsa alaphelyzetbe a jelszót önkiszolgáló jelszó alaphelyzetbe állítása, vagy blokkolja, amíg a rendszergazda lépéseket.

## <a name="risk-investigation"></a>Kockázatvizsgálat

A rendszergazdák áttekinthetik az észleléseket, és szükség esetén manuálisan is végrehajthatják azokat. A rendszergazdák három kulcsfontosságú jelentést használnak az identitásvédelemben folytatott vizsgálatokhoz:

- Kockázatos felhasználók
- Kockázatos bejelentkezések
- Kockázatészlelések

További információ a cikkben található: [Hogyan: Vizsgálja meg](howto-identity-protection-investigate-risk.md)a kockázatot .

## <a name="exporting-risk-data"></a>Kockázati adatok exportálása

Az Identity Protection adatai exportálhatók más eszközökre archiválás, további vizsgálat és társkapcsolat céljából. A Microsoft Graph alapú API-k lehetővé teszik a szervezetek számára, hogy ezeket az adatokat további feldolgozáscéljából összegyűjtsék egy olyan eszközben, mint például a SIEM. Az Identity Protection API elérésével kapcsolatos információk a cikkben találhatók: [Az Azure Active Directory identity Protection és](howto-identity-protection-graph-api.md) a Microsoft Graph – Első lépések

Az Identitásvédelem adatainak az Azure Sentinellel való integrálásáról a Cikkben olvashat, az [Azure AD Identity Protection adatainak csatlakoztatása című](../../sentinel/connect-azure-ad-identity-protection.md)cikkben.

## <a name="permissions"></a>Engedélyek

Az identitásvédelem hez a felhasználóknak biztonsági olvasónak, biztonsági operátornak, biztonsági rendszergazdának, globális olvasónak vagy globális rendszergazdának kell lenniük a hozzáféréshez.

| Szerepkör | Meg tudja csinálni | Nem tudom. |
| --- | --- | --- |
| Globális rendszergazda | Teljes hozzáférés az identitásvédelemhez |   |
| Biztonsági rendszergazda | Teljes hozzáférés az identitásvédelemhez | Felhasználó jelszavának alaphelyzetbe állítása |
| Biztonsági operátor | Az identitásvédelem összes jelentésének és áttekintési paneljének megtekintése <br><br> A felhasználói kockázat elvetése, a biztonságos bejelentkezés megerősítése, a kompromisszum megerősítése | Házirendek konfigurálása vagy módosítása <br><br> Felhasználó jelszavának alaphelyzetbe állítása <br><br> Riasztások konfigurálása |
| Biztonsági olvasó | Az identitásvédelem összes jelentésének és áttekintési paneljének megtekintése | Házirendek konfigurálása vagy módosítása <br><br> Felhasználó jelszavának alaphelyzetbe állítása <br><br> Riasztások konfigurálása <br><br> Visszajelzés küldése az észlelésekről |

A feltételes hozzáférésű rendszergazdák olyan házirendeket is létrehozhatnak, amelyek feltételként figyelembe veszik a bejelentkezési kockázatot, további információt a [Feltételes hozzáférés: Feltételek](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)című cikkben találnak.

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Képesség | Részletek | Prémium szintű Azure AD P2 | Prémium szintű Azure AD P1 | Azure AD Alapszintű/ingyenes |
| --- | --- | --- | --- | --- |
| Kockázati politikák | Felhasználói kockázati házirend (identitásvédelem révén) | Igen | Nem | Nem |
| Kockázati politikák | Bejelentkezési kockázati házirend (identitásvédelem vagy feltételes hozzáférés révén) | Igen | Nem | Nem |
| Biztonsági jelentések | Áttekintés | Igen | Nem | Nem |
| Biztonsági jelentések | Kockázatos felhasználók | Teljes hozzáférés | Korlátozott információ | Korlátozott információ |
| Biztonsági jelentések | Kockázatos bejelentkezések | Teljes hozzáférés | Korlátozott információ | Korlátozott információ |
| Biztonsági jelentések | Kockázatészlelések | Teljes hozzáférés | Korlátozott információ | Nem |
| Értesítések | A veszélyeztetett felhasználók riasztásokat észleltek | Igen | Nem | Nem |
| Értesítések | Heti kivonat | Igen | Nem | Nem |
| | MFA regisztrációs irányelvei | Igen | Nem | Nem |

## <a name="next-steps"></a>További lépések

- [Biztonsági áttekintés](concept-identity-protection-security-overview.md)

- [Mi a kockázat?](concept-identity-protection-risks.md)

- [A kockázatok csökkentésére rendelkezésre álló politikák](concept-identity-protection-policies.md)
