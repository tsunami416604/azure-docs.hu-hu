---
title: Egyesített regisztrációs Ismerkedés az Azure AD SSPR és a multi-factor Authentication (előzetes verzió) – az Azure Active Directory
description: Enable kombinált Azure AD multi-factor Authentication és az önkiszolgáló jelszó-átállítási regisztrációk (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d006bd36cc8f8c84fb13bae43702a3e472f8876a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113296"
---
# <a name="enable-combined-security-information-registration-preview"></a>Kombinált engedélyezése biztonsági információk regisztrációs (előzetes verzió)

Mielőtt engedélyezné az új felhasználói felületre, tekintse át a [biztonsági információk regisztrációs (előzetes verzió) együttes](concept-registration-mfa-sspr-combined.md) hogy biztosan megismerje a működését és a funkció hatásait.

![Egyesített biztonsági információk fokozott alkalmazásregisztrációs folyamatot](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Egyesített biztonsági információk regisztráció az Azure multi-factor Authentication és az Azure Active Directory (Azure AD) önkiszolgáló jelszóátállítás az nyilvános előzetes verziójú funkció az Azure AD. Az előzetes verziókra vonatkozó további információért lásd: [Kiegészítő Használati Feltételek a Microsoft Azure Előzetesekhez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="enable-combined-registration"></a>A kombinált regisztráció engedélyezése

Hajtsa végre ezeket a lépéseket, kombinált regisztrációs engedélyezése:

1. Jelentkezzen be az Azure portal felhasználói rendszergazdája vagy globális rendszergazdaként.
2. Lépjen a **Azure Active Directory** > **felhasználói beállítások** > **kezelheti a hozzáférési panel előzetes verziójú funkciók beállításait**.
3. Alatt **felhasználók előzetes verziójú funkciók regisztrálásához és a biztonsági adatok kezelése – a frissítés**, beállítással engedélyezi egy **kijelölt** csoport, felhasználók vagy a **összes** felhasználók.

   ![A kombinált biztonsági adatok előzetes felület az összes felhasználó engedélyezése](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> A március a 2019-től kezdődően a telefonhívási beállítások nem lesznek elérhetők a multi-factor Authentication és az ingyenes vagy próbaverziója az Azure AD-bérlő felhasználóinak az SSPR. Ez a változás nem érinti az SMS-üzenetek. A telefonhívási beállítások továbbra is elérhető lesz a felhasználók számára a fizetős Azure AD-bérlőt.

> [!NOTE]
> Miután engedélyezte a kombinált regisztrációs, felhasználók, akik regisztrálni, illetve erősítse meg a telefonszámát vagy mobilalkalmazás segítségével az új funkció a segítségükkel a multi-factor Authentication és az SSPR, ha azokat a módszereket engedélyezve vannak a multi-factor Authentication és az SSPR házirendek. Ezután tiltsa le a felhasználói élményt, ha felhasználók, akik nyissa meg az előző SSPR regisztrációs oldalon található `https://aka.ms/ssprsetup` multi-factor authentication végrehajtása a lap eléréséhez szükséges.

Ha konfigurálta a zónákhoz való társításának listája a hely az Internet Explorer, a következő helyek kell lennie az ugyanabban a zónában:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Egyesített regisztrációs feltételes hozzáférési szabályzatai

Annak biztosítása, mikor és hogyan az Azure multi-factor Authentication és az önkiszolgáló jelszó-visszaállítás felhasználók Regisztráljon most már minden lehetséges a feltételes hozzáférési szabályzat felhasználói műveletek. Az előzetes verziójú funkció érhető el a szervezetek számára, akik engedélyezték a [kombinált regisztrációs előzetes](../authentication/concept-registration-mfa-sspr-combined.md). Ez a funkció engedélyezett a felhasználók regisztráljanak az Azure multi-factor Authentication és a egy központi helyről, például a megbízható hálózati helyekkel SSPR a HR-előkészítés során haszált szervezetek lehet. Feltételes hozzáférés a megbízható helyek létrehozásával kapcsolatos további információkért tekintse meg a cikket [Mi az a hely feltétel, az Azure Active Directory feltételes hozzáférés?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Hozzon létre egy megbízható helyről regisztráció megkövetelése

A következő szabályzatot a kijelölt felhasználókkal, akik próbál meg regisztrálni, használja a kombinált regisztrációs felületet, és blokkolja a hozzáférést, kivéve, ha csatlakozik a megbízható hálózat megjelölve egy helyre vonatkozik.

![Biztonsági adatok regisztrálása szabályozhatja egy feltételes hozzáférési szabályzat létrehozása](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. Az a **az Azure portal**, keresse meg a **Azure Active Directory** > **feltételes hozzáférés**
1. Válassza az **Új szabályzat** lehetőséget.
1. A név adja meg a szabályzat nevét. Ha például **Security Info regisztrációs kombinált megbízható hálózatokon**
1. A **hozzárendelések**, kattintson a **felhasználók és csoportok**, és válassza ki a felhasználókat és csoportokat szeretné a szabályzatot a alkalmazni

   > [!WARNING]
   > Felhasználók engedélyezni kell a [kombinált regisztrációs előzetes](../authentication/howto-registration-mfa-sspr-combined.md).

1. A **alkalmazások vagy műveleteket a felhő**válassza **felhasználói műveletek**, ellenőrizze **regisztrálja biztonsági információit (előzetes verzió)**
1. A **feltételek** > **helyek**
   1. Konfigurálása **Igen**
   1. Például **bármely helyre**
   1. Kizárandó **minden megbízható hely**
   1. Kattintson a **kész** a helyek panelen
   1. Kattintson a **kész** a feltételek panelen
1. A **hozzáférés-vezérlés** > **megadása**
   1. Kattintson a **hozzáférés letiltása**
   1. Kattintson a **kiválasztása**
1. Állítsa be **házirend engedélyezése** való **a**
1. Kattintson a **létrehozása**

## <a name="next-steps"></a>További lépések

[A multi-factor Authentication és az SSPR elérhető módszerek](concept-authentication-methods.md)

[Új jelszó önkiszolgáló kérésének konfigurálása](howto-sspr-deployment.md)

[Az Azure multi-factor Authentication konfigurálása](howto-mfa-getstarted.md)

[Hibaelhárítási kombinált biztonsági adatok regisztrálása](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Mi az a hely feltétel, az Azure Active Directory feltételes hozzáférés?](../conditional-access/location-condition.md)
