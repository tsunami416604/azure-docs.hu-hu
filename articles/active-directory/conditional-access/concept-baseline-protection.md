---
title: Feltételes hozzáférés alapkonfiguráció alkalmazásvédelmi szabályzatok – Azure Active Directory
description: Alapkonfiguráció feltételes hozzáférési szabályzatok védelme érdekében a szervezet a gyakori támadásoktól
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca062f4024bb5b0946812e00c8ccc1254b56d333
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003296"
---
# <a name="what-are-baseline-policies"></a>Mik azok az eredeti szabályzatok?

Alapkonfiguráció-házirendek olyan előre definiált házirendjei, amelyek segítségével a szervezetek sok gyakori támadások elleni védelmét. Ezek a gyakori támadásoktól jelszó megfelelő, a visszajátszás és adathalász is tartalmazhat. Alapkonfiguráció házirendek az Azure AD összes kiadásában érhetők el. A Microsoft, hogy így ezek alapkonfiguráció alkalmazásvédelmi szabályzatok érhető el mindenki számára, mert a azonosító-alapú támadások voltak az elmúlt néhány évben fellendülőben. Ezek a szabályzatok célja, győződjön meg arról, hogy minden olyan szervezetnek nincs engedélyezve a biztonsági alapkonfiguráció szinttel rendelkeznek, külön költségek.  

Egyéni feltételes hozzáférési szabályzatok kezelése egy prémium szintű Azure AD-licenc szükséges.

## <a name="baseline-policies"></a>Alapvető szabályzatok

