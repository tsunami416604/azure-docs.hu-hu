---
title: Hitelesítési módszerek használata & Azure Active Directory
description: Jelentéskészítés az Azure AD önkiszolgáló jelszó-visszaállítási és Multi-Factor Authentication hitelesítési módszer használata esetén
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 12/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfff8d450a6506cb37730838e4f70609080ca7b1
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97670791"
---
# <a name="authentication-methods-usage--insights-preview"></a>Hitelesítési módszerek használati &i adatok (előzetes verzió)

A használat & az adatok segítségével megismerheti, hogy az Azure AD Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítási szolgáltatásokhoz hasonló hitelesítési módszerek hogyan működnek a szervezetben. Ez a jelentéskészítési funkció lehetővé teszi a szervezet számára, hogy megértse, milyen módszereket regisztrálnak, és hogyan használják őket.

## <a name="permissions-and-licenses"></a>Engedélyek és licencek

A következő szerepkörök használhatják a használatot és az információkat:

- Globális rendszergazda
- Biztonsági olvasó
- Biztonsági rendszergazda
- Jelentések olvasója

 A használat és az adatok eléréséhez prémium szintű Azure AD P1 vagy P2 licenc szükséges. Az Azure AD Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítási (SSPR) licencelési információk a [Azure Active Directory díjszabási webhelyen](https://azure.microsoft.com/pricing/details/active-directory/)találhatók.

## <a name="how-it-works"></a>Működés

A hitelesítési módszer használatának és az információknak a elérése:

1. Tallózással keresse meg a [Azure Portal](https://portal.azure.com).
1. Keresse meg **Azure Active Directory**  >  **jelszó-visszaállítási**  >  **használati &-információkat**.
1. A **regisztrációs** vagy **használati** áttekintésekben dönthet úgy, hogy az előre szűrt jelentéseket az igényeinek megfelelően szűri.

![Használat & információk áttekintése](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

A használat & az adatok közvetlen eléréséhez nyissa meg a következőt: [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) . Ez a hivatkozás a regisztráció áttekintését fogja elérni.

A regisztrált felhasználók, a felhasználók és a felhasználók számára kompatibilis csempék a következő regisztrációs adatait jelenítik meg a felhasználók számára:

- Regisztrálva: A felhasználó regisztrálva van, ha (vagy egy rendszergazda) elég hitelesítési módszert regisztrált a szervezete SSPR vagy Multi-Factor Authentication házirendjének teljesítéséhez.
- Engedélyezve: a rendszer engedélyezi A felhasználó számára, hogy a SSPR szabályzat hatókörében van-e. Ha a SSPR engedélyezve van egy csoport számára, akkor a rendszer engedélyezi a felhasználót, ha az adott csoportban vannak. Ha a SSPR engedélyezve van az összes felhasználó számára, akkor a bérlő összes felhasználója (kivéve a vendégeket) engedélyezettnek tekintendő.
- Képes: A felhasználó akkor tekinthető megfelelőnek, ha regisztrálva van és engedélyezve van. Ez az állapot azt jelenti, hogy szükség esetén bármikor elvégezhetik a SSPR.

Ha bármelyik csempére kattint, vagy a bennük megjelenő információk megtalálhatók, a rendszer a regisztrációs adatok előre szűrt listáját jeleníti meg.

**A regisztráció lapon található** **regisztrációk** diagram a sikeres és sikertelen hitelesítési módszereknek a hitelesítési módszer alapján történő regisztrációjának számát jeleníti meg. A **használat** lapon **a diagram alaphelyzetbe állítása** a sikeres és sikertelen hitelesítések számát jeleníti meg a jelszó-visszaállítási folyamat során a hitelesítési módszer használatával.

A diagramok bármelyikére kattintva megtekintheti a regisztrációs vagy alaphelyzetbe állítási események előre szűrt listáját.

A felső, jobb oldali sarokban lévő vezérlőelem használatával módosíthatja a regisztrációban megjelenített naplózási adatok dátumtartományt, és visszaállíthatja a diagramokat 24 órára, 7 napra vagy 30 napra.

### <a name="registration-details"></a>Regisztráció részletei

Ha a **regisztrált felhasználók**, a **felhasználók** vagy a **felhasználók számára** megfelelő csempék vagy információk lehetőségre kattint, a rendszer a regisztrációs adatokat fogja használni.

A regisztráció részletei jelentés az alábbi információkat jeleníti meg az egyes felhasználókra vonatkozóan:

- Name
- Felhasználónév
- Regisztrációs állapot (összes, regisztrált, nincs regisztrálva)
- Engedélyezett állapot (mind, engedélyezve, nincs engedélyezve)
- Képes állapot (az összes, nem alkalmas, nem használható)
- Metódusok (alkalmazás-értesítés, alkalmazás kódja, telefonhívás, SMS, E-mail, biztonsági kérdések)

A lista tetején található vezérlők használatával megkeresheti a felhasználókat, és szűrheti a felhasználók listáját a megjelenített oszlopok alapján.

### <a name="reset-details"></a>Alaphelyzetbe állítás részletei

Ha a regisztrációra kattint, vagy alaphelyzetbe állítja a diagramokat, a rendszer visszaállítja a részleteket.

A részletek visszaállítása jelentés az elmúlt 30 nap regisztrációs és alaphelyzetbe állítási eseményeit jeleníti meg, beleértve a következőket:

- Name
- Felhasználónév
- Szolgáltatás (összes, regisztráció, alaphelyzetbe állítás)
- Hitelesítési módszer (alkalmazás-értesítés, alkalmazás kódja, telefonhívás, Office-hívás, SMS, E-mail, biztonsági kérdések)
- Állapot (összes, sikeres, sikertelen)

A lista tetején található vezérlők használatával megkeresheti a felhasználókat, és szűrheti a felhasználók listáját a megjelenített oszlopok alapján.

## <a name="limitations"></a>Korlátozások

Az ezekben a jelentésekben megjelenő adatszolgáltatások 60 percen belül késleltetve lesznek. Az "utolsó frissítés" mező létezik a Azure Portalban annak azonosításához, hogy milyen közelmúltbeli az adatai.

A használati és adatelemzési adatok nem helyettesíti az Azure ad Multi-Factor Authentication tevékenységi jelentéseit vagy az Azure AD bejelentkezési jelentésében található információkat.

A jelentés jelenleg nem szűrhető úgy, hogy kizárja a külső felhasználókat.

## <a name="next-steps"></a>Következő lépések

- [A hitelesítési módszerek használati jelentésének API használata](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [A szervezet hitelesítési módszereinek kiválasztása](concept-authentication-methods.md)
- [Kombinált regisztrációs élmény](concept-registration-mfa-sspr-combined.md)