---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: fd1a7f133c5719873133554fc2292e94e6fe26a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75980328"
---
1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **helyreállítási szolgáltatásokba.**
2. Kattintson az > **Erőforrásfigyelés létrehozása + Felügyeleti** > **biztonsági másolat és a Webhely-helyreállítás elemre.** **Create a resource**
3. A **Név** mezőben adja meg a tárolót azonosító rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
4. [Hozzon létre egy erőforráscsoportot](../articles/azure-resource-manager/templates/deploy-portal.md), vagy válasszon ki egy meglévőt. Válassza ki a kívánt Azure-régiót. 
5. Ha gyorsan el szeretné érni a tárolót az irányítópultról, kattintson a Rögzítés az irányítópult > **létrehozása** **elemre.**

   ![Új tároló](./media/site-recovery-create-vault/new-vault-settings.png)

   Az új tároló megjelenik az **irányítópulton** > **Minden erőforrás**, és a fő Helyreállítási szolgáltatások **tárolók** lapon.
