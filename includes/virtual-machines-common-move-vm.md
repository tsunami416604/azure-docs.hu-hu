---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/19/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 61990e785741799fcbcd4e6df965953bd9944f4d
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49476397"
---
## <a name="use-the-azure-portal-to-move-a-vm-to-a-different-subscription"></a>Virtuális gép áthelyezése egy másik előfizetésben az Azure portal használatával
Az Azure portal használatával áthelyezheti egy virtuális Gépet, és az összes kapcsolódó erőforrás egy másik előfizetést.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Kattintson a **Tallózás** > **erőforráscsoportok** , és válassza ki az áthelyezni kívánt virtuális gép tartalmazó erőforráscsoportot.
3. Válassza ki az erőforráscsoport az oldal tetején lévő **áthelyezése** , majd **Áttérés másik előfizetésre**. A **erőforrások áthelyezése** lap megnyitásakor.
4. Válassza ki az erőforrások áthelyezése. A legtöbb esetben helyezze át az összes felsorolt kapcsolódó erőforrások.
5. Válassza ki a **előfizetés** ahol azt szeretné, hogy a virtuális gép lesz áthelyezve.
6. Válasszon egy meglévő **erőforráscsoport**, vagy adjon meg egy nevet, hogy létrehozott egy új erőforráscsoportot.
7. Amikor végzett, válassza ki, hogy megértette, hogy új erőforrás-azonosítók jön létre, és, hogy az új azonosítók kell használni a virtuális gép áthelyezése után, és adja meg áll **OK**.

## <a name="use-the-azure-portal-to-move-a-vm-to-another-resource-group"></a>Virtuális gép áthelyezése egy másik erőforráscsoportot az Azure portal használatával
Az Azure portal használatával áthelyezheti egy virtuális Gépet, és az összes kapcsolódó erőforrás egy másik erőforráscsoportot.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Kattintson a **Tallózás** > **virtuális gépek** , és válassza ki a virtuális Gépet szeretne áthelyezni a listából.
3. Válassza ki a lapon a virtuális gép az erőforráscsoportban a címke melletti **módosítás**. A **erőforrások áthelyezése** lap megnyitásakor.
4. Válassza ki az erőforrások áthelyezése. A legtöbb esetben helyezze át az összes felsorolt kapcsolódó erőforrások.
5. Válasszon egy meglévő **erőforráscsoport**, vagy adjon meg egy nevet, hogy létrehozott egy új erőforráscsoportot.
6. Amikor végzett, válassza ki, hogy megértette, hogy új erőforrás-azonosítók jön létre, és, hogy az új azonosítók kell használni a virtuális gép áthelyezése után, és adja meg áll **OK**.

