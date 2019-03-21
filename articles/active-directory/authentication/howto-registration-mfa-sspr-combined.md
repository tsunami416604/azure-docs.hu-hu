---
title: Egyesített regisztrációs Ismerkedés az Azure AD SSPR és a többtényezős hitelesítés (előzetes verzió)
description: Enable kombinált Azure AD multi-factor Authentication és az önkiszolgáló jelszó-átállítási regisztrációk (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a6896e2b9633b8de679e8d14a7957dc0e3229e7
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226725"
---
# <a name="enable-combined-security-information-registration-preview"></a>Kombinált engedélyezése biztonsági információk regisztrációs (előzetes verzió)

Mielőtt engedélyezné az új felhasználói felületre, tekintse át a [biztonsági információk regisztrációs (előzetes verzió) együttes](concept-registration-mfa-sspr-combined.md) hogy biztosan megismerje a működését, és ez a funkció a hatását.

![Egyesített biztonsági információk regisztrációs továbbfejlesztett bejelentkezéskor további információt kérő felületet. A példában regisztrálása a Microsoft Authenticator alkalmazás első módszerként.](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Egyesített biztonsági információkat regisztráció Azure multi-factor Authentication és az Azure AD önkiszolgáló jelszó-visszaállítás az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="enable-combined-registration"></a>A kombinált regisztráció engedélyezése

A következő lépéseket kombinált regisztrációs engedélyezése:

1. Jelentkezzen be az Azure portal felhasználói rendszergazdája vagy globális rendszergazdaként.
2. Keresse meg a **Azure Active Directory** > **felhasználói beállítások** > **kezelheti a hozzáférési panel előzetes verziójú funkciók beállításait**.
3. Alatt **felhasználók előzetes verziójú funkciók regisztrálásához és a biztonsági adatok kezelése – a frissítés**, beállítással engedélyezi egy **kijelölt** csoport, felhasználók vagy a **összes** felhasználók.

![A kombinált biztonsági adatok előzetes felület az összes felhasználó számára az Azure AD portálon engedélyezése](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> A telefonhívási beállítások verzióját 2019. március kezdve nem lesz elérhető ingyenes vagy próbaverziója az Azure AD-bérlők MFA és az SSPR felhasználók számára. SMS-ezni, ez a változás nem érinti. Telefonhívás továbbra is elérhető a felhasználók számára a fizetős Azure AD-bérlőt. Ez a változás csak az Azure AD ingyenes vagy próbaverziója bérlők hatással van.

> [!NOTE]
> Miután engedélyezi kombinált regisztrációs felhasználók, akik regisztrálása vagy megerősítése a telefonszám vagy mobilalkalmazás segítségével az új funkció a segítségükkel az MFA és az SSPR, ha azokat a módszereket engedélyezve vannak a többtényezős hitelesítés és az SSPR-házirendek. Ezután tiltsa le a felhasználói élményt, ha felhasználók, akik nyissa meg az előző SSPR regisztrációs oldalon található `https:/aka.ms/ssprsetup` multi-factor authentication végrehajtása a lap eléréséhez szükséges.

Ha konfigurálta a zónákhoz való társításának listája a hely a következő helyek ugyanabban a zónában kell lennie az Internet Explorer:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>További lépések

[A többtényezős hitelesítés és az SSPR elérhető módszerek](concept-authentication-methods.md)

[Új jelszó önkiszolgáló kérésének konfigurálása](howto-sspr-deployment.md)

[Az Azure multi-factor Authentication konfigurálása](howto-mfa-getstarted.md)

[Hibaelhárítási kombinált biztonsági adatok regisztrálása](howto-registration-mfa-sspr-combined-troubleshoot.md)