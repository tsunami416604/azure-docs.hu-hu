---
title: A CloudSimple vmware-megoldásának csomópontjainak törlése – Azure
description: Ismerje meg, hogyan törölheti a csomópontokat a VMWare szolgáltatásból a CloudSimple telepítésével
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024738"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Csomópontok törlése az Azure VMware-megoldásból a CloudSimple által

A CloudSimple-csomópontok at a létrehozásuk után a szolgáltatás méri.  A csomópontok mérésének leállításához törölni kell a csomópontokat.  Törli a nem használt csomópontokat az Azure Portalról.

## <a name="before-you-begin"></a>Előkészületek

Egy csomópont csak a következő feltételek mellett törölhető:

* A csomópontoksegítségével létrehozott magánfelhő törlődik.  Privát felhő törléséről olvassa el Az [Azure VMware-megoldás törlése a CloudSimple private cloud szolgáltatással című témakört.](delete-private-cloud.md)
* A csomópont a magánfelhő zsugorításával lett eltávolítva a magánfelhőből.  A magánfelhő zsugorításáról a [Shrink Azure VMware Solution by CloudSimple Private Cloud című témakörben találja.](shrink-private-cloud.md)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="delete-cloudsimple-node"></a>CloudSimple csomópont törlése

1. Válassza az **Összes szolgáltatás** elemet.

2. Keresse meg a **CloudSimple csomópontokat.**

   ![Keresés CloudSimple csomópontok](media/create-cloudsimple-node-search.png)

3. Válassza a **CloudSimple csomópontok lehetőséget.**

4. Válassza ki azokat a csomópontokat, amelyek nem tartoznak a törölni kívánt magánfelhőhöz.  **A PRIVATE CLOUD NAME** oszlop azt a magánfelhő-nevet jeleníti meg, amelyhez egy csomópont tartozik.  Ha egy csomópontot nem használ egy magánfelhő, az érték üres lesz. 

    ![Felhőegyszerű csomópontok kiválasztása](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Csak azok a csomópontok törölhetők, amelyek nem részei a magánfelhőnek.

## <a name="next-steps"></a>További lépések

* További információ a [magánfelhőről](cloudsimple-private-cloud.md)
