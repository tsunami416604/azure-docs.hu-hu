---
title: Az Azure Resource Manager vCPU kvóta növelése kérelmek |} Microsoft Docs
description: Az Azure Resource Manager vCPU kvóta növelését kérelmek
author: ganganarayanan
ms.author: gangan
ms.date: 1/18/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c22a6dde0067385a1bf8d889cc76178bb44dd0ac
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Erőforrás-kezelő vCPU kvóta növelését kérelmek

Erőforrás-kezelő vCPU kvóták a régió szint és az SKU termékcsalád szintjén lépnek érvénybe.
További tudnivalók a módját a kvóták kényszeríti ki a [Azure-előfizetés és a szolgáltatásra vonatkozó korlátozások](http://aka.ms/quotalimits) lap.
További információt a Termékváltozat-családok, előfordulhat, hogy összehasonlítja költségeket és a teljesítmény a a [Virtual Machines díjszabása](http://aka.ms/pricingcompute) lap.

A korlátozás megnövelésére hozzon létre Vcpu kvóta támogatási esetet az Azure-portálon [ https://portal.azure.com ](https://portal.azure.com).

> [!NOTE]
> Megtudhatja, hogyan [hozzon létre egy támogatási kérést](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) az Azure-portálon

1. Új támogatási kérelem lapján válassza ki a probléma típusú, mint "Kvóta" és "Magok" kvóta típus lehet.

    ![Kvóta alapvető beállítások panel](./media/resource-manager-core-quotas-request/Basics-blade.png)

2. Válassza ki az üzembe helyezési modellel, mint a "Erőforrás-kezelő", és jelöljön ki egy helyet.

    ![Kvóta probléma panel](./media/resource-manager-core-quotas-request/Problem-step.png)

3. Válassza ki a Termékváltozat családok igénylő növelését.

    ![Kiválasztott Termékváltozat-sorozat](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. Írja be az új korlátai által megszabott szeretné az előfizetést.

    ![Új kvótakérelemhez Termékváltozat](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- Távolíthatja el, törölje a jelet a Termékváltozat az SKU-család legördülő menüből, vagy kattintson az "x" elvetési ikonra.
A kívánt kvóta megadása minden SKU-család, után kattintson a "Tovább" gombra, a támogatási kérelem létrehozása folytatásához probléma lépés oldalon.
