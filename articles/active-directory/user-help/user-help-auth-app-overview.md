---
title: A Microsoft Authenticator alkalmazás áttekintése – Azure Active Directory | Microsoft Docs
description: Tudnivalók a Microsoft Authenticator alkalmazást, Mi ez, beleértve a működését, és milyen információkat ebben a szakaszban a tartalom szerepel.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/24/2019
ms.author: lizross
ms.openlocfilehash: 2379f1ff4fb4385015cc6077cb923cab998d1d11
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175213"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>Mi a Microsoft Authenticator alkalmazást?
A Microsoft Authenticator alkalmazás segítségével jelentkezzen be a fiókok kétfaktoros ellenőrzési használatakor. A kétfaktoros hitelesítési segítségével a fiókok biztonságosabb, hozzáférhet a különösen bizalmas adatok megtekintésekor. Mivel a jelszavak elfelejtett is, amelyeket elloptak vagy feltört, kéttényezős hitelesítés végrehajtása egy további biztonsági lépés, amely megnehezíti mások feltörhessék fiókja védelme érdekében.

Többféle módon, beleértve a Microsoft Authenticator alkalmazást is használhatja:

- Kérdés egy második ellenőrzési módszert biztosítása után jelentkezik be a felhasználónevét és jelszavát.

- Jelentkezzen be lehetővé anélkül, hogy a jelszó, a felhasználónév és a mobil eszköz használata az ujjlenyomatot, arc vagy PIN-kód.

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Amennyiben Ön rendszergazda, az Azure Active Directory- (Azure AD-) környezet beállításával és kezelésével kapcsolatosan további információt az [Azure Active Directory dokumentációjában](https://docs.microsoft.com/azure/active-directory) talál.<br><br>Ha bejelentkezik a fiókjába problémákat tapasztal, tekintse meg [mikor nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429) segítséget.  További információ arról, mi a teendő, ha kap a ["a megadott Microsoft-fiók nem létezik"](https://support.microsoft.com/help/13811) jelenik meg, jelentkezzen be Microsoft-fiókjával.

## <a name="terminology"></a>Terminológia
|Időtartam|Leírás|
|----|-----------|
|A kétfaktoros ellenőrzése |A-ellenőrzési folyamatot, amely előírja, hogy kifejezetten csak kétféle hitelesítési adatai, például egy jelszó és a egy PIN-kódot. A Microsoft Authenticator alkalmazás és is támogatja a standard szintű kétfaktoros hitelesítési bejelentkezés beállításának.|
|Multi-factor authentication (MFA)|Minden kétfaktoros Ez a multi-factor authentication használatát igénylő *legalább* kétféle hitelesítési adatai, a szervezet követelményei alapján.|
|Microsoft-fiók (más néven, MSA)|Hozza létre a saját személyes fiókokat, a végfelhasználóra irányuló Microsoft-termékek eléréséhez és a felhőalapú szolgáltatások, például az Outlook, OneDrive, Xbox LIVE- vagy Office 365. A Microsoft-fiók létrehozása és a Microsoft fogyasztói identitás-fiókrendszer a Microsoft által futtatott tárolja.|
|Munkahelyi vagy iskolai fiók|A szervezet által létrehozott munkahelyi vagy iskolai fiókját (például alain@contoso.com), hogy a belső hozzáférési és potenciálisan korlátozott erőforrások, például a Microsoft Azure, a Windows Intune és az Office 365-höz.|

## <a name="how-two-factor-verification-works-with-the-app"></a>Az alkalmazással kétfaktoros ellenőrzési működése
Két tényező ellenőrzési együttműködik a Microsoft Authenticator alkalmazást a következő módon:

- **Értesítés.** A felhasználónevét és jelszavát írja be az eszközt éppen jelentkezik be a munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiókjával, és rákérdez arra, hogy értesítést küld a Microsoft Authenticator alkalmazás **hagyhatja jóvá a bejelentkezést**. Válasszon **jóváhagyás** Ha ismeri fel a bejelentkezési kísérlet. Ellenkező esetben válasszon **Megtagadás**. Ha úgy dönt, **Megtagadás**, megjelölheti a csalárd jellegű kérésre.

- **Ellenőrző kódot.** A felhasználónevét és jelszavát írja be az eszközt éppen jelentkezik be a munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiókjával, és másolja a társított ellenőrző kód a **fiókok** a Microsoft képernyő Hitelesítő alkalmazás.

- **Bejelentkezés beállításának.** A felhasználónevét írja be az eszköz számára a munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiókjával jelentkezik be van, és a mobileszköz használatával győződjön meg arról, hogy az ujjlenyomat-alapú, a face vagy a PIN-kód használatával. Ezzel a módszerrel nem kell a jelszavát adja meg.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Hogy az eszköze biometrikus képességeit használja-e
Ha a hitelesítés befejezéséhez PIN-kódot használ, állíthat be a Microsoft Authenticator alkalmazást, használja inkább az eszköz ujjlenyomattal vagy arcfelismerési (biometrikus) képességeket. Beállíthat Ez az első alkalommal használja az authenticator alkalmazás ellenőrizni a fiókját, az eszköz biometrikus képességeinek használatához PIN-kód helyett azonosítására beállítás kiválasztásával.

## <a name="who-decides-if-you-use-this-feature"></a>Akik úgy dönt, hogy ha a szolgáltatás használata?
A fiók típusától függően a szervezet dönthet, hogy kétfaktoros ellenőrzési kell használnia, vagy előfordulhat, hogy a eldöntheti, hogy saját maga számára.

- **Munkahelyi vagy iskolai fiókjával.** Ha egy munkahelyi vagy iskolai fiókot használ (például alain@contoso.com), akkor működik a szervezet számára e kétfaktoros ellenőrzési azokkal a konkrét hitelesítési módszerekkel együtt kell használni. A többtényezős ellenőrzési kapcsolatos további információkért lásd: [Mi az Azure multi-factor Authentication jelent számomra](multi-factor-authentication-end-user.md). További információ a biztonsági adatok beállítása a Microsoft Authentication alkalmazás használatához: [egy hitelesítő alkalmazást (előzetes verzió) használata a biztonsági adatok beállítása](security-info-setup-auth-app.md).

- **Személyes Microsoft-fiókjával.** Dönthet úgy, hogy állítsa be a kétfaktoros ellenőrzési a személyes Microsoft-fiókok (például alain@outlook.com).

- **Nem a Microsofttól származó személyes fiókkal.** Dönthet úgy, hogy állítsa be a kétfaktoros ellenőrzési a személyes-Microsoft fiókok (például alain@gmail.com). A nem Microsoft-fiókok nem használhatja a kifejezés, kétfaktoros ellenőrzési, de meg a szolgáltatás belül található képesnek kell lennie a **biztonsági** vagy a **bejelentkezési** beállításait.

## <a name="in-this-section"></a>Ebben a szakaszban

|Cikk |Leírás |
|------|------------|
|[Az alkalmazás letöltése és telepítése](microsoft-authenticator-app-how-to.md)|Tudhatja meg, hol és hogyan lekérése és telepítése a Microsoft Authenticator alkalmazást az Android, iOS és Windows Phone rendszerű eszközökre vonatkozó.|
|[A munkahelyi vagy iskolai fiókok hozzáadása](microsoft-authenticator-app-add-work-account.md)|Ismerteti, hogyan lehet a különböző munkahelyi vagy iskolai és személyes fiókok hozzáadása a Microsoft Authenticator alkalmazást.|
|[A személyes fiókok hozzáadása](microsoft-authenticator-app-add-personal-account.md)|Ismerteti, hogyan lehet a személyes Microsoft- és nem Microsoft-fiókok hozzáadása a Microsoft Authenticator alkalmazást.|
|[A fiókok manuális hozzáadása](microsoft-authenticator-app-add-account-manual.md)|Ha nem sikerül, a megadott QR-kód bemutatja, hogyan a fiókok manuális hozzáadása a Microsoft Authenticator alkalmazást.|
|[Jelentkezzen be az alkalmazás használatával](microsoft-authenticator-app-phone-signin-faq.md)|Ismerteti, hogyan lehet bejelentkezni a különböző fiókokról, a Microsoft Authenticator alkalmazás használatával.|
|[A fiók hitelesítő adatainak biztonsági mentése és helyreállítása](microsoft-authenticator-app-backup-and-recovery.md)| Információkat biztosít a fiók hitelesítő adatainak biztonsági mentéséhez és visszaállításához a Microsoft Authenticator alkalmazás használatával.|
|[A Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdések](microsoft-authenticator-app-faq.md)|Az alkalmazással kapcsolatos gyakori kérdésekre ad választ.|
