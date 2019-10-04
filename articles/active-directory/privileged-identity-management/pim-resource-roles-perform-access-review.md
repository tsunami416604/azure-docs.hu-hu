---
title: Az Azure-beli erőforrás-szerepkörök hozzáférésének áttekintése a PIM-Azure Active Directoryban | Microsoft Docs
description: Ismerje meg, hogyan tekintheti át Azure AD Privileged Identity Management (PIM) Azure Resource szerepköreinek hozzáférését.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c0ce1a5eee4b8d4cc40c47dadd4bcdc74d03ba
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804107"
---
# <a name="review-access-to-azure-resource-roles-in-pim"></a>Tekintse át az Azure-erőforrás szerepköreinek hozzáférését a PIM-ben
A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) leegyszerűsíti, hogy a vállalatok hogyan kezelhetik az Azure-beli erőforrásokhoz való rendszerjogosultságú hozzáférést. 

Ha rendszergazdai szerepkörhöz van rendelve, a szervezet Kiemelt szerepkörű rendszergazdája kérheti, hogy rendszeresen erősítse meg, hogy továbbra is szüksége van erre a szerepkörre a feladathoz. Előfordulhat, hogy olyan e-mailt kap, amely hivatkozást tartalmaz, vagy közvetlenül a [Azure Portal](https://portal.azure.com). A cikk lépéseit követve végezze el a hozzárendelt szerepkörök önértékelését.

Ha Ön a hozzáférési felülvizsgálatok iránt érdeklődő Kiemelt szerepkörű rendszergazda, további információt talál a [hozzáférési felülvizsgálat elindításáról](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>A Privileged Identity Management alkalmazás felvétele
A felülvizsgálat végrehajtásához használhatja a [Azure Portal](https://portal.azure.com/) Azure Active Directory (Azure ad) PIM alkalmazást. Ha nem rendelkezik az alkalmazással a portálon, kövesse az alábbi lépéseket a kezdéshez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki a felhasználónevet a Azure Portal jobb felső sarkában, és válassza ki azt a könyvtárat, ahol működni fog.
3. Válassza a **minden szolgáltatás**lehetőséget, majd a **szűrő** mezőben keresse meg a *Azure ad Privileged Identity Management*.
4. Jelölje be **a rögzítés az irányítópulton**jelölőnégyzetet, majd kattintson a **Létrehozás**gombra. Megnyílik a PIM alkalmazás.

## <a name="approve-or-deny-access"></a>Hozzáférés jóváhagyása vagy megtagadása
Ha jóváhagyja vagy megtagadja a hozzáférést, csak azt közli a véleményezővel, hogy továbbra is használja-e ezt a szerepkört. Válassza a **jóváhagyás** lehetőséget, ha a szerepkörben szeretne maradni, **vagy ha** nem szeretné, hogy a hozzáférés többé nem szükséges. Az állapot csak akkor változik, ha a felülvizsgáló alkalmazza az eredményeket.

Az alábbi lépéseket követve megkeresheti és elvégezheti a hozzáférési felülvizsgálatot:
1. Tallózással keresse meg az Azure AD PIM alkalmazást.
2. Válassza a **hozzáférés áttekintése** panelt.

   ![Képernyőfelvétel a PIM-alkalmazásról, a hozzáférés áttekintése panel kijelölésével](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

3. Válassza ki a végrehajtani kívánt felülvizsgálatot. 
4. Válassza a **jóváhagyás** vagy a **Megtagadás**lehetőséget. Az adja meg a **megfelelő okot mezőben**szükség lehet a döntés indoklására.

   ![A részletek áttekintése lap képernyőképe](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkörök hozzáférési felülvizsgálatának végrehajtása a PIM-ben](pim-how-to-perform-security-review.md)
