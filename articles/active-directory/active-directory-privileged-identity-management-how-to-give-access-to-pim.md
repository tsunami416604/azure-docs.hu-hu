---
title: Zónázással biztosítson hozzáférést a Privileged Identity Management - Azure hogyan |} Microsoft Docs
description: 'Útmutató: a szerepkörök hozzáadása az Azure Active Directory Privileged Identity Management bővítmény rendelkező felhasználók részére, így a PIM kezelésére.'
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 150f994aedfc8d2847a0741792c32114e1b520fe
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233546"
---
# <a name="giving-access-to-manage-azure-ad-privileged-identity-management"></a>Adjon hozzáférést a Azure AD Privileged Identity Management kezelésére
A globális rendszergazda Azure AD Privileged Identity Management (PIM) egy olyan szervezet automatikusan lehetővé teszi, hogy le a szerepkör-hozzárendelések és a PIM elérésére. Más írási hozzáféréssel alapértelmezés szerint kap, azonban más globális rendszergazdákat is. Más globális rendszergazdák, biztonsági rendszergazdák és biztonsági olvasók rendelkezik az Azure AD PIM olvasási hozzáférést. A hozzáférésének PIM, az első felhasználó rendelhet másoknak a **kiemelt szerepkörű rendszergazda** szerepkör. Ehhez a hozzárendeléshez maga PIM belül kell végrehajtani, és a PowerShell vagy más portálokon keresztül nem módosítható.

> [!NOTE]
> Az Azure MFA kezelése az Azure AD PIM van szükség. Microsoft-fiókok az Azure MFA nem regisztrálható, mert a Microsoft-fiókkal jelentkezik be egy felhasználó nem fér hozzá a Azure AD PIM.
> 
> 

Győződjön meg arról, hogy mindig legalább két felhasználók egy kiemelt szerepkörű rendszergazda, abban az esetben, ha egy felhasználó ki van zárva, vagy a fiókot törölték.

## <a name="give-another-user-access-to-manage-pim"></a>Egy másik felhasználó hozzáférést PIM kezelése
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) válassza ki a **Azure AD Privileged Identity Management** alkalmazást az irányítópulton.
2. Válassza ki **kiemelt szerepköröket kezelése** > **kiemelt szerepkörű rendszergazda** > **Hozzáadás**.
   
    ![Adja hozzá a kiemelt szerepkörű rendszergazda – képernyőkép][1]
3. A felügyelt felhasználók hozzáadása paneljét 1. lépés már befejeződött. Válassza ki a 2 **válasszon ki egy felhasználót** és keresse meg a hozzáadni kívánt felhasználót.
   
    ![Válassza ki a felhasználók – képernyőkép][2]
4. Jelölje ki a felhasználó a keresési eredmények között, és kattintson **végzett**.
5. Kattintson a **OK** a mentéshez. A kiválasztott felhasználó megjelenik a kiemelt szerepkörű rendszergazdák listáját.
   
   * Amikor egy új szerepkör hozzárendelése valakinek, azok automatikusan be vannak állítva, jogosult a szerepkör aktiválásához. Ha engedélyezni szeretné azokat a szerepkör állandó, kattintson a felhasználónévre a listában. Válassza ki **perm ellenőrizze** a felhasználói adatokat menüben.
6. A felhasználó mutató hivatkozást küld [Ismerkedés az Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).

## <a name="remove-another-users-access-rights-for-managing-pim"></a>Egy másik felhasználói hozzáférési jogosultságokat PIM kezelésére szolgáló eltávolítása
Mielőtt törli valakit a kiemelt szerepkörű rendszergazda, ügyeljen arra, hogy még mindig lesz két hozzárendelve felhasználók.

1. A PIM irányítópulton kattintson a szerepkör **kiemelt szerepkörű rendszergazda**.  Jelenleg a szerepet betöltő felhasználók listája jelenik meg.
2. Kattintson a felhasználót a felhasználói listáról.
3. Kattintson a **eltávolítása**.  Egy megerősítő üzenet jelenik meg.
4. Kattintson a **Igen** eltávolítása a felhasználói szerepkört.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
