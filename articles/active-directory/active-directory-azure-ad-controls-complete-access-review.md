---
title: Tagok egy csoport vagy alkalmazás az Azure ad-vel való felhasználói hozzáférés hozzáférési felülvizsgálat befejezése |} A Microsoft Docs
description: Ismerje meg, hogyan végezze el a hozzáférési felülvizsgálat egy csoportot vagy egy alkalmazás az Azure Active Directory-hozzáféréssel rendelkező felhasználók tagjai számára.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 05/02/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: ad85c8d64d6cc160c1f3e75bf2288a4efc6d281c
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621674"
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>Tagok egy csoport vagy az Azure AD-alkalmazásokhoz való felhasználói hozzáférés hozzáférési felülvizsgálat befejezése

A rendszergazdák az Azure Active Directory (Azure AD) használatával [hozzáférési felülvizsgálatokat](active-directory-azure-ad-controls-create-access-review.md) hozhatnak létre a csoporttagok vagy alkalmazáshoz rendelt felhasználók számára. Az Azure AD automatikusan elküldi felülvizsgálók nekik hozzáférést kérő e-mailt. Ha a felhasználó nem tudták beszerezni egy e-mailt, küldhet nekik az utasításokat [a hozzáférés felülvizsgálata](active-directory-azure-ad-controls-perform-access-review.md). (Vegye figyelembe, hogy ki, a felülvizsgálók hozzá vannak rendelve, de nem fogadták el a meghívást vendégek nem kap egy e-mailt a hozzáférési felülvizsgálatok, mivel először el kell fogadniuk előtt tekintse át a meghívót.) A hozzáférési felülvizsgálati időszak után, vagy ha egy rendszergazda leállítja a hozzáférési felülvizsgálatot, kövesse a cikk megtekintéséhez és a alkalmazni az eredményeket.

## <a name="view-an-access-review-in-the-azure-portal"></a>Hozzáférési felülvizsgálat megtekintéséhez az Azure Portalon

1. Nyissa meg a [hozzáférési felülvizsgálatok lapot](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), jelölje be **programok**, és válassza ki a programot, amely tartalmazza a hozzáférési felülvizsgálat vezérlő.

2. Válassza ki **kezelés**, és jelölje ki a hozzáférési felülvizsgálat. Ha a programban sok vezérlő van, szűrhet adott típusú vagy állapotú vezérlőkre. A hozzáférési felülvizsgálati vezérlő neve vagy az azt létrehozó tulajdonos megjelenített neve alapján is kereshet. 

## <a name="stop-a-review-that-hasnt-finished"></a>Állítsa le a felülvizsgálatot, amely nem fejeződött be

Ha a felülvizsgálat még nem érte az ütemezett befejezési dátum, a rendszergazda választhat **leállítása** korai befejezi. Miután leállította a felülvizsgálatot, felhasználók már nem tekinthető meg. Felülvizsgálat nem indítható újra, miután leállt.

## <a name="apply-the-changes"></a>A módosítások alkalmazása 

Hozzáférési felülvizsgálat befejezése után akár, mert elérte a záró dátum, vagy egy rendszergazda manuálisan leállítását, és automatikus alkalmazása nem lett konfigurálva a felülvizsgálatra kiválaszthatja **alkalmaz** manuálisan a módosítások életbe léptetéséhez. A felülvizsgálat eredményének frissítése a csoport vagy alkalmazás valósít meg. Ha egy felhasználó hozzáférés megtagadva a felülvizsgálati, amikor a rendszergazda ezt a beállítást választja, az Azure AD eltávolítja a tagsági és alkalmazás-hozzárendelés. 

Hozzáférési felülvizsgálat befejezése, és automatikus alkalmazása után lett konfigurálva, akkor állapotát, a felülvizsgálat befejezve köztes állapota változik, és alkalmazott állapotra változik. Letiltott felhasználók megtekintéséhez számíthat, ha bármely, az erőforrást az eltávolítani kívánt csoport néhány perc múlva a tagsági és alkalmazás-hozzárendelés.

Egy konfigurált automatikus felülvizsgálat alkalmazása, vagy válassza **alkalmaz** nincs hatással a egy csoportot, amely egy helyszíni címtár származik, vagy a dinamikus csoportot. Ha szeretné módosítani egy csoportot, amely a helyszíni származik, töltse le az eredményeket, és ezeket a módosításokat alkalmazni a csoport ebben a könyvtárban reprezentációja.

## <a name="download-the-results-of-the-review"></a>A felülvizsgálat eredményeinek letöltése

A felülvizsgálat eredményeinek lekéréséhez válassza **jóváhagyások** majd **letöltése**. Az eredményül kapott CSV-fájl tekinthet meg, az Excelben vagy más programok telepítése és törlése, nyissa meg az UTF-8 kódolású CSV-fájlok.

## <a name="optional-delete-a-review"></a>Választható lehetőség: A felülvizsgálat törlése
Ha már nem szeretné használni a felülvizsgálati, törölheti azt. Válassza ki **törlése** eltávolítása a felülvizsgálat Azure AD-ből.

> [!IMPORTANT]
> Nincs figyelmeztetés törlése bekövetkezte előtt van, ezért ügyeljen arra, hogy szeretné-e a felülvizsgálat törlése.
> 
> 

## <a name="next-steps"></a>További lépések

- [Felhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Vendégfelhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Programok és vezérlők felügyelete az Azure AD hozzáférési felülvizsgálatokkal](active-directory-azure-ad-controls-manage-programs-controls.md)
- [Hozzáférési felülvizsgálat létrehozása egy csoport tagjai számára vagy egy alkalmazáshoz való hozzáférés céljából](active-directory-azure-ad-controls-create-access-review.md)
- [Hozzáférési felülvizsgálat létrehozása Azure AD rendszergazdai szerepkörrel rendelkező felhasználókhoz](privileged-identity-management/pim-how-to-start-security-review.md)
