---
title: Az Azure AD-szerepköröket a PIM - Azure Active Directory hozzáférési felülvizsgálatok elvégzése |} A Microsoft Docs
description: Útmutató az Azure AD Privileged Identity Management (PIM) az Azure AD-szerepkörök hozzáférési felülvizsgálat befejezése és az eredmények megtekintése
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a7fa3bfe159620130bc0962b470cea8e7422646
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602167"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-pim"></a>Az Azure AD PIM-szerepkörök hozzáférési felülvizsgálat befejezése
A kiemelt szerepkörű rendszergazdák után tekintse át a privileged access- [el lett indítva a hozzáférési felülvizsgálat](pim-how-to-start-security-review.md). Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) automatikusan arra kéri a felhasználót, tekintse át a hozzáférése az e-mailt küld. Ha a felhasználó nem kapott e-mailt, küldhet nekik az utasításokat [hozzáférési felülvizsgálat végrehajtása](pim-how-to-perform-security-review.md).

Után a hozzáférési felülvizsgálati időszak alatt, vagy minden felhasználó befejezte a saját tekintse át, kövesse a cikkben az eredmények megtekintéséhez és kezeléséhez a felülvizsgálatot.

## <a name="manage-access-reviews"></a>A hozzáférési felülvizsgálatok kezelése
1. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és válassza ki a **Azure AD Privileged Identity Management** alkalmazás a az irányítópulthoz.
2. Válassza ki a **hozzáférési felülvizsgálatokkal** szakasz az irányítópult.
3. Válassza ki a hozzáférési felülvizsgálatot, amelyet kezelni szeretne.

A hozzáférési felülvizsgálat részletei panelen, egy szám lehetőség van felülvizsgálat kezeléséhez.

![PIM hozzáférési felülvizsgálati gombok – képernyőkép](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Emlékeztető
Ha a hozzáférési felülvizsgálat be van állítva, így a felhasználók maguk, tekintse át a **emlékeztetése** gomb értesítést küld. 

### <a name="stop"></a>Leállítás
Minden hozzáférési felülvizsgálatok a befejező dátum rendelkezik, de használhatja a **leállítása** gomb korai befejezéséhez. Ha a felhasználók még nem lett felülvizsgálva ez idő szerint, nem tudják, a felülvizsgálat befejezése után. Felülvizsgálat nem indítható újra, miután van állítva.

### <a name="apply"></a>Alkalmaz
Hozzáférési felülvizsgálat befejezése után, mert elérte a záró dátum, vagy leállt, manuálisan, vagy a **alkalmaz** gomb a felülvizsgálat eredményének valósítja meg. Ha a felhasználó megtagadja a felülvizsgálati, akkor a lépés, amely eltávolítja a szerepkör-hozzárendelés.  

### <a name="export"></a>Exportálás
Ha azt szeretné, a hozzáférési felülvizsgálat eredményei manuális alkalmazásához, exportálhatja a felülvizsgálatot. A **exportálása** gomb indul el egy CSV-fájl letöltése. Az eredményeket Excelben vagy más programok, nyissa meg a CSV-fájlok is kezelheti.

### <a name="delete"></a>Törlés
Ha nem érdekli a felülvizsgálat minden további, törölje azt. A **törlése** gomb a felülvizsgálat távolít el a PIM alkalmazást.

> [!IMPORTANT]
> Meg fog nem megjelenik egy figyelmeztetés, akkor fordul elő, törlés előtt, ezért arról, hogy szeretné-e a felülvizsgálat törlése. 

## <a name="next-steps"></a>További lépések

- [A PIM az Azure AD-szerepkörök hozzáférési felülvizsgálat indítása](pim-how-to-start-security-review.md)
- [A PIM a saját Azure AD-szerepkörök hozzáférési felülvizsgálat végrehajtása](pim-how-to-perform-security-review.md)
