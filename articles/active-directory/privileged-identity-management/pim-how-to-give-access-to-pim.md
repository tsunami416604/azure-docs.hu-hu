---
title: Hozzáférés biztosítása más rendszergazdák számára a PIM felügyeletéhez – Azure Active Directory | Microsoft Docs
description: Megtudhatja, hogyan biztosíthat hozzáférést más adminisztrációs szolgáltatásokhoz Azure AD Privileged Identity Management (PIM) kezeléséhez.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3a0173108b6c884994ca25fd0495e9cb8d45186
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804361"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Hozzáférés biztosítása más rendszergazdák számára a PIM kezeléséhez

A szervezet Azure Active Directory (Azure AD) Privileged Identity Managementt (PIM) engedélyező globális rendszergazdája automatikusan szerepkör-hozzárendeléseket kap, és hozzáférést biztosít a PIM-hoz. Alapértelmezés szerint senki más nem kap írási hozzáférést, a többi globális rendszergazdát is beleértve. A többi globális rendszergazda, biztonsági rendszergazda és biztonsági olvasó csak olvasási hozzáféréssel rendelkezik a PIM-hez. Ahhoz, hogy hozzáférést biztosítson a PIM-hoz, az első felhasználó hozzárendelheti másokat a **Kiemelt szerepkörű rendszergazda** szerepkörhöz.

> [!NOTE]
> A PIM felügyeletéhez az Azure MFA szükséges. Mivel a Microsoft-fiókok nem regisztrálhatnak az Azure MFA-ra, a Microsoft-fiókba bejelentkező felhasználók nem férhetnek hozzá a PIM szolgáltatáshoz.

Győződjön meg arról, hogy mindig van legalább két felhasználó egy kiemelt szerepkörű rendszergazda szerepkörben, ha az egyik felhasználó ki van zárva, vagy a fiókja törölve lett.

## <a name="grant-access-to-manage-pim"></a>Hozzáférés biztosítása a PIM kezeléséhez

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure ad-szerepkörök**elemre.

1. Kattintson a **szerepkörök**elemre.

    ![PIM Azure AD-szerepkörök – szerepkörök](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Kattintson a **Kiemelt szerepkörű rendszergazda** szerepkörre a tagok lap megnyitásához.

    ![Kiemelt szerepkörű rendszergazda – tagok](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Kattintson a **tag hozzáadása** gombra a felügyelt Tagok hozzáadása ablaktábla megnyitásához.

1. Kattintson a **Tagok kiválasztása** lehetőségre a tagok kiválasztása panel megnyitásához.

    ![Kiemelt szerepkörű rendszergazda – tagok kiválasztása](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Válasszon ki egy tagot, majd kattintson a **kiválasztás**elemre.

1. Kattintson az **OK** gombra, hogy a tag jogosult legyen a **Kiemelt szerepkörű rendszergazda** szerepkörre.

    Amikor új szerepkört rendel valakihez a PIM-ben, a rendszer automatikusan **jogosultként** konfigurálja őket a szerepkör aktiválásához.

1. Ahhoz, hogy a tag állandó legyen, a Kiemelt szerepkörű rendszergazda tagok listájában kattintson a felhasználóra.

1. Kattintson a **továbbiak** , majd a **végleges** lehetőségre a hozzárendelés véglegesvé tételéhez.

    ![Kiemelt szerepkörű rendszergazda – állandó](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Küldje el a felhasználónak egy hivatkozást a [PIM használatának megkezdéséhez](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>A PIM kezelése hozzáférésének eltávolítása

Mielőtt eltávolít valakit a Kiemelt szerepkörű rendszergazda szerepkörből, mindig győződjön meg arról, hogy még legalább két felhasználó van hozzárendelve.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure ad-szerepkörök**elemre.

1. Kattintson a **szerepkörök**elemre.

1. Kattintson a **Kiemelt szerepkörű rendszergazda** szerepkörre a tagok lap megnyitásához.

1. Vegyen fel egy pipát az eltávolítani kívánt felhasználó mellett, majd kattintson a **tag eltávolítása**gombra.

    ![Kiemelt szerepkörű rendszergazda – tag eltávolítása](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. A megjelenő üzenetben megkérdezheti, hogy szeretné-e eltávolítani a tagot a szerepkörből, kattintson az **Igen**gombra.

## <a name="next-steps"></a>További lépések

- [Ismerkedés a PIM-mel](pim-getting-started.md)
