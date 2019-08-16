---
title: Csomópontok kiépítése VMware-megoldáshoz az Azure-CloudSimple
description: Ismerje meg, hogyan adhat hozzá csomópontokat a VMWare-hez a CloudSimple üzembe helyezésével
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2d5b2847109149701cb6453753e52fb671ba69d0
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533339"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>Csomópontok kiépítése VMware-megoldáshoz az Azure-CloudSimple

Csomópontok kiépítése a Azure Portalban. Ezt követően a CloudSimple saját felhőalapú környezete számára is beállíthatja a befizetési kirovó kapacitást.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Csomópont hozzáadása a saját CloudSimple-felhőhöz

1. Válassza az **Összes szolgáltatás** elemet.
2. CloudSimple- **csomópontok**keresése.

   ![CloudSimple-csomópontok keresése](media/create-cloudsimple-node-search.png)

3. Válassza a **CloudSimple**-csomópontok lehetőséget.
4. Csomópontok létrehozásához kattintson a **Hozzáadás** gombra.

    ![CloudSimple-csomópontok hozzáadása](media/create-cloudsimple-node-add.png)

5. Válassza ki azt az előfizetést, ahol CloudSimple-csomópontokat kíván kiépíteni.
6. Válassza ki a csomópontok erőforrás-csoportját. Új erőforráscsoport hozzáadásához kattintson az **új létrehozása**lehetőségre.
7. Adja meg a csomópontok azonosításához szükséges előtagot.
8. Válassza ki a csomópont-erőforrások helyét.
9. Válassza ki a csomópont-erőforrások tárolására szolgáló dedikált helyet.
10. Válassza ki a csomópont típusát. Kiválaszthatja a [CS28 vagy a CS36 lehetőséget](cloudsimple-node.md). Az utóbbi lehetőség a maximális számítási és memória-kapacitást is tartalmazza.
11. Válassza ki a kiépíteni kívánt csomópontok számát.
12. Válassza a **felülvizsgálat + létrehozás**lehetőséget.
13. Tekintse át a beállításokat. A beállítások módosításához kattintson az **előző**gombra.
14. Kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>További lépések

* [Privát felhő létrehozása](create-private-cloud.md)
