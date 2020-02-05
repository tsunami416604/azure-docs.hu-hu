---
title: VMware-megoldások csomópontjainak törlése (AVS) – Azure
description: Ismerje meg, hogyan törölhet csomópontokat a VMWare-ből az AVS-telepítéssel
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30d128d6bd2f2e1e2705a7b742f02d11fd947a03
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024738"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-avs"></a>Azure VMware-megoldás csomópontjainak törlése AVS használatával

Az AVS-csomópontok a létrehozásuk után vannak mérten. A csomópontok mérésének leállításához törölni kell a csomópontokat. Törli a Azure Portal nem használt csomópontokat.

## <a name="before-you-begin"></a>Előzetes teendők

Csomópontot csak a következő feltételekkel lehet törölni:

* A csomópontokkal létrehozott AVS Private-felhő törlődik. Az AVS Private Cloud törlését lásd: [Az Azure VMware-megoldás törlése az AVS Private Cloud használatával](delete-private-cloud.md).
* A csomópont el lett távolítva az AVS Private Cloud-ból az AVS Private Cloud lekicsinyítésével. Az AVS-beli privát felhő csökkentése: az [Azure VMware-megoldás Összezsugorodása az AVS Private Cloud használatával](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="delete-avs-node"></a>AVS-csomópont törlése

1. Válassza az **Összes szolgáltatás** elemet.

2. Az **AVS-csomópontok**keresése.

   ![AVS-csomópontok keresése](media/create-cloudsimple-node-search.png)

3. Válassza az **AVS-csomópontok**lehetőséget.

4. Válassza ki azokat a csomópontokat, amelyek nem tartoznak a törölni kívánt AVS privát felhőhöz. Az **AVS Private Cloud Name (egyéni felhő neve** ) oszlopban látható a saját AVS-felhő neve, amelyhez a csomópont tartozik. Ha egy csomópontot nem használ egy AVS privát felhő, az érték üres lesz. 

    ![AVS-csomópontok kiválasztása](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Csak azok a csomópontok törölhetők, amelyek nem részei az AVS Private Cloud szolgáltatásnak.

## <a name="next-steps"></a>Következő lépések

* Az [AVS Private Cloud](cloudsimple-private-cloud.md) megismerése
