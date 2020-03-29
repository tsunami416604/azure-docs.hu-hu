---
title: A csoportokhoz & alkalmazásokhoz való hozzáférés áttekintése a hozzáférési felülvizsgálatokban – Azure AD
description: Megtudhatja, hogyan tekintheti át a csoportokhoz vagy alkalmazásokhoz való saját hozzáférését az Azure Active Directory hozzáférési felülvizsgálatokban.
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
ms.openlocfilehash: 0d75d7ffe28fa126ee5e359cba19e66e3c0f36e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422400"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Hozzáférés áttekintése csoportokhoz vagy alkalmazásokhoz az Azure AD-hozzáférési felülvizsgálatokban

Az Azure Active Directory (Azure AD) leegyszerűsíti, hogy a vállalatok hogyan kezelhetik a csoportokhoz vagy alkalmazásokhoz való hozzáférést az Azure AD-ben és más Microsoft Online Services-ben az Azure AD-hozzáférés-felülvizsgálatok nevű funkcióval.

Ez a cikk ismerteti, hogyan tekintse át a saját hozzáférést egy csoporthoz vagy egy alkalmazáshoz.

## <a name="open-the-access-review"></a>A hozzáférési felülvizsgálat megnyitása

A hozzáférés-felülvizsgálat végrehajtásának első lépése a hozzáférési felülvizsgálat megkeresése és megnyitása.

1. Keressen egy e-mailt a Microsofttól, amely a hozzáférés áttekintését kéri. Íme egy példa e-mail, amely ből megtekintheti a csoporthoz való hozzáférést.

    ![Példa a Microsoft e-mail-címére a csoporthoz való hozzáférés áttekintéséhez](./media/review-your-access/access-review-email.png)

1. A hozzáférési ellenőrzés **áttekintése** hivatkozásra kattintva nyissa meg a hozzáférési felülvizsgálatot.

Ha nem rendelkezik az e-maillel, az alábbi lépések végrehajtásával megtalálhatja a függőben lévő hozzáférési ellenőrzéseket.

1. Jelentkezzen be a MyApps [https://myapps.microsoft.com](https://myapps.microsoft.com)portálra a .

    ![A MyApps portál felsorolja azokat az alkalmazásokat, amelyekhez engedéllyel rendelkezik](./media/review-your-access/myapps-access-panel.png)

1. Az oldal jobb felső sarkában kattintson a felhasználó szimbólumára. Ekkor megjelenik a neve és az alapértelmezett szervezete. Ha egynél több szervezet szerepel a listán, válassza ki azt a szervezetet, amely a hozzáférési felülvizsgálatot kérte.

1. A lap jobb oldalán kattintson az **Access-ellenőrzések** csempére a függőben lévő hozzáférési ellenőrzések listájának megtekintéséhez.

    A csempe nem látható, ha nincsenek végrehajtandó hozzáférési felülvizsgálatok az adott szervezetre vonatkozóan, és ilyenkor semmilyen műveletre nincs szükség.

    ![Függőben lévő hozzáférés-felülvizsgálatok listája az alkalmazásokhoz és csoportokhoz](./media/review-your-access/access-reviews-list.png)

1. Kattintson a végrehajtani kívánt hozzáférés-ellenőrzés **véleményezésének áttekintése** hivatkozásra.

## <a name="perform-the-access-review"></a>A hozzáférés-ellenőrzés végrehajtása

Miután megnyitotta a hozzáférési felülvizsgálatot, láthatja a hozzáférést.

1. Tekintse át a hozzáférést, és döntse el, hogy továbbra is szüksége van-e hozzáférésre.

    Ha a kérelem mások hozzáférésének áttekintésére van szükség, az oldal máshogy fog kinézni. További információt a [Csoportokhoz vagy alkalmazásokhoz való hozzáférés áttekintése](perform-access-review.md)című témakörben talál.

    ![Nyílt hozzáférésű ellenőrzés, amely megkérdezi, hogy továbbra is szüksége van-e egy csoporthoz](./media/review-your-access/perform-access-review.png)

1. Kattintson az **Igen** gombra a hozzáférés megtartásához, vagy kattintson a **Nem** gombra a hozzáférés eltávolításához.

1. Ha az **Igen**gombra kattint, előfordulhat, hogy meg kell adnia egy indoklást az **Ok** mezőben.

    ![Befejezett hozzáférési ellenőrzés, amely megkérdezi, hogy továbbra is szüksége van-e egy csoporthoz](./media/review-your-access/perform-access-review-submit.png)

1. Kattintson a **Küldés gombra.**

    A választás elküldésre kerül, és visszatért a MyApps portálra.

    Ha módosítani szeretné a választ, nyissa meg újra a hozzáférési felülvizsgálatok oldalát, és frissítse a választ. A válasz bármikor módosítható, amíg a hozzáférési felülvizsgálat véget nem ér.

    > [!NOTE]
    > Ha jelezte, hogy már nincs szüksége hozzáférésre, nem távolítja el azonnal. A rendszer eltávolítja, ha az ellenőrzés véget ért, vagy amikor a rendszergazda leállítja az értékelést.

## <a name="next-steps"></a>További lépések

- [Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának befejezése](complete-access-review.md)
