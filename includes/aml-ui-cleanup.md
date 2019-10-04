---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 05/06/2019
ms.openlocfilehash: 623e993dfbe6bbb3297fa6470865ab1a04f55b37
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179536"
---
>[!IMPORTANT]
>Az Ön által létrehozott erőforrások más az Azure Machine Learning szolgáltatás oktatóanyagok és útmutatók előfeltételei is használhatja.


### <a name="delete-everything"></a>Teljes tartalmának törlése

Ha nem szeretné használni, amelyeket létrehozott, teljes erőforráscsoportot törölni, így nem kell díjat fizetniük:

1. Az Azure Portalon válassza ki a **erőforráscsoportok** az ablak bal oldalán.
 
   ![Erőforráscsoport törlése az Azure Portalon](./media/aml-ui-cleanup/delete-resources.png)

1. A listában válassza ki a létrehozott erőforráscsoportot.

1. Az ablak jobb oldalán válassza a három pont gombra ( **...** ).

1. Válassza az **Erőforráscsoport törlése** elemet.

A vizuális felületen létrehozott összes erőforrást is az erőforráscsoport törlése törli.  

### <a name="delete-only-the-compute-target"></a>Csak a számítási tároló törlése

Az itt létrehozott számítási célnak *automatikusan elvégzi az automatikus skálázást* nulla csomópontokra, amikor nincs használatban. Ez a költségek minimalizálása érdekében. Ha törölni szeretné a számítási célnak, ezeket a lépéseket:

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a munkaterületet.

    ![Törölje a számítási célnak](./media/aml-ui-cleanup/delete-compute-target.png)

1. Az a **számítási** szakasz a munkaterületet, válassza ki az erőforrást.

1. Válassza a **Törlés** elemet.

### <a name="delete-individual-assets"></a>Egyes eszközök törlése

A vizuális felületen, ahol létrehozta a kísérlethez, törölje egyes eszközök jelölje ki őket, és válassza a **törlése** gombra.

![Kísérletek törlése](./media/aml-ui-cleanup/delete-experiment.png)
