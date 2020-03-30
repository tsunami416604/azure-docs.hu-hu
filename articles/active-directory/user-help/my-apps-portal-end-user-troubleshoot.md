---
title: Segítség a My Apps portálhoz – Azure Active Directory| Microsoft dokumentumok
description: Segítség a Bejelentkezéshez és a gyakori feladatok végrehajtásához a Saját alkalmazások portálon.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: f72dd5595d67ae989cec5681d22def9a2f929adf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253155"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>A Saját alkalmazások portállal kapcsolatos problémák elhárítása

Ha problémákat tapasztal a My Apps portálra való bejelentkezéssel vagy a **Saját alkalmazások** portál használatával kapcsolatban, próbálkozzon ezekkel a hibaelhárítási tippekkel, mielőtt segítségért forduljon az ügyfélszolgálathoz vagy a rendszergazdához.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Nem sikerül telepíteni a Saját alkalmazások biztonságos bejelentkezési bővítményét

Ha problémái vannak a My Apps Secure bejelentkezési bővítmény telepítésével:

- Győződjön meg arról, hogy támogatott böngészőt használ, többek között a következőket:

    - **Microsoft Edge.** Windows 10 Anniversary Edition vagy újabb rendszeren fut.

    - **Google Chrome.** Windows 7 vagy újabb, valamint Mac OS X vagy újabb rendszeren.

    - **Mozilla Firefox 26.0 vagy újabb.** Windows XP SP2 vagy újabb, valamint Mac OS X 10.6-os vagy újabb rendszeren.

    - **Internet Explorer 11.** Windows 7 vagy újabb rendszeren való futtatás (korlátozott támogatás).

- Ellenőrizze, hogy a böngészőbővítmény beállításai be vannak-e kapcsolva.

- Próbálja meg újraindítani a böngészőt, és jelentkezzen be újra a **Saját alkalmazások** portálra.

- Törölje a böngésző cookie-jait, majd indítsa újra, majd jelentkezzen be újra a **Saját alkalmazások** portálra.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Nem tudok bejelentkezni a **Saját alkalmazások** portálra

Ha nem sikerül bejelentkeznie a **Saját alkalmazások** portálra, próbálkozzon a következőkkel:

- Győződjön meg arról, hogy a megfelelő URL-t használja. Meg kell, https://myapps.microsoft.com vagy egy testreszabott lapot https://myapps.microsoft.com/contoso.coma szervezet, például .

- Ellenőrizze, hogy a jelszó helyes-e, és még nem járt-e le. További információt a [Munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md)című témakörben talál.

- Ellenőrizze, hogy az ellenőrzési adatok aktuálisak és pontosak-e. További információ: [Mit jelent számomra az Azure többtényezős hitelesítés?](multi-factor-authentication-end-user.md) vagy [a biztonsági adatok módszereinek és információinak módosítása](security-info-add-update-methods-overview.md)című témakörben talál.

- Adja hozzá a **Saját alkalmazás** portál URL-címét az Internet tulajdonságok > biztonság > a **megbízható helyek** beállításához.

- Törölje a böngésző gyorsítótárát, és próbáljon meg újra bejelentkezni.

## <a name="my-password-isnt-working"></a>Nem működik a jelszavam

Ha elfelejtette a jelszavát, soha nem kapott egyet a szervezettől, zárolva van a fiókjából, vagy módosítani szeretné a jelszavát, olvassa el a [Súgó, Elfelejtettem az Azure AD-jelszavamat.](active-directory-passwords-update-your-own-password.md)

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Azt akarom, hogy képes legyen visszaállítani a saját jelszavát

Ahhoz, hogy saját jelszavát alaphelyzetbe tudja állítani, a rendszergazdának először be kell kapcsolnia a funkciót a szervezetnél, majd frissítenie kell és ellenőriznie kell a szükséges ellenőrzési módszereket. Az ellenőrzési módszerek frissítéséről az [Önkiszolgáló jelszó-visszaállítás regisztrálása](active-directory-passwords-reset-register.md)című témakörben talál további információt.

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Egy alkalmazás indításakor hozzáférés-megtagadásról szóló üzenet jelenik meg

Ha egy alkalmazás nak a **Saját alkalmazásportálról** történő indítása után **hozzáférési megtagadott** üzenetet kap, próbálkozzon a következőkkel:

- Ellenőrizze, hogy telepítette-e a [My Apps Secure bejelentkezési bővítményt,](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) és [hogy támogatott böngészőt](my-apps-portal-end-user-access.md#supported-browsers)használ-e.

- Győződjön meg arról, hogy a megfelelő URL-címet használja az alkalmazáshoz, és hogy az URL szerepel-e **az Internetes tulajdonságok > a megbízható webhelyek** > listáján.

- Ellenőrizze, hogy a jelszó helyes-e, és még nem járt-e le. További információt a [Munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md)című témakörben talál.

- Ellenőrizze, hogy az ellenőrzési adatok aktuálisak és pontosak-e. További információ: [Mit jelent számomra az Azure többtényezős hitelesítés?](multi-factor-authentication-end-user.md) vagy [a biztonsági adatok módszereinek és információinak módosítása](security-info-add-update-methods-overview.md)című témakörben talál.

- Törölje a böngésző gyorsítótárát, és próbáljon meg újra bejelentkezni.

Ha ezek után még mindig nem tudja elérni az alkalmazást, segítségért forduljon a szervezet ügyfélszolgálatához.

## <a name="next-steps"></a>További lépések

Miután bejelentkezett a **Saját alkalmazások** portálra, frissítheti a profil- és fiókadatait, a csoportadatait, valamint a hozzáférési ellenőrzési adatokat (ha rendelkezik engedéllyel).

- [Alkalmazások elérése és használata a Saját alkalmazások portálon.](my-apps-portal-end-user-access.md)

- [Módosítsa a profiladatait.](my-apps-portal-end-user-update-profile.md)

- [A csoportokkal kapcsolatos információk megtekintése és frissítése.](my-apps-portal-end-user-groups.md)

- [Végezze el saját hozzáférési véleményeit.](my-apps-portal-end-user-access-reviews.md)
