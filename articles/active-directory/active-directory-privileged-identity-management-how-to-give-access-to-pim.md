---
title: Hozzáférés biztosítása a Privileged Identity Management – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan adja hozzá a szerepkörök a felhasználók számára az Azure Active Directory Privileged Identity Management-bővítménnyel, miközben a PIM képesek kezelni.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 8d52469e11ac508ee8b353311f6040ac9d6f2e4e
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447149"
---
# <a name="giving-access-to-manage-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management kezeléséhez hozzáférés megadását
Az Azure AD Privileged Identity Management (PIM) egy szervezet számára automatikusan lehetővé teszi, hogy a globális rendszergazda szerepkör-hozzárendelések és a PIM számára hozzáférést kaphat. Senki más nem írási hozzáférést kap alapértelmezés szerint, beleértve a más globális rendszergazdákat. Egyéb globális rendszergazdák, biztonsági rendszergazdák és biztonsági olvasók rendelkezik a csak olvasási hozzáféréssel az Azure AD PIM-ben. Hozzáférés biztosítása a PIM számára, az első felhasználó rendelhet hozzá mások számára a **kiemelt szerepkörű rendszergazda** szerepkör.

> [!NOTE]
> Az Azure MFA kezelése az Azure AD PIM-ben van szükség. A Microsoft-fiókok Azure MFA-kiszolgáló nem lehet regisztrálni, mivel a felhasználó, aki a Microsoft-fiókkal jelentkezik be nem érhető el az Azure AD PIM-ben.
> 
> 

Győződjön meg arról, hogy mindig legalább két felhasználót egy kiemelt szerepkörű rendszergazda, abban az esetben, ha egy felhasználó ki van zárva, vagy a fiókot törölték.

## <a name="give-another-user-access-to-manage-pim"></a>Egy másik felhasználói hozzáférésének a PIM kezelése
1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) , és válassza ki a **Azure AD Privileged Identity Management** alkalmazás az irányítópulton.
2. Válassza ki **kiemelt szerepkörök kezelése** > **kiemelt szerepkörű rendszergazda** > **Hozzáadás**.
   
    ![Képernyőkép – a kiemelt szerepkörű rendszergazdák hozzáadása][1]
3. A felügyelt felhasználók hozzáadása paneljét 1. lépés már befejeződött. Válassza a 2. lépés **válassza ki a felhasználók** , és keresse meg a hozzáadni kívánt felhasználó.
   
    ![Válassza ki a felhasználók – képernyőkép][2]
4. A keresési eredmények közül válassza ki a felhasználót, és kattintson a **kész**.
5. Kattintson a **OK** a mentéshez. A kiválasztott felhasználó megjelenik a kiemelt szerepkörű rendszergazdák listáját.
   
   * Minden alkalommal, amikor valaki hozzárendelhet egy új szerepkör, azok automatikusan állíthatók be, jogosult a szerepkör aktiválását. Ha azt szeretné, hogy a szerepkör állandó, kattintson a felhasználónévre a listában. Válassza ki **állandóvá** a felhasználói adatokat menüben.
6. A felhasználó mutató hivatkozást küld [Ismerkedés az Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).

## <a name="remove-another-users-access-rights-for-managing-pim"></a>A PIM felügyeletére egy másik felhasználó hozzáférési jogok eltávolítása
Eltávolítása előtt valaki a kiemelt szerepkörű rendszergazda, ügyeljen arra, hogy továbbra is lesz hozzárendelve két felhasználót.

1. A PIM irányítópultján kattintson a szerepkör **kiemelt szerepkörű rendszergazda**.  Jelenleg az adott szerepkörhöz tartozó felhasználók listája jelenik meg.
2. Kattintson a felhasználó a felhasználói listájában.
3. Kattintson a **eltávolítása**.  Megnyílik egy megerősítő üzenetet.
4. Kattintson a **Igen** a felhasználó eltávolítása a szerepkörből.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
