---
title: Hálózati korlát emelése | Microsoft dokumentumok
description: Hálózatkezelési korlát növelése
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9b5c7043b06172c2d4931ca1c3fd3ac5d0e80883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547779"
---
# <a name="networking-limit-increase"></a>Hálózatkezelési korlát növelése

Az [Azure Portal](https://portal.azure.com) használatával növelheti a hálózati kvótát.

Az aktuális hálózati használat és kvóta megtekintéséhez nyissa meg az előfizetést, majd válassza **a Használat + kvóták**lehetőséget. Az alábbi lehetőségek segítségével megtekintheti a hálózat használati és korlátait.

* [Használati CLI](/cli/azure/network#az-network-list-usages)
* [Powershell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [A hálózati használati API](/rest/api/virtualnetwork/virtualnetworks/listusage)

A **súgó + támogatás** vagy a **portál használat + kvóták** szolgáltatásával növelhető.

> [!Note]
> A **nyilvános IP-előtagok**alapértelmezett méretének módosításához válassza a legördülő listából a **Min Public IP InterNetwork előtag hossza** elemet.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Hálózati kvóta növelésének kérése előfizetési szinten a Súgó + támogatás használatával

Kövesse az alábbi utasításokat, hogy hozzon létre egy támogatási kérelmet az Azure **Portalsúgó + támogatás** használatával.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), majd válassza a **Súgó + támogatás** lehetőséget az Azure Portal menüjében, vagy keresse meg a Súgó + **támogatás**lehetőséget.

    ![Súgó + támogatás](./media/networking-quota-request/help-plus-support.png)

1. Válassza **az Új támogatási kérelem lehetőséget.**

    ![Új támogatási kérelem](./media/networking-quota-request/new-support-request.png)

1. A **Probléma típus mezőben**válassza **a Szolgáltatás- és előfizetési korlátok (kvóták)** lehetőséget.

    ![Előfizetési korlátok kiválasztása a problématípus legördülő menüből](./media/networking-quota-request/select-quota-issue-type.png)

1. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

    ![Előfizetési újsr kiválasztása](./media/networking-quota-request/select-subscription-support-request.png)

1. A **Kvótatípus csoportban**válassza a **Hálózat lehetőséget.** Válassza a **Tovább gombot: Megoldások**.

    ![Kvótatípus kiválasztása](./media/networking-quota-request/select-quota-type-network.png)

1. A PROBLÉMA RÉSZLETEI (Probléma **részletei)** területen válassza **a Részletek megadását** és további információk kitöltését a kérés feldolgozásához.

    ![Részletek megadása](./media/networking-quota-request/provide-details-link.png)

1. A **Kvóta részletei** panelen válasszon ki egy központi telepítési modellt, egy helyet és a kérelemben szerepeljen erőforrásokat.

    ![Kvóta részletei DM](./media/networking-quota-request/quota-details-network.png)

1. Adja meg az előfizetésben az új korlátokat. Sor eltávolításához szüntesse meg az erőforrás kijelölésének megszüntetését az **Erőforrások** menüből, vagy válassza az "x" elvetése ikont. Az egyes erőforrások kvótájának megadása után válassza a Mentés gombot, **és folytassa** a támogatási kérelem létrehozásának folytatását.

    ![Új korlátok](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Hálózati kvótanövekedés kérése előfizetési szinten a Használat + kvóták használatával

Kövesse ezeket az utasításokat, hogy hozzon létre egy támogatási kérelmet az Azure Portalon a **Használat + kvóta** használatával.

1. A https://portal.azure.comalkalmazásban keresse meg és válassza **az Előfizetések**lehetőséget.

    ![Előfizetések](./media/networking-quota-request/search-for-suscriptions.png)

1. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

    ![Előfizetés kiválasztása](./media/networking-quota-request/select-subscription-change-quota.png)

1. **Felhasználás + kvóták** kiválasztása

    ![Használat és kvóták kiválasztása](./media/networking-quota-request/select-usage-plus-quotas.png)

1. A jobb felső sarokban válassza a **Kérelem növelése lehetőséget.**

    ![Emelés kérése](./media/networking-quota-request/request-increase-from-subscription.png)

1. Kövesse a [3.](#request-networking-quota-increase-at-subscription-level-using-help--support)

## <a name="about-networking-limits"></a>Hálózati korlátok – ismeret

Ha többet szeretne megtudni a hálózati korlátokról, olvassa el a korlátok lap [Hálózati szakaszát](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) vagy a Hálózati korlátok gyIK-et.
