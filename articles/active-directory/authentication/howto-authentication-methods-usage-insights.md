---
title: Hitelesítési módszerek használati & insights reporting (előzetes verzió) – az Azure Active Directory
description: Jelentéskészítés az Azure AD önkiszolgáló jelszó-visszaállítás és a multi-factor Authentication hitelesítési módszer használata
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0f6a74308f1bc4a7b77576fb9f39f965de0a4f8
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561028"
---
# <a name="authentication-methods-usage--insights-preview"></a>Hitelesítési módszerek használati & insights (előzetes verzió)

Használat & insights lehetővé teszi, hogy jobban megismerhesse, szolgáltatások, mint az Azure multi-factor Authentication és az új jelszó önkiszolgáló kérésének hitelesítési módszerek működését a szervezetben. Ez a jelentéskészítő funkció megtudhatja, hogy milyen módszerekkel regisztrált, és hogyan van használva, hogy a szervezet biztosítja.

## <a name="permissions-and-licenses"></a>Engedélyek és licencek

A következő szerepkörök érhető el a használati és insights:

- Globális rendszergazda
- Biztonsági olvasó
- Biztonsági rendszergazda
- Jelentésolvasó

Nincs további licencelési nem szükséges hozzáférési használati és elemzéseket. Az Azure multi-factor Authentication és az önkiszolgáló jelszó-visszaállítási (SSPR) licencelési információk találhatók a [Azure Active Directory díjszabását ismertető a hely](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Működés

Hitelesítési módszer használatát és insights elérése:

1. Keresse fel az [Azure Portalt](https://portal.azure.com).
1. Keresse meg a **Azure Active Directory** > **új jelszó kérésére vonatkozó** > **használat & insights**.
1. Az a **regisztrációs** vagy **használati** áttekintéséhez lehet váltani, nyissa meg az előre szűrt jelentések szűrése igényei alapján.

![Használat & insights áttekintése](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Használat & insights közvetlenül eléréséhez [ https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade). Ez a hivatkozás irányítja a regisztráció áttekintése.

A felhasználók regisztrált, a felhasználók engedélyezett és a felhasználók a képes csempék megjelenítése a következő regisztrációs adatokat a felhasználók számára:

- Regisztrálva: A felhasználó akkor minősül, ha azok (vagy egy rendszergazda) elegendő hitelesítési módszereket, és megfelelnek a szervezet SSPR vagy többtényezős hitelesítési házirend van regisztrálva.
- Engedélyezve: A felhasználó akkor minősül, ha az SSPR-házirend a hatókörben vannak engedélyezve. Ha az SSPR engedélyezve van egy csoporthoz, akkor a felhasználó akkor minősül, ha benne vannak engedélyezve. Ha az SSPR minden felhasználó számára engedélyezve van, majd (a vendégeket kivéve) a bérlő összes felhasználója számítanak engedélyezve van.
- Képes: A felhasználó akkor minősül képes, ha van is regisztrálva és engedélyezve van. Ez az állapot azt jelenti, hogy végezhetnek az SSPR bármikor szükség esetén.

Ezek a csempék és a bennük a megjelenített elemzések kattintva irányítja a regisztrációs adatokat előre szűrt listája.

A **regisztrációk** a diagram a **regisztrációs** lapon számát jeleníti meg a sikeres és sikertelen hitelesítési módszer regisztrációk hitelesítési módszerrel. A **visszaállítja** a diagram a **használati** lapon tekintheti meg a sikeres és sikertelen hitelesítések során a jelszó alaphelyzetbe állításához folyamat hitelesítési módszert.

Valamelyik diagramra kattintva kell haladnia a regisztrációs előre szűrt listája, vagy alaphelyzetbe állítása az eseményeket.

Használja a vezérlőelem felső, jobb oldali sarokban, módosíthatja a dátumtartományt, a naplózási adatok 24 óra, 7 nap vagy 30 napig a regisztrációk és alaphelyzetbe diagramokon látható.

A regisztrációs adatokat a 

### <a name="registration-details"></a>Regisztráció részletei

Kattintson a a **regisztrált felhasználók**, **engedélyezett felhasználók**, vagy **képes a felhasználók** csempéket vagy elemzési eredményeket irányítja a regisztrációs adatokat.

A regisztráció részletei jelentés bemutatja az egyes felhasználók a következő információkat:

- Name (Név)
- Felhasználónév
- Regisztrációs állapot (az összes, a regisztrált, nincs regisztrálva)
- Állapot engedélyezve (az összes, engedélyezve, nincs engedélyezve)
- Képes a állapota (az összes alkalmas, nem kompatibilis)
- Metódusok (alkalmazásban megjelenő értesítésre, kódját, telefonhívás, SMS, e-mailben, a biztonsági kérdések)

A vezérlőket használja, a lista tetején, keresse meg a felhasználót, és szűrje a listát a felhasználók látható oszlopok alapján.

### <a name="reset-details"></a>Részletek alaphelyzetbe állítása

A regisztráció vagy alaphelyzetbe diagramok kattintva irányítja a visszaállítás részleteit.

A visszaállítási részletei jelentés megjeleníti az elmúlt 30 napban, beleértve a regisztrálása és visszaállítása eseményeket:

- Name (Név)
- Felhasználónév
- A szolgáltatás (az összes, a regisztrációt alaphelyzetbe)
- Hitelesítési módszer (alkalmazásban megjelenő értesítésre, kódját, telefonhívás, az Office-hívás, SMS, e-mailben, a biztonsági kérdések)
- Állapot (All, sikeres, sikertelen)

A vezérlőket használja, a lista tetején, keresse meg a felhasználót, és szűrje a listát a felhasználók látható oszlopok alapján.

## <a name="limitations"></a>Korlátozások

Ezekben a jelentésekben megjelenített adatok alapján akár 60 perc késni fog. Hogyan legutóbbi van az adatok az Azure Portalon létezik "Utolsó frissítés" mező.

Nem helyettesíti a tevékenységre vonatkozó jelentések az Azure multi-factor Authentication vagy az Azure AD bejelentkezési jelentések foglalt információk a használati és elemzéseket.

## <a name="next-steps"></a>További lépések

- [A hitelesítési módszerek használati jelentés API használata](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [A szervezet hitelesítési módszerek kiválasztása](concept-authentication-methods.md)
- [Kombinált regisztrációs élmény](concept-registration-mfa-sspr-combined.md)
