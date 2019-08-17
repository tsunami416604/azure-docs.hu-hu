---
title: Erőforráskészlet létrehozása az Azure-előfizetések leképezésével
description: Ismerteti, hogyan lehet erőforráskészletokat létrehozni saját felhőhöz az Azure-előfizetés leképezésével
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ea102ad8377da70b88a0e59834ebe3a09866632
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563217"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Erőforráskészlet létrehozása saját felhőhöz az Azure-előfizetés leképezésével
Az Azure-előfizetések leképezése lehetővé teszi erőforráskészlet létrehozását a saját felhőhöz az elérhető vSphere-erőforrás-készletekből. A CloudSimple-portálon megtekintheti és kezelheti a privát Felhőkhöz tartozó Azure-előfizetést.

> [!NOTE]
> Az erőforráskészlet leképezése szintén leképezi az összes alárendelt erőforrás-készletet. A szülő erőforráskészlet nem képezhető le, ha a gyermek-erőforrások készlete már le van képezve.

1. [Nyissa meg a CloudSimple portált](access-cloudsimple-portal.md).
2. Nyissa meg az **erőforrások** lapot, és válassza az Azure-előfizetések **hozzárendelése**elemet.  
3. Kattintson az Azure-előfizetések **leképezésének szerkesztése**elemre.  
4. Az elérhető erőforráskészlet hozzárendeléséhez jelölje ki őket a bal oldalon, majd kattintson a jobbra mutató nyílra. 
5. A leképezések eltávolításához jelölje ki őket a jobb oldalon, majd kattintson a balra mutató nyílra. 

    ![Azure-előfizetések](media/resources-azure-mapping.png)

6. Kattintson az **OK** gombra.
