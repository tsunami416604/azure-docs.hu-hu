---
title: Mi a Microsoft Authenticator alkalmazás? - Azure Active Directory | Microsoft dokumentumok
description: Ismerje meg a Microsoft Authenticator alkalmazást, beleértve annak működését, működését és a tartalom ezen szakaszában található információkat.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: c95ae2e48a7d5a3b9e9e28b1d4e14b670205c043
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062285"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>Mi a Microsoft Authenticator alkalmazás?

A Microsoft Authenticator alkalmazás segít a fiókokba való bejelentkezésben, ha kétfaktoros ellenőrzést használ. A kétfaktoros ellenőrzés segítségével biztonságosabban férhet hozzá fiókjaihoz, különösen a bizalmas adatok megtekintése közben. Mivel a jelszavak elfelejthetők, ellophatók vagy feltörhetők, a kétfaktoros ellenőrzés egy további biztonsági lépés, amely segít megvédeni fiókját azáltal, hogy megnehezíti mások számára a betörést.

A Microsoft Authenticator alkalmazást többféleképpen is használhatja, többek között:

- A felhasználónévvel és jelszóval való bejelentkezés után válaszoljon a hitelesítésre vonatkozó kérdésre.

- Jelentkezzen be jelszó megadása nélkül, a felhasználónevével, a hitelesítő alkalmazással és a mobileszközével ujjlenyomatával, arcával vagy PIN-kódjával.

- Kódgenerátorként minden más, hitelesítő alkalmazásokat támogató fiókhoz.

> [!Important]
> A Microsoft Authenticator alkalmazás minden olyan fiókkal működik, amely kétfaktoros ellenőrzést használ, és támogatja az időalapú egyszeri jelszó (TOTP) szabványokat.
>
>Ez a cikk a Microsoft Authenticator alkalmazást biztonsági ellenőrzési módszerként letölteni és használni próbáló felhasználók számára készült. Ha Ön rendszergazdaként szeretne információt tudni arról, hogyan kapcsolhatja be a jelszó nélküli bejelentkezést az Alkalmazottak hitelesítő alkalmazásával, és egyéb célokra, olvassa el [a Jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással (előzetes verzió) című témakört.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-phone)

## <a name="terminology"></a>Terminológia

| Időtartam|Leírás|
| ----|-----------|
| Kétfaktoros ellenőrzés | Olyan ellenőrzési folyamat, amelyhez kifejezetten csak két ellenőrző adatot kell használnia, például egy jelszót és egy PIN-kódot. A Microsoft Authenticator alkalmazás támogatja mind a szabványos kétfaktoros ellenőrzést, mind a jelszó nélküli bejelentkezést. |
| Multi-factor authentication (MFA) | Minden kéttényezős ellenőrzés a többtényezős hitelesítés, amely megköveteli, hogy *legalább* két darab ellenőrzési adatok, a szervezet igényeinek megfelelően. |
| Microsoft-fiók (más néven MSA) | Saját személyes fiókokat hoz létre, hogy hozzáférjen a fogyasztóorientált Microsoft-termékekhez és felhőszolgáltatásokhoz, például az Outlookhoz, a OneDrive-hoz, az Xbox LIVE-hoz vagy az Office 365-höz. A Microsoft-fiókot a Microsoft által működtetett Microsoft fogyasztói identitásfiók-rendszerben hoztuk létre és tároljuk. |
| Munkahelyi vagy iskolai fiók | A szervezet létrehozza a munkahelyi vagy alain@contoso.comiskolai fiókját (például ), hogy hozzáférést biztosítson a belső és potenciálisan korlátozott erőforrásokhoz, például a Microsoft Azure, a Windows Intune és az Office 365. |
| Ellenőrző kód | A hitelesítő alkalmazásban, az egyes hozzáadott fiók alatt megjelenő hatjegyű kód. Az ellenőrző kód 30 másodpercenként változik, megakadályozva, hogy valaki többször is használja a kódot. Ezt egyszeri jelkódnak (OTP) is nevezik. |

## <a name="how-two-factor-verification-works-with-the-app"></a>A kétfaktoros ellenőrzés működése az alkalmazással

A kéttényezős ellenőrzés a Következő módokon működik együtt a Microsoft Authenticator alkalmazással:

- **Értesítés.** Írja be felhasználónevét és jelszavát abba az eszközbe, amelybe a munkahelyi vagy iskolai fiókjához vagy a személyes **Microsoft-fiókjához**jelentkezik be, majd a Microsoft Authenticator alkalmazás értesítést küld a bejelentkezés jóváhagyása című értesítésről. Válassza **a Jóváhagyás** lehetőséget, ha felismeri a bejelentkezési kísérletet. Ellenkező esetben válassza **a Megtagadás**lehetőséget. Ha a Megtagadás lehetőséget **választja,** a kérelmet is megjelölheti csalárdként.

- **Ellenőrző kód.** Írja be felhasználónevét és jelszavát abba az eszközbe, amelybe a munkahelyi vagy iskolai fiókjához vagy személyes Microsoft-fiókjához jelentkezik be, majd másolja a kapcsolódó ellenőrző kódot a Microsoft Authenticator alkalmazás **Fiókok** képernyőjére. Az ellenőrző kódot egyszeri jelkód (OTP) hitelesítésnek is nevezik.

