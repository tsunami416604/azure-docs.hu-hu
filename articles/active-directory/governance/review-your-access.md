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
ms.date: 05/21/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22f12255bf14ca424a8a79107e7ca8e403552b62
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471747"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Hozzáférés felülvizsgálata maga a csoportokat vagy alkalmazásokat az Azure AD hozzáférési felülvizsgálatokkal

Az Azure Active Directory (Azure AD) egyszerűbbé teszi a vállalatok csoportokhoz vagy alkalmazásokhoz való hozzáférés kezelése az Azure AD és más Microsoft Online Services szolgáltatás az Azure AD hozzáférési felülvizsgálatok.

Ez a cikk a saját egy csoporthoz vagy alkalmazáshoz való hozzáférés felülvizsgálata ismerteti.

## <a name="prerequisites"></a>Előfeltételek

- Prémium szintű Azure AD P2

További információkért lásd: [mely felhasználók rendelkeznie kell licencekkel?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="open-the-access-review"></a>Nyissa meg a hozzáférési felülvizsgálatba

Hozzáférési felülvizsgálat végrehajtása első lépése, hogy keresse meg és nyissa meg a hozzáférési felülvizsgálatot.

1. Keresse meg a Microsoft, amely rákérdez, hogy tekintse át a hozzáférést egy e-mailt. Íme egy példa e-mailt a csoportba hozzáférés felülvizsgálata.

    ![Példa e-mailt a Microsoft egy csoporthoz a hozzáférés felülvizsgálata](./media/review-your-access/access-review-email.png)

1. Kattintson a **hozzáférés felülvizsgálata** nyissa meg a hozzáférési felülvizsgálat mutató hivatkozást.

Ha nem rendelkezik az e-mailt, Észreveheti, hogy a függőben lévő hozzáférési felülvizsgálatok az alábbi lépéseket.

1. Jelentkezzen be a MyApps portálról, [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![MyApps portálról, amelyekhez alkalmazások listázása](./media/review-your-access/myapps-access-panel.png)

1. Az oldal jobb felső sarkában kattintson a felhasználó szimbólumára. Ekkor megjelenik a neve és az alapértelmezett szervezete. Ha egynél több szervezet szerepel a listán, válassza ki azt a szervezetet, amely a hozzáférési felülvizsgálatot kérte.

1. Kattintson a lap jobb oldalán a **hozzáférési felülvizsgálatokkal** csempére kattintva megjelenítheti a függőben lévő hozzáférési felülvizsgálatok listáját.

    A csempe nem látható, ha nincsenek végrehajtandó hozzáférési felülvizsgálatok az adott szervezetre vonatkozóan, és ilyenkor semmilyen műveletre nincs szükség.

    ![Függőben lévő hozzáférési felülvizsgálatok listáját az alkalmazásokban és a csoportok](./media/review-your-access/access-reviews-list.png)

1. Kattintson a **felülvizsgálat megkezdése** hivatkozásra a hozzáférési felülvizsgálatot végrehajtására vonatkozó szándékát.

## <a name="perform-the-access-review"></a>A hozzáférési felülvizsgálat végrehajtása

Miután megnyitotta a hozzáférési felülvizsgálatot, láthatja a hozzáférést.

1. A hozzáférés felülvizsgálata, és eldöntheti, hogy továbbra is kell hozzáférést.

    Ha a kérés hozzáférés felülvizsgálata mások számára, az oldal fog kinézni. További információkért lásd: [csoportokhoz vagy alkalmazásokhoz való hozzáférés felülvizsgálata](perform-access-review.md).

    ![Nyissa meg hozzáférési felülvizsgálat kéri, hogy továbbra is hozzá kell férnie egy csoportot](./media/review-your-access/perform-access-review.png)

1. Kattintson a **Igen** tartsa a hozzáférést, vagy kattintson a **nem** a hozzáférést.

1. Ha rákattint **Igen**, adja meg az indoklást szüksége lehet a **OK** mezőbe.

    ![Hozzáférési felülvizsgálat kéri, hogy továbbra is hozzá kell férnie egy csoport befejeződött](./media/review-your-access/perform-access-review-submit.png)

1. Kattintson a **Submit** (Küldés) gombra.

    Választását elküldésekor, és Ön visszatér a MyApps portálról.

    Ha szeretné módosítani a választ, nyissa meg újra a hozzáférési felülvizsgálatok lapot, és frissítse a válaszát. A válasz mindaddig, amíg a hozzáférési felülvizsgálat befejeződött bármikor módosíthatja.

    > [!NOTE]
    > Ha már nincs szüksége hozzáférés, azonnal, nem törlődnek. Ha a felülvizsgálat befejeződött, vagy ha egy rendszergazda leállítja a felülvizsgálatot el lesznek távolítva.

## <a name="next-steps"></a>További lépések

- [Csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat befejezése](complete-access-review.md)
