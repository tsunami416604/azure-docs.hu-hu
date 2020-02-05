---
title: Erőforráskészlet létrehozása az Azure-előfizetések leképezésével
description: Ismerteti, hogyan lehet erőforráskészletokat létrehozni az AVS Private Cloud-hoz az Azure-előfizetések leképezésével
titleSuffix: Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab7549650e4e20d27d3ad11a96d77ba943797f88
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014963"
---
# <a name="create-resource-pools-for-your-avs-private-cloud-with-azure-subscription-mapping"></a>Erőforráskészlet létrehozása az AVS Private Cloud-hoz az Azure-előfizetés leképezésével
Az Azure-előfizetések leképezése lehetővé teszi, hogy az elérhető vSphere erőforrás-készletekből hozzon létre erőforrás-készleteket az AVS Private Cloud-hoz. Az AVS-portálon megtekintheti és kezelheti az AVS privát Felhőkhöz tartozó Azure-előfizetést.

> [!NOTE]
> Az erőforráskészlet leképezése szintén leképezi az összes alárendelt erőforrás-készletet. A szülő erőforráskészlet nem képezhető le, ha a gyermek-erőforrások készlete már le van képezve.

1. [Hozzáférés az AVS-portálhoz](access-cloudsimple-portal.md).
2. Nyissa meg az **erőforrások** lapot, és válassza az **Azure-előfizetések hozzárendelése**elemet.  
3. Kattintson az **Azure-előfizetések leképezésének szerkesztése**elemre.  
4. Az elérhető erőforráskészlet hozzárendeléséhez jelölje ki őket a bal oldalon, majd kattintson a jobbra mutató nyílra. 
5. A leképezések eltávolításához jelölje ki őket a jobb oldalon, majd kattintson a balra mutató nyílra. 

    ![Azure-előfizetések](media/resources-azure-mapping.png)

6. Kattintson az **OK** gombra.
