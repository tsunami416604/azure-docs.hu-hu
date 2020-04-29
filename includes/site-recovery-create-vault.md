---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: fd1a7f133c5719873133554fc2292e94e6fe26a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75980328"
---
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) > **Recovery Servicesba**.
2. Kattintson **az erőforrás** > **létrehozása monitoring és felügyelet** > **biztonsági mentés és site Recovery**lehetőségre.
3. A **Név** mezőben adja meg a tárolót azonosító rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
4. [Hozzon létre egy erőforráscsoportot](../articles/azure-resource-manager/templates/deploy-portal.md), vagy válasszon ki egy meglévőt. Válassza ki a kívánt Azure-régiót. 
5. Ha gyorsan el szeretné érni a tárolót az irányítópultról, kattintson a **rögzítés az irányítópulton** > **Létrehozás**gombra.

   ![Új tároló](./media/site-recovery-create-vault/new-vault-settings.png)

   Az új tároló megjelenik az **irányítópult** > **minden erőforrás**és a fő **Recovery Services** -tárolók lapon.
