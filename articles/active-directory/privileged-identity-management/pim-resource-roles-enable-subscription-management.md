---
title: A privileged Identity Management az Azure-erőforrások - előfizetés-kezelés engedélyezése |} Microsoft Docs
description: Ismerje meg, hogyan globális rendszergazdák kezelhetik a bérlői előfizetések.
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
ms.openlocfilehash: e164d8adaf5df63dba31bb6aa8e56f768741479c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="enable-subscription-management-in-your-tenant"></a>A bérlő előfizetés-kezelés engedélyezése

A címtár globális rendszergazdaként akkor előfordulhat, hogy nincs hozzáférése alapértelmezett összes előfizetéshez kapcsolódó erőforrásokat az Ön bérelt szolgáltatásának. Ez a cikk hozzáférést magát az Ön bérelt szolgáltatásának előfizetéseken lépéseit mutatja be. Egy ajánlott módszer a megfelelő biztonsági vezérlőket a szervezet szükségesek, miután a hozzáférés-re is tartalmazza.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>Ki engedélyezheti a címtár-előfizetések kezelése?

Minden felhasználó, a globális rendszergazda szerepkörrel kell az alábbi lépésekkel előfizetés-kezelés engedélyezéséhez. Miután engedélyezte az előfizetés-kezelés szolgáltatást, más globális rendszergazdákat, akik esetleg erőforrás adhat hozzá, valamint a hozzáférés. Található könyvtárat, amely lehetővé teszi a globális rendszergazdai szerepkör összes tagja számára.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure portál egy olyan fiókkal, amely a globális rendszergazdai szerepkörrel jogosult vagy aktív tagja. A fiók nem jogosult globális rendszergazda, ha először aktiválnia kell a szerepkört a következő lépéssel lépés előtt.

## <a name="access-the-azure-active-directory-admin-center"></a>Hozzáférés az Azure Active Directory felügyeleti központban

Most, hogy be van jelentkezve az Azure-portál globális rendszergazdaként, módosíthatja az Azure-előfizetések elérését lehetővé tevő beállítások. Keresse meg az Azure Active Directory (Azure AD) felügyeleti központot, és válassza ki **tulajdonságok**.

![Képernyőfelvétel az Azure AD felügyeleti központba, a kijelölt tulajdonságok](media/azure-pim-resource-rbac/aad_properties.png)

A tulajdonságok közül a **globális rendszergazdák által kezelhető az Azure-előfizetések**, jelölje be **Igen**.

![Képernyőfelvétel a tulajdonságlapot, Igen váltása](media/azure-pim-resource-rbac/aad_properties_save.png)

Fiókja most már automatikusan hozzáadódik a felhasználói hozzáférés rendszergazdai szerepkör minden előfizetés erőforráshoz a bérlőben.

## <a name="browse-to-azure-ad-pim"></a>Keresse meg az Azure AD PIM

 Itt nyissa meg az Azure AD Privileged Identity Management (PIM). A **kezelése**, jelölje be **Azure-erőforrások**.

![Képernyőfelvétel a PIM, a kijelölt Azure-erőforrások](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Kezelése és hálózati erőforrások felderítéséhez

Ha a szervezet már használja a Azure AD PIM védelme érdekében a rendszergazdák az Azure ad-ben, az előfizetések listáját láthatja, a panel betöltésekor.

![Képernyőfelvétel a PIM, a panel látható előfizetések listája](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Ha nem jelenik meg minden olyan erőforrásnál, ellenőrizze, hogy:
>- A globális rendszergazdai szerepköre nem járt le. 
>- A szervezet Azure-előfizetéssel rendelkezik.

![Képernyőfelvétel a PIM, az üres erőforrások listája](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Hozzárendelések konfigurálása

Válasszon ki egy előfizetést a listából, és rendelje hozzá a saját kezűleg (vagy felhasználói csoport tagja a) az erőforrás jogosult tulajdonos. 
[További információk szerepkörök hozzárendelése](pim-resource-roles-assign-roles.md).

Ismételje meg ezt a folyamatot az egyes erőforrások, mielőtt továbblép a következő lépéssel.

## <a name="clean-up-standing-access"></a>Állandó hozzáférés tisztítása

Most, hogy a fontos előfizetések jogosult hozzárendeléseinek a szervezetében, törölheti is folyamatos hozzáférést directory tulajdonságai beállítás letiltásával.

![Képernyőfelvétel a tulajdonságlapot, nem váltása](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>További lépések

[Erőforrások felfedezése](pim-resource-roles-discover-resources.md)

[Szerepkör-beállítások konfigurálása](pim-resource-roles-configure-role-settings.md)








