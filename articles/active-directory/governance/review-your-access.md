---
title: Hozzáférés felülvizsgálata saját csoportok vagy a hozzáférési felülvizsgálatok – Azure Active Directory-alkalmazások |} A Microsoft Docs
description: Ismerje meg, hogyan tekintse át a saját csoportokat vagy alkalmazásokat az Azure Active Directory hozzáférési felülvizsgálatokkal a hozzáférést.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fe2013ff84dd0451fed7d108539606520cb9403
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576309"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Hozzáférés felülvizsgálata maga a csoportokat vagy alkalmazásokat az Azure AD hozzáférési felülvizsgálatokkal

Az Azure Active Directory (Azure AD) egyszerűbbé teszi a vállalatok csoportokhoz vagy alkalmazásokhoz való hozzáférés kezelése az Azure AD és más Microsoft Online Services szolgáltatás az Azure AD hozzáférési felülvizsgálatok.

Ez a cikk a saját egy csoporthoz vagy alkalmazáshoz való hozzáférés felülvizsgálata ismerteti.

## <a name="open-the-access-review"></a>Nyissa meg a hozzáférési felülvizsgálatba

Hozzáférési felülvizsgálat végrehajtása első lépése, hogy keresse meg és nyissa meg a hozzáférési felülvizsgálatot.

1. Keresse meg a Microsoft, amely rákérdez, hogy tekintse át a hozzáférést egy e-mailt. Íme egy példa e-mailt a csoportba hozzáférés felülvizsgálata.

    ![E-mailek hozzáférési felülvizsgálat](./media/review-your-access/access-review-email.png)

1. Kattintson a **hozzáférés felülvizsgálata** nyissa meg a hozzáférési felülvizsgálat mutató hivatkozást.

Ha nem rendelkezik az e-mailt, Észreveheti, hogy a függőben lévő hozzáférési felülvizsgálatok az alábbi lépéseket.

1. Jelentkezzen be a MyApps portálról, [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![MyApps portálról](./media/review-your-access/myapps-access-panel.png)

1. Az oldal jobb felső sarkában kattintson a felhasználói szimbólum, mely megjeleníti a szervezet nevét és az alapértelmezett. Ha egynél több szervezet szerepel a listán, válassza ki a szervezet, amely a hozzáférési felülvizsgálat kért.

1. Kattintson a lap jobb oldalán a **hozzáférési felülvizsgálatokkal** csempére kattintva megjelenítheti a függőben lévő hozzáférési felülvizsgálatok listáját.

    A csempe nem látható, ha nincsenek hozzáférési felülvizsgálatok végrehajtani az adott szervezet lesznek, és nem kell módosítania jelenleg.

    ![A hozzáférési felülvizsgálatok listája](./media/review-your-access/access-reviews-list.png)

1. Kattintson a **felülvizsgálat megkezdése** hivatkozásra a hozzáférési felülvizsgálatot végrehajtására vonatkozó szándékát.

## <a name="perform-the-access-review"></a>A hozzáférési felülvizsgálat végrehajtása

Miután megnyitotta a hozzáférési felülvizsgálatot, láthatja a hozzáférést.

1. A hozzáférés felülvizsgálata, és eldöntheti, hogy továbbra is kell hozzáférést.

    Ha a kérés hozzáférés felülvizsgálata mások számára, az oldal fog kinézni. További információkért lásd: [csoportokhoz vagy alkalmazásokhoz való hozzáférés felülvizsgálata](perform-access-review.md).

    ![Hozzáférési felülvizsgálat végrehajtása](./media/review-your-access/perform-access-review.png)

1. Kattintson a **Igen** tartsa a hozzáférést, vagy kattintson a **nem** a hozzáférést.

1. Ha rákattint **Igen**, adja meg az indoklást szüksége lehet a **OK** mezőbe.

    ![Hozzáférési felülvizsgálat végrehajtása](./media/review-your-access/perform-access-review-submit.png)

1. Kattintson a **Submit** (Küldés) gombra.

    Választását elküldésekor, és Ön visszatér a MyApps portálról.

    Ha szeretné módosítani a választ, nyissa meg újra a hozzáférési felülvizsgálatok lapot, és frissítse a válaszát. A válasz mindaddig, amíg a hozzáférési felülvizsgálat befejeződött bármikor módosíthatja.

    > [!NOTE]
    > Ha már nincs szüksége hozzáférés, azonnal, nem törlődnek. Ha a felülvizsgálat befejeződött, vagy ha egy rendszergazda leállítja a felülvizsgálatot el lesznek távolítva.

## <a name="next-steps"></a>További lépések

- [Csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat befejezése](complete-access-review.md)
