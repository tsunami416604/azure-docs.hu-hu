---
title: Machine Learning-munkaterület - Azure Machine Learning Studio kezelése |} A Microsoft Docs
description: Az Azure Machine Learning-munkaterületek, való hozzáférés kezelése és üzembe helyezése és gépi Tanulási API-webszolgáltatások kezelése
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
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
ms.openlocfilehash: c470d10b933b31d4cfe151fb541c2182562a2805
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314104"
---
# <a name="manage-an-azure-machine-learning-workspace"></a>Azure Machine Learning-munkaterület kezelése

> [!NOTE]
> A Machine Learning Web Services portálon webszolgáltatások kezelésével kapcsolatban további információkért lásd: [egy webszolgáltatás, az Azure Machine Learning Web Services portál használata kezelheti](manage-new-webservice.md).
> 
> 

Machine Learning-munkaterületek az Azure Portalon kezelheti.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A munkaterület az Azure Portalon kezelheti:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) egy Azure-előfizetéshez rendszergazdai fiókkal.
2. A keresőmezőbe, a lap tetején adja meg a "machine learning-munkaterületek", majd **Machine Learning-munkaterületek**.
3. Kattintson a kezelni kívánt munkaterülethez.

Standard erőforrásadatok felügyeleti és a rendelkezésre álló lehetőségek mellett a következő műveletek végezhetők el:

- Nézet **tulajdonságok** – ezen a lapon látható a munkaterület és az erőforrás adatait, és módosíthatja az előfizetésben és erőforráscsoportban csoport, amely ehhez a munkaterülethez van csatlakoztatva.
- **Tárkulcsok újraszinkronizálása** – a munkaterület fenntart a tárfiókhoz kulcsot. A storage-fiók kulcsok megváltozik, akkor kattintson **kulcsok újraszinkronizálása** a kulcsok szinkronizálása a munkaterületen.

A webes szolgáltatások a munkaterülethez társított kezeléséhez használja a Machine Learning webszolgáltatások portálján. Lásd: [egy webszolgáltatás, az Azure Machine Learning Web Services portál használata kezelheti](manage-new-webservice.md) teljes körű információkat.

> [!NOTE]
> Telepítenie és felügyelnie az új webszolgáltatások, hozzá kell rendelni egy közreműködőjének vagy rendszergazdájának szerepkört az előfizetésre, amelyre a web service telepítve van. A machine learning-munkaterület egy másik felhasználó meghívása akkor, ha kell rendelnie őket az előfizetés közreműködőjének vagy rendszergazdájának szerepkörhöz üzembe helyezése vagy webszolgáltatások kezelése előtt. 
> 
>Hozzáférési engedélyek beállítása a további információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>További lépések
* Tudjon meg többet [Machine Learning az Azure Resource Manager-sablonok üzembe helyezése](deploy-with-resource-manager-template.md). 