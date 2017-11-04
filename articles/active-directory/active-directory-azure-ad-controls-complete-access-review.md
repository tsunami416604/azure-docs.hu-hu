---
title: "Fejezze be a tagok egy csoport vagy egy alkalmazás az Azure ad-vel való hozzáférést egy áttekintése |} Microsoft Docs"
description: "Ismerje meg, hogyan lehet elvégezni egy áttekintése egy csoport vagy az Azure Active Directoryban alkalmazáshoz való hozzáféréssel rendelkező felhasználók tagjai számára."
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: b301ff06c01d51c02f7d7393801b35cd8965403c
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>Fejezze be a tagok egy csoport vagy egy alkalmazás az Azure ad-ben való hozzáférést egy áttekintése

Rendszergazdák segítségével Azure Active Directory (Azure AD) [hozzon létre egy áttekintése](active-directory-azure-ad-controls-create-access-review.md) csoport tagjainak vagy egy alkalmazás rendelt felhasználók. Az Azure AD nekik hozzáférést kérő e-mailt automatikusan elküldi a felülvizsgálók. Ha a felhasználó nem kap egy e-mailt, küldhet nekik az utasításokat [tekintse át a hozzáférés](active-directory-azure-ad-controls-perform-access-review.md). A áttekintése után időszak keresztül, vagy ha egy rendszergazda leállítja a áttekintése, kövesse a cikk megtekintéséhez és alkalmazásához az eredményeket.

## <a name="view-an-access-review-in-the-azure-portal"></a>Tekintse meg az Azure portál egy áttekintése

1. Lépjen a [hozzáférés ellenőrzi, hogy lap](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), jelölje be **programok**, és válassza ki a programot, amely tartalmazza a felülvizsgálati hozzáférés-vezérlés.

2. Válassza ki **kezelése**, és jelölje ki a hozzáférési áttekintése. Ha a program sok, egy adott típusú és egy rendezési vezérlők szerint szűrheti az állapotukat. Is kereshet a felülvizsgálati hozzáférés-vezérlés nevét vagy a tulajdonos, aki létrehozta a megjelenített név. 

## <a name="stop-a-review-that-hasnt-finished"></a>Állítsa le, amely még nem fejeződött be áttekintése

Ha a felülvizsgálati még nem érhető el az ütemezett befejezési dátum, válassza a rendszergazda **leállítása** korai befejezi. A felülvizsgálati befejezése után felhasználók már nem tekinthető. Felülvizsgálat nem indítható újra, miután le van állítva.

## <a name="apply-the-changes"></a>A módosítások életbe léptetéséhez 

Egy áttekintése után, mert elérte a záró dátum vagy egy rendszergazda manuálisan, leállt kijelölhet **alkalmaz**. A felülvizsgálati eredményeit a csoport vagy az alkalmazás frissítése valósítják meg. Ha a felhasználói hozzáférés megtagadva a felülvizsgálat alatt, a rendszergazda ezt a lehetőséget választja, az Azure AD eltávolítja a tagság vagy alkalmazás-hozzárendelés. 

Kiválasztása **alkalmaz** nincs hatással a egy helyszíni Directory származó vagy dinamikus csoportot. Ha szeretné módosítani egy csoportot, amely a helyszíni származik, töltse le az eredményeket, és ezeket a módosításokat alkalmazza a megjelenítésre, a csoport ebben a könyvtárban.

## <a name="download-the-results-of-the-review"></a>Töltse le a felülvizsgálati eredményeit

Válassza ki a felülvizsgálati eredményeinek lekéréséhez **jóváhagyások** , és válassza **letöltése**. Az eredményül kapott CSV-fájl az Excel vagy más programok, nyissa meg a CSV-fájlok is megtekinthetők.

## <a name="optional-delete-a-review"></a>Választható lehetőség: Törlése áttekintése
Ha már nem szeretné használni a felülvizsgálati, törölheti azt. Válassza ki **törlése** eltávolítása a tekintse át az Azure AD.

> [!IMPORTANT]
> Nincs még törlése előtt figyelmeztetés van, ezért ügyeljen arra, hogy valóban törli a felülvizsgálati.
> 
> 

## <a name="next-steps"></a>Következő lépések

- [Felhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Vendégfelhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Programok és vezérlők felügyelete az Azure AD hozzáférési felülvizsgálatokkal](active-directory-azure-ad-controls-manage-programs-controls.md)
- [Hozzáférési felülvizsgálat létrehozása egy csoport tagjai számára vagy egy alkalmazáshoz való hozzáférés céljából](active-directory-azure-ad-controls-create-access-review.md)
- [Hozzáférési felülvizsgálat létrehozása Azure AD rendszergazdai szerepkörrel rendelkező felhasználókhoz](active-directory-privileged-identity-management-how-to-start-security-review.md)
