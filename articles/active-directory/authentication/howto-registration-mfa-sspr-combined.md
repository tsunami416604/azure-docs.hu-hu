---
title: Egyesített regisztrációs Ismerkedés az Azure AD SSPR és a multi-factor Authentication (előzetes verzió) – az Azure Active Directory
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
ms.openlocfilehash: 3baf2690ae07b87bb4d5dba30fcd20f62a1a4506
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59280570"
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
> Miután engedélyezte a kombinált regisztrációs, felhasználók, akik regisztrálni, illetve erősítse meg a telefonszámát vagy mobilalkalmazás segítségével az új funkció a segítségükkel a multi-factor Authentication és az SSPR, ha azokat a módszereket engedélyezve vannak a multi-factor Authentication és az SSPR házirendek. Ezután tiltsa le a felhasználói élményt, ha felhasználók, akik nyissa meg az előző SSPR regisztrációs oldalon található `https:/aka.ms/ssprsetup` multi-factor authentication végrehajtása a lap eléréséhez szükséges.

Ha konfigurálta a zónákhoz való társításának listája a hely az Internet Explorer, a következő helyek kell lennie az ugyanabban a zónában:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>További lépések

[A multi-factor Authentication és az SSPR elérhető módszerek](concept-authentication-methods.md)

[Új jelszó önkiszolgáló kérésének konfigurálása](howto-sspr-deployment.md)

[Az Azure multi-factor Authentication konfigurálása](howto-mfa-getstarted.md)

[Hibaelhárítási kombinált biztonsági adatok regisztrálása](howto-registration-mfa-sspr-combined-troubleshoot.md)