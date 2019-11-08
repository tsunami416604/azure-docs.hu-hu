---
title: Hozzáférési felülvizsgálat végrehajtása a saját alkalmazások portálján – Azure AD
description: Megtudhatja, hogyan tekintheti meg és kezelheti a szervezet alkalmazásai és csoportjai biztonsági hozzáférését.
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
ms.openlocfilehash: bf803027aac7c62ea500d1af104ae3234fadf8b5
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73804050"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Hozzáférési felülvizsgálat végrehajtása a saját alkalmazások portálján

Használhatja munkahelyi vagy iskolai fiókját a webalapú **saját alkalmazások** portálján, hogy megtekintse és elindítsa a szervezete felhőalapú alkalmazásait, hogy frissítse a profilját és a fiókadatok adatait, és megtekintse a **csoportok** adatait, és elvégezze a következő műveleteket **: alkalmazások és csoportok hozzáférési felülvizsgálatai** . Ha nincs hozzáférése a **saját alkalmazások** portálhoz, akkor engedélyt kell adnia az ügyfélszolgálatnak.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Ha Ön rendszergazda, további információt talál arról, hogyan állíthatja be és kezelheti felhőalapú alkalmazásait az [alkalmazás-felügyeleti dokumentációban](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Hozzáférési felülvizsgálatok kezelése

Ha a rendszergazda engedélyezte a saját hozzáférési felülvizsgálatok elvégzését, a saját **alkalmazások** portál lapon a **hozzáférési felülvizsgálatok** csempén kezelheti a csoportokat vagy az alkalmazásokat.

>[!Note]
>Ha nem látja a **hozzáférési felülvizsgálatok** csempét, akkor az azt jelenti, hogy nincs engedélye a hozzáférési felülvizsgálatok elvégzésére, vagy ha nincs függőben lévő felülvizsgálat, amely a jóváhagyásra vár. Ha úgy gondolja, hogy hozzáféréssel kell rendelkeznie a csempéhez, segítségért forduljon az ügyfélszolgálathoz.

### <a name="to-perform-your-access-reviews"></a>A hozzáférési felülvizsgálatok végrehajtása

1. Jelentkezzen be a munkahelyi vagy iskolai fiókjába.

2. Nyissa meg a webböngészőt, és lépjen a https://myapps.microsoft.comra, vagy használja a szervezete által biztosított hivatkozást. Előfordulhat például, hogy a szervezete testreszabott lapjára irányítja át, például https://myapps.microsoft.com/contoso.com.

    Megjelenik az **alkalmazások** lap, amely megjeleníti a szervezete tulajdonában lévő összes felhőalapú alkalmazást, és elérhető a használatra.

    ![Alkalmazások lap a saját alkalmazások portálon](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Válassza a **hozzáférési felülvizsgálatok** csempét a jóváhagyásra váró hozzáférési felülvizsgálatok listájának megtekintéséhez.

    ![Hozzáférési felülvizsgálatok lap a szervezetre vonatkozó függőben lévő hozzáférési felülvizsgálatokkal](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. A hozzáférési felülvizsgálat elindításához válassza az **Áttekintés megkezdése** lehetőséget.

5. Tekintse át a hozzáférését, és döntse el, hogy továbbra is szükséges-e.

    ![Hozzáférési felülvizsgálati oldal, amely az Áttekintés részleteit jeleníti meg](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Ha Ön rendszergazda, és lehetővé teszi, hogy áttekintse a szervezet hozzáférését a csoportokhoz és alkalmazásokhoz, egy másik oldal jelenik meg. A csoportok és alkalmazások szervezethez való áttekintésével kapcsolatos további információkért lásd: [hozzáférés ellenőrzése csoportokhoz vagy alkalmazásokhoz az Azure ad hozzáférési felülvizsgálatokban](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Válassza az **Igen** lehetőséget a hozzáférésének megtartásához, vagy a **nem** gombra a hozzáférés eltávolításához.

    Ha az **Igen**lehetőséget választja, lehet, hogy meg kell adnia egy indoklást az **OK** mezőben.

    ![Hozzáférési felülvizsgálati oldal, amely az OK mezőt mutatja a minta szöveggel](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Válassza a **Küldés**lehetőséget.

    A hozzáférési felülvizsgálat elkészült, és visszatér a **saját alkalmazások** portálra.

    >[!Note]
    >A hozzáférését bármikor módosíthatja, amíg a hozzáférési felülvizsgálati időszak véget nem ér. Ha eltávolít egy alkalmazáshoz vagy csoporthoz való hozzáférését, azt nem távolítja el azonnal. Az eltávolítás akkor történik meg, amikor a hozzáférési felülvizsgálati időszak véget ér, vagy amikor a rendszergazda bezárja a felülvizsgálatot.

## <a name="next-steps"></a>További lépések

- [Alkalmazások elérése és használata a saját alkalmazások portálon](my-apps-portal-end-user-access.md).

- [Módosítsa a profil adatait](my-apps-portal-end-user-update-profile.md).

- [A csoportok kapcsolódó információinak megtekintése és frissítése](my-apps-portal-end-user-groups.md).
