---
title: Tekintse át a PIM - Azure Active Directory Azure-erőforrásszerepkörök hozzáférést |} A Microsoft Docs
description: Ismerje meg, hogyan tekintse át az Azure AD Privileged Identity Management (PIM) az Azure-erőforrásszerepkörök hozzáférését.
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
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e49f0077444f86eabeed22cc7cecf5e0ac7f8b5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141187"
---
# <a name="review-access-to-azure-resource-roles-in-pim"></a>Tekintse át az Azure-erőforrásszerepkörök az PIM-ben való hozzáférés
Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) egyszerűbbé teszi a hogyan kezelhetik a vállalatok számára az emelt szintű hozzáférés az erőforrásokhoz az Azure-ban. 

Ha egy rendszergazdai szerepkörrel vannak rendelve, a szervezet kiemelt szerepkörű rendszergazda fel, hogy rendszeresen győződjön meg arról, hogy továbbra is szerepkörre van szüksége, hogy a feladatnak. Előfordulhat, hogy kap egy e-mailt, amelyben a hivatkozást, vagy megnyithatja közvetlenül a [az Azure portal](https://portal.azure.com). Kövesse a cikkben egy önálló tekintse át a hozzárendelt szerepkörök végrehajtásához.

Ha érdekli a hozzáférési felülvizsgálatok kiemelt szerepkörű rendszergazdák, a részletesebb információt kap a [hozzáférési felülvizsgálat indítása](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>A Privileged Identity Management alkalmazás felvétele
Használhatja az Azure Active Directory (Azure AD) a PIM alkalmazást az a [az Azure portal](https://portal.azure.com/) a felülvizsgálat végrehajtása. Ha az alkalmazás nem rendelkezik a portálon, kövesse az alábbi lépéseket a kezdéshez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Jelölje ki a felhasználó neve az Azure portal jobb felső sarkában, és válassza ki a címtárat, ahol Ön lesz működik.
3. Válassza ki **minden szolgáltatás**, és használja a **szűrő** mező használatával keresheti meg *Azure AD Privileged Identity Management*.
4. Ellenőrizze **rögzítés az irányítópulton**, majd válassza ki **létrehozás**. A PIM alkalmazást nyitja meg.

## <a name="approve-or-deny-access"></a>Jóváhagyja vagy megtagadja a hozzáférést
Jóváhagyja vagy megtagadja a hozzáférést, akkor még csak mondanunk a felülvizsgáló e továbbra is használhatja ezt a szerepkört, vagy nem. Válasszon **jóváhagyás** Ha szeretne maradni a szerepkörhöz vagy **Megtagadás** Ha többé már nincs szüksége a hozzáférést. Az állapot változik, csak akkor, amikor a felülvizsgáló vonatkozik az eredményeket.

Kövesse az alábbi lépéseket, és a hozzáférési felülvizsgálat befejezése:
1. Keresse meg az Azure AD PIM alkalmazást.
2. Válassza ki a **hozzáférés felülvizsgálata** panelen.

   ![Képernyőkép a PIM alkalmazást, tekintse át a hozzáférés panelen kiválasztott](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Jelölje be a felülvizsgálatot befejezéséhez. 
4. Válasszon **jóváhagyása** vagy **megtagadása**. Az a **adjon meg egy OK mező**, szüksége lehet a döntést okát tartalmazza.

   ![Képernyőkép a felülvizsgálati Részletek lap](media/azure-pim-resource-rbac/rbac-access-review-choice.png)

## <a name="next-steps"></a>További lépések

- [A PIM a saját Azure AD-szerepkörök hozzáférési felülvizsgálat végrehajtása](pim-how-to-perform-security-review.md)
