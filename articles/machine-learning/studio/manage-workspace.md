---
title: A Machine Learning Studio-munkaterület kezelése
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio-munkaterületek, való hozzáférés kezelése és üzembe helyezése és kezelése a Machine Learning API-webszolgáltatások
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: a947f9a94dd4ceed624e6b04a671b21b8926d25e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080743"
---
# <a name="manage-an-azure-machine-learning-studio-workspace"></a>Az Azure Machine Learning Studio-munkaterület kezelése

> [!NOTE]
> A Machine Learning Web Services portálon webszolgáltatások kezelésével kapcsolatban további információkért lásd: [egy webszolgáltatás, az Azure Machine Learning Web Services portál használata kezelheti](manage-new-webservice.md).
> 
> 

A Machine Learning Studio-munkaterületek az Azure Portalon kezelheti.



## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A Studio-munkaterület az Azure Portalon kezelheti:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) egy Azure-előfizetéshez rendszergazdai fiókkal.
2. A keresőmezőbe, a lap tetején adja meg a "machine learning Studio-munkaterületek", és adja meg **a Machine Learning Studio-munkaterületek**.
3. Kattintson a kezelni kívánt munkaterülethez.

Standard erőforrásadatok felügyeleti és a rendelkezésre álló lehetőségek mellett a következő műveletek végezhetők el:

- Nézet **tulajdonságok** – ezen a lapon látható a munkaterület és az erőforrás adatait, és módosíthatja az előfizetésben és erőforráscsoportban csoport, amely ehhez a munkaterülethez van csatlakoztatva.
- **Tárkulcsok újraszinkronizálása** – a munkaterület fenntart a tárfiókhoz kulcsot. A storage-fiók kulcsok megváltozik, akkor kattintson **kulcsok újraszinkronizálása** a kulcsok szinkronizálása a munkaterületen.

A webes szolgáltatások a Studio-munkaterülethez társított kezeléséhez használja a Machine Learning webszolgáltatások portálján. Lásd: [egy webszolgáltatás, az Azure Machine Learning Web Services portál használata kezelheti](manage-new-webservice.md) teljes körű információkat.

> [!NOTE]
> Telepítenie és felügyelnie az új webszolgáltatások, hozzá kell rendelni egy közreműködőjének vagy rendszergazdájának szerepkört az előfizetésre, amelyre a web service telepítve van. A machine learning Studio-munkaterület egy másik felhasználó meghívása meg, ha kell rendelnie őket az előfizetés közreműködőjének vagy rendszergazdájának szerepkörhöz üzembe helyezése vagy webszolgáltatások kezelése előtt. 
> 
>Hozzáférési engedélyek beállítása a további információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>További lépések
* Tudjon meg többet [Machine Learning az Azure Resource Manager-sablonok üzembe helyezése](deploy-with-resource-manager-template.md). 
