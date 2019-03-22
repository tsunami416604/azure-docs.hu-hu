---
title: A profil és fiók adatait, a saját alkalmazások portál – Azure Active Directory frissítése |} A Microsoft Docs
description: Ismerje meg, hogyan frissítse a profilját, és a munkahelyi vagy iskolai fiók adatait, beleértve a jelszómódosítás, ne tudják bekapcsolni a jelszó-visszaállítás, a biztonsági ellenőrzési módszerek frissítése, a use utasítás a szervezet használati megtekintése és kijelentkezés mindenhol, amelybe bejelentkezett munkahelyi vagy iskolai fiókjával.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa6de024758d16b99569261e10d46f9328241427
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340536"
---
# <a name="update-your-profile-and-account-info-from-the-my-apps-portal"></a>A profil és fiók adatait, a saját alkalmazások portál frissítése
A munkahelyi vagy iskolai fiók használata a web-alapú **saját alkalmazások** portálon megtekintheti, és indítsa el a számos szervezet felhőalapú alkalmazásokat, a profil és fiók információk megjelenítéséhez frissíteni, a **csoportok** információkat, és végrehajtásához **hozzáférési felülvizsgálatokkal** az alkalmazások és a csoportokat. Ha nem rendelkezik hozzáféréssel a **saját alkalmazások** portál, forduljon a Segélyszolgálathoz engedélyt.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Ha Ön rendszergazda, és a felhőalapú alkalmazások kezelése a további tájékoztatást talál a [Alkalmazásfelügyelet dokumentációja](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="view-your-organization-related-profile-information"></a>A szervezet kapcsolatos Profiladatok megtekintése
Attól függően, mi a szervezet úgy döntött, hogy az tartalmazza a **profil** lapján a **saját alkalmazások** portál, láthatja a munkával kapcsolatos részletes, az eszközök és a tevékenység, és bármelyik együtt egyéb szervezetek, amelyekhez tartoznak.

### <a name="to-view-your-profile-information"></a>A profiladatok megtekintése
1.  Jelentkezzen be munkahelyi vagy iskolai fiókjával [nyissa meg a **saját alkalmazások** portál](my-apps-portal-end-user-access.md).

2. Az a **alkalmazások** lapon, válassza ki a profilhoz tartozó fotó a jobb felső sarokban, ahol ugyanakkor a nevét és a szervezet, majd válassza ki **profil**.

    A **profil** megjelenítő lap jelenik meg, a profiladatait.

    ![Profil oldal, amely az összes rendelkezésre álló információt](media/my-apps-portal/my-apps-portal-profile-page.png)

3. Az a **profil** lapon is:

    - **Tekintse át a szervezet kapcsolatos részleteket.** A fénykép, név, cím, társított e-mail-címeket és office információk megtekintése. Ez az információ a szervezet által felügyelt és nem tudja majd módosítani. Ha hibaüzenet jelenik meg, forduljon a Segélyszolgálathoz.

    - **Tekintse át az eszközök és tevékenységek**. Ellenőrizze, hogy minden eszköz ismer, vagy a szervezet megfelelően csatlakozik. Ha egy eszköz nem ismeri fel, válassza ki a **letiltása eszköz** , távolítsa el a társítást a fiókjához. Ha letilt egy eszközt, a rendszer eltávolítja ezt oldal.

    - **Tekintse át a szervezetek számára.** Győződjön meg arról, hogy továbbra is dolgozik a megadott szervezetek mindegyike. Ha már nem dolgozik, az adott szervezetnél, javasoljuk, hogy bejelölte **jelentkezzen be a szervezet**. Ha elhagyja a szervezetet, ezen a lapon a rendszer eltávolítja.

## <a name="manage-your-work-or-school-account-information"></a>Kezelheti a munkahelyi vagy iskolai fiók adatait
Frissítse és kezelni a munkahelyi vagy az iskolai felhasználóifiók-adatokat a **profil** lapján a **saját alkalmazások** portálon. Ez az oldal nyújt segítséget:

- Módosítsa a munkahelyi vagy iskolai fiók jelszava.

- Kapcsolja be a jelszó-visszaállítás (Ha a rendszergazda bekapcsolta ezt a szolgáltatást).

- Adja meg a további biztonsági ellenőrzési információkat.

- Tekintse át a szervezet használati feltételeit.

- Kijelentkezés mindenhol.

## <a name="change-your-password"></a>Jelszó módosítása
Ha a munkahelyi vagy iskolai fiók jelszavát módosítani szeretné, kiválaszthatja **jelszó módosítása** származó a **fiók kezelése** területén a **profil** lap.

### <a name="to-change-your-password"></a>A jelszó módosítása
1. Az a **fiók kezelése** területén a **profil** lapon jelölje be **jelszó módosítása**.

2. Az a **jelszó módosítása** lapon ellenőrizze, hogy helyesek-e a felhasználói Azonosítóját, és írja be a régi jelszót és az új jelszót a megfelelő mezőbe.

    ![Módosítsa a jelszófrissítési lap](media/my-apps-portal/my-apps-portal-change-password-page.png)

3. Válassza ki **elküldése**.

    A jelszava megváltozott. Jelentkezzen be minden alkalmazás, amelybe korábban bejelentkezett a munkahelyi vagy iskolai fiókkal kell fogja.

## <a name="set-up-and-use-password-reset"></a>Állítsa be, és használja a jelszó-visszaállítás
Ha elfelejtette a jelszavát, soha nem lett egy, a céges ügyfélszolgálattól, vagy használja ki a fiók zárolva van, visszaállíthatja a saját jelszavát.

>[!Important]
>A rendszergazdának be kell kapcsolni, ez a funkció a, és regisztrálnia kell az való részvételre. Regisztrálása és a jelszó alaphelyzetbe állítása kapcsolatos részletekért lásd: [regisztrálása önkiszolgáló jelszó-visszaállítás](active-directory-passwords-reset-register.md) és [a munkahelyi vagy iskolai jelszó visszaállítása](user-help-reset-password.md).

## <a name="change-your-security-verification-information"></a>A biztonsági ellenőrzés adatainak módosítása
Ha a szervezet megköveteli, hogy használjon kétfaktoros ellenőrzési, meg is hozzáadása, frissítése és törlése a társított biztonsági adatait a **további biztonsági ellenőrzés** lapot.

Kéttényezős hitelesítési előírja, hogy kétféle hitelesítési adatai, például egy jelszó és a egy PIN-kód, mielőtt lekérné a fiók vagy a szervezet adatait. További információ a kétfaktoros ellenőrzési, lásd: [a kétlépéses ellenőrzéshez a fiók beállítása](multi-factor-authentication-end-user-first-time.md).

### <a name="to-change-your-security-information"></a>Saját biztonsági adatok módosítása
1. Az a **fiók kezelése** területén a **profil** lapon jelölje be **további biztonsági ellenőrzés**.

    ![További biztonsági ellenőrzési lapot](media/my-apps-portal/my-apps-portal-additional-verification-page.png)

2. Az a **további biztonsági ellenőrzés** lapon hozzáadhatja, módosíthatja, vagy törli a következő információkat:

    - **Alapértelmezett ellenőrzési módszerrel.** Válassza ki az alapértelmezett másodlagos módszer a kétfaktoros ellenőrzéshez használni. Ez a módszer automatikusan bármikor kétfaktoros ellenőrzési szükség, miután megadta a felhasználónevét és jelszavát használja.

    - **Hozzáadása, frissítése vagy eltávolítása, ellenőrzési módszerek sorát.** Hozzáadhat új, frissítse a meglévő, vagy törli a régi adatokat, amelyek már nem érvényes.

    - **Állítsa be a Microsoft Authenticator alkalmazást.** Ha szeretné, állítsa be a Microsoft Authenticator alkalmazást a ellenőrzési módszerként. A Microsoft Authenticator alkalmazással kapcsolatos további információkért lásd: [Mi az a Microsoft Authenticator alkalmazást?](user-help-auth-app-overview.md)

3. Válassza ki **mentése** a módosítások mentéséhez.

## <a name="review-your-organizations-terms-of-use-statement"></a>A use utasítás a munkahelyi feltételek áttekintése
Ha szeretné, tekintse át a szervezet használati utasítás használatát, ha rendelkezésre áll.

1. Az a **fiók kezelése** területén a **profil** lapon jelölje be **használati feltételek áttekintése**.

2. Tekintse át a szervezet használati feltételeit, és válassza ki **elfogadás** ellenőrizze, hogy olvassa el, és megértette a használati feltételeket a szervezet számára.

    ![A saját alkalmazások portál használata lapot feltételeit](media/my-apps-portal/my-apps-portal-tou-page.png)

    Ha a szervezete nem rendelkezik a használati feltételeket, válassza **kész** térjen vissza a **profil** lapot.

## <a name="sign-out-of-everywhere"></a>Kijelentkezés mindenhol
Kijelentkezés mindenhol van jelenleg bejelentkezve, a munkahelyi vagy iskolai fiókjával választhat. Ez magában foglalja az összes alkalmazásait és eszközeit.

### <a name="to-sign-out-of-everywhere"></a>A Kijelentkezés mindenhol
1. Az a **fiók kezelése** területén a **profil** lapon jelölje be **kijelentkezés mindenhol**.

2. Az a **kijelentkezés mindenhol** megerősítése mező mellett válassza **Igen** annak ellenőrzéséhez, hogy szeretné-e jelentkezzen ki a munkamenetek és eszközök. Válassza ki **nem** Ha megváltoztatja döntését.

## <a name="next-steps"></a>További lépések
A frissítések a befejezése után a **profil** lapon is:

- [Elérheti és használni az alkalmazásokat a saját alkalmazások portál](my-apps-portal-end-user-access.md).

- [Megtekintése és módosítása a csoportok kapcsolatos információkat](my-apps-portal-end-user-groups.md).

- [Hajtsa végre a saját hozzáférési felülvizsgálatok](my-apps-portal-end-user-access-reviews.md).