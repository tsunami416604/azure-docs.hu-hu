---
title: Azure AD-szerepkörök hozzáférésének áttekintése a PIM-Azure Active Directoryban | Microsoft Docs
description: Ismerje meg, hogyan tekintheti át az Azure AD-szerepkörök hozzáférését Azure AD Privileged Identity Management (PIM) alkalmazásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/21/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bd548041b086eef8d788a696497163c756fd5b7
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804426"
---
# <a name="review-access-to-azure-ad-roles-in-pim"></a>Azure AD-szerepkörök hozzáférésének áttekintése a PIM-ben

A Azure Active Directory (AD) Privileged Identity Management (PIM) leegyszerűsíti, hogy a vállalatok hogyan kezelhetik az Azure AD-ban és más Microsoft-online szolgáltatások, például az Office 365-ban vagy a-Microsoft Intune lévő erőforrásokhoz való  

Ha rendszergazdai szerepkörhöz van rendelve, a szervezet Kiemelt szerepkörű rendszergazdája megkérheti, hogy rendszeresen erősítse meg, hogy továbbra is szüksége van erre a szerepkörre a feladathoz. Előfordulhat, hogy olyan e-mailt kap, amely hivatkozást tartalmaz, vagy közvetlenül a [Azure Portal](https://portal.azure.com). A cikk lépéseit követve végezze el a hozzárendelt szerepkörök önértékelését.

Ha a hozzáférési felülvizsgálatok érdeklik a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazda, akkor további részleteket tudhat meg a [hozzáférési felülvizsgálat elindításáról](pim-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>A Privileged Identity Management alkalmazás felvétele
A felülvizsgálat végrehajtásához használhatja a [Azure Portal](https://portal.azure.com/) Azure ad PRIVILEGED Identity Management (PIM) alkalmazást.  Ha nem rendelkezik a Azure AD Privileged Identity Management alkalmazással a portálon, kövesse az alábbi lépéseket a kezdéshez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki a felhasználónevet a Azure Portal jobb felső sarkában, és válassza ki azt a könyvtárat, ahol működni fog.
3. Válassza a **Minden szolgáltatás** lehetőséget, és a Szűrő szövegmezővel keresse meg az **Azure AD Privileged Identity Management** elemet.
4. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.

## <a name="approve-or-deny-access"></a>Hozzáférés jóváhagyása vagy megtagadása
Ha jóváhagyja vagy megtagadja a hozzáférést, csak azt közli a véleményezővel, hogy továbbra is használja-e ezt a szerepkört. Válassza a **jóváhagyás** lehetőséget, ha a szerepkörben szeretne maradni, **vagy ha** nem szeretné, hogy a hozzáférés többé nem szükséges. Az állapot azonnal nem változik, amíg a felülvizsgáló nem alkalmazza az eredményeket.
Az alábbi lépéseket követve megkeresheti és elvégezheti a hozzáférési felülvizsgálatot:

1. A PIM alkalmazásban válassza az emelt **szintű hozzáférés áttekintése**elemet. Ha rendelkezik függőben lévő hozzáférési felülvizsgálatokkal, azok megjelennek az Azure AD hozzáférési felülvizsgálatok paneljén.
2. Válassza ki a végrehajtani kívánt felülvizsgálatot.
3. Hacsak nem hozta létre a felülvizsgálatot, az egyetlen felhasználóként jelenik meg a felülvizsgálatban. Jelölje be a neve melletti pipa.
4. Válassza a **jóváhagyás** vagy a **Megtagadás**lehetőséget. Előfordulhat, hogy a döntés okát is meg kell adnia az **indoklás** szövegmezőben.  
5. Az **Azure ad-szerepkörök áttekintése** panel bezárásához.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések

- [A saját Azure-erőforrásszerepkörök hozzáférési felülvizsgálatának végrehajtása a PIM szolgáltatásban](pim-resource-roles-perform-access-review.md)
