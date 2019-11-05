---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/22/2019
ms.openlocfilehash: 5a66212122745d0f4426e48e9487e9d674cec53f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489974"
---
>[!IMPORTANT]
>A létrehozott erőforrásokat használhatja más Azure Machine Learning oktatóanyagok és útmutatók előfeltételeiként.

### <a name="delete-everything"></a>Mindent törölni

Ha nem tervezi a létrehozott bármit használni, törölje a teljes erőforráscsoportot, így nem számítunk fel díjat:

1. A Azure Portal az ablak bal oldalán válassza az **erőforráscsoportok** lehetőséget.
 
   ![Erőforráscsoport törlése az Azure Portalon](./media/aml-ui-cleanup/delete-resources.png)

1. A listából válassza ki a létrehozott erőforráscsoportot.

1. Az ablak jobb oldalán kattintson a három pontot ábrázoló gombra ( **...** ).

1. Válassza az **Erőforráscsoport törlése** elemet.

Az erőforráscsoport törlése a tervezőben létrehozott összes erőforrást is törli.  

### <a name="delete-only-the-compute-target"></a>Csak a számítási cél törlése

Az itt létrehozott számítási cél *automatikusan automatikus méretezést* végez a nulla csomópontokra, amikor az nincs használatban. Ez a költségek csökkentése. Ha törölni szeretné a számítási célt, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com)nyissa meg a munkaterületet.

    ![A számítási cél törlése](./media/aml-ui-cleanup/delete-compute-target.png)

1. A munkaterület **számítási** szakaszában válassza ki az erőforrást.

1. Válassza a **Törlés** elemet.

### <a name="delete-individual-assets"></a>Egyedi eszközök törlése

A tervezőben, ahol létrehozta a kísérletet, törölje az egyes objektumokat úgy, hogy kiválasztja őket, majd a **Törlés** gombra kattint.

![Eszközök törlése](./media/aml-ui-cleanup/delete-asset.png)

Az adatkészletek törölhető a munkaterületről az egyes adatkészletek kiválasztásával, majd a **regisztráció megszüntetése**lehetőség kiválasztásával.

![Adatkészlet regisztrációjának törlése](./media/aml-ui-cleanup/unregister-dataset.png)


