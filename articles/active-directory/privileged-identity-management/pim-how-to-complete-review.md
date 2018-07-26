---
title: Hozzáférési felülvizsgálat befejezése |} A Microsoft Docs
description: Hozzáférési felülvizsgálatok az Azure AD Privileged Identity Management kezdi, miután megtudhatja, hogyan végezze el, és az eredmények megtekintése
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e8aacc95f73294420042549681aff5494dc63cce
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257810"
---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management hozzáférési felülvizsgálat befejezése
A kiemelt szerepkörű rendszergazdák után tekintse át a privileged access- [el lett indítva a hozzáférési felülvizsgálat](pim-how-to-start-security-review.md). Az Azure AD Privileged Identity Management (PIM) automatikusan arra kéri a felhasználót, tekintse át a hozzáférése az e-mailt küld. Ha a felhasználó nem kapott e-mailt, küldhet nekik az utasításokat [hozzáférési felülvizsgálat végrehajtása](pim-how-to-perform-security-review.md).

Után a hozzáférési felülvizsgálati időszak alatt, vagy minden felhasználó befejezte a saját tekintse át, kövesse a cikkben az eredmények megtekintéséhez és kezeléséhez a felülvizsgálatot.

## <a name="manage-access-reviews"></a>A hozzáférési felülvizsgálatok kezelése
1. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és válassza ki a **Azure AD Privileged Identity Management** alkalmazás a az irányítópulthoz.
2. Válassza ki a **hozzáférési felülvizsgálatokkal** szakasz az irányítópult.
3. Válassza ki a hozzáférési felülvizsgálatot, amelyet kezelni szeretne.

A hozzáférési felülvizsgálat részletei panelen, egy szám lehetőség van felülvizsgálat kezeléséhez.

![PIM hozzáférési felülvizsgálati gombok – képernyőkép](./media/pim-how-to-complete-review/PIM_review_buttons.png)

### <a name="remind"></a>Emlékeztetés
Ha a hozzáférési felülvizsgálat be van állítva, így a felhasználók maguk, tekintse át a **emlékeztetése** gomb értesítést küld. 

### <a name="stop"></a>Leállítás
Minden hozzáférési felülvizsgálatok a befejező dátum rendelkezik, de használhatja a **leállítása** gomb korai befejezéséhez. Ha a felhasználók még nem lett felülvizsgálva ez idő szerint, nem tudják, a felülvizsgálat befejezése után. Felülvizsgálat nem indítható újra, miután van állítva.

### <a name="apply"></a>Jelentkezés
Hozzáférési felülvizsgálat befejezése után, mert elérte a záró dátum, vagy leállt, manuálisan, vagy a **alkalmaz** gomb a felülvizsgálat eredményének valósítja meg. Ha a felhasználó megtagadja a felülvizsgálati, akkor a lépés, amely eltávolítja a szerepkör-hozzárendelés.  

### <a name="export"></a>Exportálás
Ha azt szeretné, a hozzáférési felülvizsgálat eredményei manuális alkalmazásához, exportálhatja a felülvizsgálatot. A **exportálása** gomb indul el egy CSV-fájl letöltése. Az eredményeket Excelben vagy más programok, nyissa meg a CSV-fájlok is kezelheti.

### <a name="delete"></a>Törlés
Ha nem érdekli a felülvizsgálat minden további, törölje azt. A **törlése** gomb a felülvizsgálat távolít el a PIM alkalmazást.

> [!IMPORTANT]
> Meg fog nem megjelenik egy figyelmeztetés, akkor fordul elő, törlés előtt, ezért arról, hogy szeretné-e a felülvizsgálat törlése. 

## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
