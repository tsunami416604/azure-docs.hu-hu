---
title: Kérjen segítséget a saját alkalmazások portál – Azure Active Directory |} A Microsoft Docs
description: Segítség a bejelentkezéshez, és végrehajtson hétköznapi feladatokat a saját alkalmazások portál.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e87ae0a8adf28176d9a97cbf1b78943179884a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340109"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>A saját alkalmazások portál kapcsolatos problémák elhárítása
Ha problémákat tapasztal a bejelentkezésnél vagy használatával kapcsolatos problémák a **saját alkalmazások** portálon, próbálja ki ezeket a hibaelhárítási tippek, mielőtt, segítségért forduljon a segélyszolgálathoz vagy a rendszergazdához.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Gondjaim vannak a saját alkalmazások biztonságos bejelentkezési bővítmény telepítése
Ha a saját alkalmazások biztonságos bejelentkezési bővítménye telepítése problémák merültek fel:

- Győződjön meg arról, hogy egy támogatott böngészővel használja többek között:

    - **Microsoft Edge.** A Windows 10 Évfordulós kiadása vagy újabb fut.
    - **Google Chrome.** Windows 7-es vagy újabb, és a Mac OS X vagy későbbi verzión futó.
    - **Mozilla Firefox 26.0 vagy újabb.** A Windows XP SP2 vagy újabb, és a Mac OS X 10.6 vagy későbbi verzión futó.
    - **Internet Explorer 11.** Fut a Windows 7 vagy újabb (korlátozott támogatást).

- Ellenőrizze, hogy a böngészőbeállítások bővítmény be vannak kapcsolva.

- Próbálja meg újraindítani a böngészőben, és jelentkezzen be a a **saját alkalmazások** újból portálra.

- Törölje a böngésző cookie-kat, majd indítsa újra, és jelentkezzen be a **saját alkalmazások** újból portálra.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Nem tudok bejelentkezni a **saját alkalmazások** portál
Ha problémába ütközik, jelentkezzen be a **saját alkalmazások** portál, megpróbálhatja a következőt:

- Győződjön meg arról, hogy a megfelelő URL-CÍMÉT használja. Érdemes azt https://myapps.microsoft.com vagy testre szabott lapot a szervezete számára például https://myapps.microsoft.com/contoso.com.

- Győződjön meg arról, hogy a jelszó helyes-e, és még nem járt le. További információ: [a munkahelyi vagy iskolai jelszó visszaállítása](active-directory-passwords-update-your-own-password.md).

- Ellenőrizze, hogy a hitelesítési adatok aktuális és pontos. További információkért lásd: [Mi az Azure multi-factor Authentication jelent a számomra?](multi-factor-authentication-end-user.md) vagy [módosítása a security info módszerekkel és információkkal](security-info-add-update-methods-overview.md).

- Adja hozzá a **saját-alkalmazás** portál URL-címet a **internetes Tulajdonságok > biztonsági > Megbízható helyek** beállítás.

- Törölje a böngésző gyorsítótárát, és próbálja meg újra bejelentkezni.

## <a name="my-password-isnt-working"></a>A jelszó nem működik
Ha elfelejtette a jelszavát, soha nem a szervezet kapott, használja ki a fiók zárolva vannak, vagy szeretné módosítani a jelszavát, lásd: [segítség, elfelejtettem Azure AD-jelszavamat](active-directory-passwords-update-your-own-password.md).

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Szeretném tudni a saját jelszó visszaállítása
Az, hogy a saját jelszó alaphelyzetbe állítása, a rendszergazda kell kapcsolnia a a szolgáltatást a szervezete számára, és ezután kell frissíteni, és ellenőrizze a szükséges ellenőrzési módszerek sorát. Az ellenőrzési módszerek sorát frissítésével kapcsolatos további információkért lásd: [regisztráció önkiszolgáló jelszó-visszaállításra](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Az alkalmazás indításakor egy hozzáférés-megtagadási üzenetet kapok
Ha egy **hozzáférés megtagadva** üzenet egy alkalmazás elindítása után a **saját-alkalmazás** portál, megpróbálhatja a következőt:

- Győződjön meg arról, hogy telepítette a [saját alkalmazások biztonságos bejelentkezési bővítménye](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) , és hogy használ egy [támogatott böngésző](my-apps-portal-end-user-access.md#supported-browsers).

- Győződjön meg arról, hogy a megfelelő URL-címet használja az alkalmazást, és, hogy az URL-cím található a **internetes Tulajdonságok > biztonsági > Megbízható helyek** listája.

- Győződjön meg arról, hogy a jelszó helyes-e, és még nem járt le. További információ: [a munkahelyi vagy iskolai jelszó visszaállítása](active-directory-passwords-update-your-own-password.md).

- Ellenőrizze, hogy a hitelesítési adatok aktuális és pontos. További információkért lásd: [Mi az Azure multi-factor Authentication jelent a számomra?](multi-factor-authentication-end-user.md) vagy [módosítása a security info módszerekkel és információkkal](security-info-add-update-methods-overview.md).

- Törölje a böngésző gyorsítótárát, és próbálja meg újra bejelentkezni.

Ezeknek a dolgoknak próbálkozás után még mindig nem férhet hozzá az alkalmazás, ha kell, segítségért forduljon a támogatási szolgálat a szervezet.

## <a name="next-steps"></a>További lépések
A bejelentkezés után a **saját alkalmazások** portálon is frissíthető a profil és fiók adatait, a csoport adatait és a hozzáférési tekintse át adatokat (ha van engedélye).

- [Elérheti és használni az alkalmazásokat a saját alkalmazások portál](my-apps-portal-end-user-access.md).

- [Módosítsa a profiladatok](my-apps-portal-end-user-update-profile.md).

- [Megtekintése és módosítása a csoportok kapcsolatos információkat](my-apps-portal-end-user-groups.md).

- [Hajtsa végre a saját hozzáférési felülvizsgálatok](my-apps-portal-end-user-access-reviews.md).