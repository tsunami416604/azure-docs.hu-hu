---
title: Az Azure AD-szerepkörök hozzáférési felülvizsgálatának befejezése a PIM-Azure Active Directoryban | Microsoft Docs
description: Ismerje meg, hogyan végezheti el a Azure AD Privileged Identity Management (PIM) Azure AD szerepköreinek hozzáférési felülvizsgálatát, és megtekintheti az eredményeket
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/06/2017
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50ccc208219896e89bcc80f40c846f69c759f9b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804403"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-pim"></a>Azure AD-szerepkörök hozzáférési felülvizsgálatának befejezése a PIM-ben
A Kiemelt szerepkörű rendszergazdák a [hozzáférési felülvizsgálat elindítása](pim-how-to-start-security-review.md)után ellenőrizhetik a Kiemelt jogosultságú hozzáférést. A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) automatikusan elküld egy e-mailt, amely arra kéri a felhasználót, hogy tekintsék át a hozzáférését. Ha a felhasználó nem kapott e-mailt, elküldheti a [hozzáférési felülvizsgálat végrehajtásának](pim-how-to-perform-security-review.md)utasításait.

Miután a hozzáférési felülvizsgálati időszak véget ért, vagy az összes felhasználó befejezte az önellenőrzést, kövesse a jelen cikkben ismertetett lépéseket a felülvizsgálat kezeléséhez és az eredmények megtekintéséhez.

## <a name="manage-access-reviews"></a>Hozzáférési felülvizsgálatok kezelése
1. Nyissa meg a [Azure Portal](https://portal.azure.com/) , és válassza ki a **Azure ad Privileged Identity Management** alkalmazást az irányítópulton.
2. Válassza ki az irányítópult **hozzáférési felülvizsgálatok** szakaszát.
3. Válassza ki a felügyelni kívánt hozzáférési felülvizsgálatot.

A hozzáférési felülvizsgálat részletei panelen számos lehetőség áll rendelkezésre a felülvizsgálat kezelésére.

![A PIM hozzáférési felülvizsgálati gombjai – képernyőfelvétel](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Emlékeztetés
Ha a hozzáférési felülvizsgálat úgy van beállítva, hogy a felhasználók saját maguk is felülvizsgálják magukat, az **emlékeztető** gomb értesítést küld. 

### <a name="stop"></a>Leállítás
Az összes hozzáférési felülvizsgálat befejező dátummal rendelkezik, de a **Leállítás** gomb használatával is befejezheti korán. Ha a felhasználó nem tekinti át ezt az időt, nem fogja tudni leállítani a felülvizsgálatot. Az ellenőrzés leállítása után nem indítható újra.

### <a name="apply"></a>Alkalmaz
A hozzáférési felülvizsgálat befejezése után vagy azért, mert elérte a befejezési dátumot, vagy manuálisan leállította, az **Apply (alkalmaz** ) gomb végrehajtja a felülvizsgálat eredményét. Ha egy felhasználó hozzáférése meg lett tagadva a felülvizsgálat során, akkor ez az a lépés, amely eltávolítja a szerepkör-hozzárendelését.  

### <a name="export"></a>Exportálás
Ha a hozzáférési felülvizsgálat eredményeit manuálisan szeretné alkalmazni, exportálhatja a felülvizsgálatot. Az **Exportálás** gomb megkezdi a CSV-fájl letöltését. Az eredményeket az Excelben vagy más, CSV-fájlokat megnyitó programok segítségével kezelheti.

### <a name="delete"></a>Törlés
Ha még nem érdekli a felülvizsgálat, törölje azt. A **Törlés** gomb eltávolítja a vizsgálatot a PIM alkalmazásból.

> [!IMPORTANT]
> A törlés előtt nem fog figyelmeztetést kapni, ezért mindenképpen törölje ezt a felülvizsgálatot. 

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkörök hozzáférési felülvizsgálatának elindítása a PIM-ben](pim-how-to-start-security-review.md)
- [Azure AD-szerepkörök hozzáférési felülvizsgálatának végrehajtása a PIM-ben](pim-how-to-perform-security-review.md)
