---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 62bb01b39f33f801f1eb43d128083cff7e16830a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116037"
---
1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **Recovery Services** szolgáltatásba.
2. Kattintson a **erőforrás létrehozása** > **Monitoring + Management** > **Backup és Site Recovery**.
3. A **Név** mezőben adja meg a tárolót azonosító rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
4. [Hozzon létre egy erőforráscsoportot](../articles/azure-resource-manager/resource-group-template-deploy-portal.md), vagy válasszon ki egy meglévőt. Válassza ki a kívánt Azure-régiót. 
5. Ha gyors hozzáférést szeretne a tárolóhoz az irányítópultról, kattintson a **Rögzítés az irányítópulton** > **Létrehozás** elemre.

   ![Új tároló](./media/site-recovery-create-vault/new-vault-settings.png)

   Az új tároló megjelenik az **Irányítópult** > **Minden erőforrás** menüben, illetve a központi **Recovery Services-tárolók** oldalon.
