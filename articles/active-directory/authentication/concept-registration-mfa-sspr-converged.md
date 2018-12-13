---
title: A hiperkonvergens regisztráció az Azure AD SSPR és a többtényezős hitelesítés (nyilvános előzetes verzió)
description: Az Azure AD multi-factor Authentication és az önkiszolgáló jelszó-átállítási regisztrációk (nyilvános előzetes verzió)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: dbced5cfa2a47dc2fdcf630d62104bb7ba8e7bc0
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186650"
---
# <a name="converged-registration-for-self-service-password-reset-and-azure-multi-factor-authentication-public-preview"></a>A hiperkonvergens regisztráció önkiszolgáló jelszó-visszaállítás és az Azure multi-factor Authentication (nyilvános előzetes verzió)

Mostanáig felhasználók hitelesítési módszerek regisztrálása az Azure multi-factor Authentication (MFA) és az önkiszolgáló jelszó-visszaállítási (SSPR) két különböző portálokon szükség volt. Hány felhasználó, hogy a hasonló módszerekkel Azure MFA és az SSPR használták, és nem szeretne regisztrálni mindkét portálokon is összetéveszteni. Néhány felhasználót nem sikerült majd vagy az Azure MFA, vagy az SSPR használatára, ha szükséges, a telefonhívások vezető. 

Most a felhasználók egyszer regisztrálni és Azure MFA és az SSPR előnyeinek kihasználása. Felhasználóknak nem kell kétszer regisztrálja a hitelesítési módszereiket, a következő szolgáltatások.  

