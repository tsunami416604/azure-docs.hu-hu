---
title: A szervezet hozzáférésének kezelése az alkalmazásokhoz & csoportokhoz – Azure AD
description: Megtudhatja, hogy miként végezhet hozzáférési felülvizsgálatot a szervezet alkalmazásai és csoportjai biztonsági hozzáférésének kezeléséhez a Saját alkalmazások portálról.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: c71195b247af6d5046d88d3e6918a660eddf09b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062378"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Hozzáférés-ellenőrzés végrehajtása a Saját alkalmazások portálról

Munkahelyi vagy iskolai fiókját a webalapú **Saját alkalmazások** portálon használhatja az alkalmazások és csoportok hozzáférési felülvizsgálatának elvégzéséhez. A hozzáférési felülvizsgálatok segítségével kezelheti az elavult hozzáférési feltételeket, illetve módosíthatja a hozzáférési követelményeket, és biztosíthatja azok felülvizsgálatát és frissítését.

Ha nem fér hozzá a **Saját alkalmazások** portálhoz, kérjen engedélyt az ügyfélszolgálattól.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ez a tartalom a **Saját alkalmazások** felhasználóinak készült. Ha Ön rendszergazda, a felhőalapú alkalmazások beállításával és kezelésével kapcsolatos további információkért az [Alkalmazáskezelési dokumentációban](https://docs.microsoft.com/azure/active-directory/manage-apps)talál.

## <a name="manage-access-reviews"></a>Hozzáférés-felülvizsgálatok kezelése

Ha a rendszergazda engedélyt adott a saját hozzáférési felülvizsgálatok elvégzésére, a csoportok vagy alkalmazások hozzáférését a **Saját alkalmazások** portál lap **Access-ellenőrzések** csempéjéről kezelheti.

>[!Note]
>Ha nem látja az **Access-ellenőrzések csempét,** az vagy azt jelenti, hogy nincs engedélye a hozzáférési ellenőrzések végrehajtására, vagy hogy nincs függőben lévő vélemény, amely a jóváhagyásra várna. Ha úgy gondolja, hogy hozzá kellene férnie a csempéhez, kérjen segítséget az ügyfélszolgálattól.

## <a name="to-perform-your-access-reviews"></a>A hozzáférési felülvizsgálatok végrehajtása

1. Jelentkezzen be munkahelyi vagy iskolai fiókjába.

2. Nyissa meg a webböngészőt, és nyissa meg a https://myapps.microsoft.com, vagy használja a szervezet által megadott hivatkozást. Előfordulhat például, hogy a rendszer a szervezet testreszabott https://myapps.microsoft.com/contoso.comoldalára irányítja, például .

    Megjelenik **az Alkalmazások** lap, amely a szervezet tulajdonában lévő és az Ön számára használható összes felhőalapú alkalmazást jeleníti meg.

    ![Alkalmazások lap a Saját alkalmazások portálon](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Válassza az **Access-ellenőrzések** csempét a jóváhagyásra váró hozzáférési felülvizsgálatok listájának megtekintéséhez.

    ![A szervezet függőben lévő hozzáférési véleményeivel rendelkező lap elérése](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. A **hozzáférés-ellenőrzés** elindításához válassza az Ellenőrzés megkezdése lehetőséget.

5. Tekintse át a hozzáférést, és állapítsa meg, hogy továbbra is szükség van-e rá.

    ![Access-ellenőrzési lap, amely az ellenőrzés részleteit tartalmazza](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Ha Ön rendszergazda, és jogosult a szervezet csoportokhoz és alkalmazásokhoz való hozzáférésének áttekintésére, egy másik oldal jelenik meg. A csoportok és alkalmazások szervezethez való áttekintéséről a [Csoportokhoz vagy alkalmazásokhoz való hozzáférés áttekintése az Azure AD Access-vélemények ben](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)című témakörben talál további információt.

6. Válassza az **Igen** lehetőséget a hozzáférés megtartásához, vagy a **Nem** lehetőséget a hozzáférés eltávolításához.

    Ha az **Igen**lehetőséget választja, előfordulhat, hogy meg kell adnia egy indoklást az **Ok** mezőben.

    ![Access-ellenőrzési lap, amely az Ok mezőt jeleníti meg mintaszöveggel](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Válassza a **Küldés** lehetőséget.

    A hozzáférési felülvizsgálat befejeződött, és visszatér a **Saját alkalmazások** portálra.

    >[!Note]
    >A hozzáférés bármikor módosíthatja, amíg a hozzáférési felülvizsgálati időszak véget nem ér. Ha eltávolítja a hozzáférést egy alkalmazáshoz vagy csoporthoz, az nem törlődik azonnal. Az eltávolítás akkor történik, amikor a hozzáférési felülvizsgálati időszak véget ér, vagy amikor a rendszergazda bezárja az értékelést.

## <a name="next-steps"></a>További lépések

- [Alkalmazások elérése és használata a Saját alkalmazások portálon](my-apps-portal-end-user-access.md)
- [Profilinformációk módosítása](my-apps-portal-end-user-update-profile.md)
- [A csoportokkal kapcsolatos adatok megtekintése és frissítése](my-apps-portal-end-user-groups.md)
