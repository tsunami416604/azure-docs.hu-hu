---
title: Adja hozzá, vagy az Azure-előfizetések rendszergazdáinak módosításáról |} A Microsoft Docs
description: Ismerteti, hogyan lehet felvenni vagy módosítani Azure-előfizetési rendszergazda szerepköralapú hozzáférés-vezérlés (RBAC) használatával.
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
ms.openlocfilehash: 6cc965f8e775e02e9dec9f610516739a9a2c1936
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56448007"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Adja hozzá, vagy az Azure-előfizetések rendszergazdáinak módosításáról

Az Azure-erőforrások hozzáférésének kezeléséhez megfelelő rendszergazdai szerepkörrel kell rendelkeznie. Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) nevű engedélyezési rendszert közül választhat, több beépített szerepkörrel rendelkező rendelkezik. Ezekkel a szerepkörökkel különféle hatókörökben, például a felügyeleti csoportban, előfizetéshez vagy erőforráscsoporthoz rendelhet hozzá.

A Microsoft azt javasolja, hogy az RBAC-erőforrások hozzáférésének kezelése. Azonban ha továbbra is a klasszikus üzemi modellel és a klasszikus erőforrások kezelése [Azure Service Management PowerShell-modul](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure), használja a hagyományos adminisztrátor kell. 

> [!TIP]
> Használatakor csak az Azure Portalon a klasszikus erőforrásokat felügyelnie, akkor nincs szükség a hagyományos adminisztrátor.

További információkért lásd: [Azure Resource Manager és klasszikus üzembe helyezési](../azure-resource-manager/resource-manager-deployment-model.md) és [klasszikus Azure-előfizetés rendszergazdái](../role-based-access-control/classic-administrators.md).

Ez a cikk azt ismerteti, hogyan hozzáadása vagy módosítása a rendszergazda szerepkör egy felhasználó az RBAC használatával az előfizetések szintjén.

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Az előfizetés rendszergazda felhasználó hozzárendelése

Ahhoz, hogy a felhasználó az Azure-előfizetés rendszergazdájának, hozzárendelheti azokat a [tulajdonosa](../role-based-access-control/built-in-roles.md#owner) szerepkör (RBAC szerepkör) az előfizetések szintjén. A tulajdonosi szerepkör a felhasználó teljes hozzáférést biztosít, az összes erőforrást az előfizetésben, beleértve a jogot arra, hogy mások való hozzáférés delegálására. Ezek a lépések ugyanazok, mint bármilyen más szerepkör-hozzárendelés.

1. Az Azure Portalon nyissa meg a [előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Kattintson arra az előfizetésre, ahol szeretné a hozzáférést.

1. Kattintson a **hozzáférés-vezérlés (IAM)**.

1. Kattintson a **szerepkör-hozzárendelések** fülre kattintva megtekintheti az előfizetéshez tartozó szerepkör-hozzárendelések.

    ![Képernyőkép a szerepkör-hozzárendelések](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. Kattintson a **Hozzáadás** > **szerepkör-hozzárendelés hozzáadása** megnyitásához a **szerepkör-hozzárendelés hozzáadása** ablaktáblán.

    Nem rendelkezik engedélyekkel szerepkörök hozzárendeléséhez, ha a beállítás letiltja.

1. Az a **szerepkör** legördülő listában válassza a **tulajdonosa** szerepkör.

1. Az a **kiválasztása** listában, válasszon ki egy felhasználót. Ha nem látja a felhasználónévre a listában, megadhatja a **kiválasztása** mezőben keressen a könyvtárban, a megjelenített nevek és e-mail-címeket.

    ![A tulajdonosi szerepkörrel, kijelölt bemutató képernyőkép](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Kattintson a **mentése** a szerepkör hozzárendelése.

    Néhány pillanat múlva a felhasználó hozzárendelve a tulajdonosi szerepkör az előfizetések szintjén.

## <a name="next-steps"></a>További lépések

* [Mi az a szerepköralapú hozzáférés-vezérlés (RBAC)?](../role-based-access-control/overview.md)
* [A különböző Azure-beli szerepkörök ismertetése](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Útmutató: Hozzárendelése vagy Azure-előfizetés hozzáadása az Azure Active Directoryhoz](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Az Azure Active Directory rendszergazdája szerepkör engedélyei](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
