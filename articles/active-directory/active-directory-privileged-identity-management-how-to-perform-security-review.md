---
title: Hogyan hajthat végre egy áttekintése |} Microsoft Docs
description: Útmutató az Azure Privileged Identity Management alkalmazással felülvizsgálat végrehajtása.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7c536a4e7f93a2f1ef42b7600513994dd80826a0
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37020870"
---
# <a name="how-to-perform-an-access-review-in-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management egy hozzáférési felülvizsgálat végrehajtása
Az Azure Active Directory (AD) Privileged Identity Management egyszerűbbé teszi a hogyan kezelhetik a vállalatok számára az erőforrások az Azure AD és más Microsoft online szolgáltatások, például az Office 365-öt vagy a Microsoft Intune privilegizált hozzáférést.  

Ha egy rendszergazdai szerepkör van rendelve, a szervezete kiemelt szerepkörű rendszergazda megkérheti, hogy rendszeresen győződjön meg arról, hogy továbbra is szerepkörre van szüksége, hogy a feladat. Előfordulhat, hogy kap egy e-mailt, amely tartalmaz egy hivatkozást, vagy elvégezheti a rögtön a [Azure-portálon](https://portal.azure.com). Kövesse a cikkben egy önálló tekintse át a hozzárendelt szerepkörök végrehajtásához.

Ha egy kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai hozzáférés értékelést iránt érdeklődik, ezzel további adatokat, [egy hozzáférési felülvizsgálat indítása](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>A Privileged Identity Management alkalmazás felvétele
Az Azure AD Privileged Identity Management (PIM) alkalmazást is használhatja a [Azure-portálon](https://portal.azure.com/) a felülvizsgálat végrehajtása.  Ha az Azure AD Privileged Identity Management alkalmazás nem rendelkezik a portálon, kövesse az alábbi lépéseket a kezdéshez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Jelölje be az Azure-portálon, és válassza ki a könyvtárat, ahol meg fog jobb felső sarokban a felhasználónevére működik.
3. Válassza a **Minden szolgáltatás** lehetőséget, és a Szűrő szövegmezővel keresse meg az **Azure AD Privileged Identity Management** elemet.
4. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.

## <a name="approve-or-deny-access"></a>Hagyja jóvá vagy nem engedélyezik a hozzáférést
Hagyja jóvá vagy nem engedélyezik a hozzáférést, akkor még csak szólítja fel a felülvizsgáló e továbbra is használhatja ezt a szerepkört vagy nem. Válasszon **jóváhagyás** Ha azt szeretné, hogy a szerepkör belül vagy **Megtagadás** Ha már nincs szüksége a hozzáférés. Az állapota nem azonnal, megváltoztatni, amíg meg nem a felülvizsgáló vonatkozik az eredményeket.
Kövesse az alábbi lépéseket található, majd fejezze be a áttekintése:

1. Válassza ki a PIM alkalmazást **felülvizsgálati emelt szintű hozzáférés**. Ha minden függőben lévő hozzáférés felülvizsgálatra, azok megjelennek az Azure AD hozzáférési értékelést panel.
2. Válassza ki a végrehajtani kívánt áttekintése.
3. Kivéve, ha a felülvizsgálati hozott létre, akkor jelennek meg az egyetlen felhasználó a felülvizsgálat alatt. Válassza ki a pipa jelre a neve mellett.
4. Válasszon **jóváhagyása** vagy **megtagadása**. Szükség lehet a döntés okát közé tartozik a **okot** szövegmezőben.  
5. Zárja be a **tekintse át az Azure AD-szerepkörök** panelen.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
