---
title: Hozzáférés biztosítása a PIM kezeléséhez – Azure Active Directory | Microsoft Docs
description: Megtudhatja, hogyan biztosíthat hozzáférést más adminisztrációs szolgáltatásokhoz Azure AD Privileged Identity Management (PIM) kezeléséhez.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c17847546ace558d367aed6d935db0fed6d817f9
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84742198"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>Hozzáférés biztosítása más rendszergazdáknak a Privileged Identity Management kezeléséhez

A szervezet Privileged Identity Management (PIM) szolgáltatását engedélyező globális rendszergazda automatikusan beolvassa a szerepkör-hozzárendeléseket, és hozzáférést biztosít Privileged Identity Managementhoz. A Azure Active Directory-(Azure AD-) szervezetben senki más nem kap írási hozzáférést alapértelmezés szerint, beleértve a többi globális rendszergazdát is. A többi globális rendszergazda, biztonsági rendszergazda és biztonsági olvasó csak olvasási hozzáféréssel rendelkezik a Privileged Identity Managementhoz. A Privileged Identity Managementhoz való hozzáférés biztosítása érdekében az első felhasználó hozzárendelheti másokat a **Kiemelt szerepkörű rendszergazda** szerepkörhöz.

> [!NOTE]
> A Privileged Identity Management kezeléséhez Azure Multi-Factor Authentication szükséges. Mivel a Microsoft-fiókok nem regisztrálhatnak az Azure Multi-Factor Authenticationre, a Microsoft-fiókba bejelentkező felhasználók nem férhetnek hozzá Privileged Identity Managementhoz.

Győződjön meg arról, hogy mindig van legalább két felhasználó egy kiemelt szerepkörű rendszergazda szerepkörben, ha az egyik felhasználó ki van zárva, vagy a fiókja törölve lett.

## <a name="grant-access-to-manage-pim"></a>Hozzáférés biztosítása a PIM kezeléséhez

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Az Azure AD-ben nyissa meg **Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök**elemet.

1. Válassza a **szerepkörök**lehetőséget.

    ![Azure AD-szerepkörök Privileged Identity Management – szerepkörök](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Válassza ki a **Kiemelt szerepkörű rendszergazda** szerepkört a tagok lap megnyitásához.

    ![Kiemelt szerepkörű rendszergazda – tagok](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Válassza a **tag hozzáadása** elemet a felügyelt Tagok hozzáadása ablaktábla megnyitásához.

1. Válassza a **Tagok kiválasztása** lehetőséget a tagok kiválasztása panel megnyitásához.

    ![Kiemelt szerepkörű rendszergazda – tagok kiválasztása](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Válasszon ki egy tagot, majd kattintson a **kiválasztás**elemre.

1. Kattintson az **OK** gombra, hogy a tag jogosult legyen a **Kiemelt szerepkörű rendszergazda** szerepkörre.

    Ha Privileged Identity Management-ben valakihöz rendel hozzá egy új szerepkört, a rendszer automatikusan **jogosultként** konfigurálja őket a szerepkör aktiválásához.

1. Ahhoz, hogy a tag állandó legyen, válassza ki a felhasználót a Kiemelt szerepkörű rendszergazda tagok listájában.

1. Válassza a **továbbiak** , majd a **végleges** lehetőséget, hogy a hozzárendelés állandó legyen.

    ![Kiemelt szerepkörű rendszergazda – állandó](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Küldje el a felhasználónak egy hivatkozást az [Privileged Identity Management használatának megkezdéséhez](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>A PIM kezelése hozzáférésének eltávolítása

Mielőtt eltávolít valakit a Kiemelt szerepkörű rendszergazda szerepkörből, mindig győződjön meg arról, hogy még legalább két felhasználó van hozzárendelve.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök**elemet.

1. Válassza a **szerepkörök**lehetőséget.

1. Válassza ki a **Kiemelt szerepkörű rendszergazda** szerepkört a tagok lap megnyitásához.

1. Jelölje be az eltávolítani kívánt felhasználó melletti jelölőnégyzetet, majd válassza a **tag eltávolítása**lehetőséget.

    ![Kiemelt szerepkörű rendszergazda – tag eltávolítása](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Ha a rendszer arra kéri, hogy erősítse meg, hogy el kívánja távolítani a tagot a szerepkörből, válassza az **Igen**lehetőséget.

## <a name="next-steps"></a>További lépések

- [A Privileged Identity Management használatának első lépései](pim-getting-started.md)
