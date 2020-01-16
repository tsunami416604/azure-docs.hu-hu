---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: fd1a7f133c5719873133554fc2292e94e6fe26a4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980328"
---
1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **Recovery Services** szolgáltatásba.
2. Kattintson **az erőforrás létrehozása** > **monitoring és felügyelet** > **biztonsági mentés és site Recovery**elemre.
3. A **Név** mezőben adja meg a tárolót azonosító rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
4. [Hozzon létre egy erőforráscsoportot](../articles/azure-resource-manager/templates/deploy-portal.md), vagy válasszon ki egy meglévőt. Válassza ki a kívánt Azure-régiót. 
5. Ha gyors hozzáférést szeretne a tárolóhoz az irányítópultról, kattintson a **Rögzítés az irányítópulton** > **Létrehozás** elemre.

   ![Új tároló](./media/site-recovery-create-vault/new-vault-settings.png)

   Az új tároló megjelenik az **Irányítópult** > **Minden erőforrás** menüben, illetve a központi **Recovery Services-tárolók** oldalon.
