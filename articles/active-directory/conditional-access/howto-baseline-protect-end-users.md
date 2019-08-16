---
title: Alapkonfiguráció házirend végfelhasználói védelme (előzetes verzió) – Azure Active Directory
description: Feltételes hozzáférési szabályzat a többtényezős hitelesítés megköveteléséhez a felhasználók számára
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
ms.openlocfilehash: 131d38f6154e7a6e2f3175838b084e47e17ec582
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532932"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Alapterv házirend: Végfelhasználói védelem (előzetes verzió)

Úgy gondoljuk, hogy a rendszergazdai fiókok csak a többtényezős hitelesítéssel (MFA) szembeni védelmet igénylő fiókok. A rendszergazdák széles körű hozzáféréssel rendelkeznek a bizalmas adatokhoz, és módosíthatják az előfizetési szintű beállításokat. Azonban a rossz színészek általában a végfelhasználók számára célozzák meg. A hozzáférés megszerzése után ezek a rossz szereplők az eredeti fiók tulajdonosának nevében igényelhetnek hozzáférést a Kiemelt információkhoz, vagy a teljes könyvtárat letöltve elvégezhetik az adathalászat elleni támadást a teljes szervezeten belül. Az egyik gyakori módszer az, hogy javítsa az összes felhasználó védelmét, így a fiókok ellenőrzésének erősebb formáját kell megkövetelni, például a többtényezős hitelesítést (MFA).

A biztonság és a használhatóság ésszerű egyensúlyának elérése érdekében a felhasználóknak nem kell megadniuk minden alkalommal, amikor bejelentkeznek. A normál felhasználói viselkedést tükröző hitelesítési kérések, például ugyanarról az eszközről való bejelentkezés ugyanazon a helyen, kis valószínűséggel sérülnek. Az MFA-kihívásokkal csak a kockázatos és a rossz színész jellemzőinek megjelenítését kérő bejelentkezések szükségesek.

A végfelhasználói védelem a kockázatalapú MFA alapszabályzata, amely a címtár összes felhasználóját védi, beleértve az összes rendszergazdai szerepkört is. [](concept-baseline-protection.md) Ennek a szabályzatnak az engedélyezéséhez minden felhasználónak regisztrálnia kell az MFA-t a hitelesítő alkalmazás használatával. A felhasználók 14 napig figyelmen kívül hagyhatják az MFA regisztrációs kérését, amely után a rendszer letiltja a bejelentkezést, amíg nem regisztrálja az MFA-t. Az MFA-regisztráció után a rendszer csak a kockázatos bejelentkezési kísérletek során kéri a felhasználókat a többtényezős hitelesítésre. A rendszer letiltja a feltört felhasználói fiókokat, amíg a jelszó alaphelyzetbe nem áll, és a kockázati események el lettek távolítva.

> [!NOTE]
> Ez a szabályzat minden felhasználóra vonatkozik, beleértve a vendég fiókokat, és az összes alkalmazásba való bejelentkezéskor lesz kiértékelve.

## <a name="recovering-compromised-accounts"></a>Sérült fiókok helyreállítása

Ügyfeleink védelméhez a Microsoft kiszivárgott hitelesítő adatai nyilvánosan elérhető felhasználóneveket és jelszavakat találnak. Ha megfelelnek valamelyik felhasználójuk, akkor a fiók azonnali védelmét nyújtjuk. A kiszivárgott hitelesítő adatokként azonosított felhasználókat a rendszer megerősíti. A rendszer letiltja a felhasználók bejelentkezését, amíg a jelszó alaphelyzetbe nem áll.

A prémium szintű Azure AD licenccel rendelkező felhasználók visszaállíthatják a hozzáférést az önkiszolgáló jelszó-visszaállítás (SSPR) használatával, ha a képesség engedélyezve van a címtárban. A letiltott prémium szintű licenccel nem rendelkező felhasználóknak kapcsolatba kell lépniük a rendszergazdával a jelszó manuális alaphelyzetbe állításához és a megjelölt felhasználói kockázati esemény elvetéséhez.

