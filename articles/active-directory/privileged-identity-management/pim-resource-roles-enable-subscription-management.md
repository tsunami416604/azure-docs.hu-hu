---
title: Privileged Identity Management az Azure-erőforrások - előfizetés-kezelés engedélyezése |} Microsoft Docs
description: Ismerje meg, hogyan a globális rendszergazdák kezelhetik a bérlői előfizetések.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/27/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4c6ae3da34fe5157314b8ea422591f7ecbd2a667
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="enable-subscription-management"></a>Előfizetés-kezelés engedélyezése

A címtár globális rendszergazdaként akkor előfordulhat, hogy nincs hozzáférése alapértelmezett összes előfizetéshez kapcsolódó erőforrásokat az Ön bérelt szolgáltatásának. Ez a cikk a saját kezűleg hozzáférésének a bérlő és a fennmaradó megfelelő biztonsági vezérlőket hozzáférés fogadása után a szervezet megköveteli az ajánlott módszer az összes előfizetést a lépéseket fogja szerkezeti.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>Ki engedélyezheti a címtár-előfizetések kezelése?

Minden felhasználó, a globális rendszergazda szerepkörrel kell az alábbi lépésekkel előfizetés-kezelés engedélyezéséhez. Miután engedélyezte az előfizetés-kezelés szolgáltatást, hozzáadhat más globális rendszergazdákat, amelyek esetleg, valamint az erőforrások eléréséhez. Nincs directory beállítás, amely lehetővé teszi a globális rendszergazdai szerepkör összes tagja van.

## <a name="log-on-to-the-azure-portal"></a>Jelentkezzen be az Azure-portálon

Első lépésként jelentkezzen be az Azure portál egy olyan fiókkal, amely a globális rendszergazdai szerepkörrel jogosult vagy aktív tagja. Ha a fiók nem jogosult globális rendszergazda először aktiválnia kell a szerepkört a következő lépéssel lépés előtt.

## <a name="access-the-azure-ad-admin-center"></a>Hozzáférés az Azure AD felügyeleti központban

Most, hogy az Azure-portál globális rendszergazdaként van bejelentkezve, amely az Azure-előfizetésekhez hozzáférést biztosít beállításainak szerkesztésével. Keresse meg az Azure AD felügyeleti központot, keresse meg és válassza a Tulajdonságok lapot a bal oldali navigációs.

![](media/azure-pim-resource-rbac/aad_properties.png)

A tulajdonságok listájában váltása a beállítást, "a globális rendszergazdák által kezelhető Azure-előfizetések" című az "Igen".

![](media/azure-pim-resource-rbac/aad_properties_save.png)

## <a name="navigate-to-azure-ad-pim"></a>Keresse meg az Azure AD PIM

Fiókját engedélyezi ezt a beállítást, az automatikusan hozzáadódik a "Felhasználói hozzáférés adminisztrátora" szerepkör minden előfizetés erőforráshoz a bérlőben. Itt navigáljon az Azure AD PIM, és válassza ki a bal oldali navigációs kezelése részében található Azure-erőforrások.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Kezelése és hálózati erőforrások felderítéséhez

Ha a munkahelyén már működik a Azure AD PIM védelme érdekében a rendszergazdák az Azure Active Directoryban látni fogja az előfizetések listáját a panel betöltésekor.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Ha nem jelenik meg minden olyan erőforrásnál, részletek:
>- A globális rendszergazdai szerepköre nem járt le. 
>- A szervezete rendelkezik Azure-előfizetés

![](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Hozzárendelések konfigurálása

Válasszon ki egy előfizetést a listából, és rendelje hozzá a saját kezűleg (vagy felhasználói csoport tagja a) az erőforrás jogosult tulajdonos. 
[További információk szerepkörök hozzárendelése](pim-resource-roles-assign-roles.md).

Ismételje meg ezt a folyamatot az egyes erőforrások, mielőtt továbblép a következő lépéssel.

## <a name="clean-up-standing-access"></a>Állandó hozzáférés tisztítása

Most, hogy a fontos előfizetések jogosult hozzárendeléseinek a szervezetében, törölheti is folyamatos hozzáférést directory tulajdonságai beállítás letiltásával:

![](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>További lépések

[Erőforrások felderítéséhez](pim-resource-roles-discover-resources.md)

[Felhasználóiszerep-beállítások konfigurálása](pim-resource-roles-configure-role-settings.md)