![Alapkonfiguráció feltételes hozzáférési szabályzatokat az Azure Portalon](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Nincsenek négy alapvető szabályzatok, ami lehetővé teszi a szervezetek számára:

* [Többtényezős hitelesítés a rendszergazdák számára](howto-baseline-protect-administrators.md)
* [Végfelhasználói protection (előzetes verzió)](howto-baseline-protect-end-users.md)
* [Az örökölt hitelesítés letiltása (előzetes verzió)](howto-baseline-protect-legacy-auth.md)
* [Többtényezős hitelesítés a service management (előzetes verzió)](howto-baseline-protect-azure.md)

Ezek a szabályzatok mind a négy örökölt hitelesítési folyamatok például a POP, IMAP és régebbi Office asztali ügyfelek negatív hatással lesz.

### <a name="require-mfa-for-admins"></a>MFA megkövetelése rendszergazdáktól

A power és így hozzáférhet a rendszergazdai fiókok, mert kezelje őket gondosan. Egy közös módszerrel történő kiemelt jogosultságú fiókok védelméhez, hogy egy erősebb formája fiókhitelesítés szükséges való bejelentkezéshez használt. Az Azure Active Directoryban azzal, hogy a rendszergazdák számára, hogy a regisztrálása és használata az Azure multi-factor Authentication szolgáltatás egy erősebb fiókhitelesítés kaphat.

[Többtényezős hitelesítés a rendszergazdák számára](howto-baseline-protect-administrators.md) van alapkonfiguráció szabályzata előírja a többtényezős hitelesítés (MFA) a következő-címtárbeli szerepkörökhöz tartozó, a rendszer a legtöbb emelt szintű Azure AD-szerepkörök:

* Globális rendszergazda
* SharePoint-rendszergazda
* Exchange-rendszergazda
* Feltételes hozzáférésű rendszergazda
* Biztonsági rendszergazda
* Ügyfélszolgálati adminisztrátor / rendszergazdai jelszó
* Számlázási adminisztrátor
* Felhasználói adminisztrátor

Ha a szervezete ezeket a fiókokat a parancsfájlokban vagy a kódot használja, fontolja meg, és cserélje le őket az [felügyelt identitások](../managed-identities-azure-resources/overview.md). Ideiglenes Áthidaló megoldásként a referenciakonfiguráció házirendből zárhat ki egyedi felhasználói fiókokhoz.

### <a name="end-user-protection-preview"></a>Végfelhasználói protection (előzetes verzió)

Magas szintű jogosultsággal rendelkező rendszergazdák nem csak nekik van a támadások célként. Kártékony elemek általában a normál felhasználók számára. Hozzáfér, ezeket is kérhet hozzáférést az információk az eredeti fióktulajdonos nevében, vagy töltse le a teljes címtárra és hajtsa végre a adathalász támadások, a teljes cég számára a. Egy közös védelmét az összes felhasználó számára, hogy egy fiókhitelesítés erősebb formája kérése, ha az észlelt kockázatos bejelentkezés.

**Végfelhasználói protection (előzetes verzió)** egy alapkonfiguráció szabályzat, amely védelmet nyújt a címtárban szereplő összes felhasználó. A szabályzat engedélyezése szükséges regisztrálni az Azure multi-factor Authentication 14 napon belül minden felhasználó. Miután regisztrált, felhasználók felszólítást kapnak a multi-factor Authentication csak a kockázatos bejelentkezési kísérlet során. Feltört felhasználói fiókok le lesznek tiltva, amíg a jelszó alaphelyzetbe állítása és kockázati elbocsátása.

### <a name="block-legacy-authentication-preview"></a>Az örökölt hitelesítés letiltása (előzetes verzió)

Az örökölt hitelesítési protokollok (például: IMAP, SMTP, POP3) olyan protokollok, általában használt régebbi levelezési ügyfelek hitelesítéséhez. Örökölt protokollok nem támogatják a többtényezős hitelesítést. Ha már rendelkezik multi-factor authentication szolgáltatás a címtáron szabályzat, kiderítsék hitelesítheti a ezek az örökölt protokollok egyikével és multi-factor authentication kihagyásához.

A legjobb módszer a rosszindulatú hitelesítési kérést hoz létre örökölt protokollok fiókja védelme érdekében, hogy letiltja őket.

A **örökölt hitelesítés letiltása (előzetes verzió)** alapkonfiguráció szabályzat letiltja a hitelesítési kérelmek örökölt protokollok használatával. Sikeresen jelentkezzen be az összes felhasználó számára a modern hitelesítést kell használni. A referenciakonfiguráció házirendek együtt használja, régebbi protokollokra érkező kérések le lesz tiltva. Emellett minden felhasználó lesz szükség a többtényezős hitelesítés minden alkalommal, amikor szükséges. Ez a szabályzat nem tiltja le az Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Többtényezős hitelesítés a service management (előzetes verzió)

Szervezetek számára az Azure-szolgáltatások különböző használja, és azok kezelését az Azure Resource Manager-alapú eszközök, mint például:

* Azure Portal
* Azure PowerShell
* Azure CLI

Felsorolt eszközök bármelyikével hajtsa végre az erőforrás-kezelés használata esetén a magas jogosultsági szintű műveletet. Ezek az eszközök előfizetéshez kiterjedő konfigurációhoz, például a szolgáltatás beállításainak és az előfizetés számlázási is módosítható.

Privilegizált műveletek védelme érdekében ez **service management (előzetes verzió) a többtényezős hitelesítés megkövetelése** házirendet kell a multi-factor authentication szolgáltatás bármely felhasználó fér hozzá az Azure Portalon, az Azure PowerShell vagy az Azure CLI.

## <a name="enable-a-baseline-policy"></a>Egy alapkonfiguráció házirend engedélyezése

Egy alapkonfiguráció-házirend engedélyezése:

1. Jelentkezzen be a **az Azure portal** globális rendszergazdai, biztonsági rendszergazdai vagy feltételes hozzáférésű rendszergazda.
1. Keresse meg a **az Azure Active Directory** > **feltételes hozzáférési**.
1. A házirendek listájában válassza ki egy alapvető házirendet, amelyet engedélyezni szeretne.
1. Állítsa be **házirend engedélyezése** való **a**.
1. Kattintson a Mentés gombra.

## <a name="next-steps"></a>További lépések

További információkért lásd:

* [Öt lépést a személyazonosság-infrastruktúra védelme](../../security/azure-ad-secure-steps.md)
* [Mi az az Azure Active Directory feltételes hozzáférés?](overview.md)
* [Többtényezős hitelesítés a rendszergazdák számára](howto-baseline-protect-administrators.md)
* [Végfelhasználói protection (előzetes verzió)](howto-baseline-protect-end-users.md)
* [Az örökölt hitelesítés letiltása (előzetes verzió)](howto-baseline-protect-legacy-auth.md)
* [Többtényezős hitelesítés a service management (előzetes verzió)](howto-baseline-protect-azure.md)