---
title: Csomópontok kiépítése a CloudSimple VMware-megoldásához – Azure
description: Ismerje meg, hogyan adhat hozzá csomópontokat a VMWare-hez a CloudSimple telepítésével
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eb033425d18b472c9da1a2d6a1bb6f166702905e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024806"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Csomópontok kiépítése a CloudSimple Azure VMware-megoldásához

Üzembe helyezhető csomópontok az Azure Portalon. Ezután beállíthatja a pay-as-you kapacitása a CloudSimple privát felhőalapú környezetben.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Csomópont hozzáadása a CloudSimple magánfelhőhöz

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg a **CloudSimple csomópontokat.**

   ![Keresés CloudSimple csomópontok](media/create-cloudsimple-node-search.png)

3. Válassza a **CloudSimple csomópontok lehetőséget.**
4. Csomópontok létrehozásához kattintson a **Hozzáadás** gombra.

    ![CloudSimple-csomópontok hozzáadása](media/create-cloudsimple-node-add.png)

5. Válassza ki azt az előfizetést, amelyhez CloudSimple-csomópontokat szeretne kiépíteni.
6. Válassza ki a csomópontok erőforráscsoportját. Új erőforráscsoport hozzáadásához kattintson az **Új létrehozása gombra.**
7. Adja meg az előtagot a csomópontok azonosításához.
8. Válassza ki a csomóponti erőforrások helyét.
9. Válassza ki a csomópont-erőforrások üzemeltetéséhez szükséges dedikált helyet.
10. Válassza ki a [csomópont típusát](cloudsimple-node.md).
11. Válassza ki a kiépítve kiépítandó csomópontok számát.
12. Válassza **a Véleményezés + Létrehozás lehetőséget.**
13. Tekintse át a beállításokat. A beállítások módosításához kattintson az **Előző**gombra.
14. Kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>További lépések

* [Privát felhő létrehozása](create-private-cloud.md)