Mielőtt a szervezet számára engedélyezi az új felületet, azt javasoljuk, hogy elolvasta-e ez a cikk és a [felhasználói dokumentáció](https://aka.ms/securityinfoguide) , amelyre a felhasználói élményt a felhasználók számára. a hatás megismeréséhez. A felhasználói dokumentációra segítségével betanítása és a felhasználók előkészítése az új felhasználói felületre, és biztosíthatja a sikeres bevezetéshez.

|     |
| --- |
| Konvergens regisztráció önkiszolgáló jelszó-visszaállítás és az Azure multi-factor Authentication az Azure Active Directory (Azure AD) nyilvános előzetes verziójú funkció. Az előzetes verziókra vonatkozó további információért lásd: [Kiegészítő Használati Feltételek a Microsoft Azure Előzetesekhez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Ahhoz, hogy a felhasználók hitelesítési módszerek regisztrálása az Azure multi-factor Authentication és az önkiszolgáló jelszó-visszaállítás egyetlen felületen, a következő lépéseket:

1. Jelentkezzen be egy globális rendszergazdai vagy a felhasználó rendszergazdaként az Azure Portalon.
2. Keresse meg a **Azure Active Directory** > **felhasználói beállítások** > **kezelheti a hozzáférési panel előzetes verziójú funkciók beállításait**.
3. A **felhasználók használhatják a előzetes verziójú funkciók regisztrálásához és a biztonsági adatok kezelése**, beállítással engedélyezi egy **kijelölt** csoport, felhasználók vagy a **összes** felhasználók.

Felhasználók ellátogathatnak most [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo) MFA és az SSPR hitelesítési módszereinek regisztrálhat. Mi a felhasználók látni fognak az új felületet kapcsolatos további információkért tekintse meg a [felhasználói dokumentáció](https://aka.ms/securityinfoguide).  

> [!NOTE]
> Miután engedélyezte a felhasználói élményt, felhasználók, akik regisztrálni, illetve erősítse meg, a telefonszám vagy mobilalkalmazás segítségével az új felhasználói felület is használhatja őket a többtényezős hitelesítés és az SSPR, ha azokat a módszereket engedélyezve vannak a többtényezős hitelesítés és az SSPR-házirendek. Ezután tiltsa le a felhasználói élményt, ha felhasználók, akik az előző SSPR regisztrációs oldalra https:/aka.ms/ssprsetup, nyissa meg kell adni a multi-factor authentication végrehajtása a lap eléréséhez.  

## <a name="how-it-works"></a>Működés

A felhasználó korábban már regisztrálva van a hitelesítési módszerek segítségével a különálló MFA és az SSPR regisztrációs élményt, ha azok nem lesz újból ezt az információt. De a beállítások a felhasználók regisztráljanak a többtényezős hitelesítés vagy az SSPR használatához, ha azok felszólíthatja való bejelentkezéskor, tekintse át a biztonsági információkat.

Ha egy felhasználó olyan módszer, amely a multi-factor Authentication használható van regisztrálva, bekapcsolják a multi-factor authentication végrehajtása az új funkció eléréséhez.

Ha Ön rendelkezik regisztrációs MFA vagy az SSPR és a egy felhasználó még nem regisztrált, bekapcsolják regisztrálni, amikor bejelentkeznek.

Felhasználók, akik a rendszer felszólítja a regisztráció közben jelentkezik be a következő élmény lásd:

![A hiperkonvergens regisztráció. Új felhasználó módszerek állíthatja be.](./media/concept-registration-mfa-sspr-converged/concept-registration-add-methods.png)

> [!NOTE]
> Ez a tapasztalat csak amikor egy felhasználó kéri, hogy regisztrálja aláírás közben látható. Felhasználók, akik közvetlenül a tapasztalata https://aka.ms/setupsecurityinfo tekintse meg a felhasználói felületen, a cikk későbbi részében ismertetett egy másik verziója.

A megjelenített hitelesítési módszerek módosítás engedélyezve van az MFA és az SSPR-házirendek a módszerek alapján. A felhasználónak meg kell adnia a hitelesítési módszerek felel meg a többtényezős hitelesítési szabályzat, SSPR szabályzat vagy mindkettő szükséges minimális számát regisztrálja. Ha rugalmasság a hitelesítési módszerekről a felhasználó regisztrálhatja, és kiválaszthatja **válassza ki a biztonsági adatok** további hitelesítési módszerek kiválasztásához.  

> [!NOTE]
> Mobilalkalmazás-értesítés és a mobilalkalmazás-kód használatának engedélyezése, ha felhasználók, akik regisztrálni a Microsoft Authenticator alkalmazás értesítést keresztül segítségével értesítés és kód is identitásukat.

Ellentétben az előző MFA regisztrációs működését rendszer nem kéri a felhasználókat egy alkalmazásjelszót regisztrálni az új regisztrációs felület végighaladva. Ehelyett követik az alkalmazások jelszavak oktatóanyag regisztrálni alkalmazásjelszókat az új felhasználói felületre a felsorolt lépéseket.  

Miután a felhasználó regisztrációs elkészül, az alapértelmezett MFA módszer értéke automatikusan. Ha a felhasználó regisztrálta egy hitelesítő alkalmazást, az alapértelmezett módszer alkalmazáshoz van beállítva. Ha a felhasználó nem regisztrálta egy hitelesítő alkalmazást, és csak regisztrált telefonszámát, az alapértelmezett módszer telefonhívás értéke. A felhasználók módosíthatják az alapértelmezett a https://aka.ms/setupsecurityinfo és kiválasztásával **alapértelmezés módosítása**.  

Regisztráció nem lép életbe, ha a felhasználók kezelhetik a saját hitelesítési módszerek https://aka.ms/setupsecurityinfo. Ha a felhasználó korábban már regisztrálva van olyan módszer, amely a multi-factor Authentication használható, a rendszer megkéri, a multi-factor authentication végrehajtása a lap eléréséhez.  

![A hiperkonvergens regisztráció. Módosítsa a módszerekkel regisztrált felhasználók.](./media/concept-registration-mfa-sspr-converged/concept-registration-edit-methods.png)

Ezen az oldalon a felhasználók látják a korábban regisztrált hitelesítési módszerek és a hitelesítési módszerek, például az irodai telefon regisztrálva. Felhasználók is hozzáadása, szerkesztése vagy törlése a hitelesítési módszereket (kivéve az irodai telefon) is.  

Az új felületet tartozó auditnaplók az auditnapló hitelesítési módszerek kategóriában található.  

## <a name="known-issues"></a>Ismert problémák

Azonosítottuk a hibát a konvergens, ahol B2B vendégfelhasználók számára engedélyezettek a konvergens regisztrációs kihasználására nem képes a multi-factor Authentication regisztrálása regisztrációs. Amikor a rendszer átirányítja a regisztrációs oldalhoz, a lap hibákat ki. Fejlesztők ismeri a problémát és megoldást nyújtanak a dolgozik. Most a javaslatot, hogy hozzon létre egy csoportot, és bármely B2B-felhasználók kizárása a csoportból.

**Alapértelmezett MFA módszer telefonhívás van beállítva, amikor egy felhasználó regisztrálja magát a telefonon, szöveges üzenet használatával**

   * Néhány felhasználó előfordulhat, hogy figyelje meg, hogy az alapértelmezett MFA módszer beállítása telefonhívás után a szöveges üzenet segítségével regisztrálják telefonszámát. Felhasználók fel tudják oldani a problémát úgy módosítja az alapértelmezett módszer a cikk utasításait követve [kezelheti a biztonsági adatok (előzetes verzió)](../user-help/security-info-manage-settings.md#change-your-info).

**Felhasználó nem tud hozzáférni az új regisztrációs felület, miután a rendszergazda kikapcsolja az alapértelmezett módszer**

   * Egyes felhasználók nem férhetnek hozzá az új regisztrációs felület, ha a rendszergazda letiltotta a korábban regisztrált alapértelmezett MFA módszer. Íme egy példa:
      1. Felhasználó korábban regisztrálva a telefonszámukat és telefonon hívja fel az alapértelmezett módszert állítja be.
      2. Rendszergazda kikapcsolja az telefonhívás MFA módszerként a bérlő számára.
      3. Regisztrálja a bejelentkezés során, mivel szükségük van egy további módszer felel meg a bérlő az SSPR csoportházirend regisztráljon kéri a felhasználótól.
      4. Felhasználói próbál meg regisztrálni, de nem az oldal eléréséhez, és elakadt egy hurokba, mert az alapértelmezett módszer nincs beállítva.

## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan lehet üzembe helyezni az Azure AD önkiszolgáló jelszó-visszaállítás](howto-sspr-deployment.md)

[Ismerje meg, hogyan többtényezős hitelesítés megkövetelése a bejelentkezéshez](howto-mfa-getstarted.md)

[Ismerje meg a felhasználó hitelesítési módszerek konfigurálása](https://aka.ms/securityinfoguide)