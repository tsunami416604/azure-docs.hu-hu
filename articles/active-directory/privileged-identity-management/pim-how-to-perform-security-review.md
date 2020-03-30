---
title: Az Azure AD-szerepkörökhöz való hozzáférés áttekintése a PIM-ben – Azure AD | Microsoft dokumentumok
description: Megtudhatja, hogyan tekintheti meg az Azure Active Directory-szerepkörök hozzáférését az Azure AD emelt szintű identitáskezelés (PIM) szolgáltatásában.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76eccb5d62b68865b7a117312be62753f203e2cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847090"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Hozzáférés áttekintése az Azure AD-szerepkörökhöz a kiemelt identitáskezelésben

A kiemelt identitáskezelés (PIM) leegyszerűsíti, hogy a vállalatok hogyan kezeljék az Azure Active Directory (AD) és más Microsoft online szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásokhoz való kiemelt hozzáférését. A cikkben ismertetett lépéseket követve sikeresen ellenőrizheti önmagát a hozzárendelt szerepkörökben.

Ha rendszergazdai szerepkörhöz van hozzárendelve, a szervezet kiemelt szerepkör-rendszergazdája rendszeresen kérheti, hogy erősítse meg, hogy továbbra is szüksége van a szerepkörre a feladatához. Előfordulhat, hogy kap egy e-mailt, amely tartalmaz egy hivatkozást, vagy mehetsz egyenesen az [Azure Portalon,](https://portal.azure.com) és kezdődik.

Ha Kiemelt szerepszintű rendszergazda vagy globális rendszergazda, érdeklődik a hozzáférési felülvizsgálatok iránt, további részleteket a [Hozzáférés-felülvizsgálat indítása](pim-how-to-start-security-review.md)című részben talál.

## <a name="add-a-pim-dashboard-tile"></a>PIM-irányítópult-csempe hozzáadása

Ha az Azure AD kiemelt identitáskezelési szolgáltatás nem rögzítve az irányítópulton az Azure Portalon, kövesse az alábbi lépéseket az első lépésekhez.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Válassza ki a felhasználónevét az Azure Portal jobb felső sarkában, és válassza ki azt a könyvtárat, amelyet működtetni fog.
3. Válassza a **Minden szolgáltatás** lehetőséget, és a Szűrő szövegmezővel keresse meg az **Azure AD Privileged Identity Management** elemet.
4. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.

## <a name="approve-or-deny-access"></a>Hozzáférés jóváhagyása vagy megtagadása

Amikor jóváhagyja vagy megtagadja a hozzáférést, csak azt mondja a véleményezőnek, hogy továbbra is használja-e ezt a szerepkört vagy sem. Válassza **a Jóváhagyás** lehetőséget, ha a szerepkörben szeretne maradni, vagy a Megtagadás **lehetőséget,** ha már nincs szüksége a hozzáférésre. Az állapota nem változik azonnal, amíg a véleményező nem alkalmazza az eredményeket.
A hozzáférési felülvizsgálat megkereséséhez és befejezéséhez kövesse az alábbi lépéseket:

1. A Kiemelt identitáskezelés szolgáltatásban válassza a **Bizalmas hozzáférés áttekintése**lehetőséget. Ha van függőben lévő hozzáférési felülvizsgálatok, azok megjelennek az Azure AD **Access-értékelések** oldalon.
2. Jelölje ki a végrehajtani kívánt értékelést.
3. Ha nem ön hozta létre az értékelést, ön jelenik meg az ellenőrzés egyetlen felhasználójaként. Jelölje be a neve melletti pipát.
4. Válassza a **Jóváhagyás** vagy **a Megtagadás**lehetőséget. Előfordulhat, hogy a döntés okát meg kell adnia a **Ok beviteli** mezőben.  
5. Zárja be az **Azure AD-szerepkörök áttekintése** panel.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések

- [A saját Azure-erőforrásszerepkörök hozzáférési felülvizsgálatának végrehajtása a PIM szolgáltatásban](pim-resource-roles-perform-access-review.md)
