---
title: A hozzáférési felülvizsgálatokban lévő csoportok vagy alkalmazások hozzáférésének áttekintése – Azure Active Directory | Microsoft Docs
description: Megtudhatja, hogyan tekintheti át a csoportokhoz vagy alkalmazásokhoz való saját hozzáférését Azure Active Directory hozzáférési felülvizsgálatokban.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b48e035476e5381104b442f87dcac03ac11778
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499674"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Az Azure AD hozzáférési felülvizsgálatokban lévő csoportok és alkalmazások hozzáférésének áttekintése

Azure Active Directory (Azure AD) leegyszerűsíti, hogy a vállalatok hogyan kezelhetik az Azure ad-ben és más Microsoft Online Servicesben lévő csoportokhoz vagy alkalmazásokhoz való hozzáférést az Azure AD hozzáférési felülvizsgálatok szolgáltatásával.

Ez a cikk bemutatja, hogyan tekintheti át a saját hozzáférését egy csoporthoz vagy alkalmazáshoz.

## <a name="prerequisites"></a>Előfeltételek

- Azure AD Premium P2

További információ: [mely felhasználóknak kell licenceket tartalmazniuk?](access-reviews-overview.md#which-users-must-have-licenses)

## <a name="open-the-access-review"></a>A hozzáférési felülvizsgálat megnyitása

A hozzáférési felülvizsgálat végrehajtásának első lépése a hozzáférési felülvizsgálat megkeresése és megnyitása.

1. Keresse meg a Microsoft e-mail-címét, amely arra kéri, hogy tekintse át a hozzáférést. Íme egy példa e-mailben, amely áttekinti a csoporthoz való hozzáférést.

    ![Példa a Microsoft e-mail-címére a csoporthoz való hozzáférés áttekintéséhez](./media/review-your-access/access-review-email.png)

1. A hozzáférési felülvizsgálat megnyitásához kattintson a **hozzáférés áttekintése** hivatkozásra.

Ha nem rendelkezik az e-mail-címmel, a következő lépésekkel megkeresheti a függőben lévő hozzáférési felülvizsgálatokat.

1. Jelentkezzen be a MyApps portálra [https://myapps.microsoft.com](https://myapps.microsoft.com)a címen.

    ![A MyApps-portál felsorolja azokat az alkalmazásokat, amelyekre jogosult](./media/review-your-access/myapps-access-panel.png)

1. Az oldal jobb felső sarkában kattintson a felhasználó szimbólumára. Ekkor megjelenik a neve és az alapértelmezett szervezete. Ha egynél több szervezet szerepel a listán, válassza ki azt a szervezetet, amely a hozzáférési felülvizsgálatot kérte.

1. Az oldal jobb oldalán kattintson a **hozzáférési felülvizsgálatok** csempére a függőben lévő hozzáférési felülvizsgálatok listájának megtekintéséhez.

    A csempe nem látható, ha nincsenek végrehajtandó hozzáférési felülvizsgálatok az adott szervezetre vonatkozóan, és ilyenkor semmilyen műveletre nincs szükség.

    ![Alkalmazások és csoportok függőben lévő hozzáférési felülvizsgálatok listája](./media/review-your-access/access-reviews-list.png)

1. Kattintson a **megkezdés felülvizsgálati** hivatkozásra a végrehajtani kívánt hozzáférési felülvizsgálathoz.

## <a name="perform-the-access-review"></a>A hozzáférési felülvizsgálat végrehajtása

Ha megnyitotta a hozzáférési felülvizsgálatot, a hozzáférését láthatja.

1. Tekintse át a hozzáférését, és döntse el, hogy van-e hozzáférése.

    Ha a kérelem a mások számára való hozzáférés felülvizsgálatát kéri, a lap eltérő lesz. További információ: a [csoportok vagy alkalmazások hozzáférésének áttekintése](perform-access-review.md).

    ![Nyissa meg a hozzáférési felülvizsgálatot, és kérje meg, hogy van-e hozzáférése egy csoporthoz](./media/review-your-access/perform-access-review.png)

1. Az **Igen** gombra kattintva megtarthatja a hozzáférését, vagy a **nem** gombra kattintva eltávolíthatja a hozzáférését.

1. Ha az **Igen**gombra kattint, lehet, hogy meg kell adnia egy indoklást az **OK** mezőben.

    ![A hozzáférési felülvizsgálat befejezésével megkérdezte, hogy továbbra is szüksége van-e egy csoport elérésére](./media/review-your-access/perform-access-review-submit.png)

1. Kattintson a **Submit** (Küldés) gombra.

    A rendszer elküldte a kijelölést, és visszatér a MyApps-portálra.

    Ha módosítani szeretné a választ, nyissa meg újra a hozzáférési felülvizsgálatok lapot, és frissítse a választ. Bármikor módosíthatja a választ, amíg a hozzáférési felülvizsgálat véget nem ér.

    > [!NOTE]
    > Ha azt jelezte, hogy már nincs szüksége a hozzáférésre, nem távolítja el azonnal. Ha a felülvizsgálat véget ért, vagy ha a rendszergazda leállítja a felülvizsgálatot, a rendszer eltávolítja.

## <a name="next-steps"></a>További lépések

- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md)
