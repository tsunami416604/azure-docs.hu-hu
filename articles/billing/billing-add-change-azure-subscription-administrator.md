---
title: Azure-előfizetés-adminisztrátorok hozzáadása vagy módosítása | Microsoft Docs
description: A cikk azt ismerteti, hogyan végezheti el az Azure-előfizetés-adminisztrátorok hozzáadását vagy módosítását szerepköralapú hozzáférés-vezérléssel (RBAC).
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 000315a2d751a05d3e401ee1bb9f593c6e321194
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "64922900"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Azure-előfizetés-adminisztrátorok hozzáadása vagy módosítása

Az Azure-erőforrások hozzáférésének kezeléséhez megfelelő rendszergazdai szerepkörrel kell rendelkeznie. Az Azure egy szerepköralapú hozzáférés-vezérlés (RBAC) nevű engedélyezési rendszert használ, amelyben számos beépített szerepkör közül választhat. A szerepkörök hozzárendelését különböző hatókörök (például felügyeleti csoport, előfizetés vagy erőforráscsoport) szerint is elvégezheti.

A Microsoft azt javasolja, hogy az erőforrásokhoz való hozzáférést az RBAC segítségével szabályozza. Azonban, ha továbbra is a klasszikus üzembehelyezési modellt használja, és klasszikus erőforrásokat felügyel az [Azure szolgáltatásfelügyeleti PowerShell-moduljával](https://docs.microsoft.com/powershell/module/servicemanagement/azure), egy klasszikus adminisztrátorra lesz szüksége. 

> [!TIP]
> Amennyiben kizárólag az Azure Portalon keresztül végzi a klasszikus erőforrások felügyeletét, nem szükséges klasszikus adminisztrátort használni.

További információt [az Azure Resource Manager-alapú és a klasszikus üzemelő példányok összehasonlításában](../azure-resource-manager/resource-manager-deployment-model.md) és az [Azure klasszikus előfizetés-adminisztrátorok](../role-based-access-control/classic-administrators.md) ismertetésében talál.

Ez a cikk azt ismerteti, hogyan adhatja hozzá az adminisztrátori szerepkört egy felhasználóhoz, illetve módosíthatja azt később az RBAC használatával az előfizetés hatókörében.

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Felhasználó hozzáadása egy előfizetés adminisztrátoraként

Ha egy felhasználót egy Azure-előfizetés adminisztrátoraként szeretne hozzáadni, rendelje hozzá a [Tulajdonos](../role-based-access-control/built-in-roles.md#owner) szerepkört (RBAC-szerepkör) az előfizetés hatókörében. A Tulajdonos szerepkör teljes hozzáférést biztosít a felhasználónak az előfizetés összes erőforrásához, beleértve a hozzáférés mások számára való delegálásának jogát is. Ez ugyanúgy történik, mint bármely más szerepkör-hozzárendelés esetében.

1. Az Azure Portalon nyissa meg az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) oldalt.

1. Kattintson arra az előfizetésre, amelynél a hozzáférést biztosítani kívánja.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **Szerepkör-hozzárendelések** lapra az előfizetés szerepkör-hozzárendeléseinek megtekintéséhez.

    ![Képernyőkép a szerepkör-hozzárendelésekről](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. Kattintson a **Hozzáadás** > **Szerepkör-hozzárendelés hozzáadása** lehetőségre a **Szerepkör-hozzárendelés hozzáadása** szakasz megnyitásához.

    Ha nem rendelkezik a szerepkörök hozzárendeléséhez szükséges engedéllyel, a lehetőség nem érhető el.

1. A **Szerepkör** legördülő listában válassza a **Tulajdonos** szerepkört.

1. A **kiválasztási** listában válasszon ki egy felhasználót. Ha a listában nem látja a felhasználót, beírhatja a nevét a **kiválasztási** mezőbe, hogy rákeressen a megjelenítendő neveket és e-mail-címeket tartalmazó címtárban.

    ![A kiválasztott Tulajdonos szerepkört megjelenítő képernyőkép](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

    Néhány pillanat múlva a rendszer hozzárendeli a felhasználót a Tulajdonos szerepkörhöz az előfizetés hatókörében.

## <a name="next-steps"></a>További lépések

* [Mi az a szerepköralapú hozzáférés-vezérlés (RBAC)?](../role-based-access-control/overview.md)
* [A különböző Azure-beli szerepkörök ismertetése](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Útmutató: Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Adminisztrátori szerepkörök engedélyei az Azure Active Directoryban](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha további segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
