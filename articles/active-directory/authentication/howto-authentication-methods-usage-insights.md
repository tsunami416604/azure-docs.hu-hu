---
title: A hitelesítési módszerek használata & elemzések – Azure Active Directory
description: Jelentés készítés az Azure AD önkiszolgáló jelszó-alaphelyzetbe állításról és a többtényezős hitelesítéshitelesítési módszer használatáról
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1730939de399cacd13c62988259904ba84ee78ff
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654149"
---
# <a name="authentication-methods-usage--insights-preview"></a>A hitelesítési módszerek használata & elemzések (előzetes verzió)

A használati & elemzések segítségével megismerheti, hogyan működnek az Azure többtényezős hitelesítéshez és az önkiszolgáló jelszó-visszaállításhoz használt hitelesítési módszerek a szervezetben. Ez a jelentéskészítési funkció biztosítja a szervezet számára a regisztrációhoz szükséges módszerek megértéséhez szükséges eszközöket, és hogyan használják őket.

## <a name="permissions-and-licenses"></a>Engedélyek és licencek

A következő szerepkörök férhetnek hozzá a használathoz és az elemzési adatokhoz:

- Globális rendszergazda
- Biztonsági olvasó
- Biztonsági rendszergazda
- Jelentések olvasó

A használat és az elemzések eléréséhez nincs szükség további licencelésre. Az Azure többtényezős hitelesítéssel és önkiszolgáló jelszó-visszaállítási (SSPR) licencelési információkkal kapcsolatos információk az [Azure Active Directory díjszabási webhelyén](https://azure.microsoft.com/pricing/details/active-directory/)találhatók.

## <a name="how-it-works"></a>Működés

A hitelesítési módszerek használatának és elemzési adatainak elérése:

1. Keresse fel az [Azure Portalt](https://portal.azure.com).
1. Tallózással keresse fel az **Azure Active Directory** > **jelszó-visszaállítási** > & insights című**részében.**
1. A **Regisztráció** vagy **a Használat** áttekintésből megnyithatja az előre szűrt jelentéseket, hogy az igényeinek megfelelően szűrjön.

![Felhasználási & elemzések – áttekintés](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

A használati & elemzések [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)közvetlen eléréséhez nyissa meg a t. Ez a link a regisztrációs áttekintést fogja eredményezni.

A Regisztrált felhasználók, a Felhasználók engedélyezve és a Felhasználók képes csempék a következő regisztrációs adatokat jelenítik meg a felhasználók számára:

- Regisztrált: A felhasználó akkor tekinthető regisztráltnak, ha ők (vagy egy rendszergazda) elegendő hitelesítési módszert regisztráltak ahhoz, hogy megfeleljenek a szervezet SSPR vagy többtényezős hitelesítési házirendjének.
- Engedélyezve: A felhasználó akkor tekinthető engedélyezettnek, ha az SSPR-házirend hatókörében van. Ha az SSPR engedélyezve van egy csoporthoz, akkor a felhasználó akkor tekinthető engedélyezve, ha ebben a csoportban van. Ha az SSPR minden felhasználó számára engedélyezve van, akkor a bérlő összes felhasználója (a vendégek kivételével) engedélyezettnek minősül.
- Képes: A felhasználó akkor tekinthető alkalmasnak, ha regisztrálva és engedélyezve van. Ez az állapot azt jelenti, hogy szükség esetén bármikor végrehajthatják az SSPR-t.

Ha rákattint ezekre a csempékre vagy a bennük látható elemzési adatokra, akkor a regisztrációs adatok előre szűrt listájára kerül.

A **Regisztráció** lap **Regisztrációk** diagramja a sikeres és sikertelen hitelesítési módszerregisztrációk számát mutatja hitelesítési módszerrel. A **Használat** lap **Alaphelyzetbe állítás** diagramja a sikeres és sikertelen hitelesítések számát mutatja a jelszó-visszaállítási folyamat során hitelesítési módszerrel.

Ha rákattint valamelyik diagramra, akkor a regisztrációs vagy visszaállítási események előre szűrt listájára kerül.

A jobb felső sarokban lévő vezérlővel a Regisztrációk és alaphelyzetbe állítás diagramokban megjelenített naplózási adatok dátumtartományát 24 órára, 7 napra vagy 30 napra módosíthatja.

### <a name="registration-details"></a>Regisztrációs adatok

Ha a **Regisztrált felhasználók**, a **Felhasználók engedélyezve**vagy a **Felhasználók képes** csempékre vagy elemzési adatokra kattint, akkor megjelenik a regisztrációs adatok.

A regisztrációs adatok jelentés a következő információkat jeleníti meg az egyes felhasználók számára:

- Név
- Felhasználónév
- Regisztrációs állapot (minden, regisztrált, nincs regisztrálva)
- Engedélyezett állapot (minden, engedélyezve, Nincs engedélyezve)
- Képes állapot (Minden, Képes, Nem képes)
- Módszerek (Alkalmazásértesítés, Alkalmazáskód, Telefonhívás, SMS, E-mail, Biztonsági kérdések)

A lista tetején található vezérlők segítségével megkereshet egy felhasználót, és szűrheti a felhasználók listáját a megjelenített oszlopok alapján.

### <a name="reset-details"></a>Részletek alaphelyzetbe állítása

A Regisztrációk vagy alaphelyzetbe állítás diagramokra kattintva megnyílik a visszaállítás részletei.

A visszaállításrészleteiről szóló jelentés az elmúlt 30 nap regisztrációs és alaphelyzetbe állítási eseményeit jeleníti meg, többek között a következőket:

- Név
- Felhasználónév
- Funkció (mind, regisztráció, visszaállítás)
- Hitelesítési módszer (alkalmazásértesítés, alkalmazáskód, telefonhívás, Office-hívás, SMS, E-mail, Biztonsági kérdések)
- Állapot (mind, sikeres, sikertelen)

A lista tetején található vezérlők segítségével megkereshet egy felhasználót, és szűrheti a felhasználók listáját a megjelenített oszlopok alapján.

## <a name="limitations"></a>Korlátozások

Az ezekben a jelentésekben megjelenő adatok akár 60 percet is elhalaszthatnak. Az Azure Portalon létezik egy "Utolsó frissítés" mező, amely azonosítja az adatok legutóbbi adatait.

A használati és elemzési adatok nem helyettesítik az Azure többtényezős hitelesítési tevékenységjelentéseit vagy az Azure AD bejelentkezések jelentésben szereplő információkat.

## <a name="next-steps"></a>További lépések

- [A hitelesítési módszerek használati jelentés API-jának használata](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [A szervezet hitelesítési módszereinek kiválasztása](concept-authentication-methods.md)
- [Kombinált regisztrációs tapasztalat](concept-registration-mfa-sspr-combined.md)
