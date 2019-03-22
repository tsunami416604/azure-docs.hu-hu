---
title: Hajtsa végre a saját alkalmazások portál – Azure Active Directory hozzáférési felülvizsgálatok |} A Microsoft Docs
description: Ismerje meg, hogyan tekintheti meg és kezelheti a szervezet alkalmazásokat és a csoportok biztonsági hozzáférési.
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
ms.openlocfilehash: 551bbe4e994e15688c59fd395ab762e14927dfda
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340199"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>A saját alkalmazások portál a hozzáférési felülvizsgálat végrehajtása
A munkahelyi vagy iskolai fiók használata a web-alapú **saját alkalmazások** portálon megtekintheti, és indítsa el a számos szervezet felhőalapú alkalmazásokat, a profil és fiók információk megjelenítéséhez frissíteni, a **csoportok** információkat, és végrehajtásához **hozzáférési felülvizsgálatokkal** az alkalmazások és a csoportokat. Ha nem rendelkezik hozzáféréssel a **saját alkalmazások** portál, forduljon a Segélyszolgálathoz engedélyt.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Ha Ön rendszergazda, és a felhőalapú alkalmazások kezelése a további tájékoztatást talál a [Alkalmazásfelügyelet dokumentációja](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>A hozzáférési felülvizsgálatok kezelése
Ha a rendszergazda engedélyt adott a saját hozzáférési felülvizsgálatok végrehajtásához, kezelheti a csoportokhoz vagy alkalmazásokhoz való hozzáférés a **hozzáférési felülvizsgálatokkal** csempét a **saját alkalmazások** portáloldalán.

>[!Note]
>Ha nem látja a **hozzáférési felülvizsgálatokkal** csempére, vagy azt jelenti, hogy nincs engedélye a hozzáférési felülvizsgálatok végrehajtásához, vagy az, hogy nincs függőben lévő értékelések várjuk a jóváhagyását. Ha úgy gondolja, a csempe hozzáféréséhez kell, forduljon a segélyszolgálathoz segítségért.

### <a name="to-perform-your-access-reviews"></a>A hozzáférési felülvizsgálatok végrehajtásához

1.  Jelentkezzen be munkahelyi vagy iskolai fiókjával.

2.  Nyissa meg a webböngészőjét, és nyissa meg https://myapps.microsoft.com, vagy használja a szervezet által biztosított hivatkozást. Például akkor lehet, hogy átirányítja egy testre szabott lap a szervezete számára például https://myapps.microsoft.com/contoso.com.

    A **alkalmazások** lap jelenik meg, a felhőalapú alkalmazások megjelenítése, a szervezet tulajdonában és elérhető a használatra.

    ![Saját alkalmazások portál alkalmazások lapján](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Válassza ki a **hozzáférési felülvizsgálatokkal** csempe megtekintéséhez egy hozzáférési felülvizsgálatok jóváhagyásra vár.

    ![Hozzáférési felülvizsgálatok lapot a függőben lévő hozzáférési felülvizsgálatok a szervezet számára](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Válassza ki **felülvizsgálat megkezdése** a hozzáférési felülvizsgálat indítása.

5. A hozzáférés felülvizsgálata, és vizsgálja, hogy továbbra is szükséges.

    ![Hozzáférési felülvizsgálat oldaláról, a felülvizsgálat részletei](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Ha rendszergazdája, és tekintse át a szervezetének hozzáférését a csoportok és alkalmazások számára engedélyezett, megjelenik egy másik oldalon. Csoportokat vagy alkalmazásokat a szervezet megtekintésével kapcsolatos további információkért lásd: [csoportokat vagy alkalmazásokat az Azure AD hozzáférési felülvizsgálatokkal történő hozzáférés felülvizsgálata](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Válassza ki **Igen** , hogy a hozzáférés vagy **nem** a hozzáférést.

    Ha **Igen**, adja meg az indoklást szüksége lehet a **OK** mezőbe.

    ![Hozzáférés tekintse át az oldalról, mintaszöveggel a OK mezőbe](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Válassza ki **elküldése**.

    A hozzáférési felülvizsgálat befejeződött, és visszatér a **saját alkalmazások** portálon.

    >[!Note]
    >A hozzáférés mindaddig, amíg a hozzáférés tekintse át az időszak vége bármikor módosíthatja. Ha eltávolítja a hozzáférés egy alkalmazás vagy csoport, akkor nem törlődnek azonnal. Az eltávolítás akkor fordul elő, ha a hozzáférési tekintse át az időszak vége, vagy egy rendszergazda bezárása után a felülvizsgálati. 

## <a name="next-steps"></a>További lépések

- [Elérheti és használni az alkalmazásokat a saját alkalmazások portál](my-apps-portal-end-user-access.md).

- [Módosítsa a profiladatok](my-apps-portal-end-user-update-profile.md).

- [Megtekintése és módosítása a csoportok kapcsolatos információkat](my-apps-portal-end-user-groups.md).