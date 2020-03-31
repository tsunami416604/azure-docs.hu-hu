---
title: Hozzáférés a PIM kezeléséhez - Azure Active Directory | Microsoft dokumentumok
description: Ismerje meg, hogyan adhat hozzáférést más felügyeleti azure AD kiemelt identitáskezelés (PIM) kezeléséhez.
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
ms.openlocfilehash: 7424e92f8520d13137b6ac8787523095058a005f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022115"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>Hozzáférés megadása más rendszergazdáknak a kiemelt identitáskezelés kezeléséhez

A globális rendszergazda, aki lehetővé teszi a kiemelt identitáskezelés (PIM) a szervezet automatikusan megkapja a szerepkör-hozzárendelések és a kiemelt identitáskezelés. Az Azure Active Directory (Azure AD) szervezetben azonban senki más nem kap írási hozzáférést, beleértve más globális rendszergazdákat is. Más globális rendszergazdák, biztonsági rendszergazdák és biztonsági olvasók írásvédett identitáskezelés hez férhetnek hozzá. A kiemelt identitáskezelés hez való hozzáférés engedélyezéséhez az első felhasználó másokat rendelhet a **kiemelt szerepkör-kezelési** szerepkörhöz.

> [!NOTE]
> A kiemelt identitáskezelés kezeléséhez az Azure többtényezős hitelesítése szükséges. Mivel a Microsoft-fiókok nem regisztrálhatnak az Azure többtényezős hitelesítéshez, a Microsoft-fiókkal bejelentkező felhasználó nem férhet hozzá a Kiemelt identitáskezeléshez.

Győződjön meg arról, hogy mindig legalább két felhasználó van egy emelt szintű szerepkör-rendszergazdai szerepkörben, ha egy felhasználó zárolva van, vagy a fiók törlődik.

## <a name="grant-access-to-manage-pim"></a>Hozzáférés a PIM kezeléséhez

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Az Azure AD-ben nyissa **meg a kiemelt identitáskezelés.**

1. Válassza az **Azure AD-szerepkörök lehetőséget.**

1. Válassza a **Szerepkörök lehetőséget.**

    ![Kiemelt identitáskezelés Azure AD-szerepkörök – szerepkörök](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. A tagok lap megnyitásához válassza **a Kiemelt szerepkör-rendszergazda** szerepkört.

    ![Kiemelt szerep-rendszergazda – tagok](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. A **Tag hozzáadása** lehetőséget választva nyissa meg a Felügyelt tagok hozzáadása ablaktáblát.

1. Válassza **a Tagok kijelölése lehetőséget** a Tagok kijelölése ablaktábla megnyitásához.

    ![Kiemelt szerep-rendszergazda – Tagok kiválasztása](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Jelöljön ki egy tagot, majd kattintson **a Kijelölés gombra.**

1. Válassza **az OK lehetőséget,** ha a tagot jogosulttá szeretné tenni a **kiemelt szerepkör-rendszergazdai** szerepkörre.

    Amikor új szerepkört rendel valakihez a Kiemelt identitáskezelésben, azok automatikusan **konfigurálva jogosultak** a szerepkör aktiválásához.

1. Ha állandóvá szeretné tenni a tagot, jelölje ki a felhasználót a Kiemelt szerepkör-rendszergazda i taglistában.

1. Válassza az **Egyebek,** majd az **Állandóvá tétel** lehetőséget a hozzárendelés véglegessé tételéhez.

    ![Kiemelt szerep-rendszergazda – Állandóvá tétel](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Küldjön a felhasználónak egy hivatkozást a [Kiemelt identitáskezelés használatának megkezdéséhez.](pim-getting-started.md)

## <a name="remove-access-to-manage-pim"></a>Hozzáférés eltávolítása a PIM kezeléséhez

Mielőtt eltávolítana valakit a Kiemelt szerepkör-rendszergazda szerepkörből, mindig győződjön meg arról, hogy még mindig legalább két felhasználó van hozzárendelve.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure AD-szerepkörök lehetőséget.**

1. Válassza a **Szerepkörök lehetőséget.**

1. A tagok lap megnyitásához válassza **a Kiemelt szerepkör-rendszergazda** szerepkört.

1. Jelölje be az eltávolítani kívánt felhasználó melletti jelölőnégyzetet, majd válassza **a Tag eltávolítása lehetőséget.**

    ![Kiemelt szerep-rendszergazda – Tag eltávolítása](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Amikor a rendszer arra kéri, hogy erősítse meg, hogy el szeretné távolítani a tagot a szerepkörből, válassza az **Igen**lehetőséget.

## <a name="next-steps"></a>További lépések

- [A Privileged Identity Management használatának első lépései](pim-getting-started.md)
