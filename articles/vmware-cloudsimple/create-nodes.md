---
title: Csomópontok kiépítése VMware-megoldásokhoz (AVS) – Azure
description: Ismerje meg, hogyan adhat hozzá csomópontokat a VMWare-hez az AVS üzembe helyezésével
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94ca681153f1e3ccd9ff628b41a9d0e547be5802
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024806"
---
# <a name="provision-nodes-for-azure-vmware-solutions-avs"></a>Csomópontok kiépítése Azure VMware-megoldásokhoz (AVS)

Csomópontok kiépítése a Azure Portalban. Ezt követően a saját AVS-beli felhőalapú környezete számára is beállíthatja az utólagos elszámolású kapacitást.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="add-a-node-to-your-avs-private-cloud"></a>Csomópont hozzáadása az AVS Private Cloud-hoz

1. Válassza az **Összes szolgáltatás** elemet.
2. Az **AVS-csomópontok**keresése.

   ![AVS-csomópontok keresése](media/create-cloudsimple-node-search.png)

3. Válassza az **AVS-csomópontok**lehetőséget.
4. Csomópontok létrehozásához kattintson a **Hozzáadás** gombra.

    ![AVS-csomópontok hozzáadása](media/create-cloudsimple-node-add.png)

5. Válassza ki azt az előfizetést, ahol az AVS-csomópontokat szeretné kiépíteni.
6. Válassza ki a csomópontok erőforrás-csoportját. Új erőforráscsoport hozzáadásához kattintson az **új létrehozása**lehetőségre.
7. Adja meg a csomópontok azonosításához szükséges előtagot.
8. Válassza ki a csomópont-erőforrások helyét.
9. Válassza ki a csomópont-erőforrások tárolására szolgáló dedikált helyet.
10. Válassza ki a [csomópont típusát](cloudsimple-node.md).
11. Válassza ki a kiépíteni kívánt csomópontok számát.
12. Válassza a **felülvizsgálat + létrehozás**lehetőséget.
13. Tekintse át a beállításokat. A beállítások módosításához kattintson az **előző**gombra.
14. Kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>Következő lépések

* [AVS Private Cloud létrehozása](create-private-cloud.md)
