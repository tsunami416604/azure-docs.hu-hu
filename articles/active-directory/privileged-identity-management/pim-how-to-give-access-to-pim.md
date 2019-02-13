---
title: Hozzáférés biztosítása más rendszergazdák számára a PIM - Azure kezelését |} A Microsoft Docs
description: Megtudhatja, hogyan hozzáférést biztosítani más kezelése az Azure AD Privileged Identity Management (PIM) számára.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/29/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0d8cb77cda97c3141987fa8b0ea39541ace6db3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191735"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Hozzáférés biztosítása más rendszergazdák számára, miközben a PIM kezelése

Az Azure AD Privileged Identity Management (PIM) egy szervezet számára automatikusan lehetővé teszi, hogy a globális rendszergazda szerepkör-hozzárendelések és a PIM számára hozzáférést kaphat. Senki más nem írási hozzáférést kap alapértelmezés szerint, beleértve a más globális rendszergazdákat. Egyéb globális rendszergazdák, biztonsági rendszergazdák és biztonsági olvasók rendelkezik a PIM számára olvasási hozzáférést. A PIM számára hozzáférést, az első felhasználó rendelhet hozzá mások számára a **kiemelt szerepkörű rendszergazda** szerepkör.

> [!NOTE]
> A PIM kezelése az Azure MFA van szükség. A Microsoft-fiókok Azure MFA-kiszolgáló nem lehet regisztrálni, mivel a felhasználó, aki a Microsoft-fiókkal jelentkezik be a PIM férhetnek hozzá.

Ellenőrizze, hogy mindig legalább két felhasználót a kiemelt szerepkörű rendszergazda szerepkörrel, abban az esetben, ha egy felhasználó zárolva van, vagy a fiókot törölték.

## <a name="grant-access-to-manage-pim"></a>Hozzáférést biztosít a PIM kezelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **az Azure AD-címtárbeli szerepkörök**.

1. Kattintson a **szerepkörök**.

    ![A PIM az Azure AD-címtárbeli szerepkörök - szerepkörök](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Kattintson a **kiemelt szerepkörű rendszergazda** szerepkör a tagok lap megnyitásához.

    ![A kiemelt szerepkörű rendszergazda - tagok](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Kattintson a **tag hozzáadása** a felügyelt tagok hozzáadása panel megnyitásához.

1. Kattintson a **tagok kiválasztása** a jelölje be a tagok panel megnyitásához.

    ![A kiemelt szerepkörű rendszergazda – jelölje be a tagok](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Jelöljön ki egy tagot, és kattintson a **kiválasztása**.

1. Kattintson a **OK** jogosult tagként a **kiemelt szerepkörű rendszergazda** szerepkör.

    Ha valaki a PIM egy olyan új szerepkör rendeli, azokat automatikusan konfigurálják **jogosult** a szerepkör aktiválását.

1. Adja hozzá az állandó, kattintson a felhasználó a kiemelt szerepkörű rendszergazda taglistára.

1. Kattintson a **további** , majd **állandóvá** , a hozzárendelés véglegesítéséhez.

    ![A kiemelt szerepkörű rendszergazda - állandóvá tétel](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. A felhasználó mutató hivatkozást küld [használatához a PIM](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Távolítsa el a hozzáférést a PIM kezelése

Valaki távolítja el a kiemelt szerepkörű rendszergazda szerepkört, előtt ügyeljen arra, hogy továbbra is lesznek rendelve legalább két felhasználót.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **az Azure AD-címtárbeli szerepkörök**.

1. Kattintson a **szerepkörök**.

1. Kattintson a **kiemelt szerepkörű rendszergazda** szerepkör a tagok lap megnyitásához.

1. El kívánja távolítani, majd kattintson a felhasználó melletti jelölőnégyzet bejelölésekor **tag eltávolítása**.

    ![A kiemelt szerepkörű rendszergazda - tag eltávolítása](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Jelenik meg, ha azt szeretné, a tag eltávolítása a szerepkörből, amely rákérdez, hogy az üzenetben kattintson **Igen**.

## <a name="next-steps"></a>További lépések

- [Ismerkedés a PIM-mel](pim-getting-started.md)
