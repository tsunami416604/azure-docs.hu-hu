---
title: Hozzáférési felülvizsgálat végrehajtása |} A Microsoft Docs
description: Útmutató az Azure Privileged Identity Management alkalmazásban felülvizsgálat végrehajtása.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 4d19f9e04b6288fad70f62e95579475c16f2112c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590337"
---
# <a name="how-to-perform-an-access-review-in-azure-ad-privileged-identity-management"></a>Hozzáférési felülvizsgálat végrehajtása az Azure AD Privileged Identity Management
Az Azure Active Directory (AD) Privileged Identity Management egyszerűsíti, hogy hogyan kezelhetik a vállalatok számára az erőforrások az Azure AD és más Microsoft online szolgáltatásaihoz, például az Office 365 vagy a Microsoft Intune emelt szintű hozzáférés.  

Ha egy rendszergazdai szerepkörrel vannak rendelve, a szervezet kiemelt szerepkörű rendszergazda megkérheti, hogy rendszeresen győződjön meg arról, hogy továbbra is szerepkörre van szüksége, hogy a feladatnak. Előfordulhat, hogy kap egy e-mailt, amelyben a hivatkozást, vagy megnyithatja közvetlenül a [az Azure portal](https://portal.azure.com). Kövesse a cikkben egy önálló tekintse át a hozzárendelt szerepkörök végrehajtásához.

Ha Ön kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai hozzáférési felülvizsgálatok iránt, a részletesebb információt kap a [hozzáférési felülvizsgálat indítása](pim-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>A Privileged Identity Management alkalmazás felvétele
Használhatja az Azure AD Privileged Identity Management (PIM) alkalmazás a [az Azure portal](https://portal.azure.com/) a felülvizsgálat végrehajtása.  Ha a portálon az Azure AD Privileged Identity Management alkalmazás nem rendelkezik, kövesse az alábbi lépéseket a kezdéshez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a felhasználónevére a jobb felső sarkában az Azure Portalon, és válassza a könyvtárban, ahol Ön lesz működik.
3. Válassza a **Minden szolgáltatás** lehetőséget, és a Szűrő szövegmezővel keresse meg az **Azure AD Privileged Identity Management** elemet.
4. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.

## <a name="approve-or-deny-access"></a>Jóváhagyja vagy megtagadja a hozzáférést
Jóváhagyja vagy megtagadja a hozzáférést, akkor még csak mondanunk a felülvizsgáló e továbbra is használhatja ezt a szerepkört, vagy nem. Válasszon **jóváhagyás** Ha szeretne maradni a szerepkörhöz vagy **Megtagadás** Ha többé már nincs szüksége a hozzáférést. Az állapot a mindaddig, amíg a felülvizsgáló vonatkozik az eredmények azonnal, nem fognak változni.
Kövesse az alábbi lépéseket, és a hozzáférési felülvizsgálat befejezése:

1. Válassza ki a PIM alkalmazásban **tekintse át a privilegizált hozzáférési jogosultsága**. Ha minden függőben lévő hozzáférési felülvizsgálatokat, azok megjelennek az Azure AD hozzáférési felülvizsgálatok panelen.
2. Jelölje be a felülvizsgálatot befejezéséhez.
3. Kivéve, ha a felülvizsgálat hozta létre, akkor a felülvizsgálati kizárólag egyetlen felhasználó tartalomként jelennek meg. Válassza ki a pipa jelre a neve mellett.
4. Válasszon **jóváhagyása** vagy **megtagadása**. Szükség lehet a döntéseiben okát tartalmazza a **indokolja** szövegmezőben.  
5. Zárja be a **tekintse át az Azure AD-szerepkörök** panelen.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
