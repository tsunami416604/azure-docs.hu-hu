---
title: Hozzáférés felülvizsgálata maga a csoportokat vagy alkalmazásokat az Azure AD hozzáférési felülvizsgálatokkal |} A Microsoft Docs
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
ms.openlocfilehash: 76f90a5aa3f201fa5d1578ac63526be26377aedf
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731208"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Tekintse át a hozzáférést a saját maga csoportokat vagy alkalmazásokat az Azure AD hozzáférési felülvizsgálatok

Az Azure Active Directory (Azure AD) egyszerűbbé teszi a hogyan vállalatok való hozzáférés kezelése csoportokat vagy alkalmazásokat az Azure ad-ben, és más Microsoft Online Services-szolgáltatás neve az Azure AD hozzáférési felülvizsgálatok.

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
