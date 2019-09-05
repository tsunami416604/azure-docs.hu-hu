---
title: Az alapházirend megköveteli az MFA használatát a rendszergazdák számára – Azure Active Directory
description: Feltételes hozzáférési szabályzat a többtényezős hitelesítés megköveteléséhez a rendszergazdák számára
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
ms.openlocfilehash: 213540a5b6c77146155365133f2cca08eea25351
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608157"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>Alapterv házirend: MFA megkövetelése a rendszergazdák számára (előzetes verzió)

A rendszerjogosultságú fiókokhoz hozzáféréssel rendelkező felhasználók korlátlan hozzáféréssel rendelkeznek a környezethez. Ezeknek a fiókoknak a kihasználása miatt speciális gondossággal kell kezelnie őket. A rendszerjogosultságú fiókok védelmének egyik gyakori módszere, ha a bejelentkezéshez a fiókok ellenőrzésének erősebb formáját igényli. Azure Active Directory a többtényezős hitelesítés (MFA) megkövetelésével erősebb fiókokat lehet ellenőrizni.

**Az MFA megkövetelése a rendszergazdák számára (előzetes verzió)**  olyan alapszintű [szabályzat](concept-baseline-protection.md) , amely minden alkalommal megköveteli az MFA használatát, amikor az alábbi jogosultsági szintű rendszergazdai szerepkörök valamelyike jelentkezik be:

* Globális rendszergazda
* SharePoint-rendszergazda
* Exchange-rendszergazda
* Feltételes hozzáférési rendszergazda
* Biztonsági rendszergazda
* Segélyszolgálat rendszergazdája/jelszó-rendszergazda
* Számlázási adminisztrátor
* Felhasználói adminisztrátor

Az MFA megkövetelése a rendszergazdák számára házirend engedélyezésekor a fenti kilenc rendszergazdai szerepkörre lesz szükség az MFA-regisztrációhoz a hitelesítő alkalmazás használatával. Az MFA-regisztráció befejeződése után a rendszergazdáknak minden alkalommal be kell hajtaniuk az MFA-t, amikor bejelentkeznek.

## <a name="deployment-considerations"></a>Telepítési szempontok

Mivel az **MFA használata a rendszergazdák számára (előzetes verzió)** házirend minden kritikus rendszergazda esetében érvényes, számos megfontolandó szempontot kell figyelembe venni a zökkenőmentes üzembe helyezés érdekében. Ezen megfontolások közé tartozik az Azure AD azon felhasználói és szolgáltatási alapelveinek azonosítása, amelyeken nem végezhető el az MFA, valamint a szervezet által használt, a modern hitelesítést nem támogató alkalmazások és ügyfelek.

### <a name="legacy-protocols"></a>Örökölt protokollok

A levelezési ügyfelek a korábbi hitelesítési protokollokat (IMAP, SMTP, POP3 stb.) használják a hitelesítési kérések elvégzésére. Ezek a protokollok nem támogatják az MFA-t. A Microsoft által észlelt fiókok többségét az MFA megkerülését megkísérlő örökölt protokollok elleni támadások okozzák. Annak biztosítása érdekében, hogy az MFA-t rendszergazdai fiókba való bejelentkezéskor, a rossz résztvevők ne tudják megkerülni az MFA-t, ez a szabályzat blokkolja az örökölt protokollokból származó rendszergazdai fiókokra vonatkozó összes hitelesítési kérelmet.

> [!WARNING]
> A szabályzat engedélyezése előtt győződjön meg arról, hogy a rendszergazdák nem használnak örökölt hitelesítési protokollokat. Tekintse meg [a következő cikket: How to: Az Azure ad-val való örökölt hitelesítés](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) blokkolása feltételes hozzáféréssel további információért.

## <a name="enable-the-baseline-policy"></a>Az alapkonfiguráció házirend engedélyezése

A házirend **alapkonfigurációjának házirendje: Az MFA megkövetelése a rendszergazdák** számára (előzetes verzió) előre konfigurálva van, és a felső részen jelenik meg, amikor a Azure Portal a feltételes hozzáférés panelre lép.

A szabályzat engedélyezése és a rendszergazdák általi védelem:

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > a**feltételes hozzáférést**.
1. A szabályzatok listájában válassza ki **az alapházirend elemet: MFA megkövetelése a rendszergazdák számára**(előzetes verzió).
1. Állítsa be a házirend **engedélyezése** beállítást a **szabályzat azonnali használatára**.
1. Kattintson a **Mentés**gombra.

> [!WARNING]
> Ha a szabályzat előzetes verzióban volt, **a későbbiekben automatikusan engedélyezheti a szabályzatot** . Ezzel a beállítással csökkenthető a felhasználói hatás hirtelen csökkentése. Ha ezt a lehetőséget választotta, ha elérhetővé vált, **ne használja** automatikusan a házirendet. Ha ezt az alapkonfigurációt szeretné használni, tekintse meg a fenti lépéseket az engedélyezéshez.

## <a name="next-steps"></a>További lépések

További információkért lásd:

* [A feltételes hozzáférés alapkonfigurációjának védelmi házirendjei](concept-baseline-protection.md)
* [Öt lépés a személyazonossági infrastruktúra biztonságossá tételéhez](../../security/fundamentals/steps-secure-identity.md)
* [Mi a feltételes hozzáférés a Azure Active Directory?](overview.md)
