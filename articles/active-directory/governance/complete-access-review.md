---
title: A csoportok &-alkalmazások hozzáférési felülvizsgálatának befejezése – Azure AD
description: Megtudhatja, hogyan végezheti el a csoporttagok vagy alkalmazások hozzáférési felülvizsgálatát Azure Active Directory hozzáférési felülvizsgálatokban.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 251ca1dc2b288d3448cdcda0f28335a92c54b431
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078860"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>A csoportok és alkalmazások hozzáférési felülvizsgálatának befejezése az Azure AD hozzáférési felülvizsgálatokban

Rendszergazdaként [létre kell hoznia a csoportok vagy alkalmazások hozzáférési](create-access-review.md) felülvizsgálatát, [a felülvizsgálók pedig elvégzik a hozzáférési felülvizsgálatot](perform-access-review.md). Ez a cikk bemutatja, hogyan tekintheti meg a hozzáférési felülvizsgálat eredményeit, és hogyan alkalmazhatja az eredményeket.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Előfeltételek

- Prémium szintű Azure AD P2
- Globális rendszergazda, felhasználói rendszergazda, biztonsági rendszergazda vagy biztonsági olvasó

További információkért lásd a [licencekre vonatkozó követelményeket](access-reviews-overview.md#license-requirements).

## <a name="view-an-access-review"></a>Hozzáférési felülvizsgálat megtekintése

Nyomon követheti a folyamat előrehaladását, mivel a véleményezők befejezik a felülvizsgálatokat.

1. Jelentkezzen be a Azure Portalba, és nyissa meg az [Identity irányításáért lapot](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. A bal oldali menüben kattintson a **hozzáférési felülvizsgálatok**elemre.

1. A listában kattintson a hozzáférési felülvizsgálatra.

    A hozzáférési felülvizsgálatok egy sorozatának megtekintéséhez navigáljon a hozzáférési felülvizsgálathoz, és megtekintheti az ütemezett felülvizsgálatok közelgő eseményeit.

    Az **Áttekintés** oldalon láthatja a folyamatot. A címtárban nem módosulnak hozzáférési jogosultságok, amíg a felülvizsgálat be nem fejeződik.

    ![Hozzáférési felülvizsgálatok állapota](./media/complete-access-review/overview-progress.png)

1. Ha le szeretné állítani egy hozzáférési felülvizsgálatot, mielőtt elérte az ütemezett befejezési dátumot, kattintson a **Leállítás** gombra.

    A felülvizsgálatok leállításakor a felülvizsgálók többé nem fognak tudni válaszokat adni. A leállítását követően a felülvizsgálat nem indítható újra.

1. Ha már nem érdekli a hozzáférési felülvizsgálat, akkor a **Törlés** gombra kattintva törölheti.

## <a name="apply-the-changes"></a>A módosítások alkalmazása

Ha az **eredmények az erőforráshoz való automatikus alkalmazása** engedélyezve lett, és a **befejezési beállításokban**megadott beállítások alapján, az automatikus alkalmazás a felülvizsgálat befejezési dátuma vagy a felülvizsgálat manuális leállítása után lesz végrehajtva.

Ha az **eredmények az erőforráshoz való automatikus alkalmazása** nincs engedélyezve a felülvizsgálathoz, kattintson az **alkalmaz** gombra a módosítások manuális alkalmazásához. Ha a felülvizsgálat során a felhasználó hozzáférése megtagadva, az **alkalmaz**gombra kattintva az Azure ad eltávolítja a tagságot vagy az alkalmazás-hozzárendelést.

![Hozzáférés-felülvizsgálati módosítások alkalmazása](./media/complete-access-review/apply-changes.png)

A felülvizsgálat állapota a **befejezéstől** kezdve a **közbenső állapotok**szerint változik, például az **alkalmazás** és végül az állapot érvényesítése. A megtagadott felhasználókat, ha vannak ilyenek, a csoporttagság vagy az alkalmazás-hozzárendelésből néhány perc múlva el kell távolítani.

A konfigurált automatikus alkalmazás-ellenőrzés vagy az **alkalmazás** kiválasztása nem befolyásolja a helyszíni címtárból vagy dinamikus csoportból származó csoportra vonatkozó hatást. Ha módosítani kíván egy olyan csoportot, amely a helyszínen található, töltse le az eredményeket, és alkalmazza ezeket a módosításokat a csoportnak a címtárban való megjelenítésére.

## <a name="retrieve-the-results"></a>Az eredmény lekérése

Egy egyszeri hozzáférési felülvizsgálat eredményeinek megtekintéséhez kattintson a **Results (eredmények** ) lapra. Ha csak egy felhasználó hozzáférését szeretné megtekinteni, a keresőmezőbe írja be annak a felhasználónak a megjelenítendő nevét vagy egyszerű felhasználónevét, amelynek a hozzáférését felülvizsgálták.

![Hozzáférési felülvizsgálat eredményeinek beolvasása](./media/complete-access-review/retrieve-results.png)

Az ismétlődő aktív hozzáférési felülvizsgálat előrehaladásának megtekintéséhez kattintson az **eredmények** lapra.

Ha meg szeretné tekinteni az ismétlődő hozzáférési felülvizsgálat befejezett példányának eredményeit, kattintson az **Előzmények**megtekintése elemre, majd válassza ki az adott példányt a befejezett hozzáférés-felülvizsgálati példányok listájáról a példány kezdő és befejező dátuma alapján. A példány eredményei az **eredmények** lapról szerezhetők be.

A hozzáférési felülvizsgálat összes eredményének lekéréséhez kattintson a **Letöltés** gombra. Az eredményül kapott CSV-fájlt az Excelben vagy más, UTF-8 kódolású CSV-fájlokat megnyitó programokban lehet megtekinteni.

## <a name="remove-users-from-an-access-review"></a>Felhasználók eltávolítása hozzáférési felülvizsgálatból

 Alapértelmezés szerint a törölt felhasználók 30 napig töröltek maradnak az Azure AD-ban, amely idő alatt szükség esetén egy rendszergazda visszaállíthatja őket.  A felhasználók 30 nap után véglegesen törlődnek.  Emellett a Azure Active Directory portál használatával a globális rendszergazda explicit módon [véglegesen törölheti a nemrég törölt felhasználókat](../fundamentals/active-directory-users-restore.md) az adott időszak elérésekor.  Egy felhasználó végleges törlése után a felhasználó későbbi adatai el lesznek távolítva az aktív hozzáférési felülvizsgálatokból.  A törölt felhasználókkal kapcsolatos naplózási információk az auditnaplóban maradnak.

## <a name="next-steps"></a>További lépések

- [Felhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](manage-user-access-with-access-reviews.md)
- [Vendégfelhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](manage-guest-access-with-access-reviews.md)
- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md)
- [Hozzáférési felülvizsgálat létrehozása Azure AD rendszergazdai szerepkörrel rendelkező felhasználókhoz](../privileged-identity-management/pim-how-to-start-security-review.md)
