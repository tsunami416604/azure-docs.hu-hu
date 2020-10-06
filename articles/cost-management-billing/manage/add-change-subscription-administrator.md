---
title: Azure-előfizetés-adminisztrátorok hozzáadása vagy módosítása
description: A cikk azt ismerteti, hogyan végezheti el az Azure-előfizetés-adminisztrátorok hozzáadását vagy módosítását Azure-beli szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 273f12765f60eb1a0d65037e30e33e58b16463bf
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372306"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Azure-előfizetés-adminisztrátorok hozzáadása vagy módosítása


Az Azure-erőforrások hozzáférésének kezeléséhez megfelelő rendszergazdai szerepkörrel kell rendelkeznie. Az Azure az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) nevű engedélyezési rendszert használja, amelyben számos beépített szerepkör közül választhat. A szerepkörök hozzárendelését különböző hatókörök (például felügyeleti csoport, előfizetés vagy erőforráscsoport) szerint is elvégezheti. Alapértelmezés szerint az új Azure-előfizetést létrehozó személy rendelheti hozzá más felhasználókhoz az adminisztratív hozzáférést az előfizetésekhez.

Ez a cikk azt ismerteti, hogyan adhatja hozzá az adminisztrátori szerepkört egy felhasználóhoz, illetve módosíthatja azt később az Azure RBAC használatával az előfizetés hatókörében.

A Microsoft azt javasolja, hogy az erőforrásokhoz való hozzáférést az Azure RBAC segítségével szabályozza. Azonban, ha továbbra is a klasszikus üzembehelyezési modellt használja, és klasszikus erőforrásokat felügyel az [Azure szolgáltatásfelügyeleti PowerShell-moduljával](/powershell/module/servicemanagement/azure.service), egy klasszikus adminisztrátorra lesz szüksége.

> [!TIP]
> Amennyiben kizárólag az Azure Portalon keresztül végzi a klasszikus erőforrások felügyeletét, nem szükséges klasszikus adminisztrátort használni.

További információt [az Azure Resource Manager-alapú és a klasszikus üzemelő példányok összehasonlításában](../../azure-resource-manager/management/deployment-models.md) és az [Azure klasszikus előfizetés-adminisztrátorok](../../role-based-access-control/classic-administrators.md) ismertetésében talál.

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-subscription-administrator"></a>Előfizetés-adminisztrátor hozzárendelése

Ha egy felhasználót egy Azure-előfizetés adminisztrátorává szeretne megtenni, ahhoz a meglévő adminisztrátornak hozzá kell rendelnie a [Tulajdonos](../../role-based-access-control/built-in-roles.md#owner) szerepkört (Azure-szerepkör) az előfizetés hatókörében. A Tulajdonos szerepkör teljes hozzáférést biztosít a felhasználónak az előfizetés összes erőforrásához, beleértve a hozzáférés mások számára való delegálásának jogát is. Ez ugyanúgy történik, mint bármely más szerepkör-hozzárendelés esetében.

Ha nem biztos benne, hogy az adott előfizetésnek ki a fiókadminisztrátora, az alábbi lépésekkel kiderítheti.

1. Nyissa meg [az Azure Portal Előfizetések lapját](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válassza ki az ellenőrizni kívánt előfizetést, majd lépjen a **Beállítások** területre.
1. Válassza ki a **Tulajdonságok** elemet. Az előfizetés fiókadminisztrátora a **Fiókadminisztrátor** mezőben látható.

### <a name="to-assign-a-user-as-an-administrator"></a>Felhasználó adminisztrátorként való hozzáadása

1. Jelentkezzen be az Azure Portalra az előfizetés tulajdonosaként, és nyissa meg az [Előfizetéseket](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Kattintson arra az előfizetésre, amelynél a hozzáférést biztosítani kívánja.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **Szerepkör-hozzárendelések** lapra az előfizetés szerepkör-hozzárendeléseinek megtekintéséhez.

    ![Képernyőkép a szerepkör-hozzárendelésekről](./media/add-change-subscription-administrator/role-assignments.png)

1. Kattintson a **Hozzáadás** > **Szerepkör-hozzárendelés hozzáadása** lehetőségre a **Szerepkör-hozzárendelés hozzáadása** szakasz megnyitásához.

    Ha nem rendelkezik a szerepkörök hozzárendeléséhez szükséges engedéllyel, a lehetőség nem érhető el.

1. A **Szerepkör** legördülő listában válassza a **Tulajdonos** szerepkört.

1. A **kiválasztási** listában válasszon ki egy felhasználót. Ha a listában nem látja a felhasználót, beírhatja a nevét a **kiválasztási** mezőbe, hogy rákeressen a megjelenítendő neveket és e-mail-címeket tartalmazó címtárban.

    ![A kiválasztott Tulajdonos szerepkört megjelenítő képernyőkép](./media/add-change-subscription-administrator/add-role.png)

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

    Néhány pillanat múlva a rendszer hozzárendeli a felhasználót a Tulajdonos szerepkörhöz az előfizetés hatókörében.

## <a name="next-steps"></a>További lépések

* [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md)
* [A különböző Azure-beli szerepkörök ismertetése](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directory-bérlőhöz](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Adminisztrátori szerepkörök engedélyei az Azure Active Directoryban](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha további segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