- **Jelszó nélküli bejelentkezés.** Írja be a felhasználónevét abba az eszközbe, amelybe bejelentkezik a munkahelyi vagy iskolai fiókjához vagy a személyes Microsoft-fiókjához, majd a mobileszközével ellenőrizze, hogy Ön az ujjlenyomata, az arca vagy a PIN-kódja segítségével. Ehhez a módszerhez nem kell megadnia a jelszavát.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Az eszköz biometrikus képességeinek használata

Ha PIN-kódot használ a hitelesítési folyamat befejezéséhez, beállíthatja a Microsoft Authenticator alkalmazást úgy, hogy ehelyett az eszköz ujjlenyomat- vagy arcfelismerő (biometrikus) képességeit használja. Ezt beállíthatja akkor, amikor először használja a hitelesítő alkalmazást a fiók ellenőrzésére, ha kiválasztja azt a lehetőséget, hogy az eszköz biometrikus képességeit használja azonosítóként a PIN-kód helyett.

## <a name="who-decides-if-you-use-this-feature"></a>Ki dönti el, hogy használja-e ezt a funkciót?

A fiók típusától függően a szervezet dönthet úgy, hogy kétfaktoros ellenőrzést kell használnia, vagy ön dönthet ön maga.

- **Munkahelyi vagy iskolai fiók.** Ha munkahelyi vagy iskolai fiókot használ (például a szervezeten múlik, alain@contoso.comhogy kétfaktoros ellenőrzést kell-e használnia az adott ellenőrzési módszerekkel együtt. A munkahelyi vagy iskolai fiók Microsoft Authenticator alkalmazáshoz való hozzáadásáról a Munkahelyi vagy iskolai fiókok hozzáadása című [témakörben](user-help-auth-app-add-work-school-account.md)talál további információt.

- **Személyes Microsoft-fiók.** Beállíthatja a kétfaktoros ellenőrzést a személyes Microsoft-fiókjaihoz alain@outlook.com(például). A személyes Microsoft-fiók hozzáadásáról a [Személyes fiókok hozzáadása](user-help-auth-app-add-personal-ms-account.md)című témakörben talál további információt.

- **Nem Microsoft-fiók.** Beállíthatja a nem Microsoft-fiókok (például) alain@gmail.comkétfaktoros ellenőrzését. Előfordulhat, hogy a nem Microsoft-fiókok nem használják a kétfaktoros ellenőrzést, de a funkciót a **Biztonság** vagy a **Bejelentkezési** beállítások között kell megtalálnia. A Microsoft Authenticator alkalmazás minden olyan fiókkal működik, amely támogatja a TOTP-szabványokat. A nem Microsoft-fiókok hozzáadásáról a [Nem Microsoft-fiókok hozzáadása](user-help-auth-app-add-non-ms-account.md)című témakörben talál további információt.

## <a name="in-this-section"></a>A szakasz tartalma

| Cikk | Leírás |
| ------ | ------------ |
| [Az alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) | Ez a témakör azt ismerteti, hogy hol és hogyan szerezheti be és telepítheti a Microsoft Authenticator alkalmazást Android és iOS rendszerű eszközökre. |
| [Munkahelyi vagy iskolai fiókok hozzáadása](user-help-auth-app-add-work-school-account.md) | Ez a témakör azt ismerteti, hogy miként veheti fel a különböző munkahelyi, iskolai és személyes fiókjait a Microsoft Authenticator alkalmazásba. |
| [Személyes fiókok hozzáadása](user-help-auth-app-add-personal-ms-account.md) | Ez a témakör azt ismerteti, hogy miként veheti fel személyes Microsoft-fiókjait a Microsoft Authenticator alkalmazásba. |
| [Nem Microsoft-fiókok hozzáadása](user-help-auth-app-add-non-ms-account.md) | A cikk azt ismerteti, hogy miként veheti fel a nem Microsoft-fiókokat a Microsoft Authenticator alkalmazásba. |
| [Fiókok manuális hozzáadása](user-help-auth-app-add-account-manual.md) | A cikk azt ismerteti, hogy miként adható hozzá manuálisan a fiókok a Microsoft Authenticator alkalmazáshoz, ha nem tudja benézni a megadott QR-kódot. |
| [Bejelentkezés az alkalmazás használatával](user-help-auth-app-sign-in.md) | Ez a témakör azt ismerteti, hogy miként jelentkezik be a különböző fiókokba a Microsoft Authenticator alkalmazással.|
| [A fiók hitelesítő adatainak biztonsági mentése és helyreállítása](user-help-auth-app-backup-recovery.md) | Információkat biztosít a fiók hitelesítő adatainak biztonsági mentéséhez és visszaállításához a Microsoft Authenticator alkalmazás használatával. |
| [A Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdések](user-help-auth-app-faq.md) | Az alkalmazással kapcsolatos gyakori kérdésekre ad választ. |
