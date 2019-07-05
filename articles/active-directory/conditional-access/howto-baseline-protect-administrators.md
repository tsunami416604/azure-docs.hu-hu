---
title: Alapvető házirendet az Azure Active Directory-rendszergazdák – a többtényezős hitelesítés megkövetelése
description: A többtényezős hitelesítés megkövetelése a rendszergazdák feltételes hozzáférési szabályzat
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
ms.openlocfilehash: 4474283b9a233e39497cd05f0f04ea0984f02401
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560940"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>Alapvető házirendet: Többtényezős hitelesítés megkövetelése a rendszergazdák (előzetes verzió)

Kiemelt jogosultságú fiókok hozzáféréssel rendelkező felhasználó ugyanolyan korlátlan hozzáféréssel rendelkezik a környezetben. A teljesítmény, a fiókokra, mert kezelje őket gondosan. Egy közös módszerrel történő kiemelt jogosultságú fiókok védelméhez, hogy egy fiókhitelesítés erősebb formája kérése, amikor használhassák jelentkezhetnek be. Az Azure Active Directoryban azzal, hogy a többtényezős hitelesítés (MFA) egy erősebb fiókhitelesítés kaphat.

**Többtényezős hitelesítés (előzetes verzió) rendszergazdák** van egy [alapvető házirendet](concept-baseline-protection.md) , hogy a többtényezős Hitelesítést igényel, és minden alkalommal jelentkezik be a következő emelt szintű rendszergazdai szerepkörök egyike:

* Globális rendszergazda
* SharePoint-rendszergazda
* Exchange-rendszergazda
* Feltételes hozzáférésű rendszergazda
* Biztonsági rendszergazda
* Ügyfélszolgálati adminisztrátor / rendszergazdai jelszó
* Számlázási rendszergazda
* Felhasználói adminisztrátor

Követően a rendszergazdák házirend szükséges többtényezős hitelesítés engedélyezése, a fenti kilenc rendszergazdai szerepköröket kell regisztrálni a multi-factor Authentication az Authenticator alkalmazás használatával. MFA-regisztráció befejezése után a rendszergazdák hajthatok végre MFA minden egyetlen alkalommal be kell.

## <a name="deployment-considerations"></a>Telepítési szempontok

Mivel a **rendszergazdák (előzetes verzió) a többtényezős hitelesítés megkövetelése** házirend vonatkozik az összes kritikus rendszergazdák, különböző szempontok lesz szükség ahhoz, hogy egy zökkenőmentes üzembe helyezési. Ezeket a szempontok közé tartoznak, felhasználók és az Azure AD nem tudja vagy nem kell végrehajtani, az MFA, valamint az alkalmazások és az ügyfelek a szervezet által használt, modern hitelesítést nem támogató szolgáltatás alapelvek azonosító.

### <a name="legacy-protocols"></a>Örökölt protokollok

E-mail az örökölt hitelesítési protokollok (IMAP, SMTP, POP3, stb.) segítségével hitelesítési kéréseket. Ezeket a protokollokat nem támogatja a többtényezős hitelesítés. A Microsoft által látott fiók értelmeznie a legtöbb örökölt protokollok MFA kerülni próbál elleni támadások végrehajtása kártékony elemek okozzák. Győződjön meg arról, hogy többtényezős hitelesítés szükséges egy rendszergazdai fiókkal való bejelentkezéskor és kártékony elemek nincs MFA kerülheti el, ez a szabályzat blokkolja a rendszergazdai fiókokhoz készült örökölt protokollok minden hitelesítési kérelemre.

> [!WARNING]
> Mielőtt engedélyezné ezt a házirendet, győződjön meg arról, a rendszergazdák nem használja az örökölt hitelesítési protokollok. Tekintse meg a cikket [hogyan: Az Azure AD feltételes hozzáférés letiltása a régebbi hitelesítési](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) további információt.

## <a name="enable-the-baseline-policy"></a>Az alapkonfiguráció-házirend engedélyezése

A szabályzat **alapvető házirendet: Többtényezős hitelesítés (előzetes verzió) rendszergazdák** előre konfigurálva, és jelennek meg az oldal tetején a feltételes hozzáférés paneljén, az Azure Portalon lépve.

A szabályzat engedélyezéséhez, és a rendszergazdák védelmére:

1. Jelentkezzen be a **az Azure portal** globális rendszergazdai, biztonsági rendszergazdai vagy feltételes hozzáférésű rendszergazda.
1. Keresse meg a **az Azure Active Directory** > **feltételes hozzáférési**.
1. A házirendek listájából válassza ki **alapvető házirendet: Többtényezős hitelesítés (előzetes verzió) rendszergazdák**.
1. Állítsa be **házirend engedélyezése** való **a házirend azonnal használható**.
1. Kattintson a **mentése**.

> [!WARNING]
> Hiba történt egy beállítást **szabályzat automatikus engedélyezés a jövőben** Ha e szabályzat volt érvényes előzetes verzióban érhető el. Eltávolítottuk ezt a beállítást, amikor hirtelen felhasználókra gyakorolt hatás minimalizálása érdekében. Ha ezt a beállítást választotta, amikor volt elérhető, **ne használja a házirend** automatikusan miután már kiválasztotta. Ha használni kívánja az alapvető házirendet, tekintse meg a engedélyezze azt a fenti lépéseket.

## <a name="next-steps"></a>További lépések

További információkért lásd:

* [Feltételes hozzáférés alapkonfiguráció alkalmazásvédelmi szabályzatok](concept-baseline-protection.md)
* [Öt lépést a személyazonosság-infrastruktúra védelme](../../security/azure-ad-secure-steps.md)
* [Mi az az Azure Active Directory feltételes hozzáférés?](overview.md)
