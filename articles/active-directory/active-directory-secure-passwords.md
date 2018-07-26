---
title: Azure AD rétegzett jelszóvédelem | Microsoft Docs
description: A cikk azt ismerteti, hogy az Azure AD hogyan kényszeríti ki az erős jelszavak használatát, és hogyan védi a felhasználók jelszavait a számítógépes bűnözőktől.
services: active-directory
documentationcenter: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/28/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 277e66e954d0fdff026d473ba3f6ad07a8b87da7
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173409"
---
# <a name="a-multi-tiered-approach-to-azure-ad-password-security"></a>Többrétegű megközelítés az Azure AD jelszóvédelméhez

Ez a cikk néhány, felhasználóként vagy rendszergazdaként követhető ajánlott eljárást mutat be az Azure Active Directory- (Azure AD-) és Microsoft-fiókok védelme érdekében.

 > [!NOTE]
 > **Azért van itt, mert problémák merültek fel a bejelentkezéssel kapcsolatban?** Ha igen, [így módosíthatja vagy állíthatja alaphelyzetbe a jelszavát](user-help/active-directory-passwords-update-your-own-password.md).
 >
 > Az Azure AD-rendszergazdák az [Azure Active Directory-felhasználók jelszavának alaphelyzetbe állításával](fundamentals/active-directory-users-reset-password-azure-portal.md) kapcsolatos cikkben leírt módon állíthatják alaphelyzetbe a felhasználói jelszavakat.
 >

## <a name="password-requirements"></a>A jelszavakra vonatkozó követelmények

Az Azure AD a következő általános megközelítéseket tartalmazza a jelszavak védelme érdekében:

* Jelszóhosszra vonatkozó követelmények
* A jelszó összetettségére vonatkozó követelmények
* Normál és időszakos jelszólejárat

Az Azure Active Directory-jelszavak alaphelyzetbe állításával kapcsolatos információért tekintse meg [az informatikai szakemberek számára készült, az Azure AD önkiszolgáló jelszó-visszaállításával](user-help/active-directory-passwords-update-your-own-password.md) kapcsolatos témakört.

## <a name="azure-ad-password-protections"></a>Azure AD-jelszóvédelem

Az Azure AD és a Microsoft-fiókrendszer az iparágban már bizonyított megközelítéseket alkalmaz a felhasználói és rendszergazdai jelszavak védelmének biztosítása érdekében, többek között a következőket:

* Dinamikusan letiltott jelszavak
* Intelligens jelszózárolás

A jelszókezeléssel kapcsolatos, aktuális kutatásokon alapuló információkért tekintse meg a [jelszavakról szóló tájékoztató](https://aka.ms/passwordguidance) tanulmányt.

### <a name="dynamically-banned-passwords"></a>Dinamikusan letiltott jelszavak

Az Azure AD- és a Microsoft-fiókok a jelszóvédelem biztosítása érdekében dinamikusan letiltják az összes gyakran használt jelszót. Az Azure AD Identity Protection csapat rendszeresen elemzi a letiltott jelszavak listáit, amely segít meggátolni, hogy a felhasználók gyakran használt jelszavakat válasszanak. Ez a szolgáltatás az Azure AD és a Microsoft-fiókszolgáltatás ügyfeleinek érhető el.

A jelszavak létrehozásakor fontos, hogy a rendszergazdák arra bátorítsák a felhasználókat, hogy betűk, számok, karakterek és szavak egyedi kombinációját tartalmazó jelszavakat válasszanak. Ez a módszer elősegíti, hogy a felhasználói jelszavakat szinte lehetetlen legyen feltörni, de a felhasználók számára könnyebb legyen megjegyezni.

#### <a name="password-breaches"></a>Jelszavakkal való visszaélés

A Microsoft mindig azon dolgozik, hogy egy lépéssel a számítógépes bűnözők előtt járjon.

Az Azure AD Identity Protection csapata folyamatosan elemzi a gyakran használt jelszavakat. A számítógépes bűnözők is hasonló stratégiákat használnak a támadásaikhoz, például [szivárványtáblát](https://en.wikipedia.org/wiki/Rainbow_table) hoznak létre a jelszókivonatok feltörése érdekében.

A Microsoft folyamatosan elemzi az [adatszivárgásokat](https://www.privacyrights.org/data-breaches) a tiltott jelszavak dinamikusan frissített listájának fenntartása érdekében, így a sebezhető jelszavak még azelőtt le lesznek tiltva, hogy valódi fenyegetést jelenthetnének az Azure AD ügyfelei számára. Az aktuális biztonsági erőfeszítéseinkről további információt a [Microsoft biztonsági intelligenciáról szóló jelentésében](https://www.microsoft.com/security/sir/default.aspx) talál.

### <a name="smart-password-lockout"></a>Intelligens jelszózárolás

Amikor az Azure AD érzékeli, hogy egy lehetséges számítógépes bűnöző megpróbál feltörni egy felhasználói jelszót, az Intelligens jelszózárolás segítségével zároljuk a felhasználói fiókot. Az Azure AD-t arra tervezték, hogy meghatározza az egyes bejelentkezési munkamenetekkel társított kockázatokat. Ezután a legfrissebb biztonsági adatok használatával zárolási szemantikát alkalmazunk a számítógépes fenyegetések elhárításához.

Ha egy felhasználó ki van zárva az Azure AD-ből, a képernyője a következőhöz hasonlóan néz ki:

  ![Kizárva az Azure AD-ből](./media/active-directory-secure-passwords/locked-out-azuread.png)

Egyéb Microsoft-fiókok esetén a képernyő a következőhöz hasonlóan néz ki:

  ![Kizárva egy Microsoft-fiókból](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Az Azure Active Directory-jelszavak alaphelyzetbe állításával kapcsolatos információért tekintse meg [az informatikai szakemberek számára készült, az Azure AD önkiszolgáló jelszó-visszaállításával](user-help/active-directory-passwords-update-your-own-password.md) kapcsolatos témakört.

  >[!NOTE]
  >Ha Ön Azure AD-rendszergazda, javasolt a [Windows Hello](https://www.microsoft.com/windows/windows-hello) használata, hogy a felhasználók egyáltalán ne is hozhassanak létre hagyományos jelszavakat.
  >

## <a name="next-steps"></a>További lépések

* [Saját jelszó frissítése](user-help/active-directory-passwords-update-your-own-password.md)
* [Az Azure-identitáskezelés alapjai](fundamentals-identity.md)
* [Jelentés jelszó-visszaállítási tevékenységről](authentication/howto-sspr-reporting.md)