### <a name="steps-to-unblock-a-user"></a>A felhasználó blokkolásának feloldásának lépései

Győződjön meg arról, hogy a házirend letiltotta a felhasználót a felhasználó bejelentkezési naplóinak vizsgálatával.

1. A rendszergazdának be kell jelentkeznie a **Azure Portalba** , és meg kell keresnie **Azure Active Directory** > **felhasználókat** > kattintson a felhasználó nevére, és navigáljon a bejelentkezések elemre.
1. A jelszó alaphelyzetbe állításának elindításához a rendszergazdának meg kell keresnie **Azure Active Directory** > a**kockázatnak megjelölt felhasználókat**
1. Kattintson arra a felhasználóra, akinek a fiókja le van tiltva, és tekintse meg a felhasználó legutóbbi bejelentkezési tevékenységével kapcsolatos információkat.
1. Kattintson a jelszó alaphelyzetbe állítása elemre egy ideiglenes jelszó hozzárendeléséhez, amelyet a következő bejelentkezéskor módosítani kell.
1. Kattintson az összes esemény elvetése lehetőségre a felhasználó kockázati pontszámának alaphelyzetbe állításához.

A felhasználó most már bejelentkezhet, visszaállíthatja a jelszavát, és elérheti az alkalmazást.

## <a name="deployment-considerations"></a>Telepítési szempontok

Mivel a **végfelhasználói védelmi** házirend a címtárban lévő összes felhasználóra vonatkozik, számos megfontolandó szempontot kell figyelembe venni a zökkenőmentes üzembe helyezés biztosítása érdekében. Ezen megfontolások közé tartozik az Azure AD azon felhasználói és szolgáltatási alapelveinek azonosítása, amelyeken nem végezhető el az MFA, valamint a szervezet által használt, a modern hitelesítést nem támogató alkalmazások és ügyfelek.

### <a name="legacy-protocols"></a>Örökölt protokollok

A levelezési ügyfelek a korábbi hitelesítési protokollokat (IMAP, SMTP, POP3 stb.) használják a hitelesítési kérések elvégzésére. Ezek a protokollok nem támogatják az MFA-t.  A Microsoft által észlelt fiókok többségét az MFA megkerülését megkísérlő örökölt protokollok elleni támadások okozzák. Annak biztosítása érdekében, hogy a rendszer az MFA-t a fiókba való bejelentkezéshez és a rossz szereplőkkel nem tudja megkerülni az MFA megkerülését, ez a szabályzat blokkolja az örökölt protokollokból származó rendszergazdai fiókokra vonatkozó összes hitelesítési kérelmet

> [!WARNING]
> A szabályzat engedélyezése előtt győződjön meg arról, hogy a felhasználók nem használnak örökölt hitelesítési protokollokat. Tekintse meg [a következő cikket: How to: Az Azure ad-val való örökölt hitelesítés](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) blokkolása feltételes hozzáféréssel további információért.

## <a name="enable-the-baseline-policy"></a>Az alapkonfiguráció házirend engedélyezése

A házirend **alapkonfigurációjának házirendje: A végfelhasználói védelem (előzetes** verzió) előre konfigurálva van, és a felső részen jelenik meg, amikor a Azure Portal a feltételes hozzáférés panelre lép.

A szabályzat engedélyezése és a felhasználók védetté tétele:

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > a**feltételes hozzáférést**.
1. A szabályzatok listájában válassza ki **az alapházirend elemet: Végfelhasználói védelem (előzetes verzió**).
1. Állítsa be a házirend **engedélyezése** beállítást a **szabályzat azonnali használatára**.
1. Kattintson a **Save** (Mentés) gombra.

## <a name="next-steps"></a>További lépések

További információkért lásd:

* [A feltételes hozzáférés alapkonfigurációjának védelmi házirendjei](concept-baseline-protection.md)
* [Öt lépés a személyazonossági infrastruktúra biztonságossá tételéhez](../../security/fundamentals/steps-secure-identity.md)
* [Mi a feltételes hozzáférés a Azure Active Directory?](overview.md)
