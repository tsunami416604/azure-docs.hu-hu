---
title: "Hozzon létre egy csoport vagy a hozzáférést egy alkalmazáshoz, az Azure ad-vel rendelkező felhasználók tagjai egy áttekintése |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy áttekintése egy csoport vagy az alkalmazáshoz hozzáféréssel rendelkező felhasználók tagjai számára."
services: active-directory
author: markwahl-msft
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: b2f8985f12e17ac69543cfb3a33725f796eedde8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Hozzon létre egy áttekintése csoporttagok vagy alkalmazás-hozzáférés az Azure ad-vel

Hozzáférés hozzárendelések vált "elavult", ha a felhasználók többé nem kell hozzáférést. Elavult hozzáférés hozzárendelések társított kockázatok csökkentése érdekében a rendszergazdák segítségével Azure Active Directory (Azure AD) hozzon létre egy áttekintése csoport tagjainak vagy egy alkalmazás rendelt felhasználók. Ezek a forgatókönyvek további információkért lásd: [felügyelheti a felhasználók hozzáférését](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) és [vendég-hozzáférés kezelése](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Hozzon létre egy áttekintése

1. Globális rendszergazdaként, navigáljon a [hozzáférés ellenőrzi, hogy lap](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), és válassza ki **programok**.

2. Válassza ki a program, amely tárolja a felülvizsgálati hozzáférés szeretne létrehozni. **Alapértelmezett Program** mindig jelen, vagy létrehozhat egy másik programot. Választhat, például, hogy minden megfelelőségi Initiative egy program vagy üzleti cél.

3. Válassza ki a programból **vezérlők**, majd válassza ki **Hozzáadás** vezérlő hozzáadása.

4. Nevezze el minden áttekintése. Minden egyes felülvizsgálati szükség esetén adjon meg leírást. A többi felhasználó neve megjelenik.

5. A kezdő és záró dátumának beállítása. Alapértelmezés szerint egy hozzáférési tekintse át a ugyanarra a napra esnek létrehozták, és karakterlánccal végződik-e havi elindul. Módosíthatja a kezdő és záró dátumát, az elérhető legyen, ellenőrizze start legutóbbi és a jövőben azonban hány napig szeretné.

6. Hozzáférés értékelést lehet egy csoport tagjai számára, vagy a felhasználók számára egy alkalmazás lettek társítva. További hatókörét megadhatja a hozzáférés csak tekintse át a tekintse át a Vendég számára tagok (vagy az alkalmazáshoz hozzárendelt), ahelyett, hogy minden olyan felhasználó, akik tagjai áttekintése vagy rendelkező felhasználók hozzáférni az alkalmazáshoz.

7. Válasszon egy vagy több személyeket, és tekintse át a hatókör összes felhasználóját. Vagy tekintse át a saját hozzáférésüket a tagot is kiválaszthatja. Ha az erőforrás egy csoport, kérje meg a csoportházirend-tulajdonosok áttekintéséhez. Is megkövetelheti, hogy felülvizsgáló OK adnia, amikor azok hagyja jóvá a hozzáférés.

8. Végül válassza ki **Start**.


## <a name="manage-the-access-review"></a>Kezelheti a áttekintése

Alapértelmezés szerint az Azure AD küld egy e-mailt a felülvizsgálók röviddel a felülvizsgálat indítása után. Ha úgy dönt, hogy ne legyenek az e-mailek küldése az Azure AD, ügyeljen arra, hogy tájékoztatja a felülvizsgálók váró egy áttekintése befejeződnek. Megjelenítheti őket a utasításokkal szolgál [tekintse át a hozzáférési](active-directory-azure-ad-controls-perform-access-review.md). Ha a felülvizsgálati tekintse át a saját hozzáférésüket a Vendégek, megjelenítéséhez a utasításokkal szolgál [tekintse át a saját hozzáférés](active-directory-azure-ad-controls-perform-access-review.md).

Ha a felülvizsgálók némelyike Vendégek, Vendégek értesítést kap e-mailben csak akkor, ha már korábban a meghívó elfogadásának.


A végrehajtás, a többi felhasználó végezze el az Azure AD-irányítópulton a áttekintette nyomon a **ellenőrzi, hogy hozzáférési** szakasz. Nincs hozzáférési jogosultsága, módosulnak, amíg a címtár [a felülvizsgálati befejeződött](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Következő lépések

Egy áttekintése indításakor az Azure AD nekik hozzáférést kérő e-mailt küld automatikusan felülvizsgálók. Ha a felhasználó nem kap egy e-mailt, elküldhetjük a utasításokkal szolgál [tekintse át a hozzáférési](active-directory-azure-ad-controls-perform-access-review.md). 

Miután a hozzáférés felülvizsgálati időszak keresztül, vagy a rendszergazda leállítja a áttekintése, kövesse a [végrehajtani egy áttekintése](active-directory-azure-ad-controls-complete-access-review.md) megtekintéséhez és alkalmazásához az eredményeket.


