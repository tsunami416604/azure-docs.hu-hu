---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/16/2019
ms.openlocfilehash: 0071b0df2c2e173eced1722372f88b1de2708afa
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692427"
---
>[!IMPORTANT]
>A létrehozott erőforrásokat használhatja más Azure Machine Learning szolgáltatás-oktatóanyagok és-útmutatók előfeltételeiként.

### <a name="delete-everything"></a>Mindent törölni

Ha nem tervezi a létrehozott bármit használni, törölje a teljes erőforráscsoportot, így nem számítunk fel díjat:

1. A Azure Portal az ablak bal oldalán válassza az **erőforráscsoportok** lehetőséget.
 
   ![Erőforráscsoport törlése az Azure Portalon](./media/aml-ui-cleanup/delete-resources.png)

1. A listából válassza ki a létrehozott erőforráscsoportot.

1. Az ablak jobb oldalán kattintson a három pontot ábrázoló gombra ( **...** ).

1. Válassza az **Erőforráscsoport törlése** elemet.

Az erőforráscsoport törlése a vizualizációs felületen létrehozott összes erőforrást is törli.  

### <a name="delete-only-the-compute-target"></a>Csak a számítási cél törlése

Az itt létrehozott számítási cél *automatikusan automatikus méretezést* végez a nulla csomópontokra, amikor az nincs használatban. Ez a költségek csökkentése. Ha törölni szeretné a számítási célt, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com)nyissa meg a munkaterületet.

    ![A számítási cél törlése](./media/aml-ui-cleanup/delete-compute-target.png)

1. A munkaterület **számítási** szakaszában válassza ki az erőforrást.

1. Válassza a **Törlés** elemet.

### <a name="delete-individual-assets"></a>Egyedi eszközök törlése

A kísérletet létrehozó vizualizációs felületen törölje az egyes eszközöket, majd válassza a **Törlés** gombot. Az adatkészletek törölhető a munkaterületről az egyes adatkészletek kiválasztásával, majd a **regisztráció megszüntetése**lehetőség kiválasztásával.

![Eszközök törlése](./media/aml-ui-cleanup/delete-asset.png)
