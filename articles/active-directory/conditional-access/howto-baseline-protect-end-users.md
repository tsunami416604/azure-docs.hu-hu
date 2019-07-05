---
title: Végfelhasználói protection (előzetes verzió) – az Azure Active Directory alapvető házirendet
description: Feltételes hozzáférési szabályzatot a felhasználók számára a többtényezős hitelesítés megkövetelése
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
ms.openlocfilehash: f2644e0e35139ac470b89f6af1b95cf510f60a0a
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561009"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Alapvető házirendet: Végfelhasználói protection (előzetes verzió)

Hogy általában úgy gondolja, hogy a rendszergazdai fiókok biztosítják a multi-factor Authentication (MFA) védelemre van szükségük csak fiókokat. A rendszergazdák bizalmas adatok széles körű hozzáférése, és módosíthatja az előfizetés kiterjedő beállítások. Kártékony elemek azonban általában a cél végfelhasználók számára. Hozzáfér, ezek kártékony elemek nevében az eredeti fióktulajdonos információk hozzáférés kérése, vagy töltse le a adathalász támadások, a teljes cég számára a végrehajtásához a teljes címtárra. Egy közös módszer védelmét az összes felhasználó számára, akkor kötelező, például a többtényezős hitelesítést (MFA) a Fiókellenőrzés erősebb formája.

Biztonság és a használhatóság megfelelő egyensúly eléréséhez nem megkérdezi a felhasználókat minden egyetlen alkalommal be. Hitelesítési kéréseket, amelyek tükrözik a normál felhasználói viselkedést, ugyanarról a helyről ugyanarra az eszközre jelentkezik be például a biztonsági sérülés alacsony esélyét rendelkezik. Csak bejelentkezések kockázatos minősülnek, és kiderítsék jellemzői megjelenítése a rendszer kéri az MFA megoldandó feladatot is teremthet.

Végfelhasználói védelem, a kockázatalapú MFA [alapvető házirendet](concept-baseline-protection.md) , amely védi a minden felhasználó egy könyvtárban, beleértve az összes rendszergazdai szerepköröket. Minden felhasználó számára a multi-factor Authentication használatával az Authenticator alkalmazás regisztrálása a szabályzat engedélyezése szükséges. Felhasználók az MFA regisztrációs kérés figyelmen kívül a 14 nap elteltével, a rendszer letiltja a jelentkezik be, amíg nem regisztrálják az MFA-hoz. A multi-factor Authentication regisztrálása után felhasználók felszólítást kapnak a multi-factor Authentication csak a kockázatos bejelentkezési kísérlet során. Feltört felhasználói fiókok le lesznek tiltva, amíg a jelszavát alaphelyzetbe áll, és a kockázati események elutasítottnak.

> [!NOTE]
> Ez a szabályzat többek között a Vendég minden felhasználóra vonatkozik, és akkor lesz kiértékelve, amikor bejelentkezik az összes alkalmazást.

## <a name="recovering-compromised-accounts"></a>Fiókok helyreállítása biztonsága sérült.

Elkövettünk ügyfeleink védelme érdekében a Microsoft kiszivárgott hitelesítő adatok szolgáltatása megkeresi a nyilvánosan elérhető felhasználónév/jelszó párokat. Ha megfelelnek a felhasználóink egyikét, hogy biztonságossá tétele, hogy a fiók azonnal. A felhasználók, amelyek az kiszivárgott hitelesítő adatok megerősített biztonsága sérült. Ezek a felhasználók bejelentkezésének, amíg a jelszavát alaphelyzetbe áll le lesz tiltva.

Egy prémium szintű Azure AD-licenccel rendelkező önkiszolgáló jelszó-visszaállítás (SSPR) keresztüli hozzáférés helyreállíthatja, ha a funkció engedélyezve van a címtárban. Felhasználók premium licenc nélkül, hogy le legyen tiltva kapcsolatba kell lépnie a rendszergazda a manuális jelszó-visszaállítás végrehajtása és bezárása a megjelölt felhasználók kockázati esemény.

### <a name="steps-to-unblock-a-user"></a>A felhasználó feloldásának lépéseit

