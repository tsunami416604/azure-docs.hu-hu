---
title: Az Azure AD-szerepkörök hozzáférésének befejezése a PIM-ben – Azure AD | Microsoft dokumentumok
description: Megtudhatja, hogyan végezheti el az Azure AD-szerepkörök hozzáférés-felülvizsgálatát az Azure AD-alapú kiemelt identitáskezelésben (PIM) és az eredmények megtekintése
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe2d85d605b9ee418a5709ddcdb448c56be1d918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022283"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Access-ellenőrzés befejezése az Azure AD-szerepkörökről a kiemelt identitáskezelésben

A kiemelt szerepszintű rendszergazdák a [hozzáférés-felülvizsgálat megkezdése](pim-how-to-start-security-review.md)után áttekinthetik a kiemelt hozzáféréseket.  Privilegizált identitáskezelés (PIM) automatikusan küld egy e-mailt a felhasználók az Azure Active Directory (Azure AD) szervezet kéri őket, hogy tekintse át a hozzáférést. Ha a felhasználó nem kap e-mailt, elküldheti nekik a [hozzáférési felülvizsgálat elvégzéséhez.](pim-how-to-perform-security-review.md)

Miután a hozzáférési felülvizsgálati időszak lejárt, vagy az összes felhasználó befejezte az önellenőrzés, kövesse a cikkben leírt lépéseket az ellenőrzés kezeléséhez és az eredmények megtekintéséhez.

## <a name="manage-access-reviews"></a>Hozzáférés-felülvizsgálatok kezelése

1. Nyissa meg az [Azure Portalon,](https://portal.azure.com/) és válassza ki az **Azure AD kiemelt identitáskezelési** szolgáltatás az irányítópulton.
1. Válassza ki az **irányítópult Access-ellenőrzések** szakaszát.
1. Jelölje ki a kezelni kívánt hozzáférési felülvizsgálatot.

A hozzáférési felülvizsgálat részletes panelen számos lehetőség van az ellenőrzés kezelésére.

![Kiemelt identitáskezelési hozzáférési ellenőrző gombok – képernyőkép](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Emlékeztetni

Ha a hozzáférés-felülvizsgálat úgy van beállítva, hogy a felhasználók átnézzék magukat, az **Emlékeztető** gomb értesítést küld.

### <a name="stop"></a>Leállítás

Minden hozzáférési felülvizsgálatnak van záró dátuma, de a **Leállítás** gombbal korán befejezheti. Ha eddig egyetlen felhasználót sem vizsgáltak meg, akkor az ellenőrzés leállítása után nem fogják tudni. Az ellenőrzés leállítása után nem indítható újra.

### <a name="apply"></a>Alkalmaz

A hozzáférés-felülvizsgálat befejezése után, vagy azért, mert elérte a befejezési dátumot, vagy manuálisan leállította, az **Alkalmaz** gomb megvalósítja az ellenőrzés eredményét. Ha egy felhasználó hozzáférését megtagadták az ellenőrzés, ez a lépés, amely eltávolítja a szerepkör-hozzárendelés.  

### <a name="export"></a>Exportálás

Ha manuálisan szeretné alkalmazni a hozzáférési felülvizsgálat eredményeit, exportálhatja az értékelést. Az **Exportálás** gomb elindítja egy CSV-fájl letöltését. Az eredményeket kezelheti az Excelben vagy más, CSV-fájlokat megnyitva tartalmazó programokban.

### <a name="delete"></a>Törlés

Ha nem érdekli a felülvizsgálat tovább, törölje azt. A **Törlés** gomb eltávolítja az értékelést a Kiemelt identitáskezelés szolgáltatásból.

> [!IMPORTANT]
> Nem kell megerősítenie ezt a roncsolásos módosítást, ezért ellenőrizze, hogy törölni szeretné-e az értékelést.

## <a name="next-steps"></a>További lépések

- [Hozzáférés-felülvizsgálat indítása az Azure AD-szerepkörökhöz a kiemelt identitáskezelésben](pim-how-to-start-security-review.md)
- [Access-ellenőrzés végrehajtása az Azure AD-szerepkörökről a kiemelt identitáskezelésben](pim-how-to-perform-security-review.md)
