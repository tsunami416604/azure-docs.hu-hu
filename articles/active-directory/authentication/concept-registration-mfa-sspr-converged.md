---
title: A hiperkonvergens regisztráció az Azure AD SSPR és a többtényezős hitelesítés (nyilvános előzetes verzió)
description: Az Azure AD multi-factor Authentication hitelesítés és az önkiszolgáló jelszó-átállítási regisztrációk (nyilvános előzetes verzió)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: af57faddcc1413747b4bb847e27287ba86562175
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056381"
---
# <a name="converged-registration-for-self-service-password-reset-and-azure-multi-factor-authentication-public-preview"></a>A hiperkonvergens regisztráció önkiszolgáló jelszó-visszaállítás és az Azure multi-factor Authentication (nyilvános előzetes verzió)

Mostanáig felhasználók hitelesítési módszerek regisztrálása az Azure multi-factor Authentication (MFA) és az önkiszolgáló jelszó-visszaállítási (SSPR) két különböző portálokon szükség volt. Hány felhasználó lett eleinte, hogy a hasonló módszerekkel Azure MFA és az SSPR használták, és nem szeretne regisztrálni mindkét portálokon. Ez a különbség néhány felhasználó nem használható, vagy az Azure MFA, vagy az SSPR, amikor szükséges, a segélyszolgálat hívása, ami alatt, és egy figyelmébe felhasználó vezetett. Most a felhasználók egyszer regisztrálni és Azure MFA és az SSPR, így nem kell kétszer regisztrálja a hitelesítési módszereiket, a következő szolgáltatások előnyeinek kihasználása.  

