---
title: A „Nincsenek jogosult előfizetések” hiba elhárítása az Azure Portalon
description: Ez a cikk segítséget nyújt a foglalás vásárlására tett kísérletkor megjelenő „Nincsenek jogosult előfizetések” hibának az Azure Portalon történő elhárításához.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.openlocfilehash: 1b36577c3c0940687f98394f8ea4faae83f371be
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887195"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>A „Nincsenek jogosult előfizetések” hiba elhárítása

Ez a cikk segítséget nyújt a foglalás vásárlására tett kísérletkor megjelenő *Nincsenek jogosult előfizetések* hibának az Azure Portalon történő elhárításához.

## <a name="symptoms"></a>Hibajelenségek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és lépjen a **Foglalások** területre.
1. Válassza a **Hozzáadás** lehetőséget, majd válasszon ki egy szolgáltatást.
1. A következő hibaüzenet jelenik meg:
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should have owner or reservation purchaser permission on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. A **Válassza ki a megvásárolni kívánt terméket** területen bontsa ki a **Számlázási előfizetés** listát annak megtekintéséhez, hogy egy adott előfizetés miért nem jogosult fenntartott példány vásárlására. Az alábbi képen példák láthatók arra, hogy miért nem vásárolható meg egy foglalás.  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="Példa arra, miért nem vásárolható meg egy foglalás" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>Ok

Fenntartott Azure-példány vásárlásához rendelkeznie kell legalább egy olyan előfizetéssel, amely megfelel az alábbi követelményeknek:

- Az előfizetésnek támogatott ajánlattípusúnak kell lennie. A támogatott ajánlattípusok: Használatalapú fizetés, Felhőszolgáltató (CSP), Microsoft Azure Enterprise vagy Microsoft Ügyfélszerződés.
- Tulajdonosnak vagy a foglalás megvásárlójának kell lennie az előfizetésre vonatkozóan.

Ha nem rendelkezik a követelményeknek megfelelő előfizetéssel, a `No eligible subscriptions` hibaüzenet jelenik meg.

### <a name="cause-1"></a>1\. ok

Az előfizetésnek támogatott ajánlattípusúnak kell lennie. A támogatott ajánlattípusok: Használatalapú fizetés, CSP, Microsoft Azure Enterprise vagy Microsoft Ügyfélszerződés. Az előfizetés típusa nem tartozik a támogatott típusok közé. Ha olyan előfizetést választ ki, amelynek ajánlattípusa nem támogatja a foglalásokat, az alábbi hibaüzenet jelenik meg.

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="Példa „Az előfizetés nem jogosult vásárlásra” hibaüzenetre" :::

### <a name="cause-2"></a>2\. ok

Tulajdonosnak vagy a foglalás megvásárlójának kell lennie az előfizetésre vonatkozóan. Ha nem rendelkezik megfelelő engedélyekkel, a következő hibaüzenet jelenik meg.

```
You do not have owner or reservation purchaser access on the subscription

You can only purchase reservations using subscriptions on which you have owner or reservation purchaser access.
```

## <a name="solution"></a>Megoldás

- Ha az aktuális ajánlat nem támogatja a foglalásokat, új Azure-előfizetést kell létrehoznia.
- Ha nem rendelkezik hozzáféréssel egy meglévő foglaláshoz, az aktuális tulajdonostól kérhet hozzáférést.

### <a name="solution-1"></a>1\. megoldás

Foglalás vásárlásához létre kell hoznia egy új, a foglalásokat támogató Azure-előfizetést.

- Ha ingyenes Azure-próbaverzióval rendelkezik, [frissítheti az előfizetést](../manage/upgrade-azure-subscription.md).
- Létrehozhat egy új Azure-előfizetést [használatalapú díjszabással](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).
- Regisztráljon [Microsoft Ügyfélszerződésre](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/), és hozzon létre egy új előfizetést.
- Vásároljon új előfizetést egy [CSP-nél](https://www.microsoft.com/solution-providers/home), és hozzon létre egy új előfizetést.

### <a name="solution-2"></a>2\. megoldás

Ha tulajdonosi hozzáféréssel szeretne rendelkezni egy foglaláshoz, hozzáféréssel kell rendelkeznie az alábbiak valamelyikéhez:

- Ahhoz a foglalási rendeléshez, amellyel a foglalást megvásárolták;
- Magához a foglaláshoz.

Az aktuális foglalási rendelés tulajdonosa vagy a foglalás tulajdonosa az alábbi lépésekkel delegálhat Önnek hozzáférést.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza a **Minden szolgáltatás** > **Foglalások** lehetőséget azoknak a foglalásoknak a megtekintéséhez, amelyekhez hozzáféréssel rendelkezik.
1. Válassza ki azt a foglalást, amelyhez való hozzáférést delegálni szeretné más felhasználóknak.
1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
1. Válassza a **Szerepkör-hozzárendelés hozzáadása** > **Szerepkör** > **Tulajdonos** lehetőséget. Ha korlátozott hozzáférést szeretne biztosítani, válasszon egy másik szerepkört.
1. Írja be annak a felhasználónak az e-mail-címét, akit tulajdonosként kíván hozzáadni.
1. Válassza ki a felhasználót, majd válassza a **Mentés** lehetőséget.

További információk [a foglalást kezelő felhasználók hozzáadásáról vagy módosításáról](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="next-steps"></a>Következő lépések

- Tekintse meg [A foglalást kezelő felhasználók hozzáadása vagy módosítása](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation) című szakaszt, ha a foglalás tulajdonosától szeretne hozzáférést kérni.