Győződjön meg arról, hogy a felhasználó letiltotta a szabályzatot a felhasználó bejelentkezési naplók vizsgálatával.

1. A rendszergazdának kell bejelentkezni a **az Azure portal** , és keresse meg **Azure Active Directory** > **felhasználók** > annak a felhasználónak a nevére, és keresse meg a bejelentkezések.
1. Az új jelszavak kérését a letiltott felhasználó kezdeményezhet, a rendszergazdának kell lépjen **Azure Active Directory** > **kockázatosként megjelölt felhasználók**
1. Kattintson a felhasználóra, akinek a fiókja le van tiltva, a felhasználó legutóbbi bejelentkezési tevékenységre vonatkozó információk megtekintése.
1. Kattintson a jelszó alaphelyzetbe állítása egy ideiglenes jelszót, amely a következő bejelentkezéskor módosítania kell hozzárendelni.
1. Kattintson a felhasználó kockázati pontszámát alaphelyzetbe állítása az összes esemény elvetését.

A felhasználó most már bejelentkezhet, alaphelyzetbe állíthatja a jelszavát, és az alkalmazás eléréséhez.

## <a name="deployment-considerations"></a>Telepítési szempontok

Mivel a **végfelhasználói védelmi** a címtárban szereplő összes felhasználó szabályzat vonatkozik, több szempontok lesz szükség ahhoz, hogy egy zökkenőmentes üzembe helyezési. Ezeket a szempontok közé tartoznak, felhasználók és az Azure AD nem tudja vagy nem kell végrehajtani, az MFA, valamint az alkalmazások és az ügyfelek a szervezet által használt, modern hitelesítést nem támogató szolgáltatás alapelvek azonosító.

### <a name="legacy-protocols"></a>Örökölt protokollok

E-mail az örökölt hitelesítési protokollok (IMAP, SMTP, POP3, stb.) segítségével hitelesítési kéréseket. Ezeket a protokollokat nem támogatja a többtényezős hitelesítés.  A Microsoft által látott fiók értelmeznie a legtöbb örökölt protokollok MFA kerülni próbál elleni támadások végrehajtása kártékony elemek okozzák. Győződjön meg arról, hogy többtényezős hitelesítés szükséges fiókkal való bejelentkezéskor és kártékony elemek nincs MFA kerülheti el, ez a szabályzat blokkolja a rendszergazdai fiókokhoz készült örökölt protokollok minden hitelesítési kérelemre.

> [!WARNING]
> Mielőtt engedélyezné ezt a házirendet, győződjön meg arról, a felhasználók nem használja az örökölt hitelesítési protokollok. Tekintse meg a cikket [hogyan: Az Azure AD feltételes hozzáférés letiltása a régebbi hitelesítési](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) további információt.

## <a name="enable-the-baseline-policy"></a>Az alapkonfiguráció-házirend engedélyezése

A szabályzat **alapvető házirendet: Végfelhasználói protection (előzetes verzió)** előre konfigurálva, és jelennek meg az oldal tetején a feltételes hozzáférés paneljén, az Azure Portalon lépve.

A szabályzat engedélyezéséhez és a felhasználók védelmét:

1. Jelentkezzen be a **az Azure portal** globális rendszergazdai, biztonsági rendszergazdai vagy feltételes hozzáférésű rendszergazda.
1. Keresse meg a **az Azure Active Directory** > **feltételes hozzáférési**.
1. A házirendek listájából válassza ki **alapvető házirendet: Végfelhasználói protection (előzetes verzió)** .
1. Állítsa be **házirend engedélyezése** való **a házirend azonnal használható**.
1. Kattintson a **mentése**.

## <a name="next-steps"></a>További lépések

További információkért lásd:

* [Feltételes hozzáférés alapkonfiguráció alkalmazásvédelmi szabályzatok](concept-baseline-protection.md)
* [Öt lépést a személyazonosság-infrastruktúra védelme](../../security/azure-ad-secure-steps.md)
* [Mi az az Azure Active Directory feltételes hozzáférés?](overview.md)