Mielőtt a szervezet számára engedélyezi az új felületet, azt javasoljuk, hogy elolvasta-e ez a cikk, valamint a [végfelhasználói dokumentációt](https://aka.ms/securityinfoguide) a hatás megismeréséhez tapasztalatok lesz a felhasználók számára. Használhatja a [végfelhasználói dokumentációt](https://aka.ms/securityinfoguide) betanítására és készítse elő a felhasználók, az új élményt, és ellenőrizze, sikeres bevezetéshez.

|     |
| --- |
| Önkiszolgáló jelszó-visszaállítás és az Azure multi-factor Authentication konvergens regisztráció az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Ahhoz, hogy a felhasználók hitelesítési módszerek regisztrálása az Azure multi-factor Authentication és az önkiszolgáló jelszó-visszaállítás egyetlen felületen, a következő lépéseket:

1. Jelentkezzen be egy globális rendszergazdai vagy a felhasználó rendszergazdaként az Azure Portalon.
2. Keresse meg a **Azure Active Directory**, **felhasználói beállítások**, **kezelheti a hozzáférési panel előzetes verziójú funkciók beállításait**.
3. A **felhasználók használhatják a előzetes verziójú funkciók regisztrálásához és a biztonsági adatok kezelése**, beállítással engedélyezi egy **kijelölt** csoport, felhasználók vagy a **összes** felhasználók.

Felhasználók ellátogathatnak most [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo) MFA és az SSPR hitelesítési módszereinek regisztrálhat. Mi a felhasználók látni fognak az új felületet kapcsolatos további információkért tekintse meg a [végfelhasználói dokumentációt](https://aka.ms/securityinfoguide).  

> [!NOTE]
> Miután engedélyezte, hogy ez a tapasztalat, felhasználók, akik regisztrálni, illetve erősítse meg a telefonszámát, vagy mobilalkalmazás segítségével az új felület méretváltozata használni őket MFA és az SSPR, ha azokat a módszereket engedélyezve vannak a többtényezős hitelesítés és az SSPR-házirendek. Ha ez a tapasztalat majd letiltja, felhasználók, akik keresse meg az előző SSPR regisztrációs oldalra, aka.ms/ssprsetup hajthatok végre MFA, az oldal eléréséhez kell adni.  

## <a name="how-it-works"></a>Működés

Ha a felhasználó korábban már regisztrálva van a hitelesítési módszerek segítségével a különálló MFA és az SSPR regisztrációs élményt, azokat nem kell regisztrálnia ezt az információt újra. Azonban a beállítások a felhasználók regisztráljanak a többtényezős hitelesítés vagy az SSPR használatához, akkor jelenhet meg egy parancssort, tekintse át a biztonsági adataikat, amikor bejelentkezik.

Ha egy felhasználó olyan módszer, amely a multi-factor Authentication használható van regisztrálva, kéri a hajthatok végre MFA, az új funkció eléréséhez.

Ha Ön rendelkezik regisztrációs MFA vagy az SSPR és a egy felhasználó még nem regisztrált, kéri, amikor bejelentkeznek a regisztrálásához.

Felhasználók, akik a rendszer felszólítja a regisztráció közben jelentkezik be a következő élmény jelenik meg:

![A hiperkonvergens regisztráció. Új felhasználói metódusok beállítása](./media/concept-registration-mfa-sspr-converged/concept-registration-add-methods.png)

> [!NOTE]
> Ez a tapasztalat csak jelennek meg ha a felhasználó regisztrálása közben jelentkezik be kéri. Felhasználók, akik közvetlenül az új tapasztalata aka.ms/setupsecurityinfo jelenik meg a felhasználói felületen, a cikk későbbi részében ismertetett egy másik verziója.

A hitelesítési módszerek látható a módszerek engedélyezve van az MFA vagy az SSPR-házirendek alapján változik. A felhasználó hitelesítési módszerek felel meg a többtényezős hitelesítési szabályzat, SSPR szabályzat vagy mindkettő szükséges minimális száma regisztrálni kell adnia. Ha rugalmasság a hitelesítési módszerekről a felhasználó regisztrálhatja, és kiválaszthatja **válassza ki a biztonsági adatok** további hitelesítési módszerek kiválasztásához.  

> [!NOTE]
> Mobilalkalmazás-értesítés és a mobilalkalmazás-kód használatának engedélyezése, ha a felhasználók, akik regisztrálni a Microsoft Authenticator alkalmazás használatával értesítést értesítés és kód is használhatják az identitásukat.

Ellentétben az előző MFA regisztrációs működését nem kéri a felhasználótól egy alkalmazásjelszót regisztrálni az új regisztrációs felület végighaladva. Ehelyett az alkalmazások regisztrálni alkalmazásjelszókat az új funkció a jelszavakat az oktatóanyagban szereplő lépéseket követik.  

Miután a felhasználó regisztrációs elkészül, az alapértelmezett MFA módszer automatikusan lesz beállítva. Ha a felhasználó regisztrálta egy hitelesítő alkalmazást, az alkalmazás az alapértelmezett módszer lesz beállítva. Ha a felhasználó nem regisztrálta egy hitelesítő alkalmazást, és csak regisztrált telefonszámát, az alapértelmezett módszer telefonhívás fog szerepelni. A felhasználók módosíthatják az alapértelmezett a [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo) és kiválasztásával **alapértelmezés módosítása**.  

Regisztráció nem lép életbe, ha a felhasználók kezelhetik a saját hitelesítési módszerek [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo). A felhasználó korábban már regisztrálva van egy hajthatok végre MFA segítségével módszert, ha a rendszer megkéri többtényezős hitelesítés végrehajtására, a lap eléréséhez.  

![A hiperkonvergens regisztráció. Módosítsa a módszerekkel regisztrált felhasználók](./media/concept-registration-mfa-sspr-converged/concept-registration-edit-methods.png)

Ezen az oldalon a felhasználók látni fogják korábban regisztrált hitelesítési módszerek és a hitelesítési módszerek, például az irodai telefon regisztrálva őket. Felhasználók is hozzáadása, szerkesztése vagy törlése a hitelesítési módszereket (kivéve az irodai telefon) is.  

Az új felületet tartozó auditnaplók az auditnapló hitelesítési módszerek kategóriában található.  

## <a name="known-issues"></a>Ismert problémák

**Alapértelmezett MFA módszer telefonhívás van beállítva, amikor a felhasználó regisztrálja az SMS-üzenet tehető**

   * Néhány felhasználó Észreveheti, hogy az alapértelmezett MFA módszer beállítása telefonhívás után regisztrálják a telefonszámukat, szöveges üzenet használatával. Felhasználók fel tudják oldani a problémát úgy módosítja az alapértelmezett módszer a cikkben található utasításokat követve [kezelheti a biztonsági adatok (előzetes verzió)](../user-help/security-info-manage-settings.md#change-your-info).

**A felhasználó nem fér hozzá az új regisztrációs felület, miután a rendszergazda kikapcsolja az alapértelmezett módszer**

   * Néhány felhasználót nem lehet férhetnek hozzá az új regisztrációs felület, ha a korábban regisztrált alapértelmezett MFA módszert a rendszergazda által le van tiltva. Íme egy példa:
      1. Felhasználó korábban regisztrálva a telefonszámukat és telefonon hívja fel az alapértelmezett módszert állítja be.
      2. Rendszergazda kikapcsolja az telefonhívás MFA módszerként a bérlő számára.
      3. Regisztrálja a bejelentkezés során, mivel szükségük van egy további módszer felel meg a bérlő az SSPR csoportházirend regisztráljon kéri a felhasználótól.
      4. Felhasználó próbál meg regisztrálni, de az oldal nem kell egy beállított alapértelmezett metódus miatt nem érhető és hurokba került elakadt.

## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan lehet üzembe helyezni az Azure AD önkiszolgáló jelszó-visszaállítás](howto-sspr-deployment.md)

[Ismerje meg, hogyan többtényezős hitelesítés megkövetelése bejelentkezéskor](howto-mfa-getstarted.md)

[Végfelhasználói hitelesítés metódus konfigurációs dokumentációja](https://aka.ms/securityinfoguide)