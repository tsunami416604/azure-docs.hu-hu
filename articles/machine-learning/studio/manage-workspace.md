---
title: A Machine Learning-munkaterület kezelése |} Microsoft Docs
description: Azure Machine Learning munkaterületekhez való hozzáférés kezelése és szolgáltatások telepítésére és kezelésére ML API webkiszolgáló
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 8c8e65d5fd7efd1f9212b76e1a72b91a0ea07272
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835066"
---
# <a name="manage-an-azure-machine-learning-workspace"></a>Azure Machine Learning-munkaterület kezelése

> [!NOTE]
> A Machine Learning webszolgáltatások portálon webszolgáltatások kezeléséről további információért lásd: [kezelése az Azure Machine Learning webszolgáltatások portál használatával egy webszolgáltatás-bővítmény](manage-new-webservice.md).
> 
> 

Gépi tanulás munkaterületek az Azure-portálon kezelheti.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A munkaterület az Azure-portálon kezelheti:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) Azure-előfizetéshez rendszergazdai fiók használatával.
2. Az oldal tetején a keresési mezőbe, írja be a "számítógép tanulási munkaterületek", és válassza ki **Machine Learning munkaterületek**.
3. Kattintson a kezelni kívánt munkaterületre.

A szabványos erőforrás-kezelési információkat és a rendelkezésre álló lehetőségek mellett a következő műveletek végezhetők el:

- Nézet **tulajdonságok** – ezen a lapon a munkaterületet, és az erőforrás-információkat jeleníti meg, és módosíthatja a előfizetésbe és erőforráscsoportba csoport, a munkaterület kapcsolódnak.
- **Tárolási kulcsok újraszinkronizálásra** -a munkaterület fenntartja a tárfiók kulcsait. Ha a tárfiók kulcsait megváltozik, ezt követően kattinthat **kulcsok újraszinkronizálásra** a kulcsok szinkronizálása a munkaterületen.

A munkaterület társított webszolgáltatások kezeléséhez használja a Machine Learning webszolgáltatások portálon. Lásd: [kezelése az Azure Machine Learning webszolgáltatások portál használatával egy webszolgáltatás-bővítmény](manage-new-webservice.md) kapcsolatos részletes információkért.

> [!NOTE]
> Központi telepítése, illetve új webszolgáltatások kezelése az előfizetést, amelyhez a webszolgáltatások telepítése egy közreműködő vagy a rendszergazda szerepkört kell hozzárendelni. Ha meghívni egy másik felhasználót egy machine learning munkaterülettel, hozzá kell rendelnie azokat egy közreműködő vagy a rendszergazda szerepkört az előfizetés ahhoz, azok központilag telepíthetne vagy kezelhetne webszolgáltatások. 
> 
>A hozzáférési engedélyek beállítása további információkért lásd: [access-hozzárendelések megtekintése a felhasználók és csoportok az Azure portálon](../../role-based-access-control/role-assignments-users.md).

## <a name="next-steps"></a>További lépések
* További információ [telepítése Azure Resource Manager-sablonok a Machine Learning](deploy-with-resource-manager-template.md). 